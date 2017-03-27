### **在线安装**

1、打开Sublime Text3控制台 
```
ctrl+` 或 View > Show Console
```

2、粘贴代码

```
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
### **离线安装**
1、下载 [Package Control](https://packagecontrol.io/Package%20Control.sublime-package)安装包 
2、选择菜单 **Preferences > Browse Packages**
3、打开一个 **package** 文件夹，进入上级页面
4、将下载的文件拷贝到 **Installed Packages** 文件夹
5、重启软件
