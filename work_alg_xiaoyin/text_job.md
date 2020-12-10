## 单字底图
1. AE的设计
    参考自适应底图的设计, 每个单字加一个形状图层, 用来表示文字的区域, 但不需要layout标记来表示拉伸的方式.
    
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
