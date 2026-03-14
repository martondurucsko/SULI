# Java elméleti ZH – Kérdéssor

> Témák: Java1–Java5 (JVM, IO, OOP, Interfészek, Generikusok, Collections, Stream)
> Típusok: Igaz/Hamis, Többválasztós, Kódolvasás, Hibakeresés, Párosítás, Rövid magyarázat

---

## 1. szakasz – JVM, Java alapok

### 1. kérdés — Igaz/Hamis

Jelöljük meg, hogy az alábbi állítások igazak vagy hamisak!

a) A JVM JIT módszere a forráskódot sorról-sorra fordítja natív kóddá futás közben.

b) A Hot Spot Detection a ritkán használt kódrészleteket fordítja natív kóddá, hogy időt takarítson meg.

c) Ugyanaz a `.class` fájl futtatható Windows-on és Linux-on is, amennyiben mindkét platformra létezik JVM. 
IGAZ

d) A `javap` eszköz arra való, hogy Java forráskódból bájtkódot készítsen.
HAMIS
e) Kotlin és Scala forráskódból is generálható JVM által értelmezhető bájtkód.

---

### 2. kérdés — Többválasztós

Mi a helyes magyarázata annak, hogy az alábbi kód **nem** módosítja a `main`-beli `i2` értékét?

```java
public static void param(int primitive, Integer reference) {
    reference++;
}
public static void main(String[] arg) {
    Integer i2 = 5;
    param(0, i2);
    System.out.println(i2); // 5
}
```

a) Mert az `Integer` típus `final`, ezért a referenciát nem lehet megváltoztatni.

b) Mert a `reference++` egy új `Integer` példányt hoz létre (`reference = Integer.valueOf(reference.intValue() + 1)`), és a lokális paraméter kap új referenciát, az eredeti `i2` nem.

c) Mert a `static` metódusban a paraméterátadás referencia szerinti, de az `Integer` immutable, ezért az érték nem változik.

d) Mert a `++` operátor `Integer` típusnál nem megvalósított, ezért a sor nem fut le.

---

### 3. kérdés — Hibakeresés

Az alábbi kódban van-e hiba? Ha igen, pontosan mi, és miért?

```java
public class Main {
    public static void main(String[] args) {
        final int[] arr = {1, 2, 3};
        arr[0] = 99;
        arr = new int[]{4, 5, 6};
    }
}
```

---

### 4. kérdés — Igaz/Hamis

a) Java-ban egy `final` osztályváltozó értéke megadható a statikus inicializáló blokkban is, nem csak a deklarációnál.

b) A példányinicializáló blokk az ősosztály konstruktora **előtt** fut le.

c) Egy osztályban több inicializáló blokk is lehet.

d) A `static` inicializáló blokk az osztály legelső példányosításakor fut le.

e) A lokális változók – ellentétben a példányváltozókkal – nem inicializálódnak automatikusan `null`-ra.

---

### 5. kérdés — Kódolvasás

Mi az alábbi kód kimenete?

```java
public class A {
    static { System.out.println("A static"); }
    { System.out.println("A példány"); }
    A() { System.out.println("A konstruktor"); }
}

public class B extends A {
    static { System.out.println("B static"); }
    { System.out.println("B példány"); }
    B() { System.out.println("B konstruktor"); }
    public static void main(String[] args) {
        new B();
    }
}
```

---

### 6. kérdés — Rövid magyarázat

Mi a különbség a `==` operátor és az `equals()` metódus között String objektumok összehasonlításánál? Miért lehet, hogy `"A" == "A"` mégis `true`-t ad vissza?

---

## 2. szakasz – IO, String, RegEx, Kivételkezelés

### 7. kérdés — Többválasztós

Melyik állítás igaz a `try-with-resources` szerkezetre?

a) Csak egy erőforrást lehet megadni a zárójelben.

b) Az erőforrás automatikusan lezáródik, ha az osztálya implementálja az `AutoCloseable` interfészt – kivétel esetén is.

c) A `try-with-resources` esetén nem lehet `catch` ág.

d) Az erőforrást a `try` blokkon kívül kell deklarálni, csak a zárójelben hivatkozni rá.

---

### 8. kérdés — Hibakeresés

Az alábbi `catch` blokkok sorrendje helyes-e? Indokold!

```java
try {
    throw new IOException("hiba");
} catch (Exception e) {
    System.out.println("Exception");
} catch (IOException e) {
    System.out.println("IOException");
}
```

---

### 9. kérdés — Igaz/Hamis

a) A `RuntimeException` ellenőrzött kivétel, ezért a `throws` kulcsszóval jelezni kell a metódus szignatúrájában.

b) A `finally` blokk még `return` utasítás esetén is lefut.

c) Egy `catch` ágban többféle kivételtípust is meg lehet adni a `|` operátorral.

d) Az `Error` típusú kivételeket nem kötelező elkapni, de elkapni is lehet.

e) A `main` metódus dobhat `Exception`-t – ez szintaktikailag helyes és szemantikailag is ajánlott megoldás.

---

### 10. kérdés — Kódolvasás

Mi az alábbi regex kifejezés jelentése, és mire illeszkedik?

```
([a-z]{3})([a-z]{2})((([1-9])+([0-9])*))?
```

Illeszkedik-e az alábbi stringekre? (Igen/Nem)

a) `kelad`
b) `horis45`
c) `ada`
d) `hor0is`
e) `abcde12`

---

### 11. kérdés — Többválasztós

Melyik kód **nem fordul le**?

a)
```java
public void m() throws ArithmeticException {
    int i = 10 / 0;
}
```

b)
```java
public void m() {
    int i = 10 / 0;
}
```

c)
```java
public void m() {
    var br = new BufferedReader(new FileReader("f.txt"));
}
```

d)
```java
public void m() throws IOException {
    var br = new BufferedReader(new FileReader("f.txt"));
}
```

---

### 12. kérdés — Rövid magyarázat

Mi a különbség a `String`, `StringBuilder` és `StringBuffer` osztályok között? Mikor melyiket érdemes használni?

---

### 13. kérdés — Igaz/Hamis

a) A `BufferedReader` közvetlenül `InputStream`-ből hozható létre.

b) A `System.out` típusa `PrintStream`.

c) Ha nem zárjuk le a kimeneti adatfolyamot (`close()`), előfordulhat, hogy az adatok nem kerülnek ki a fájlba.

d) A `flush()` lezárja az adatfolyamot és kiüríti a puffert.

e) A `File` osztály példányosításakor a fájlnak ténylegesen léteznie kell a fájlrendszeren.

---

## 3. szakasz – OOP, öröklés, polimorfizmus

### 14. kérdés — Kódolvasás

Mi az alábbi kód kimenete? Indokold!

```java
class Animal {
    public String sound() { return "..."; }
    public String toString() { return "Animal: " + sound(); }
}

class Dog extends Animal {
    @Override
    public String sound() { return "Vau"; }
}

public class Main {
    public static void main(String[] args) {
        Animal a = new Dog();
        System.out.println(a.toString());
    }
}
```

---

### 15. kérdés — Hibakeresés

Az alábbi kódban pontosan hány hiba van és mik azok?

```java
public class Vehicle {
    private String type;
    private int capacity;

    public Vehicle(String type, int capacity) {
        this.type = type;
        this.capacity = capacity;
    }
}

public class Bike extends Vehicle {
    private double wheelSize;

    public Bike(String type, double wheelSize) {
        this.wheelSize = wheelSize;
        this.type = type;
    }
}
```

---

### 16. kérdés — Többválasztós

Mi a kimenete az alábbi kódnak?

```java
class Apple {
    protected static int kgPrice = 200;
    public static int getKgPrice() { return kgPrice; }
}
class GreenApple extends Apple {
    protected static int kgPrice = 500;
    public static int getKgPrice() { return kgPrice; }
}
public class Main {
    public static void main(String[] args) {
        Apple a = new GreenApple();
        System.out.println(a.getKgPrice());
    }
}
```

a) 500 – mert a dinamikus típus `GreenApple`

b) 200 – mert statikus metódusoknál nincs dinamikus kötés, a statikus típus (`Apple`) dönti el

c) Fordítási hiba

d) 0 – mert a statikus változó nem inicializálódik

---

### 17. kérdés — Igaz/Hamis

a) Felüldefiniáláskor a leszármazott metódus **bővítheti** az ellenőrzött dobható kivételek körét.

b) Felüldefiniáláskor a leszármazott metódus láthatósága nem szűkíthető.

c) Egy `final` osztályból nem lehet örökölni, de példányosítani lehet.

d) `abstract` osztályból lehet példányosítani, ha minden absztrakt metódusa implementálva van.

e) A konstruktor is örökölhető, ha `protected` láthatóságú.

---

### 18. kérdés — Rövid magyarázat

Mi a különbség a **felüldefiniálás** (overriding) és az **elfedés** (hiding) között statikus metódusok esetén? Mit jelent, hogy „nincs dinamikus kötés elfedésnél"?

---

### 19. kérdés — Kódolvasás

Mi az alábbi kód kimenete, és miért?

```java
class Base {
    public Base() {
        init();
    }
    public void init() {
        System.out.println("Base.init()");
    }
}

class Child extends Base {
    private int value = 10;

    @Override
    public void init() {
        System.out.println("Child.init(), value=" + value);
    }
}

public class Main {
    public static void main(String[] args) {
        new Child();
    }
}
```

---

### 20. kérdés — Hibakeresés

Az alábbi `instanceof`-os kód le fog fordulni? Futás közben mi történik?

```java
Car b = new Car("Lada", 10, 5, 0);
if (b instanceof Taxi) {
    Taxi t = (Taxi) b;
    t.transfer(100);
}
```

---

### 21. kérdés — Többválasztós

Melyik állítás igaz az alábbi kód esetén?

```java
Vehicle[] arr = new Vehicle[3];
arr[0] = new Car("Lada", 10, 4, 0);
arr[1] = new Taxi("Honda", 5, 5, 300);
arr[2] = new Bicycle("Trek");

for (Vehicle v : arr) {
    v.go(10);
}
```

a) Fordítási hiba, mert `Vehicle` absztrakt és nem lehet tömbben tárolni.

b) Lefut, minden jármű a saját `go()` implementációját futtatja dinamikus kötés miatt.

c) Futás közben `ClassCastException` keletkezik, mert különböző típusok vannak a tömbben.

d) Csak akkor fut le, ha a `Vehicle` osztály nem absztrakt.

---

## 4. szakasz – Interfészek, Rekordok, Sealed, Lambda, Reflection

### 22. kérdés — Hibakeresés

Az alábbi interfész definíciók közül melyik(ek) helytelenek és miért?

```java
// a)
public interface A {
    public abstract default void doSomething() { }
}

// b)
public interface B {
    public static final int X = 5;
    int Y = 10;
}

// c)
public interface C {
    public C(int x);
}

// d)
public interface D {
    void method();
    default void helper() { method(); }
}

// e)
public interface E {
    abstract void m();
    default void m() { }
}
```

---

### 23. kérdés — Igaz/Hamis

a) Egy osztály egyszerre több interfészt is megvalósíthat.

b) Egy interfész örökölhet több másik interfésztől is.

c) Ha egy `default` metódus két interfészből is öröklődik, a fordító automatikusan az egyik örökölt implementációt választja.

d) Lambda kifejezéssel csak olyan interfész valósítható meg, amelynek pontosan egy absztrakt metódusa van (funkcionális interfész).

e) Egy rekord `record` nem implementálhat interfészt.

---

### 24. kérdés — Kódolvasás

Mi az alábbi kód kimenete?

```java
interface Greeter {
    String greet(String name);
}

public class Main {
    public static void main(String[] args) {
        Greeter g = name -> "Hello, " + name + "!";
        System.out.println(g.greet("Világ"));

        Greeter g2 = (String name) -> {
            return name.toUpperCase();
        };
        System.out.println(g2.greet("világ"));
    }
}
```

---

### 25. kérdés — Többválasztós

Adott az alábbi sealed osztályhierarchia:

```java
public sealed class Shape permits Circle, Rectangle, Triangle { }
public final class Circle extends Shape { }
public non-sealed class Rectangle extends Shape { }
public sealed class Triangle extends Shape permits RightTriangle { }
```

Melyik osztálydeklaráció érvényes?

a) `public class Square extends Rectangle { }` — érvényes

b) `public class EquilateralTriangle extends Triangle { }` — érvényes

c) `public class Oval extends Shape { }` — érvényes

d) `public class SmallCircle extends Circle { }` — érvényes

---

### 26. kérdés — Rövid magyarázat

Mi a különbség a **statikus beágyazott osztály** (static nested class) és a **nem-statikus belső osztály** (inner class) között? Mikor melyiket érdemes választani?

---

### 27. kérdés — Igaz/Hamis

a) A Reflection API segítségével privát mezők értékét is le lehet kérdezni és módosítani.

b) `Class.forName("java.lang.String")` primitív típushoz is használható.

c) A `str.getClass()` hívás után a visszakapott `Class` objektum hivatkozik az eredeti `str` példányra.

d) Reflection használata lassítja a kód végrehajtását.

e) Reflection-nel futás közben új csomagokat és osztályokat lehet betölteni.

---

### 28. kérdés — Hibakeresés

Mi a hiba az alábbi rekordban?

```java
record Point(double x, double y) {
    private double x;

    public Point {
        if (x < 0 || y < 0) throw new IllegalArgumentException();
        this.x = x;
        this.y = y;
    }
}
```

---

### 29. kérdés — Kódolvasás

Az alábbi kód le fog-e fordulni? Ha igen, mi a kimenete?

```java
interface Food {
    String whatToEat();
    default Integer howManyCalories() { return null; }
}

interface Delicious extends Food {
    int howDelicious();
}

class Chocolate implements Delicious {
    public String whatToEat() { return "Chocolate"; }
    public int howDelicious() { return 10; }
}

public class Main {
    public static void main(String[] args) {
        Food f = new Chocolate();
        System.out.println(f.howManyCalories());
        System.out.println(f.howDelicious()); // ?
    }
}
```

---

## 5. szakasz – Generikusok, Collections, Stream

### 30. kérdés — Hibakeresés

Az alábbi kódok közül melyik NEM fordul le? Miért?

```java
// a)
Box<String> b1 = new Box<>("hello");     // Box<T extends Number>

// b)
Box<Integer> b2 = new Box<>(10);

// c)
Box<Box<Integer>> b3 = new Box<>(new Box<>(5));

// d)
Box<Float> b4 = new Box<>(3);            // 3 int literál

// e)
Box<?> b5 = new Box<>(42);
```

---

### 31. kérdés — Igaz/Hamis

a) A Type Erasure miatt futás közben nem lehet megtudni, hogy egy generikus osztály milyen aktuális típusparaméterrel lett példányosítva.

b) A `List<Integer>` altípusa a `List<Object>`-nek.

c) A `List<Integer>` altípusa a `List<?>`-nek.

d) `List<? extends Number>` esetén bármilyen `Number` leszármazottból álló listát át lehet adni paraméterként.

e) `List<? super Integer>` esetén `Integer`-eket bele lehet tenni a listába.

---

### 32. kérdés — Többválasztós

Miért nem fordul le az alábbi kód?

```java
public class Container<T> {
    public void create() {
        T obj = new T(); // ???
    }
}
```

a) Mert `T` típusú változót nem lehet deklarálni metóduson belül.

b) Mert Type Erasure miatt a fordító nem tudja, milyen konstruktort kell meghívni `T`-re.

c) Mert generikus osztályban nem lehet metódust definiálni.

d) Mert a `new` kulcsszó generikuson belül tilos.

---

### 33. kérdés — Párosítás

Párosítsd össze a leírt problémát a legmegfelelőbb Collection implementációval!

| Leírás | Implementáció |
|---|---|
| 1. Elemeket szúrunk be és törlünk mindkét végéről hatékonyan | A. `TreeMap` |
| 2. Kulcs alapján O(1) átlagos elérési idő, sorrend nem számít | B. `LinkedHashMap` |
| 3. Kulcs alapján bejárás **növekvő** sorrendben | C. `ArrayDeque` |
| 4. Kulcs alapján bejárás **beszúrási** sorrendben | D. `HashMap` |
| 5. Index alapján O(1) véletlen hozzáférés, fix méretű adatokhoz | E. `ArrayList` |

---

### 34. kérdés — Kódolvasás

Mi az alábbi stream lánc végeredménye?

```java
List<String> words = Arrays.asList("alma", "körte", "ananász", "barack", "avokádó");

long result = words.stream()
    .filter(w -> w.startsWith("a"))
    .map(String::toUpperCase)
    .distinct()
    .count();

System.out.println(result);
```

---

### 35. kérdés — Hibakeresés

Az alábbi kódban mi a hiba?

```java
public void processObjectList(List<Object> list) {
    list.forEach(System.out::println);
}

public static void main(String[] args) {
    List<Integer> numbers = List.of(1, 2, 3);
    processObjectList(numbers); // ???
}
```

---

### 36. kérdés — Igaz/Hamis

a) A `TreeMap` piros-fekete fa implementációval működik.

b) A `HashMap` garantálja a bejárási sorrendet.

c) A `LinkedList` implementálja mind a `List`, mind a `Deque` interfészt.

d) Az `ArrayList` közepébe való beszúrás O(1) művelet.

e) A `Collection` interfész a `Map` interfész leszármazottja.

---

### 37. kérdés — Kódolvasás

Mi a kimenete az alábbi kódnak?

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6);

Optional<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)
    .reduce((a, b) -> a * b);

System.out.println(result.orElse(-1));
```

---

### 38. kérdés — Rövid magyarázat

Mi a különbség a `Comparable` és a `Comparator` interfész között? Mikor melyiket érdemes használni? Adj egy-egy példát!

---

### 39. kérdés — Többválasztós

Melyik wildcard korlátozás helyes, ha egy metódusban csak **olvasni** szeretnénk a lista elemeit, és `Number` leszármazottakat várunk?

a) `List<Number>`
b) `List<? super Number>`
c) `List<? extends Number>`
d) `List<?>`

---

### 40. kérdés — Hibakeresés

Az alábbi `Comparator`-os kód helyesen rendez-e? Ha nem, mi a probléma?

```java
List<String> names = new ArrayList<>(Arrays.asList("Béla", "Anna", "Cecília"));
names.sort((a, b) -> a.length() - b.length());
System.out.println(names);
```

---

## 6. szakasz – Vegyes, komplex kérdések

### 41. kérdés — Komplex kódolvasás

Mi az alábbi kód kimenete? Lépésről lépésre indokold!

```java
interface Printable {
    default void print() { System.out.println("Printable"); }
}

interface Loggable {
    default void print() { System.out.println("Loggable"); }
}

class Document implements Printable, Loggable {
    @Override
    public void print() {
        Printable.super.print();
        Loggable.super.print();
        System.out.println("Document");
    }
}

public class Main {
    public static void main(String[] args) {
        Printable p = new Document();
        p.print();
    }
}
```

---

### 42. kérdés — Komplex hibakeresés

Az alábbi kód fordításkor vagy futáskor hibát ad? Hányszor és hol?

```java
public class GenericTest {
    public static <T extends Comparable<T>> T max(List<T> list) {
        T result = list.get(0);
        for (T item : list) {
            if (item.compareTo(result) > 0) {
                result = item;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        List<Integer> ints = Arrays.asList(3, 7, 1, 9, 2);
        System.out.println(max(ints));

        List<String> strs = Arrays.asList("alma", "körte", "barack");
        System.out.println(max(strs));

        List<Object> objs = Arrays.asList(new Object(), new Object());
        System.out.println(max(objs)); // ???
    }
}
```

---

### 43. kérdés — Komplex Igaz/Hamis

a) Egy `sealed` osztály leszármazottja lehet `non-sealed`, amelyből aztán bárki örökölhet.

b) Egy `record` típusban lehet `static` mező.

c) Lambda kifejezéssel több absztrakt metódust tartalmazó interfész is implementálható.

d) A Reflection API megtöri az enkapszulációt – `private` mezők is elérhetők.

e) Egy interfész `default` metódusa felülírható az implementáló osztályban.

f) Egy `final` metódus lehet egyben `abstract` is.

g) A Java-ban egy osztálynak csak egy közvetlen ősosztálya lehet, de több interfészt is megvalósíthat.

---

### 44. kérdés — Kivételkezelés és öröklés

Adott az alábbi ősmetódus:

```java
public abstract void process() throws IOException, SQLException;
```

Az alábbi felüldefiniálások közül melyik(ek) érvényesek?

a) `public void process() throws IOException, SQLException, RuntimeException`

b) `public void process() throws IOException`

c) `public void process()`

d) `public void process() throws Exception`

e) `public void process() throws FileNotFoundException` (ahol `FileNotFoundException extends IOException`)

---

### 45. kérdés — Stream és Collection

Írd le szavakban, mit csinál az alábbi stream lánc, és mi a végeredmény típusa!

```java
Map<Integer, List<String>> result = words.stream()
    .collect(Collectors.groupingBy(String::length));
```

---

### 46. kérdés — Generikusok és wildcard

Mi a különbség az alábbi két metódusdefiníció között? Mikor melyiket kell használni?

```java
// 1.
public static double sumList(List<Number> list) {
    return list.stream().mapToDouble(Number::doubleValue).sum();
}

// 2.
public static double sumList(List<? extends Number> list) {
    return list.stream().mapToDouble(Number::doubleValue).sum();
}
```

---

### 47. kérdés — OOP tervezés

Adott az alábbi kódrészlet:

```java
public class OrderService {
    public void process(Object order) {
        if (order instanceof OnlineOrder) {
            ((OnlineOrder) order).sendEmail();
        } else if (order instanceof StoreOrder) {
            ((StoreOrder) order).printReceipt();
        }
    }
}
```

a) Mi a probléma ezzel a tervezéssel OOP szempontból?

b) Hogyan lehetne ezt jobban megvalósítani Java eszközökkel? (legalább két megoldást adj)

---

### 48. kérdés — Igaz/Hamis — Csapda kérdések

a) A `List<Integer>` és a `List<Double>` mindkettő altípusa a `List<Number>`-nek.

b) A statikus típus határozza meg, hogy milyen metódusokat lehet meghívni egy referencián.

c) A dinamikus típus határozza meg, hogy egy felüldefiniált metódus melyik implementációja fut le.

d) Egy absztrakt osztálynak muszáj lennie legalább egy absztrakt metódusa.

e) Ha egy osztály implementál egy interfészt, de nem valósítja meg az összes metódusát, fordítási hibát kapunk – kivéve, ha az osztály maga is absztrakt.

---

### 49. kérdés — Komplex kódolvasás

Mi az alábbi kód kimenete? Mi fog történni a 3. sorban?

```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
Iterator<String> it = list.iterator();
list.add("d"); // 3. sor
while (it.hasNext()) {
    System.out.println(it.next());
}
```

---

### 50. kérdés — Összefoglaló tervezési kérdés

Az alábbi szituációhoz válaszd ki a legjobb Java eszközt, és **indokold meg** a döntésedet:

| Szituáció | Legjobb eszköz | Indoklás |
|---|---|---|
| Egy banki tranzakció típusait kell modellezni (Átutalás, Betét, Kivét), más osztályok nem örökölhetnek a tranzakció osztályból, de a háromból igen. | ? | ? |
| GPS koordinátát tárolunk (lat, lon), amelyen távolságot számolunk, az objektum ne legyen módosítható. | ? | ? |
| Különböző állatok hangjait szeretnénk egységesen kezelni, minden állat máshogy hangzik. | ? | ? |
| Egy konténer osztályt írunk, ami bármilyen `Comparable` típust tárolhat, és ki tudja adni a maximumot. | ? | ? |
