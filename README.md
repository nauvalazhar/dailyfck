# dailyfck
start collecting my daily fcks

## "the partition table format in use on your disks normally requires you to create a separate partition"

Ini terjadi ketika gw mau install Ubuntu alongside Win10, saya pake Win 10 (no secure boot; UEFI). Ketika install Ubuntu hingga tahap create partition manually itu lancar aja, tapi pas klik "Install Now" malah muncul error di atas. Masalah ini bisa terjadi ketika Windows booting mode UEFI sedangkan installer Ubuntu jalan dengan mode Legacy (mungkin bisa juga sebaliknya) -- intinya mixed-boot-mode.

Sebenernya pas bagian pemilihan "mode" instalasi, yang ada "erase" atau pilih "something else" itu udah curiga sih, karena kalo dual boot harusnya sistem operasi yang pertama bisa dideteksi oleh installer Ubuntu dan harusnya setidaknya ada 3 pilihan: install alongside windows, erase, dan something else. 

Walaupun ada error message ini, sebenernya instalasi masih bisa lanjut, tapi ada kemungkinan gagal dan bila berhasil bisa memungkinkan masalah yang lebih besar.

### Solution
Pertama, exit dulu dari installer, abis itu restart dan masuk dulu ke BIOS, cari firmware setup atau menu semcamnya yang di dalamnya ada pengaturan "boot mode" dengan pilihan "UEFI and Legacy", "Legacy only", and "UEFI only". 

Simpan perubahan (tekan F10), coba unplug installation media dan coba plug lagi. Habis itu di GRUB pilih Ubuntu untuk masuk ke installer Ubuntu. Dan mulai lagi melakukan instalasi sampai tahap di mana bikin partisi manual. Saya bikin partisi: `/`, `/home`, dan `/swap` (sebenernya ini gak butuh2 amat sih aowkoakwoawk), selain itu, kamu juga butuh partisi dengan size (kalo saya) 512 MB terus set sebagai EFI Boot Partiion. Abis itu bisa harusnya klik tombol "Install Now".

### Reference
- https://www.google.com/search?source=hp&ei=XQ-SX7rDHNbf9QOGvrqwAw&q=the+partition+table+format+in+use+on+your+disks+normally+requires+you+to+create+a+separate+partition&oq=the+partition+table&gs_lcp=CgZwc3ktYWIQAxgAMgIIADICCAAyAggAMgIIADICCAAyAggAMgIIADICCAAyBggAEBYQHjIGCAAQFhAeOggIABCxAxCDAToFCAAQsQM6BQguELEDOggILhCxAxCDAToCCC46CAguELEDEJMCOgUILhCTAlAiWMtoYNh1aAFwAHgAgAFniAHbCpIBBDIxLjGYAQCgAQGqAQdnd3Mtd2l6&sclient=psy-ab
- Gw lupa tadi webnya mana aja 

---

## Ubuntu 20.04 freeze randomly
Jadi, gw punya OS Windows 10, dan satu lagi Ubuntu yang masih fresh. Gw dapet notif update, dan langsung gw update tuh Ubuntu, abis update dan reboot nggak ada masalah. Karena gw mau nyoba rekaman di Ubuntu, yaudah install OBS, terus setup scene: masukin mic + screen. Dan tidak ada masalah apapun, sampe beberapa kali rekaman pun gak ada issue.

Tibalah saatnya. Ketika gw mau menjajal smartphone android gw sebagai webcam di OBS lewat same network, muncul tuh di OBS, walaupun hasilnya udah pasti kurang bagus (lag), lalu tiba2 OBS gw hang. Dan ternyata bukan OBS-nya yang hang, tapi Ubuntu gw yang freeze semuanya. Ga bisa pake keyboard, mouse cuma stuck aja gitu.

Udah pake tombol kombinasi "magic" juga gak bisa (ALT+PRINTSCREEN+REISUB), gw pikir ini emang bener2 Ubuntu-nya yang freeze bukan cuma deksop-nya aja. Yaudah gw restart dengan tekan tombol restart di CPU, abis itu gw kira bakal bisa, ketika selesai booting dan gw login malah freeze di situ, anjayani!

Abis gitu gw coba cari kenapa, dan gak nemu solusi yang clear, kebanyakan dari mereka yang punya masalah freeze itu karena pake NVIDIA, sedangkan gw pake driver AMD. Yaudah coba masuk ke GRUB terus ke menu advanced cari versi kernel lainnya, abis booting, udah berhasil login, eh malah freeze juga di dalem desktop.

Gw restart lagi lewat CPU, dan gw coba masuk ke safe mode (recovery), terus masuk ke root shell, dan coba install driver graphic lewat situ, ternyata gak ada yang perlu di-install. Lalu gw bingung, dan gw coba `apt update` dan `apt upgrade` sapa tau ada yang perlu diperbarui, dan muncul konfirmasi ada package yang perlu diperbarui, gw lupa tadi nama package-nya apa tapi awalnya `ubuntu-*`, kalo gak salah ada 2 package. Abis itu gw restart lagi, dan masalahnya sampe gw nulis ini belom muncul lagi. Ya semoga aja masalahnya karena software ga update aja.

### Workaround
```
sudo apt update && sudo apt upgrade
```

### System Summary
- OS: Ubuntu 20.04
- DE: Gnome
- Graphic: AMD

-----

## Strapi Admin UI takes 1.6 minutes to load, but REST is so fast

Ini masalah di Strapi. Saya lagi bikin app yang adminnya pake CMS Strapi. Sudah 1 tahun terakhir pake Strapi dan gak ada masalah ini, masalahnya itu halaman admin Strapi lambat banget di-load, bahkan sampe 1.6 menit. Gw kira ini cuma initial load aja (w/o cache) tapi ternyata pas berikutnya juga sama lambatnya. Berarti ada yang gak beres. Beruntungnya, API-nya cepet banget (ya bisa gw bilang normal lah). 

Gw awlanya gak curiga sih ini lambat, karena gw pikir emang bundle Strapi ini gede banget, dan ditambah masih di dev env. Dan gw coba build buat prod, tetap aja hasilnya lambat. Berarti emang ada yang salah sih di Strapi nya. Coba cari solus di GitHub issue, nggak ada solusi yang clear, atau bahkan nggak ada masalah yang bener2 serupa. Ya udah gw open [new issue](https://github.com/strapi/strapi/issues/8375) dan ternyata ada beberapa orang punya masalah yang sama, bahkan server mereka lebih bagus dari pada punya gw.

Setelah ditelusuri ternyata ini masalah compression brotli yang bikin response jadi slow-down, dan compression ini enabled by default.

### Solution
Disable brotli for better life.
```
// /config/middleware.js
module.exports = {
    gzip: {
      enabled: true,
      options: {
        br: false
      }
    }
  },
};
```

### System Summary
- Node.js version: v10.16.0
- NPM version:6.9.0
- Strapi version: 3.1.2
- Database: MySQL
- Operating system: Ubuntu 16.04

### Reference
- https://github.com/strapi/strapi/issues/8375
- https://strapi.io/documentation/v3.x/concepts/middlewares.html#core-middleware-configurations
- https://strapi.io/documentation/v3.x/deployment/heroku.html#gzip

## OBS won't launch on Ubuntu

Ok, jadi sebenernya app ini bisa jalan sebelumnya, nah entah kenapa pas gw buka lagi di masa depan gak bisa. Kayaknya sih gw ada nambahin beberapa konfigurasi, tapi gw lupa apa konfigurasinya. Nah, gw cari masalahnya dan ternyata gak ada yang sama. Yaudah gw mikir solve sendiri, gw coba remove package, gak bisa; gw coba, purge, gak bisa juga. Yaudah gw coba cek log, dan ada beberapa yang error, dan gw gak ngerti ini error nya relevan apa nggak. Yaudah lah gw coba hapus konfigurasi aja. Hasilnya bisa, OBS gw kebuka lagi.

### Solution
Remove OBS studio configuration at `~/.config/obs-studio` with `rm -rf ~/.config/obs-studio`.

### System Summary

- OS: Ubuntu 20.04
- OBS Version: 26.0.2

## Firefox Ctrl+Mousewheel (Zoom in/out) too slow
Ini lambat banget sih, gw kira masalah di Ubuntu, ternyata di Firefox. Karena di Google Chrome melakukan zoom in/out di Figma pake mousewheel itu cepet bener. Sedangkan di Firefox lambatnya bukan main. 

### Solution
Open `about:config`. Find the `mousewheel.with_control.delta_multiplier_y` configuration. In my case, change the value from 100 to 1000. And that's it!

### System Summary
- OS: Ubuntu 20.04
- Firefox Version: 82.0 (64-bit)

## Bluetooth not detecting any devices on Ubuntu

Gw baru beli headphone bluetooth, di hape gw sih oke2 aja. Tapi, pas coba di pc ubuntu saya malah ga mau detect sama sekali. Pas gw cek ternyata emang driver ubuntu-nya ga detect device apapun bukan cuma headphone gw aja. Ternyata di firmware brcm nya missing, jadi harus download manual.

### Solution
Download file brcm yang missing di Ubuntu lo. Cek aja pake:
```
dmesg | grep -i 'blue' 
```
There if you find `Bluetooth: hci0: BCM: patch brcm/BCM43142A0-0a5c-216c.hcd not found` berarti lo punya masalah kek gw.
Download file yang katanya missing di sini: https://github.com/winterheart/broadcom-bt-firmware/tree/master/brcm

Terus salin file yang di download tadi:
```
sudo cp  BCM43142A0-0a5c-21d7.hcd /lib/firmware/brcm/BCM43142A0-0a5c-21d7.hcd

sudo modprobe -r btusb

sudo modprobe btusb
```

Coba restart pc lo terus tes scan devices lagi. Harusnya sih udah bisa.

### System Summary
- OS: Ubuntu 20.04

## Static Electricity Flickering
Ok, ini terjadi pada pc gw. Situasinya ketika masuk login screen OS Windows. Saat di Bios dan Grub sih ok ok aja, ketika masuk ke login screen Windows mulailah layar monitor gw dah kayak tv yang antenanya ccd. Masalah diperparah ketika gw buka gambar atau window yang ada warna hitamnya, di bagian hitam doang itu flickering muncul, di bagian lain apalagi putih ga muncul sama sekali.

### Solution
Gw gak tau sih sebenernya ini kenapa, cuma gw curiga ini masalah driver aja, kalo masalah dari vga card harusnya dari awal gw nyalain nih pc udah flickering. Tapi bisa aja sih, gw gak begitu ahli dalam hal ini. Jadi yang gw lakuin di sini reinstall semua driver yang ada hubungannya dengan grafis lewat safe mode, mulai dari Intel HD Graphic, sampe yang Radeon gw uninstall dan delete driver-nya. Abis itu restart pc, ketika masuk windows dia bakal punya display yang aneh dah, karena pake basic display driver.

Di sini, gw download software buat detect driver amd yang cocok buat pc gw, abis itu gw install, detect dan mulai download. Abis itu dia minta restart, ketika restart hasilnya sama aja, masih muncul flickering. Gw coba2 setting di app radeon-nya, gw coba pilih2 bagian color dan segala macem, gw switch dari ycbcr ke rgb. Lumayan tuh hasilnya ga begitu parah, tapi masih tetep ada. Ya udah gw diemin tinggal tidur tanpa gw matiin, paginya window minta update, ya udah gw update and restart, pas udah selesai dia reboot sendiri, ketika masuk windows dah ilang. Ajaib bener.

### System Summary
- OS: Windows 10

## Windows Warm Color Issue
Gw kira ini masih ada hubungannya sama issue di atas, ternyata ini berbeda. Jadi pas masuk login screen warna display nya normal2 aja. Ketika dah berhasil login juga masih aman, cuma pada saat beberapa waktu kemudian, warna display gw kayak warm gitu, agak merah atau orange lah. Gw kira ini cuma masalah di monitor, tinggal atur saturation atau semacamnya lah. Ternyata nggak, dan ini cuma soal fitur night light di windows 10, jadi kalo fitur ini diaktifin, ketika masuk jam jam malam, dia bakal merubah warna display jadi agak anget atau warm.

### Solution
Karena ini sebenernya lebih ke preferensi aja sih, bukan masalah. Buat matiin fitur ini, masuk ke display settings, terus disable Night Light fitur. Selesai.

### System Summary
- OS: Windows 10

## Git: error setting certificate verify locations
Ini terjadi ketika gw mau clone remote repo ke local. Dan ini gw tau masalah SSL certificate aja, tapi gw gak tau cara config nya gimana.
Error-nya gini:
```bash
Cloning into 'repo'...
fatal: unable to access 'https://gitlab.com/username/repo.git/': error setting certificate verify locations:
  CAfile: Wrong/path/to/ssl/certs/ca-bundle.crt
  CApath: none
```

### Solution
Ini tinggal ngasih tau aja ke git path certificate yang bener itu di mana, lo bisa ngatur per repo atau global config.

Config per repo:
```bash
git config http.sslCAinfo "/path/to/ssl/certs/ca-bundle.crt"
```

Global config:
```bash
git config --global http.sslCAinfo "/path/to/ssl/certs/ca-bundle.crt"
```

Nyari path certificate kalo di windows itu adanya di folder instalasi git, terus di: `mingw64/ssl/certs/ca-bundle.crt`. Kalo di OS lain gw gak tau cari aja sendiri.

### System Summary
- OS: Windows 10
- Git: 2.28.0.windows.1
- Git Host: Gitlab

# License
http://www.wtfpl.net/txt/copying/
