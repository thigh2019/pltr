# .def[Automatyczne oznaczanie zawartości<br>tłuszczu wewnątrzmięśniowego<br>z obrazów Dixona]

### Agnieszka Sabisz<br>Włodzimierz Bzyl<br>Piotr Arłukowicz<br>Beata Brzeska<br>Arkadiusz Szarmach

## Gdańsk, 6–8 czerwca 2019

---

## .def[Monitorowanie .solid[zmian] zawartości tłuszczu]

### Tłuszcz w mięśniach szkieletowych:

* więcej u osób starszych
* może być skutkiem choroby (Duchenne’a, sarkopenia)

### Celem jest program do:

* automatyczne oddzielenie mięśni uda z obrazu MR<br>
  (.part[moduł 1] [thigh])
* .important[precyzyjnego oznaczania] zawartości
  tłuszczu wewnątrzmięśniowego (.part[moduł 2])

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

---

## .def[Dane:] obrazy Dixona, wybrane warstwy <span class="whitespace-left def">Program</span>

<img style="margin-left:-1em;" src=images/in_phase_slab.png height=500 alt="">
<img style="margin-left:4em;" src=images/fat_fraction_in_muscle_slab.png height=500 alt="">

---

## Tłuszcz na obrazach in-phase i fat-fraction

<img src=images/in_phase.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction.png width=350>

---

## Tłuszcz na obrazach in-phase i fat-fraction bez tła

<img src=images/in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_without_background.png width=350>

---

## Mięśnie na obrazach in-phase i fat-fraction bez tła

<img src=images/muscle_in_in_phase_without_background.png width=350>
<img style="margin-left:1em;" src=images/fat_fraction_in_muscle_without_background.png width=350>

---

## 1 warstwa z mięśniami z wyciętą kością udową

<img style="margin-left:-1.25em;" src=images/intramuscular_fat_2d.png width=800>

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
