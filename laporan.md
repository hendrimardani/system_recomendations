## **Laporan Proyek Machine Learning - Hendri Mardani**
***


## Project Overview
---
Dataset ini tentang film yang berisi database _user_, judul film, dan rating yang diberikan _user_ kepada server dataset ini terdiri dari 610 _user_. Dataset ini dibuat selama 22 tahun yakni dari **29 Maret 1996** sampai **24 September 2018** _file_ tersebut berisi **movies.csv, ratings.csv, tags.csv dan links.csv**. Dalam kasus kali ini kita akan menggunakan sistem rekomendasi kepada user yang sebelumnya pernah disukai ataupun yang belum pernah dilihat film terbaru oleh _user_. Tetapi sebelumnya, kita harus memilah fitur-fitur penting terlebih dahulu yang berhubungan dengan permasalahan kita yang akan digunakan sebagai analisis eksplor lebih lanjut. Kita hanya membutuhkan user yang pernah memberi rating pada film yang pernah dilihat, ataupun belum pernah melihat film sama sekali.


## Business Understanding
---
Adapun permasalah pada kali ini adalah sebagai berikut:
1. Problem Statement
    * Dari banyaknya **610 _user_**, _user_ yang mana yang sering atau paling banyak memberi rating kepada server ?
    * Apakah rating _user_ mempengaruhi pada sistem rekomendasi kita ?
    * Dari sekumpulan _user_ yang ada, _user_ yang manakah menjadi pelanggan setia atau sering memberikan rating pada film-film yang ada?
2. Goals
    * Membuat sebuah visualisasi dalam bentuk histogram dalam masing-masing _user_
    * Membuat sistem rekomendasi berdasarkan fitur genre film (_Content Based Filtering_), dan juga membuat sistem rekomendasi rating _user_ kepada server yang pernah memberikan rating _user_ ke server(_Collaborative Filtering_).
    * Membuat visualisasi _user_ mana yang selalu memberikan rating pada film-film yang ada

* Dalam uraian diatas, kita tentu memilah fitur-fitur terlebih dahulu yang berhubungan dengan sistem rekomendasi salah satunya barang yang akan direkomendasikan dalam hal ini judul film, dan berdasarkan jenis atau isi dari film tersebut.
* Untuk permasalahan evaluasi ini kita akan menggunakan matrix **_Root Mean Squared Error(RMSE)_** sebagai acuan monitor model dalam bentuk _similarity_ kemiripan dalam film yang sudah bernah dilihat ataupun belu pernah dilihat.


## Data Understanding
---
* Nama dataset **Movie Lens Small Latest Dataset**

Dataset ini terdiri dari beberapa fitur salah satunya fitur _user_,_rating_ dan film. Rincian fitur-fitur tersebut sebagai berikut:

**Tabel 1. Deskripsi fitur dataset**

| NO |  Fitur  |                                       Deskripsi                                      |
|:--:|:-------:|:-------------------------------------------------------------------------------------|
|  1 |  movies | Berisi daftar film sekaligus genre film                                              |
|  2 | ratings | Berisi daftar rating yang diberikan pengguna kepada server dalam hal ini rating film |
|  3 |   tags   | Berisi jenis-jenis isi film apakah menyenangkan, menyeramkan, dll                    |
|  4 |  links  | Berisi kode-kode unik film                                                           |

* Dataset movies memiliki **9742** baris **3** kolom
* Dataset ratings memiliki **100836** baris **4** kolom
* Dataset tags memiliki **3683** baris **3** kolom
* Dataset links memiliki **9742** baris **3** kolom

Karena pada dataset **ratings** memiliki banyak jumlah baris pada tahap ini kita gunakan **5000** baris saja sebagai explorasi. Begitu pula dengan dataset **movies** kita hanya gunakan **2500** baris

Link dataset : [dataset](https://www.kaggle.com/datasets/shubhammehta21/movie-lens-small-latest-dataset)


## Data Preparation
---
* _Load_ dataset **_movies_**

Pada tahap ini kita akan membersihkan data atau dalam istilahnya _cleaning dataset_ karena dataset yang didapat tidak selalu bersih, dalam hal ini bisa bermacam-macam seperti: **data duplikat(nilai data yang sama), nilai NaN(nilai yang berisi kosong)** atau dalam kasus karakter _string_ diubah kedalam bentuk _lowercase_ (karakter kecil). Sebelumnya kita harus mengecek terlebih dahulu dataset yang sudah _diload_ yang akan di analisis menggunakan teknik **Explore Data Analisis(EDA)** apakah terdapat nilai kosong atau tidak.

**Tabel 2. Memeriksa nilai kosong**
| #   | Column  | Non-Null Count | Dtype  |
|-----|---------|----------------|--------|
| 0   | movieId | 2500 non-null  | int64  |
| 1   | title   | 2500 non-null  | object |
| 2   | genres  | 2500 non-null  | object |

* Pada **tabel 2** fitur-fitur tersebut tidak memiliki nilai yang kosong darimana kita tahu bahwa fitur-fitur itu tidak memiliki nilai kosong?, kita bisa lihat pada kolom **Non-Null COun** yang berisi 2500 non-null pada masing-masing baris yang artinya pada dataset fitur ini memiliki jumlah baris yang sama, sehingga kita bisa mengeksplor ketahap selanjutnya.

| NO | movieId |                title               | genres                                          |
|:--:|:-------:|:----------------------------------:|-------------------------------------------------|
|  0 |    1    |          Toy Story (1995)          | Adventure\|Animation\|Children\|Comedy\|Fantasy |
|  1 |    2    |           Jumanji (1995)           | Adventure\|Children\|Fantasy                    |
|  2 |    3    |       Grumpier Old Men (1995)      | Comedy\|Romance                                 |
|  3 |    4    |      Waiting to Exhale (1995)      | Comedy\|Drama\|Romance                          |
|  4 |    5    | Father of the Bride Part II (1995) | Comedy                                          |

**Tabel 3. Sebelum _spliting fitur genres_**


* Karena pada dataset _movie.csv_ terdapat fitur _genres_ yang memiliki banyak genre film pada masing-masing film, tentunya kita harus mewakili **1 genre** dalam masing-masing film yang akan kita gunakan sebagai sistem rekomendasi, caranya adalah dengan melakukan _spliting_ pada fitur **genres** tersebut dengan menggunakan **build in function python** fungsi dari bawaan _python_ itu sendiri.

**Tabel 4. Hasil _spliting fitur genres_**
|  NO  |      genres |
|:----:|------------:|
|   0  | Adventure   |
|   1  | Adventure   |
|   2  | Comedy      |
|   3  | Comedy      |
|   4  | Comedy      |
|  ... | ...         |
| 2495 | Drama       |
| 2496 | Drama       |
| 2497 | Drama       |
| 2498 | Crime       |
| 2499 | Documentary |

* Karena sebelumnya kita sudah membuat _spliting fitur_ pada _genres_, selanjutnya kita menggabungkan fitur _genres_ yang baru pada dataset yang akan dianalisis kemudian menghapus _fitur genres_ yang lama.
Untuk perbandingan sebelum dan sesudah _spliting_ bisa dilihat pada **tabel 3 dan tabel 4**

**Tabel 5. Penggabungan _fitur genres_**
|  NO  | movieId | title                              |    genres   |
|:----:|:-------:|------------------------------------|:-----------:|
|   0  |    1    | Toy Story (1995)                   |  Adventure  |
|   1  |    2    | Jumanji (1995)                     |  Adventure  |
|   2  |    3    | Grumpier Old Men (1995)            |    Comedy   |
|   3  |    4    | Waiting to Exhale (1995)           |    Comedy   |
|   4  |    5    | Father of the Bride Part II (1995) |    Comedy   |
|  ... |   ...   | ...                                |     ...     |
| 2495 |   3329  | The Year My Voice Broke (1987)     |    Drama    |
| 2496 |   3330  | Splendor in the Grass (1961)       |    Drama    |
| 2497 |   3331  | My Tutor (1983)                    |    Drama    |
| 2498 |   3334  | Key Largo (1948)                   |    Crime    |
| 2499 |   3338  | For All Mankind (1989)             | Documentary |

* _Load_ dataset **_ratings_**

**Tabel 6. _Load dataset ratings_**
| # | Column    | Non-Null Count | Dtype   |
|---|-----------|----------------|---------|
| 0 | userId    | 5000 non-null  | int64   |
| 1 | movieId   | 5000 non-null  | int64   |
| 2 | rating    | 5000 non-null  | float64 |
| 3 | timestamp | 5000 non-null  | int64   |

* Pada **tabel 6** terdapat **5000** baris **4** fitur, dalam dataset ini tidak memiliki nilai kosong sama seperti pada **tabel2** sehingga kita bisa mengesplornya lebih lanjut.

**Tabel 7. Rangkuman dataset dalam bentuk statistika**
|   #   |    userId   |    movieId    |    rating   |   timestamp  |
|:-----:|:-----------:|:-------------:|:-----------:|:------------:|
| count | 5000.000000 |  5000.000000  | 5000.000000 | 5.000000e+03 |
|  mean |  17.347800  |  18057.196000 |   3.393100  | 1.166446e+09 |
|  std  |   8.266323  |  34330.635129 |   1.077476  | 2.267503e+08 |
|  min  |   1.000000  |    1.000000   |   0.500000  | 8.354410e+08 |
|  25%  |  10.000000  |   904.000000  |   3.000000  | 9.657037e+08 |
|  50%  |  19.000000  |  2485.000000  |   3.500000  | 1.110807e+09 |
|  75%  |  22.000000  |  6936.000000  |   4.000000  | 1.417378e+09 |
|  max  |  32.000000  | 187593.000000 |   5.000000  | 1.535471e+09 |

* Pada **tabel 7** fitur _timestamp_ pada analisis kali ini tidak digunakan karena fitur tersebut tidak memiliki hubungan dengan tujuan dalam kasus kita. Untuk itu kita bisa menghapusnya. Keterangan dalam fitur **_timestamp_** dan **_userId_**

**Tabel 8. Deskripsi fitur pada dataset rating**
| NO |   Fitur   | Deskripsi                                                                                                  |
|:--:|:---------:|------------------------------------------------------------------------------------------------------------|
|  1 |   userId  | Kode user yang digunakan                                                                                   |
|  2 | timestamp | Waktu user ketika menonton film( Berkunjung) ke sebuah bioskop, dalam hal ini menggunakan satuan timestamp |

**Tabel 9. Penghapusan fitur _timestamp_**
|  NO  | userId | movieId | rating |
|:----:|:------:|:-------:|:------:|
|   0  |    1   |    1    |   4.0  |
|   1  |    1   |    3    |   4.0  |
|   2  |    1   |    6    |   4.0  |
|   3  |    1   |    47   |   5.0  |
|   4  |    1   |    50   |   5.0  |
|  ... |   ...  |   ...   |   ...  |
| 4995 |   32   |   780   |   3.0  |
| 4996 |   32   |   784   |   3.0  |
| 4997 |   32   |   786   |   3.0  |
| 4998 |   32   |   788   |   1.0  |
| 4999 |   32   |   799   |   3.0  |

Setelah penghapusan fitur yang tidak digunakan selanjutnya kita mengeksplor pada fitur rating bisa dilihat pada **tabel 7** terlihat bahwa nilai minimal pada fitur rating memiliki nilai 0.5 rating dan nilai maksimal 5, padahal secara umum nilai minimal rating itu memiliki kisaran **1** sampai **5** saja. Hal ini tidaklah wajah untuk itu kita bisa mengubah nilai tersebut

**Tabel 10. Fitur rating memiliki nilai 0.5**
|  NO  | userId | movieId | rating |
|:----:|:------:|:-------:|:------:|
|  261 |    3   |    31   |   0.5  |
|  262 |    3   |   527   |   0.5  |
|  263 |    3   |   647   |   0.5  |
|  264 |    3   |   688   |   0.5  |
|  265 |    3   |   720   |   0.5  |
|  ... |   ...  |   ...   |   ...  |
| 3776 |   22   |  69757  |   0.5  |
| 3777 |   22   |  70286  |   0.5  |
| 3778 |   22   |  71464  |   0.5  |
| 3780 |   22   |  74789  |   0.5  |
| 4697 |   28   |  58293  |   0.5  |

* Untuk perubahan itu, kita bisa menggunakan bantuan modul `numpy` atau `pandas`, tetapi pada kali ini kita menggunakan bawaan dari `pandas` menggunakan _method_ `round()`
**Tabel 11. Setelah perubahan nilai fitur rating**

|   #   |    userId   |    movieId    |    rating   |
|:-----:|:-----------:|:-------------:|:-----------:|
| count | 5000.000000 |  5000.000000  | 5000.000000 |
|  mean |  17.347800  |  18057.196000 |   3.394200  |
|  std  |   8.266323  |  34330.635129 |   1.111869  |
|  min  |   1.000000  |    1.000000   |   0.000000  |
|  25%  |  10.000000  |   904.000000  |   3.000000  |
|  50%  |  19.000000  |  2485.000000  |   4.000000  |
|  75%  |  22.000000  |  6936.000000  |   4.000000  |
|  max  |  32.000000  | 187593.000000 |   5.000000  |

Selanjutnya kita akan mengeksplor pada fitur rating dan _user_, untuk mengehui _user_ mana yang paling banyak memberi rating pada film yang pernah ditonton, dan juga dengan ini kita bisa memutuskan bahwa _user_ tersebut menjadi pelanggan setia yang selalu menonton film pada bioskop kita, yang mana sesuai dengan **tujuan** kita yaitu untuk mengetahui _user_ mana yang selalu memberikan rating pada film kita.

**Tabel 12. _User_ yang berlangganan**

| userId |  movieId | rating |
|-------:|:--------:|:------:|
|   18   | 20192700 | 1879.0 |
|   19   |  1324751 | 1833.0 |
|   28   | 11466185 | 1705.0 |
|   21   | 23829552 | 1471.0 |
|    6   |  122159  | 1097.0 |
|    1   |  430268  | 1013.0 |
|   20   |  782407  |  866.0 |
|    4   |  428140  |  768.0 |
|    7   |  1658055 |  485.0 |
|   27   |  242812  |  479.0 |



* Pada **tabel 12** terlihat bahwa kode _user_ **18** selalu memberikan rating pada film kita entah itu rating 1, atau 5. Dengan ini kita bisa menyimpulkan bahwa _user_ **18** adalah pelanggan kita diantara _user-user_ yang lainnya.

![hist](https://user-images.githubusercontent.com/49816104/196033257-bdb73bce-c97d-4a87-82b5-fa397105c3b6.jpg)

**Gambar 1. _User_ yang berlangganan dalam bentuk visualisasi**

 * _Load_ dataset **_tags_**

**Tabel 13. _Load_ dataset _tags_**
| # | Column    | Non-Null Count | Dtype  |
|---|-----------|----------------|--------|
| 0 | userId    | 3683 non-null  | int64  |
| 1 | movieId   | 3683 non-null  | int64  |
| 2 | tag       | 3683 non-null  | object |
| 3 | timestamp | 3683 non-null  | int64  |

* Pada **Tabel 13** dataset _tags_ memiliki **3683** baris **4** fitur, sebenarnya kita bisa mengeksplor lebih jauh dari dataset _tags_ ini, namun dalam kasus ini kita tidak menggunakan dataset ini.

* _Load_ dataset **_links_**

**Tabel 14. _Load_ dataset _links_**
| # |  Column | Non-Null Count |  Dtype  |
|:-:|:-------:|:--------------:|:-------:|
| 0 | movieId |  9742 non-null |  int64  |
| 1 |  imdbId |  9742 non-null |  int64  |
| 2 |  tmdbId |  9734 non-null | float64 |

* Pada **tabel 14** dataset _links_ memiliki **9742** baris **3** fitur, dataset ini sama seperti dataset _tags_, pada explorasi kali ini kita tidak menggunakannya. Karena pada dataset ini tidak memiliki hubungan fitur-fitur yang dibutuhkan untuk sistem rekomendasi.

Sebelumnya kita sudah mmiliki data analisis antara dataset _ratings.csv_ dan _movies.csv_. Dataset inilah yang akan kita pakai dalam sistem rekomendasi

**Tabel 15. Dataset sistem rekomendasi**
|   #  | userId | movieId | rating |                 title                |   genres  |
|:----:|:------:|:-------:|:------:|:------------------------------------:|:---------:|
|   0  |    1   |    1    |   4.0  |           Toy Story (1995)           | Adventure |
|   1  |    1   |    3    |   4.0  |        Grumpier Old Men (1995)       |   Comedy  |
|   2  |    1   |    6    |   4.0  |              Heat (1995)             |   Action  |
|   3  |    1   |    47   |   5.0  |      Seven (a.k.a. Se7en) (1995)     |  Mystery  |
|   4  |    1   |    50   |   5.0  |      Usual Suspects, The (1995)      |   Crime   |
|  ... |   ...  |   ...   |   ...  |                  ...                 |    ...    |
| 4995 |   32   |   780   |   3.0  | Independence Day (a.k.a. ID4) (1996) |   Action  |
| 4996 |   32   |   784   |   3.0  |         Cable Guy, The (1996)        |   Comedy  |
| 4997 |   32   |   786   |   3.0  |             Eraser (1996)            |   Action  |
| 4998 |   32   |   788   |   1.0  |      Nutty Professor, The (1996)     |   Comedy  |
| 4999 |   32   |   799   |   3.0  |        Frighteners, The (1996)       |   Comedy  |

* Sebelumnya kita harus memeriksa lagi apakah setelah proses penggabungan data, apakah masing-masing fitur kita memiliki nilai yang kosong.

**Tabel 16. Fitur dengan nilai yang kosong**
|  userId |   0  |
|:-------:|:----:|
| movieId |   0  |
|  rating |   0  |
|  title  | 2054 |
|  genres | 2054 |

* Pada **tabel 16** fitur _title dan genres_ memiliki nilai yag kosong yang dengan **2054** baris, karena memiliki jumlah baris yang sangat banyak kita bisa menghapusnya dari masing-masing fitur. Sehinggan yang awalnya memiliki **5000** baris menjadi **2946** baris saja.

Setelah proses penghapusan nilai kosong, dataset kita memiliki jumlah film **1226** dengan jumlah genre sebanyak **17** genre. Nama-nama genre tersebut yaitu: **Adventure, Comedy, Action, Mystery, Crime, Thriller, Drama, Animation, Children, Horror, Documentary, Film-Noir, Fantasy, Western, Sci-Fi, Musical dan Romance.**
* Karena pada fitur _genres_ memiliki karakter _uppercase_ kita terlebih dahuliu harus dirubah kedalam _lowercase_ selain itu dalam nilai fitur _genres_ kita memiliki nilai **Sci-Fi** dalam karakter ini memiliki karakter `( - )` , hal tersebut sangat sensitif pada proses model, sehingga kita harus menghilangkannya.

**Tabel 17. Pengubahan karakter _uppercase_ pada fitur _genres_**
|  NO  | userId | movieId | rating |                 title                |   genres  |
|:----:|:------:|:-------:|:------:|:------------------------------------:|:---------:|
|   0  |    1   |    1    |   4.0  |           Toy Story (1995)           | adventure |
|   1  |    1   |    3    |   4.0  |        Grumpier Old Men (1995)       |   comedy  |
|   2  |    1   |    6    |   4.0  |              Heat (1995)             |   action  |
|   3  |    1   |    47   |   5.0  |      Seven (a.k.a. Se7en) (1995)     |  mystery  |
|   4  |    1   |    50   |   5.0  |      Usual Suspects, The (1995)      |   crime   |
|  ... |   ...  |   ...   |   ...  |                  ...                 |    ...    |
| 4995 |   32   |   780   |   3.0  | Independence Day (a.k.a. ID4) (1996) |   action  |
| 4996 |   32   |   784   |   3.0  |         Cable Guy, The (1996)        |   comedy  |
| 4997 |   32   |   786   |   3.0  |             Eraser (1996)            |   action  |
| 4998 |   32   |   788   |   1.0  |      Nutty Professor, The (1996)     |   comedy  |
| 4999 |   32   |   799   |   3.0  |        Frighteners, The (1996)       |   comedy  |

* Setelah proses pengubahan bentuk karakterk. Langkah selanjutnya kita menghapus data duplikat(data yang bernilai sama pada fitur _movieId_) dengan menggunakan _method_ bawaan pandas yaitu `data.drop_duplicates()`, bisa dilihat pada **tabel 18**

**Tabel 18. Penghapusan data duplikasi pada fitur _movieId_**
|  NO  | userID | movieId | rating |                       title                       |    genres   |
|:----:|:------:|:-------:|:------:|:-------------------------------------------------:|:-----------:|
|   0  |    1   |    1    |   4.0  |                  Toy Story (1995)                 |  adventure  |
|   1  |    1   |    3    |   4.0  |              Grumpier Old Men (1995)              |    comedy   |
|   2  |    1   |    6    |   4.0  |                    Heat (1995)                    |    action   |
|   3  |    1   |    47   |   5.0  |            Seven (a.k.a. Se7en) (1995)            |   mystery   |
|   4  |    1   |    50   |   5.0  |             Usual Suspects, The (1995)            |    crime    |
|  ... |   ...  |   ...   |   ...  |                        ...                        |     ...     |
| 4951 |   32   |   194   |   5.0  |                    Smoke (1995)                   |    comedy   |
| 4952 |   32   |   203   |   4.0  | To Wong Foo, Thanks for Everything! Julie Newm... |    comedy   |
| 4955 |   32   |   246   |   4.0  |                 Hoop Dreams (1994)                | documentary |
| 4974 |   32   |   471   |   3.0  |            Hudsucker Proxy, The (1994)            |    comedy   |
| 4997 |   32   |   786   |   3.0  |                   Eraser (1996)                   |    action   |

Disini terlihat perbedaan tipedata pada fitur rating dengan tipedata **float** kita harus mengubahnya terlebih dahulu kedalam tipedata **int** sebelum lanjut ketahap berikutnya. lihat pada **tabel 19**

**Tabel 19. Pengubahan tipedata pada fitur rating**
|  NO  | userId | movieId | rating |                       title                       |    genres   |
|:----:|:------:|:-------:|:------:|:-------------------------------------------------:|:-----------:|
|   0  |    1   |    1    |    4   |                  Toy Story (1995)                 |  adventure  |
|   1  |    1   |    3    |    4   |              Grumpier Old Men (1995)              |    comedy   |
|   2  |    1   |    6    |    4   |                    Heat (1995)                    |    action   |
|   3  |    1   |    47   |    5   |            Seven (a.k.a. Se7en) (1995)            |   mystery   |
|   4  |    1   |    50   |    5   |             Usual Suspects, The (1995)            |    crime    |
|  ... |   ...  |   ...   |   ...  |                        ...                        |     ...     |
| 4951 |   32   |   194   |    5   |                    Smoke (1995)                   |    comedy   |
| 4952 |   32   |   203   |    4   | To Wong Foo, Thanks for Everything! Julie Newm... |    comedy   |
| 4955 |   32   |   246   |    4   |                 Hoop Dreams (1994)                | documentary |
| 4974 |   32   |   471   |    3   |            Hudsucker Proxy, The (1994)            |    comedy   |
| 4997 |   32   |   786   |    3   |                   Eraser (1996)                   |    action   |

* Kemudian setelah itu kita menggunakan _method_ `reset_index()` tujuannya adalah untuk mereset index, tentu kita sudah melakukan tahapan mulai dari proses penghapus nilai kosong, transofrmasi dataset, dan sebagainya. Padahal data kita yang sebenarnya tidak memiliki **5000** baris melainkan kurang dari itu.

**Tabel 20. Fungsi reset_index**
|  NO  | index | userId | movieId | rating |                       title                       |    genres   |
|:----:|:-----:|:------:|:-------:|:------:|:-------------------------------------------------:|:-----------:|
|   0  |   0   |    1   |    1    |    4   |                  Toy Story (1995)                 |  adventure  |
|   1  |   1   |    1   |    3    |    4   |              Grumpier Old Men (1995)              |    comedy   |
|   2  |   2   |    1   |    6    |    4   |                    Heat (1995)                    |    action   |
|   3  |   3   |    1   |    47   |    5   |            Seven (a.k.a. Se7en) (1995)            |   mystery   |
|   4  |   4   |    1   |    50   |    5   |             Usual Suspects, The (1995)            |    crime    |
|  ... |  ...  |   ...  |   ...   |   ...  |                        ...                        |     ...     |
| 1221 |  4951 |   32   |   194   |    5   |                    Smoke (1995)                   |    comedy   |
| 1222 |  4952 |   32   |   203   |    4   | To Wong Foo, Thanks for Everything! Julie Newm... |    comedy   |
| 1223 |  4955 |   32   |   246   |    4   |                 Hoop Dreams (1994)                | documentary |
| 1224 |  4974 |   32   |   471   |    3   |            Hudsucker Proxy, The (1994)            |    comedy   |
| 1225 |  4997 |   32   |   786   |    3   |                   Eraser (1996)                   |    action   |


* Pada **Tabel 20** kita melihat hal yang janggal, yaitu memiliki fitur baru bernama index, hal ini sangatlah menggangu, untuk itu kita bisa menghapus fitur index tersebut karena tidak masuk dalam analisis kita.

**Tabel 21. Penghapusan fitur index**
|  NO  | userId | movieId | rating |                       title                       |    genres   |
|:----:|:------:|:-------:|:------:|:-------------------------------------------------:|:-----------:|
|   0  |    1   |    1    |    4   |                  Toy Story (1995)                 |  adventure  |
|   1  |    1   |    3    |    4   |              Grumpier Old Men (1995)              |    comedy   |
|   2  |    1   |    6    |    4   |                    Heat (1995)                    |    action   |
|   3  |    1   |    47   |    5   |            Seven (a.k.a. Se7en) (1995)            |   mystery   |
|   4  |    1   |    50   |    5   |             Usual Suspects, The (1995)            |    crime    |
|  ... |   ...  |   ...   |   ...  |                        ...                        |     ...     |
| 1221 |   32   |   194   |    5   |                    Smoke (1995)                   |    comedy   |
| 1222 |   32   |   203   |    4   | To Wong Foo, Thanks for Everything! Julie Newm... |    comedy   |
| 1223 |   32   |   246   |    4   |                 Hoop Dreams (1994)                | documentary |
| 1224 |   32   |   471   |    3   |            Hudsucker Proxy, The (1994)            |    comedy   |
| 1225 |   32   |   786   |    3   |                   Eraser (1996)                   |    action   |



* Pada **tabel 21** terlihat jelas bahwa dataset kita memang kurang dari **5000** baris menjadi **2946** baris, hal ini dikarenakan kita sudah melakukan tahapan penghapusan fitur, transformasi data, data _cleaning_ dll.

Sebelum pada tahap model, kita membuat dataset baru terlebih dahulu tujuannya adalah untuk mengelompokkan data pada sistem rekomendasi **Conten Based Filtering** tentu fitur-fitur yang ada pada film seperti **kode film, judul film, dan genre film**. Untuk itu kita membuat sebuah variabel bernama **data_baru**, kita seleksi dulu fitur yang akan digunakan pada kali ini kita gunakan fitur:**movieId, title, dan genres** lalu buat kedalam tabel dataframe pandas. Hasilnya seperti pada **tabel 22** berikut.

**Tabel 22. Pembuatan dataset baru dengan variabel bernama data_baru**
|  NO  | id_film |                     judul_film                    |  genre_film |
|:----:|:-------:|:-------------------------------------------------:|:-----------:|
|   0  |    1    |                  Toy Story (1995)                 |  adventure  |
|   1  |    3    |              Grumpier Old Men (1995)              |    comedy   |
|   2  |    6    |                    Heat (1995)                    |    action   |
|   3  |    47   |            Seven (a.k.a. Se7en) (1995)            |   mystery   |
|   4  |    50   |             Usual Suspects, The (1995)            |    crime    |
|  ... |   ...   |                        ...                        |     ...     |
| 1221 |   194   |                    Smoke (1995)                   |    comedy   |
| 1222 |   203   | To Wong Foo, Thanks for Everything! Julie Newm... |    comedy   |
| 1223 |   246   |                 Hoop Dreams (1994)                | documentary |
| 1224 |   471   |            Hudsucker Proxy, The (1994)            |    comedy   |
| 1225 |   786   |                   Eraser (1996)                   |    action   |

Selanjutnya kita tahap pada modeling setelah proses _cleaning_ data, transformasi data, penghapusan fitur.


## Modeling
---

* Content Based Filtering
    * Pada tahap ini kita akan gunakan sistem rekomendasi berdasarkan barang dalam hal ini( Film ) atau yang sering kita sebut **Content Based Filtering**. Dalam hak ini, kita melibatkan fungsi `TfidfVectorizer()` yang terdapat pada modul scikit-learn fungsinya adalah untuk mentransformasikan nilai fitur kedalam matrix yang akan diproses model.

    * Selanjutnya matrix yang sudah dibuat kita masukkan kedalam fungsi `cosine_similarity()` yang terdapat pada modul yang sama yaitu scikit-learn tujuannya adalah untuk menentukan kemiripan barang(film) _user_ dengan barang(film) yang ada pada server. Contohnya adalah ketika _user_ ingin mencari film dengan genre adventure maka akan muncul dengan genre yang sama yang direkomendasikan oleh server.

    * Untuk cara kerja sistem rekomendasi **Content Based Filtering adalah ketika _user_ menyukai judul film toy story dengan genre adventure 

    **Tabel 23. User menyukai film toy story dengan genre adventure**
    | NO | id_film |    judul_film    | genre_film |
    |---:|:-------:|:----------------:|:----------:|
    |  0 |    1    | Toy Story (1995) |  adventure |

    * Pada **tabel 23** sebagai contoh _kode user_ **1** menyukai film toy story dengan genre adventur, maka sistem akan merekomendasikan dengan genre yang sesua dengan user sukai yaitu **adventure**, hasil dari _output_ sistem bisa dilihat pada **tabel 24**

    **Tabel 24. Hasil _output_ sistem rekomendasi**
    | NO |                     judul_film                    | genre_film |
    |:--:|:-------------------------------------------------:|:----------:|
    |  0 |  Grand Day Out with Wallace and Gromit, A (1989)  |  adventure |
    |  1 |               Howard the Duck (1986)              |  adventure |
    |  2 | Adventures of Buckaroo Banzai Across the 8th D... |  adventure |
    |  3 |                   Legend (1985)                   |  adventure |
    |  4 | 101 Dalmatians (One Hundred and One Dalmatians... |  adventure |

    * Pada **tabel 24** sistem rekomendasi akan merekomendasikan dengan nilai top 5 teratas.

    * Untuk perhitungan precision bisa menggunanakan rumus sebagai berikut:

    ## PRECISION = $\frac{\alpha}{\sum}$

    Dengan :
    * $\alpha$ = Jumlah genre film yang disukai oleh _user_
    * $\sum$ = Jumlah data keseluruhan

    Fungsi precision sendiri yaitu untuk mengetahui tingkat positif (kemiripan) berdasarkan genre_film dalam hal ini.

* Colaborative Filtering
    * Pada tahap kedua ini kita akan gunakan sistem rekomendasi berdasarkan rating yang diberikan _user_ dimasa lalu kepada barang (film), dalam hal ini terlebih dahulu kita melakukan _mapping_ terlebih dahulu ada fitur _id_user_ dan _id_film_ yang ada pada dataset rating, setelah itu fitur yang sudah di_mapping_ digabungkan dengan dataset rating untuk membuat fitur baru bernama fitur user, dan fitur film

    * Setelah itu kita cari nilai maksimal dan minimum pada fitur rating, dalam kali ini adalah 0.0 dan 5.0, lalu fitur tersebut kita normalisasi tujuannya adalah supaya nilai rentang berada pada **0 sampai 1** agar model mudah memproses fitur

    * Membagi data menggunakan _method_ `train_test_split()` pada modul scikit`-learn tujuannya adalah untuk membagi porsi dataset training dan dataset validasi, porsi tersebut ditentukan dengan aturan umum yakni 80:20, 80 untuk data training dan 20 untuk data validasi.

    * Membuat sebuah arsitektur _neural network_ yang melibatkan `layers.Embedded` yang ada pada modul keras. Dalam kompiler model  kita gunakan sebagai berikut:
        * Loss = `BinaryCrossentropy()`
        * Optimizer = `Adam()` dengan learning rate default yaitu 1e-3(0.001)
        * metriks = `Root Mean Squared Error()` sebagai monitor model

    * Kemudian tahap terakhir melakukan training setelah arsitektur _neural network_ dibuat dengan waktu 100 pelatihan

    * Selanjutnya kita tampilkan _output_ dari model dengan teknik _Colaborative FIltering_ ini menggunakan prediksi model yang sudah dilatih _neural network_ . Hasil dari prediksinya pada **tabel 26**

    **Tabel 25. Daftar rating tertinggi _user_ ke film**
    | Daftar film rating tertinggi dari _user_ | Genre Film |
    |------------------------------------------|------------|
    | Princess Bride, The (1987)               | action     |
    | Rear Window (1954)                       | mystery    |
    | Maltese Falcon, The (1941)               | filmnoir   |
    | Ferris Bueller's Day Off (1986)          | comedy     |
    | Nightmare Before Christmas, The (1993)   | animation  |

    **Tabel 26. Hasil prediksi teknik _colaborative filtering_**
    | Film                               | Genre Film |
    |------------------------------------|------------|
    | Wizard of Oz, The (1939)           | adventure  |
    | West Side Story (1961)             | drama      |
    | Cookie's Fortune (1999)            | comedy     |
    | First Knight (1995)                | action     |
    | Houseguest (1994)                  | comedy     |
    | Just Cause (1995)                  | mystery    |
    | Miracle on 34th Street (1994)      | drama      |
    | Aristocats, The (1970)             | animation  |
    | Producers, The (1968)              | comedy     |
    | Madness of King George, The (1994) | comedy     |

    * Pada **tabel 25** adalah daftar film yang pernah ditonton yang diberikan rating _user_ dengan rating paling tinggi dalam hal ini sebanyak 5 film berdasarkan genre film. Sedangkan pada **tabel 26** adalah hasil prediksi film sistem rekomendasi yang harus direkomendasikan kepada _user_ dalam hal ini sebanyak 10 film berdasarkan genrenya. 

    * Dari hasil prediksi antara kedua **tabel 25** dan **tabel 26** sistem tepat merekomendasikan berdasarkan genre film yang disukai _user_ genre-genre yang disukai _user_ tersebut :**action,mystery, filmnoir, comedy dan animation**, sehinga kita bisa merekomendasikan genre-genre tersebut kepada _user_ 

## Evaluation
---
* Pada metriks evaluasi kali ini kita gunakan **Root Mean Squared Error(RMSE)** untuk proses monitoring model, dengan menggunakan kinerja rumus sebagai berikut:

## RMSE = $\frac{1}{2}(\frac{(\sum(y - yi))}{n})$

Dengan :

* $\sum$ = jumlah data
* y = nilai aktual
* yi = nilai prediksi

![rmse](https://user-images.githubusercontent.com/49816104/196038689-4ac9de3a-d160-4272-bdc4-0f69129b63b2.jpg)

**Gambar 2. Evaluasi model dalam visualisasi**

* Pada **gambar 2** terlihat bahwa hasil training menunjukan **overfiting**, yang artinya model tidak bisa memprediksi data baru. Tetapi pada kasus ini kita gunakan model sebagai acuan rekomendasi kepada _user_ dengan _user_ yang belum pernah memberikan rating kepada film baru dengan genre adventure ataupun komedi. Sehingga dengan ini model bisa memberikan rekomendasi kepada _user_ yang bersangkutan.

## Kesimpulan
---
Tahap terakhir seuai dengan **goals** kita yaitu yang pertama visualisasi dalam bentuk histogram ada pada **gambar 1**. Sistem rekomendasi berdasarkan barang (Content Based Filtering) dan Colaborative Filtering ada pada tahap modeling, dan untuk melihat _user_ pelanggan sama seperti **gambar 1**. 

Berdasarkan teknik-teknik yang dilakukan sistem rekomendasi bergantung sesuai kebutuhan jika kita memiliki banyak data rating bisa menggunakan teknik **Colaborative Filtering** film yang disukai _user_ dimasa lalu, begitupun sebaliknya jika kita memiliki _user_ baru, maka langkah yang tepat adalah menggunakan teknik **Content Based Filtering**, yang mana cara kerja teknik ini menyesuakan/menghitung film berdasarkan kemiripan, kemudian hasil tersebut direkomendasikan kepada _user_ baru.