
# Pajton deskriptori

**Rezime** : u ovom tutorijalu ćete saznati o deskriptorima u Pajtonu, kako deskriptori funkcionišu i kako ih efikasnije primeniti.

## Uvod u Pajton deskriptore

Pretpostavimo da imate klasu *Person* sa dva atributa instance *first_name* i *last_name*:

```py
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name
```

I želite da atributi *first_name* i *last_name* budu neprazni stringovi. Ovi obični atributi ne mogu to garantovati.

Da biste obezbedili validnost podataka, možete koristiti svojstvo sa `getter` i `setter` metodama, kao što je ovo:

```py
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

    @property
    def first_name(self):
        return self._first_name

    @first_name.setter
    def first_name(self, value):
        if not isinstance(value, str):
            raise ValueError('The first name must a string')

        if len(value) == 0:
            raise ValueError('The first name cannot be empty')

        self._first_name = value

    @property
    def last_name(self):
        return self._last_name

    @last_name.setter
    def last_name(self, value):
        if not isinstance(value, str):
            raise ValueError('The last name must a string')

        if len(value) == 0:
            raise ValueError('The last name cannot be empty')

        self._last_name = value
```

U ovoj *Person* klasi, `getter` vraća vrednost atributa dok je `setter` validira pre nego što je dodeli atributu.

Ovaj kod radi savršeno dobro. Međutim, suvišan je jer logika validacije potvrđuje da li su ime i prezime isti.

Takođe, ako klasa ima više atributa koji zahtevaju string koji nije prazan, potrebno je da duplirate ovu logiku u drugim svojstvima. Drugim rečima, ova logika validacije se ne može ponovo koristiti.

Da biste izbegli dupliranje logike, možete imati metodu koja validira podatke i ponovo koristiti ovu metodu u drugim svojstvima. Ovaj pristup će omogućiti ponovnu upotrebu. Međutim, Pajton ima bolji način da ovo reši korišćenjem `deskriptora`.

Prvo, definišite klasu `deskriptora` koja implementira tri metode `__set_name__`, `__get__` i `__set__`:

```py
class RequiredString:
    def __set_name__(self, owner, name):
        self.property_name = name

    def __get__(self, instance, owner):
        if instance is None:
            return self

        return instance.__dict__[self.property_name] or None

    def __set__(self, instance, value):
        if not isinstance(value, str):
            raise ValueError(f'The {self.property_name} must be a string')

        if len(value) == 0:
            raise ValueError(f'The {self.property_name} cannot be empty')

        instance.__dict__[self.property_name] = value
```

Drugo, koristite *RequiredString* klasu u `Person` klasi:

```py
class Person:
    first_name = RequiredString()
    last_name = RequiredString()
```

Ako dodelite prazan string ili vrednost koja nije string atributu *first_name* or *last_name* klase *Person*, dobićete grešku.

Na primer, sledeći pokušaj dodeljivanja praznog stringa atributu *first_name*:

```py
try:
    person = Person()
    person.first_name = ''
except ValueError as e:
    print(e)
```
```py
Error:
The first_name must be a string
```

Takođe, klasu *RequiredString* možete koristiti u bilo kojoj klasi sa atributima koji zahtevaju vrednost stringa koja nije prazna.

Pored *RequiredString*, možete definisati i druge deskriptore da biste primenili druge tipove podataka kao što su *age*, *email* i *phone*. A ovo je samo jednostavna primena deskriptora.

Hajde da razumemo kako funkcionišu deskriptori.

### Protokol deskriptor

U Pajtonu, `protokol deskriptor` se sastoji od tri metode:

    `__get__` dobija vrednost atributa
    `__set__` postavlja vrednost atributa
    `__delete__` briše atribut

Opciono, deskriptor može imati __set_name__ metodu koja postavlja atribut na instanci klase na novu vrednost.

### Šta je deskriptor

Deskriptor je objekat klase koji implementira jednu od metoda navedenih u protokolu deskriptora.

Deskriptora ima dve vrste: 
- Deskriptor podataka je objekat klase koji implementira metod `__set__` i/ili `_delete__` i 
- Deskriptor koji nije podatak je objekat koji implementira samo metodu `__get__`.

Tip deskriptora određuje rezoluciju pretrage svojstva koju ćemo obraditi u sledećem tutorijalu.

### Kako funkcionišu deskriptori

Sledeće menja `RequiredString` klasu da bi uključilo `print` naredbe koje ispisuju argumente.

```py
class RequiredString:
    def __set_name__(self, owner, name):
        print(f'__set_name__ was called with owner={owner} and name={name}')
        self.property_name = name

    def __get__(self, instance, owner):
        print(f'__get__ was called with instance={instance} and owner={owner}')
        if instance is None:
            return self

        return instance.__dict__[self.property_name] or None

    def __set__(self, instance, value):
        print(f'__set__ was called with instance={instance} and value={value}')

        if not isinstance(value, str):
            raise ValueError(f'The {self.property_name} must a string')

        if len(value) == 0:
            raise ValueError(f'The {self.property_name} cannot be empty')

        instance.__dict__[self.property_name] = value

class Person:
    first_name = RequiredString()
    last_name = RequiredString()
```

Kada kompajlirate kod, videćete da Pajton kreira objekte deskriptora za *first_name* i *last_name* i automatski poziva `__set_name__` metodu ovih objekata. Evo rezultata:

```py
    `__set_name__` was called with owner=<class '__main__.Person'> and name=first_name
    `__set_name__` was called with owner=<class '__main__.Person'> and name=last_name
```

U ovom primeru, argument *owner* `__set_name__` je podešen na *Person* klasu u `__main__` modulu, a *name* argument je podešen na atribut *first_name* i *last_name* shodno tome.

To znači da Pajton automatski poziva `__set_name__` kada se kreira klasa koja je vlasnik *Person*. Sledeće izjave su ekvivalentne:

```py
first_name = RequiredString()
```

i

```py
first_name.__set_name__(Person, 'first_name')
```

Unutar `__set_name__` metode, dodeljujemo *name* argument atributu *property_name* instance objekta `descriptor` kako bismo mu kasnije mogli pristupiti u metodi ` __get__` and `__set__`:

```py
self.property_name = name
```

I first_name i last_name su promenljive klase Person. Ako pogledate atribut klase Person.__dict__, videćete dva objekta deskriptora first_name i last_name:

```py
from pprint import pprint

pprint(Person.__dict__)
```

Izlaz:
```py
mappingproxy({'__dict__': <attribute '__dict__' of 'Person' objects>,
            '__doc__': None,
            '__module__': '__main__',
            '__weakref__': <attribute '__weakref__' of 'Person' objects>,
            'first_name': <__main__.RequiredString object at 0x0000019D6AB947F0>,
            'last_name': <__main__.RequiredString object at 0x0000019D6ACFBE80>})
```

Evo __set__metode klase RequiredString:

```py
def __set__(self, instance, value):
    print(f'__set__ was called with instance={instance} and value={value}')

    if not isinstance(value, str):
        raise ValueError(f'The {self.property_name} must be a string')

    if len(value) == 0:
        raise ValueError(f'The {self.property_name} cannot be empty')

    instance.__dict__[self.property_name] = value
```

Kada dodelite novu vrednost deskriptoru, Pajton poziva __set__ metodu da postavi atribut na instanci klase owner na novu vrednost. Na primer:

```py
person = Person()
person.first_name = 'John'
```

Izlaz:
```py
__set__ was called with instance=<__main__.Person object at 0x000001F85F7167F0> and value=John
```

U ovom primeru, instanceargument je personobjekat, a vrednost je string 'John'. Unutar __set__metode, podižemo `a` ValueErrorako nova vrednost nije string ili ako je prazan string.

U suprotnom, dodeljujemo vrednost atributu instance first_nameobjekta person:

instance.__dict__[self.property_name] = value
Kodni jezik:  Pajton  ( python )

Imajte na umu da Pajton koristi instance.__dict__rečnik za čuvanje atributa instance objekta instance.

Kada podesite *first_name* i *last_name* instance objekta *Person*, videćete atribute instance sa istim imenima u `instance.__dict__`. Na primer:

```py
person = Person()
print(person.__dict__)  # {}

person.first_name = 'John'
person.last_name = 'Doe'

print(person.__dict__) # {'first_name': 'John', 'last_name': 'Doe'}
```

Izlaz:
```py
{}
{'first_name': 'John', 'last_name': 'Doe'}
```

Sledeće prikazuje __get__ metodu klase RequiredString:

```py
def __get__(self, instance, owner):
    print(f'__get__ was called with instance={instance} and owner={owner}')
    if instance is None:
        return self

    return instance.__dict__[self.property_name] or None
```

Pajton poziva __get__ metod Person objekta 's' kada pristupite first_name atributu. Na primer:

```py
person = Person()

person.first_name = 'John'
print(person.first_name)
```

Izlaz:
```py
__set__ was called with instance=<__main__.Person object at 0x000001F85F7167F0> and value=John
__get__ was called with instance=<__main__.Person object at 0x000001F85F7167F0> and owner=<class '__main__.Person'>
```

Metod __get__ vraća deskriptor ako je instance `None`. Na primer, ako pristupite first_name ili last_name iz Person klase, videćete objekat deskriptora:

```py
print(Person.first_name)
```

Izlaz:
```py
<__main__.RequiredString object at 0x000001AF1DA147F0>
```

Ako instanca nije None, __get__() metod vraća vrednost atributa sa imenom property_name objekta instance.
### Rezime

Deskriptori su objekti klase koji implementiraju jednu od metoda u protokolu deskriptora, uključujući `__set__`, `__get__`,`__del__`
