# LAPORAN PRAKTIKUM STRUKTUR DATA
## Modul 5 – Singly Linked List (Bagian Kedua)

**Nama** : Risma Auliyani
**NIM** : 109082500120
**Kelas** : S1IF-13-01

---

## 1. Tujuan Praktikum

1. Memahami penggunaan *linked list* dengan *pointer* dan operator-operatornya dalam program.
2. Memahami operasi-operasi dasar dalam *linked list*.
3. Membuat program dengan menggunakan *linked list* dengan *prototype* yang sudah ditentukan.

---

## 2. Dasar Teori

*Singly linked list* adalah struktur data yang terdiri dari kumpulan *node*, di mana tiap *node* menyimpan data (`info`) dan sebuah *pointer* (`next`) yang menunjuk ke *node* berikutnya. *Node* terakhir menunjuk ke `Nil` (`NULL`) sebagai tanda akhir list. List itu sendiri hanya menyimpan satu *pointer* bernama `first` yang menunjuk ke *node* pertama. List dikatakan kosong kalau `first = Nil`.

Pada modul ini operasi yang dibahas adalah **searching**, yaitu mencari *node* tertentu dengan cara menelusuri list dari *node* pertama sampai *node* yang dicari ketemu atau sampai list habis. Operasi pencarian ini juga membantu operasi lain seperti *insert after*, *delete after*, dan *update*, karena kita perlu tahu dulu alamat *node* yang dituju.

Dua operasi lain yang dipakai di laporan ini:
- **Insert First**: *node* baru disambungkan ke *node* pertama yang lama (`P->next = L.first`), lalu `L.first` dipindah ke *node* baru. Akibatnya urutan list selalu terbalik dari urutan *insert*.
- **Update**: mengubah isi data pada *node* yang sudah ada tanpa mengubah susunan list.

---

## 3. Guided

Pada bagian guided, list yang dipakai menyimpan data buah (nama, jumlah, harga). Program terdiri dari `listBuah.h` dan `listBuah.cpp`, dan dijalankan lewat file `main.cpp` yang memanggil fungsi-fungsinya.

### 3.1 Source Code

#### listBuah.h
```cpp
#ifndef LISTBUAH_H
#define LISTBUAH_H
#define Nil NULL

#include<iostream>
using namespace std;

struct buah{
    string nama;
    int jumlah; 
    float harga;
};

typedef buah dataBuah;  

typedef struct node *address; 

struct node{ 
    dataBuah isidata;
    address next;
};

struct linkedlist{ 
    address first;
};

bool isEmpty(linkedlist List);
void createList(linkedlist &List);
address alokasi(string nama, int jumlah, float harga);
void dealokasi(address &node);
void printList(linkedlist List);
void insertFirst(linkedlist &List, address nodeBaru);
void insertAfter(linkedlist &List, address nodeBaru, address prev);
void insertLast(linkedlist &List, address nodeBaru);
void delFirst(linkedlist &List);
void delLast(linkedlist &List);
void delAfter(linkedlist &List, address nodeHapus, address nodePrev);
int nbList(linkedlist List);
void deleteList(linkedlist &List);

void updateFirst(linkedlist List);
void updateLast(linkedlist List);
void updateAfter(linkedlist List, address prev);

void FindNodeByData(linkedlist list, string data);
void FindNodeByAddress(linkedlist list, address node);
void FindNodeByRange(linkedlist list, float hargaAwal, float HargaAkhir);

#endif
```

#### listBuah.cpp
```cpp
#include "listBuah.h"
#include <iostream>
using namespace std;

//fungsi untuk cek apakah list kosong atau tidak
bool isEmpty(linkedlist List) {
    if(List.first == Nil){
        return true; 
    } else {
        return false;
    }
}

//pembuatan linked list kosong
void createList(linkedlist &List) {
    List.first = Nil;
}

//pembuatan node baru dengan menerapkan manajemen memori
address alokasi(string nama, int jumlah, float harga) { 
    address nodeBaru = new node; 
    nodeBaru->isidata.nama = nama;
    nodeBaru->isidata.jumlah = jumlah; 
    nodeBaru->isidata.harga = harga;
    nodeBaru->next = Nil;
    return nodeBaru;
}

//penghapusan node dengan menerapkan manajemen memori
void dealokasi(address &node) {
    node->next = Nil;
    delete node;
}

//prosedur-prosedur untuk insert / menambahkan node baru kedalam list
void insertFirst(linkedlist &List, address nodeBaru) {
    nodeBaru->next = List.first; 
    List.first = nodeBaru;
}

void insertAfter(linkedlist &List, address nodeBaru, address Prev) {
    if (Prev != Nil) {
        nodeBaru->next = Prev->next;
        Prev->next = nodeBaru;
    } else {
        cout << "Node sebelumnya tidak valid!" << endl;
    }
}

void insertLast(linkedlist &List, address nodeBaru) {
    if (isEmpty(List)) {
        List.first = nodeBaru;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu->next != Nil) {
            nodeBantu = nodeBantu->next;
        }
        nodeBantu->next = nodeBaru;
    }
}

//prosedur-prosedur untuk delete / menghapus node yang ada didalam list
void delFirst(linkedlist &List){
    address nodeHapus;
    if (isEmpty(List) == false) {
        nodeHapus = List.first;
        List.first = List.first->next;
        nodeHapus->next = Nil;
        dealokasi(nodeHapus);
        cout << "Node pertama berhasil terhapus!" << endl;
    } else {
        cout << "List kosong!" << endl;
    }
}

void delLast(linkedlist &List){
    address nodeHapus, nodePrev;
    if(isEmpty(List) == false){
        nodeHapus = List.first;
        if(nodeHapus->next == Nil){
            List.first->next = Nil;
            dealokasi(nodeHapus);
        } else { 
            while(nodeHapus->next != Nil){
                nodePrev = nodeHapus; 
                nodeHapus = nodeHapus->next;
            }
            nodePrev->next = Nil; 
            dealokasi(nodeHapus);
        }
        cout << "Node terakhir berhasil terhapus!" << endl;
    } else {
        cout << "list kosong" << endl;
    }
}

void delAfter(linkedlist &List, address nodeHapus, address nodePrev){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else { //jika list tidak kosong
        if (nodePrev != Nil && nodePrev->next != Nil) { 
            nodeHapus = nodePrev->next;       
            nodePrev->next = nodeHapus->next;  
            nodeHapus->next = Nil;         
            dealokasi(nodeHapus);
            cout << "Node setelah node " << nodePrev->isidata.nama << " berhasil terhapus!" << endl;
        } else {
            cout << "Node sebelumnya (prev) tidak valid!" << endl;
        }
    }
}

//prosedur untuk menampilkan isi list
void printList(linkedlist List) {
    if (isEmpty(List)) {
        cout << "List kosong." << endl;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu != Nil) { 
            cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
            nodeBantu = nodeBantu->next;
        }
    }
}

//function untuk menampilkan jumlah node didalam list
int nbList(linkedlist List) {
    int count = 0;
    address nodeBantu = List.first;
    while (nodeBantu != Nil) {
        count++;
        nodeBantu = nodeBantu->next; 
    }
    return count;
}

//prosedur untuk menghapus list (menghapus semua node didalam list)
void deleteList(linkedlist &List){
    address nodeBantu, nodeHapus;
    nodeBantu = List.first;
    while(nodeBantu != Nil){
        nodeHapus = nodeBantu;
        nodeBantu = nodeBantu->next;
        dealokasi(nodeHapus); 
    }
    List.first = Nil; 
    cout << "List berhasil terhapus!" << endl;
}

/*----- MATERI PERTEMUAN 5 - SINGLY LINKED LIST (BAGIAN KEDUA) - UPDATE -----*/
//prosedur-prosedur untuk melakukan update data node
void updateFirst(linkedlist List){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else {
        cout << "Masukkan update data node pertama : " << endl;
        cout << "Nama buah : ";
        cin >> List.first->isidata.nama;
        cout << "Jumlah : ";
        cin >> List.first->isidata.jumlah;
        cout << "Harga : ";
        cin >> List.first->isidata.harga;
        cout << "Data Berhasil Diupdate!" << endl;
        cout << endl;
    }
}

void updateLast(linkedlist List){
    if (isEmpty(List) == true) {
        cout << "List Kosong!" << endl;
    } else {
        address nodeBantu = List.first;
        while (nodeBantu->next != Nil) {
            nodeBantu = nodeBantu->next;
        }
        cout << "masukkan update data node terakhir : " << endl;
        cout << "Nama buah : ";
        cin >> nodeBantu->isidata.nama;
        cout << "Jumlah : ";
        cin >> nodeBantu->isidata.jumlah;
        cout << "Harga : ";
        cin >> nodeBantu->isidata.harga;
        cout << "Data Berhasil Diupdate!" << endl;
        cout << endl;
    }
}

void updateAfter(linkedlist List, address nodePrev){
    if(isEmpty(List) == true){
        cout << "List kosong!" << endl;
    } else {
        if (nodePrev != Nil && nodePrev->next != Nil){
            address nodeBantu = nodePrev->next;
            cout << "masukkan update data node setelah node " << nodePrev->isidata.nama << " : " << endl;
            cout << "Nama buah : ";
            cin >> nodeBantu->isidata.nama;
            cout << "Jumlah : ";
            cin >> nodeBantu->isidata.jumlah;
            cout << "Harga : ";
            cin >> nodeBantu->isidata.harga;
            cout << "Data Berhasil Diupdate!" << endl;
            cout << endl;
        } else {
            cout << "Node sebelumnya (prev) tidak valid!" << endl;
        }
    }
}

/*----- MATERI PERTEMUAN 5 - SINGLY LINKED LIST (BAGIAN KEDUA) - SEARCHING -----*/
//prosedur-prosedur untuk searching data
//prosedur untuk mencari node berdasarkan data
void FindNodeByData(linkedlist list, string data){
    if(isEmpty(list) == true){
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        while(nodeBantu != Nil){
            posisi++;
            if(nodeBantu->isidata.nama == data){
                cout << "Data " << data << " ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false){
            cout << "Node dengan data " << data << " tidak ditemukan!" << endl;
        }
    }
    cout << endl;
}

//prosedur untuk mencari node berdasarkan alamat node
void FindNodeByAddress(linkedlist list, address node) {
    if(isEmpty(list) == true) {
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        while (nodeBantu != Nil) {
            posisi++;
            if(nodeBantu == node) {
                cout << "Node ditemukan pada posisi ke-" << posisi << "!" << endl;
                cout << "Alamat node : " << nodeBantu << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                found = true;
                break;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false) {
            cout << "Node dengan alamat " << node << " tidak ditemukan dalam list!" << endl;
        }
    }
    cout << endl;
}

//prosedur untuk mencari node berdasarkan range data (range harga)
void FindNodeByRange(linkedlist list, float hargaAwal, float hargaAkhir) {
    if(isEmpty(list) == true) {
        cout << "List kosong!" << endl;
    } else {
        address nodeBantu = list.first;
        int posisi = 0;
        bool found = false;
        cout << "--- Buah dalam range harga " << hargaAwal << " - " << hargaAkhir << " ---" << endl;
        cout << "-------------------------------------------" << endl;
        while (nodeBantu != Nil) {
            posisi++;
            float harga = nodeBantu->isidata.harga;
            if(harga >= hargaAwal && harga <= hargaAkhir) {
                cout << "Data ditemukan pada posisi ke-" << posisi << " :" << endl;
                cout << "Nama Buah : " << nodeBantu->isidata.nama << ", Jumlah : " << nodeBantu->isidata.jumlah << ", Harga : " << nodeBantu->isidata.harga << endl;
                cout << "-------------------------------------------" << endl;
                found = true;
            }
            nodeBantu = nodeBantu->next;
        }
        if(found == false) {
            cout << "Tidak ada data buah dalam range harga tersebut!" << endl;
            cout << "-------------------------------------------" << endl;
        }
    }
    cout << endl;
}
```

#### main.cpp (guided)
```cpp
#include "listBuah.h"

#include<iostream>
using namespace std;

int main(){
    linkedlist List;
    address nodeA, nodeB, nodeC, nodeD, nodeE = Nil;
    createList(List);

    dataBuah dtBuah;

    nodeA = alokasi("Jeruk", 100, 3000);
    nodeB = alokasi("Apel", 75, 4000);
    nodeC = alokasi("Pir", 87, 5000);
    nodeD = alokasi("Semangka", 43, 11500);
    nodeE = alokasi("Durian", 15, 31450);

    insertFirst(List, nodeA);
    insertLast(List, nodeB);
    insertAfter(List, nodeC, nodeA);
    insertAfter(List, nodeD, nodeC);
    insertLast(List, nodeE);

    cout << "--- ISI LIST SETELAH DILAKUKAN INSERT ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    updateFirst(List);
    printList(List);
    updateLast(List);
    printList(List);
    updateAfter(List, nodeD);
    printList(List);

    cout << "--- ISI LIST SETELAH DILAKUKAN UPDATE ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    FindNodeByData(List, "kelapa");
    FindNodeByAddress(List, nodeC);
    FindNodeByRange(List, 5000, 10000);

    delFirst(List);
    printList(List);
    delLast(List);
    printList(List);
    delAfter(List, nodeD, nodeC);
    printList(List);

    cout << "--- ISI LIST SETELAH DILAKUKAN DELETE ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    deleteList(List);
    cout << "--- ISI LIST SETELAH DILAKUKAN HAPUS LIST ---" << endl;
    printList(List);
    cout << "jumlah node : " << nbList(List) << endl;
    cout << endl;

    return 0;
}
```

### 3.2 Screenshot Output

![Output guided](https://github.com/rismaauliyani12-arch/MODUL-5/raw/main/Screenshot%202026-10-23%20183448.png)

*Gambar 1. Output program guided*

Link screenshot guided: https://github.com/rismaauliyani12-arch/MODUL-5/blob/main/Screenshot%202026-10-23%20183448.png

### 3.3 Penjelasan

Struktur datanya memakai tiga tipe. `buah` menyimpan `nama`, `jumlah`, dan `harga`. `node` berisi satu data `buah` (`isidata`) dan *pointer* `next`. `linkedlist` hanya berisi `first`.

Fungsi-fungsi yang ada di `listBuah.cpp` bisa dikelompokkan seperti ini:

- **Dasar**: `isEmpty` mengecek apakah `first == Nil`. `createList` mengosongkan list. `alokasi` membuat *node* baru dengan `new` dan mengisi datanya, sedangkan `dealokasi` mengembalikan memorinya dengan `delete`.
- **Insert**: `insertFirst` menaruh *node* di awal list. `insertAfter` menyisipkan *node* setelah *node* `Prev`. `insertLast` menelusuri list sampai *node* terakhir lalu menyambungkan *node* baru di situ (kalau list kosong, *node* baru langsung jadi `first`).
- **Delete**: `delFirst`, `delLast`, dan `delAfter` melepas *node* dari list, lalu `dealokasi` dipanggil supaya memorinya dibebaskan. `deleteList` menghapus semua *node* satu per satu.
- **Update** (materi modul 5): `updateFirst`, `updateLast`, dan `updateAfter` mencari *node* yang dituju lalu meminta input baru untuk nama, jumlah, dan harga.
- **Searching** (materi modul 5): `FindNodeByData` mencari berdasarkan nama buah, `FindNodeByAddress` mencari berdasarkan alamat *node*, dan `FindNodeByRange` menampilkan semua buah yang harganya ada di antara `hargaAwal` dan `hargaAkhir`. Ketiganya menelusuri list memakai *node* bantu (`nodeBantu`) dan sambil menghitung posisi *node*.

Di `main`, dibuat lima *node* buah: Jeruk (100, 3000), Apel (75, 4000), Pir (87, 5000), Semangka (43, 11500), dan Durian (15, 31450). Urutan *insert*-nya: Jeruk dengan `insertFirst`, Apel dengan `insertLast`, Pir disisipkan setelah Jeruk, Semangka disisipkan setelah Pir, lalu Durian dengan `insertLast`. Jadi urutan list setelah *insert* adalah Jeruk, Pir, Semangka, Apel, Durian, dengan jumlah *node* 5.

Tahap berikutnya:
- **Update**: `updateFirst`, `updateLast`, dan `updateAfter` (setelah *node* Semangka) meminta input data baru. Jumlah *node* tetap 5 karena yang berubah hanya isinya.
- **Searching**: `FindNodeByData` mencari buah bernama "kelapa", `FindNodeByAddress` mencari *node* Pir lewat alamatnya, dan `FindNodeByRange` mencari buah dengan harga 5000 sampai 10000.
- **Delete**: `delFirst` menghapus *node* pertama, `delLast` menghapus *node* terakhir, lalu `delAfter` menghapus *node* setelah Pir. Sisa list tinggal 2 *node*.
- **Hapus list**: `deleteList` menghapus semua *node* sehingga jumlah *node* menjadi 0 dan `printList` menampilkan "List kosong."

---

## 4. Unguided

Soal: membuat ADT *Singly Linked List* (`Singlylist.h` dan `Singlylist.cpp`) berisi `createList`, `alokasi`, `dealokasi`, `printInfo`, dan `insertFirst`, lalu mengisinya dengan data 9, 12, 8, 0, 2 dan mengujinya di `main.cpp`. Setelah itu ditambah dua soal: mencari elemen dengan info 8 (fungsi `findElm`) dan menghitung total info seluruh elemen.

Pada program ini angka-angkanya dimasukkan lewat *input* keyboard, bukan ditulis langsung di kode.

### 4.1 Source Code

#### Singlylist.h
```cpp
#ifndef SINGLYLIST_H
#define SINGLYLIST_H

#include <iostream>
#include <cstddef>

#define Nil NULL

using namespace std;

typedef int infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
};

struct List {
    address first;
};

void createList(List &L);
address alokasi(infotype x);
void dealokasi(address &P);
void printInfo(List L);
void insertFirst(List &L, address P);

// soal 3: mencari elemen dengan info x, mengembalikan address-nya (Nil jika tidak ada)
address findElm(List L, infotype x);

// soal 4: menjumlahkan info dari seluruh elemen
int totalInfo(List L);

#endif
```

#### Singlylist.cpp
```cpp
#include "Singlylist.h"

void createList(List &L) {
    L.first = Nil;
}

address alokasi(infotype x) {
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    return P;
}

void dealokasi(address &P) {
    delete P;
    P = Nil;
}

void printInfo(List L) {
    address P = L.first;
    while (P != Nil) {
        cout << P->info << " ";
        P = P->next;
    }
    cout << endl;
}

void insertFirst(List &L, address P) {
    P->next = L.first;
    L.first = P;
}

address findElm(List L, infotype x) {
    address P = L.first;
    while (P != Nil) {
        if (P->info == x) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

int totalInfo(List L) {
    int total = 0;
    address P = L.first;
    while (P != Nil) {
        total += P->info;
        P = P->next;
    }
    return total;
}
```

#### main.cpp
```cpp
#include <iostream>
#include "Singlylist.h"
using namespace std;

int main() {
    List L;
    address P1, P2, P3, P4, P5 = Nil;
    int x1, x2, x3, x4, x5, cari;
    createList(L);

    // input data (urutan insert sesuai soal: 2, 0, 8, 12, 9)
    cout << "Masukkan 5 angka (dipisah spasi/enter): ";
    cin >> x1 >> x2 >> x3 >> x4 >> x5;

    P1 = alokasi(x1);
    insertFirst(L, P1);

    P2 = alokasi(x2);
    insertFirst(L, P2);

    P3 = alokasi(x3);
    insertFirst(L, P3);

    P4 = alokasi(x4);
    insertFirst(L, P4);

    P5 = alokasi(x5);
    insertFirst(L, P5);

    // soal 2: tampilkan isi list
    cout << "Isi list: ";
    printInfo(L);

    // soal 3: cari elemen berdasarkan input
    cout << "Masukkan info yang dicari: ";
    cin >> cari;
    address hasil = findElm(L, cari);
    if (hasil != Nil) {
        cout << hasil->info << " ditemukan dalam list" << endl;
    } else {
        cout << cari << " tidak ditemukan dalam list" << endl;
    }

    // soal 4: total info seluruh elemen
    cout << "Total info dari kelima elemen adalah " << totalInfo(L) << endl;

    return 0;
}
```

### 4.2 Screenshot Output

![Output unguided](https://github.com/rismaauliyani12-arch/MODUL-5/raw/main/Screenshot%202026-10-23%20182310.png)

*Gambar 2. Output program unguided*

Link screenshot unguided: https://github.com/rismaauliyani12-arch/MODUL-5/blob/main/Screenshot%202026-10-23%20182310.png

### 4.3 Contoh Input dan Output

| No | Input data | Info yang dicari | Isi list | Hasil pencarian | Total info |
|----|------------|------------------|----------|-----------------|------------|
| 1 | `2 0 8 12 9` | 8 | `9 12 8 0 2` | 8 ditemukan dalam list | 31 |
| 2 | `2 0 8 12 9` | 5 | `9 12 8 0 2` | 5 tidak ditemukan dalam list | 31 |
| 3 | `5 10 15 20 25` | 15 | `25 20 15 10 5` | 15 ditemukan dalam list | 75 |
| 4 | `3 7 1 9 4` | 9 | `4 9 1 7 3` | 9 ditemukan dalam list | 24 |

### 4.4 Penjelasan

**Struktur data.** `ElmList` berisi `info` (bertipe `infotype` = `int`) dan `next` (bertipe `address`, yaitu *pointer* ke `ElmList`). `List` hanya berisi `first`. `Nil` didefinisikan sebagai `NULL`.

**createList.** Mengisi `L.first` dengan `Nil`, jadi list dimulai dalam keadaan kosong.

**alokasi.** Membuat *node* baru dengan `new ElmList`, mengisi `info` dengan nilai `x`, dan mengisi `next` dengan `Nil`, lalu mengembalikan alamat *node* tersebut.

**dealokasi.** Menghapus *node* dengan `delete` lalu mengisi `P` dengan `Nil` supaya tidak ada *pointer* yang menggantung.

**insertFirst.** *Node* `P` disambungkan ke *node* pertama yang lama (`P->next = L.first`), lalu `L.first` diarahkan ke `P`. Karena tiap data baru selalu masuk di depan, input `2 0 8 12 9` menghasilkan list `9 12 8 0 2`. Urutan tampilan sengaja terbalik dari urutan input.

**printInfo.** Memakai *pointer* bantu `P` yang mulai dari `L.first`, lalu bergerak ke `P->next` sambil menampilkan `info` sampai `P` bernilai `Nil`.

**findElm (soal 3).** Menelusuri list dari *node* pertama. Setiap *node* dicek apakah `info`-nya sama dengan `x`. Kalau sama, alamat *node* itu dikembalikan. Kalau sampai ujung list tidak ada yang cocok, fungsi mengembalikan `Nil`. Di `main`, hasilnya dicek: kalau tidak `Nil` ditampilkan "ditemukan dalam list", kalau `Nil` ditampilkan "tidak ditemukan dalam list".

**totalInfo (soal 4).** Menelusuri seluruh list dengan variabel `total` yang awalnya 0, lalu menambahkan `info` dari setiap *node*. Untuk data 9, 12, 8, 0, 2 hasilnya 31.

---

## 5. Kesimpulan

Dari praktikum ini dapat disimpulkan bahwa *singly linked list* dikelola lewat *pointer*: setiap operasi, mulai dari menambah, mencari, sampai menghitung, dilakukan dengan menelusuri *node* satu per satu lewat `next` sampai bertemu `Nil`. Operasi *searching* menjadi dasar untuk operasi lain karena kita harus tahu alamat *node* yang dituju sebelum melakukan *insert after*, *delete after*, atau *update*. Penggunaan `alokasi` dan `dealokasi` juga penting supaya memori yang dipakai *node* dibuat dan dikembalikan dengan benar. Pada bagian unguided, `insertFirst` membuat urutan list terbalik dari urutan input, dan `findElm` serta `totalInfo` berhasil menemukan elemen dan menghitung total info (31) sesuai soal.
