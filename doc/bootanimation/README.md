# Linux bootanimation

## 简介

基于Rokchip DRM显示框架，用于开机显示动态LOGO

## 部署

将启动脚本脚本`[当前目录]/etc/init.d/S01bootanimation`放到`/etc/init.d/`目录下，赋予可执行权限

将配置文件`[当前目录]/etc/bootanimation/bootanimation.conf`放到`/etc/bootanimation/`目录下

将程序`[当前目录]/usr/bin/modeset`放到`/usr/bin`目录下，赋予可执行权限

将图片目录`[当前目录]/usr/share/bootanimation/part0`放到`/usr/share/bootanimation/part0`目录下，赋予可执行权限

## 图片格式要求

- `jpeg`格式
- 与屏幕分辨率一致
- 图片名字从0开始，`0.jpg ->n.jpg`

## 配置方法

开机动画，会读取这个配置文件：

```bash
/etc/bootanimation/bootanimation.conf
```

以下是默认配置，可以根据需要修改：（`cnt`： 动画图片张数； `path`：动画图片路径；`fps`：图片刷新率）

```
cnt=24   
path=/usr/share/bootanimation/part0
fps=15
```

根据需要替换`[path]`目录中的图片
