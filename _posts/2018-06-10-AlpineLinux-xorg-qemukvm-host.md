---
---
Make AlpineLinx with Xorg as Qemu/KVM host
## 安全OS推薦：Alpine Linux 並作爲 Qemu/KVM 宿主機

> 緣起：站長一直想找個理想的發行版，作爲宿主機，然後用多個虛擬客戶機辦公（當然這需求最理想的還是要用 QubesOS）。站長這個週末重溫Alpine，但也斷斷續續折騰了一夜+大半白天也算折騰好，遂記錄一下避免入坑的步驟。  

[Alpine Linux](https://alpinelinux.org)最開始時不太出名，後來成爲很多廠商產品的Docker里的容器（container）載體後，名聲大振。而且最重要的是 Grsecurity 的加固內核是它的標配，這就是內核級的高安全性了。  


### 0x01
下載它（去官網即可），dd到USB盤，啓動之。然後很簡單，執行 `setup-alpine` 腳本一路往下走。我是打算用sys模式安裝到（另一個USB盤）。（有哪些模式請參考官網wiki的安裝教程）  


### 0x02  
**安裝Xorg + dwm（桌面）**  
這裏有坑。按照官網的[Xorg教程](http://wiki.alpinelinux.org/wiki/Alpine_setup_scripts#setup-xorg-base)，基本無法直接帶起X server，你需要有些顯卡驅動的知識，根據你用的顯卡去apk源安裝相應的驅動。不過至少 vesa，modesetting，fbdev等都裝上來兜底（經過一番折騰我終於用對了驅動，這是因爲Slackware 有一套非常全的驅動配置文件（參考這裏，感謝Slackware！->） [xorg.conf.d](https://github.com/mdrights/Myscripts/tree/master/xorg-conf)。這篇[文檔](http://troglobit.com/2017/09/10/install-x-window-in-alpine-linux/)也值得參考。    

接下來我安裝了 [dwm](https://dwm.suckless.org/)，因爲它效率高速度快體積小，當然我的需求也少，2333。你們可以選擇 XFCE（官網有教程），awesome，openbox 等等（感謝社區的壯大，不然我差點要用 musl 編譯打包那些程序了2333）。  


### 0x03
**安裝 Qemu/KVM/libvirt/virt-manager**   
網上文檔大多記錄的是把 Alpine 當作虛擬化平臺的客戶機（guest OS）。教你把Alpine做出母機的也就[這篇](https://blog.skunkw0rks.io/blog/2017/04/01/alpine-linux-as-a-kvm-host/)了，還有官網那麼一丁點兒~(見 Qemu頁 KVM頁)。噢，還有[這篇](http://wiki.alpinelinux.org/wiki/User:CandyAngel/KVM_Host_Tutorial)。    

你需要安裝這些包（從官方源里）：  
```bash
apk add qemu-system-x86_64 qemu-gtk netcat-openbsd libvirt-daemon libvirt dbus polkit qemu-img virt-manager
```

開機自動加載內核模塊：  
`modprobe kvm-intel` 或 `modprobe kvm-amd` （根據你用啥顯卡）   

如果你用默認的 NAT 網絡，要加載這個模塊：  
`modprobe tun`  

加入開機啓動：  
```
rc-update add libvirtd 
rc-update add dbus
```

把你使用的用戶加到這些組里：  
```
addgroup user libvirt     
addgroup user qemu   
addgroup user kvm    
```

(這裏最好重啓機器一下，穩)

### 0x04
最後你就可以啓動 virt-manager了：  
`sudo virt-manager`  


### 尾記
由於 Alpine 用了 musl，busybox 等（和其他普通GNU系發行版不一樣），使用起來不太順手，也不能像以前一樣編譯C代碼，可以參考這篇官網[wiki](https://wiki.alpinelinux.org/wiki/How_to_get_regular_stuff_working)。

Happy Hardening！
