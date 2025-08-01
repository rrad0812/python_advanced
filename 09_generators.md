
# Generatori

## Funkcije generatora

**Rezime**: u ovom tutorijalu ćete naučiti o generatorima u Pajtonu i kako da koristite generatore za kreiranje iteratora

### Uvod u generatore

Tipično, Pajton izvršava regularnu funkciju od vrha do dna na osnovu modela "izvršavanje od početka do završetka".

To znači da Pajton ne može pauzirati regularnu funkciju na pola, a zatim nastaviti funkciju nakon toga. Na primer:

```py
def greeting():
    print('Hi!')
    print('How are you?')
    print('Are you there?')
```

Kada Pajton izvršava *greeting()* funkciju, on izvršava kod red po red od vrha do dna.

Takođe, Pajton ne može pauzirati funkciju u nekom redu:

```py
print('How are you?')
```

... već izvršava i prelazi na drugi red koda i nastavlja izvršavanje dalje.

Da biste pauzirali funkciju na pola i nastavili od mesta gde je funkcija pauzirana, koristite `yield` naredbu.

Kada funkcija sadrži barem jednu `yield` naredbu, to je funkcija `generatora`.

Po definiciji, `generator` je funkcija koja sadrži barem jednu naredbu `yield`.

Kada pozovete `funkciju generatora`, ona vraća novi `objekat generatora`. Međutim, ne pokreće funkciju.

Generatorski objekti (ili generatori) implementiraju protokol `iterator`. U stvari, generatori su `lenji iteratori`. Stoga, da biste izvršili funkciju generatora, pozivate `next()` ugrađenu funkciju na objektu generatora.

### Jednostavan primer generatora u Pajtonu

Pogledajte sledeći primer:

```py
def greeting():
    print('Hi!')
    yield 1
    print('How are you?')
    yield 2
    print('Are you there?')
    yield 3
```

Pošto *greeting()* funkcija sadrži `yield` naredbu, ona je `funkcija generatora`.

Naredba `yield` je kao `return` naredba u regularnoj funkciji. Međutim, postoji velika razlika. Kada Pajton naiđe na `yield` naredbu, vraća vrednost navedenu u `yield` naredbi. Pored toga, pauzira izvršavanje funkcije.

Ako ponovo "pozovete" istu funkciju, Pajton će nastaviti od mesta `yield` naredbe.

Kada pozovete `funkciju generatora`, ona vraća `objekat generatora`. Na primer:

```py
messenger = greeting()
```

*messenger* je objekat `generator` koji je takođe i `iterator`.

Da biste izvršili telo funkcije *greeting()*, potrebno je da koristite `next()` ugrađenu funkciju:

```py
result = next(messenger)
print(result)
```

Kada se *greeting()* funkcija izvrši, ona prikazuje prvu poruku Hi! i vraća 1:

```shell
Hi!
1
```

Takođe, pauzira se odmah kod prve `yield` izjave. Ako ponovo "pozovete" *greeting()* funkciju, ona će nastaviti izvršavanje od poslednje `yield` izjave:

```py
result = next(messenger)
print(result)
```

Izlaz:

```shell
How are you?
2
```

I možete ga ponovo pozvati:

```py
result = next(messenger)
print(result) 
```

Izlaz:

```shell
Are you there?
3
```

Međutim, ako ponovo izvršite generator, izazvaće izuzetak `StopIteration` jer je u pitanju iterator:

```py
next(messenger)
```

```shell
Error:
StopIteration      
```

Evo celog programa:

```py
def greeting():
    print('Hi!')
    yield 1
    print('How are you?')
    yield 2
    print('Are you there?')
    yield 3

messenger = greeting()

result = next(messenger)
print(result)

result = next(messenger)
print(result)

result = next(messenger)
print(result)

print(result)
```

Izlaz:

```shell
Hi!
1
How are you?
2
Are you there?
3
3
```

### Korišćenje generatora za kreiranje iteratora

Sledeći primer definiše iterator koji vraća kvadrat celog broja.

```py
class Squares:
    def __init__(self, length):
        self.length = length
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        result = self.current ** 2

        self.current += 1

        if self.current > self.length:
            raise StopIteration

        return result
```

Možete koristiti *Squares* iterator da generišete kvadratne brojeve prvih 5 celih brojeva od 0 do 5:

```py
length = 5
square = Squares(length)
for s in square:
    print(s)
```

Ovaj kod radi kako smo očekivali. Ali ima jedan problem, a to je da ima mnogo šablonskog načina rada.

I kao što možete pretpostaviti, koristite funkciju generatora da biste izgradili taj iterator.

Sledeće prepisuje *Squares* iterator kao `funkciju generatora`:

```py
def squares(length):
    for n in range(length):
        yield n ** 2
```

Novi kod je mnogo kraći i izražajniji. Upotreba `squares funkcije generatora` je slična gore navedenom iteratoru:

```py
length = 5
square = squares(length)
for s in square:
    print(s)
```

### Rezime generatora

- Pajton `generatori` su funkcije koje sadrže barem jednu naredbu `yield`.
- `Funkcija generatora` vraća `objekat generatora`.
- `Objekat generatora` je `iterator`. Stoga se iscrpljuje kada nema elemenata za vraćanje.

## Izrazi generatora

**Rezime**: u ovom tutorijalu ćete naučiti o izrazu generatora u Pajtonu za kreiranje objekta generatora.

### Uvod u izraze generatora

`Izraz generatora` je izraz koji vraća `objekat generatora`.

U osnovi, `funkcija generatora` je funkcija koja sadrži `yield` naredbu i vraća `objekat generatora`.

Na primer, sledeće definiše funkciju generatora:

```py
def squares(length):
    for n in range(length):
        yield n ** 2
```

`Funkcija generatora` *squares* vraća `objekat generatora` koji proizvodi kvadrate celih brojeva od 0 do ( length - 1 ).

Pošto je `objekat generatora iterator`, možete koristiti petlju `for` da biste iterirali kroz njegove elemente:

```py
def squares(length):
    for n in range(length):
        yield n ** 2

for square in squares(5):
    print(square)
```

Izlaz:

```shell
0
1
4
9
16
```

`Izraz generatora` vam pruža jednostavniji način za vraćanje `objekta generatora`.

Sledeći primer definiše izraz generatora koji vraća kvadrate celih brojeva od 0 do 4:

```py
squares = (n ** 2 for n in range(5))
```

Pošto je *squares* `objekat generatora`, možete iterirati kroz njegove elemente ovako:

```py
for square in squares:
    print(square)
```

Kao što vidite, umesto korišćenja funkcije za definisanje `funkcije generatora`, možete koristiti `izraz generatora`.

`Izraz generatora` je sličan `listi razumevanja` u smislu sintakse. Na primer, `izraz generatora` takođe podržava složene sintakse, uključujući:

- `if` naredbe
- Više ugnježđenih petlji
- Ugnežđena razumevanja

Međutim, izraz generatora koristi male () umesto uglastih zagrada [].

### Izrazi generatora u odnosu na liste razumevanja

Sledeće je prikazano kako se koristi `lista razumevanja` za generisanje kvadrata brojeva od 0 do 4:

```py
square_list = [n ** 2 for n in range(5)]
```

I ovo definiše generator kvadrata brojeva:

```py
square_generator = (n ** 2 for n in range(5))
```

**Sintaksa**
Što se tiče sintakse, izraz generatora koristi zagrade () dok razumevanje liste koristi uglaste zagrade [].

**Iskorišćenost memorije**
Razumevanje liste vraća listu, dok izraz generatora vraća objekat generatora. To znači da razumevanje liste vraća kompletnu listu elemenata unapred. Međutim, izraz generatora vraća listu elemenata, jedan po jedan, na osnovu zahteva.

Razumevanje liste je brzo, dok je izraz generatora lenj. Drugim rečima, razumevanje liste kreira sve elemente odmah i učitava ih sve u memoriju. Suprotno tome, izraz generatora kreira jedan element na osnovu zahteva. On učitava samo jedan element u memoriju.

**Izraz generatora naspram iteratora**
Razumevanje liste vraća iterabilnu vrednost. To znači da možete iterirati preko rezultata razumevanja liste iznova i iznova. Međutim, izraz generatora vraća iterator, tačnije lenji iterator koji postaje iscrpljen kada završite sa iteracijom preko njega.

### Rezime

Koristite izraz generatora u da biste vratili generator.
