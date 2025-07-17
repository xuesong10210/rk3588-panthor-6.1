# Panthor
### kernel
panthor的驱动程序，目前在rockchip的6.1.75内核中进行的测试。
panthor在驱动正常加载log
```
[    6.250427] panthor fb000000.gpu-panthor: [drm] clock rate = 198000000
[    6.251893] panthor fb000000.gpu-panthor: EM: OPP:400000 is inefficient
[    6.251903] panthor fb000000.gpu-panthor: EM: OPP:300000 is inefficient
[    6.252035] panthor fb000000.gpu-panthor: EM: created perf domain
[    6.252497] panthor fb000000.gpu-panthor: [drm] mali-g610 id 0xa867 major 0x0 minor 0x0 status 0x5
[    6.252512] panthor fb000000.gpu-panthor: [drm] Features: L2:0x7120306 Tiler:0x809 Mem:0x301 MMU:0x2830 AS:0xff
[    6.252524] panthor fb000000.gpu-panthor: [drm] shader_present=0x50005 l2_present=0x1 tiler_present=0x1
[    6.255086] panthor fb000000.gpu-panthor: [drm] Firmware protected mode entry not be supported, ignoring
[    6.255188] panthor fb000000.gpu-panthor: [drm] Firmware git sha: 814b47b551159067b67a37c4e9adda458ad9d852 
[    6.255516] panthor fb000000.gpu-panthor: [drm] CSF FW using interface v1.1.0, Features 0x0 Instrumentation features 0x71
[    6.256180] [drm] Initialized panthor 1.3.0 20230801 for fb000000.gpu-panthor on minor 1
```

### patch
0001-add-panthor-gpu-driver-for-6.1.75.patch上面内核驱动的patch形式而已。


### mesa
1、安装mesa库。
```
tar -xvf mesa-25.0.7.tar.gz -C /
echo "/usr/local/lib" > /etc/ld.so.conf.d/00-aarch64-mesa.conf
ldconfig

```
2、编译mesa库
安装必要的软件包：
```
sudo apt install -y x11proto-core-dev xtrans-dev libpixman-1-dev libx11-dev libxkbfile-dev libxfont-dev nettle-dev systemd libudev-dev libdrm-dev libepoxy-dev mesa-common-dev nettle-dev libudev-dev libgbm-dev libepoxy-dev libxshmfence-dev libxcb-xinput-dev
sudo apt install -y gcc g++ ninja-build pkg-config cmake python3-mako zlib1g-dev libexpat1-dev libdrm-dev byacc bison flex libxcb1-dev libxcb-dri2-0-dev libxcb-dri3-dev libxcb-present-dev libxcb-sync-dev libxcb-xfixes0-dev libxcb-shm0-dev libxcb-glx0-dev libx11-dev libxext-dev libxrender-dev libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev libx11-xcb-dev libxshmfence-dev libxxf86vm-dev x11proto-core-dev xtrans-dev libpixman-1-dev libx11-dev libxkbfile-dev libxfont-dev nettle-dev systemd libudev-dev libdrm-dev libepoxy-dev mesa-common-dev nettle-dev libudev-dev libgbm-dev libepoxy-dev libxshmfence-dev libxcb-xinput-dev llvm-dev libwayland-egl-backend-dev
```
注意: meson可能因为版本的问题需要单独安装
```
pip3 install --upgrade --user meson
export PATH="$PATH:$HOME/.local/bin"
```

编译：
```
git clone https://gitlab.freedesktop.org/mesa/mesa.git
cd mesa
git checkout -f mesa-25.0.7 -b 25.0.7
# 如需wayland后端支持，设置-Dplatforms=x11,wayland
meson build -Dvulkan-drivers=panfrost -Dgallium-drivers=panfrost -Dplatforms=x11 -Dglx=auto -Dprefix=/usr/local
sudo ninja -C build install
echo "/usr/local/lib" > /etc/ld.so.conf.d/00-aarch64-mesa.conf
ldconfig
```
drm、xserver需要切换到公版。  
依赖库是否链接正常可以通过`ldd /usr/bin/glxinfo`查看。
查看GPU使用率`cat /sys/devices/platform/fb000000.gpu-panthor/devfreq/fb000000.gpu-panthor/load`



