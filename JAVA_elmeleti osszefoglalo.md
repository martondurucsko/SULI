# 3. Hét - Java Programozás Részletes Elméleti Összefoglaló

---

## Tartalomjegyzék

| # | Témakör | Leírás |
|---|---------|--------|
| 1 | [OOP alapfogalmak](#1-oop-alapfogalmak) | Mi az OOP, miért jó? |
| 2 | [Osztályok felépítése](#2-osztályok-felépítése) | Mezők, metódusok, konstruktorok |
| 3 | [A `this` kulcsszó](#3-a-this-kulcsszó) | Az aktuális objektum referenciája |
| 4 | [Konstruktorok részletesen](#4-konstruktorok-részletesen) | Túlterhelés, konstruktor-láncolás |
| 5 | [Getter és Setter metódusok](#5-getter-és-setter-metódusok) | Encapsulation, adatvédelem |
| 6 | [toString() metódus](#6-tostring-metódus) | Szöveges reprezentáció |
| 7 | [Csomagok szervezése](#7-csomagok-szervezése) | package, import, ClassLoader |
| 8 | [Láthatósági módosítók](#8-láthatósági-módosítók) | public, private, protected |
| 9 | [Statikus tagok](#9-statikus-tagok) | static mezők és metódusok |
| 10 | [Inicializáló blokkok](#10-inicializáló-blokkok) | instance init, static init |
| 11 | [Öröklődés](#11-öröklődés) | extends, egyszeres öröklődés |
| 12 | [Konstruktorok öröklődésnél](#12-konstruktorok-öröklődésnél) | super(), végrehajtási sorrend |
| 13 | [Metódus felüldefiniálás](#13-metódus-felüldefiniálás-override) | @Override, dinamikus kötés |
| 14 | [Metódus túlterhelés](#14-metódus-túlterhelés-overloading) | Azonos név, más paraméterek |
| 15 | [Absztrakt osztályok](#15-absztrakt-osztályok) | abstract class, abstract method |
| 16 | [Polimorfizmus](#16-polimorfizmus) | Statikus és dinamikus típus |
| 17 | [Típuskonverzió](#17-típuskonverzió-casting) | Implicit, explicit, instanceof |
| 18 | [Hibakeresés (Debugging)](#18-hibakeresés-debugging) | Breakpoint, watch, léptetés |
| 19 | [Gyors összefoglaló](#19-gyors-összefoglaló-tanuláshoz) | A legfontosabb dolgok |

---

## 1. OOP alapfogalmak

Az **Objektumorientált Programozás (OOP)** nem egyszerűen egy nyelvi funkció, hanem egy **programozási módszertan**.

### Mit jelent az OOP?

- Az adatokat és a rajtuk végzett műveleteket **egységbe zárjuk** (objektumok)
- Az objektumok **egymással üzeneteket váltanak** (metódushívások)
- A kód **újrafelhasználható** és **karbantartható** lesz

### OOP alapfogalmak

| Fogalom | Leírás |
|:---|:---|
| **Osztály (Class)** | Sablon/tervrajz az objektumok számára |
| **Objektum (Object)** | Az osztály egy konkrét példánya |
| **Öröklődés** | Meglévő osztály kibővítése |
| **Polimorfizmus** | Ugyanaz a hívás különböző viselkedést produkál |
| **Egységbezárás** | Adatok és metódusok összekapcsolása |

### Az OOP elemei Javában

```
OOP összetevők:
  ├── Osztályok (változók + metódusok)
  ├── Konstruktorok (inicializálás)
  ├── Destruktorok (Java: nincs, GC kezeli!)
  ├── Inicializáló blokkok
  ├── Láthatósági módosítók
  └── Adattagok elérése
```

> **Fontos:** Az OOP egy gondolkodásmód. A GTK+ például C-ben is OOP elveket követ, bár C nem OOP nyelv!

---

## 2. Osztályok felépítése

### Objektum létrehozása

```java
Car c = new Car();
```

- A `new` kulcsszó memóriát foglal a **heap**-en
- A referencia a stack-en tárolódik, az **objektumra mutat**
- Inicializálatlan lokális referencia: **null** (automatikusan)

### Egy osztály általános felépítése

```java
package vehicles;

public class Car {

    // ── Mezők (instance változók) ──
    private String carType;
    private double consumption;   // liter/100km
    private double odometer;
    private double fuel;
    private double capacity;
    private String licencePlate;

    // ── Konstruktorok ──
    public Car(String carType, double consumption, double capacity) {
        this.carType = carType;
        this.consumption = consumption;
        this.capacity = capacity;
        this.fuel = 0;
        this.odometer = 0;
    }

    // ── Metódusok ──
    public void refuel(double liter) {
        fuel += liter;
    }

    public double fuelNeed(double km) {
        return km * consumption / 100;
    }
}
```

### Egy osztály tartalmazhat

| Elem | Leírás |
|:---|:---|
| **Mezők** | Instance változók (minden objektumnak saját) |
| **Osztályváltozók** | `static` mezők (az osztályhoz tartoznak) |
| **Konstruktorok** | Inicializálásra |
| **Metódusok** | Viselkedés meghatározása |
| **Inicializáló blokkok** | Komplexebb inicializáláshoz |

---

## 3. A `this` kulcsszó

A `this` az **aktuális objektumra mutató referencia**.

### Miért kell a `this`?

Ha a paraméter neve megegyezik a mező nevével, a `this` segít megkülönböztetni:

```java
public class Car {
    private String carType;

    // PROBLÉMA: a paraméter árnyékolja a mezőt
    public void setType(String carType) {
        // carType = carType;  // ROSSZ! Önmagát állítja
        this.carType = carType;  // HELYES! this.carType = a mező
    }
}
```

### A `this()` konstruktor-delegáláshoz

A `this()` hívással egy másik konstruktort lehet meghívni **ugyanabból az osztályból**:

```java
public Car(String type, double consumption) {
    this(type, consumption, 50.0);  // Hívja a 3 paraméteres konstruktort
}

public Car(String type, double consumption, double capacity) {
    this.carType = type;
    this.consumption = consumption;
    this.capacity = capacity;
}
```

> **Szabály:** A `this()` hívásnak a konstruktor **első utasításának** kell lennie!

---

## 4. Konstruktorok részletesen

### Alapszabályok

- A konstruktornak **nincs visszatérési értéke** (még `void` sem!)
- Az osztály neve és a konstruktor neve **megegyezik**
- Ha nem írunk konstruktort → a fordító generál egy **alapértelmezett (no-arg)** konstruktort
- Ha írunk saját konstruktort → az alapértelmezett **NEM generálódik** automatikusan!

### Konstruktor túlterhelés (overloading)

Több konstruktor is lehet, különböző paraméterekkel:

```java
public class Car {
    private String carType;
    private double consumption;
    private double capacity;

    // 1. konstruktor - minden paraméter
    public Car(String carType, double consumption, double capacity) {
        this.carType = carType;
        this.consumption = consumption;
        this.capacity = capacity;
    }

    // 2. konstruktor - alapértelmezett kapacitással (delegál)
    public Car(String carType, double consumption) {
        this(carType, consumption, 50.0);  // delegálás this()-sel
    }

    // 3. konstruktor - csak típus, alapértelmezett értékekkel
    public Car(String carType) {
        this(carType, 7.5, 50.0);
    }
}
```

---

## 5. Getter és Setter metódusok

### Miért ne legyenek a mezők `public`-ok?

**Getter és setter metódusok** használatának okai:

| Ok | Magyarázat |
|:---|:---|
| **Integritás védelem** | Érvénytelen értékek kizárása |
| **Módosítás időzítése** | Mikor engedélyezzük az írást |
| **Értéktartomány ellenőrzés** | Pl. fogyasztás nem lehet negatív |
| **Jövőbeli rugalmasság** | Belső implementáció változhat |

### Getter metódusok

```java
public double getFuel() {
    return fuel;
}

public double getOdometer() {
    return odometer;
}

public double getConsumption() {
    return consumption;
}
```

### Setter metódusok (validációval)

```java
public void setConsumption(double consumption) {
    if (consumption > 0) {           // Validáció: nem lehet negatív!
        this.consumption = consumption;
    }
    // Ha érvénytelen → egyszerűen nem állítjuk be (vagy kivételt dobunk)
}
```

> **Konvenció:** `getX()` a lekérdező, `setX(value)` a beállító. Boolean esetén: `isX()`.

---

## 6. toString() metódus

### Minden Java objektumnak van `toString()`-je

Az `Object` osztályból örökölt metódus, amely az objektum **szöveges reprezentációját** adja vissza.

- Alapértelmezett implementáció: `ClassName@memóriacím` (nem túl hasznos)
- **Felülírva** (override) értelmes szöveget adhatunk vissza

### Felülírás

```java
@Override
public String toString() {
    return "Car{type='" + carType + "', fuel=" + fuel +
           "/" + capacity + ", km=" + odometer + "}";
}
```

### Automatikus hívás

A `toString()` automatikusan hívódik:
- `System.out.println(auto);` → meghívja a `toString()`-t
- String összefűzéskor: `"Auto: " + auto`

```java
Car c = new Car("Toyota", 6.5, 50);
System.out.println(c);  // Car{type='Toyota', fuel=0.0/50.0, km=0.0}
```

---

## 7. Csomagok szervezése

### Csomagok hierarchiája

A csomagok a fájlrendszernek felelnek meg:

```
hu/
  ppke/
    itk/
      java/
        class3/
          vehicles/
            Car.java
            Taxi.java
```

```java
package hu.ppke.itk.java.class3.vehicles;

public class Car { ... }
```

### Osztályok használata más csomagokból

**3 módszer:**

```java
// 1. Teljes minősítéssel (import nélkül)
java.util.ArrayList<String> lista = new java.util.ArrayList<>();

// 2. Egy osztály importálása
import java.util.ArrayList;
ArrayList<String> lista = new ArrayList<>();

// 3. Egész csomag importálása
import java.util.*;
ArrayList<String> lista = new ArrayList<>();
```

### ClassLoader és Class objektum

```java
// Osztály explicit betöltése futásidőben
Class<?> cls = Class.forName("vehicles.Car");
```

- A JVM a `.class` fájlokat a **ClassLoader** tölti be
- `java.lang.Class` tartalmaz minden információt az osztályról

---

## 8. Láthatósági módosítók

### Átfogó táblázat

#### Osztályokra

| Módosító | Mit jelent? |
|:---|:---|
| `public` | Bárhonnan elérhető |
| `final` | Nem lehet tovább örökölni |
| `abstract` | Nem példányosítható, absztrakt metódusai vannak |
| *(alapért.)* | Csak a csomagon belül látható |

#### Mezőkre és metódusokra

| Módosító | Osztály | Csomag | Gyermek | Mindenki |
|:---|:---:|:---:|:---:|:---:|
| `private` | ✓ | | | |
| *(alapért.)* | ✓ | ✓ | | |
| `protected` | ✓ | ✓ | ✓ | |
| `public` | ✓ | ✓ | ✓ | ✓ |

#### Metódusokra specifikus módosítók

| Módosító | Leírás |
|:---|:---|
| `static` | Példányosítás nélkül elérhető (osztálymetódus) |
| `final` | Nem lehet felüldefiniálni (override-olni) |
| `abstract` | Nincs törzse, a leszármazottnak kell megvalósítani |
| `synchronized` | Szálak közötti szinkronizáció |

---

## 9. Statikus tagok

### Statikus mezők (osztályváltozók)

- A **statikus mező az osztályhoz** tartozik, nem az egyes objektumhoz
- **Minden példány ugyanazt** a statikus mezőt látja
- `ClassName.staticField` formával érhető el

```java
public class Car {
    // Statikus mező: minden autóra ugyanaz az üzemanyagár
    public static double fuelPrice = 650.0;

    // Instance mező: minden autónak saját értéke van
    private double fuel;
}

// Használat:
System.out.println(Car.fuelPrice);   // 650.0
Car.fuelPrice = 700.0;               // Minden autóra hat!
```

### Statikus metódusok (osztálymetódusok)

- Nem kell példányosítani a híváshoz
- **Csak statikus tagokhoz** férhetnek hozzá!
- Nem használhatják a `this` kulcsszót

```java
public class Car {
    private static double fuelPrice = 650.0;

    public static double fuelCost(int liter) {
        return liter * fuelPrice;   // OK: statikus mezőhöz fér
        // return this.fuel;        // HIBA! Nincs 'this' statikus metódusban
    }
}

// Hívás:
double koltseg = Car.fuelCost(40);
```

---

## 10. Inicializáló blokkok

### Instance inicializáló blokk

- **Minden objektum létrehozásakor** lefut (a konstruktor előtt)
- Komplex mezőinicializáláshoz hasznos

```java
public class Car {
    private int[] history;

    // Instance inicializáló blokk
    {
        history = new int[10];
        for (int i = 0; i < history.length; i++) {
            history[i] = 0;
        }
    }

    public Car() {
        // A blokk már lefutott ide érkezéskor
    }
}
```

### Statikus inicializáló blokk

- **Egyszer fut le**, amikor az osztály betöltődik a JVM-be
- Statikus mezők inicializálásához

```java
public class MathHelper {
    public static final int[] FACTORIAL = new int[10];

    // Statikus inicializáló blokk
    static {
        FACTORIAL[0] = 1;
        for (int i = 1; i < FACTORIAL.length; i++) {
            FACTORIAL[i] = FACTORIAL[i-1] * i;
        }
    }
}
```

### Végrehajtási sorrend

```
1. Statikus inicializáló blokk (csak egyszer, osztály betöltésekor)
2. Instance inicializáló blokk (minden példányosításkor)
3. Konstruktor törzse
```

---

## 11. Öröklődés

### Az öröklődés alapgondolata

Ha egy új osztálynak **hasonló viselkedése** van, mint egy meglévőnek, nem kell mindent újraírni!

**Példa:** A `Taxi` osztálynak minden megvan, ami a `Car`-nak, PLUSZ saját dolgai vannak.

```
Vehicle (általános)
    └── Car (személyautó)
          └── Taxi (taxi)
                └── Bus (busz)
```

### Öröklődés szintaxisa

```java
public class Taxi extends Car {
    // A Taxi megkapja a Car összes (nem private) tagját!
    // + Hozzáadhat saját mezőket és metódusokat
}
```

### Az `extends` szabályai

| Szabály | Leírás |
|:---|:---|
| **Egyszeres öröklődés** | Egy osztálynak csak **egy** szülője lehet |
| **Implicit Object** | Ha nem írunk `extends`-t → automatikusan `extends Object` |
| **Is-a kapcsolat** | A `Taxi` *egy* `Car` (minden Taxi autó, de nem minden autó taxi) |
| **Protected hozzáférés** | A gyermek látja a szülő `protected` tagjait |

### Az Object osztály

**Minden Java osztály** az `Object` osztályból örökli:

| Metódus | Leírás |
|:---|:---|
| `toString()` | Szöveges reprezentáció |
| `equals()` | Egyenlőségvizsgálat |
| `hashCode()` | Hash érték (pl. HashMap-hez) |

---

## 12. Konstruktorok öröklődésnél

### A `super()` hívás

A gyermekosztály konstruktora **kötelezően meg kell hívja** a szülő konstruktorát:

```java
public class Car {
    protected String carType;
    protected double consumption;

    public Car(String carType, double consumption) {
        this.carType = carType;
        this.consumption = consumption;
    }
}

public class Taxi extends Car {
    private double pricePerKm;

    public Taxi(String carType, double consumption, double pricePerKm) {
        super(carType, consumption);   // Szülő konstruktor hívása - KÖTELEZŐ ELSŐ!
        this.pricePerKm = pricePerKm; // Saját mező inicializálása
    }
}
```

> **Szabály:** A `super()` hívásnak a konstruktor **legelső utasításának** kell lennie!
> Ha nem írunk `super()`-t → a fordító automatikusan hozzáadja a no-arg `super()`-t.

### Végrehajtási sorrend öröklődésnél

```java
class A {
    static { System.out.println("A static"); }   // 1.
    { System.out.println("A instance"); }        // 3.
    A() { System.out.println("A constructor"); } // 4.
}

class B extends A {
    static { System.out.println("B static"); }   // 2.
    { System.out.println("B instance"); }        // 5.
    B() { System.out.println("B constructor"); } // 6.
}
```

**Kimenet B b = new B() esetén:**
```
A static        ← 1. A osztály statikus blokkja (A betöltésekor)
B static        ← 2. B osztály statikus blokkja (B betöltésekor)
A instance      ← 3. A instance inicializáló blokkja
A constructor   ← 4. A konstruktora
B instance      ← 5. B instance inicializáló blokkja
B constructor   ← 6. B konstruktora
```

---

## 13. Metódus felüldefiniálás (@Override)

### Mi az override?

A gyermekosztály **újradefiniálhatja** a szülőosztály metódusait - ugyanolyan névvel és paraméterekkel, de **más törzssel**.

```java
public class Car {
    public String toString() {
        return "Car: " + carType;
    }

    public double cost(double km) {
        return fuelNeed(km) * Car.fuelPrice;
    }
}

public class Taxi extends Car {
    private double pricePerKm;

    @Override
    public String toString() {
        return "Taxi: " + carType + ", " + pricePerKm + " Ft/km";
    }

    @Override
    public double cost(double km) {
        return km * pricePerKm;  // Taxi máshogy számít!
    }
}
```

### A `@Override` annotáció

- **Nem kötelező**, de **erősen ajánlott**
- A fordító ellenőrzi, hogy valóban létezik ilyen szülő metódus
- Megvéd elgépeléstől: ha rosszul írjuk a nevet → fordítási hiba

### Szülő metódus elérése (`super`)

```java
@Override
public double cost(double km) {
    double alapKoltseg = super.cost(km);  // Szülő implementációja
    return alapKoltseg + extraFee;
}
```

---

## 14. Metódus túlterhelés (Overloading)

### Override vs. Overload

| | Override (felüldefiniálás) | Overload (túlterhelés) |
|:---|:---|:---|
| **Osztály** | Szülő + gyermek | Ugyanaz az osztály |
| **Metódusnév** | Ugyanaz | Ugyanaz |
| **Paraméterek** | Ugyanazok | **Különbözőek** |
| **Visszatérés** | Ugyanaz (vagy altípus) | Bármi |

### Példa: Overloading

```java
public class Taxi extends Car {

    // 1. verzió: km alapján
    public double transfer(double km) {
        go(km);
        return cost(km);
    }

    // 2. verzió: km és személyek száma alapján (OVERLOAD!)
    public double transfer(double km, int numPersons) {
        go(km);
        return cost(km) / numPersons;
    }
}
```

> A fordító a **paraméterek száma és típusa** alapján dönti el, melyik metódust hívja!

---

## 15. Absztrakt osztályok

### Mikor kell absztrakt osztály?

Ha egy osztály **általánosítást** reprezentál, amelyet **nem érdemes** (vagy nem lehet) közvetlenül példányosítani.

**Példa:** `Vehicle` - járművet nem gyártanak, de autót, taxidat, buszt igen!

```java
public abstract class Vehicle {
    protected String type;
    protected double odometer;

    public Vehicle(String type) {
        this.type = type;
        this.odometer = 0;
    }

    // Absztrakt metódus: NINCS törzse! A leszármazott KÖTELES megvalósítani!
    public abstract void go(double km);

    // Konkrét metódus: örökölhető
    public double getOdometer() {
        return odometer;
    }

    @Override
    public String toString() {
        return type + " [" + odometer + " km]";
    }
}
```

### Absztrakt osztály szabályai

| Szabály | Leírás |
|:---|:---|
| `abstract class` | Nem példányosítható (`new Vehicle()` → HIBA!) |
| `abstract` metódus | Nincs törzse (`{}`), a gyermeknek kell megvalósítani |
| Örökölhető | Absztrakt osztályból lehet örökölni |
| Konkrét metódus | Absztrakt osztálynak lehetnek kész metódusai is |
| `final abstract` | **TILOS!** (`final` = nem örökölhető, `abstract` = örökölni KELL) |

### A Bicycle mint példa

```java
public class Bicycle extends Vehicle {
    private int gears;

    public Bicycle(int gears) {
        super("Bicycle");
        this.gears = gears;
    }

    // Kötelező megvalósítani az absztrakt metódust!
    @Override
    public void go(double km) {
        odometer += km;
        System.out.println("Pedaling " + km + " km");
    }
}
```

---

## 16. Polimorfizmus

### Mi a polimorfizmus?

**Polimorfizmus** = "sokformájúság" = ugyanaz a kód különböző típusú objektumokkal is működik.

```java
Vehicle v1 = new Car("Toyota", 6.5, 50);
Vehicle v2 = new Taxi("Mercedes", 8.0, 500);
Vehicle v3 = new Bicycle(21);

// Ugyanaz a hívás - különböző viselkedés!
v1.go(100);   // Car.go() fut
v2.go(100);   // Taxi.go() fut (ha van override)
v3.go(100);   // Bicycle.go() fut
```

### Statikus típus vs. Dinamikus típus

| Fogalom | Mikor? | Leírás |
|:---|:---|:---|
| **Statikus típus** | Fordítási idő | A deklarált típus (pl. `Vehicle`) |
| **Dinamikus típus** | Futási idő | A tényleges objektum típusa (pl. `Taxi`) |

```java
Vehicle v = new Taxi("Mercedes", 8.0, 500);
//  ^                  ^
// Statikus típus   Dinamikus típus
```

**Szabály:** A dinamikus típus **egyenlő vagy specifikusabb** mint a statikus típus.

### Dinamikus kötés (Dynamic Binding)

A Java **automatikusan** megkeresi a helyes metódus implementációt futáskor:

```java
Vehicle[] jarművek = {
    new Car("Toyota", 6.5, 50),
    new Taxi("Mercedes", 8.0, 500),
    new Bicycle(21)
};

for (Vehicle v : jarművek) {
    v.go(10);    // Mindig a TÉNYLEGES típus metódusa fut!
}
```

> **Java-ban minden metódus automatikusan "virtuális"** (C++-ban explicit `virtual` kulcsszó kell)

---

## 17. Típuskonverzió (Casting)

### Implicit konverzió (Upcasting)

Gyermek → Szülő típus, **automatikus**, adatvesztés nélkül:

```java
Taxi t = new Taxi("Mercedes", 8.0, 500);
Car c = t;        // OK! Taxi IS-A Car
Vehicle v = t;    // OK! Taxi IS-A Vehicle
```

### Explicit konverzió (Downcasting)

Szülő → Gyermek típus, **manuális castolás**, futási hiba lehetséges:

```java
Vehicle v = new Taxi("Mercedes", 8.0, 500);

Taxi t = (Taxi) v;      // OK! v dinamikus típusa valóban Taxi
Car c = (Car) v;        // OK! Taxi IS-A Car

// VESZÉLY:
Bus b = (Bus) v;        // Fordítási hiba esetleg nem, de:
                        // ClassCastException futáskor! (v nem Bus)
```

### Az `instanceof` operátor

Biztonságos ellenőrzés a castolás előtt:

```java
Vehicle v = new Taxi("Mercedes", 8.0, 500);

if (v instanceof Taxi) {
    Taxi t = (Taxi) v;       // Biztonságos!
    t.transfer(100);
}

if (v instanceof Bus) {
    // Ide NEM jut el! Taxi nem Bus.
}
```

### Modern szintaxis (Java 16+)

```java
if (v instanceof Taxi t) {
    t.transfer(100);    // Egyből használható, nincs szükség castolásra!
}
```

---

## 18. Hibakeresés (Debugging)

### Miért hasznos a debugger?

- Lépésről-lépésre követhetjük a program futását
- Valós időben ellenőrizhetjük a változók értékeit
- Megérthetjük a referenciák és objektumok viszonyát

### Breakpoint (töréspontok)

A **breakpoint** megállítja a programot egy adott sornál.

```java
public static void main(String[] args) {
    Car c = new Car("Toyota", 6.5, 50);  // ← Breakpoint ide
    c.refuel(30);
    c.go(100);  // ← Breakpoint ide
}
```

### Debugger léptetési módok

| Parancs | Mit csinál? |
|:---|:---|
| **Step Into** | Belép a hívott metódusba |
| **Step Over** | Végrehajtja a sort, de nem lép be a metódusba |
| **Step Return** | Kilép az aktuális metódusból |
| **Run to Cursor** | A kurzorig fut |

### Watch points

- Figyelhetünk egy változó értékét
- A debugger megállítja a programot, ha a változó értéke megváltozik

> **Tipp:** Különösen hasznos az **immutable** osztályok és a **referenciák** viselkedésének megértéséhez. IntelliJ IDEA-ban: jobb klikk a sorra → `Add Breakpoint`, majd `Debug` gomb.

---

## 19. Gyors összefoglaló tanuláshoz

### A 10 legfontosabb dolog, amit tudni KELL

| # | Téma | Lényeg |
|:---:|:---|:---|
| 1 | **OOP alapok** | Objektum = adat + metódus; osztály = sablon |
| 2 | **`this`** | Az aktuális objektumra mutat; `this()` = konstruktor-delegálás |
| 3 | **Konstruktor** | Nincs visszatérési értéke; ha nem írsz, generálódik no-arg |
| 4 | **Getter/Setter** | Privát mezők védelme; validálás lehetséges |
| 5 | **`static`** | Az osztályhoz tartozik, nem az objektumhoz |
| 6 | **Öröklődés** | `extends`; egyszeres; is-a kapcsolat; `super()` elsőként |
| 7 | **`@Override`** | Szülő metódus felüldefiniálása; annotáció ajánlott |
| 8 | **`abstract`** | Nem példányosítható; leszármazott köteles megvalósítani |
| 9 | **Polimorfizmus** | Dinamikus kötés; statikus vs. dinamikus típus |
| 10 | **Casting** | Implicit (up) automatikus; explicit (down) `instanceof` ellenőrzéssel |

### Tipikus buktatók

| Buktató | Mi történik? | Megoldás |
|:---|:---|:---|
| `new AbstractClass()` | Fordítási hiba | Absztrakt osztályt nem lehet példányosítani |
| Hiányzó `super()` | Auto no-arg super, de ha nincs → fordítási hiba | Mindig hívd explicit a megfelelő szülő konstruktort |
| `static` metódusból `this` | Fordítási hiba | Statikus metódusban nincs `this`! |
| Rossz downcast | `ClassCastException` futáskor | Mindig `instanceof`-val ellenőrizz előtte |
| `@Override` hiánya | Typo esetén nincs hibajelzés | Mindig írd ki az `@Override`-ot! |
| `private` mező öröklődésnél | Gyermek nem látja | Használj `protected`-et, ha szükséges |

### Osztályhierarchia a 3. héten

```
Object
  └── Vehicle (abstract)
        ├── Car
        │     └── Taxi
        │           └── Bus (final)
        └── Bicycle
```

---

> **Tipp a tanuláshoz:** Rajzold le az osztályhierarchiát papírra! Próbáld ki IntelliJ IDEA debuggerben a `Car → Taxi → Bus` öröklési láncot, és figyeld meg, hogy melyik `toString()` vagy `cost()` metódus hívódik az egyes objektumokon. A polimorfizmus megértéséhez különösen hasznos a `Vehicle[]` tömbbe rakott különböző típusú objektumok kipróbálása!
