## 单字底图
1. AE的设计
    参考自适应底图的设计, 每个单字加一个形状图层, 用来表示文字的区域, 但不需要layout标记来表示拉伸的方式.
    注: 利用第一个字的shape layer来计算背景图的缩放, 其他shaper layer的目的是为了计算底图与文字的位置对齐.    

2. `atomae_textfx.js` line 1635:
    ```javascript
    // 添加bbt=3的逻辑
    if (layerMarker.byo_bg_type !== ATOM.ByoBgTypeNone) {
            animateText.textByoBackground = new ATOM.ByoBackground();
            var imageSet = [];
            var findRes = ATOM.findAutoBgLayer(layerVar);
            if (findRes.autobg != undefined && findRes.shapelyr != undefined) {
                ATOM.makeLayerTexImage(findRes.autobg, drawDesc, imageSet, undefined);
                ATOM.exportByoParts(findRes.shapelyr, findRes.autobg, animateText.textByoBackground);
            }
    ```

3. 验证script改完之后, 原自适应底图是否正常.

4. 修改解析代码
    ```c++
    struct QTextByoBackImage{
        MBool applyLines;
        MBool closePath;
        MFloat  boardScale;
        //MRECTF  textRect;
        //MRECTF  byoRect;
        Array<MRECTF>   textRect; //!< 存放单字大小
        Array<MRECTF>   byoRects; //!< 存放单字底图大小
        Array<QTextByoSubRegion> regions;
        std::shared_ptr<BitmapRGBA8> imageRef;
    };
    QVET_ERR_TA_PARSER_BASE
    CVETextAnimationParamParser::ParseTextByoBg()
    {
        // if (m_pMarkUp->FindElem("textRect")) {
        //     GET_OPTIONAL_DOUBLE_ATTR("left", 0, bgParam.textRect.left);
        //     GET_OPTIONAL_DOUBLE_ATTR("top", 0, bgParam.textRect.top);
        //     GET_OPTIONAL_DOUBLE_ATTR("right", 0, bgParam.textRect.right);
        //     GET_OPTIONAL_DOUBLE_ATTR("bottom", 0, bgParam.textRect.bottom);
        // }
        // if (m_pMarkUp->FindElem("layerRect")) {
        //     GET_OPTIONAL_DOUBLE_ATTR("left", 0, bgParam.byoRect.left);
        //     GET_OPTIONAL_DOUBLE_ATTR("top", 0, bgParam.byoRect.top);
        //     GET_OPTIONAL_DOUBLE_ATTR("right", 0, bgParam.byoRect.right);
        //     GET_OPTIONAL_DOUBLE_ATTR("bottom", 0, bgParam.byoRect.bottom);
        // }
    }
    ```

    背景图片的读取:
    ```c++
    CQVETTextRenderFilterOutputStreamImpl::Load(MVoid* pURL)
    {
        //if(mpTextParser->getImageSource()){
        //    mpTextParser->getImageSource()->pkgPath = m_szTemplateFile;
        //    LoadImageToTexture(*mpTextParser->getImageSource(), hSessionCtx);
        //}

        if(!mpTextParser->getImageSource().empty()){
            for (auto& img_src : mpTextParser->getImageSource())
            {
                img_src.pkgPath = m_szTemplateFile;
                LoadImageToTexture(img_src, hSessionCtx);
            }
        }        
    }
    ```

    背景图片的合并:
    ```c++
    
    ```

    sprite设置, 参考*获取每个glyph在视图中的位置信息:
    ```c++
    CQVETTextRenderFilterOutputStreamImpl::JumpIconAnimation() {
        // 获取每个glyph以及每行的信息
        Array<MRECTF> lines(lenCnt);
        Array<QEVTTextRange> lineRanges(lenCnt);
        Array<Array<MRECTF>> linesglyphs(lenCnt);
        Array<Array<MRECTF>> linesDescentglyphs(lenCnt);
        for (MInt32 i = 0; i < lenCnt; i++) {
            m_hTextDrawer->getTextGlyphsOfLine(i, lineRanges[i], linesglyphs[i]);
            m_hTextDrawer->getTextGlyphsOfLine(i, lineRanges[i], linesDescentglyphs[i], MFalse);
            for (int j = 0; j < linesDescentglyphs[i].size(); j++) linesglyphs[i][j].bottom = linesDescentglyphs[i][j].bottom;
            m_hTextDrawer->getTextLines(i, lines[i]);
            glyphSum += linesglyphs[i].size();
        }
    }
    ```

5. 渲染支持
    ```c++
    MRESULT CQVETTextRenderFilterOutputStreamImpl::DrawByoBgSprite(MFloat alpha)
    {
        // 为每个字设置一个背景, 并设置背景的textcoord
        // 多图背景支持
    }
    ```

    思路: 做一次预渲染, 将背景整合到一张纹理上.

    question:
    * 背景的绘制流程
        ```c++
        CQVETTextRenderFilterOutputStreamImpl::RenderBackItem {
            InitUpdateByoRender() {
                bindLinearTex(m_hByoBgTexture, m_pByoBgSpriteAtlas, 0); // 绑定纹理, sprite与背景关联
            }
            DrawByoBgSprite() { // sprite textcoord设置并绘制
            }
        }
        ```
    
    * 如何预绘制生成一个纹理
        目标输出类型: `CQVETTexture* m_hByoBgTexture;`
        参考文字纹理合并绘制
        ```c++
        struct TextureCache{
            MBool chnnels[4];
            Sptr<TextureWP> textureRef;
            TextureCache(){
                textureRef = nullptr;
                memset(chnnels, 0, sizeof(MBool)*4);
            }
        };        
        CQEVTTextRenderCommon::prepareTexture()
        {
            TextureCache * dstTexture;
            mpMerger = std::make_shared<RenderWp>(pContext);
            CHECK_QREPORT_RET(mpMerger->prepare(config));
            mpMerger->setOneZeroBlendMode();
            CHECK_QREPORT_RET(mpMerger->renderTex(*dstTexture.textureRef/*TextureWp*/,*tempTex));
        }

        // 使用
        {
            RenderWp::bindTexture(const TextureWP& tex, int index) {
                QVETGLsamplerSource sampleSource = { 0 };
                sampleSource.pTexture = (CQVETTexture*)tex.mTexHandle;
                sampleSource.texType = QVET_GL_TEXTURE_TYPE_IAMGE_2D;
                sampleSource.minFilter = sampleSource.magFilter = QVET_GL_SAMPLER_FILTER_LINEAR;
                return mpRender->bindSamplerSource(index, &sampleSource);
            }
        }
        ```

        将多张背景图片合并到一张, 以期减少text数量.
        ```c++
        // 按行存储
        class Bitmap
        {
            // 创建
            Bitmap(MUInt32 w,MUInt32 h);

            // 获取宽和高
            MUInt32 width() const { return _width; }
            MUInt32 height() const { return _height; }
        };
        typedef Bitmap<MByte,4> BitmapRGBA8;
        ```

## 文字行间距问题
