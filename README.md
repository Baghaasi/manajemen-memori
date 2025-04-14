# manajemen-memori

![Screenshot (3)](https://github.com/user-attachments/assets/db8ae7be-b89d-43f5-9b08-9fbcdd299b75)

Kode ini ditulis untuk arsitektur x86-64 dan menggunakan sistem panggilan Linux untuk mengalokasikan memori dan menampilkan pesan ke konsol.

# Struktur Kode

## 1.  Section Declaration:

  section .text

     global _start

      section .text: Menandakan bahwa bagian ini berisi kode eksekusi (kode program).
      global _start: Menandakan bahwa label _start dapat diakses dari luar, dan ini adalah titik masuk program.



## 2.  Memulai Program:

  _start:

      Label _start adalah titik awal eksekusi program.
  


## 3.  Alokasi Memori Menggunakan mmap:

   mov rax, 9          ; sys_mmap
  
   xor rdi, rdi        ; alamat awal (NULL)
   mov rsi, 16384      ; ukuran 16 KB
  
   mov rdx, 3          ; PROT_READ | PROT_WRITE
  
   mov r10, 34         ; MAP_PRIVATE | MAP_ANONYMOUS
  
   xor r8, r8          ; file descriptor (tidak digunakan)
  
   xor r9, r9          ; offset (tidak digunakan)
  
   syscall              ; panggil kernel

  *     mov rax, 9: Menetapkan nomor sistem panggilan untuk mmap ke register rax.
  *     xor rdi, rdi: Mengatur alamat awal (parameter pertama untuk mmap) menjadi NULL, yang berarti kernel akan memilih alamat yang sesuai.
  *     mov rsi, 16384: Menetapkan ukuran memori yang akan dialokasikan (16 KB).
  *     mov rdx, 3: Menetapkan proteksi memori (PROT_READ | PROT_WRITE), yang berarti memori dapat dibaca dan ditulis.
  *     mov r10, 34: Menetapkan flag untuk mmap (MAP_PRIVATE | MAP_ANONYMOUS), yang berarti memori bersifat pribadi dan tidak terkait dengan file.
  *     xor r8, r8 dan xor r9, r9: Mengatur file descriptor dan offset menjadi 0, karena tidak digunakan dalam konteks ini.
  *     syscall: Memanggil kernel untuk mengeksekusi sistem panggilan mmap.




## 4.   Pemeriksaan Kesalahan Alokasi:

   cmp rax, -1

je exit             ; keluar jika terjadi kesalahan 

 *     cmp rax, -1: Membandingkan nilai yang dikembalikan oleh mmap (disimpan di rax) dengan -1. Jika mmap gagal, ia akan mengembalikan -1.
 *     je exit: Jika hasil perbandingan sama (artinya alokasi gagal), lompat ke label exit.




## 5.  Inisialisasi Memori yang Dialokasikan:

   mov rdi, rax        ; rdi = alamat yang baru dialokasikan

mov rcx, 4096       ; jumlah DWORD yang akan diinisialisasi

xor rax, rax        ; bersihkan rax (set ke 0)

rep stosd           ; isi memori yang dialokasikan dengan nol

*     mov rdi, rax: Menyimpan alamat yang baru dialokasikan ke register rdi.
*     mov rcx, 4096: Menetapkan jumlah DWORD (4 byte) yang akan diinisialisasi. 16 KB = 4096 DWORD.
*     xor rax, rax: Mengatur rax menjadi 0, yang akan digunakan untuk mengisi memori.
*     rep stosd: Mengisi memori yang dialokasikan dengan nilai 0. rep mengulangi instruksi stosd sebanyak rcx kali.



## 6.   Menulis Pesan ke Standard Output:

  mov rax, 1          ; sys_write

mov rdi, 1          ; file descriptor 1 (stdout)

mov rsi, pesan      ; pointer ke pesan

mov rdx, len        ; panjang pesan

syscall              ; panggil kernel

*     mov rax, 1: Menetapkan nomor sistem panggilan untuk write ke register rax.
*     mov rdi, 1: Menetapkan file descriptor untuk stdout (1).
*     mov rsi, pesan: Menetapkan alamat pesan yang akan ditulis ke rsi.
*     mov rdx, len: Menetapkan panjang pesan
