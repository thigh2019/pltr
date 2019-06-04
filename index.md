# .def[Automatyczne oznaczanie zawartości<br>tłuszczu wewnątrzmięśniowego<br>z obrazów Dixona]

### Agnieszka Sabisz<br>Włodzimierz Bzyl<br>Piotr Arłukowicz<br>Beata Brzeska<br>Arkadiusz Szarmach

## Gdańsk, 6–8 czerwca 2019

---

## .def[Monitorowanie .solid[zmian] zawartości tłuszczu]

### Tłuszcz w mięśniach szkieletowych:

* więcej u osób starszych
* może być skutkiem choroby (Duchenne’a, sarkopenia)

### Celem jest program do:

* automatycznego oddzielenia mięśni uda z obrazu MR<br>
  (.part[moduł 1] [thigh])
* .important[precyzyjnego oznaczania] zawartości
  tłuszczu wewnątrzmięśniowego (.part[moduł 2])

???

**Nasz cel** to program obliczający zawartość tłuszczu z dużą
dokładnością. Z literatury wynika, że dokładność **±1%** jest
możliwa do osiągnięcia.

Taka precyzja, powinna umożliwić monitorowanie **zmian**
tłuszczu w trakcie ćwiczeń lub w rozwoju choroby.

Niestety, obrazy MR zawierają szum.
Dlatego obliczenia **zawsze** będą obarczone błędem
i jeśli chcemy osiąnąć nasz cel, to ten błąd musimy mieć pod kontrolą.

----

Program składa się z dwóch modułów.

Napisanie pierwszego modułu wymagało **pomysłowości**, a także
trochę **szczęścia**. Na naszych danych zadziałała funkcja
z pakietu do **Advanced Normalization Tools for R**
do przetwarzania obrazów mózgu.

Moduł drugi korzysta z metody Dixona.

Hines et al.
[Optimization of Flip Angle to Allow Tradeoffs in T1 Bias and SNR Performance for Fat Quantification](https://cds.ismrm.org/protected/10MProceedings/files/2927_3564.pdf)

> zob. Figure 1.

Hu, Li et al.
[Quantification of Absolute Fat Mass by Magnetic Resonance Imaging: a Validation Study against Chemical Analysis](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3509746/)

> A PDFF error of less than 1% is introduced when
> the true fat fraction is between 0 and 20% and
> an error of only 2–3% was observed for true fat fractions
> between 30 and 50% at 1.5 and 3 Tesla field strengths.

---

## [Dlaczego obrazy Dixona?](http://mriquestions.com/uploads/3/4/5/7/34572113/dixon_method_1984_radiology.pdf)

### 4 obrazy MR: in-phase, out-phase, water (.def[W]), fat (.def[F]).

Intensywność sygnału w obrazach .def[W] i .def[F]
reprezentuje gęstości protonów pochodzących od
–CH<sub>2</sub>– i –CH<sub>3</sub>,
odpowiednio.

<div class="pseudomath">
  <span class=def>signal fat-fraction</span>
    <span class=relation>&equiv;</span>
  <span class=fraction>
    <span class=numerator>S<sub>F</sub></span>
    <span class=.denominator>S<sub>W</sub> + S<sub>F</sub></span>
  </span>
</div>

<div class="pseudomath bbox">
  true fat-fraction
    <span class=relation>≈</span>
  <span class=def>signal fat-fraction</span>
</div>

(dla małego *flip angle*)

???

### Dlaczego obrazy Dixona?

Używamy obrazów Dixona, bo metoda Dixona nie wyklucza
możliwości napisania takiego programu.

----

Z przybliżenia w złotej ramce wynika, że ułamek
**signal fat-fraction** przybliża prawdziwą proporcję tłuszczu w wokselu.

Skrót: PDFF – proton density fat fraction.

* Spectroscopy: **true fat-fraction**.
* Magnetic Resonance: **signal fat-fraction**:
  w liczniku sygnał pochodzący od „tłuszczu”,
  a w mianowniku mamy całkowity sygnał –
  „tłuszczu + wody”.

**signal fat-fraction** jest liczony dla każdego woksela osobno.
W ten sposób otrzymujemy obraz **fat-fraction**.

Przybliżenie jest tym lepsze im mniejszy jest flip angle: 5-10 stopni.
Mniejszy .def[α] flip angle, to większa dokładność przybliżenia TFF.

---

## .def[Dane:] obrazy Dixona, wybrane warstwy <span class="whitespace-left def">Program</span>

<img style="margin-left:-1em;" src=images/in_phase_slab.png height=500 alt="">
<img style="margin-left:4em;" src=images/fat_fraction_in_muscle_slab.png height=500 alt="">

???

### Dane / Program

Danymi do programu są obrazy 3D: in-phase, water, fat
i warstwy w których chcemy policzyć tłuszcz (objętość lub proporcję).

Każdy z obrazów 3D powstał z ok. 10 milionów wokseli / kostek.

Po prawej stronie widzimy obszar mięśni **w obrazie fat-fraction**.
W mięśniach tłuszczu jest mało i dlatego mało co widać.

---

## Tłuszcz na obrazach in-phase i fat-fraction

<img src=images/in_phase.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction.png width=350>

???

### Tłuszcz na obrazach in-phase i fat-fraction

.def[A free lunch is only found in mousetraps] -<br>
czyli po polsku w życiu nie ma nic za darmo.

Niestety, tło obrazu fat-fraction (powietrze + stół),
zawiera **fałszywy** tłuszcz.
Powstaje on w wyniku dzielenia szumu przez szum (0 przez 0).

Oczywiście fałszywy tłuszcz musi zostać usunięty.
I przy okazji przecięta wzdłuż noga.
Na kolejnych obrazach wygumkowano tło i nogę na .background[żółto].

Info: flip angle = 10°

---

## Tłuszcz na obrazach in-phase i fat-fraction bez tła

<img src=images/in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_without_background.png width=350>

???

### Tłuszcz na obrazach in-phase i fat-fraction bez tła

Do zamalowania wokseli w tle wykorzystano
.solid[morphological processing]. Konkretnie operacje: wymazywanie – .def[erode]
i *dodawania* wokseli – .def[dilate].

Na przykład, skorę usunięto za pomocą erode.

---

## Mięśnie na obrazach in-phase i fat-fraction bez tła

<img src=images/muscle_in_in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_in_muscle_without_background.png width=350>

???

### Mięśnie na obrazach in-phase i fat-fraction bez tła

Teraz przyszedł czas na wycięcie mięśni (wykorzystujemy obrazy water i fat)
i kości udowej (in-phase). Musimy to zrobić dokładnie.

Uwaga: **k-means and outliers**.

When the squared error criterion is used, outliers can unduly inﬂuence
the clusters that are found. In particular, when outliers are present,
the resulting cluster centroids (prototypes) are typically not as
representative as they otherwise would be and thus, the
SSE (sum of squared errors) will be higher.

Because of this, it is often useful to discover **outliers** and
eliminate them beforehand. – czego oczywiście nie chcemy robić.

---

## 1 warstwa z mięśniami z wyciętą kością udową

<img style="margin-left:-1.25em;" src=images/intramuscular_fat_2d.png width=800>

???

### 1 warstwa z mięśniami z wyciętą kością udową

Na koniec, w **interesującym nas obszarze** (ROI),
sumujemy objętości wokseli i udziały tłuszczu w każdym wokselu.

Na slajdzie ROI, to pojedyncza warstwa.

Wyniki są w podpisach pod obrazkami.

---

## .warn[Po więcej informacji]

### dodatkowe slajdy, policzone objętości dla kilkunastu pacjentów

### .warn[zapraszamy do przejrzenia repozytorium:]

### https://thigh2019.github.io/pltr/

---

## Informacja o obrazach Dixona

Aparat MRI: 1.5T Siemens Magnetom Aera.<br>
Cewki: automatyczny wybór elementów cewki (dwie cewki body coil oraz cewka kręgosłupowa).<br>
Zakres badania: od początku stawu kolanowego do końca stawu biodrowego.<br>
Pole obrazowania podzielono na dwa obszary tak by objąć cały badany zakres.<br>

Protokół badania.

1. Sekwencja T2-zależna space – woksel 0.8mm x 0.8mm x 3.0mm;
1. Sekwencja T1-zależna vibe dixon – woksel  0.7mm x 0.7mm x 2.5mm

---

# Extras
## misconceptions, bugs, algorithm pathologies

---

### True Fat Fraction .def[≈] Fat Fraction w obrazie fat-fraction fantomu F1

<img src=images/fantom_f1_3d.png width=600>

???

### Przygotowanie fantomu to bułka z masłem… (?)

Nasz pierwszy, przygotowany na szybko (filp-angle, α = 10°),
fantom miał zweryfikować dokładność algorytmu.

W fantomie było 200 cm^3 tłuszczu (gęsiego, odmierzonego strzykawką)
i podobna objętość mięśni (chuda cielęcina).

Policzona przez program objętość tłuszczu w tłuszczu, **panel C**,
to 202.8 cm^3. Wielkość błędu, **1.4%**, zrobiła na nas duże wrażenie.

---

# .def[Featured Extras]

## W. Thomas Dixon – pierwsze obrazy MR (1984)
## Arithmetic on Dixon Images
## Schemat działania programu

---

## W. T. Dixon, Simple Proton Spectroscopic Imaging

<img src=images/dixon_1984.png width=450>

???

* Margarine (Nabisco) – 3 squares.
* Dawn detergent – Procter & Gamble.
* Hen’s egg.
* Container of CuSO_4 in water.

---

## abs(in-phase - (water + fat)) = 0 (.def[from definition])

<img src=images/difference1.png width=450>

---

## abs(A - B) = 0

<img src=images/zero.png width=350>

```
A = sqrt(in_phase^2 + out_phase^2)
B = sqrt(2) * sqrt(water^2 + fat^2)
```

---

## abs(C - 2 * water) = 0

<img src=images/zero2.png width=350>

```
A = (in_phase + out_phase) * (water >= fat)
B = (in_phase - out_phase) * (water < fat)
C = A + B
```

---

## (in-phase - out-phase) / (2 * in_phase) = fat-fraction

<img src=images/fat_fraction3.png width=400>

---

## Schemat działania programu

<pre>
.stage[Dane:] <i>wybrane warstwy z obrazów</i>
  in-phase, water, fat, .def[fat-fraction] ← fat / (water + fat)

.stage[Stage 1a:] <i>liczymy maski dla uda i kości udowej</i>
  .def[tissue-mask] ← z obrazu in-phase liczymy maskę dla całego uda
  .def[femur-mask]  ← wykorzystujemy maskę tissue-mask do policzenia
     maski dla kości udowej

.stage[Stage 1b:] <i>liczymy maskę dla mięśni</i>
  .def[muscle-mask] ← (water > fat) * (tissue-mask - femur-mask)

.stage[Stage 2a:] <i>obliczamy objętość mięśnia</i>
  sumujemy objętości wszystkich wokseli w <b>muscle-mask</b>

.stage[Stage 2b:] <i>obliczamy objętość tłuszczu wewnątrzmięśniowego</i>
  sumujemy wartości wszystkich wokseli w obrazie
  <b>fat-fraction * muscle-mask</b>; otrzymaną sumę mnożymy przez
  objętość jednego woksela
</pre>
