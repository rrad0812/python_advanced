
# Menadžeri konteksta

**Rezime**: u ovom tutorijalu ćete saznati o Pajton kontekst menadžerima i kako ih efikasno koristiti
Uvod u menadžere konteksta u Pajtonu

Menadžer konteksta je objekat koji definiše kontekst izvršavanja unutar withnaredbe.

Počnimo sa jednostavnim primerom da bismo razumeli koncept menadžera konteksta.

Pretpostavimo da imate datoteku pod nazivom data.txtkoja sadrži ceo broj 100 .

Sledeći program čita datoteku data.txt, pretvara njen sadržaj u broj i prikazuje rezultat na standardnom izlazu:

```py
f = open('data.txt')
data = f.readlines()

# convert the number to integer and display it
print(int(data[0]))

f.close()
```

Kod je jednostavan i jasan.

Međutim, data.txt može sadržati podatke koji se ne mogu konvertovati u broj. U ovom slučaju, kod će rezultirati izuzetkom.

Na primer, ako data.txt sadrži string '100' umesto broja 100, dobićete sledeću grešku:

```py
ValueError: invalid literal for int() with base 10: "'100'"
```

Zbog ovog izuzetka, Pajton možda neće pravilno zatvoriti datoteku.

Da biste ovo popravili, možete koristiti sledeću try...except...finally izjavu:

try:
    f = open('data.txt')
    data = f.readlines()
    # convert the number to integer and display it
    print(int(data[0]))
except ValueError as error:
    print(error)
finally:
    f.close()
Kodni jezik:  Pajton  ( python )

Pošto se kod u finallybloku uvek izvršava, kod će uvek pravilno zatvoriti datoteku.

Ovo rešenje funkcioniše kako se očekuje. Međutim, prilično je opširno.

Stoga, Pajton vam pruža bolji način koji vam omogućava da automatski zatvorite datoteku nakon što završite njenu obradu.

Tu dolaze do izražaja menadžeri konteksta .

Sledeće je prikazano kako se koristi menadžer konteksta za obradu data.txtdatoteke:

with open('data.txt') as f:
    data = f.readlines()
    print(int(data[0])    
Kodni jezik:  Pajton  ( python )

U ovom primeru koristimo open()funkciju sa withizrazom . Nakon withbloka, Pajton će se automatski zatvoriti.
Pajton sa izrazom

Evo tipične sintakse izjave with:

with context as ctx:
    # use the the object 

# context is cleaned up
Kodni jezik:  Pajton  ( python )

Kako to funkcioniše.

    Kada Pajton naiđe na withizraz, kreira novi kontekst. Kontekst može opciono vratiti object.
    Nakon withbloka, Pajton automatski čisti kontekst.
    Opseg `the` ctxima isti opseg kao i `the` withnaredba. To znači da `the` možete pristupiti ctxi unutar i nakon withnaredbe.

Sledeće je prikazano kako pristupiti promenljivoj fnakon withizraza:

with open('data.txt') as f:
    data = f.readlines()
    print(int(data[0]))


print(f.closed)  # True
Kodni jezik:  Pajton  ( python )

Protokol za upravljanje kontekstom Pajtona

Pajton menadžeri konteksta rade na osnovu protokola za menadžer konteksta .

Protokol menadžera konteksta ima sledeće metode:

    __enter__()– podesiti kontekst i opciono vratiti neki objekat
    __exit__()– očistiti objekat.

Ako želite da klasa podržava protokol menadžera konteksta, potrebno je da implementirate ove dve metode.

Pretpostavimo da ContextManagerje to klasa koja podržava protokol menadžera konteksta.

Sledeće je prikazano kako se koristi ContextManagerklasa:

with ContextManager() as ctx:
    # do something
# done with the context
Kodni jezik:  Pajton  ( python )

Kada koristite ContextManagerclass sa withnaredbom , Pajton implicitno kreira instancu klase ContextManager( instance) i automatski poziva __enter__()metodu na toj instanci.

Metoda __enter__()može opciono vratiti objekat. Ako je to slučaj, Pajton dodeljuje vraćenom objektu ctx.

Obratite pažnju da ctxse poziva na objekat koji vraća metoda __enter__(). Ne poziva se na instancu klase ContextManager.

Ako se izuzetak desi unutar bloka with ili nakon withbloka, Pajton poziva __exit__()metodu na instanceobjektu.
Пајтон менаџери контекста

Funkcionalno, withizjava je ekvivalentna sledećoj try...finallyizjavi:

   instance = ContextManager()
ctx = instance.__enter__()

try:
    # do something with the txt
finally:
    # done with the context
    instance.__exit__()
Kodni jezik:  Pajton  ( python )

Metoda __enter__(

U __enter__()metodi možete izvršiti potrebne korake za podešavanje konteksta.

Opciono, možete vratiti objekat iz __enter__()metode.
Metoda __exit__(

Pajton uvek izvršava __exit__()metodu čak i ako se u bloku pojavi izuzetak with.

Metoda __exit__()prihvata tri argumenta: tip izuzetka, vrednost izuzetka i objekat trasiranja. Svi ovi argumenti će biti Noneako se ne dogodi izuzetak.

def __exit__(self, ex_type, ex_value, ex_traceback):
    ...
Kodni jezik:  Pajton  ( python )

Metod __exit__()vraća bulovsku vrednost, ili Trueili False.

Ako je povratna vrednost True, Python će svaki izuzetak učiniti nečujnim. U suprotnom, neće se izuzetak isključiti.
Aplikacije za upravljanje kontekstom u Pajtonu

Kao što vidite iz prethodnog primera, uobičajena upotreba menadžera konteksta je automatsko otvaranje i zatvaranje datoteka.

Međutim, možete koristiti menadžere konteksta u mnogim drugim slučajevima:
1) Otvori – Zatvori

Ako želite da automatski otvorite i zatvorite resurs, možete koristiti menadžer konteksta.

Na primer, možete otvoriti soket i zatvoriti ga pomoću menadžera konteksta.
2) Zaključaj – otpusti

Menadžeri konteksta mogu vam pomoći da efikasnije upravljate zaključavanjima objekata. Oni vam omogućavaju da automatski postavite zaključavanje i otključate ga.
3) Start – stop

Menadžeri konteksta vam takođe pomažu da radite sa scenarijem koji zahteva faze pokretanja i zaustavljanja.

Na primer, možete koristiti menadžer konteksta da biste pokrenuli tajmer i automatski ga zaustavili.
3) Promena – resetovanje

Menadžeri konteksta mogu da rade sa scenarijima promena i resetovanja.

Na primer, vaša aplikacija treba da se poveže sa više izvora podataka. I ima podrazumevanu vezu.

Da biste se povezali sa drugim izvorom podataka:

    Prvo, koristite menadžer konteksta da biste promenili podrazumevanu vezu na novu.
    Drugo, radite sa novom vezom
    Treće, vratite je na podrazumevanu vezu kada završite sa radom sa novom vezom.

Implementacija protokola za upravljanje kontekstom u

Sledeća slika prikazuje jednostavnu implementaciju funkcije open()korišćenjem protokola menadžera konteksta:

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
Kodni jezik:  Pajton  ( python )

Kako to funkcioniše.

    Prvo, inicijalizujte filenamei modeu __init__()metodi.
    Drugo, otvorite datoteku u __enter__()metodi i vratite objekat datoteke.
    Treće, zatvorite datoteku ako je otvorena u __exit__()metodi.

pokretanja i zaustavljanja

Sledeće definiše Timerklasu koja podržava protokol menadžera konteksta:

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
Kodni jezik:  Pajton  ( python )

Kako to funkcioniše.

    Prvo, uvezite perf_counteriz timemodula.
    Drugo, pokrenite tajmer u __enter__()metodi
    Treće, zaustavite tajmer u __exit__()metodi i vratite proteklo vreme.

Sada možete koristiti Timerklasu da izmerite vreme potrebno za izračunavanje Fibonačijevog broja od 1000, milion puta:

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
Kodni jezik:  Pajton  ( python )

Rezime

    Koristite Pajton kontekstne menadžere da biste definisali kontekste izvršavanja prilikom izvršavanja u withnaredbi.
    implementirajte __enter__()i __exit__()metode za podršku protokolu menadžera konteksta.
