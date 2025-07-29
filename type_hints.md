 
# Naznake tipova

**Rezime**: u ovom tutorijalu ćete naučiti o naznakama tipova u Pajtonu i kako da koristite alatku mypy za statičku proveru tipova.

## Uvod u naznake tipova

Neki programski jezici imaju statičko tipiziranje, kao što je C/C++. To znači da je potrebno unapred deklarisati tipove promenljivih, parametara i povratnih vrednosti funkcije. Unapred definisani tipovi omogućavaju kompajlerima da provere kod pre kompajliranja i pokretanja programa.

Pajton koristi dinamičko tipiziranje, u kojem promenljive, parametri i povratne vrednosti funkcije mogu biti bilo kog tipa. Takođe, tipovi promenljivih se mogu menjati dok se program izvršava. Generalno, dinamičko tpiziranje olakšava programiranje i uzrokuje neočekivane greške koje možete otkriti tek dok se program ne pokrene.

Pajtonove naznake tipova vam pružaju opciono statičko tipiziranje kako biste iskoristili najbolje od statičkog i dinamičkog tipiziranja. Sledeći primer definiše jednostavnu funkciju koja prihvata string i vraća drugi string:

```py
def say_hi(name):
    return f'Hi {name}'

greeting = say_hi('John')
print(greeting)
```

Evo sintakse za dodavanje naznaka tipa parametru i povratnoj vrednosti funkcije:

```py
parameter: type
-> type
```

Na primer, sledeće pokazuje kako se koriste naznake tipa za *name* parametar i povratnu vrednost funkcije *say_hi()*:

```py
def say_hi(name: str) -> str:
    return f'Hi {name}'

greeting = say_hi('John')
print(greeting)
```

Izlaz:
```py
Hi John
```

U ovoj novoj sintaksi, *name* parametar ima `str` naznaku tipa:

```py
name: str
```

I povratna vrednost funkcije *say_hi()* takođe ima naznaku tipa `str`:

```py
-> str
```

Pored `str` naznake tipa, možete koristiti i druge ugrađene tipove kao što su `int`, `float`, `bool`, i `bytes`za naznake tipova.

Važno je napomenuti da Pajton interpreter potpuno ignoriše naznake tipova. Ako funkciji *say_hi()* prosledite broj, program će se pokrenuti bez ikakvog upozorenja ili greške:

```py
def say_hi(name: str) -> str:
    return f'Hi {name}'

greeting = say_hi(123)
print(greeting)
```

Izlaz:
```py
Hi 123
```

Da biste proverili sintaksu za naznake tipova, potrebno je da koristite alatku za statičku proveru tipova.

### Korišćenje mypy alata za statičku proveru tipova

Pajton nema zvanični alat za statičku proveru tipova. Trenutno, najpopularniji alat treće strane je *Mypy*. Pošto je *Mypy* paket treće strane, potrebno ga je instalirati pomoću sledeće `pip` komande:

```shell
pip instal mypy
```

Jednom instaliran *mypy*, možete koristiti za proveru tipa pre pokretanja programa pomoću sledeće komande:

```shell
mypy app.py
```

Ovo će prikazati sledeću poruku:

```shell
app.py:5: error: Argument 1 to "say_hi" has incompatible type "int"; expected "str"
Found 1 error in 1 file (checked 1 source file)
```

Greška ukazuje da je argument *say_hi* `int` dok je očekivani tip `str`.

Ako vratite argument u string i *mypy* ponovo pokrenete, prikazaće se poruka o uspehu:

```shell
Success: no issues found in 1 source file
```

### Nagoveštavanje tipa i zaključivanje

Prilikom definisanja promenljive, možete dodati naznaku tipa kao što je ova:

```py
name: str = 'John'
```

Tip *name* promenljive je str. Ako promenljivoj dodelite vrednost koja nije string name, statička provera tipa će izdati grešku. Na primer:

```py
name: str = 'Hello'
name = 100
```

Greška:
```shell
app.py:2: error: Incompatible types in assignment (expression has type "int", variable has type "str")
Found 1 error in 1 file (checked 1 source file)
```

Dodavanje tipa promenljivoj nije potrebno jer statički proveravači tipova obično mogu da zaključe tip na osnovu vrednosti dodeljene promenljivoj.

U ovom primeru, vrednost imena je literalni string, tako da će statički proverivač tipa zaključiti tip promenljive imena kao `str`. Na primer:

```py
name = 'Hello'
name = 100
```

Izdaće istu grešku:
```shell
app.py:2: error: Incompatible types in assignment (expression has type "int", variable has type "str")
Found 1 error in 1 file (checked 1 source file)
```

### Dodavanje naznaka tipova za više tipova

Sledeća *add()* funkcija vraća zbir dva broja:

```py
def add(x, y):
    return x + y
```

Brojevi mogu biti celi ili brojevi sa pokretnim decimalom. Možete koristiti modul za podešavanje naznaka tipova `typing` za više tipova.

- Prvo, uvoz `Union` iz `typing` modula:

    ```py
    from typing import Union
    ```

- Drugo, koristite `Union` da biste kreirali tip unije koji uključuje `int` i `float`:

    ```py
    def add(x: Union[int, float], y: Union[int, float]) -> Union[int, float]:
        return x + y
    ```

Evo kompletnog izvornog koda:

```py
from typing import Union

def add(x: Union[int, float], y: Union[int, float]) -> Union[int, float]:
    return x + y
```

Počevši od Pajtona 3.10, možete koristiti sintaksu X | Y da biste kreirali tip unije, na primer:

```py
def add(x: int | float, y: int | float) -> int | float:
    return x + y
```

### Pseudonimi tipa

Pajton vam omogućava da dodelite alijas tipu i koristite taj alijas za naznake tipa. Na primer:

```py
from typing import Union

number = Union[int, float]

def add(x: number, y: number) -> number:
    return x + y
```

U ovom primeru, tipu `Union[int, float]` dodeljujemo  alias *Number* i koristimo *Number* alias u funkciji *add()*.

### Dodavanje naznake tipa za liste, rečnike i skup

Možete koristiti sledeće ugrađene tipove da biste postavili savete za tipove za listu , rečnik i skup :

    list
    dict
    set

Ako u promenljivu unesete naznake kao listu, ali joj kasnije dodelite rečnik, dobićete grešku:

```py
ratings: list = [1, 2, 3]
ratings = {1: 'Bad', 2: 'average', 3: 'Good'}
```

Greška:
```py
app.py:3: error: Incompatible types in assignment (expression has type "Dict[int, str]", variable has type "List[Any]")
Found 1 error in 1 file (checked 1 source file)
```

Da biste odredili tipove vrednosti u listi, rečniku i skupovima, možete koristiti alijase tipova iz modula `typing`:

Tip alijasa   |	Ugrađeni tip
--------------|---------------
List 	      |  list
Torka 	      |  tuple
Dict     	  |  dict
Set 	      |  set
Frozenset     |  fozenset
Sequence 	  |  Za listu, torku i bilo koji drugi tip podataka sekvence.
Map      	  |  Za rečnik (dict), set, frozenset i bilo koji drugi tip podataka za mapiranje
Array bytes   |	 Tipovi bytes, bytearray i memmory_view.

Na primer, sledeće definiše listu celih brojeva:
```py
from typing import List

ratings: List[int] = [1, 2, 3]
```

### Nijedan tip

Ako funkcija eksplicitno ne vraća vrednost, možete koristiti None da biste uneli naznaku vraćene vrednosti. Na primer:

```py
def log(message: str) -> None:
    print(message)
```

### Tipovi bukvala

Ako želite da dozvolite promenljivoj ili parametru funkcije da prihvati određenu listu literalnih vrednosti, možete koristiti tipove. Na primer:

```py
from typing import Literal

def set_mode(mode: Literal['read', 'write']) -> None:
    print(f"Setting mode to {mode}")

set_mode('write')
```

Kako funkcioniše:

- Prvo, uvezite `Literal` obrazac modula `typing`:

    ```py
    from typing import Literal
    ```

- Drugo, definišite `set_mode`funkciju koja prihvata parametar *mode* tipa `Literal`. `set_mode` može da prihvati samo jedan od dva string literal-a 'read' ili 'write':

    ```py
    mode: Literal['read', 'write']
    ```

- Treće, pozovite set_modefunkciju i prosledite 'write'argument:

    ```py
    set_mode('write')
    ```

Ako pokrenete mypy komandu, prikazaće se izlaz:

```shell
Success: no issues found in 1 source file
```

Ako promenite argument na vrednost koja nije 'read' ili 'write', *mypy* ddaje greške:

```py
set_mode('execute')
```

Greška:
```shell
main.py:8: error: Argument 1 to "set_mode" has incompatible type "Literal['execute']"; expected "Literal['read', 'write']"  [arg-type]
Found 1 error in 1 file (checked 1 source file)
```

### Konačne promenljive

Možete jednom dodeliti vrednost finalnoj promenljivoj. Ako joj ponovo dodelite vrednost, naići ćete na grešku. Finalne promenljive vam pomažu da sprečite slučajnu izmenu konstanti. Na primer:

```py
from typing import Final

HTTPS : Final[bool] = True
print(HTTPS )
```

Kako to funkcioniše.

- Prvo, uvezite Finaliz typingmodula:

    ```py
    from typing import Final
    ```

- Drugo, definišite konstantu *HTTPS* tipa `Final[bool]` i postavite njenu vrednost na `True`:

    ```py
    HTTPS : Final[bool] = True
    ```

    Pored toga bool, možete koristiti i druge ugrađene tipove kao što su `int`, `float`, `string`, itd., za čuvanje konačnih vrednosti različitih tipova.

- Treće, pokažite vrednost *HTTPS*:

    ```py
    print(HTTPS)
    ```

    Program *mypy* će označiti kod kao uspešan.

- Ako pokušate da promenite HTTPS u `False`, proveravač tipa ( mypy ) će označiti dodelu kao grešku.

    ```py
    from typing import Final

    HTTPS : Final[bool] = True
    HTTPS = False # error
    ```

    Greška:
    ```py
    main.py:4: error: Cannot assign to final name "HTTPS"  [misc]
    Found 1 error in 1 file (checked 1 source file)
    ```

### Rezime

Koristite naznake tipova i alate za statičku proveru tipova da biste učinili svoj kod robusnijim.
