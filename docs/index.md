# .def[Automatyczne oznaczanie zawartości<br>tłuszczu wewnątrzmięśniowego<br>z obrazów Dixona]

### Agnieszka Sabisz<br>Włodzimierz Bzyl<br>Piotr Arłukowicz<br>Beata Brzeska<br>Arkadiusz Szarmach

## Gdańsk, 6–8 czerwca 2019

---

## .def[Monitorowanie .solid[zmian] zawartości tłuszczu]

### Tłuszcz w mięśniach szkieletowych:

* więcej u osób starszych
* może być skutkiem choroby (Duchenne’a, sarkopenia)

### Celem jest program do:

* Automatycznej segmentacji mięśni uda (.part[moduł 1] [thigh])
* .important[Precyzyjnego oznaczania] zawartości
  tłuszczu wewnątrzmięśniowego (.part[moduł 2])

???

Bez precyzyjnej metody oznaczania tłuszczu nie będzie możliwe wykrywanie
zmian chorobowych na wczesnych etapach rozwoju choroby.

Obrazy MR zawierają dużo szumu. Dlatego obliczenia, wykorzystujące
intensywność sygnału w pikselach będą obarczone błędem.

Oczywiście, możemy usunąć szum z obrazu. Taki obraz, prawdobodobnie,
będzie dla lekarza zawierał więcej informacji. Niestety tak poprawiane
obrazy będą dla algorytmów zawierać mniej informacji. Na przykład
algorytm k-średnich nie będzie działał poprawnie na obrazie
po „histogram equalization”.

Nasz program wylicza zawartość tłuszczu w dwóch etapach.
Na każdym etapie błąd kontrolowany jest inaczej.

* Etap 1: liczenia masek (obrazy ROI) – jakość i odpowiedniość
  masek można ocenić oglądając ich obrazy lub odpowiednio wybierając ROI.

* Etap 2: wyliczania objętości (tłuszczu, mięśni) w maskach – dokładność
  w liczeniu objętości wynika z przyjętego modelu (obrazy Dixona).

---

## [Dlaczego obrazy Dixona?](http://mriquestions.com/uploads/3/4/5/7/34572113/dixon_method_1984_radiology.pdf)

### 4 obrazy: in-phase, out-phase, water (.def[W]), fat (.def[F]).

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

Skrót: PDFF – proton density fat fraction

true fat-fraction – spectroscopy
signal fat-fraction – magnetic resonance

A PDFF error of less than 1% is introduced when
the true fat fraction is between 0 and 20% and
an error of only 2–3% was observed for true fat fractions
between 30 and 50% at 1.5 and 3 Tesla field strengths.

Hu, Li et al.
[Quantification of Absolute Fat Mass by Magnetic Resonance Imaging: a Validation Study against Chemical Analysis](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3509746/)

Chia-Ying Liu et al.,
[Fat Quantification…](https://onlinelibrary.wiley.com/doi/pdf/10.1002/mrm.21301),
sekcja Small flip angle (.def[α]) approach, eq. [8]).

M. Bydder et al.,
[Effect of Flip Angle, (.def[α]), on Fat Quantification by Dixon Techniques](https://cds.ismrm.org/ismrm-2006/files/02300.pdf) eq. [1].

Chia-Ying Liu et al.,
[Myocardial Fat Quantification in Humans: Evaluation by TwoPoint Water-Fat Imaging and Localized Proton Spectroscopy](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3039693/pdf/nihms268873.pdf).

Scott B. Reeder, Houchun H. Hu et al.
[Proton Density Fat-Fraction: A Standardized MR-Based Biomarker of Tissue Fat Concentration](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4779595/pdf/nihms764556.pdf).

Wartości voxeli w obrazach **fat-fraction** przybliżają prawdziwą
wartość tłuszczu w woxelach.

Przybliżenie jest tym lepsze im mniejszy jest flip angle: 5-10 stopni.
Mniejszy .def[α] flip angle, to większa dokładność przybliżenia TFF.

---

## .def[Dane:] obrazy Dixona, wybrane warstwy <span class="whitespace-left def">Program</span>

<img style="margin-left:-1em;" src=images/in_phase_slab.png height=500 alt="">
<img style="margin-left:4em;" src=images/fat_fraction_in_muscle_slab.png height=500 alt="">

---

## Tłuszcz na obrazach in-phase i fat-fraction

<img src=images/in_phase.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction.png width=350>

???

Każdy z obrazów 3D powstał z ok. 10 milionów wokseli / kostek.

Z obrazu fat-fraction widzimy/możemy odczytać jaką część
woksela (kostki. prostopadłościanu) zajmuje tłuszcz.

Jaki udział w kostce/wokselu ma tłuszcz.

### There is no such thing as a free lunch.
### It is impossible to get something for nothing.

flip angle = 10°

Tło obrazu fat-fraction, czyli powietrze + stół,
zawiera dużo fałszywego/nieprawdziwego tłuszczu
(wynik dzielenia szumu przez szum).

Dlatego nasz program zaczyna działanie od usunięcia tła.
Na kolejnych obrazach tło zamaskujemy na .background[żółto].

---

## Tłuszcz na obrazach in-phase i fat-fraction bez tła

<img src=images/in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_without_background.png width=350>

???

1. Nigdzie nie używamy algorytmu k-średnich, c-średnich i podobnych.

1. .solid[Thresholding] (progowanie) to dzielenie wokseli na dwie grupy.
Do progowania używamy średniej intensywności sygnału obrazu (mniejsze albo większe od średniej),
wyniku porównania intensywności sygnału dwóch obrazów (False albo True), np.
`water ≥ fat`, `fat < water`.
Progowania używamy do tworzenia .def[masek], czyli obrazów,
w których woksele przyjmują dwie wartości 0 lub 1.

1. Wykorzystujemy .solid[morphological processing] do *wymazywania* wokseli, operacja .def[erode]
(zmiana wartości 1 → 0), *dodawania* wokseli, operacja .def[dilate] (0 → 1),
np. usuwamy skórę za pomocą erode.

---

## Mięśnie na obrazach in-phase i fat-fraction bez tła

<img src=images/muscle_in_in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_in_muscle_without_background.png width=350>

???

1. Oczekujemy dużego SNR – Signal to Noise ratio (nie mniej niż 5-7).
1. Liczenie fat-mask z obrazu fat zazwyczaj korzysta z algorytmu
   k-means lub c-means. Nie jest najlepszym pomysłem.

### k-means and outliers

1. When the squared error criterion is used, outliers can unduly inﬂuence
the clusters that are found. In particular, when outliers are present,
the resulting cluster centroids (prototypes) are typically not as
representative as they otherwise would be and thus, the SSE will be
higher. Because of this, it is often useful to discover outliers and
eliminate them beforehand.

---

## 1 warstwa z mięśniami z wyciętą kością udową

<img style="margin-left:-1.25em;" src=images/intramuscular_fat_2d.png width=800>

---

## .warn[Po więcej informacji]

### dodatkowe slajdy, policzone objętości dla kilkunastu pacjentów

### .warn[zapraszamy do przejrzenia repozytorium:]

### https://github.com/wbzyl/pltr2019

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

### Przygotowanie fantomu to bułka z masłem

Przybliżenie .def[True Fat Fraction] policzone dla α = 10°

Fantom **F1** w całej okazałości na obrazku **A**.

Wartości fat-fraction to tylko przybliżenia
„prawdziwej” wartości.

Nasz pierwszy fantom miał zweryfikować na ile dobre jest to przybliżenie,
dla wartości filp-angle, α = 10°.

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

## abs(A - B) ≈ 0

<img src=images/zero.png width=350>

```
A = sqrt(in_phase^2 + out_phase^2)
B = sqrt(2) * sqrt(water^2 + fat^2)
```

---

## abs(C - 2 * water) ≈ 0

<img src=images/zero2.png width=350>

```
A = (in_phase + out_phase) * (water >= fat)
B = (in_phase - out_phase) * (water < fat)
C = A + B
```

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
