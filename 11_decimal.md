
# Decimal modul

**Rezime**: u ovom tutorijalu ćete saznati o Pajton `decimal modulu` koji podržava brzu aritmetiku sa pokretnim zarezom i ispravnim zaokruživanjem.

## Python decimal modul

### Uvod u decimal modul

Mnogi decimalni brojevi nemaju tačne reprezentacije u binarnom sistemu sa pokretnim zarezom, kao što je 0,1. Kada koristite ove brojeve u aritmetičkim operacijama, dobićete rezultat koji ne biste očekivali. Na primer:

```py
x = 0.1
y = 0.1
z = 0.1

s = x + y + z

print(s)
```

Izlaz:

```shell
0.30000000000000004
```

Rezultat je 0,30000000000000004, a ne 0,3.

Da biste rešili ovaj problem, koristite `Decimal` klasu iz `decimal` modula na sledeći način:

```py
import decimal
from decimal import Decimal

x = Decimal('0.1')
y = Decimal('0.1')
z = Decimal('0.1')

s = x + y + z

print(s)
```

Izlaz:

```shell
0.3
```

Izlaz je kao što se očekivalo.

Pajton `decimal` modul podržava aritmetiku koja funkcioniše isto kao aritmetika koju učite u školi.

Za razliku od float-a , Pajton tačno predstavlja decimalne brojeve. I tačnost se prenosi i na aritmetiku. Na primer, sledeći izraz vraća tačno 0,0:

```py
Decimal('0.1') + Decimal('0.1') + Decimal('0.1') - Decimal('0.3')
```

### Decimalni kontekst

Decimalni broj se uvek povezuje sa kontekstom koji kontroliše sledeće aspekte:

- Preciznost tokom aritmetičke operacije
- Algoritam za zaokruživanje

Podrazumevano, kontekst je globalan. Globalni kontekst je podrazumevani kontekst. Takođe, možete podesiti privremeni kontekst koji će stupiti na snagu lokalno bez uticaja na globalni kontekst.

Da biste dobili podrazumevani kontekst, pozovete `getcontext()` funkciju iz `decimal` modula:

```py
decimal.getcontext()
```

Funkcija `getcontext()` vraća podrazumevani kontekst, koji može biti globalni ili lokalni.

Da biste kreirali novi kontekst kopiran iz drugog konteksta, koristite `localcontext()` funkciju:

```py
decimal.localcontext(ctx=None)
```

`localcontext()` vraća novi kontekst kopiran iz konteksta `ctx` ako je naveden.

Kada dobijete kontekstni objekat, možete pristupiti preciznosti i rutiranju preko svojstva `pre`, `rounding` respektivno:

- `ctx.pre`: preuzimanje ili podešavanje preciznosti. ctx.pre je ceo broj koji je podrazumevano 28
- `ctx.rounding`: preuzima ili postavlja mehanizam zaokruživanja. Zaokruživanje je string. Podrazumevana vrednost je 'ROUND_HALF_EVEN'. Zabeležene brojeve sa pokretnim plutajućim sklopom takođe koriste ovaj mehanizam zaokruživanja.

Pajton pruža sledeće mehanizme zaokruživanja:

Zaokruživanje   | Opis
----------------|----------------------
ROUND_UP        | zaokruženo od nule
ROUND_DOWN      | zaokruženo prema nuli
ROUND_CEILING   | zaokruženo do max (prema pozitivnoj beskonačnosti)
ROUND_FLOOR     | zaokruženo do min (prema negativnoj beskonačnosti)
ROUND_HALF_UP   | zaokruženo na najbliže, vezano dalje od nule
ROUND_HALF_DOWN | zaokruženo na najbliže, vezano za nulu
ROUND_HALF_EVEN | zaokruženo na najbliži, vezati za parni broj (najmanja značajna cifra)

Ovaj primer ilustruje kako dobiti podrazumevanu preciznost i zaokruživanje podrazumevanog konteksta:

```py
import decimal

ctx = decimal.getcontext()

print(ctx.prec)
print(ctx.rounding)
```

Izlaz:

```shell
28
ROUND_HALF_EVEN
```

Sledeći primer pokazuje kako 'ROUND_HALF_EVEN' mehanizam zaokruživanja stupa na snagu:

```py
import decimal
from decimal import Decimal


x = Decimal('2.25')
y = Decimal('3.35')

print(round(x, 1))
print(round(y, 1))
```

Izlaz:

```shell
2.2
3.4
```

Ako promenite zaokruživanje na 'ROUND_HALF_UP', dobićete drugačiji rezultat:

```py
import decimal
from decimal import Decimal


ctx = decimal.getcontext()
ctx.rounding = decimal.ROUND_HALF_UP

x = Decimal('2.25')
y = Decimal('3.35')

print(round(x, 1))
print(round(y, 1))
```

Izlaz:

```shell
2.3
3.4
```

Sledeći primer vam pokazuje kako da kopirate podrazumevani kontekst i promenite zaokruživanje na 'ROUND_HALF_UP':

```py
import decimal
from decimal import Decimal

x = Decimal('2.25')
y = Decimal('3.35')

with decimal.localcontext() as ctx:
    print('Local context:')
    ctx.rounding = decimal.ROUND_HALF_UP
    print(round(x, 1))
    print(round(y, 1))

print('Global context:')
print(round(x, 1))
print(round(y, 1))
```

Izlaz:

```shell
Local context:
2.3
3.4
Global context:
2.2
3.4
```

Obratite pažnju da lokalni kontekst ne utiče na globalni kontekst. Nakon bloka with, Pajton koristi podrazumevani mehanizam zaokruživanja.

### Decimal konstruktor

Konstruktor `Decimal` vam omogućava da kreirate novi `Decimal` objekat na osnovu vrednosti:

```py
Decimal(value='0', context=None)
```

Argument `value` može biti ceo broj, string, torka, broj sa pokretnim zamahom ili drugi objekat tipa Decimal. Ako ne navedete argument vrednosti, podrazumevano se koristi '0'.

Ako je vrednost torka, trebalo bi da ima tri komponente:

- znak (0 za pozitivnu ili 1 za negativnu),
- torku cifara i
- celobrojni eksponent:

(sign, (digit1,digit2, digit3,...), exponent)

Na primer:

```py
3.14 = 314 x 10^-2
```

Torka ima tri elementa kao što sledi:

- znak je 0
- cifre su (3,1,4)
- eksponent je -2

Stoga, konstruktoru ćete morati da prosledite sledeći tupl Decimal:

```py
import decimal
from decimal import Decimal

x = Decimal((0, (3, 1, 4), -2))
print(x)
```

Izlaz:

```shell
3.14
```

Obratite pažnju da preciznost decimalnog konteksta utiče samo na aritmetičku operaciju, a ne na `Decimal` konstruktor. Na primer:

```py
import decimal
from decimal import Decimal

decimal.getcontext().prec = 2

pi = Decimal('3.14159')
radius = 1

print(pi)

area = pi * radius * radius
print(area)
```

Kada koristite broj sa pokretnim zarezom (float) koji nema tačnu binarnu reprezentaciju sa pokretnim zarezom, `Decimal` konstruktor ne može da kreira tačnu decimalnu reprezentaciju. Na primer:

```py
import decimal
from decimal import Decimal

x = Decimal(0.1)
print(x)
```

Izlaz:

```shell
0.1000000000000000055511151231257827021181583404541015625
```

U praksi, koristićete string ili torku da biste konstruisali Decimal.

### Decimalne aritmetičke operacije

Neki aritmetički operatori ne funkcionišu isto kao sa brojevima sa pokretnim zarezom ili celim brojevi, kao što su div ( // ) i mod( % ).

Za decimalne brojeve, // operator vrši skraćeno deljenje:

```py
x // y = trunc( x / y)
```

Klasa `Decimal` pruža neke matematičke operacije kao što su `sqrt` i `log`. Međutim, nema sve funkcije definisane u `math` modulu.

Kada koristite funkcije iz `math` modula za decimalne brojeve, Pajton će konvertovati `Decimal` objekte u brojeve sa pokretnim zarezom pre nego što izvrši aritmetičke operacije. Ovo dovodi do gubitka preciznosti ugrađene u decimalne objekte.

### Rezime

- Koristite Pajton `decimal` modul kada želite da podržite brzu, ispravno zaokruženu aritmetiku sa pokretnim zarezom.
- Koristite `Decimal` klasu iz `decimal` modula za kreiranje objekta Decimal od stringova, celih brojeva i torki.
Brojevi `Decimal` imaju kontekst koji kontroliše mehanizam preciznosti i zaokruživanja.
- Klasa `Decimal` nema sve metode definisane u `math`modulu. Međutim, trebalo bi da koristite aritmetičke metode klase `Decimal` ako su dostupne.
