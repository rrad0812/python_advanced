
# Menadžeri konteksta

**Rezime**: u ovom tutorijalu ćete saznati o Pajton kontekst menadžerima i kako ih efikasno koristiti

## Menadžer konteksta

### Uvod u menadžere konteksta

`Menadžer konteksta` je objekat koji definiše kontekst izvršavanja unutar `with` naredbe.

Počnimo sa jednostavnim primerom da bismo razumeli koncept menadžera konteksta. Pretpostavimo da imate datoteku pod nazivom *data.txt* koja sadrži ceo broj 100.

Sledeći program čita datoteku *data.txt*, pretvara njen sadržaj u broj i prikazuje rezultat na standardnom izlazu:

```py
f = open('data.txt')
data = f.readlines()

# convert the number to integer and display it
print(int(data[0]))

f.close()
```

Kod je jednostavan i jasan.

Međutim, *data.txt* može sadržati podatke koji se ne mogu konvertovati u broj. U ovom slučaju, kod će rezultirati izuzetkom.

Na primer, ako `data.txt` sadrži string '100' umesto broja 100, dobićete sledeću grešku:

```py
ValueError: invalid literal for int() with base 10: "'100'"
```

Zbog ovog izuzetka, Pajton možda neće pravilno zatvoriti datoteku.

Da biste ovo popravili, možete koristiti sledeću `try...except...finally` izjavu:

```py
try:
    f = open('data.txt')
    data = f.readlines()
    # convert the number to integer and display it
    print(int(data[0]))
except ValueError as error:
    print(error)
finally:
    f.close()
```

Pošto se kod u `finally` bloku uvek izvršava, kod će uvek pravilno zatvoriti datoteku.

Ovo rešenje funkcioniše kako se očekuje. Međutim, prilično je opširno.

Stoga, Pajton vam pruža bolji način koji vam omogućava da automatski zatvorite datoteku nakon što završite njenu obradu.

Tu dolaze do izražaja `menadžeri konteksta`.

Sledeće je prikazano kako se koristi menadžer konteksta za obradu `data.txt` datoteke:

```py
with open('data.txt') as f:
    data = f.readlines()
    print(int(data[0])    
```

U ovom primeru koristimo `open()` funkciju sa `with` izrazom . Nakon `with` bloka, Pajton će automatski zatvoriti/osloboditi sve resurse.

### Pajton sa izrazom

Evo tipične sintakse izjave with:

```py
with context as ctx:
    # use the the object 

# context is cleaned up
```

Kako to funkcioniše.

- Kada Pajton naiđe na `with` izraz, kreira novi kontekst. Kontekst može opciono vratiti object.
- Nakon `with` bloka, Pajton automatski čisti kontekst.
- Opseg `ctx` ima isti opseg kao i `with` naredba. To znači da možete pristupiti `ctx` i unutar i nakon `with` naredbe.

Sledeće je prikazano kako pristupiti promenljivoj *f* nakon `with` izraza:

```py
with open('data.txt') as f:
    data = f.readlines()
    print(int(data[0]))

print(f.closed)  # True
```

### Protokol za upravljanje kontekstom

Pajton menadžeri konteksta rade na osnovu protokola za menadžer konteksta.

Protokol menadžera konteksta ima sledeće metode:

- `__enter__()` – podesi kontekst i opciono vrati neki objekat
- `__exit__()` – očisti objekat.

Ako želite da klasa podržava protokol menadžera konteksta, potrebno je da implementirate ove dve metode.

Pretpostavimo da je `ContextManager` klasa koja podržava protokol menadžera konteksta.

Sledeće je prikazano kako se koristi `ContextManager` klasa:

```py
with ContextManager() as ctx:
    # do something
# done with the context
```

Kada koristite `ContextManager` klasu sa `with` naredbom, Pajton implicitno kreira instancu klase `ContextManager` i automatski poziva `__enter__()` metodu na toj instanci.

Metoda `__enter__()` može opciono vratiti objekat. Ako je to slučaj, Pajton dodeljuje vraćenom objektu `ctx`.

Obratite pažnju da se `ctx` poziva na objekat koji vraća metoda `__enter__()`. Ne poziva se na instancu klase `ContextManager`.

Ako se izuzetak desi unutar bloka `with` ili nakon `with` bloka, Pajton poziva `__exit__`() metodu na objektu instance.

Funkcionalno, `with` naredba je ekvivalentna sledećoj `try...finally` naredbi:

```py
instance = ContextManager()
ctx = instance.__enter__()

try:
    # do something with the txt
finally:
    # done with the context
    instance.__exit__()
```

**Metoda `__enter__`**
U `__enter__()` metodi možete izvršiti potrebne korake za podešavanje konteksta.Opciono, možete vratiti objekat iz `__enter__()` metode.

**Metoda `__exit__`**
Pajton uvek izvršava `__exit__()` metodu čak i ako se u bloku `with` pojavi izuzetak.

Metoda `__exit__()` prihvata tri argumenta: tip izuzetka, vrednost izuzetka i objekat trasiranja. Svi ovi argumenti će biti None ako se ne dogodi izuzetak.

```py
def __exit__(self, ex_type, ex_value, ex_traceback):
    ...
```

Metod `__exit__()` vraća bulovsku vrednost, ili `True` ili `False`.

Ako je povratna vrednost `True`, Python će svaki izuzetak učiniti nečujnim. U suprotnom, neće se izuzetak isključiti.

### Aplikacije za upravljanje kontekstom u Pajtonu

Kao što vidite iz prethodnog primera, uobičajena upotreba menadžera konteksta je automatsko otvaranje i zatvaranje datoteka.

Međutim, možete koristiti menadžere konteksta u mnogim drugim slučajevima:

1) `Otvori – Zatvori`

    Ako želite da automatski otvorite i zatvorite resurs, možete koristiti menadžer konteksta. Na primer, možete otvoriti soket i zatvoriti ga pomoću `menadžera konteksta.

2) `Zaključaj – otpusti`

    Menadžeri konteksta mogu vam pomoći da efikasnije upravljate zaključavanjima objekata. Oni vam omogućavaju da automatski postavite zaključavanje i otključate ga.

3) `Start – stop`
    Menadžeri konteksta vam takođe pomažu da radite sa scenarijem koji zahteva faze pokretanja i zaustavljanja.

    Na primer, možete koristiti menadžer konteksta da biste pokrenuli tajmer i automatski ga zaustavili.

4) `Promena – resetovanje`

    Menadžeri konteksta mogu da rade sa scenarijima promena i resetovanja.

    Na primer, vaša aplikacija treba da se poveže sa više izvora podataka. I ima podrazumevanu vezu. Da biste se povezali sa drugim izvorom podataka:

    - Prvo, koristite menadžer konteksta da biste promenili podrazumevanu vezu na novu.
    - Drugo, radite sa novom vezom

    Sledeći kod prikazuje jednostavnu implementaciju funkcije `open()` korišćenjem protokola menadžera konteksta:

    ```py
    class File:
        def __init__(self, filename, mode):
            self.filename = filename
            self.mode = mode

        def __enter__(self):
            print(f'Opening the file {self.filename}.')
            self.__file = open(self.filename, self.mode)
            return self.__file

        def __exit__(self, exc_type, exc_value, exc_traceback):
            print(f'Closing the file {self.filename}.')
            if not self.__file.closed:
                self.__file.close()

            return False

    with File('data.txt', 'r') as f:
        print(int(next(f)))
    ```

    Kako to funkcioniše.

    - Prvo, inicijalizujte filenamei mode u `__init__()` metodi.
    - Drugo, otvorite datoteku u `__enter__()` metodi i vratite objekat datoteke.
    - Treće, zatvorite datoteku ako je otvorena u `__exit__()` metodi.

5) `Pokretanja i zaustavljanja`

    Sledeći kod definiše Timer klasu koja podržava protokol menadžera konteksta:

    ```py
    from time import perf_counter

    class Timer:
        def __init__(self):
            self.elapsed = 0

        def __enter__(self):
            self.start = perf_counter()
            return self

        def __exit__(self, exc_type, exc_value, exc_traceback):
            self.stop = perf_counter()
            self.elapsed = self.stop - self.start
            return False
    ```

    Kako to funkcioniše.

    - Prvo, uvezite `perf_counter` iz `time` modula.
    - Drugo, pokrenite tajmer u `__enter__()` metodi
    - Treće, zaustavite tajmer u `__exit__()` metodi i vratite proteklo vreme.

    Sada možete koristiti `Timer` klasu da izmerite vreme potrebno za izračunavanje Fibonačijevog broja od 1000, milion puta:

    ```py
    def fibonacci(n):
        f1 = 1
        f2 = 1
        for i in range(n-1):
            f1, f2 = f2, f1 + f2

        return f1


    with Timer() as timer:
        for _ in range(1, 1000000):
            fibonacci(1000)

    print(timer.elapsed)
    ```

### Rezime

- Koristite Pajton `kontekstne menadžere` da biste definisali kontekste izvršavanja prilikom izvršavanja u `with` naredbi.
- Implementirajte `__enter__()` i `__exit__()` metode za podršku protokolu menadžera konteksta.
