
# Pajton property

**Rezime**: u ovom tutorijalu ćete naučiti o Python `property` klase i kako da ga koristite za definisanje svojstava klase.

## Uvod u property klase

Sledeći kod definiše *Person* klasu koja ima dva atributa *name* i *age*, i kreira novu instancu klase *Person*:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

john = Person('John', 18)
```

Pošto je *age* atribut instance klase *Person*, možete mu dodeliti novu vrednost ovako:

```py
john.age = 19
```

Sledeći zadatak je takođe tehnički validan:

```py
john.age = -1
```

Međutim, *age* je semantički netačan. Da biste bili sigurni da *age* nije nula ili negativna, koristite `if` naredbu za dodavanje provere na sledeći način:

```py
age = -1
if age <= 0:
    raise ValueError('The age must be positive')
else:
    john.age = age
```

I to morate da uradite svaki put kada želite da dodelite vrednost atributu *age*. Ovo je ponavljajuće i teško za održavanje. Da biste izbegli ovo ponavljanje, možete definisati par metoda koje se zovu `getter` i `seter`.

### Getter i seter 

Metode `getter` i `setter` pružaju interfejs za pristup atributu instance:

- `Getter` vraća vrednost atributa
- `Setter` postavlja novu vrednost atributa

U našem primeru, možete učiniti *age* atribut privatnim (po konvenciji) i definisati metodu za dobijanje i postavljanje da biste manipulisali *age* atributom.

Sledeći kod prikazuje novu *Person* klasu sa metodama za dobijanje i postavljanje *age* atributa:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.set_age(age)

    def set_age(self, age):
        if age <= 0:
            raise ValueError('The age must be positive')
        self._age = age

    def get_age(self):
        return self._age
```

Kako ovo funkcioniše.

U *Person* klasi, *set_age()* je `seter`, a *get_age()* je `getter`. Po konvenciji, `getter` i `seter` imaju imena: `get_<attribute>()` i `set_<attribute>()`.

U *set_age()* metodi, podižemo `ValueError` ako je *age* manje ili jednako nuli. U suprotnom, dodeljujemo *age* argument atributu *_age*:

```py
def set_age(self, age):
    if age <= 0:
        raise ValueError('The age must be positive')
    self._age = age
```

Metoda *get_age()* vraća vrednost atributa *_age*:

```py
def get_age(self):
    return self._age
```

U `__init__()` metodi, pozivamo `setter` - *set_age()* metodu da bismo inicijalizovali *_age* atribut:

```py
def __init__(self, name, age):
    self.name = name
    self.set_age(age)
```

Sledeći pokušaji dodeljivanja nevažeće vrednosti atributu *age*:

```py
john = Person('John', 18)
john.set_age(-19)
```

I Pajton je izdao `ValueError` kao što se i očekivalo.

> `ValueError: The age must be positive`

Ovaj kod radi sasvim dobro. Ali ima problem sa kompatibilnošću unazad.

Pretpostavimo da ste objavili *Person* klasu pre nekog vremena i da je drugi programeri već koriste. A sada kada dodate `getter` i `seter`, sav kod koji koristi *Person* više neće raditi.

Da biste definisali `getter` i `setter` metode, a istovremeno postigli kompatibilnost sa prethodnim verzijama, možete koristiti `property()` klasu.

### Klasa property

Klasa `property` vraća `property` objekat. `property()` klasa ima sledeću sintaksu:

```py
property(fget=None, fset=None, fdel=None, doc=None)
```

`property()` ima sledeće parametre:
- `fget` je funkcija ili meoda za dobijanje vrednosti atributa.
- `fset` je funkcija ili metoda za postavljanje vrednosti atributa.
- `fdel` je funkcija za brisanje atributa.
- `doc` je dokumentacioni string, tj. komentar.

Sledeći primer koristi `property()` funkciju za definisanje *age* svojstva *Person* klase.

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def set_age(self, age):
        if age <= 0:
            raise ValueError('The age must be positive')
        self._age = age

    def get_age(self):
        return self._age

    age = property(fget=get_age, fset=set_age)
```

U *Person* klasi kreiramo novi objekat tipa `property` pozivanjem `property()` i dodeljujemo objekat svojstva atributa *age*. Imajte na umu da je *age* atribut klase, a ne atribut instance.

Sledeće pokazuje da *Person.age* je `property` objekat:

```py
print(Person.age)
```

Izlaz:
```py
<property object at 0x000001F5F5149180>
```

Sledeći kod kreira novu instancu klase *Personi* i pristupa *age* atributu:

```py
john = Person('John', 18)
```

`john.__dict__` čuva atribute instance objekta *john*. Sledeća kod prikazuje sadržaj `john.__dict__`:
```py
pprint(john.__dict__)
```

Izlaz:

```py
{'_age': 18, 'name': 'John'}
```

Kao što jasno možete videti iz izlaza, `john.__dict__` nema *age* atribut.

Sledeća komanda dodeljuje vrednost atributu *age* objekta *john*:

```py
john.age = 19
```

U ovom slučaju, Pajton prvo traži atribut *age* u `john.__dict__`. Pošto Pajton ne pronalazi *age* atribut u `john.__dict__`, onda će ga pronaći *age* u `Person.__dict__`.

`Person.__dict__` čuva atribute klase *Person*. Sledeći kod prikazuje sadržaj `Person.__dict__`:

```py
pprint(Person.__dict__)
```

Izlaz:

```py
mappingproxy({'__dict__': <attribute '__dict__' of 'Person' objects>,
              '__doc__': None,
              '__init__': <function Person.__init__ at 0x000002242F5B2670>,
              '__module__': '__main__',
              '__weakref__': <attribute '__weakref__' of 'Person' objects>,
              'age': <property object at 0x000002242EE39180>,
              'get_age': <function Person.get_age at 0x000002242F5B2790>,
              'set_age': <function Person.set_age at 0x000002242F5B2700>})
```

Pošto Pajton pronalazi *age* atribut u `Person.__dict__`, pozvaće *age* objekat svojstva.

Kada dodelite vrednost objektu *age*:

```py
john.age = 19
```

Pajton će pozvati funkciju dodeljenu argumentu `fset`, a to je *set_age()*.

Slično tome, kada čitate iz *age* objekta svojstva, Pajton će izvršiti funkciju dodeljenu argumentu `fget`, a to je *get_age()* metoda.

Korišćenjem `property()` klase, možemo dodati svojstvo klasi uz očuvanje kompatibilnosti sa verzijama unazad. U praksi, prvo ćete definisati atribute. Kasnije, možete dodati svojstvo klasi ako je potrebno.

### Spajanje svega zajedno

```py
from pprint import pprint

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def set_age(self, age):
        if age <= 0:
            raise ValueError('The age must be positive')
        self._age = age

    def get_age(self):
        return self._age

    age = property(fget=get_age, fset=set_age)

print(Person.age)

john = Person('John', 18)
pprint(john.__dict__)

john.age = 19
pprint(Person.__dict__)
```

### Rezime

Koristite Python `property()` klasu da definišete svojstvo za klasu.

## Dekorator @property

**Rezime**: u ovom tutorijalu ćete naučiti o dekoratoru svojstava ( @property ) u Pajtonu i, što je još važnije, kako on funkcioniše.

### Uvod u dekorator @property

U prethodnom tutorijalu ste naučili kako da koristite klasu `property` da biste dodali svojstvo klasi. Evo sintakse klase property:

```py
class property(fget=None, fset=None, fdel=None, doc=None)
```

Sledeće definiše *Person* klasu sa dva atributa *name* i *age*:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Da biste definisali metodu za dobijanje *age* atributa, koristite `property` klasu ovako:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    def get_age(self):
        return self._age

    age = property(fget=get_age)
```
`property` prihvata metodu za dobijanje i vraća objekat svojstva.

Sledeća kod kreira instancu klase *Person* i dobija vrednost svojstva *age* preko instance:

```py
john = Person('John', 25)
print(john.age)
```

Izlaz:
```py
25
```

Takođe, možete direktno pozvati *get_age()* metod objekta *Person* ovako:

```py
print(john.get_age())
```

Dakle, da biste dobili vrednost *age* objekta *Person*, možete koristiti ili *age* svojstvo ili *get_age()* metod. 

Ovo stvara nepotrebnu redundantnost. Da biste izbegli ovu redundantnost, možete preimenovati get_age() metodu u age() metodu ovako:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    def age(self):
        return self._age

    age = property(fget=age)
```

`property()` prihvata *age* metodu i vraća *age* `property` objekat - što je dekorator. Stoga, možete koristiti `@property` dekorator za dekorisanje *age()* metode na sledeći način:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    @property
    def age(self):
        return self._age
```

Dakle, korišćenjem `@property` dekoratora možete pojednostaviti definiciju svojstva klase.

### Dekorateri setter-a

Sledeći kod dodaje `setter` metodu ( set_age ) da bi se dodelila vrednost *_age* atributu *Person* klase:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    @property
    def age(self):
        return self._age

    def set_age(self, value):
        if value <= 0:
            raise ValueError('The age must be positive')
        self._age = value 
```

Da biste dodelili *set_age* objektu `fset` svojstva *age*, pozovite `setter` metod age objekta svojstva na sledeći način:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    @property
    def age(self):
        return self._age

    def set_age(self, value):
        if value <= 0:
            raise ValueError('The age must be positive')
        self._age = value

    age = age.setter(set_age)
```

Metoda setter prihvata *set_age* funkciju i vraća drugu funkciju ( `property` objekat ). Stoga, dekorator za metodu property možete koristiti ovako: *@age.setter(set_age)*

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def set_age(self, value):
        if value <= 0:
            raise ValueError('The age must be positive')
        self._age = value
```

Sada možete promeniti *set_age()* metod u *age()* metod i koristiti *age* svojstvo u `__init__()` metodi:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value <= 0:
            raise ValueError('The age must be positive')
        self._age = value
```

Ukratko, možete koristiti dekoratore da biste kreirali svojstvo koristeći sledeći obrazac:

```py
class MyClass:
    def __init__(self, attr):
        self.prop = attr

    @property
    def prop(self):
        return self.__attr

    @prop.setter
    def prop(self, value):
        self.__attr = value
```

U ovom obrascu, *__attr* je privatni atribut, a *prop* je naziv svojstva.

Sledeći primer koristi `@property` dekoratore za kreiranje svojstava *name* and *age* u *Person* klasi:

```py
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value <= 0:
            raise ValueError('The age must be positive')
        self._age = value

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        if value.strip() == '':
            raise ValueError('The name cannot be empty')
        self._name = value
```

### Rezime

- Koristite `@property` dekorator da biste kreirali svojstvo klase.

## Read-only property

**Rezime**: u ovom tutorijalu ćete naučiti kako da definišete svojstvo samo za čitanje u Pajtonu i kako da ga koristite za definisanje izračunatih svojstava.

### Uvod u svojstvo samo za čitanje

Da biste definisali svojstvo samo za čitanje ( readonly ), potrebno je da kreirate svojstvo samo sa funkcijom za dobijanje ( getter ). Međutim, ono nije zaista samo za čitanje jer uvek možete pristupiti osnovnom atributu i promeniti ga.

Svojstva samo za čitanje su korisna u nekim slučajevima, kao što su izračunata svojstva.

Sledeći primer definiše klasu pod nazivom *Circle* koja ima *radius* atribut i *area()* metod:

```py
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return math.pi * self.radius ** 2
```

A sledeći kod kreira novi *Circle* eobjekat i vraća njegovu površinu:

```py
c = Circle(10)
print(c.area())
```

Izlaz:

```py
314.1592653589793
```

Evo kompletnog scenarija:

```py
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return math.pi * self.radius ** 2
    
c = Circle(10)
print(c.area())    
```

Izlaz:

```py
314.1592653589793
```

Ovaj kod radi savršeno dobro.

Ali bi bilo prirodnije da je površina svojstvo objekta *Circle*, a ne metoda. Da biste napravili *area()* metod kao svojstvo klase *Circle*, možete koristiti `@property` dekorator na sledeći način:

```py
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return math.pi * self.radius ** 2


c = Circle(10)
print(c.area)
```

Površina se izračunava iz radius atributa. Stoga se često naziva izračunato ili kompletirano svojstvo.

### Keširanje izračunatih svojstava 

Pretpostavimo da kreirate novi objekat kruga i pristupate svojstvu površine više puta. Svaki put, površina mora biti ponovo izračunata, što nije efikasno.

Da biste poboljšali efikasnost, potrebno je ponovo izračunati površinu kruga samo kada se poluprečnik promeni. Ako se poluprečnik ne menja, možete ponovo koristiti prethodno izračunatu površinu.

Da biste to uradili, možete koristiti tehniku keširanja:

- Prvo, izračunajte površinu i sačuvajte je u keš memoriji.
- Drugo, ako se radijus promeni, resetujte površinu. U suprotnom, vratite površinu direktno iz keša bez ponovnog izračunavanja.

Sledeće definiše novu *Circle* klasu sa keširanim *area* svojstvom:

```py
import math

class Circle:
    def __init__(self, radius):
        self._radius = radius
        self._area = None

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value < 0:
            raise ValueError('Radius must be positive')

        if value != self._radius:
            self._radius = value
            self._area = None

    @property
    def area(self):
        if self._area is None:
            self._area = math.pi * self.radius ** 2

        return self._area
```

Kako to funkcioniše.

- Prvo, postavite *_area* na `None` u `__init__` metodi. Atribut *_area* je keš memorija koja čuva izračunatu površinu.

- Drugo, ako se radijus promeni (u setteru), resetujte _area na None.

- Treće, definišite *area* izračunato svojstvo. *area* svojstvo vraća *_area* ako nije `None`. U suprotnom, izračunajte površinu, sačuvajte je u *_area* i vratite je.

### Rezime 

- Definišite samo `getter` metodu da bi svojstvo bilo samo za čitanje
- Koristite izračunato svojstvo da biste svojstvo klase učinili prirodnijim
- Koristite keširanje izračunatih svojstava da biste poboljšali performanse.

## Deleter property

**Rezime**: u ovom tutorijalu ćete naučiti kako da koristite klasu `property()` za brisanje svojstva objekta.

Da biste kreirali svojstvo klase, koristite dekorator `@property`. Ispod, `@property` dekorator koristi `property` klasu koja ima tri metode: `setter`, `getter` i `deleter`.

Korišćenjem metode za brisanje možete obrisati svojstvo objekta. Obratite pažnju da `deleter()` metoda briše svojstvo objekta, a ne klasu.

Sledeće definiše *Person* klasu sa *name* svojstvom:

```py
from pprint import pprint

class Person:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        if value.strip() == '':
            raise ValueError('name cannot be empty')
        self._name = value

    @name.deleter
    def name(self):
        del self._name
```

U *Person* klasi koristimo `@name.deleter` dekorator. Unutar deletera koristimo `del` ključnu reč za brisanje *_name* atributa instance Person.

Sledeće prikazuje deo `__dict__` klase Person:

```py
pprint(Person.__dict__)
```

Izlaz:

```py
mappingproxy({'__dict__': <attribute '__dict__' of 'Person' objects>,
              '__doc__': None,
              '__init__': <function Person.__init__ at 0x000001DC41D62670>,
              '__module__': '__main__',
              '__weakref__': <attribute '__weakref__' of 'Person' objects>,
              'name': <property object at 0x000001DC41C89130>})
```

Klasa `Person.__dict__` ima *name* promenljivu. Sledeća instrukcija kreira novu instancu klase *Person*:

```py
person = Person('John')
```

Ima person.__dict__atribut _name:

```py
pprint(person.__dict__)
```

Izlaz:

```py
{'_name': 'John'}
```

Sledeća komanda koristi `del` ključnu reč za brisanje *name* svojstva:

```py
del person.name
```

Interno, Pajton će izvršiti `deleter` metodu koja briše *_name* atribut iz *person* objekta. `person.__dict__` biće prazno ovako:

```py
{}
```

A ako ponovo pokušate da pristupite *name* svojstvu, dobićete grešku:

```py
print(person.name)
```

```py
Error:
AttributeError: 'Person' object has no attribute '_name'
```

### Rezime 

- Koristite dekorator `deleter` da biste obrisali svojstvo instance klase.
