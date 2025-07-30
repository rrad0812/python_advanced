
# Izuzeci

**Rezime**: u ovom tutorijalu ćete naučiti o izuzecima u Pajtonu i kako da ih graciozno obradite u programima.

## Uvod u izuzetke

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

### Primer obrade izuzetaka

Sledeći primer definiše divisionfunkciju koja vraća rezultat deljenja a sa b:

```py
def division(a, b):
    return a / b

c = division(10, 0)
```

Izlaz
```shell
ZeroDivisionError: division by zero
```

U ovom primeru, ako je b nula, ZeroDivisionErrordoći će do izuzetka. Da biste obradili ZeroDivisionErrorizuzetak, koristite try...exceptsledeću izjavu:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

{'success': False, 'message': 'b cannot be zero', 'result': None}
Jezik koda:  PHP  ( php )

U ovom primeru, funkcija vraća rečnik koji ima tri elementa:

    successje bulova vrednost koja pokazuje da li je operacija uspešna ili ne.
    messageoznačava poruku o grešci ili uspehu.
    resultčuva rezultat od a / b ili Noneako je b nula.

Sledeće prikazuje izlaz ako se to ZeroDivisionErrordogodi:

{'success': False, 'message': 'b cannot be zero', 'result': None}
Kodni jezik:  običan tekst  ( plaintext )

Sada, ako ne uhvatite ZeroDivisionErrorizuzetak već opštiji izuzetak poput Exceptionklase:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

{'success': False, 'message': 'b cannot be zero', 'result': None}
Jezik koda:  PHP  ( php )

Program radi kao i pre jer try...excepttakođe hvata tip izuzetka koji je podklasa klase Exception.

Međutim, ako funkciji prosledite dva niza umesto dva broja division(), dobićete istu poruku kao da ZeroDivisionErrorse izuzetak dogodio:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

{'success': False, 'message': 'b cannot be zero', 'result': None}
Kodni jezik:  običan tekst  ( plaintext )

U ovom primeru, izuzetak nije ZeroDivisionErrorveć TypeError. Međutim, kod ga i dalje obrađuje kao ZeroDivisionErrorizuzetak.

Stoga bi uvek trebalo da obrađujete izuzetke od najspecifičnijeg do najmanje specifičnog. Na primer:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

{'success': False, 'message': 'Both a & b must be numbers', 'result': None}
Jezik koda:  PHP  ( php )

U ovom primeru, hvatamo TypeError, ZeroDivisionError, i Exceptionredosledom kojim se pojavljuju u try...exceptizjavi.

Ako je kod koji obrađuje različite izuzetke isti, možete grupisati sve izuzetke u jedan na sledeći način:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

{'success': False, 'message': 'division by zero', 'result': None}
Kodni jezik:  običan tekst  ( plaintext )

Rezime

    Pajton izuzeci su objekti klasa, koje su podklase klase BaseException.
    Obradi izuzetak od najspecifičnijeg do najmanje specifičnog.


Obrada izuzetaka u Pajtonu

Rezime : u ovom tutorijalu ćete naučiti kako da pravilno obrađujete izuzetke u Pajtonu koristeći trynaredbu.
Uvod u obradu izuzetaka u Pajtonu

Za obradu izuzetaka koristite trynaredbu. tryIzjava ima sledeće klauzule:

try:
    # code that you want to protect from exceptions
except <ExceptionType> as ex:
    # code that handle the exception
finally:
    # code that always execute whether the exception occurred or not
else:
    # code that excutes if try execute normally (an except clause must be present)
Kodni jezik:  Pajton  ( python )

Hajde da trydetaljnije ispitamo izjavu.
pokušaj

U tryklauzuli smeštate kod koji štiti od jednog ili više potencijalnih izuzetaka. Dobra je praksa da kod bude što kraći. Često ćete imati jednu izjavu u tryklauzuli.

Klauzula tryse pojavljuje tačno jednom u tryizjavi.
osim

U exceptklauzuli se postavlja kod koji obrađuje određeni tip izuzetka. tryIzjava može imati nula ili više exceptklauzula. Tipično, svaka exceptklauzula obrađuje različite tipove izuzetaka na specifične načine.

U exceptklauzuli, `the` as exje opciono. I `the` <ExceptionType>je takođe opciono. Međutim, ako izostavite `the` <ExceptionType> as ex, imaćete golu obrađivačku jedinicu izuzetaka.

Prilikom navođenja tipova izuzetaka u exceptklauzulama, postavljate najspecifičnije do najmanje specifičnih izuzetaka od vrha nadole.

Ako imate istu logiku koja obrađuje različite tipove izuzetaka, možete ih grupisati u jednu exceptklauzulu. Na primer:

try:
...
except <ExceptionType1> as ex:
    log(ex)
except <ExceptionType2> as ex:
    log(ex)
Kodni jezik:  Pajton  ( python )

Postani

try:
...
except (<ExceptionType1>, <ExceptionType2>) as ex:
    log(ex)
Kodni jezik:  Pajton  ( python )

Važno je napomenuti da exceptje redosled važan jer će Pajton pokrenuti prvu exceptklauzulu čiji tip izuzetka odgovara nastalom izuzetku.
konačno

Klauzula finallyse može pojaviti nula ili jednom u tryizjavi. finallyKlauzula se uvek izvršava bez obzira da li se dogodio izuzetak ili ne.
inače

Klauzula elsese takođe pojavljuje nula ili jednom. I elseklauzula je validna samo ako naredba try ima bar jednu exceptklauzulu.

Obično se postavlja kod koji se izvršava ako tryse klauzula normalno završi.
Primer obrade izuzetaka u Pajtonu

Sledeća definicija definiše funkciju koja vraća rezultat broja pomoću drugog broja:

def divide(a, b):
    return a / b
Kodni jezik:  Pajton  ( python )

Ako drugom argumentu dodamo 0, dobićemo ZeroDivisionErrorizuzetak:

divide(10, 0)
Kodni jezik:  Pajton  ( python )

Greška:

ZeroDivisionError: division by zero
Kodni jezik:  Pajton  ( python )

Da biste to popravili, možete obraditi ZeroDivisionErrorizuzetak u divide()funkciji na sledeći način:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        return None
Kodni jezik:  Pajton  ( python )

U ovom primeru, divide()funkcija vraća vrednost Noneako ZeroDivisionErrorse desi:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        return None
Kodni jezik:  Pajton  ( python )

Kada koristite divide()funkciju, potrebno je da proverite da li je rezultat None:

result = divide(10, 0)

if result is not None:
    print('result:', result)
else:
    print('Invalid inputs')
Kodni jezik:  Pajton  ( python )

Ali vraćanje Nonemožda nije najbolje jer drugi mogu slučajno proceniti rezultat u ifizjavi ovako:

result = divide(10, 0)

if result:
    print('result:', result)
else:
    print('Invalid inputs')
Kodni jezik:  Pajton  ( python )

U ovom slučaju, radi. Međutim, neće raditi ako je prvi argument nula. Na primer:

result = divide(0, 10)

if result:
    print('result:', result)
else:
    print('Invalid inputs')
Kodni jezik:  Pajton  ( python )

Bolji pristup je da se pozivaocu pokrene izuzetak ako ZeroDivisionErrorse izuzetak dogodio. Na primer:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('The second argument (b) must not be zero')
Kodni jezik:  Pajton  ( python )

U ovom primeru, divide()funkcija će izdati grešku ako bje nula. Da biste koristili divide()funkciju, potrebno je da uhvatite ValueErrorizuzetak:

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
Kodni jezik:  Pajton  ( python )

Izlaz:

The second argument (b) must not be zero
Kodni jezik:  Pajton  ( python )

NoneDobra je praksa da se u posebnim slučajevima pokrene izuzetak umesto vraćanja .
Osim primera porudžbine

Kada uhvatite izuzetak u klauzuli except, potrebno je da postavite izuzetke od najspecifičnijeg do najmanje specifičnog u smislu hijerarhije izuzetaka.

Sledeće prikazuje tri klase izuzetaka: Exception, LookupErrori IndexError:

Ako uhvatite izuzetak, potrebno je da ih postavite sledećim redosledom: IndexError, LookupErorr i Exception.

Na primer, sledeći kod definiše listu od tri niza znakova i pokušava da pristupi četvrtom elementu:

colors = ['red', 'green', 'blue']
try:
    print(colors[3])
except IndexError as e:
    print(type(e), 'Index error')
except LookupError as e:
    print(type(e), 'Lookup error')
Kodni jezik:  Pajton  ( python )

Izdaje sledeću grešku:

<class 'IndexError'> Index error
Kodni jezik:  Pajton  ( python )

Pristup colors[3]izaziva IndexErrorizuzetak. Međutim, ako zamenite exceptklauzule i uhvatite LookupErrorprvu i IndexErrordrugu ovako:

colors = ['red', 'green', 'blue']
try:
    print(colors[3])
except LookupError as e:
    print(type(e), 'Lookup error')
except IndexError as e:
    print(type(e), 'Index error')
Kodni jezik:  Pajton  ( python )

Izlaz:

<class 'IndexError'> Lookup error
Kodni jezik:  Pajton  ( python )

Izuzetak je i dalje, IndexErrorali sledeća poruka je obmanjujuća.
Rukovaoci

Kada želite da uhvatite bilo koji izuzetak, možete koristiti obrađivače izuzetaka. Obradač izuzetaka ne određuje tip izuzetka:

try:
    ...
except:
    ...
Kodni jezik:  Pajton  ( python )

To je ekvivalentno sledećem:

try:
    ...
except BaseException:

    ...
Kodni jezik:  Pajton  ( python )

Obrada izuzetaka bez dodatnih funkcija će hvatati sve izuzetke, uključujući izuzetke SystemExit i KeyboardInterupt.

Samo jedan izuzetak će otežati prekidanje programa pomoću Control-C i prikrivanje drugih programa.

Ako želite da uhvatite sve izuzetke koji signaliziraju greške u programu, možete umesto toga koristiti `except Exception`:

try:
    ...
except Exception:
    ...
Kodni jezik:  Pajton  ( python )

U praksi, trebalo bi da izbegavate korišćenje obrađivača izuzetaka. Ako ne znate izuzetke koje treba da hvatate, samo pustite da se izuzetak dogodi, a zatim izmenite kod da bi obrađivao te izuzetke.

Da biste dobili informacije o izuzetku iz samog obrađivača izuzetaka, koristite exc_info()funkciju iz sysmodula.

Funkcija sys.exc_info()vraća torku koja se sastoji od tri vrednosti:

    typeje tip izuzetka koji se dogodio. To je podklasa od BaseException.
    valueje instanca tipa izuzetka.
    tracebackje objekat koji inkapsulira stek poziva na mestu gde se izuzetak prvobitno dogodio.

Sledeći primer koristi sys.exc_info()funkciju za ispitivanje izuzetka kada je string podeljen brojem:

import sys

try:
    '20' / 2
except:
    exc_info = sys.exc_info()
    print(exc_info)
Kodni jezik:  Pajton  ( python )

Izlaz:

(<class 'TypeError'>, TypeError("unsupported operand type(s) for /: 'str' and 'int'"), <traceback object at 0x000001F19F42E700>)
Kodni jezik:  Pajton  ( python )

Izlaz pokazuje da kod u klauzuli try izaziva TypeErrorizuzetak. Stoga možete izmeniti kod da bi ga posebno obrađivao na sledeći način:

try:
    '20' / 2
except TypeError as e:
    print(e)
Kodni jezik:  Pajton  ( python )

Izlaz:

unsupported operand type(s) for /: 'str' and 'int'
Kodni jezik:  Pajton  ( python )

Rezime

    Koristite tryizjavu za obradu izuzetka.
    U klauzulu stavite samo minimalni kod koji želite da zaštitite od potencijalnih izuzetaka try.
    Obrađujte izuzetke od najspecifičnijeg do najmanje specifičnog u smislu tipova izuzetaka. Redosled exceptklauzula je važan.
    Funkcija finally se uvek izvršava bez obzira da li su se izuzeci dogodili ili ne.
    Klauzula elsese izvršava samo kada tryse normalno završi. elseKlauzula je validna samo ako tryiskaz ima barem jednu exceptklauzulu.
    Izbegavajte korišćenje obrađivača izuzetaka.


Pajton podiže izuzetak

Rezime : u ovom tutorijalu ćete naučiti kako da podignete izuzetke koristeći Pajton raisenaredbu.
Uvod u Pajtonovu naredbu za

Da biste podigli izuzetak , koristite sledeću raiseizjavu:

raise ExceptionType()
Kodni jezik:  Pajton  ( python )

Mora ExceptionType()biti podklasa klase BaseException. Tipično, to je podklasa klase Exception. Imajte na umu da ExceptionTypene mora biti direktno nasleđen od Exceptionklase. Može indirektno nasleđivati od klase koja je podklasa klase Exception.

Klasa BaseExceptionima __init__metodu koja prihvata *argsargument. To znači da možete proslediti bilo koji broj argumenata objektu izuzetka prilikom pokretanja izuzetka.

Sledeći primer koristi raisenaredbu za pokretanje ValueErrorizuzetka. Metodi se prosleđuju tri argumenta ValueError __init__:

try:
    raise ValueError('The value error exception', 'x', 'y')
except ValueError as ex:
    print(ex.args)
Kodni jezik:  Pajton  ( python )

Izlaz:

('The value error exception', 'x', 'y')
Kodni jezik:  običan tekst  ( plaintext )

Ponovo pokreni trenutni izuzetak

Ponekad želite da evidentirate izuzetak i ponovo pokrenete isti izuzetak. U ovom slučaju, možete koristiti raisenaredbu bez navođenja objekta izuzetka.

Na primer, sledeće definiše division()funkciju koja vraća deljenje dva broja:

def division(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        print('Logging exception:', str(ex))
        raise
Kodni jezik:  Pajton  ( python )

Ako drugom argumentu funkcije prosledite nulu division(), ZeroDivisionErrordoći će do izuzetka. Međutim, umesto obrade izuzetka, možete ga evidentirati i ponovo ga podići.

Imajte na umu da ne morate da navodite objekat izuzetka u raisenaredbi. U ovom slučaju, Pajton zna da raiseće naredba podići trenutni izuzetak koji je uhvaćen klauzulom except.

Sledeći kod izaziva ZeroDivisionErrorizuzetak:

division(1, 0)
Kodni jezik:  Pajton  ( python )

I videćete i poruku o evidentiranju i izuzetak u izlazu:

Logging exception: division by zero
Traceback (most recent call last):
  File "c:/pythontutorial/app.py", line 9, in <module>
    division(1, 0)
  File "C:/pythontutorial/app.py", line 3, in division
    return a / b
ZeroDivisionError: division by zero
Kodni jezik:  običan tekst  ( plaintext )

Pokreni još jedan izuzetak tokom obrade izuzetka

Prilikom obrade izuzetka, možda ćete želeti da pokrenete drugi izuzetak. Na primer:

def division(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not zero')
Kodni jezik:  Pajton  ( python )

U division()funkciji, podižemo ValueErrorizuzetak ako ZeroDivisionErrorse dogodi .

Ako pokrenete sledeći kod, dobićete detalje traga steka:

division(1, 0)
Kodni jezik:  Pajton  ( python )

Izlaz:

Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 3, in division
    return a / b
ZeroDivisionError: division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 8, in <module>
    division(1, 0)
  File "C:/pythontutorial/app.py", line 5, in division
    raise ValueError('b must not zero')
ValueError: b must not zero
Kodni jezik:  običan tekst  ( plaintext )

Prvo, ZeroDivisionErrorizuzetak se javlja:

Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 3, in division
    return a / b
ZeroDivisionError: division by zero
Kodni jezik:  običan tekst  ( plaintext )

Drugo, tokom obrade ZeroDivisionErrorizuzetka, ValueErrorizuzetak se javlja:

Traceback (most recent call last):
  File "C:/pythontutorial/app.py", line 8, in <module>
    division(1, 0)
  File "C:/pythontutorial/app.py", line 5, in division
    raise ValueError('b must not zero')
ValueError: b must not zero
Kodni jezik:  običan tekst  ( plaintext )

Rezime

    Koristite Pajton raisenaredbu da biste podigli izuzetak.
    Prilikom obrade izuzetka, možete pokrenuti isti ili drugi izuzetak.


Pajton podiže iz

Rezime : u ovom tutorijalu ćete naučiti kako da koristite Pajtonovu naredbu raise from da biste podigli izuzetak sa dodatnim informacijama.
Uvod u Pajtonovu naredbu za povećanje

Izjava raise fromima sledeću sintaksu:

raise <ExceptionType> from <cause>
Kodni jezik:  Pajton  ( python )

Tehnički, to je ekvivalentno sledećem:

ex = ExceptionType
ex.__cause__ = cause
raise ex
Kodni jezik:  Pajton  ( python )

Podrazumevano, __cause__atribut na objektima izuzetakaNone je uvek inicijalizovan na .
Primer povećanja vrednosti iz naredbe u Pajtonu

Sledeća divide()funkcija deli jedan broj drugim i vraća rezultat deljenja:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero')
Kodni jezik:  Pajton  ( python )

Funkcija divide()ima obrađivač izuzetaka koji hvata ZeroDivisionErrorizuzetak. Unutar obrađivača, generišemo novi ValueErrorizuzetak.

Ako drugom argumentu funkcije dodamo nulu divide():

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero') from ex


divide(10, 0)
Kodni jezik:  Pajton  ( python )

dobićete sledeći trag steka:

Traceback (most recent call last):
  File "c:/python/app.py", line 3, in divide
    return a / b
ZeroDivisionError: division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "c:/python/app.py", line 8, in <module>
    divide(10, 0)
  File "c:/python/app.py", line 5, in divide
    raise ValueError('b must not be zero')
ValueError: b must not be zero
Kodni jezik:  Pajton  ( python )

Poruka o uvozu je:

During handling of the above exception, another exception occurred:
Kodni jezik:  Pajton  ( python )

ZeroDivisionErrorTo znači da se izuzetak dogodio dok ste obrađivali ValueErrorizuzetak.

Da biste naložili Pajtonu da želite da izmenite i prosledite ZeroDivisionErrorizuzetak, možete koristiti raise fromsledeću izjavu:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as ex:
        raise ValueError('b must not be zero') from ex

divide(10, 0)
Kodni jezik:  Pajton  ( python )

Kada pokrenete kod, dobićete sledeći trag steka:

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
Kodni jezik:  Pajton  ( python )

Sada dobijate ValueErrorizuzetak sa uzrokom dodatim atributu __cause__ objekta izuzetka.

Sledeći kod menja gornji kod da bi se prikazao __cause__atribut ValueErrorizuzetka:

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
Kodni jezik:  Pajton  ( python )

Izlaz:

cause: division by zero
exception: b must not be zero
Kodni jezik:  Pajton  ( python )

Pajton podiže izuzetak iz None

Ako uzrok izuzetka nije važan, možete ga izostaviti korišćenjem sledeće raise exception from Noneizjave:

raise <ExceptionType> from None
Kodni jezik:  Pajton  ( python )

Na primer, možete sakriti uzrok ValueErrorizuzetka u divide()funkciji na sledeći način:

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
Kodni jezik:  Pajton  ( python )

Probaj

Izlaz:

cause: None
exception: b must not be zero
Kodni jezik:  Pajton  ( python )

Sada, __cause__je None. Takođe, divide()funkcija podiže ValueErrorizuzetak bez ikakvih dodatnih informacija.

Ako uklonite tryizjavu u kodu koja poziva divide()funkciju:

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        raise ValueError('b must not be zero') from None


divide(10, 0)
Kodni jezik:  Pajton  ( python )

dobićete sledeći trag steka:

Traceback (most recent call last):
  File "c:/python/app.py", line 8, in <module>
    divide(10, 0)
  File "c:/python/app.py", line 5, in divide
    raise ValueError('b must not be zero') from None
ValueError: b must not be zero
Kodni jezik:  Pajton  ( python )

Rezime

    Koristite Pajton raise fromnaredbu da biste izmenili i prosledili postojeći izuzetak.
    Koristite raise exception from Noneizjavu da sakrijete uzrok izuzetka.


Prilagođeni izuzetak u Pajtonu

Rezime : u ovom tutorijalu ćete naučiti kako da definišete prilagođene klase izuzetaka u Pajtonu.
Uvod u prilagođeni izuzetak u Pajtonu

Da biste kreirali prilagođenu klasu izuzetaka , definišete klasu koja nasleđuje ugrađenu Exceptionklasu ili jednu od njenih podklasa, kao što je ValueErrorclass:
Прилагођени изузетак у Пајтону

Sledeći primer definiše CustomExceptionklasu koja nasleđuje od Exceptionklase:

class CustomException(Exception):
    """ my custom exception class """
Kodni jezik:  Pajton  ( python )

Imajte na umu da CustomExceptionklasa ima dokumentacioni string koji se ponaša kao iskaz. Stoga, ne morate dodavati passiskaz da bi sintaksa bila validna.

Da biste podigli izuzetak CustomException, koristite raisenaredbu . Na primer, sledeći kod koristi raisenaredbu za podizanje izuzetka CustomException:

class CustomException(Exception):
    """ my custom exception class """


try:
    raise CustomException('This is my custom exception')
except CustomException as ex:
    print(ex)
Kodni jezik:  Pajton  ( python )

Izlaz:

This is my custom exception
Kodni jezik:  Pajton  ( python )

Kao i standardne klase izuzetaka, prilagođeni izuzeci su takođe klase. Stoga, možete dodati funkcionalnosti prilagođenim klasama izuzetaka kao što su:

    Dodavanje atributa i svojstava .
    Dodavanje metoda , npr. evidentiranje izuzetka, formatiranje izlaza itd.
    Prepisivanje metoda ` __str__and`__repr__
    I raditi bilo šta drugo što možete da radite sa redovnom nastavom.

U praksi, želećete da prilagođene izuzetke organizujete kreiranjem prilagođene hijerarhije izuzetaka. Prilagođena hijerarhija izuzetaka vam omogućava da hvatate izuzetke na više nivoa, kao što su standardne klase izuzetaka.
Primer prilagođenog izuzetka u Pajtonu

Pretpostavimo da treba da razvijete program koji pretvara temperaturu iz Farenhajta u Celzijuse.

Minimalna i maksimalna vrednost temperature u Farenhajtima su 32 i 212. Ako korisnici unesu vrednost koja nije u ovom opsegu, želite da pokrenete prilagođeni izuzetak, npr. FahrenheitError.
Definišite prilagođenu klasu izuzetka

Sledeće definiše FahrenheitErrorklasu izuzetka:

class FahrenheitError(Exception):
    min_f = 32
    max_f = 212

    def __init__(self, f, *args):
        super().__init__(args)
        self.f = f

    def __str__(self):
        return f'The {self.f} is not in a valid range {self.min_f, self.max_f}'
Kodni jezik:  Pajton  ( python )

Kako to funkcioniše.

    Prvo, definišite klasu FahrenheitError koja nasleđuje od Exceptionklase.
    Drugo, dodajte dva atributa klase min_fkoji max_fpredstavljaju minimalne i maksimalne vrednosti Farenhajta.
    Treće, definišite __init__metod koji prihvata vrednost Farenhajta ( f) i određeni broj argumenata pozicije ( *args). U __init__metodi, pozovite __init__metod osnovne klase. Takođe, dodelite fargument atributu finstance.
    Konačno, prepišite __str__metodu da biste vratili prilagođenu reprezentaciju instance klase u obliku stringa.

Definišite funkciju fahrenheit_to_celsius

Sledeća definicija definiše fahrenheit_to_celsiusfunkciju koja prihvata temperaturu u Farenhajtima i vraća temperaturu u Celzijusima:

def fahrenheit_to_celsius(f: float) -> float:
    if f < FahrenheitError.min_f or f > FahrenheitError.max_f:
        raise FahrenheitError(f)

    return (f - 32) * 5 / 9
Kodni jezik:  Pajton  ( python )

Funkcija fahrenheit_to_celsiuspodiže FahrenheitErrorizuzetak ako ulazna temperatura nije u važećem opsegu. U suprotnom, konvertuje temperaturu iz Farenhajta u Celzijuse.
Napravite glavni program

Sledeći glavni program koristi fahrenheit_to_celsiusfunkciju i FahrenheitErrorprilagođenu klasu izuzetaka:

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
Kodni jezik:  Pajton  ( python )

Kako to funkcioniše.

Prvo, zamolite korisnike da unesu temperaturu u Farenhajtima.

f = input('Enter a temperature in Fahrenheit:')
Kodni jezik:  Pajton  ( python )

Drugo, konvertujte ulaznu vrednost u broj sa pokretnim zarezom . Ako float()ne možete da konvertujete ulaznu vrednost, program će izazvati ValueErrorizuzetak. U ovom slučaju, prikazuje poruku o grešci iz ValueErrorizuzetka:

try:
    f = float(f)
    # ...
except ValueError as ex:
    print(ex)
Kodni jezik:  Pajton  ( python )

Treće, konvertujte temperaturu u Celzijuse pozivanjem fahrenheit_to_celsiusfunkcije i ispišite poruku o grešci ako ulazna vrednost nije validna Fahrenheitvrednost:

try:
    c = fahrenheit_to_celsius(float(f))
except FahrenheitError as ex:
    print(ex)
else:
    print(f'{f} Fahrenheit = {c:.4f} Celsius')
Kodni jezik:  Pajton  ( python )

Spojite sve zajedno

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
Kodni jezik:  Pajton  ( python )

Rezime

    Podklasirajte Exceptionklasu ili jednu od njenih podklasa da biste definisali prilagođenu klasu izuzetaka.
    Napravite hijerarhiju klasa izuzetaka kako biste klase izuzetaka učinili organizovanijim i hvatali izuzetke na više nivoa.
