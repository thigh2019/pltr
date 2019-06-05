# .def[Automatyczne oznaczanie zawartości<br>tłuszczu wewnątrzmięśniowego<br>z obrazów Dixona]

### Agnieszka Sabisz<br>Włodzimierz Bzyl<br>Piotr Arłukowicz<br>Beata Brzeska<br>Arkadiusz Szarmach

## Gdańsk, 6–8 czerwca 2019

???

Dzień dobry!

Nie jestem Agnieszką Sabisz.<br>
Przedstawić się.

Wspólnie z Agnieszką, Piotrem, Arkadiuszem i Beatą tworzymy program
do oznaczania zawartości tłuszczu wewnątrzmięśniowego z obrazów Dixona.

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

**Naszym celem** jest napisanie programu obliczającego zawartość
tłuszczu z dokładnością **±1%**.
Z literatury wynika, że taka dokładność jest możliwa do osiągnięcia.

Taka dokładność powinna umożliwić monitorowanie **zmian**
tłuszczu w trakcie ćwiczeń lub w rozwoju choroby.

<!--
Niestety, aby osiągnąć taką precyzję, musimy kontrolować szum
(rozkład **Rayleigh**, **Ricci**; SNR) w obrazach MR.
-->

----

Nasz program składa się z dwóch modułów.

.def[Pierwszy moduł] został napisany **pod dane**: obrazy MR nóg.
Napisanie tego modułu wymagało **pomysłowości**, a także trochę **szczęścia**.
Udało się zaadaptować do naszych celów funkcje z pakietu
*Advanced Normalization Tools for R*.

.def[Moduł drugi] korzysta z **metody Dixona**.

----

Literatura:

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
–CH<sub>2</sub>– i –CH<sub>3</sub>, odpowiednio.

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

Z obrazów W i F można utworzyć obraz .def[signal fat-fraction],
licząc, dla każdego woksela osobno, udział sygnału pochodzącego
od „tłuszczu” do całkowitej intensywności sygnału.

Z przybliżenia w ramce wynika, że ułamek **signal fat-fraction**
przybliża prawdziwą proporcję tłuszczu w wokselu.

 **True fat-fraction** jest zdefiniowane w spectroskopii (spektrometrii)
 jako **proton density fat fraction** (PDFF).

Dokładność przybliżenia zależy od .def[α] flip angle.
Im mniejszy kąt (5-10 stopni), tym lepsze przybliżenie.

---

## .def[Dane:] obrazy Dixona, wybrane warstwy <span class="whitespace-left def">Program</span>

<img style="margin-left:-1em;" src=images/in_phase_slab.png height=500 alt="">
<img style="margin-left:4em;" src=images/fat_fraction_in_muscle_slab.png height=500 alt="">

???

### Dane / Program

Danymi do naszego programu są obrazy 3D:<br>
in-phase, water, fat i warstwy (plastry), w których
będziemy liczyć tłuszcz (ROI).

Każdy z obrazów 3D powstał z ok. 10 milionów wokseli (kostek),
a obraz nogi z 1–2 mln wokseli.

Po prawej stronie, pod napisem .def[Program],
widzimy obszar mięśni **w obrazie fat-fraction**.
W mięśniach tłuszczu jest mało i dlatego mało co widać.

---

## Tłuszcz na obrazach in-phase i fat-fraction

<img src=images/in_phase.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction.png width=350>

???

### Tłuszcz na obrazach in-phase i fat-fraction

Anglicy mówią, .def[a free lunch is only found in mousetraps].<br>
Po polsku mówimy, że w życiu nie ma nic za darmo.

Jak widać, tło obrazu fat-fraction, czyli powietrze plus stół,
zawiera **fałszywy** tłuszcz.
Powstaje on w wyniku dzielenia szumu przez szum. Dlatego już
na początku mamy więcej pracy, bo cały fałszywy tłuszcz
należy usunąć na tym etapie.

Przy okazji usuwamy też częściowo widoczną, przeciętą wzdłuż nogę.
Na kolejnych obrazach tło i nogę wygumkowano na .background[żółto].

---

## Tłuszcz na obrazach in-phase i fat-fraction bez tła

<img src=images/in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_without_background.png width=350>

???

### Tłuszcz na obrazach in-phase i fat-fraction bez tła

Do zamalowania wokseli w tle wykorzystano .solid[morphological processing].
Konkretnie operacje: wymazywania – .def[erode] i *dodawania* wokseli – .def[dilate].

Na przykład, skórę usunięto za pomocą erode.

---

## Mięśnie na obrazach in-phase i fat-fraction bez tła

<img src=images/muscle_in_in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_in_muscle_without_background.png width=350>

???

### Mięśnie na obrazach in-phase i fat-fraction bez tła

Teraz przyszedł czas na wycięcie interesującego nas obszaru z mięśni.
Wycinamy też **kość udową**, która zawiera tłuszcz.

---

## 1 warstwa z mięśniami z wyciętą kością udową – 8%

<img style="margin-left:-1.25em;" src=images/intramuscular_fat_2d.png width=800>

???

### 1 warstwa z mięśniami z wyciętą kością udową

Na koniec, w **interesującym nas obszarze**,
sumujemy objętości wokseli i udziały tłuszczu w każdym wokselu.

Na slajdzie interesujący nas obszar, to jedna warstwa,
lub wedlug mojej 3 letniej wnuczki .def[**stopa słonia**].
Wyniki są w podpisach pod obrazkami.

W literaturze do metody Dixona, napisano, że dla małego flip angle
(kilka stopni) i ilości tłuszczu do kilkunastu procent(?)
dokładność powinna wynosić **ok. 1%**.

Aktualnie, jesteśmy w trakcie przygotowywania fantomów,
których użyjemy do potwierdzenia tych rezulatów.

Nasz pierwszy, przygotowany na szybko fantom, który nazwaliśmy Torcikiem.<br>
**12 + Enter**

---

## .warn[Po więcej informacji]

### (policzone objętości dla kilkunastu pacjentów, dodatkowe slajdy)

### .warn[zapraszamy do repozytorium:]

### https://github.com/thigh2019/pltr

----

# Dziękuję za uwagę!

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

### Pierwszy fantom, „Torcik”

<img src=images/fantom_f1_3d.png width=600>

???

### Przygotowanie fantomu to bułka z masłem… (?)

Miał potwierdzić słuszność naszego podejścia do obliczania
objętości **tłuszczu w tłuszczu**.

W tym fantomie było 200 cm<sup>3</sup> tłuszczu
(gęsiego, odmierzonego strzykawką)
i podobna objętość mięśni (z uda jelenia).

Policzona przez program objętość tłuszczu w tłuszczu,
**panel C**, to 202.8 cm<sup>3</sup>.

Tak mały błąd, w liczniu objętości –
2.8<sup>3</sup>, czyli **1.4%** –
zrobił na nas duże wrażenie i dał motywację do dalszej pracy.

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
