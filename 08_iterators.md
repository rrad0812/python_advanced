
# Pajton iteratori

**Rezime**: u ovom tutorijalu ćete naučiti o iteratoru u Pajtonu i kako da definišete prilagođeni iterator koristeći protokol iteratora.

## Šta je iterator?

Iterator je objekat koji implementira:

- `__iter__` metoda koja vraća sam objekat.
- `__next__` metoda koja vraća sledeću stavku. Ako su sve stavke vraćene, metoda izbacuje `StopIteration` izuzetak.

Imajte na umu da su ove dve metode poznate i kao `Iterator protokol`.

Pajton vam omogućava da koristite iteratore u `for` petljama, `razumevanju` i drugim ugrađenim funkcijama, uključujući `map`, `filter`, `reduce` i `zip`.

### Primer iteratora u Pajtonu

Kvadrat broja je proizvod celog broja sa samim sobom. Na primer, kvadrat broja 2 je 4 ( = 2 * 2 ).

Sledeći primer definiše *Square* `iterator` klasu koja vraća kvadratne brojeve.

```py
class Square:
    def __init__(self, length):
        self.length = length
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current >= self.length:
            raise StopIteration

        self.current += 1
        return self.current ** 2
```

Kako to funkcioniše.

- Prvo, inicijalizujte atribute `length` i `current` u `__init__` metodi. Atribut `length` određuje broj kvadriranih brojeva koje klasa treba da vrati. A `current` atribut prati trenutni ceo broj.

- Drugo, implementirajte `__iter__` metodu koja vraća `self` objekat.

- Treće, implementirajte `__next__` metodu koja vraća sledeći kvadrirati broj. Ako je broj vraćenih kvadratnih brojeva veći od dužine, metoda izbacuje izuzetak `StopIteration`.

### Korišćenje objekta iteratora

Sledeće je prikazano kako se koristi `iterator` *Square* u *for* petlji:

```py
square = Square(5)

for sq in square:
    print(sq)
```

Izlaz:

```shell
1
4
9
16
25
```

Kako ovo funkcioniše:

- Prvo, kreirajte novu instancu klase *Square*.
- Zatim, koristite `for` petlju za iteraciju kroz elemente *Square* iteratora.

Kada prođete kroz sve stavke, iterator je iscrpljen. To znači da morate da kreirate novi iterator da biste ponovo iterirali kroz njegove stavke.

Ako pokušate da koristite iterator koji je već iscrpljen, dobićete izuzetak `StopIteration`. Na primer:

```py
next(square)
```

```shell
Error:
StopIteration
```

Takođe, iterator se ne može ponovo pokrenuti jer ima samo `__next__` metodu koja vraća sledeću stavku iz kolekcije.

### Rezime iterator

- Iterator je objekat koji implementira `__iter__` i `__next__` metode.
- Iterator se ne može ponovo koristiti nakon što su svi elementi vraćeni.

## Iterator naspram iterabilnog objekta

**Rezime**: u ovom tutorijalu ćete saznati o iteratoru i iterabilnim objekatima u Pajtonu i njihovim razlikama.

### Iteratori

Iterator je objekat koji implementira `iterator` protokol. Drugim rečima, iterator je objekat koji implementira sledeće metode:

- `__iter__` vraća sam objekat iteratora.
- `__next__` vraća sledeći element.

Kada završite iteraciju kolekcije pomoću iteratora, iterator je iscrpljen. To znači da više ne možete koristiti objekat iteratora.

### Iterabilni objekti

Iterabilni objekat je objekat preko kojeg možete iterirati.

Objekat je iterabilan kada implementira `__iter__` metodu. A njegova `__iter__` metoda vraća novi iterator.

### Ugrađene liste i iteratori

U Pajtonu, lista je uređena kolekcija elemenata. Takođe je iterabilna jer objekat liste ima `__iter__` metodu koja vraća iterator. Na primer:

```py
numbers = [1, 2, 3]

number_iterator = numbers.__iter__()
print(type(number_iterator))
```

Izlaz:

```shell
<class 'list_iterator'>
```

U ovom primeru, `__iter__` metod vraća iterator tipa `list_iterator`.

Pošto `list_iterator` implementira `__iter__` metodu, možete koristiti `iter` ugrađenu funkciju da biste dobili objekat iteratora:

```py
numbers = [1, 2, 3]
number_iterator = iter(numbers)
```

Pošto `list_iterator` takođe implementira `__next__` metod, možete koristiti ugrađenu funkciju `next` za iteraciju kroz listu:

```py
numbers = [1, 2, 3]

number_iterator = iter(numbers)

next(number_iterator)
next(number_iterator)
next(number_iterator)
```

Ako ponovo pozovete `next` funkciju, dobićete `StopIteration` izuzetak.

```py
next(number_iterator)
```

```shell
Error:
StopIteration
```

To je zato što je `list_iterator` iscrpljen. Da biste ponovo iterirali listu, potrebno je da kreirate novi iterator.

Ovo ilustruje odvajanje liste od njenog iteratora. Lista se kreira jednom, dok se iterator kreira svaki put kada je potrebno da se iterira kroz listu.

### Pajton iterator i iterabilni objekat

Sledeće definiše *Colors* klasu:

```py
class Colors:
    def __init__(self):
        self.rgb = ['red', 'green', 'blue']
        self.__index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.__index &gt;= len(self.rgb):
            raise StopIteration

        # return the next color
        color = self.rgb[self.__index]
        self.__index += 1
        return color
```

U ovom primeru, *Colors* klasa igra dve uloge:

- iterabilnu i
- iteratorsku.

Klasa `Colors` je iterator jer implementira `__iter__` i `__next__` metod. `__iter__` metod vraća sam objekat. `__next__` metod vraća sledeću stavku sa liste.

Klasa *Colors* je takođe iterabilna jer implementira `__iter__` metodu koja vraća sam objekat, a to je iterator.

Sledeći kod kreira novu instancu klase *Colors* i iterira kroz njene elemente koristeći `for` petlju:

```py
colors = Colors()

for color in colors:
    print(color)
```

Kada završite sa iteracijom, *colors* objekat postaje beskoristan. Ako pokušate ponovo da je iterirate, dobićete `StopIteration` izuzetak:

```py
next(colors)
```

```shell
Error:
StopIteration
```

Ako koristite `for` petlju, nećete dobiti ništa nazad. `Iterator` je prazan:

```py
for color in colors:
    print(color)
```

Da biste ponovo ponovili iteraciju, potrebno je da kreirate novi *colors* objekat sa *rgb* atributom. Ovo je neefikasno.

### Odvajanje iteratora od iterabilnog objekta

Hajde da odvojimo `iterator` *colors* od njegovog iterabilnog objekta kao što Pajton radi sa iteratorom liste i listom.

Sledeće definiše *Colors* klasu:

```py
class Colors:
    def __init__(self):
        self.rgb = ['red', 'green', 'blue']

    def __len__(self):
        return len(self.rgb)
```

Sledeće definiše *ColorIterator* klasu:

```py
class ColorIterator:
    def __init__(self, colors):
        self.__colors = colors
        self.__index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.__index >= len(self.__colors):
            raise StopIteration

        # return the next color
        color = self.__colors.rgb[self.__index]
        self.__index += 1
        return color
```

Kako to funkcioniše.

- Metoda `__init__` prihvata iterabilni tip koja je instanca klase *Colors*.
- Metoda `__iter__` vraća sam iterator.
- Metoda `__next__` vraća sledeći element iz *Colors* objekta.

Sledeće je prikazano kako se koristi *ColorIterator* za iteraciju kroz *Colors* objekat:

```py
colors = Colors()
color_iterator = ColorIterator(colors)

for color in color_iterator:
    print(color)
```

Da biste ponovo iterirali *Colors* objekat, samo treba da kreirate novu instancu klase *ColorIterator*.

Postoji jedan problem!

Kada želite da iterirate *Colors* objekat, potrebno je da ručno kreirate novi *ColorIterator* objekat. Takođe, potrebno je da zapamtite ime iteratora *ColorIterator*.

Bilo bi odlično kada biste ovo mogli automatizovati. Da biste to uradili, možete učiniti *Colors* klasu iterabilnom implementacijom `__iter__` metode:

```py
class Colors:
    def __init__(self):
        self.rgb = ['red', 'green', 'blue']

    def __len__(self):
        return len(self.rgb)

    def __iter__(self):
        return ColorIterator(self)
```

Metoda `__iter__` vraća novu instancu klase *ColorIterator*.

Sada možete iterirati *Colors* objekat bez eksplicitnog kreiranja *ColorIteratorobjekta*:

```py
colors = Colors()

for color in colors:
    print(color)
```

Interno, `for` petlja poziva `__iter__` metod objekta *colors* da bi dobila iterator i koristi ga za iteraciju kroz elemente objekta *colors*.

Sledeće postavlja *ColorIterator* klasu unutar *Colors* klase da bi ih kapsuliralo u jednu klasu:

```py
class Colors:
    def __init__(self):
        self.rgb = ['red', 'green', 'blue']

    def __len__(self):
        return len(self.rgb)

    def __iter__(self):
        return self.ColorIterator(self)

    class ColorIterator:
        def __init__(self, colors):
            self.__colors = colors
            self.__index = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self.__index >= len(self.__colors):
                raise StopIteration

            # return the next color
            color = self.__colors.rgb[self.__index]
            self.__index += 1
            return color
```

Rezime

- Iterabilni objekat je objekat koji implementira `__iter__` metodu koja vraća iterator.
- Iterator je objekat koji implementira `__iter__` metodu koja vraća samog sebe i `next` metodu koja vraća sledeći element.
- Iteratori su takođe iterabilne jedinice.

## iter

**Rezime**: u ovom tutorijalu ćete naučiti kako da efikasno koristite ugrađene funkcije Pajtona.

### Uvod u funkciju iter

Funkcija `iter()` vraća iterator datog objekta:

```py
iter(object)
```

Funkcija `iter()` zahteva argument koji može biti iterabilni tip ili sekvenca. Generalno, argument *object* može biti bilo koji objekat koji podržava protokol `iterator` ili sekvence.

Kada pozovete `iter()` funkciju na objektu, funkcija prvo traži `__iter__()` metod tog objekta.

Ako `__iter__()` metod postoji, `iter()` funkcija ga poziva da bi dobila iterator. U suprotnom, `iter()` funkcija će tražiti `__getitem__()` metod.

Ako je `__getitem__()` dostupan, `iter()` funkcija kreira objekat iteratora i vraća taj objekat. U suprotnom, izaziva `TypeError` izuzetak.

### Primeri funkcije iter()

Sledeći primer definiše jednostavnu *Counter* klasu i koristi `iter()` funkciju za dobijanje iteratora objekta *Counter*:

```py
class Counter:
    def __init__(self):
        self.__current = 0

counter = Counter()
iterator = iter(counter)
```

Ovo bi izazvalo `TypeError` jer counterobjekat nije iterabilan.

```py
TypeError: 'Counter' object is not iterable
```

Sledeće dodaje `__getitem__()` metodu `Counter` klasi:

```py
class Counter:
    def __init__(self):
        self.current = 0

    def __getitem__(self, index):
        if isinstance(index, int):
            self.current += 1
            return self.current
```

Pošto `Counter` implementira `__getitem__()` metodu koja vraća element na osnovu indeksa, to je sekvenca.

Sada možete koristiti `iter()` funkciju da biste dobili iterator *Counter*:

```py
counter = Counter()

iterator = iter(counter)
print(type(iterator))
```

Izlaz:

```shell
<class 'iterator'>
```

U ovom slučaju, Pajton kreira objekat iteratora i vraća ga. Stoga, možete koristiti objekat iteratora za iteraciju counter:

```py
for _ in range(1, 4):
    print(next(iterator))
```

Sledeće dodaje *CounterIterator* klasu klasi *Counter* i implementira iterabilni protokol:

```py
class Counter:
    def __init__(self):
        self.current = 0

    def __getitem__(self, index):
        if isinstance(index, int):
            self.current += 1
            return self.current

    def __iter__(self):
        return self.CounterIterator(self)

    class CounterIterator:
        def __init__(self, counter):
            self.__counter = counter

        def __iter__(self):
            return self

        def __next__(self):
            self.__counter.current += 1
            return self.__counter.current
```

Kako to funkcioniše.

- Klasa Counter implementira `__iter__()` metodu koja vraća `iterator`. `Iterator` za povratak je nova instanca klase *CounterIterator*.
- Klasa *CounterIterator* podržava protokol iteratora implementacijom metoda `__iter__()` i `__next__()`.

Kada postoje obe metode `__iter__()` i `__getitem__()` funkcija uvek koristi metod: `iter()`.

```py
counter = Counter()

iterator = iter(counter)
print(type(iterator))
```

Izlaz:

```shell
<class '__main__.Counter.CounterIterator'>
1
2
3
```

U ovom primeru, `iter()` funkcija poziva `__iter__()` metod umesto `__getitem__()` metode. Zato vidite *CounterIterator* u izlazu.

### Drugi oblik funkcije iter()

Sledeća slika prikazuje drugi oblik funkcije `iter()`:

```py
iter(callable, sentinel)
```

Pozvaće iter(callable,sentinel) se pozivajuća metoda kada next()se pozove.

Vratiće vrednost koju je vratila pozivna funkcija ili će izazvati izuzetak StopIteration ako je rezultat jednak vrednosti sentinel-a.

Uzmimo primer da bismo razumeli kako to iter(callable, sentinel)funkcioniše.

- Prvo, definišite funkciju koja vraća zatvaranje:

    ```py
    def counter():
        count = 0

        def increase():
            nonlocal count
            count += 1
            return count

        return increase
    ```

    Funkcija `counter()` vraća zatvaranje. A zatvaranje vraća novi ceo broj počevši od jedan kada se pozove.

- Drugo, koristite *counter()* funkciju da biste prikazali brojeve od 1 do 3:

    ```py
    cnt = counter()

    while True:
        current = cnt()
        print(current)
        if current == 3:
            break
    ```

    Izlaz:

    ```shell
    1
    2
    3
    ```

    Da biste ga učinili generičkijim, možete umesto toga koristiti iterator.

- Treće, definišite novi iterator *Counter*:

    ```py
    class CounterIterator:
        def __init__(self, fn, sentinel):
            self.fn = fn
            self.sentinel = sentinel

        def __iter__(self):
            return self

        def __next__(self):
            current = self.fn()
            if current == self.sentinel:
                raise StopIteration

            return current
    ```

Konstruktor *CounterIterator*-a prihvata pozivajući tip *fn* i *sentinel*.

Metod `__next__()` vraća vrednost koju je vratila pozivna metoda ( *fn* ) ili podiže `StopIteration` izuzetak ako je vraćena vrednost jednaka *sentinelu*.

Sledeće je prikazano kako se koristi *CounterIterator*:

```py
cnt = counter()
iterator = CounterIterator(cnt, 4)
for count in iterator:
    print(count)
```

Izlaz:

```shell
1
2
3
```

Umesto definisanja novog iteratora svaki put kada želite da iterirate vrednosti koje vraća pozivna funkcija, možete koristiti `iter(callable, sentinel)` funkciju:

```py
cnt = counter()
iterator = iter(cnt, 4)

for count in iterator:
    print(count)
```

Izlaz:

```shell
1
2
3
```

### Provera da li je objekat iterabilan

Koristite funkciju `iter()` da biste proverili da li je objekat iterabilan.

Da biste utvrdili da li je objekat iterabilan, možete proveriti da li implementira `__iter__()` ili `__getitem__()` metod.

Međutim, možete koristiti iter() funkciju da biste testirali da li je objekat iterabilan na sledeći način:

```py
def is_iterable(object):
    try:
        iter(object)
    except TypeError:
        return False
    else:
        return True
```

Ako objekat ne implementira ni `__iter__()` metod ni `__getitem__()` metod, `iter()` funkcija podiže `TypeError` izuzetak.

Sledeće je prikazano kako se koristi `is_iterable()` funkcija:

```py
print(is_iterable([1, 2, 3]))
print(is_iterable('Python iter'))
print(is_iterable(100))
```

Izlaz:

```shell
True
True
False
```

Rezime iter funkcije

Koristite `iter()` funkciju da biste dobili iterator objekta.
