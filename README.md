# Manajemen System Parkir Mall
Sistem ini adalah aplikasi berbasis C yang digunakan untuk mengelola parkir di sebuah mall. Aplikasi ini menyediakan fitur untuk mencatat kendaraan yang masuk dan keluar, menghitung biaya parkir berdasarkan durasi, serta menghasilkan laporan pendapatan.

## Anggota Kelompok 
1. Besty Mega Fauziah (L0124007)
2. Deoshi Anessah Zheren Areja (L0124009)
3. Syifa Qurrota Salsabila (L0124032)

## Fitur Utama
1. **Masuk Parkir** :
   - Memilih slot parkir yang tersedia.
   - Mencatat informasi kendaraan, seperti jenis (motor/mobil), plat nomor, dan waktu masuk.
3. **Keluar Parkir** :
   - Masukkan plat nomor kendaraan untuk mencatat waktu keluar.
   - Menghitung biaya parkir berdasarkan durasi dan jenis kendaraan.
4. **Laporan Pendapatan** :
   - Melihat daftar transaksi parkir yang telah dilakukan.
   - Mengitung total pendapatan parkir.
5. **Visualisasi Slot Parkir** :
   - Menampilkan status slot parkir (kosong atau terisi).

## Cara Penggunaan 
1. Pilih **"Masuk Parkir"** untuk mencatat kendaraan yang masuk:
   - Masukkan plat nomor, jenis kendaraan, waktu masuk, dan pilih slot parkir.
2. Pilih **"Keluar Parkir"** untuk mencatat kendaraan yang keluar:
   - Masukkan plat nomor kendaraan, waktu keluar, dan aplikasi akan menghitung tarif parkir.
3. Pilih **"Laporan"** untuk melihat semua transaksi dan total pendapatan parkir.
4. Pilih **"Keluar"** untuk keluar dari program.

## Struktur Proyek
1. **Slot Parkir** :
   - Dikelola dalam bentuk matriks berukuran 5 bari dan 10 kolom dengan status slot parkir K untuk kosong dan T untuk terisi.
2. **Jenis Kendaraan** :
   - Motor dengan tarif Rp 2.000 untuk jam pertama dan tambahan Rp 1.000 untuk per jam berikutnya.
   - Mobil dengan tarif Rp 3.000 untuk jam pertama dan tambahan Rp 1.000 untuk per jam berikutnya.
3. **Transaksi** :
   - Maksimal 100 transaksi dalam satu sesi program.
4. **Fungsi Utama** :
   - InisialisasiSlotPakir : Mengatur semua slot parkir menjadi kosong saat program dimulai.
   - TampilkanSlotParkir : Menampilkan status slot parkir.
   - PilihSlotParkir : Memilih slot parkir untuk kendaraan.
   - HitungTarif : Menghitung biaya parkir berdasarkan jenis kendaraan dan durasi parkir.

## Source Code 
```c
#include <stdio.h>
#include <time.h>
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_TRANSAKSI 100    // Maksimal transaksi parkir yang dapat dilakukan
#define TOTAL_SLOT 50        // Total jumlah slot parkir
#define TARIF_MOTOR 2000     // Tarif parkir per jam untuk motor
#define TARIF_MOBIL 4000     // Tarif parkir per jam untuk mobil
#define BARIS 5               // Jumlah baris slot parkir
#define KOLOM 10              // Jumlah kolom slot parkir

// Struktur untuk menyimpan informasi transaksi parkir
struct transaksi {
    int jenis;                  // Jenis kendaraan (1 = motor, 2 = mobil)
    int biaya;                  // Biaya parkir yang harus dibayar
    char plat[20];              // Plat nomor kendaraan
    char tgl_masuk[100];        // Tanggal masuk parkir
    char tgl_keluar[100];       // Tanggal keluar parkir
    int baris;                  // Baris slot parkir yang dipilih
    int kolom;                  // Kolom slot parkir yang dipilih
    int jam_masuk, menit_masuk; // Jam dan menit kendaraan masuk
    int jam_keluar, menit_keluar; // Jam dan menit kendaraan keluar
};

bool slotParkir[BARIS][KOLOM];        // Matriks untuk menyimpan status slot parkir (true = terisi, false = kosong)
char infoKendaraan[BARIS][KOLOM][20]; // Matriks untuk menyimpan plat kendaraan yang terparkir di slot tertentu

// Fungsi untuk membersihkan layar
void bersihkanLayar() {
    #ifdef _WIN32
        system("cls");  // Untuk sistem operasi Windows
    #else
        system("clear"); // Untuk sistem operasi selain Windows (Linux/Mac)
    #endif
}

// Fungsi untuk menginisialisasi status semua slot parkir menjadi kosong
void inisialisasiSlotParkir() {
    for (int i = 0; i < BARIS; i++) {
        for (int j = 0; j < KOLOM; j++) {
            slotParkir[i][j] = false;  // Menandakan slot parkir kosong
            strcpy(infoKendaraan[i][j], ""); // Tidak ada kendaraan yang terparkir di slot
        }
    }
}

// Fungsi untuk menampilkan status slot parkir
void tampilkanSlotParkir() {
    printf("\nStatus Slot Parkir:\n");
    printf("K = Kosong \nT = Terisi\n\n");
    printf("   ");
    for (int j = 0; j < KOLOM; j++) {
        printf("%2d ", j);  // Menampilkan nomor kolom slot parkir
    }
    printf("\n");

    for (int i = 0; i < BARIS; i++) {
        printf("%d  ", i);  // Menampilkan nomor baris slot parkir
        for (int j = 0; j < KOLOM; j++) {
            printf("%2c ", slotParkir[i][j] ? 'T' : 'K'); // Menampilkan status slot parkir: T untuk terisi, K untuk kosong
        }
        printf("\n");
    }
}

// Fungsi untuk memilih slot parkir
bool pilihSlotParkir(struct transaksi *trx) {
    int baris, kolom;
    while (1) {
        tampilkanSlotParkir();  // Menampilkan status slot parkir
        printf("\nPilih slot parkir (baris kolom): ");
        if (scanf("%d %d", &baris, &kolom) != 2) {  // Membaca input baris dan kolom slot parkir
            while (getchar() != '\n');  // Flush input buffer
            printf("Input tidak valid. Masukkan dua angka untuk baris dan kolom.\n");
            continue;
        }
        if (baris < 0 || baris >= BARIS || kolom < 0 || kolom >= KOLOM) {
            printf("Pilihan tidak valid.\n");
            continue;
        }
        if (slotParkir[baris][kolom]) {  // Memeriksa apakah slot sudah terisi
            printf("Slot terisi. Pilih slot lain.\n");
            continue;
        }
        slotParkir[baris][kolom] = true;  // Menandakan slot terisi
        trx->baris = baris;               // Menyimpan informasi baris
        trx->kolom = kolom;               // Menyimpan informasi kolom
        return true;
    }
}

// Fungsi untuk menghitung tarif parkir berdasarkan jenis kendaraan dan durasi parkir
int hitungTarif(int jenis, int jam_masuk, int menit_masuk, int jam_keluar, int menit_keluar) {
    int tarif = 0;
    int durasi_menit = (jam_keluar - jam_masuk) * 60 + (menit_keluar - menit_masuk);  // Menghitung durasi parkir dalam menit
    int jam_parkir = (durasi_menit + 59) / 60;  // Membulatkan durasi parkir dalam jam

    if (jam_parkir <= 1) {
        if (jenis == 1) {
            tarif = 2000;  // Tarif motor untuk 1 jam pertama
        } else if (jenis == 2) {
            tarif = 3000;  // Tarif mobil untuk 1 jam pertama
        }
    } else {
        if (jenis == 1) {
            tarif = 2000 + (jam_parkir - 1) * 1000;  // Tarif motor
        } else if (jenis == 2) {
            tarif = 3000 + (jam_parkir - 1) * 1000;  // Tarif mobil
        }
    }
    return tarif;
}

// Fungsi utama untuk menjalankan sistem parkir
int main() {
    struct transaksi trx[MAX_TRANSAKSI];  // Array untuk menyimpan transaksi parkir
    int m = -1, pil, total = 0;
    char yn;

    inisialisasiSlotParkir();  // Menginisialisasi status slot parkir

    bersihkanLayar();

    while(1) {
        // Menampilkan menu utama
        printf("SELAMAT DATANG DI SISTEM MANAJEMEN PARKIR MALL\n");
        printf("==================== Menu ====================\n");
        printf(" 1. Masuk Parkir        \n");
        printf(" 2. Keluar Parkir       \n");
        printf(" 3. Laporan             \n");
        printf(" 4. Keluar              \n");
        printf("==============================================\n");
        printf("Masukan Pilihan anda [ 1 - 4 ] = ");
        scanf("%d", &pil);  // Membaca pilihan menu

        bersihkanLayar();

        if (pil == 1) {  // Transaksi masuk parkir
            while (1) {
                if (m + 1 >= MAX_TRANSAKSI) {  // Mengecek apakah transaksi sudah mencapai batas maksimal
                    printf("Maaf, jumlah transaksi sudah maksimal!\n");
                    break;
                }

                m++;
                printf("\n\n========= FORM TRANSAKSI MASUK =========\n");
                printf("\nPilih slot parkir untuk kendaraan Anda:\n");
                if (!pilihSlotParkir(&trx[m])) {  // Memilih slot parkir untuk kendaraan
                    m--;
                    continue;
                }

                getchar();  // Membaca karakter newline setelah scanf

                printf("\nMasukkan Plat : ");
                fgets(trx[m].plat, sizeof(trx[m].plat), stdin);  // Membaca input plat nomor kendaraan
                size_t len = strlen(trx[m].plat);
                if (len > 0 && trx[m].plat[len - 1] == '\n') {
                    trx[m].plat[len - 1] = '\0';  // Menghapus karakter newline pada akhir plat nomor
                }

                // Memilih jenis kendaraan (motor atau mobil)
                printf("\nJenis kendaraan : \n  1. Motor \n  2. Mobil \n");
                printf("Pilih jenis kendaraan [ 1 / 2 ] : ");
                scanf("%d", &trx[m].jenis);

                // Memasukkan waktu jam masuk
                printf("\nMasukkan Jam Masuk (hh:mm): ");
                scanf("%d:%d", &trx[m].jam_masuk, &trx[m].menit_masuk);

                // Menyimpan tanggal dan waktu masuk
                time_t waktu;
                struct tm *waktu_tm;
                time(&waktu);
                waktu_tm = localtime(&waktu);
                strftime(trx[m].tgl_masuk, sizeof(trx[m].tgl_masuk), "%d %b %Y", waktu_tm);

                // Menyimpan informasi kendaraan pada slot yang dipilih
                strcpy(infoKendaraan[trx[m].baris][trx[m].kolom], trx[m].plat);

                // Menampilkan ringkasan informasi transaksi parkir
                printf("====================================== \n");
                printf("Plat          : %s\n", trx[m].plat);
                printf("Slot          : Baris %d, Kolom %d\n", trx[m].baris, trx[m].kolom);
                printf("Jenis         : %s\n", trx[m].jenis == 1 ? "Motor" : "Mobil");
                printf("Jam Masuk     : %02d:%02d\n", trx[m].jam_masuk, trx[m].menit_masuk);
                printf("Tanggal Masuk : %s\n", trx[m].tgl_masuk);
                printf("====================================== \n");

                // Menanyakan apakah ingin menambah transaksi lagi
                printf("Tambah Lagi ? (y/n) : ");
                scanf(" %c", &yn);  // Menangani karakter newline setelah input angka

                bersihkanLayar();

                // Jika tidak ingin menambah transaksi, kembali ke menu utama
                if (yn != 'y' && yn != 'Y') {
                    break;  // Mengembalikan alur ke menu utama
                }
            }
        } else if (pil == 2) {  // Transaksi keluar parkir
            char plat[20];
            int ditemukan = 0;

            printf("\n\n======== FORM TRANSAKSI KELUAR ========\n");
            printf("Masukkan Plat Kendaraan yang keluar: ");
            scanf("%s", plat);  // Membaca plat kendaraan yang keluar

            // Mencari transaksi parkir berdasarkan plat nomor kendaraan
            for (int i = 0; i <= m; i++) {
                if (strcmp(trx[i].plat, plat) == 0) {
                    // Menghitung biaya parkir jika ditemukan
                    printf("Masukkan Jam Keluar (hh:mm): ");
                    scanf("%d:%d", &trx[i].jam_keluar, &trx[i].menit_keluar);

                    // Menyimpan tanggal dan waktu keluar
                    time_t waktu;
                    struct tm *waktu_tm;
                    time(&waktu);
                    waktu_tm = localtime(&waktu);
                    strftime(trx[i].tgl_keluar, sizeof(trx[i].tgl_keluar), "%d %b %Y", waktu_tm);

                    // Menampilkan ringkasan informasi transaksi keluar parkir
                    printf("====================================== \n");
                    printf("Plat           : %s\n", trx[i].plat);
                    printf("Slot           : Baris %d, Kolom %d\n", trx[i].baris, trx[i].kolom);
                    printf("Jenis          : %s\n", trx[i].jenis == 1 ? "Motor" : "Mobil");
                    printf("Jam Masuk      : %02d:%02d\n", trx[i].jam_masuk, trx[i].menit_masuk);
                    printf("Jam Keluar     : %02d:%02d\n", trx[i].jam_keluar, trx[i].menit_keluar);
                    printf("Tanggal Masuk  : %s\n", trx[i].tgl_masuk);
                    printf("Tanggal Keluar : %s\n", trx[i].tgl_keluar);
                    printf("====================================== \n");

                    trx[i].biaya = hitungTarif(trx[i].jenis, trx[i].jam_masuk, trx[i].menit_masuk, trx[i].jam_keluar, trx[i].menit_keluar);  // Menghitung biaya parkir
                    printf("Biaya parkir   : %d\n", trx[i].biaya);

                    // Menandakan slot parkir kosong kembali
                    slotParkir[trx[i].baris][trx[i].kolom] = false;
                    strcpy(infoKendaraan[trx[i].baris][trx[i].kolom], "");
                    ditemukan = 1;
                    break;
                }
            }

            if (!ditemukan) {
                printf("Plat kendaraan tidak ditemukan!\n");  // Jika kendaraan tidak ditemukan
            }

            // Menunggu input Enter untuk kembali
            printf("Tekan Enter untuk melanjutkan...");
            while (getchar() != '\n');  // Flush input buffer
            getchar();
            bersihkanLayar();
        } else if (pil == 3) {  // Laporan pendapatan parkir
            total = 0;
            printf("\n\n========= LAPORAN PENDAPATAN =========\n\n");

            // Menampilkan semua transaksi parkir
            for (int i = 0; i <= m; i++) {
                total += trx[i].biaya;  // Menambahkan biaya transaksi parkir ke total pendapatan
                printf("Plat           : %s\n", trx[i].plat);
                printf("Jenis          : %s\n", trx[i].jenis == 1 ? "Motor" : "Mobil");
                printf("Slot Parkir    : Baris %d, Kolom %d\n", trx[i].baris, trx[i].kolom);
                printf("Jam Masuk      : %02d:%02d\n", trx[i].jam_masuk, trx[i].menit_masuk);
                printf("Jam Keluar     : %02d:%02d\n", trx[i].jam_keluar, trx[i].menit_keluar);
                printf("Tanggal Masuk  : %s\n", trx[i].tgl_masuk);
                printf("Tanggal Keluar : %s\n", trx[i].tgl_keluar);
                printf("Biaya          : %d\n", trx[i].biaya);
                printf("====================================== \n");
            }

            printf("\nTotal Pendapatan: %d\n", total);  // Menampilkan total pendapatan parkir
            printf("Tekan Enter untuk kembali...");
            while (getchar() != '\n');  // Flush input buffer
            getchar();
            bersihkanLayar();
        } else if (pil == 4) {  // Keluar dari program
            printf("\n\n ======== TERIMAKASIH ======== \n\n");
            break;
        }
    }

    return 0;
}
