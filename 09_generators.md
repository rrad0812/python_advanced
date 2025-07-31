
# Pajton generatori

**Rezime**: u ovom tutorijalu ćete naučiti o generatorima u Pajtonu i kako da koristite generatore za kreiranje iteratora

## Uvod u generatore

Tipično, Pajton izvršava regularnu funkciju od vrha do dna na osnovu modela „od izvršavanja do završetka“.

To znači da Pajton ne može pauzirati regularnu funkciju na pola, a zatim nastaviti funkciju nakon toga. Na primer:

```py
def greeting():
    print('Hi!')
    print('How are you?')
    print('Are you there?')
```

Kada Pajton izvršava greeting()funkciju, on izvršava kod red po red od vrha do dna.

Takođe, Pajton ne može pauzirati funkciju u sledećem redu:

```py
print('How are you?')
```

... i prelazi na drugi kod i nastavlja izvršavanje od te linije.

Da biste pauzirali funkciju na pola i nastavili od mesta gde je funkcija pauzirana, koristite yieldnaredbu.

Kada funkcija sadrži barem jednu yieldizjavu, to je generatorska funkcija .

Po definiciji, generator je funkcija koja sadrži barem jednu naredbu yield.

Kada pozovete funkciju generatora, ona vraća novi objekat generatora. Međutim, ne pokreće funkciju.

Generatorski objekti (ili generatori) implementiraju protokol iteratora . U stvari, generatori su lenji iteratori. Stoga, da biste izvršili funkciju generatora, pozivate next()ugrađenu funkciju na njoj.
Jednostavan primer generatora u Pajtonu

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

Pošto greeting()funkcija sadrži yieldiskaze, ona je generatorska funkcija.

Izjava yieldje kao returnizjava u funkciji. Međutim, postoji velika razlika.

Kada Pajton naiđe na yieldizraz, vraća vrednost navedenu u yield. Pored toga, pauzira izvršavanje funkcije.

Ako ponovo „pozovete“ istu funkciju, Pajton će nastaviti od mesta gde yieldse naišlo na prethodnu izjavu.

Kada pozovete funkciju generatora, ona vraća objekat generatora. Na primer:

```py
messenger = greeting()
```

je messengerobjekat generatora, koji je takođe iterator .

Da biste zapravo izvršili telo funkcije greeting(), potrebno je da koristite next()ugrađenu funkciju:

```py
result = next(messenger)
print(result)
```

Kada se greeting()funkcija izvrši, ona prikazuje prvu poruku i vraća 1:

```shell
Hi!
1
```

Takođe, pauzira se odmah kod prve yieldizjave. Ako ponovo „pozovete“ greeting()funkciju, ona će nastaviti izvršavanje od poslednje yieldizjave:

result = next(messenger)
print(result)
Kodni jezik:  Pajton  ( python )

Izlaz:

How are you?
2
Kodni jezik:  Pajton  ( python )

I možete ga ponovo pozvati:

result = next(messenger)
print(result) 
Kodni jezik:  Pajton  ( python )

Izlaz:

Are you there?
3
Kodni jezik:  Pajton  ( python )

Međutim, ako ponovo izvršite generator, izazvaće izuzetak StopIteration jer je u pitanju iterator :

next(messenger)
Kodni jezik:  Pajton  ( python )

Greška:

StopIteration      
Kodni jezik:  Pajton  ( python )

Evo celog programa:

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
Jezik koda:  PHP  ( php )

Izlaz:

Hi!
1
How are you?
2
Are you there?
3
3

Korišćenje Pajton generatora za kreiranje iteratora

Sledeći primer definiše iterator koji vraća kvadratni broj celog broja.

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
Kodni jezik:  Pajton  ( python )

I možete koristiti Squaresiterator da generišete kvadratne brojeve prvih 5 celih brojeva od 0 do 5:

length = 5
square = Squares(length)
for s in square:
    print(s)
Kodni jezik:  Pajton  ( python )

Ovaj kod radi kako smo očekivali. Ali ima jedan problem, a to je da ima mnogo šablonskog načina rada.

I kao što možete pretpostaviti, koristite funkciju generatora da biste izgradili taj iterator.

Sledeće prepisuje Squaresiterator kao funkciju generatora:

def squares(length):
    for n in range(length):
        yield n ** 2
Kodni jezik:  Pajton  ( python )

Novi kod je mnogo kraći i izražajniji. Upotreba squaresfunkcije generatora je slična gore navedenom iteratoru:

length = 5
square = squares(length)
for s in square:
    print(s)
Kodni jezik:  Pajton  ( python )

Rezime

    Pajton generatori su funkcije koje sadrže barem jednu naredbu yield.
    Funkcija generatora vraća objekat generatora.
    Objekat generatora je iterator. Stoga se iscrpljuje kada nema preostalog elementa za vraćanje.


Izrazi generatora Pajtona

Rezime : u ovom tutorijalu ćete naučiti o izrazu generatora u Pajtonu za kreiranje objekta generatora.
Uvod u izraze generatora

Generatorski izraz je izraz koji vraća objekat generatora .

U osnovi, generatorska funkcija je funkcija koja sadrži yieldiskaz i vraća objekat generatora.

Na primer, sledeće definiše funkciju generatora:

def squares(length):
    for n in range(length):
        yield n ** 2
Jezik koda:  Javaskript  ( javaskript )

Funkcija squaresgeneratora vraća objekat generatora koji proizvodi kvadratne brojeve celih brojeva od 0do length - 1.

Pošto je objekat generatora iterator , možete koristiti petlju for da biste iterirali kroz njegove elemente:

def squares(length):
    for n in range(length):
        yield n ** 2

for square in squares(5):
    print(square)
Jezik koda:  PHP  ( php )

Probaj

Izlaz:

0
1
4
9
16

Generatorski izraz vam pruža jednostavniji način za vraćanje objekta generatora.

Sledeći primer definiše izraz generatora koji vraća kvadratne brojeve celih brojeva od 0 do 4:

squares = (n** 2 for n in range(5))

Pošto squaresje objekat generatora, možete iterirati kroz njegove elemente ovako:

for square in squares:
    print(square)
Jezik koda:  PHP  ( php )

Kao što vidite, umesto korišćenja funkcije za definisanje funkcije generatora, možete koristiti izraz generatora.

Generatorski izraz je sličan listi razumevanja u smislu sintakse. Na primer, generatorski izraz takođe podržava složene sintakse, uključujući:

    ako naredbe
    Više ugnježđenih petlji
    Ugnežđena razumevanja

Međutim, generatorski izraz koristi zagrade ()umesto uglastih zagrada [].
Generatorski izrazi u odnosu na liste

Sledeće je prikazano kako se koristi list comprehend za generisanje kvadratnih brojeva od 0 do 4:

square_list = [n** 2 for n in range(5)]

I ovo definiše generator kvadratnih brojeva:

square_generator = (n** 2 for n in range(5))

Sintaksa

Što se tiče sintakse, izraz generatora koristi zagrade, ()dok razumevanje liste koristi uglaste zagrade [].
Iskorišćenost memorije

Razumevanje liste vraća listu, dok izraz generatora vraća objekat generatora.

To znači da razumevanje liste vraća kompletnu listu elemenata unapred. Međutim, generatorski izraz vraća listu elemenata, jedan po jedan, na osnovu zahteva.

Razumevanje liste je brzo, dok je izraz generatora lenj.

Drugim rečima, razumevanje liste kreira sve elemente odmah i učitava ih sve u memoriju.

Suprotno tome, generatorski izraz kreira jedan element na osnovu zahteva. On učitava samo jedan element u memoriju.
naspram iteratora

Razumevanje liste vraća iterabilnu vrednost . To znači da možete iterirati preko rezultata razumevanja liste iznova i iznova.

Međutim, izraz generatora vraća iterator , tačnije lenji iterator. Postaje iscrpljujuće kada završite sa iteracijom preko njega.
Rezime

    Koristite izraz generatora u Pajtonu da biste vratili generator.
