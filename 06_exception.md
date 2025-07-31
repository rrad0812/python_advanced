
# Izuzeci

**Rezime**: u ovom tutorijalu ćete naučiti o izuzecima u Pajtonu i kako da ih graciozno obradite u programima.

## Uvod

### Uvod u izuzetke

U Pajtonu, izuzeci su objekti klasa izuzetaka. Sve klase izuzetaka su podklase  `BaseException` klase.

Međutim, skoro sve ugrađene klase izuzetaka nasleđuju od `Exception` klase , koja je podklasa klase `BaseException`:

Sledeći primer definiše listu od tri elementa i pokušava da pristupi četvrtom:

```py
colors = ['red', 'green', 'blue']

print(colors[3])
```

Nevažeći indeks je izazvao `IndexError` izuzetak kako se i očekivalo:

```py
IndexError: list index out of range
```

Kada se desi izuzetak, Pajton zaustavlja program osim ako ga ne obradite. Da biste obradili izuzetak, koristite `try...except` naredbu. Na primer:

```py
colors = ['red', 'green', 'blue']

try:
    print(colors[3])
except IndexError as e:
    print(e)

print('Continue to run')
```

Izlaz:

```shell
<class 'IndexError'> - list index out of range
Continue to run
```

U ovom primeru, koristimo `try...except` naredbu za obradu `IndexError` izuzetka. Kao što možete videti iz izlaza, program nastavlja da se izvršava nakon `try...except` naredbe.

Klasa `IndexError` nasleđuje od `LookupError` klase koja nasleđuje od `Exception` klase:

I možete uhvatiti bilo koju od klasa `LookupError` ili `ExceptionIndexError` kada se dogodi izuzetak. Na primer:

```py
colors = ['red', 'green', 'blue']

try:
    print(colors[3])
except LookupError as e:
    print(e.__class__, '-', e)

print('Continue to run')
```

Izlaz:

```shell
<class 'IndexError'> - list index out of range
Continue to run
```

U ovom primeru, izuzetak je i dalje prisutni `IndexError` iako ga hvatamo sa `LookupError`. Stoga, kada obrađujete izuzetak, program za obradu izuzetaka će hvatati tip izuzetka koji navedete i bilo koji od njegovih podklasa.

Program se pokreće isto ako koristite `Exception` klasu umesto `LookupError` klase:

```py
colors = ['red', 'green', 'blue']

try:
    print(colors[3])
except Exception as e:
    print(e.__class__, '-', e)

print('Continue to run')
```

Izlaz:

```shell
<class 'IndexError'> - list index out of range
Continue to run
```

U praksi, trebalo bi da beležite izuzetke što je preciznije moguće kako biste znali kako da se nosite sa svakim izuzetkom na specifičan način.

### Primer rada sa izuzecima

Sledeći primer definiše *division* funkciju koja vraća rezultat deljenja a sa b:

```py
def division(a, b):
    return a / b

c = division(10, 0)
```

Izlaz

```shell
ZeroDivisionError: division by zero
```

U ovom primeru, ako je b nula, doći će do `ZeroDivisionError` izuzetka. Da biste obradili `ZeroDivisionError` izuzetak, koristite `try...except` naredbu:

```py
def division(a, b):
    try:
        return {
            'success': True,
            'message': 'OK',
            'result': a / b
        }
    except ZeroDivisionError as e:
        return {
            'success': False,
            'message': 'b cannot be zero',
            'result': None
        }


result = division(10, 0)
print(result)
```

Izlaz:

```shell
{'success': False, 'message': 'b cannot be zero', 'result': None}
```

U ovom primeru, funkcija vraća rečnik koji ima tri elementa:

- `success` je bulova vrednost koja pokazuje da li je operacija uspešna ili ne.
- `message` označava poruku o grešci ili uspehu.
- `result` čuva rezultat od a / b ili `None` ako je b nula.

Sledeće prikazuje izlaz ako se `ZeroDivisionError` dogodi:

```shell
{'success': False, 'message': 'b cannot be zero', 'result': None}
```

Sada, ako ne uhvatite `ZeroDivisionError` izuzetak već opštiji izuzetak poput `Exception` klase:

```py
def division(a, b):
    try:
        return {
            'success': True,
            'message': 'OK',
            'result': a / b
        }
    except Exception as e:
        return {
            'success': False,
            'message': 'b cannot be zero',
            'result': None
        }


result = division(10, 0)
print(result)
```

Izlaz:

```shell
{'success': False, 'message': 'b cannot be zero', 'result': None}
```

Program radi kao i pre jer `try...except` takođe hvata tip izuzetka koji je podklasa klase `Exception`.

Međutim, ako funkciji `division()` prosledite dva stringa umesto dva broja, dobićete istu poruku kao da se `ZeroDivisionError` izuzetak dogodio:

```py
def division(a, b):
    try:
        return {
            'success': True,
            'message': 'OK',
            'result': a / b
        }
    except Exception as e:
        return {
            'success': False,
            'message': 'b cannot be zero',
            'result': None
        }


result = division('10', '2')
print(result)
```

Izlaz:

```shell
{'success': False, 'message': 'b cannot be zero', 'result': None}
```

U ovom primeru, izuzetak nije `ZeroDivisionError` već `TypeError`. Međutim, kod ga i dalje obrađuje kao `ZeroDivisionError` izuzetak.

Stoga bi uvek trebalo da obrađujete izuzetke od najspecifičnijeg do najmanje specifičnog. Na primer:

```py
def division(a, b):
    try:
        return {
            'success': True,
            'message': 'OK',
            'result': a / b
        }
    except TypeError as e:
        return {
            'success': False,
            'message': 'Both a & b must be numbers',
            'result': None
        }
    except ZeroDivisionError as e:
        return {
            'success': False,
            'message': 'b cannot be zero',
            'result': None
        }
    except Exception as e:
        return {
            'success': False,
            'message': str(e),
            'result': None
        }


result = division('10', '2')
print(result)
```

Izlaz:

```shell
{'success': False, 'message': 'Both a & b must be numbers', 'result': None}
```

U ovom primeru, hvatamo `TypeError`, `ZeroDivisionError`, i `Exception` redosledom kojim se pojavljuju u `try...except` izjavi.

Ako je kod koji obrađuje različite izuzetke isti, možete grupisati sve izuzetke u jedan na sledeći način:

```py
def division(a, b):
    try:
        return {
            'success': True,
            'message': 'OK',
            'result': a / b
        }
    except (TypeError, ZeroDivisionError, Exception) as e:
        return {
            'success': False,
            'message': str(e),
            'result': None
        }


result = division(10, 0)
print(result)
```

Izlaz:

```shell
{'success': False, 'message': 'division by zero', 'result': None}
```

### Rezime uvoda u izuzetke

- Pajton izuzeci su objekti klasa, koje su podklase klase BaseException.
- Obradi izuzetak od najspecifičnijeg do najmanje specifičnog.

## Obrada izuzetaka

**Rezime**: u ovom tutorijalu ćete naučiti kako da pravilno obrađujete izuzetke u Pajtonu koristeći trynaredbu.

### Uvod u obradu izuzetaka

Za obradu izuzetaka koristite `try` naredbu. `try` naredba ima sledeće klauzule:

```py
try:
    # code that you want to protect from exceptions
except <ExceptionType> as ex:
    # code that handle the exception
finally:
    # code that always execute whether the exception occurred or not
else:
    # code that excutes if try execute normally (an except clause must be present)
```

Hajde da detaljnije ispitamo try naredbu.

U try klauzuli smeštate kod koji štitite od jednog ili više potencijalnih izuzetaka. Dobra je praksa da kod bude što kraći. Često ćete imati jednu izjavu u `try` klauzuli.

Klauzula `try` se pojavljuje tačno jednom u `try` izjavi.

U `except` klauzuli se postavlja kod koji obrađuje određeni tip izuzetka. `try` naredba može imati nula ili više `except` klauzula. Tipično, svaka `except` klauzula obrađuje različite tipove izuzetaka na specifične načine.

U `except` klauzuli, `as ex` je opciono. I `<ExceptionType>` je takođe opciono. Međutim, ako izostavite `<ExceptionType> as ex`, imaćete golu obrađivačku jedinicu izuzetaka.

Prilikom navođenja tipova izuzetaka u `except` klauzulama, postavljate najspecifičnije do najmanje specifičnih izuzetaka od vrha nadole.

Ako imate istu logiku koja obrađuje različite tipove izuzetaka, možete ih grupisati u jednu `except` klauzulu. Na primer:

```py
try:
...
except <ExceptionType1> as ex:
    log(ex)
except <ExceptionType2> as ex:
    log(ex)
```

```py
try:
...
except (<ExceptionType1>, <ExceptionType2>) as ex:
    log(ex)
```

Važno je napomenuti da `except` redosled je važan jer će Pajton pokrenuti prvu `except` klauzulu čiji tip izuzetka odgovara nastalom izuzetku.

Klauzula `finally` se može pojaviti nula ili jednom u `try` naredbi. `finally` klauzula se uvek izvršava bez obzira da li se dogodio izuzetak ili ne.

Klauzula `else` se takođe pojavljuje nula ili jednom. I `else` klauzula je validna samo ako naredba try ima bar jednu `except` klauzulu.

Obično se postavlja kod koji se izvršava ako se `try` klauzula normalno završi.

### Primer obrade izuzetaka

Sledeća definicija definiše funkciju koja vraća rezultat broja pomoću drugog broja:

```py
def divide(a, b):
    return a / b
```

Ako drugom argumentu odamo 0, dobićemo ZeroDivisionError izuzetak:

```py
divide(10, 0)
```

Greška:

```py
ZeroDivisionError: division by zero
```

Da biste to popravili, možete obraditi `ZeroDivisionError` izuzetak u *divide()* funkciji na sledeći način:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        return None
```

U ovom primeru, *divide()* funkcija vraća vrednost `None` ako se `ZeroDivisionError` desi:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        return None
```

Kada koristite *divide()* funkciju, potrebno je da proverite da li je rezultat None:

```py
result = divide(10, 0)

if result is not None:
    print('result:', result)
else:
    print('Invalid inputs')
```

Ali vraćanje `None` možda nije najbolje jer drugi mogu slučajno proceniti rezultat u `if` izjavi ovako:

```py
result = divide(10, 0)

if result:
    print('result:', result)
else:
    print('Invalid inputs')
```

U ovom slučaju, radi. Međutim, neće raditi ako je prvi argument nula. Na primer:

result = divide(0, 10)

```py
if result:
    print('result:', result)
else:
    print('Invalid inputs')
```

Bolji pristup je da se pozivaocu pokrene izuzetak ako se `ZeroDivisionError` izuzetak dogodio. Na primer:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('The second argument (b) must not be zero')
```

U ovom primeru, divide() funkcija će izdati grešku ako je *b* nula. Da biste koristili *divide()* funkciju, potrebno je da uhvatite `ValueError` izuzetak:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('The second argument (b) must not be zero')

    try:
        result = divide(10, 0)
    except ValueError as e:
        print(e)
    else:
        print('result:', result)
```

Izlaz:

```shell
The second argument (b) must not be zero
```

Dobra je praksa da se u posebnim slučajevima pokrene izuzetak umesto vraćanja `None`.

### Primer

Kada uhvatite izuzetak u klauzuli except, potrebno je da postavite izuzetke od najspecifičnijeg do najmanje specifičnog u smislu hijerarhije izuzetaka.

Sledeće prikazuje tri klase izuzetaka: `Exception`, `LookupError` i `IndexError`:

Ako uhvatite izuzetak, potrebno je da ih postavite sledećim redosledom: `IndexError`, `LookupErorr` i `Exception`.

Na primer, sledeći kod definiše listu od tri niza znakova i pokušava da pristupi četvrtom elementu:

```py
colors = ['red', 'green', 'blue']
try:
    print(colors[3])
except IndexError as e:
    print(type(e), 'Index error')
except LookupError as e:
    print(type(e), 'Lookup error')
```

Izdaje se sledeća greška:

```py
<class 'IndexError'> Index error
```

Pristup *colors[3]* izaziva `IndexError` izuzetak. Međutim, ako zamenite redosled except klauzule i uhvatite LookupError prvu i IndexErrordrugu drugu, ovako:

```py
colors = ['red', 'green', 'blue']
try:
    print(colors[3])
except LookupError as e:
    print(type(e), 'Lookup error')
except IndexError as e:
    print(type(e), 'Index error')
```

Izlaz:

```shell
<class 'IndexError'> Lookup error
```

Izuzetak je i dalje, `IndexErroral` i poruka je obmanjujuća.

### Rukovaoci

Kada želite da uhvatite bilo koji izuzetak, možete koristiti obrađivače izuzetaka. Obradač izuzetaka ne određuje tip izuzetka:

```py
try:
    ...
except:
    ...
```

To je ekvivalentno sledećem:

```py
try:
    ...
except BaseException:
    ...
```

Obrada izuzetaka bez dodatnih funkcija će hvatati sve izuzetke, uključujući izuzetke `SystemExit` i `KeyboardInterupt`.

Samo jedan izuzetak će otežati prekidanje programa pomoću Control-C i prikrivanje drugih programa.

Ako želite da uhvatite sve izuzetke koji signaliziraju greške u programu, možete umesto toga koristiti `except Exception`:

```py
try:
    ...
except Exception:
    ...
```

U praksi, trebalo bi da izbegavate korišćenje obrađivača izuzetaka. Ako ne znate izuzetke koje treba da hvatate, samo pustite da se izuzetak dogodi, a zatim izmenite kod da bi obrađivao te izuzetke.

Da biste dobili informacije o izuzetku iz samog obrađivača izuzetaka, koristite `exc_info()` funkciju iz `sys` modula.

Funkcija `sys.exc_info()` vraća torku koja se sastoji od tri vrednosti:

- `type` je tip izuzetka koji se dogodio. To je podklasa od BaseException.
- `value` je instanca tipa izuzetka.
- `traceback` je objekat koji inkapsulira stek poziva na mestu gde se izuzetak prvobitno dogodio.

Sledeći primer koristi `sys.exc_info()` funkciju za ispitivanje izuzetka kada je string podeljen brojem:

```py
import sys

try:
    '20' / 2
except:
    exc_info = sys.exc_info()
    print(exc_info)
```

Izlaz:

```shell
(<class 'TypeError'>, TypeError("unsupported operand type(s) for /: 'str' and 'int'"), <traceback object at 0x000001F19F42E700>)
```

Izlaz pokazuje da kod u klauzuli `try` izaziva `TypeError` izuzetak. Stoga možete izmeniti kod da bi ga posebno obrađivao na sledeći način:

```py
try:
    '20' / 2
except TypeError as e:
    print(e)
```

Izlaz:

```shell
unsupported operand type(s) for /: 'str' and 'int'
```

### Rezime obrade izuzetaka

- Koristite `try` izjavu za obradu izuzetka.
- U klauzulu `try` stavite samo minimalni kod koji želite da zaštitite od potencijalnih izuzetaka.
- Obrađujte izuzetke od najspecifičnijeg do najmanje specifičnog u smislu tipova izuzetaka. Redosled `except` klauzula je važan.
- Funkcija `finally` se uvek izvršava bez obzira da li su se izuzeci dogodili ili ne.
- Klauzula `else` se izvršava samo kada se `try` normalno završi. `else` klauzula je validna samo ako `try` naredba ima barem jednu `except` klauzulu.
- Izbegavajte korišćenje obrađivača izuzetaka.

## Podizanje izuzetka

**Rezime**: u ovom tutorijalu ćete naučiti kako da podignete izuzetke koristeći Pajton `raise` naredbu.

### Uvod u podizanje izuzetka

Da biste podigli izuzetak, koristite sledeću `raise` izjavu:

```py
raise ExceptionType()
```

ExceptionType() mora biti podklasa klase `BaseException`. Tipično, to je podklasa klase `Exception`. Imajte na umu da `ExceptionType` ne mora biti direktno nasleđen od `Exception` klase. Može indirektno nasleđivati od klase koja je podklasa klase `Exception`.

Klasa `BaseExceptionima` ima  `__init__` metodu koja prihvata `*args` argument. To znači da možete proslediti bilo koji broj argumenata objektu izuzetka prilikom pokretanja izuzetka.

Sledeći primer koristi `raise` naredbu za pokretanje `ValueError` izuzetka. Metodi `__init__` se prosleđuju tri argumenta `ValueError`:

```py
try:
    raise ValueError('The value error exception', 'x', 'y')
except ValueError as ex:
    print(ex.args)
```

Izlaz:

```shell
('The value error exception', 'x', 'y')
```

### Ponovo pokretanje trenutnog izuzetka

Ponekad želite da evidentirate izuzetak i ponovo pokrenete isti izuzetak. U ovom slučaju, možete koristiti `raise` naredbu bez navođenja objekta izuzetka.

Na primer, sledeće definiše *division()* funkciju koja vraća deljenje dva broja:

```py
def division(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        print('Logging exception:', str(ex))
        raise
```

Ako drugom argumentu funkcije *division()* prosledite nulu,  *division()* doći će do `ZeroDivisionError` izuzetka. Međutim, umesto obrade izuzetka, možete ga evidentirati i ponovo podići.

Imajte na umu da ne morate da navodite objekat izuzetka u `raise` naredbi. U ovom slučaju, Pajton zna da će `raise` naredba podići trenutni izuzetak koji je uhvaćen klauzulom `except`.

Sledeći kod izaziva `ZeroDivisionError` izuzetak:

```py
division(1, 0)
```

I videćete i poruku o evidentiranju i izuzetak u izlazu:

```shell
Logging exception: division by zero
Traceback (most recent call last):
  File "c:/pythontutorial/app.py", line 9, in <module>
    division(1, 0)
  File "C:/pythontutorial/app.py", line 3, in division
    return a / b
ZeroDivisionError: division by zero
```

### Pokretanje još jednog izuzetka tokom obrade izuzetka

Prilikom obrade izuzetka, možda ćete želeti da pokrenete drugi izuzetak. Na primer:

```py
def division(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not zero')
```

U *division()* funkciji, podižemo `ValueError` izuzetak ako `ZeroDivisionError` se dogodi.

Ako pokrenete sledeći kod, dobićete detalje traga steka:

```py
division(1, 0)
```

Izlaz:

```shell
Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 3, in division
    return a / b
ZeroDivisionError: division by zero
```

During handling of the above exception, another exception occurred:

```shell
Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 8, in <module>
    division(1, 0)
  File "C:/pythontutorial/app.py", line 5, in division
    raise ValueError('b must not zero')
ValueError: b must not zero
```

- Prvo, `ZeroDivisionError` izuzetak se javlja:

    ```shell
    Traceback (most recent call last):
    File "C:/pythontutorial/app.py", line 3, in division
        return a / b
    ZeroDivisionError: division by zero
    ```

- Drugo, tokom obrade `ZeroDivisionError` izuzetka, `ValueError` izuzetak se javlja:

    ```shell
    Traceback (most recent call last):
    File "C:/pythontutorial/app.py", line 8, in <module>
        division(1, 0)
    File "C:/pythontutorial/app.py", line 5, in division
        raise ValueError('b must not zero')
    ValueError: b must not zero
    ```

### Rezime podizanje izuzetaka

- Koristite Pajton `raise` naredbu da biste podigli izuzetak.
- Prilikom obrade izuzetka, možete pokrenuti isti ili drugi izuzetak.

## raise from naredba

**Rezime**: u ovom tutorijalu ćete naučiti kako da koristite Pajtonovu naredbu `raise from` da biste podigli izuzetak sa dodatnim informacijama.

### Uvod u raise from naredbu

Izjava `raise from` ima sledeću sintaksu:

```py
raise <ExceptionType> from <cause>
```

Tehnički, to je ekvivalentno sledećem:

```py
ex = ExceptionType
ex.__cause__ = cause
raise ex
```

Podrazumevano, `__cause__` atribut na objektima izuzetaka je uvek inicijalizovan na `None`.

### Primer naredbe raise from

Sledeća *divide()* funkcija deli jedan broj drugim i vraća rezultat deljenja:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero')
```

Funkcija *divide()* ima obrađivač izuzetaka koji hvata `ZeroDivisionError` izuzetak. Unutar obrađivača, generišemo novi `ValueError` izuzetak.

Ako drugom argumentu funkcije dodamo nulu divide():

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero') from ex

divide(10, 0)
```

dobićete sledeći trag steka:

```shell
Traceback (most recent call last):
  File "c:/python/app.py", line 3, in divide
    return a / b
ZeroDivisionError: division by zero
```

Za vreme obrade ovog izuzetka drugi izuzetak se pojavljuje:

```shell
Traceback (most recent call last):
  File "c:/python/app.py", line 8, in <module>
    divide(10, 0)
  File "c:/python/app.py", line 5, in divide
    raise ValueError('b must not be zero')
ValueError: b must not be zero
```

Poruka o uvozu je:

```shell
During handling of the above exception, another exception occurred: ZeroDivisionError
```

To znači da se izuzetak dogodio dok ste obrađivali `ValueError` izuzetak.

Da biste naložili Pajtonu da želite da izmenite i prosledite `ZeroDivisionError` izuzetak, možete koristiti `raise from` naredbu:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero') from ex

divide(10, 0)
```

Kada pokrenete kod, dobićete sledeći trag steka:

```shell
Traceback (most recent call last):
  File "c:/python/app.py", line 3, in divide
    return a / b
ZeroDivisionError: division by zero

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "c:/python/app.py", line 8, in <module>
    divide(10, 0)
  File "c:/python/app.py", line 5, in divide
    raise ValueError('b must not be zero') from ex
ValueError: b must not be zero
```

Sada dobijate `ValueError` izuzetak sa uzrokom dodatim atributu `__cause__` objekta izuzetka.

Sledeći kod menja gornji kod da bi se prikazao `__cause__` atribut `ValueError` izuzetka:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero') from ex

try:
    divide(10, 0)
except ValueError as ex:
    print('cause:', ex.__cause__)
    print('exception:', ex)
```

Izlaz:

```shell
cause: division by zero
exception: b must not be zero
```

### Pajton podiže izuzetak iz None

Ako uzrok izuzetka nije važan, možete ga izostaviti korišćenjem sledeće `raise exception from None` naredbe:

```py
raise <ExceptionType> from None
```

Na primer, možete sakriti uzrok ValueErrorizuzetka u divide()funkciji na sledeći način:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        raise ValueError('b must not be zero') from None

try:
    divide(10, 0)
except ValueError as ex:
    print('cause:', ex.__cause__)
    print('exception:', ex)
```

Izlaz:

```shell
cause: None
exception: b must not be zero
```

Sada, `__cause__` je None. Takođe, *divide()* funkcija podiže *ValueError* izuzetak bez ikakvih dodatnih informacija.

Ako uklonite try izjavu u kodu koja poziva *divide()* funkciju:

```py
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        raise ValueError('b must not be zero') from None


divide(10, 0)
```

dobićete sledeći trag steka:

```shell
Traceback (most recent call last):
  File "c:/python/app.py", line 8, in <module>
    divide(10, 0)
  File "c:/python/app.py", line 5, in divide
    raise ValueError('b must not be zero') from None
ValueError: b must not be zero
```

### Rezime raise from naredbe

-- Koristite Pajton `raise from` naredbu da biste izmenili i prosledili postojeći izuzetak.
-- Koristite `raise exception from None` naredbu da sakrijete uzrok izuzetka.

## Prilagođeni izuzetak

**Rezime**: u ovom tutorijalu ćete naučiti kako da definišete prilagođene klase izuzetaka u Pajtonu.

### Uvod u prilagođeni izuzetak

Da biste kreirali prilagođenu klasu izuzetaka, definišete klasu koja nasleđuje ugrađenu `Exception` klasu ili jednu od njenih podklasa, kao što je `ValueError class`

Sledeći primer definiše `CustomException` klasu koja nasleđuje od `Exception` klase:

```py
class CustomException(Exception):
    """ my custom exception class """
```

Imajte na umu da `CustomException` klasa ima dokumentacioni string koji se ponaša kao iskaz. Stoga, ne morate dodavati `pass` iskaz da bi sintaksa bila validna.

Da biste podigli izuzetak `CustomException`, koristite `raise` naredbu. Na primer, sledeći kod koristi `raise` naredbu za podizanje izuzetka `CustomException`:

```py
class CustomException(Exception):
    """ my custom exception class """

try:
    raise CustomException('This is my custom exception')
except CustomException as ex:
    print(ex)
```

Izlaz:

```py
This is my custom exception
```

Kao i standardne klase izuzetaka, prilagođeni izuzeci su takođe klase. Stoga, možete dodati funkcionalnosti prilagođenim klasama izuzetaka kao što su:

- Dodavanje atributa i svojstava.
- Dodavanje metoda, npr. evidentiranje izuzetka, formatiranje izlaza itd.
- Prepisivanje metoda `__str__` and `__repr__`.
- I raditi bilo šta drugo što možete da radite sa redovnom klasom.

U praksi, želećete da prilagođene izuzetke organizujete kreiranjem prilagođene hijerarhije izuzetaka. Prilagođena hijerarhija izuzetaka vam omogućava da hvatate izuzetke na više nivoa, kao što su standardne klase izuzetaka.

### Primer prilagođenog izuzetka

Pretpostavimo da treba da razvijete program koji pretvara temperaturu iz Farenhajta u Celzijuse.

Minimalna i maksimalna vrednost temperature u Farenhajtima su 32 i 212. Ako korisnici unesu vrednost koja nije u ovom opsegu, želite da pokrenete prilagođeni izuzetak, npr. *FahrenheitError*.

**Definišite prilagođenu klasu izuzetka**
Sledeće definiše *FahrenheitError* klasu izuzetka:

```py
class FahrenheitError(Exception):
    min_f = 32
    max_f = 212

    def __init__(self, f, *args):
        super().__init__(args)
        self.f = f

    def __str__(self):
        return f'The {self.f} is not in a valid range {self.min_f, self.max_f}'
```

Kako to funkcioniše.

- Prvo, definišite klasu *FahrenheitError* koja nasleđuje od `Exception` klase.
- Drugo, dodajte dva atributa klase, *min_f* i *max_f* koji predstavljaju minimalne i maksimalne vrednosti Farenhajta.
- Treće, definišite `__init__` metod koji prihvata vrednost Farenhajta ( f ) i određeni broj pozicionih argumenata ( `*args` ). U `__init__metodi`, pozovite `__init__` metod osnovne klase. Takođe, dodelite *f* argument atributu *f* instance.
- Konačno, prepišite `__str__` metodu da biste vratili prilagođenu reprezentaciju instance klase u obliku stringa.

**Definišite funkciju fahrenheit_to_celsius**
Sledeća definicija definiše *fahrenheit_to_celsius* funkciju koja prihvata temperaturu u Farenhajtima i vraća temperaturu u Celzijusima:

```py
def fahrenheit_to_celsius(f: float) -> float:
    if f < FahrenheitError.min_f or f > FahrenheitError.max_f:
        raise FahrenheitError(f)

    return (f - 32) * 5 / 9
```

Funkcija `fahrenheit_to_celsius` podiže `FahrenheitError` izuzetak ako ulazna temperatura nije u važećem opsegu. U suprotnom, konvertuje temperaturu iz Farenhajta u Celzijuse.

**Napravite glavni program**
Sledeći glavni program koristi *fahrenheit_to_celsius* funkciju i *FahrenheitError* prilagođenu klasu izuzetaka:

```py
if __name__ == '__main__':
    f = input('Enter a temperature in Fahrenheit:')
    try:
        f = float(f)
    except ValueError as ex:
        print(ex)
    else:
        try:
            c = fahrenheit_to_celsius(float(f))
        except FahrenheitError as ex:
            print(ex)
        else:
            print(f'{f} Fahrenheit = {c:.4f} Celsius')
```

Kako to funkcioniše.

- Prvo, zamolite korisnike da unesu temperaturu u Farenhajtima.

    ```py
    f = input('Enter a temperature in Fahrenheit:')
    ```

- Drugo, konvertujte ulaznu vrednost u broj sa pokretnim zarezom . Ako float() ne može da konvertujete ulaznu vrednost, program će izazvati `ValueError` izuzetak. U ovom slučaju, prikazuje poruku o grešci iz `ValueError` izuzetka:

    ```py
    try:
        f = float(f)
        # ...
    except ValueError as ex:
        print(ex)
    ```

- Treće, konvertujte temperaturu u Celzijuse pozivanjem *fahrenheit_to_celsius* funkcije i ispišite poruku o grešci ako ulazna vrednost nije validna Fahrenheit vrednost:

    ```py
    try:
        c = fahrenheit_to_celsius(float(f))
    except FahrenheitError as ex:
        print(ex)
    else:
        print(f'{f} Fahrenheit = {c:.4f} Celsius')
    ```

Spojite sve zajedno

```py
class FahrenheitError(Exception):
    min_f = 32
    max_f = 212

    def __init__(self, f, *args):
        super().__init__(args)
        self.f = f

    def __str__(self):
        return f'The {self.f} is not in a valid range {self.min_f, self.max_f}'

def fahrenheit_to_celsius(f: float) -> float:
    if f < FahrenheitError.min_f or f > FahrenheitError.max_f:
        raise FahrenheitError(f)

    return (f - 32) * 5 / 9


if __name__ == '__main__':
    f = input('Enter a temperature in Fahrenheit:')
    try:
        f = float(f)
    except ValueError as ex:
        print(ex)
    else:
        try:
            c = fahrenheit_to_celsius(float(f))
        except FahrenheitError as ex:
            print(ex)
        else:
            print(f'{f} Fahrenheit = {c:.4f} Celsius')
```

### Rezime prilagođeni izuzetak

- Podklasirajte `Exception` klasu ili jednu od njenih podklasa da biste definisali prilagođenu klasu izuzetaka.
- Napravite hijerarhiju klasa izuzetaka kako biste klase izuzetaka učinili organizovanijim i hvatali izuzetke na više nivoa.
