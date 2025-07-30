
# Funkcionalno programiranje u Pajtonu

## Kako transformisati elemente liste

**Rezime**: u ovom tutorijalu ćete naučiti kako da koristite Pajton `map()` funkcije sa listama.

### Uvod u funkciju map()

Kada radite sa listom ( ili tuple ), često je potrebno da transformišete elemente liste i vratite novu listu koja sadrži transformisane elemente.

Pretpostavimo da želite da udvostručite svaki broj u sledećoj *bonuses* listi:

```py
bonuses = [100, 200, 300]
```

Da biste to uradili, možete koristiti `for` petlju za iteraciju kroz elemente, udvostručiti svaki od njih i dodati ga na novu listu kao što je ova:

```py
bonuses = [100, 200, 300]

new_bonuses = []

for bonus in bonuses:
    new_bonuses.append(bonus * 2)

print(new_bonuses)
```

Izlaz:
```py
[200, 400, 600]
```

Pajton pruža bolji način za obavljanje ove vrste zadatka korišćenjem `map()` ugrađene funkcije.

Funkcija `map()` iterira kroz sve elemente u listi (ili torki), primenjuje funkciju na svaki i vraća iterator novih elemenata.

Sledeća kod prikazuje osnovnu sintaksu funkcije `map()`:

```py
iterator = map(fn, list)
```

U ovoj sintaksi, `fn` je naziv funkcije koja će se pozivati za svaki element liste.

U stvari, funkciji map() možete proslediti bilo koju iterabilnu stavku, ne samo listu ili torku.

Vraćajući se na prethodni primer, da biste koristili `map()` funkciju, prvo definišete funkciju koja udvostručuje bonus, a zatim koristite funkciju `map()` na sledeći način:

```py
def double(bonus):
    return bonus * 2

bonuses = [100, 200, 300]

iterator = map(double, bonuses)
```

Ili možete učiniti ovaj kod sažetijim korišćenjem lambda izraza poput ovog:

```py
bonuses = [100, 200, 300]
iterator = map(lambda bonus: bonus * 2, bonuses)
```

Kada imate iterator, možete iterirati kroz nove elemente koristeći `for` petlju.
Ili možete konvertovati iterator u listu koristeći funkciju list():

```py
bonuses = [100, 200, 300]
iterator = map(lambda bonus: bonus * 2, bonuses)
print(list(iterator))
```

### Korišćenje funkcije map() sa listom stringova

Sledeći primer koristi `map()` funkciju za vraćanje nove liste gde se svaki element transformiše u odgovarajući slučaj:

```py
names = ['david', 'peter', 'jenifer']
new_names = map(lambda name: name.capitalize(), names)
print(list(new_names))
```

Izlaz:
```py
['David', 'Peter', 'Jenifer']
```

### Korišćenje funkcije map() sa listom torki

Pretpostavimo da imate sledeću korpu za kupovinu predstavljenu kao listu torki:

```py
carts = [['SmartPhone', 400],
         ['Tablet', 450],
         ['Laptop', 700]]
```

I potrebno je da izračunate iznos poreza za svaki proizvod sa porezom od 10%. Pored toga, potrebno je da dodate iznos poreza trećem elementu svake stavke na listi.

Lista povratka bi trebalo da bude otprilike ovakva:

```py
[['SmartPhone', 400, 40.0],
['Tablet', 450, 45.0],
['Laptop', 700, 70.0]]
```

Da biste to uradili, možete koristiti `map()` funkciju za kreiranje novog elementa liste i dodavanje novog iznosa poreza svakom ovako:

```py
carts = [['SmartPhone', 400],
         ['Tablet', 450],
         ['Laptop', 700]]

tax = 0.1
carts = map(lambda item: [item[0], item[1], item[2] * tax], carts)

print(list(carts))
```

Izlaz:
```py
[['SmartPhone', 400, 40.0], ['Tablet', 450, 45.0], ['Laptop', 700, 70.0]]
```

### Rezime

Koristite Pajton map()funkciju da pozovete funkciju na svakoj stavki liste i vratite iterator.


## Kako filtrirati elemente liste

**Rezime**: u ovom tutorijalu ćete naučiti kako da filtrirate elemente liste koristeći ugrađenu Pajton `filter()` funkciju.

### Uvod u funkciju filter()

Ponekad je potrebno da iterativno pregledate elemente liste i izaberete neke od njih na osnovu određenih kriterijuma.

Pretpostavimo da imate sledeću listu *scores*:

```py
scores = [70, 60, 80, 90, 50]
```

Da biste dobili sve elemente sa *scores* liste gde je svaki element veći ili jednak 70, koristite sledeći kod:

```py
scores = [70, 60, 80, 90, 50]

filtered = []

for score in scores:
    if score >= 70:
        filtered.append(score)

print(filtered)
```

Izlaz:
```py
[70, 80, 90]
```

Kako to funkcioniše.

- Prvo, definišite praznu listu ( *filtered* ) koja će sadržati elemente iz *scores* liste.
- Drugo, iterativno prođite kroz elemente liste *scores*. Ako je element veći ili jednak 70, dodajte ga na *filtered* listu.
- Treće, prikažite *filtered* listu na ekranu.

Pajton ima ugrađenu funkciju `filter()` koja vam omogućava da filtrirate listu (ili torku) na lepši način.

Sledeći kod prikazuje sintaksu funkcije `filter()`:

```py
filter(fn, list)
```

Funkcija `filter()` iterira kroz elemente liste i primenjuje *fn()* funkciju na svaki element. Vraća iterator za elemente gde *fn()* vraća `True`.

U stvari, drugom argumentu funkcije `filter()` možete proslediti bilo koju iterabilnu stavku, ne samo listu.

Sledeće je prikazano kako se `filter()` funkcija koristi za filtriranje liste *scores* gde je svaki rezultat veći ili jednak 70:

```py
scores = [70, 60, 80, 90, 50]
filtered = filter(lambda score: score >= 70, scores)

print(list(filtered))
```

Izlaz:
```py
[70, 80, 90]
```

Pošto `filter()` funkcija vraća iterator, možete koristiti `for` petlju da biste ga uradili iteraciju. Ili možete koristiti `list()` funkciju da biste iterator pretvorili u listu.

Primer korišćenja funkcije `filter()` u Pajtonu za filtriranje liste torki

Pretpostavimo da imate sledeću listu torki:

```py
countries = [
    ['China', 1394015977],
    ['United States', 329877505],
    ['India', 1326093247],
    ['Indonesia', 267026366],
    ['Bangladesh', 162650853],
    ['Pakistan', 233500636],
    ['Nigeria', 214028302],
    ['Brazil', 21171597],
    ['Russia', 141722205],
    ['Mexico', 128649565]
]
```

Svaki element u listi je torka koja sadrži ime države i broj stanovnika.

Da biste dobili sve zemlje čija je populacija veća od 300 miliona, možete koristiti `filter()` funkciju na sledeći način:

```py
countries = [
    ['China', 1394015977],
    ['United States', 329877505],
    ['India', 1326093247],
    ['Indonesia', 267026366],
    ['Bangladesh', 162650853],
    ['Pakistan', 233500636],
    ['Nigeria', 214028302],
    ['Brazil', 21171597],
    ['Russia', 141722205],
    ['Mexico', 128649565]
]

populated = filter(lambda c: c[1] > 300000000, countries)

print(list(populated))
```

Izlaz:
```py
[['China', 1394015977], ['India', 1326093247], ['United States', 329877505]]
```

### Rezime

Koristite Pajton `filter()` funkciju za filtriranje liste (ili torke).


## Kako redukovati listu na jednu vrednost

**Rezime**: u ovom tutorijalu ćete naučiti kako da koristite funkciju `reduce()` u Pajtonu da biste smanjili listu na jednu vrednost.

### Redukovanje liste

Ponekad želite da svedete listu na jednu vrednost. Na primer, pretpostavimo da imate listu brojeva :

```py
scores = [75, 65, 80, 95, 50]
```

A da biste izračunali zbir svih elemenata na *scores* listi, možete koristiti `for` petlju poput ove:

```py
scores = [75, 65, 80, 95, 50]

total = 0

for score in scores:
    total += score

print(total)
```

Izlaz:
```py
365
```

U ovom primeru, sveli smo celu listu na jednu vrednost, koja je zbir svih elemenata sa liste.

### Uvod u funkciju reduce() u Pajtonu

Pajton nudi funkciju `reduce()` koja vam omogućava da skratite listu na sažetiji način.

Evo sintakse funkcije reduce():

```py
reduce(fn,list)
```

*Funkcija `reduce()` primenjuje *fn* funkciju sa dva argumenta kumulativno na stavke liste, s leva na desno, da bi svela listu na jednu vrednost.

Za razliku od funkcija `map()` i `filter()`, `reduce()` nije ugrađena funkcija u Pajtonu. U stvari, `reduce()`funkcija pripada `functools` modulu.

Da biste koristili `reduce()`funkciju, potrebno je da je uvezete iz `functools`modula koristeći sledeću naredbu na vrhu datoteke:

```py
from functools import reduce
```

Sledeće ilustruje kako se `reduce()` funkcija koristi za izračunavanje zbira elemenata liste scores:

```py
from functools import reduce

def sum(a, b):
    print(f"a={a}, b={b}, {a} + {b} ={a+b}")
    return a + b


scores = [75, 65, 80, 95, 50]
total = reduce(sum, scores)
print(total)
```

Izlaz:
```py
a=75, b=65, 75 + 65 = 140
a=140, b=80, 140 + 80 = 220
a=220, b=95, 220 + 95 = 315
a=315, b=50, 315 + 50 = 365
365
```

Kao što se jasno vidi iz izlaza, `reduce()` funkcija kumulativno dodaje dva elementa liste s leva na desno i smanjuje celu listu na jednu vrednost.

Da biste kod učinili sažetijim, možete koristiti `lambda izraz` umesto definisanja `sum()` funkcije:

```py
from functools import reduce

scores = [75, 65, 80, 95, 50]
total = reduce(lambda a, b: a + b, scores)

print(total)
```

### Rezime

Koristite Pajton `reduce()` funkciju da biste smanjili listu na jednu vrednost.

