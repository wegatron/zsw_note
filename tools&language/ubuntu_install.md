## 基本软件安装
```bash
sudo apt install -y cmake synaptic git emacs openssh-server filezilla fcitx foxit-reader
sudo dpkg -i google-chrome-stable_current_amd64.deb wps-office_11.1.0.9505_amd64.deb 
tar -jxvf Zotero-5.0.85_linux-x86_64.tar.bz2 -C ~/opt/
tar -zxvf pycharm-community-2020.1.tar.gz  -C ~/opt/
chmod 400 id_rsa
cp id_rsa ~/.ssh
ssh-add
```

## deepwine 安装
参考 https://github.com/zq1997/deepin-wine
中文乱码问题: 安装fonts-wqy-microhei完美解决

对于英文系统, 在启动脚本中增加
```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```