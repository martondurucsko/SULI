# 📚 Java OOP – 4. óra ZH Felkészülő Összefoglaló
## (4.1 Interfészek · 4.2 További OOP · 4.3 Reflection API · 4.4 Kivételkezelés OOP · 4.5 Feladatok)

---

# 4.1 Interfészek

## 🔑 Mi az interfész?

Az interfész egy **felületet definiál**, amellyel egy osztály **szolgáltatásait írjuk le**:
- Meghatározza, **mit** kell tudni (de **nem hogyan**!)
- Teljesen absztrakt osztályként viselkedik
- Nincs közös ősük (ellentétben az osztályokkal, ahol van `java.lang.Object`)
- „Használati utasítás / Sablon" az osztályhoz

### Java interfész tartalmazhat:
| Elem | Megjegyzés |
|------|-----------|
| Konstansok | automatikusan `public static final` |
| Tagfüggvények szignatúrája | automatikusan `public abstract` |
| Statikus metódusok | implementációval együtt (`static` kulcsszó) |
| Default metódusok | implementációval együtt (`default` kulcsszó) – erről később |

---

## 🏗️ Java építőelemek

1. **`class`** – osztály, közös ős: `java.lang.Object`
2. **`interface`** – teljesen absztrakt, nincs közös ős
3. **`enum`** – osztály, de véges számú példánnyal
4. **`record`** – adattárolásra tervezett osztály, `java.lang.Object` leszármazott

---

## ⚠️ Többszörös öröklődés problémája (Diamond Problem)

```
         PoweredDevice
        /             \
    Scanner          Printer
        \             /
            Copier
```

- **C++**: meg kell mondani, melyik őstől örököljük a megvalósítást
- **Java megoldása**: ❌ nincs többszörös öröklődés, ✅ DE egy osztály **több interfészt** is megvalósíthat

---

## ✍️ Szintaxis

### Interfész definiálása:
```java
public interface CanTransport {
    double transfer(double km);
}
```

### Implementálás:
```java
public class Taxi extends Car implements CanTransport {
    // kötelező megvalósítani a transfer() metódust
}
```

> ⚠️ Ha egyszerre `extends` és `implements`, az `extends` jön előbb!
> `public class Apple extends Fruit implements Food`

---

## 📋 Szabályok

- Egy osztály **akármennyi** interfészt megvalósíthat
- Egy interfészt **akármennyi** osztály megvalósíthat
- Ha egy osztály megvalósít egy interfészt: **minden metódust meg kell valósítani**, vagy az osztályt **absztraktnak** kell jelölni
- Ha megvalósít egy interfészt → az interfész **statikus típusként** is használható

---

## 🔗 Interfészek közötti öröklés

```java
public interface Delicious extends Edible {
    int howDelicious();
}
```

- Interfészek között is van **többszörös öröklődés** (ellentétben az osztályokkal!)
- A leszármazott örökli az ős konstansait és tagfüggvényeit

### Problémák örökléskor:
- **Azonos nevű konstansok** → nem lehet hivatkozni rájuk (elfedéssel lehet az egyik ősére)
- **Metódusütközés** (ha van implementáció) → nekünk kell feloldani

---

## 🎯 Mire jó az interfész?

✅ Funkcionalitás biztosítása (garantált tudás az osztálynak)  
✅ Több hasonló osztály kezelőfelületének egységesítése  
✅ Statikus típusként használható → „egy kalap alá vehetjük" az implementáló osztályokat  

❌ **NEM** belső szerkezet meghatározásához  
❌ **NEM** az encapsulationt töri fel  
❌ Az interfész a „MIT?" kérdésre válaszol, nem a „HOGYAN?"-ra  

### Különbség az absztrakt osztálytól:
| | Interfész | Absztrakt osztály |
|--|-----------|-----------------|
| Implementáció | ❌ (alap esetben) | ✅ |
| Konstruktor | ❌ | ✅ |
| Mezők | csak `public static final` | bármilyen |
| Öröklés | ✅ többszörös | ❌ egyszeres |

---

## 📦 Beépített interfészek

| Interfész | Mire való |
|-----------|-----------|
| `Comparable` | összehasonlításhoz |
| `Cloneable` | `Object.clone()` engedélyezése (marker) |
| `Serializable` | objektum „sorosítása" (marker) |
| `Runnable` | szálként futtatható |

> **Marker interfész**: Nem ad meg metódust implementálásra, csak „jelöli" az osztályt (pl. JVM számára speciális tudással bír).

---

## 🔧 Interfészek módosítása (default metódusok)

**Probléma:** Ha utólag kibővítünk egy interfészt, az implementáló osztályok „lerokkannak".

**Megoldások:**
1. Hozz létre **új interfészt**, ami az eredetiből örököl + tartalmazza az új metódust
2. Használj **default metódust** (implementáció az interfészben)

```java
public interface Food {
    String whatToEat();
    default Integer howManyCalories() {
        return null;  // alapértelmezett implementáció
    }
}
```

### Ha default metódus többszörösen öröklődik (ütközés):
```java
// 1. Újra absztraktnak jelölöd:
@Override
String whatToEat();

// 2. Meghívod valamelyik ős implementációját:
Parent.super.defaultMethod();
```

---

## 💻 Kód példa (teljes)

```java
public interface Edible { String whatToEat(); }

public interface Delicious extends Edible { int howDelicious(); }

public class Apple implements Delicious {
    @Override public String whatToEat() { return "Apple"; }
    @Override public int howDelicious() { return 10; }
}

public class Spinach implements Edible {
    @Override public String whatToEat() { return "Spinach"; }
}

// Használat:
Edible[] edibleArray = new Edible[3];
Delicious[] deliciousArray = new Delicious[2];
deliciousArray[0] = new Chocolate();
// deliciousArray[2] = new Spinach(); // HIBA! Spinach nem Delicious
edibleArray[2] = new Spinach();       // OK!
```

---

# 4.2 További OOP Lehetőségek

## 📦 Rekordok (Record)

Sok esetben osztályt csak adattárolásra hozunk létre (pl. cím, dátum). Erre való a **record**.

### Hagyományos osztály helyett:
```java
// ÍGY SOKAT KELL ÍRNI:
public final class Rectangle {
    private final double length;
    private final double width;
    public Rectangle(double length, double width) { ... }
    double length() { return this.length; }
    double width() { return this.width; }
    public boolean equals(...) { ... }
    public int hashCode(...) { ... }
    public String toString() { ... }
}
```

```java
// RECORD: TÖMÖR SZINTAXIS!
record Rectangle(double length, double width) { }
```

### Mi jön létre automatikusan?
- `private final` mezők minden fejrészben megadott változóhoz
- `public` getter minden mezőhöz (azonos névvel!)
- Konstruktor (inicializáláshoz)
- `equals()` és `hashCode()` (Object felüldefiniálva)
- `toString()`

### Mit tehetünk rekordban?
✅ Saját konstruktort adhatunk meg  
✅ Lekérdező (getter) metódusok  
✅ Osztályváltozók és osztálymetódusok  
✅ Példánymetódusok  
✅ Interfész implementálás  

### Saját konstruktor (validációval):
```java
record Rectangle(double length, double width) {
    public Rectangle {   // <-- kompakt konstruktor (nincs értékadás!)
        if (length <= 0 || width <= 0) {
            throw new IllegalArgumentException("Invalid dimensions!");
        }
    }
}
```

---

## 🔒 Sealed osztályok (Java 17+)

Lehetőség az **öröklődés finomhangolására**: korlátozhatjuk, hogy kik örökölhetnek belőle.

### Kulcsszavak:
| Kulcsszó | Jelentés |
|----------|---------|
| `sealed` | korlátozott örökölhetőség |
| `permits` | felsorolja az engedélyezett leszármazottakat |
| `final` | nem örökölhető tovább |
| `non-sealed` | bármely osztály örökölhet belőle |

### Szabályok:
- Az engedélyezett osztálynak **ugyanabba a modulba** kell tartoznia
- Az engedélyezett osztálynak **explicit örökölnie** kell
- Az engedélyezett osztálynak **definiálnia kell** a módosítóját (`final` / `sealed` / `non-sealed`)

### Példa:
```java
public sealed class Vehicle permits Car, Truck, Bus { ... }
public sealed interface Service permits Truck, Bus { ... }

public final class Car extends Vehicle { ... }     // nem örökölhető tovább
public sealed class Truck extends Vehicle implements Service permits SpecialTruck { ... }
public non-sealed class Bus extends Vehicle { ... } // bárki örökölhet belőle
```

---

## 🪆 Beágyazott / Belső osztályok

Egy osztályon **belül deklarált** újabb osztály.

- Az **outer class** eléri az **inner class** minden tagját (és fordítva)

### Fajtái:
| Típus | Leírás |
|-------|--------|
| **Statikus (nested)** | Csak szervezésbeli szerepe van; példányosítható a külső nélkül |
| **Nem-statikus (inner)** | Csak a külső példány keretében értelmezhető; fenntart referenciát (`Outer.this`) → potential memory leak! |

```java
class Outer {
    private int a = 5;
    private static int s = 2;

    private class Inner {
        @Override public String toString() { return "Inner: " + a; }
    }

    public static class StaticNested {
        @Override public String toString() { return "Nested: " + s; }
    }
}
```

> ⚠️ Belső osztályok láthatósága lehet `private` és `protected` is (eltérően a top-level osztályoktól)!

---

## 👻 Anonim belső osztályok

Névtelen, ad hoc osztály: definiálás és példányosítás **egy helyen**.

```java
Edible a = new Food() {
    @Override
    public String whatToEat() {
        return "AnonymousFood";
    }
};
```

Ajánlott: rövid eseménykezelőkhöz, ahol a logika egyszer kell.

---

## ⚡ Lambda kifejezések

Egy rövid metódust tartalmazó névtelen osztály **tömörebb** megírása.

```java
// Interfész (csak 1 metódussal – "functional interface"):
interface IExample { int getResult(int x); }

// Anonymous inner class:
new IExample() { int getResult(int x) { return x * x; } }

// Lambda – mind ekvivalens:
(x) -> x * x
(int x) -> x * x
(x) -> { return x * x; }
(int x) -> { return x * x; }
```

> 💡 Lambda = eseményvezérelt rendszerekben nagyon hasznos (GUI, mobil)

---

# 4.3 Reflection API

## 🔍 Mi a Reflection API?

Segítségével **futásidőben** vizsgálhatjuk és módosíthatjuk az alkalmazás szerkezetét.

### Mire jó?
- Milyen tagfüggvényei/tagváltozói vannak egy osztálynak?
- Módosítók, nevek, paraméterek, visszatérési értékek lekérdezése
- Osztályhierarchia bejárása

### Előnyök / Hátrányok:
| Előnyök | Hátrányok |
|---------|-----------|
| Rugalmasság (igény szerinti importálás) | Encapsulation feltörése (privát mezőkhöz is!) |
| Teljes elérhetőség | Kisebb hatékonyság |
| | Biztonsági korlátok (pl. applet) |

---

## ⚙️ Hogyan működik?

Minden szerkezetet **osztályok** reprezentálnak:

| Osztály | Mit reprezentál |
|---------|-----------------|
| `Package` | csomag |
| `Class` | osztály |
| `Field` | tagváltozó |
| `Method` | tagfüggvény |
| `Constructor` | konstruktor |

> **Fontos:** Minden objektum – még a típusok is! Ez Java alapelve.

---

## 🪤 Class objektum megszerzése

### 1. Meglévő objektum alapján:
```java
String str = "ez egy String";
Class c = str.getClass();  // c a String-et reprezentálja (nem str-t!)
// ❌ Primitívhez nem használható!
```

### 2. Statikus befogás:
```java
Class c = boolean.class;
Class d = java.io.PrintStream.class;
Class e = int[][][].class;
// ✅ Primitív típusokhoz is!
```

### 3. Név alapján:
```java
Class c = Class.forName("java.lang.String");
// ❌ Primitívhez nem használható!
```

---

# 4.4 Kivételkezelés OOP Kiegészítése

## 🔄 Ismétlés

- A kivétel az **elvárttól eltérő állapotot** leíró **objektum**
- `catch`-blokk: egy adott típusú **vagy abból származtatott** kivételt kap el
- Ha nincs megfelelő `catch` → egy szinttel feljebb kerül a kivétel
- Ha nincs feljebb (nincs hívó metódus) → a **JVM kapja el**, program megáll
- Ha ellenőrzött kivételt dob egy metódus → **kötelező** a szignatúrába kiírni: `throws IOException`

---

## 🧬 Öröklés és kivételkezelés

### Az örökölt metódus:
```java
public abstract int readIntFromFile(String file) throws IOException, NumberFormatException;
```

### Megvalósításkor:
- ❌ **NEM bővítheted** a dobható kivételek körét!
  - Miért? Mert statikus típusként az ős típusát használva nem tudnánk tudni, milyen kivételt dobhat.
  - Nincs altípusképzés a kivételek bővítésénél!
- ✅ **SZŰKÍTHETED** a dobható kivételek körét!
  - Biztonságos: az ős statikus típusa fel van készítve több kivételre is.

> **Összefoglaló szabály:**  
> Felüldefiniálásnál a leszármazott **kevesebbet dobhat** (szűkíthet), de **többet nem** (nem bővíthet).

---

# 4.5 Gyakorló Feladatok – Összefoglalás

## G04F01 – Interfészek + Iterator
- `ListItem` interfész: `getValue()` metódus
- `Card` osztály: implementálja `ListItem`-et, `Rank` enum mezővel
- `Iterator` interfész: `next()`, `hasNext()`
- `ListIterator` (örököl `Iterator`-ból): `add()`, `remove()`, `set()`, `previous()`, `hasPrevious()`
- `StaticList`: implementálja `ListIterator`-t
- `CardFactory`: `generateCardList()` osztálymetódus

## G04F02 – Logger interfészek
- `Logger` interfész: `log(String)` eljárás
- `LoggerFactory`: `consoleLogger()`, `fileLogger()`, `multiLogger()`
- `ThrowableLogger` interfész: örököl `Logger`-tól, túlterhelés `Throwable` paraméterrel
- Saját kivétel osztály

## G04F03 – Lambda + belső osztályok
- `Operation` interfész: `operate(int, int): int`
- `DescribableOperation` (örököl `Operation`-tól): `describe()` – **default metódus** visszaad "Operation"
- `Operations` osztály: több belső osztály (`static` és nem-`static`)
- Tömb: lambda + névtelen osztály + belső osztály példányok

## G04F04 – Valutaváltó
- `Currency` absztrakt osztály: `rate` (final double), készlet (double)
- `BuyableCurrency`: `buyCurrency(double): double`
- `SellableCurrency`: `sellCurrency(double): double`
- Minden valuta külön osztály (USD, EUR, GBP, CHF, JPY)
- GBP: csak vásárlás (nem eladható), JPY: csak eladás (nem vásárolható)
- USD: max 1000 eladható
- CHF: `boolean enabled` + `trigger()` kapcsoló
- Saját kivételek!

## G04F05 – CompressionAlgorithm
- `CompressionAlgorithm` interfész: 3x `compress()` overload
- `AbstractCompressionAlgorithm` absztrakt osztály: `Getter` belső interfész
- Anonim osztályok használata

## G04F06 – Sokszögek
- `Sokszogek` absztrakt osztály
- `KeruletSzamithato` interfész: `computeKerulet()`
- `KeruletTeruletSzamithato` interfész: `computeTerulet()`
- Háromszög, Négyszög, Kör osztályok
- Kivételek: általános sokszög kivétel + specifikus leszármazottak
- JavaDoc kötelező!

---

# 🧠 ZH Csapdakérdések – Ne feledd!

| Kérdés | Válasz |
|--------|--------|
| Lehet-e interfésznek konstruktora? | ❌ Nem |
| Hány interfészt valósíthat meg egy osztály? | Korlátlan |
| Hány osztályból örökölhet egy osztály? | Csak 1 (egyszeres öröklődés) |
| Mi a default interfész metódus? | Implementációval rendelkező metódus az interfészben |
| Milyen automatikusan adott módosítója van az interfész mezőnek? | `public static final` |
| Milyen automatikusan adott módosítója van az interfész metódusnak? | `public abstract` |
| Felüldefiniálásnál bővíthető-e a throws lista? | ❌ Nem (csak szűkíthető) |
| Mi a diamond problem? | Többszörös örökléskor azonos metódus ütközése |
| Mi a különbség a static nested és az inner class közt? | Inner fenntart referenciát a külső osztályra (Outer.this) |
| Mire való a sealed class? | Korlátozott örökölhetőség (Java 17+) |
| Mire való a record? | Adattárolás, automatikus getter/equals/toString |

---

*Összefoglaló készítve: PPKE Java Programozás – 4. óra anyaga alapján*
