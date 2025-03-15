---
title: "VISUALISASI DATA KATEGORIK"
author: "Patricia Pingkan Kumenap"
date: "2025-03-09"
output: 
  html_document:
    toc: yes
    toc_float: yes
    number_sections: yes
    theme: flatly
    highlight: zenburn
    css: cssfile.css
  pdf_document:
    toc: yes
editor_options:
  markdown:
    wrap: 72
---
## BAR CHART 
```{r, echo=TRUE}
library(ggplot2)
ggplot(data = esoph, mapping = aes(x = alcgp)) + 
  geom_bar()
```

Dataset "Esoph" merupakan dataset R Studio yang menyajikan informasi seputar hubungan antara 
konsumsi alkohol dan konsumsi tembakau dengan kanker esofagus. 
Dalam hal ini, dataset "Esoph" memiliki beberapa variabel yang meliputi : 
- egegp : Kelompok Umur 
- alcgp : Kelompok Konsumsi Alkohol 
- tobgp : Kelompok Konsumsi Tembakau 
- ncases : Jumlah Individu yang terdiagnosis kanker esofagus 
- ncontrols : Jumlah individu dalam kelompok tersebut yang tidak terdiagnosis kanker esofagus.

Data set ini terdiri dari 88 observasi dengan total 5 variabel. 

Secara default, geom_bar() menggunakan stat = "count", yang berarti ia akan menghitung jumlah observasi (frekuensi) untuk setiap kategori dari variabel yang disebutkan pada sumbu x dan menampilkannya sebagai tinggi batang. Namun, ketika Anda menggunakan stat = "identity", Anda memberitahu ggplot2 untuk menggunakan nilai yang sudah ada di data sebagai tinggi batang, bukan menghitung frekuensi.

## BAR CHART DENGAN TABEL FREKUENSI
### Membuat tabel frekuensi
```{r, echo=TRUE}
freqtab <- as.data.frame(table(esoph$alcgp))
freqtab
```

### Membuat Bar Chart
```{r, echo=TRUE}
ggplot(data = freqtab, mapping = aes(x = Var1, y = Freq)) + 
  geom_bar(stat = "identity")
```

Cara lain yang dapat digunakan untuk membuat diagram batang ketika data yang kita miliki sudah dalam bentuk tabel frekuensi adalah dengan geom_col().
```{r, echo=TRUE}
ggplot(data = freqtab, mapping = aes(x = Var1, y = Freq)) + 
  geom_col()
```

### Modifikasi Bar Chart
Menambahkan judul, mewarnai grafik, dan memberikan label pada setiap batang.

```{r, echo=TRUE}
ggplot(data = freqtab, mapping = aes(x = Var1, y = Freq)) + 
  geom_col(fill = "coral", alpha = 0.7) + 
  labs(title = "Frekuensi berdasarkan tingkat konsumsi alkohol", 
       x = "Konsumsi Alkohol", 
       y = "Frekuensi") + 
  geom_text(aes(label = Freq), vjust = -0.25)
```

## GROUPED BAR CHART 
```{r, echo=TRUE}
ggplot(data = esoph, mapping=aes(x = alcgp, fill = as.factor(tobgp))) + 
  geom_bar(position = "dodge", stat = "count") +
  labs(
    x = "Tingkat Konsumsi Alkohol",
    fill = "Tingkat Konsumsi Tembakau",
    y = "Jumlah Observasi"
  ) +
  scale_fill_brewer(palette = "Paired") +
  theme_light()
```

## PIE CHART 
Menyiapkan data dengan meringkas menjadi tabel frekuensi
```{r, echo=TRUE}
library(dplyr)
df <- esoph %>%
  group_by(alcgp) %>%
  summarise(counts = n())
df
```
Mengitung posisi label teks sebagai jumlah kumulatif proporsi.

```{r, echo=TRUE}
df <- df %>%
  arrange(desc(alcgp)) %>%
  mutate(
    prop = round(counts * 100 / sum(counts), 1),
    lab.ypos = cumsum(prop) - 0.5 * prop
  )
head(df, 4)
```
- arrange(desc(alcgp)) : mengurutkan kategori alhokol dari terbesar ke terkecil
- prop=round(counts * 100 / sum(counts), 1)) : menghitung proporsi setiap kategori dalam presentase
- lab.ypos=cumsum(prop) - 0.5 * prop) : menentukan posisi teks pada pie chart agar berada di tengah-tengah

Membuat grafik Pie Chart
```{r, echo=TRUE}
library(ggplot2)
library(ggpubr)
ggplot(df, aes(x = "", y = prop, fill = alcgp)) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  geom_text(aes(y = lab.ypos, label = paste0(prop, "%")), color = "white") +
  coord_polar("y", start = 0) +
  ggpubr::fill_palette("jco") +
  theme_void() +
  labs(fill = "Konsumsi Alkohol", title = "Distribusi Konsumsi Alkohol dalam Dataset Esoph")
```
- geom_bar(width = 1, stat = "identity", color = "white") : membuat bar chart dengan warna putih sebagai batas antar bagian
- geom_text(aes(y = lab.ypos, label = paste0(prop, "%")), color = "white") : menambahkan label presentase pada tiap bagian pie chart 
- coord_polar("y", start = 0) : mengubah bar chart menjadi pie chart 
- ggpubr::fill_palette("jco") : menggunakan palet warna dari ggpubr 

## NEEDLE CHART 
```{r, echo=TRUE}
library(ggplot2)
ggplot(data = freqtab, mapping=aes(x = reorder(Var1, Freq), y = Freq)) +
  geom_segment(aes(x = reorder(Var1, Freq),
                   xend = reorder(Var1, Freq),
                   y = 0, yend = Freq),
               color = "skyblue") +
  geom_point(color = "navyblue", size = 4, alpha = 0.6) +
  geom_text(aes(label = Freq), vjust = -1.00) +
  coord_flip() +
  labs(
    y = "Jumlah Observasi",
    x = "Tingkat Konsumsi Alkohol",
    title = "Distribusi Konsumsi Alkohol dalam Dataset Esoph"
  ) +
  theme_minimal()

```

## STACKED BAR CHART 
```{r, echo=TRUE}
ggplot(data=esoph,
   mapping=aes(x=alcgp, fill=as.factor(tobgp)))+
  geom_bar(position = "stack", stat="count")+
 
  labs(x="Tingkat Konsumsi Alkohol", fill="Tingkat Konsumsi Tembakau",
       y="Jumlah Observasi")+
  scale_fill_brewer(palette="Set3")+
  theme_light()  
```
Interpretasi : 
Berwarna merah/pink dengan 30+ batang tembakau per hari : mencapai sekitar 5 di semua kategori konsumsi alkohol
ada sekitar 5 orang dalam tiap kategori alkohol yang merokok lebih dari 30 batang per hari

Berwarna Ungu dengan 20-29 batang per hari : tingginya bertambah dari 5 ke sekitar 10 artinya dalam tiap kelompok konsumsi alkohol adalah sekitar 5 lagi keatas untuk yang merokok lebih dari 20 batang per hari

Berwarna kuning dengan 10-19 batang per hari : tingginya mencapai lebih dari 15. Artinya terdapat tambahan sekitar 5-7 orang yang merokok 10-19 batang per hari

Berwarna hijau dengan 0-9 batang per hari : mencapai sekitar lebih dari 20 yang mana sebagian besar orang dalam tiap kelompok konsumsi alkohol hanya merokok 0-9 batang per hari

Simpulan : 
Warna lebih bawah (merah dan ungu) : menunjukkan kelompok dengan konsumsi tembakau tinggi tetapi jumlah mereka sedikit
warna lebih atas (kuning, hijau) : menunjukkan kelompok dengan konsumsi tembakau lebih rendah tetapi jumlah mereka banyak. 


## MAP 
### DATA SPASIAL 
```{r, echo=TRUE}
#Import Data
library(readxl)
library(sf)

data.spasial <- read_xlsx("C:/Users/Fanndry/Documents/output_datashpsulut1.xlsx",sheet = 1)
head(data.spasial)

shp.Provsulut<- read_sf("C:/Users/Fanndry/Documents/SULUT/Export_Output_2.dbf")
shp.Provsulut

#menggabungkn data

# Import library yang diperlukan
library(sf)
library(dplyr)  # Pastikan library dplyr digunakan untuk left_join

gabung.Provsulut<- left_join(shp.Provsulut,data.spasial,by="ID_2",relationship = "many-to-many")

plot.Provsulut<- ggplot(data = gabung.Provsulut)+
  geom_sf(aes(fill = Latitude.x))+
  scale_fill_distiller("index rate", palette = "PuRd")
plot.Provsulut
```
