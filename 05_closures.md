
# Zatvaranja

**Rezime**: u ovom tutorijalu ćete saznati o zatvaranjima u Pajtonu i njihovoj praktičnoj primeni.

## Uvod u zatvaranja u Pajtonu

U Pajtonu možete definisati funkciju unutra druge funkcije. Ova funkcija se naziva ugnežđena funkcija. Na primer:

```py
def say():
    greeting = 'Hello'

    def display():
        print(greeting)

    display()
```

U ovom primeru, definišemo *display* funkciju unutar *say* funkcije. *display* funkcija se naziva ugnežđena funkcija.

Unutar *display* funkcije, pristupate *greeting* promenljivoj iz njenog `nelokalnog` opsega važenja.

Pajton naziva *greeting* promenljivu slobodnom promenljivom.

Kada pogledate *display* funkciju, zapravo gledate na:

- Sama funkcija display.
- I slobodna promenljiva *greeting* sa vrednošću 'Hello'.

Dakle, kombinacija funkcije *display* i *greeting* promenljive naziva se `zatvaranje` ( ili `closure` ):

**Po definiciji**, `zatvaranje` je ugnežđena funkcija koja referencira jednu ili više promenljivih iz svog obuhvatajućeg opsega važenja.

## Vraćanje unutrašnje funkcije

U Pajtonu, funkcija može vratiti vrednost koja je druga funkcija. Na primer:

```py
def say():
    greeting = 'Hello'

    def display():
        print(greeting)

    return display    
```

U ovom primeru, *say* funkcija vraća *display* funkciju umesto da je izvršava.
Kada *say* funkcija vrati *display* funkciju, ona zapravo vraća `zatvaranje`:

Sledeća komanda dodeljuje povratnu vrednost funkcije *say* promenljivoj *fn*. Pošto je *fn* funkcija, možete je izvršiti:

```py
fn = say()
fn()
```
Izlaz
```py
Hello
```

Funkcija *say* se izvršava i vraća funkciju *fn*. Kada se *fn* funkcija izvršava, funkcija *say* je već završena. Drugim rečima, opseg funkcije *say* je nestao u trenutku izvršavanja *fn* funkcije.

Pošto *greeting* promenljiva pripada opsegu važenja funkcije *say*, ona bi takođe trebalo da bude uništena sa opsegom važenja funkcije *say*.

Međutim, vidite da *fn* i dalje prikazuje vrednost promenljive *greeting*. 

## Pajton ćelije i promenljive sa višestrukim domenima

Vrednost promenljive *greeting* se deli između dva opsega važenja:

- Funkcija say,
- Zatvaranja fn.

Oznake *greeting* su u dva različita opsega. Međutim, one uvek referenciraju isti string objekat sa vrednošću 'Hello'.

Da bi se ovo postiglo, Pajton kreira posrednički objekat koji se zove `cell`:

Da biste pronašli memorijsku adresu objekta ćelije, možete koristiti `__closure__` svojstvo na sledeći način:

```py
print(fn.__closure__)
```

Izlaz:
```py
(<cell at 0x0000017184915C40: str object at 0x0000017186A829B0>,)
```

Vraća `__closure__` torku ćelije.

U ovom primeru, memorijska adresa ćelije je 0x0000017184915C40. Ona referencira string objekat na 0x0000017186A829B0.

Ako prikažete memorijsku adresu objekta tipa string u *say* funkciji i *closure*, trebalo bi da vidite da se odnose na isti objekat u memoriji:

```py
def say():
    greeting = 'Hello'
    print(hex(id(greeting)))

    def display():
        print(hex(id(greeting)))
        print(greeting)

    return display

fn = say()
fn()
```

Izlaz:
```py
0x17186a829b0
0x17186a829b0
```

Kada pristupite vrednosti promenljive *greeting*, Pajton će tehnički "duplo skočiti" da bi dobio vrednost stringa.

Ovo objašnjava zašto kada *say()* funkcija je bila van opsega, i dalje možete pristupiti objektu stringa na koji se poziva *greeting* promenljiva.

Na osnovu ovog mehanizma, možete zamisliti `zatvaranje` kao funkciju i prošireni opseg koji sadrži slobodne promenljive.

Da biste pronašli slobodne promenljive koje zatvaranje sadrži, možete koristiti `__code__.co_freevars`, na primer:

```py
def say():

    greeting = 'Hello'

    def display():
        print(greeting)

    return display

fn = say()
print(fn.__code__.co_freevars)
```

Izlaz:
```py
('greeting',)
```

U ovom primeru, `fn.__code__.co_freevars` vraća *greeting* slobodnu promenljivu zatvaranja *fn*.

## Kada Pajton kreira zatvaranje

Pajton kreira novi opseg važenja kada se funkcija izvrši. Ako ta funkcija kreira zatvaranje, Pajton takođe kreira novo zatvaranje. Razmotrite sledeći primer:

- Prvo, definišite funkciju *multiplier* koja vraća zatvaranje:

    ```py
    def multiplier(x):
        def multiply(y):
            return x * y
        return multiply
    ```

    Funkcija *multiplier* vraća *multiply* dva argumenta. Međutim, umesto toga koristi zatvaranje.

- Drugo, pozovite multiplier funkciju tri puta:

    ```py
    m1 = multiplier(1)
    m2 = multiplier(2)
    m3 = multiplier(3)
    ```

    Ovi pozivi funkcija kreiraju tri zatvaranja. Svaka funkcija množi broj sa 1, 2 i 3.

- Treće, izvršite funkcije zatvaranja:

    ```py
    print(m1(10))
    print(m2(10))
    print(m3(10))
    ```

    Linije m1, m2 i m3 imaju različite slučajeve zatvaranja.

Spojite sve zajedno:

```
def multiplier(x):
    def multiply(y):
        return x * y
    return multiply

m1 = multiplier(1)
m2 = multiplier(2)
m3 = multiplier(3)

print(m1(10))
print(m2(10))
print(m3(10))
```

Izlaz:
```py
10
20
30
```

## Zatvaranja u Pajtonu i petlja

Pretpostavimo da želite da kreirate sva tri gore navedena zatvaranja odjednom i možda ćete smisliti sledeće:

```py
multipliers = []
for x in range(1, 4):
    multipliers.append(lambda y: x * y)

m1, m2, m3 = multipliers

print(m1(10))
print(m2(10))
print(m3(10))
```

Kako ovo funkcioniše:

- Prvo, deklarišite listu *multipliers* koja će čuvati zatvaranja.
- Drugo, koristite `lambda izraz` da biste kreirali zatvaranje i dodali zatvaranje listi *multipliers* u svakoj iteraciji.
- Treće, raspakujte zatvaranja sa liste u promenljive *m1*, *m2* i *m3*.
- Konačno, prosledite vrednost 10, svakom zatvaranju i izvršite ga.

Sledeće prikazuje izlaz:
```py
30
30
30
```

Ovo ne funkcioniše kako si očekivalo. Ali zašto?

*x* počinje od 1 i ide do 3 u for petlji. Nakon petlje, njegova vrednost je 3.
Svaki element liste je sledeći rezultata:

```py
lambda y: x * y
```

Pajton izvršava x kada pozovete *m1(10)*, *m2(10)* i *m3(10)*. U trenutku izvršavanja zatvaranja, *x* je 3.

Zato vidite isti rezultat kada pozovete m1(10), m2(10), i m3(10).

Da biste ovo popravili, potrebno je da naložite Pajtonu da izvrši izvršenje *x* u petlji:

```py
def multiplier(x):
    def multiply(y):
        return x * y
    return multiply

multipliers = []
for x in range(1, 4):
    multipliers.append(multiplier(x))

m1, m2, m3 = multipliers

print(m1(10))
print(m2(10))
print(m3(10))
```

## Rezime

Zatvaranje je funkcija i prošireni opseg koji sadrže slobodne promenljive.
