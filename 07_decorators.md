
# Dekoratori

**Rezime**: u ovom tutorijalu ćete naučiti o dekoratorima u Pajtonu i kako da razvijete sopstvene dekoratore.

## Šta je dekorator

Dekorator je funkcija koja uzima drugu funkciju kao argument i proširuje njeno ponašanje bez eksplicitne promene originalne funkcije.

### Jednostavan primer za razumevanje koncepta

Sledeće definiše *net_price* funkciju:

```py
def net_price(price, tax):
    """ calculate the net price from price and tax
    Arguments:
        price: the selling price
        tax: value added tax or sale tax
    Return
        the net price
    """
    return price * (1 + tax)
```

Funkcija *net_price* izračunava neto cenu iz prodajne cene i poreza. Vraća vrednost *net_price* kao broj.

Pretpostavimo da treba da formatirate neto cenu koristeći valutu USD. Na primer, 100 postaje $100. Da biste to uradili, možete koristiti dekorator.

Po definiciji, dekorator je funkcija koja uzima funkciju kao argument:

```py
def currency(fn):
    pass
```

I vraća drugu funkciju:

```py
def currency(fn):
    def wrapper(*args, **kwargs):
        fn(*args, **kwargs)

    return wrapper
```

Funkcija *currency* vraća *wrapper* funkciju. *wrapper* funkcija ima parametre `*args` i `**kwargs`.

Ovi parametri vam omogućavaju da pozovete bilo koju *fn* funkciju sa bilo kojom kombinacijom pozicionih argumenata i argumenata ključnih reči.

U ovom primeru, wrapper funkcija u suštini direktno izvršava *fn* funkciju i ne menja  ponašanje funkcije *fn*.

U *wrapper* funkciji možete pozvati *fn* funkciju, dobiti njen rezultat i formatirati rezultat kao string valute:

```py
def currency(fn):
    def wrapper(*args, **kwargs):
        result = fn(*args, **kwargs)
        return f'${result}'
    return wrapper
```

Funkcija *currency* je dekorator.

Prihvata bilo koju funkciju koja vraća broj i formatira taj broj kao string valute.

Da biste koristili *currency* dekorator, potrebno je da *net_price* prosledite funkciju kako biste dobili novu funkciju i izvršili novu funkciju kao da je originalna funkcija. Na primer:

```py
def currency(fn):
    def wrapper(*args, **kwargs):
        result = fn(*args, **kwargs)
        return f'${result}'
    return wrapper

net_price = currency(net_price)
print(net_price(100, 0.05))
```

Izlaz:

```shell
$105.0
```

### Definicija dekoratora u Pajtonu

Generalno, dekorater je:

- Funkcija koja uzima drugu funkciju (originalnu funkciju) kao argument i vraća drugu funkciju ( ili zatvaranje )
- Zatvaranje obično prihvata bilo koju kombinaciju pozicionih argumenata i argumenata samo za ključne reči.
- Funkcija zatvaranja poziva originalnu funkciju koristeći argumente prosleđene zatvaranju i vraća rezultat funkcije.

Unutrašnja funkcija je zatvaranje jer referencira *fn* argument iz svog obuhvatajućeg opsega ili funkcije dekoratora.

### Simbol @

U prethodnom primeru, *currency* je dekorator. Funkciju *net_price* možete dekorisati  koristeći sledeću sintaksu:

```py
net_price = currency(net_price)
```

Generalno, ako *decorate* je funkcija dekoratora i želite da dekorišete drugu funkciju *fn*, možete koristiti ovu sintaksu:

```py
fn = decorate(fn)
```

Da bi bilo praktičnije, Pajton pruža kraći način ovako:

```py
@decorate
def fn():
    pass
```

Na primer, umesto korišćenja sledeće sintakse:

```py
net_price = currency(net_price)
```

... možete dekorisati *net_price* funkciju koristeći *@currency* dekorator na sledeći način:

```py
@currency
def net_price(price, tax):
    """ calculate the net price from price and tax
    Arguments:
        price: the selling price
        tax: value added tax or sale tax
    Return
        the net price
    """
    return price * (1 + tax)
```

Sada sve to spojimo.

```py
def currency(fn):
    def wrapper(*args, **kwargs):
        result = fn(*args, **kwargs)
        return f'${result}'
    return wrapper


@currency
def net_price(price, tax):
    """ calculate the net price from price and tax
    Arguments:
        price: the selling price
        tax: value added tax or sale tax
    Return
        the net price
    """
    return price * (1 + tax)

print(net_price(100, 0.05))
```

### Introspekcija dekorisanih funkcija

Kada dekorišete funkciju:

```py
@decorate
def fn(*args,**kwargs):
    pass
```

To je ekvivalentno:

```py
fn = decorate(fn)
```

Funkcija decorate vraća novu funkciju, koja je funkcija omotač ili zatvaranje.

Ako koristite ugrađenu help funkciju da biste prikazali dokumentaciju nove funkcije, nećete videti dokumentaciju originalne funkcije. Na primer:

```py
help(net_price)
```

Izlaz:

```shell
wrapper(*args, **kwargs)
None
```

Takođe, ako proverite ime nove funkcije, Pajton će vratiti ime unutrašnje funkcije koju je vratio dekorator:

```py
print(net_price.__name__)
```

Izlaz:

```shell
wrapper
```

Dakle, kada dekorišete funkciju, izgubićete originalni potpis funkcije i dokumentaciju.

Da biste ovo popravili, možete koristiti `wraps` funkciju iz `functools` standardnog modula. U stvari, `wraps` funkcija je takođe dekorator.

Sledeće je prikazano kako se koristi `wraps` dekorator:

```py
from functools import wraps

def currency(fn):
    @wraps(fn)                          # <<--- 
    def wrapper(*args, **kwargs):
        result = fn(*args, **kwargs)
        return f'${result}'
    return wrapper

@currency
def net_price(price, tax):
    """ calculate the net price from price and tax
    Arguments:
        price: the selling price
        tax: value added tax or sale tax
    Return
        the net price
    """
    return price * (1 + tax)

help(net_price)
print(net_price.__name__)
```

Izlaz:

```shell
net_price(price, tax)
    calculate the net price from price and tax
    Arguments:
        price: the selling price
        tax: value added tax or sale tax
    Return
        the net price

net_price
```

Rezime dekoratora

- Dekorator je funkcija koja menja ponašanje druge funkcije bez eksplicitnog modifikovanja iste.
- Koristite `@` simbol za dekorisanje funkcije.
- Koristite `wraps` funkciju iz `functools` ugrađenog modula da biste zadržali dokumentaciju i naziv originalne funkcije.

## Dekoratori sa argumentima

**Rezime**: u ovom tutorijalu ćete naučiti kako da definišete dekoratore u Pajtonu sa argumentima koristeći fabrički dekorator.

### Uvod u dekorator sa argumentima

Pretpostavimo da imate funkciju koja se zove *say* i da ispisuje poruku:

```py
def say(message):
    ''' print the message 
    Arguments
        message: the message to show
    '''
    print(message)
```

i želite da izvršite say() funkciju 5 puta ponavljajući svaki put kada je pozovete. Na primer:

```py
say('Hi')
```

Trebalo bi da prikaže sledeću *Hi* poruku pet puta na sledeći način:

```shell
Hi
Hi
Hi
Hi
Hi
```

Da biste to uradili, možete koristiti običan dekorator:

```py
@repeat
def say(message):
    ''' print the message
    Arguments
        message: the message to show
    '''
    print(message)
```

A dekorator *repeat* možete definisati na sledeći način:

```py
def repeat(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        for _ in range(5):
            result = fn(*args, **kwargs)
        return result

    return wrapper    
```

Sledeći kod prikazuje kompletan kod:

```py
from functools import wraps

def repeat(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        for _ in range(5):
            result = fn(*args, **kwargs)
        return result

    return wrapper

@repeat
def say(message):
    ''' print the message
    Arguments
        message: the message to show
    '''
    print(message)

say('Hello')
```

Šta ako želite da izvršite say() funkciju deset puta više? U ovom slučaju, potrebno je da promenite čvrsto kodiranu vrednost 5 u *repeat* dekoratoru.

Međutim, ovo rešenje nije fleksibilno. Na primer, želite da koristite *repeat* dekorator da biste izvršili funkciju 5 puta, a zatim još 10 puta. *repeat* dekorator ne bi ispunio zahtev.

Da biste ovo popravili, potrebno je da promenite *repeat* dekorator tako da prihvata argument koji određuje koliko puta funkcija treba da se izvrši ovako:

```py
@repeat(5)
def say(message):
    ...
```

Da bi se definisao *repeat* dekorator, *repeat(5)* treba da vrati originalni dekorator.

```py
def repeat(times):
    # return the original "repeat" decorator
```

Nova *repeat* funkcija vraća dekorator. I često se naziva fabrika dekoratora.

Sledeća *repeat* funkcija vraća dekorator:

```py
def repeat(times):
    ''' call a function a number of times '''
    def decorate(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = fn(*args, **kwargs)
            return result
        return wrapper
    return decorate
```

U ovom kodu, *decorate* funkcija je dekorator. Ekvivalentna je originalnom *repeat* dekoratoru.

Imajte na umu da nova funkcija repeat nije dekorator. To je fabrika dekoratora koja vraća dekorator.

Ako sve to spojimo,

```py
from functools import wraps

def repeat(times):
    ''' call a function a number of times '''
    def decorate(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = fn(*args, **kwargs)
            return result
        return wrapper
    return decorate

@repeat(10)
def say(message):
    ''' print the message
    Arguments
        message: the message to show
    '''
    print(message)

say('Hello')
```

Izlaz:

```shell
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
```

### Rezime dekoratora sa argumentima

Koristite fabrički dekorator da biste vratili dekorator koji prihvata argumente.

## Klasa kao dekorator

**Rezime**: u ovom tutorijalu ćete naučiti o dekoratorima klasa u Pajtonu. Nakon tutorijala, znaćete kako da definišete klase kao dekoratore.

### Uvod u klasu kao dekorator

Do sada ste naučili kako da koristite funkcije za definisanje dekoratora.

Na primer, sledeća *star* funkcija ispisuje određeni broj * znakova pre i posle pozivanja dekorisane funkcije:

```py
def star(n):
    def decorate(fn):
        def wrapper(*args, **kwargs):
            print(n*'*')
            result = fn(*args, **kwargs)
            print(result)
            print(n*'*')
            return result
        return wrapper
    return decorate
```

*star* je fabrika dekoratora koja vraća dekorator. Prihvata argument koji određuje broj * znakova za prikazivanje.

Sledeće ilustruje kako se koristi *star* fabrika dekoratora:

```py
def star(n):
    def decorate(fn):
        def wrapper(*args, **kwargs):
            print(n*'*')
            result = fn(*args, **kwargs)
            print(result)
            print(n*'*')
            return result
        return wrapper
    return decorate

@star(5)
def add(a, b):
    return a + b

add(10, 20)
```

Izlaz:

```shell
*****
30
*****
```

Fabrika dekoratora *star()* prima argument i vraća pozivnu funkciju. Pozivna funkcija prima argument ( fn ), što je funkcija koja će biti dekorisana. Takođe, pozivna funkcija može pristupiti argumentu ( n ) koji je prosleđen fabrici dekoratora.

Instanca klase može biti pozana implementira __call__metodu. Stoga, metodu `__call__` možete napraviti kao dekorator.

Sledeći primer prepisuje *star* fabriku dekoratora koristeći klasu umesto toga:

```py
class Star:
    def __init__(self, n):
        self.n = n

    def __call__(self, fn):
        def wrapper(*args, **kwargs):
            print(self.n*'*')
            result = fn(*args, **kwargs)
            print(result)
            print(self.n*'*')
            return result
        return wrapper
```

I možete koristiti `Star` klasu kao dekorater ovako:

```py
@Star(5)
def add(a, b):
    return a + b
```

@Star(5) vraća instancu klase *Star*. Tu instanca moguće je pozvati, tako da možete uraditi nešto poput ovoga:

```py
add = Star(5)(add)
```

Dakle, možete koristiti pozivajuće klase za dekorisanje funkcija.

Spojite sve ovo zajedno:

```py
from functools import wraps

class Star:
    def __init__(self, n):
        self.n = n

    def __call__(self, fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            print(self.n * '*')
            result = fn(*args, **kwargs)
            print(result)
            print(self.n * '*')
            return result
        return wrapper

@Star(5)
def add(a, b):
    return a + b

add(10, 20)
```

Izlaz:

```shell
*****
30
*****
```

### Rezime klase kao dekoratora

- Koristite pozivajuće klase kao dekoratore implementacijom `__call__` metode.
- Prosledite argumente dekoratora metodi `__init__`.

## Monkey patching

**Rezime**: u ovom tutorijalu ćete naučiti koncept "monkey patching"-a u Pajtonu, kada ga koristiti i kako ga efikasno primeniti.

### Uvod u krpljenje koda

Majmunsko krpljenje je tehnika koja vam omogućava da izmenite ili proširite ponašanje postojećih *modula*, *klasa* ili *funkcija* tokom izvršavanja programa bez promene originalnog izvornog koda.

### Primena majmunskog krpljenja

Da biste primenili tehniku majmunskog krpljenja, pratite ove korake:

- Prvo, identifikujte cilj koji može biti *modul*, *klasa*, *metoda* ili *funkcija* koju želite da zakrpite.
- Drugo, kreirajte svoj peč tako što ćete napisati kod za dodavanje, modifikovanje ili zamenu postojeće logike.
- Treće, primenite zakrpu koristeći dodelu da biste je primenili na cilj. Zakrpa će prebrisati ili proširiti postojeće ponašanje.

Iako je majmunsko krpljenje moćan alat, trebalo bi da ga koristite pažljivo kako biste izbegli neočekivano ponašanje.

### Primer krpljenja majmuna

Pretpostavimo da imate *MyClass* klasu koja ima samo jednu `__init__()` metodu:

```py
class Robot:
    def __init__(self, name):
        self.name = name
```

Da biste proširili ponašanje klase *Robot* tokom izvršavanja bez promene *Robot* klase, možete koristiti tehniku "majmunskog krpljenja".

Slika koja vam je potrebna da biste proširili ponašanje koje omogućava robotskim instancama da govore. Evo koraka za to:

- Prvo, definišite funkciju koja se zove *add_speech* koja prihvata klasu kao parametar:

    ```py
    def add_speech(cls):
        cls.speak = lambda self, message: print(message)
        return cls
    ```

    Funkcija *add_speech()* dodaje *speak()* metod dodavanjem atributa *speak* klasi *cls*, dodeljivanjem *lambda izraza* i vraćanjem klase. *Lambda izraz* prihvata poruku i prikazuje je u konzoli.

- Drugo, zakrpite *Robot* klasu tako što ćete je proslediti *add_speech()* metodi:

    ```py
    Robot = add_speech(Robot)
    ```

    Nakon ove linije koda, *Robot* klasa će imati *speak()* metodu.

    Imajte na umu da funkciju *add_speech()* možete koristiti za krpljenje bilo koje klase, ne samo *Robot* klase.

- Treće, kreirajte novu instancu klase *Robot* i pozovite *speak()* metodu:

    ```py
    robot = Robot('Optimus Prime')
    robot.speak('Hi')
    ```

Spojite sve ovo zajedno:

```py
def add_speech(cls):
    cls.speak = lambda self, message: print(message)
    return cls

class Robot:
    def __init__(self, name):
        self.name = name

Robot = add_speech(Robot)

robot = Robot('Optimus Prime')
robot.speak('Hi')
```

Ako pokrenete program, videćete sledeći izlaz:

```shell
Hi
```

Pošto je ova linija koda dekorator:

```py
Robot = add_speech(Robot)
```

možete ga ukloniti i koristiti sintaksu dekoratora:

```py
@add_speech
class Robot:
    def __init__(self, name):
        self.name = name
```

Novi kod će izgledati ovako:

```py
def add_speech(cls):
    cls.speak = lambda self, message: print(message)
    return cls

@add_speech
class Robot:
    def __init__(self, name):
        self.name = name

robot = Robot('Optimus Prime')
robot.speak('Hi')
```

### Kada koristiti majmunsko krpljenje

U praksi, trebalo bi da koristite "monkey patching" samo kada je to neophodno jer može otežati razumevanje i debagovanje koda.

Na primer, ako koristite biblioteku treće strane i ona ima hitnu grešku za koju ne možete čekati zvanično objavljivanje, u tom slučaju možete koristiti "monkey patching" da biste primenili brze ispravke dok čekate odgovarajuće rešenje.

Drugi scenario je da želite da dodate funkcionalnost klasama koje ne kontrolišete i ne možete da koristite druge tehnike poput nasleđivanja ili kompozicije, u ovom slučaju je korisno "monkey patching" ( "majmunsko krpljenje" ).

U praksi, naći ćete "monkey patching" u biblioteci lažnih primeraka kao što je standardni `unittest.mockModul`. `unittest.mockModul` ima `patch()` metodu koja privremeno zamenjuje cilj lažnim objektom.

### Rezime Monkey patching

- Tehnika "majmunskog krpljenja“ u Pajtonu vam omogućava da dinamički menjate ili proširujete postojeći kod tokom izvršavanja bez promene originalnog koda.
- Koristite tehniku majmunskog krpljenja ako imate dobar razlog za to.
