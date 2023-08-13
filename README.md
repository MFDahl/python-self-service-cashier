# python-self-service-cashier

## LATAR BELAKANG
Andi sebagai pemilik supermarket besar di salahsatu kota di Indonesia memiliki rencana untuk 
melakukan digitalisasi proses pembelian barang secara online yang memungkinkan pembeli melakukan 
self service untuk membeli barang kebutuhan yang diinginkan pada supermarket miliknya.
Harapannya pelanggan dapat secara langsung memasukkan item barang yang dibeli sesuai harga item 
tersebut dan fitur lain apabila pelanggan berubah pikiran dan ingin mengubah isi list belanjanya

## LINK VIDEO 


## ANALISIS PROSES YANG DIBUTUHKAN
Dalam pengembangan sistem ini pelanggan dapat melakukan hal-hal berikut antara lain:
# memulai transaksi dengan menginisiasi mulai belanja
  'Transaction()'
# menambah item dengan menggunakan metode 'add_item()' disertai argumen nama item
  jumlah item, dan harga per item
# mengubah nama, jumlah, atau harga item dengan method
  'update_item_name()','update_item_qty()', 'update_item_price()'
# menghapus salahsatu item dengan 'delete_item()'
# menghapus seluruh item dengan 'reset_transaction()'
# mengecek daftar belanjaan dengan 'check_order()'
# melakukan 'check_out' dan mengdapatkan perhitungan disertai diskon (jika ada).
  setiap kali 'check_out()' dilakukan, data transaksi masuk ke dalam database.

## PENJELASAN DAN CONTOH
Ada dua class: 'Transaction' dan 'Item'.
Customer akan menggunakan kasir lewat kode python, cukup dengan class 'Transaction' saja
Data Pembelian disimpan oleh 'Transaction' sebagai dictionary dengan nama item sebagai key dan object
'item' sebagai value yang memuat jumlah dan harga per item.
...
from orders import *

# inisiasi
trx = Transaction()
assert f"{trx}" == "{}"

# menambah item
trx.add_item('Ayam Goreng', 2, 20000)
assert f"{trx}" == "{'Ayam Goreng': [2, 20000]}"

# mengubah nama, jumlah, atau harga
trx.update_item_name('Ayam Goreng', 'Fried Chicken')
trx.update_item_qty('Fried Chicken', 1)
trx.update_item_price('Fried Chicken', 30000)
assert f"{trx}" == "{'Fried Chicken': [1, 30000]}"

# menghapus sebuah item
trx.delete_item('Fried Chicken')
assert f"{trx} == "{}"

# menghapus seluruh item 
trx.reset_transaction()
assert f"{trx} == "{}"

# menambah item & cek pesanan
trx.add_item('Ayam Goreng', 2, 20000)
trx.check_order()

# melakukan check_out & memasukkan transaksi ke database
trx.check_out()
...
Method 'check_out()' dari 'Transaction' akan menghitung total belanjaan.
Total belanjaan lalu akan dikurangi diskon dan ditampilkan harga final,
Perhitungan diskon untuk selanjutnya bisa dilakukan dengan dua cara.

Contoh-contoh lain dapat dilihat dengan membuka file Jupyter Notebook 'test_cases.ipynb'.

## ATURAN DISKON
Tanpa settingan apapun, secara default promo yang akan digunakan adalah promo per item('BY_ITEM').
Sekarang ada dua jenis promo, 'BY_ITEM' dan 'BY_TRX' dengan aturan lengkap sebagaimana di bawah.
Contoh kode mengganti promo:
trax.set_promo(promos.BY_TRX)
...

## Promo per item (BY_ITEM)
# Jika total harga item diatas Rp. 200.000 maka harga item tersebut dikenakan diskon 5%
# Jika total harga item diatas Rp. 300.000 maka harga item tersebut dikenakan diskon 6%
# jika total harga item diatas Rp. 500.000 maka harga item tersebut dikenakan diskon 7%

Contoh perhitungan: 
|-----------------|------|--------------|----------|------------|---------------|
| Nama Item       |  Qty |  Harga Item  | Subtotal | Diskon (%) | Setelah Diskon|
|-----------------|------|--------------|----------|------------|---------------|
| Ayam Goreng     |  2   |  20,000      |  40,000  | 0%         |  40,000       |
| Mainan Tamiya   |  1   |  300,000     | 300,000  | 0%         | 300,000       |
|-----------------|------|--------------|----------|------------|---------------|
| TOTAL                                 | 340,000  | 6%         | 319,500       |
|---------------------------------------|----------|------------|---------------|
<br>

## STRUKTUR DATABASE SQLite

**Table Name:** 'transactions'

| **Columns:**                                   |
|------------------------------------------------|
| - 'id': INTEGER (PRIMARY KEY, AUTOINCREMENT)   |
| - 'total_harga_rp': INTEGER                    |
| - 'diskon_persen': INTEGER                     |
| - 'harga_final_rp': INTEGER                    |

<br>

**Table Name:** 'order_details'

| **Columns:**                                                            |
|-------------------------------------------------------------------------|
| - 'id': INTEGER (PRIMARY KEY, AUTOINCREMENT                             | 
| - 'transaction_id': INTEGER (FOREIGN KEY REFERENCES 'transactions (id)) |
| - 'nama_item': TEXT                                                     |
| - 'jumlah_item': INTEGER                                                |
| - 'harga_item' : INTEGER                                                |
