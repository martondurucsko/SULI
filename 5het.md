# Java programozás – 5. óra: Generikusok és Collections Framework
## ZH felkészülő jegyzet

---

## Tartalom
1. [Parametrikus polimorfizmus és generikusok](#1-parametrikus-polimorfizmus-és-generikusok)
2. [Type Erasure – hogyan működik valójában](#2-type-erasure)
3. [Generikusok írása – szintaxis és konvenciók](#3-generikusok-írása)
4. [Korlátok és Wildcard](#4-korlátok-és-wildcard)
5. [Collections Framework](#5-collections-framework)
6. [Elemek összehasonlítása: Comparable és Comparator](#6-comparable-és-comparator)
7. [Stream API](#7-stream-api)
8. [ZH csapdakérdések](#8-zh-csapdakérdések)

---

## 1. Parametrikus polimorfizmus és generikusok

### Mi a probléma generikusok nélkül?

Képzeld el, hogy írni akarsz egy általános tárolót, ami bármilyen típusú objektumot eltárolhat. Az első ösztön az, hogy `Object`-et használsz, hiszen mindennek az őse:

```java
List myIntList = new LinkedList();
myIntList.add(new Integer(0));
Integer x = (Integer) myIntList.iterator().next(); // explicit cast szükséges
```

Ez látszólag működik, de van egy **komoly veszélye**: a fordító nem tudja ellenőrizni, hogy tényleg `Integer` jön-e ki a listából. Ha véletlenül mást teszel bele, és utána `Integer`-ré castolod, **futásidőben kapod a hibát** (`ClassCastException`), nem fordításkor. Ez az egyik legrosszabb hiba, amit el lehet követni, mert csak futás közben derül ki.

**A generikusok célja:** hogy a fordító már fordítási időben ellenőrizni tudja a típushelyességet, és ne kelljen explicit castolni.

```java
List<Integer> myIntList = new LinkedList<Integer>();
myIntList.add(new Integer(0));
Integer x = myIntList.iterator().next(); // nincs cast, a fordító tudja a típust
```

### Java 7-től: diamond operátor

Ha a bal oldalon megadod a típust, a jobb oldalon már nem kell kiírni – a fordító kitalálja:

```java
List<Integer> integerList = new ArrayList<>(); // a <> a diamond operátor
```

---

## 2. Type Erasure

Ez az egyik legfontosabb – és leginkább félreértett – dolog a Java generikusokkal kapcsolatban.

### Generikusok vs. C++ template

Szintaktikailag a kettő nagyon hasonlít: `List<List<String>>` Java-ban és C++-ban egyaránt értelmes. De a **működés teljesen más**.

| | C++ template | Java generikus |
|---|---|---|
| Hogyan működik? | expansion: minden típushoz külön kódot generál a fordító | Type Erasure: fordítási időben ellenőriz, majd törli a generikus információt |
| Futásidőben | több változat létezik a kódból | csak egy változat létezik, Object-ekkel dolgozik |
| Visszafelé kompatibilis? | nem feltétlenül | igen, Java 5 előtti JVM-en is fut |

### Mit jelent a Type Erasure konkrétan?

A fordító lefordítja és **ellenőrzi** a generikus kódot, majd az összes generikus paramétert **kitörli** és visszaalakítja `Object`-té (vagy a megadott felső korlát típusára). Tehát ez a két kód fordítás után egyenértékű lesz:

```java
// Amit te írsz:
List<String> words = new ArrayList<String>();
words.add("Hello ");
words.add("world!");
String s = words.get(0) + words.get(1);

// Amit a fordító csinál belőle (bytecode szinten):
List words = new ArrayList();
words.add("Hello ");
words.add("world!");
String s = ((String) words.get(0)) + ((String) words.get(1));
```

A különbség: ha te írtad generikusan, a fordító garantálja, hogy a castok biztosan helyek. Ha kézzel írtad az Object-es verziót, ezt nem garantálja senki.

### A Type Erasure következményei – FONTOS!

Ezek a viselkedések direkten a ZH-n is előjöhetnek:

- **Futásidőben nem lehet generikus típusparaméterrel példányosítani.** `new T()` nem működik, mert futásidőben már nem létezik T, csak Object.
- **A generikus típusparaméter csak referencia típus lehet.** Primitíveket (int, double, stb.) nem lehet közvetlenül generikus paraméternek adni. Helyettük az autoboxing segít: `Integer`, `Double`, stb.
- **`List<Integer>` nem altípusa `List<Object>`-nek**, hiába az `Integer` altípusa `Object`-nek. Erről bővebben a Wildcard résznél.

---

## 3. Generikusok írása

### Generikus osztály

Az osztály neve után `<>` zárójelben adjuk meg a formális típusparamétert. Neve lehet bármi, de **konvenció szerint egybetűs nagybetű**.

```java
class Info<T> {
    T obj;                              // tagváltozó típusa T

    public Info(T obj) {
        this.obj = obj;
    }

    public T getObj() { return obj; }   // visszatérési érték típusa T

    public String getInfo() {
        return obj.getClass().toString();
    }
}

// Használat:
Info<Integer> info = new Info<Integer>(1);
Info<String> sinfo = new Info<>("hello");  // diamond operátor
```

### Generikus metódus

A metódus **saját** típusparamétert is kaphat, ami nem feltétlenül azonos az osztályéval. A típusparamétert a visszatérési érték **elé** kell írni:

```java
public <T> Info<T> getInfo(T a) {
    return new Info<T>(a);
}

// Hívás:
Info<Integer> info = getInfo(1);
```

Sőt, teljesen generikus metódust írhatunk olyan osztályban is, amelynek **nincs** saját típusparamétere.

### Konvenciók – mit jelent az E, K, T, V, N?

Ezeket a betűket nem kötelező használni, de a Java dokumentációban mindenhol ezek szerepelnek, ezért érdemes ismerni:

| Betű | Jelentés |
|------|---------|
| E | Element – a Collections Framework elemei |
| K | Key – kulcs (Map-eknél) |
| V | Value – érték (Map-eknél) |
| T | Type – általános típus |
| N | Number – szám típus |
| S, U, ... | Ha több típusparaméter kell |

---

## 4. Korlátok és Wildcard

### Felső korlát: `extends`

Alapból bármilyen referencia típust be lehet helyettesíteni egy generikus paraméterbe. Ha ezt korlátozni akarod, az `extends` kulcsszóval adhatod meg a **felső korlátot** – vagyis azt mondod, hogy csak az adott osztályból (vagy interfészből) leszármazott típus fogadható el.

```java
// Csak Number és leszármazottai (Integer, Double, Float, stb.) lehetnek U
public class Box<U extends Number> { ... }

// Több korlát is megadható & jellel:
// U legyen Number leszármazott ÉS valósítsa meg a VectorProduct interfészt
public class Box<U extends Number & VectorProduct> { ... }
```

### Wildcard: `<?>`

Az egyik legzavaróbb dolog a generikusokkal, de ha egyszer megérted, utána már logikus.

**A probléma:** Legyen egy metódus, amely listát kap paraméterként, és kiírja az elemeit. Azt gondolhatnád, hogy `List<Object>` típusú paramétert elfogad mindenféle listát:

```java
public void processObjectList(List<Object> list) { ... }

ArrayList<Integer> intList = new ArrayList<>();
processObjectList(intList); // FORDÍTÁSI HIBA!
```

Ez **nem** működik! A `List<Integer>` **nem altípusa** a `List<Object>`-nek, hiába az `Integer` altípusa `Object`-nek. Ez a Type Erasure és a Java típusrendszer egyik kontraindikáló következménye.

**A megoldás: wildcard (`<?>`)**

A `<?>` egy ismeretlen típust jelöl. A `List<?>` azt jelenti: „valamilyen típusú lista, nem tudjuk, melyik, de valamilyen".

```java
public void processWildcardList(List<?> list) { ... }

ArrayList<Integer> intList = new ArrayList<>();
processWildcardList(intList); // OK!
```

A `List<Integer>` **altípusa** a `List<?>`-nek – ez az, ami nem igaz `List<Object>`-re.

### Wildcard korlátok

A wildcard is korlátozható az `extends` és `super` kulcsszavakkal:

**Felső korlát (`extends`):** Olyan listát fogadunk el, amelynek elemei `Number` leszármazottai lehetnek (`List<Integer>`, `List<Double>`, stb.)

```java
// Ez nem jó – csak pontosan List<Number>-t fogad el:
public void printList(List<Number> l) { }

// Ez a helyes – List<Integer>, List<Double> stb. is elfogadott:
public void printList(List<? extends Number> l) { }
```

**Alsó korlát (`super`):** Olyan listát fogadunk el, amelybe `Integer`-t lehet beletenni – tehát `List<Integer>`, `List<Number>`, `List<Object>` mind megfelel, mert mindegyikbe tehető `Integer`.

```java
// Ez nem jó – csak pontosan List<Integer>-t fogad el:
public void printList(List<Integer> l) { }

// Ez a helyes – List<Integer>, List<Number>, List<Object> mind elfogadott:
public void printList(List<? super Integer> l) { }
```

**Ökölszabály (PECS – Producer Extends, Consumer Super):**
- Ha adatot **olvasol ki** a listából → `? extends T`
- Ha adatot **teszel bele** a listába → `? super T`

---

## 5. Collections Framework

### Mi ez és mire való?

Programozás közben nagyon sokszor szükségünk van adatszerkezetekre: listákra, halmazokra, sorokra, kulcs-érték tárakra. Ezeket persze megírhatnánk magunk, de miért tennénk, ha a Java már megírta őket optimálisan? A Collections Framework pontosan ez: egy kész, jól optimalizált adatszerkezet-könyvtár, amely ráadásul kiterjeszthető is – a saját implementációinkat is be lehet illeszteni.

### A két alap interfész

Az egész keretrendszer két fő interfészre épül:

**`Collection`**: egy csoportba tartozó objektumok tárolója (lista, halmaz, sor, stb.)  
Legfontosabb metódusai: `add()`, `remove()`, `size()`, `iterator()`, `isEmpty()`, `toArray()`

**`Map`**: kulcs-érték párokat tárol (asszociatív tömb / szótár)  
Legfontosabb metódusai: `put()`, `get()`, `remove()`, `containsKey()`, `containsValue()`, `keySet()`, `values()`, `entrySet()`

**Fontos:** a `Map` **független** a `Collection` interfésztől, nem leszármazottja annak!

### Collection hierarchia – legfontosabb interfészek

```
Collection
├── Set          – halmaz, nincs duplikátum (equals() alapján)
│   └── SortedSet    – rendezett halmaz
├── List         – sorrend számít, van index
├── Queue        – FIFO sor, feldolgozás előtti tárolásra
│   └── Deque        – kétirányú sor (mindkét végére tehetsz)
│   └── BlockingQueue – többszálú használatra
```

### Legfontosabb megvalósítások

| Adatszerkezet | Interfész | Belső struktúra | Mikor érdemes? |
|---|---|---|---|
| `ArrayList` | `List` | dinamikus tömb | random access, ritkán szúrunk be középre |
| `LinkedList` | `List`, `Deque` | láncolt lista | sokat szúrunk be/törlünk |
| `HashSet` | `Set` | hash tábla | gyors keresés, sorrend nem számít |
| `TreeSet` | `SortedSet` | piros-fekete fa | rendezett halmaz |
| `HashMap` | `Map` | hash tábla | kulcs alapú gyors keresés |
| `TreeMap` | `Map` | piros-fekete fa | rendezett kulcsok |
| `LinkedHashMap` | `Map` | hash tábla + láncolt lista | kulcs-érték tár, de bejárási sorrend = beszúrási sorrend |

### Absztrakt megvalósítások

A keretrendszer tartalmaz absztrakt osztályokat is (pl. `AbstractList`, `AbstractMap`), amelyek csak az alapmetódusokat valósítják meg. Ha saját adatszerkezetet írsz, ezekből örökölhetsz, és csak a szükséges metódusokat kell felüldefiniálni – nem kell mindent nulláról implementálni.

Az előadáson: amit nem implementáltok, annak törzsében `UnsupportedOperationException`-t dobjatok. **De ez csak a feladatok miatt van így, éles kódban soha ne csináljuk!**

### Bejárás

Kétféleképpen lehet bejárni:

```java
// 1. for-each (ha nincs szükség törlésre közben)
for (T t : collection) {
    t.doSomething();
}

// 2. Iterator (ha közben törölni is kell)
Iterator<T> iterator = collection.iterator();
while (iterator.hasNext()) {
    T element = iterator.next();
    if (elTorli(element)) {
        iterator.remove(); // CSAK így szabad törölni bejárás közben!
    }
}
```

**Figyelem:** bejárás közben ne töröljön a for-each-ből! Az `iterator.remove()` az egyetlen biztonságos módja a törlésnek iteráció közben.

### Kivételek a Collections-ban

A beépített megvalósítások standard kivételeket dobnak:

```java
List<Integer> l = new ArrayList<>();
l.add(5, 4);  // IndexOutOfBoundsException – csak 0 elem van, 5. indexre nem lehet tenni
l.get(1);     // IndexOutOfBoundsException – üres lista
```

---

## 6. Comparable és Comparator

Az adatszerkezetek rendezéshez (pl. `TreeMap`, `TreeSet`, `Arrays.sort()`) szükség van arra, hogy az elemeket össze lehessen hasonlítani. Mivel Java-ban nincs operátor-túlterhelés (ellentétben C++-szal), ezt interfészeken keresztül oldjuk meg.

### Comparable – az osztály önmaga határozza meg a sorrendet

Ha egy osztály megvalósítja a `Comparable<T>` interfészt, akkor definiál egy `compareTo()` metódust, amellyel az objektum össze tudja magát hasonlítani egy másik ugyanolyan típusú objektummal.

**A compareTo() visszatérési értéke:**
- **Negatív szám**: `this` kisebb, mint a paraméter
- **Nulla**: egyenlők
- **Pozitív szám**: `this` nagyobb, mint a paraméter

```java
Double dd = 5d;
boolean b = dd.compareTo(4d) > 0; // true, mert 5 > 4
```

Ha egy típus megvalósítja a `Comparable`-t, az `Arrays.sort()` és a `Collections.sort()` automatikusan tud rá rendezni:

```java
Integer[] ai = { 3, 2, 1, 4 };
Arrays.sort(ai); // most { 1, 2, 3, 4 } lesz
```

### Comparator – külső összehasonlító

Mi van, ha nem te írtad az osztályt, amit rendezni akarsz, tehát nem tudsz `Comparable`-t implementálni? Vagy ha ugyanazt az osztályt **többféle szempont szerint** akarod rendezni?

Ilyenkor `Comparator`-t használunk: egy külön osztályt írunk, amelynek `compare(a, b)` metódusa összehasonlít két objektumot.

**A compare() visszatérési értéke ugyanaz, mint a compareTo()-nál.**

```java
// Egy Comparator, ami csapatokat pontszám szerint hasonlít
class HandballTeamComparator implements Comparator<HandballTeam> {
    @Override
    public int compare(HandballTeam a, HandballTeam b) {
        return Integer.compare(b.getPoints(), a.getPoints()); // csökkenő sorrend
    }
}
```

### Comparable vs. Comparator összefoglalás

| | Comparable | Comparator |
|---|---|---|
| Hol van a logika? | magában az osztályban | külső osztályban |
| Hány szempont? | csak egy (természetes sorrend) | annyi, amennyit akarsz |
| Interfész | `Comparable<T>` | `Comparator<T>` |
| Metódus | `compareTo(T other)` | `compare(T a, T b)` |
| Mikor? | az osztály "természetes" sorrendjéhez | ha nincs hozzáférésed az osztályhoz, vagy több szempont kell |

---

## 7. Stream API

Java 8-ban vezették be. A lényege: Collection-ökön **funkcionális stílusban** lehet műveleteket végezni, for ciklusok nélkül. Nagyon tömör és olvasható kódot lehet így írni.

### Alap felépítés

```java
collection.stream()
    .közbülső_művelet1()
    .közbülső_művelet2()
    .terminális_művelet();
```

- **Közbülső műveletek** (intermediate): átalakítják a stream-et, de még nem hajtják végre → `filter()`, `map()`, `distinct()`, `sorted()`
- **Terminális művelet** (terminal): "lezárja" a stream-et és eredményt ad → `forEach()`, `count()`, `collect()`, `reduce()`

### Legfontosabb műveletek

**forEach** – minden elemen végrehajt valamit:
```java
l.stream().forEach(e -> System.out.println(e));
```

**filter** – szűr egy feltétel alapján:
```java
l.stream().filter(e -> e.contains("Java"))
```

**distinct** – eltávolítja a duplikátumokat:
```java
l.stream().distinct()
```

**count** – megszámolja az elemeket:
```java
long db = l.stream().distinct().filter(e -> e.contains("Java")).count();
```

**map** – minden elemből csinál valamit (leképzés):
```java
// Minden String helyett megkapjuk a hosszát
l.stream().map(e -> e.length());
```

**reduce** – összevon egy értékké:
```java
Optional<String> result = s.stream().reduce(String::concat); // összefűzi az összes Stringet
```

**collect** – az eredményt visszagyűjti egy Collection-be:
```java
ArrayList<String> filtered = l.stream()
    .distinct()
    .filter(e -> e.contains("a"))
    .collect(Collectors.toCollection(ArrayList::new));
```

**anyMatch** – igaz, ha legalább egy elem megfelel:
```java
boolean b = list.stream().anyMatch(str -> str.endsWith("."));
```

### Optional típus

A `reduce()` és más műveletek `Optional<T>`-vel térnek vissza, mert előfordulhat, hogy nincs eredmény (pl. üres lista). Az `Optional` biztonságos kezelése:

```java
Optional<String> result = s.stream().reduce(String::concat);

// Hosszú változat:
if (result.isPresent()) {
    String val = result.get();
}

// Rövid változat:
String val = result.orElse("alapértelmezett érték");
```

---

## 8. ZH csapdakérdések

**Generikusok – típussal kapcsolatos trükkök**

Az egyik leggyakoribb csapda: lehet-e primitívet generikus paraméternek adni?

Nem. `List<int>` nem létezik. Helyette `List<Integer>` kell. Az autoboxing (automatikus `int` → `Integer` konverzió) az `add(10)` esetén működik, de a típusnévnél nem.

---

Ha van egy `CorrectContainer<Double>`, és megpróbálod `add(10)`-t hívni rajta, mi történik?

Fordítási hiba. A `10` integer literál, amit a fordító `Integer`-ré boxol – és `Integer`-t nem lehet `Double` helyére tenni, még ha primitívek közt megengedett is a konverzió.

---

Miért nem lehet `List<Integer>`-t `List<Object>` paraméternek adni?

Mert a Java generikusai **invariánsak** – az altípusosság nem terjed ki a generikusokra. Bár `Integer` altípusa `Object`-nek, `List<Integer>` **nem** altípusa `List<Object>`-nek. Ha ez lehetséges lenne, bele lehetne tenni a listába `String`-et is (hiszen az is `Object`), ami majd `Integer`-ként jönne ki – és elromlana a típusbiztonság.

---

Mi az `extends` és `super` különbsége wildcard esetén?

- `List<? extends Number>`: olvasásra jó, Number-ként lehet olvasni az elemeket, de nem tudunk betenni (mert nem tudjuk pontosan, melyik leszármazottról van szó)
- `List<? super Integer>`: írásra jó, Integer-t lehet beletenni, de csak Object-ként tudunk olvasni

---

Lehet-e futásidőben `new T()`-t írni generikus T-re?

Nem, mert Type Erasure miatt futásidőben már nincs T típusinformáció.

---

Miben különbözik az `ArrayList` és a `LinkedList` teljesítménye?

- `ArrayList`: gyors az indexelt elérés (O(1)), lassú a közbülső szúrás/törlés (O(n))
- `LinkedList`: lassú az indexelt elérés (O(n)), gyors a szúrás/törlés iterátor esetén (O(1))

---

Mire való a `TreeMap` a `HashMap`-pal szemben?

A `TreeMap` piros-fekete fában tárolja az elemeket, ezért a kulcsok **rendezve** vannak. A `HashMap` nem garantál sorrendet, viszont gyorsabb (O(1) átlagos keresés vs. O(log n) `TreeMap`-nél).

---

Mi a különbség a `Comparable` és `Comparator` közt?

A `Comparable` az osztályon belül definiálja az összehasonlítást (az osztálynak van `compareTo()` metódusa), és csak egyféle rendezési szempont adható meg. A `Comparator` külső osztály, ugyanarra a típusra több is írható különböző szempontok szerint. Ha nem Te írtad az összehasonlítani kívánt osztályt, akkor Comparator-t kell használni.

---

Mi az `Optional` és mire jó?

Egy tároló, amely vagy tartalmaz egy értéket, vagy nem (null helyett). Tipikusan stream terminális műveletek adják vissza, ahol előfordulhat, hogy nincs eredmény. Az `isPresent()` ellenőrzi, van-e érték, az `orElse()` alapértelmezett értéket ad vissza, ha nincs.

---

Ha bejárás közben törölni akarsz egy Collection-ből, mit kell használni?

Az `Iterator`-t és annak `remove()` metódusát. For-each ciklus közben közvetlen törlés `ConcurrentModificationException`-t dob.

---

*Összeállítva a PPKE Java programozás 5. óra (Generikusok és Collections Framework) anyaga alapján*
