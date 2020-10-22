# dailyfck
start collecting my daily fcks

## "the partition table format in use on your disks normally requires you to create a separate partition"

Ini terjadi ketika gw mau install Ubuntu alongside Win10, saya pake Win 10 (no secure boot; UEFI). Ketika install Ubuntu hingga tahap create partition manually itu lancar aja, tapi pas klik "Install Now" malah muncul error di atas. Masalah ini bisa terjadi ketika Windows booting mode UEFI sedangkan installer Ubuntu jalan dengan mode Legacy (mungkin bisa juga sebaliknya) -- intinya mixed-boot-mode.

Sebenernya pas bagian pemilihan "mode" instalasi, yang ada "erase" atau pilih "something else" itu udah curiga sih, karena kalo dual boot harusnya sistem operasi yang pertama bisa dideteksi oleh installer Ubuntu dan harusnya setidaknya ada 3 pilihan: install alongside windows, erase, dan something else. 

Walaupun ada error message ini, sebenernya instalasi masih bisa lanjut, tapi ada kemungkinan gagal dan bila berhasil bisa memungkinkan masalah yang lebih besar.

Solution:
Pertama, exit dulu dari installer, abis itu restart dan masuk dulu ke BIOS, cari firmware setup atau menu semcamnya yang di dalamnya ada pengaturan "boot mode" dengan pilihan "UEFI and Legacy", "Legacy only", and "UEFI only". 

Simpan perubahan (tekan F10), coba unplug installation media dan coba plug lagi. Habis itu di GRUB pilih Ubuntu untuk masuk ke installer Ubuntu. Dan mulai lagi melakukan instalasi sampai tahap di mana bikin partisi manual. Saya bikin partisi: `/`, `/home`, dan `/swap` (sebenernya ini gak butuh2 amat sih aowkoakwoawk), selain itu, kamu juga butuh partisi dengan size (kalo saya) 512 MB terus set sebagai EFI Boot Partiion. Abis itu bisa harusnya klik tombol "Install Now".

Reference:
- https://www.google.com/search?source=hp&ei=XQ-SX7rDHNbf9QOGvrqwAw&q=the+partition+table+format+in+use+on+your+disks+normally+requires+you+to+create+a+separate+partition&oq=the+partition+table&gs_lcp=CgZwc3ktYWIQAxgAMgIIADICCAAyAggAMgIIADICCAAyAggAMgIIADICCAAyBggAEBYQHjIGCAAQFhAeOggIABCxAxCDAToFCAAQsQM6BQguELEDOggILhCxAxCDAToCCC46CAguELEDEJMCOgUILhCTAlAiWMtoYNh1aAFwAHgAgAFniAHbCpIBBDIxLjGYAQCgAQGqAQdnd3Mtd2l6&sclient=psy-ab
- Gw lupa tadi webnya mana aja 
