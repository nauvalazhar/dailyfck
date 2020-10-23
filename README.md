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

# License
http://www.wtfpl.net/txt/copying/
