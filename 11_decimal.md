
Pajton decimalni sistem

Rezime : u ovom tutorijalu ćete saznati o Pajton decimalmodulu koji podržava brzu aritmetiku sa pokretnim zarezom i ispravnim zaokruživanjem.
Uvod u decimalni modul Pajtona

Mnogi decimalni brojevi nemaju tačne reprezentacije u binarnom sistemu sa pokretnim zarezom, kao što je 0,1. Kada koristite ove brojeve u aritmetičkim operacijama, dobićete rezultat koji ne biste očekivali. Na primer:

x = 0.1
y = 0.1
z = 0.1

s = x + y + z

print(s)
Jezik koda:  PHP  ( php )

Izlaz:

0.30000000000000004
Jezik koda:  CSS  ( css )

Rezultat je 0,30000000000000004, a ne 0,3.

Da biste rešili ovaj problem, koristite Decimalklasu iz decimalmodula na sledeći način:

import decimal
from decimal import Decimal


x = Decimal('0.1')
y = Decimal('0.1')
z = Decimal('0.1')

s = x + y + z

print(s)
Jezik koda:  Javaskript  ( javaskript )

Izlaz:

0.3
Jezik koda:  CSS  ( css )

Izlaz je kao što se očekivalo.

Pajton decimalmodul podržava aritmetiku koja funkcioniše isto kao aritmetika koju učite u školi.

Za razliku od float-ova , Pajton tačno predstavlja decimalne brojeve. I tačnost se prenosi i na aritmetiku. Na primer, sledeći izraz vraća tačno 0,0:

Decimal('0.1') + Decimal('0.1') + Decimal('0.1') - Decimal('0.3')
Jezik koda:  Javaskript  ( javaskript )

Decimalni kontekst

Decimalni broj se uvek povezuje sa kontekstom koji kontroliše sledeće aspekte:

    Preciznost tokom aritmetičke operacije
    Algoritam za zaokruživanje

Podrazumevano, kontekst je globalan. Globalni kontekst je podrazumevani kontekst. Takođe, možete podesiti privremeni kontekst koji će stupiti na snagu lokalno bez uticaja na globalni kontekst.

Da biste dobili podrazumevani kontekst, pozovete getcontext()funkciju iz decimalmodula:

decimal.getcontext()
Jezik koda:  CSS  ( css )

Funkcija getcontext()vraća podrazumevani kontekst, koji može biti globalni ili lokalni.

Da biste kreirali novi kontekst kopiran iz drugog konteksta, koristite localcontext()funkciju:

decimal.localcontext(ctx=None)

Vraća localcontext()novi kontekst kopiran iz konteksta ctxako je naveden.

Kada dobijete kontekstni objekat, možete pristupiti preciznosti i rutiranju preko svojstva preci , roundingrespektivno:

    ctx.pre: preuzimanje ili podešavanje preciznosti. ctx.pre je ceo broj koji je podrazumevano 28
    ctx.rounding: preuzima ili postavlja mehanizam zaokruživanja. Zaokruživanje je string. Podrazumevana vrednost je 'ROUND_HALF_EVEN'. Zabeležene brojeve sa pokretnim plutajućim sklopom takođe koriste ovaj mehanizam zaokruživanja.

Pajton pruža sledeće mehanizme zaokruživanja:
Zaokruživanje 	Opis
ZAOKRUŽIVANJE 	zaokružiti od nule
ZAOKRUŽIVANJE NADOLE 	zaokružiti prema nuli
OKRUGLI_PLAFON 	zaokruženo do plafona (prema pozitivnoj beskonačnosti)
OKRUGLI_SPRAT 	zaokružiti do poda (prema negativnoj beskonačnosti)
POLA_OKRUGLENOG_GORNJEG 	zaokruženo na najbliže, vezano dalje od nule
ZAOKRUGLENOST_POLA_DOLE 	zaokruženo na najbliže, vezano za nulu
POLA_RUNDA_PARA 	zaokružiti na najbliži, vezati za parni broj (najmanja značajna cifra)

Ovaj primer ilustruje kako dobiti podrazumevanu preciznost i zaokruživanje podrazumevanog konteksta:

import decimal

ctx = decimal.getcontext()

print(ctx.prec)
print(ctx.rounding)
Jezik koda:  PHP  ( php )

Izlaz:

28
ROUND_HALF_EVEN

Sledeći primer pokazuje kako 'ROUND_HALF_EVEN'mehanizam zaokruživanja stupa na snagu:

import decimal
from decimal import Decimal


x = Decimal('2.25')
y = Decimal('3.35')

print(round(x, 1))
print(round(y, 1))
Jezik koda:  Javaskript  ( javaskript )

Izlaz:

2.2
3.4
Jezik koda:  CSS  ( css )

Ako promenite zaokruživanje na 'ROUND_HALF_UP', dobićete drugačiji rezultat:

import decimal
from decimal import Decimal


ctx = decimal.getcontext()
ctx.rounding = decimal.ROUND_HALF_UP

x = Decimal('2.25')
y = Decimal('3.35')

print(round(x, 1))
print(round(y, 1))
Jezik koda:  Javaskript  ( javaskript )

Izlaz:

2.3
3.4
Jezik koda:  CSS  ( css )

Sledeći primer vam pokazuje kako da kopirate podrazumevani kontekst i promenite zaokruživanje na 'ROUND_HALF_UP':

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
Jezik koda:  PHP  ( php )

Izlaz:

Local context:
2.3
3.4
Global context:
2.2
3.4
Jezik koda:  CSS  ( css )

Obratite pažnju da lokalni kontekst ne utiče na globalni kontekst. Nakon bloka with, Pajton koristi podrazumevani mehanizam zaokruživanja.
Decimalni konstruktor

Konstruktor Decimalvam omogućava da kreirate novi Decimalobjekat na osnovu vrednosti:

Decimal(value='0', context=None)
Jezik koda:  Javaskript  ( javaskript )

Argument valuemože biti ceo broj, string, torka, broj sa pokretnim zamahom ili drugi objekat tipa Decimal. Ako ne navedete argument vrednosti, podrazumevano se koristi '0'.

Ako je vrednost torka, trebalo bi da ima tri komponente: znak (0 za pozitivnu ili 1 za negativnu), torku cifara i celobrojni eksponent:

(sign, (digit1,digit2, digit3,...), exponent)

Na primer:

3.14 = 314 x 10^-2

Torka ima tri elementa kao što sledi:

    znak je 0
    cifre su (3,1,4)
    eksponent je -2

Stoga, konstruktoru ćete morati da prosledite sledeći tupl Decimal:

import decimal
from decimal import Decimal

x = Decimal((0, (3, 1, 4), -2))
print(x)
Jezik koda:  Javaskript  ( javaskript )

Izlaz:

3.14
Jezik koda:  CSS  ( css )

Obratite pažnju da preciznost decimalnog konteksta utiče samo na aritmetičku operaciju, a ne na Decimalkonstruktor. Na primer:

import decimal
from decimal import Decimal


decimal.getcontext().prec = 2

pi = Decimal('3.14159')
radius = 1

print(pi)

area = pi * radius * radius
print(area)
Jezik koda:  Javaskript  ( javaskript )

Kada koristite broj sa pokretnim zarezom (float) koji nema tačnu binarnu reprezentaciju sa pokretnim zarezom, Decimalkonstruktor ne može da kreira tačnu decimalnu reprezentaciju. Na primer:

import decimal
from decimal import Decimal

x = Decimal(0.1)
print(x)
Jezik koda:  Javaskript  ( javaskript )

Izlaz:

0.1000000000000000055511151231257827021181583404541015625
Jezik koda:  CSS  ( css )

U praksi, koristićete string ili torku da biste konstruisali Decimal.
Decimalne aritmetičke operacije

Neki aritmetički operatori ne funkcionišu isto kao brojevi sa pokretnim zarezom ili celi brojevi , kao što su div ( //) i mod() %).

Za decimalne brojeve, //operator vrši skraćeno deljenje:

x // y = trunc( x / y)
Jezik koda:  Javaskript  ( javaskript )

Klasa Decimalpruža neke matematičke operacije kao što su sqrti log. Međutim, nema sve funkcije definisane u mathmodulu.

Kada koristite funkcije iz mathmodula za decimalne brojeve, Pajton će konvertovati Decimalobjekte u brojeve sa pokretnim zarezom pre nego što izvrši aritmetičke operacije. Ovo dovodi do gubitka preciznosti ugrađene u decimalne objekte.
Rezime

    Koristite Pajton decimalmodul kada želite da podržite brzu, ispravno zaokruženu aritmetiku sa pokretnim zarezom.
    Koristite Decimalklasu iz decimalmodula za kreiranje objekta Decimal od stringova, celih brojeva i torki.
    Brojevi Decimalimaju kontekst koji kontroliše mehanizam preciznosti i zaokruživanja.
    Klasa Decimalnema sve metode definisane u mathmodulu. Međutim, trebalo bi da koristite aritmetičke metode klase Decimal ako su dostupne.
