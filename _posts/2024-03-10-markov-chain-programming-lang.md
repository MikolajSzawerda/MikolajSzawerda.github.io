---
layout: post
title:  "Markov programming language"
author: "Mikołaj Szawerda"
tags: Software
---
# Propozycja tematu projektu TKOM

# consider
- czy dodać var
- kiedy ustalają się wagi - stworzenie wielu piosenek
- dopisz jakie zewnętrzne zależności
- dodanie typow iteratorow, i generatorow
- jak to zostanie zaimplementowane
- jakoś lepiej with zrobic
  
opis zakładanej funkcjonalności i przykłady obrazujące dopuszczalne konstrukcje językowe
oraz ich semantykę.
• formalna specyfikacja i składnia: realizowanego języka (jeśli chodzi np. o interpretery),
wszystkich plików/strumieni wejściowych, danych konfiguracyjnych itp.
• zwięzłą analizę wymagań funkcjonalnych i niefunkcjonalnych, obsługa błędów (jakiego rodzaju
błędy będą wykrywane, tolerowane?, jak będzie wyglądał przykładowy komunikat o błędzie?).
• sposób uruchomienia, wej./wyj.
• zwięzły opis sposobu testowania – podane przykłady testowanych konstrukcji językowych,
przypadków złożonych, błędnych itp.

**Tytuł**: Interpreter języka generatorów utworów midi

**Autor**: Mikołaj Szawerda

## Opis zakładanej funkcjonalności

Projekt ma na celu stworzenie interpretera języka ogólnego przeznaczenia z dodatkowymi typami i funkcjami umożliwiającymi deklaratywny odczyt i warunkowanie generowanego pliku MIDI. Język będzie udostępniał typy: wysokości nut, czasu trwania nuty, generator melodii deterministyczny i stochastyczny, tworzenie finalnego pliku midi z podanych generatorów.

## Charakterystyka języka

- język typowany statycznie silnie
- język wspiera tylko wbudowane typy prymitywne oraz wbudowane typy złożone
- język wspiera operatory arytmetyczne/logiczne/znakowe/relacji porządku/specjalne muzyczne
- język wspiera dla danej jednostki kontekst globalny, nie ma konieczności deklaracji funkcji `main()`
- zmienne przesłaniane do najbliższego kontekstu
- język wspiera operację warunkową `if` oraz pętlę `for` - iteracja tylko po typach udostępniających iterator
- język wspiera deklarację funkcji:
  - typy prymitywne przekazywane przez wartość, złożone przez referencję
  - brak parametrów domyślnych
  - możliwość przeciążenia
  - wsparcie dla funkcji wyższego rzędu
  - wsparcie dla rekurencji
- język nie wspiera obsługi błędów w czasie wykonania programu - wystąpienie wyjątku kończy program z komunikatem
- język wspiera operacje odczytu i zapisu plików MIDI, oraz manipulację danymi poprzez typy wbudowane
- język wspiera operacje wypisania na standardowe wyjście

### Przykłady kontrukcji językowych

- silne statyczne typowanie

```
fun add(Int a, Int b) -> Int{
  return a+b;
}

add("5", 1); //compilation error

if("5"==5){ //compilation error
  //...
}

if(Int("5")==5){ //correct
  //...
}

Int a = 5;
a="5"; //compilation error
Int b = 5.0; //compilation error
if(Int(5.0)==5){ //correct
  //...
}
```

- wbudowane typy prymitywne

```
Int a = 5;
Double b = 4.0;
Bool c = true;
Float e;
e=4.0;
const Int f = Int(b)*a+a/2;
f=4; //compilation error
if(a+f>Int(b)){
  const Int a = round(10.0*b);
  String k = String(a);
}
```

- wbudowane typy złożone

```
Opt[Int] o = Opt.of(1);
Opt[Int] o1 = Opt.empty();
Opt[Int] o2 = o1.or(o);
Pitch a = C1;
Pitch[] a1 = [C2, D4];
Rythm b = R_Q;
Rythm[] b1 = [R_Q, R_E];
Note d = {C1, R_Q};
Note d1 = {R_Q}; //pause
Motive d = [{a, b}, {D2, R_H}, {R_Q}];
Markov e = Markov([{a, b}, {D2, R_H}, {R_Q}]);
Markov e1 = Markov(d);
Markov e2 = with "song.mid";
Markov e3 = with ("song.mid", "song1.mid");
Markov e4 = Markov(d) //panic - at least len=2
Composer f = Composer.with(e).with(d).with(e);
Song g = f.gen(125, 60); //125 BPM, 60 seconds
```

- operatory

```
Int a = 5;
Int b = 10;

a += b;
a = (a+b)*(a-b)/(-a+2*b);
Bool isBigger = a > b;

String a1 = "Hello";
String a2 = "world;

if(a1+" "+a2 == "Hello world") {
  print(a1);
} else {
  print(a2);
}

if(a % 15 == 0) {
  print("foobar_"+String(a));
}

Pitch a = D1;
a.transpose(1); // D#1
a.transpose(-2); //C#1
Rythm b = R_Q;
b.speed(2); //R_E
Motive b = [{D1, R_Q}, R_Q];
b.transpose(1); // {D#1, R_Q}, R_Q
b.speed(2); //{D#1, R_E}, R_E
```

- kontekst globalny

```
Markov a;

fun sum(Int a, Int b) -> Int {
  return a+b;
}

fun enrichWithLick(Motive b) -> Void{
  a << b;
}

a << [{C1, R_Q}];
enrichWithLick([{Q}]);
print(a);
```

- pętla for

```
Range a = Range(3);
Motive b = [{D1, Q}, Q, {C1, Q}];
Markov c = Markov(b);

for(Int i in a){
  print(i);
} // 1 2

for(Note n in b) {
  print(n);
} //{D1, Q}, Q, {C1, Q}

for(Note n in b.gen()) {
  print(n);
} //{D1, Q}, Q, {C1, Q}, ... inf loop(b[i%len(b)])

for(Note n in c){
  print(n);
}//compilation error

for(Note n in c.gen({D1, Q})){
  print(n);
} //{D1, Q}, {?}, ... inf loop
```

- deklaracja funkcji

```
Pitch a = C1;
fun transpose(Pitch a) -> Void {
  a.transpose(1);
}

print(a); //C#1

fun transpose2(val Pitch a) -> Void {
  a.transpose(1);
}

print(a); //C#1

fun transpose3(Pitch a) -> Void {
  a = B3;
}

print(a); //B3

fun transpose4(const Pitch a) -> Void {
  a = C4; //compilation error
}

fun nwd(Int a, Int b) -> Int
{
    if(b != 0) {
    	return nwd(b,a%b);
    }
    return a;
}

fun buissnesLogic(Int age) -> Opt[Motive]{
  if(age > 18){
    return Opt.of([{C1, Q}, Q]);
  }
  return Opt.empty();
}

Motive lick = buisnesLogic(29)
  .map((Motive a)->{
    a.transpose(1);
  })
  .or([{D1, Q}]);

fun transformMelody(const Motive a, (Motive)->Motive transformation) -> Motive {
  Motive b = Motive(a);
  return transformation(b);
}

```

- sytuacje wyjątkowe

```

Markov a = with "lick1.mid" //panic("File not found");

fun detonateProgram() -> Void {
  panic "Nuke nuke";
}

detonateProgram(); //panic("Nuke nuke");
```

- język wspiera deklaratywny odczyt plików midi

```
Pitch[] a = with "lick.mid";
print(a); //C1, C2, C3
Rythm[] b = with "lick.mid";
print(b); //Q E H
Motive c = with "lick.mid";
print(c); //{C1, Q}, {C2, E}, {C3, H}
Markov d = with "lick.mid"; // d << c;
print(d); //potentialy will print matrix line by line?
```

- język wspiera zapis wynikowego pliku midi

```
Motive a = with "lick.mid";
a.transpose(1);
Markov b = Markov(a);

Composer c = Composer
  .with(a)
  .with(b, {C1, Q});

Song d = c.gen(125, 60); //125BPM 60 seconds

save d "song.mid";

b << [{D1, Q}, Q]; //next song gen will use new values

save c.with([{C1, Q}, Q]).gen(60, 10) "song2.mid";
```






## Analiza wymagań

### Język typowany statycznie silnie
  - śledzenie typu wyrażenia
  - zapewnienie operacji konwersji

#### Konwersja

||Result|
|-|-|
|Motive(R_Q)|[{EMPTY, R_Q}]
|Pitch({C1, R_Q})|C1|
|Rythm(Note(C1, R_Q))|R_Q|
|Int(2.0)|2|
|Double(Int(1))|1.0|
|Pitch[](Motive{[{C1, R_Q}, {C2, R_E}]})|[C1, C2]

### Język wspiera tylko wbudowane typy prymitywne oraz wbudowane typy złożone

|Token|Typ|Opis|
|-|-|-|
|Int|p|int32
|Double|p|IEEE 64b|
|Bool|p|true,false
|Unit|p|void - używany do procedur
|Opt|z|Optional - zastępuje brak nulla
|Pitch|z|Wysokość nuty, tworzony z zastrzeżonych słów kluczowych
|Rythm|z|Długość trwania nuty, tworzony z zastrzeżonych słów kluczowych
|Note|z|Nuta - określona wysokość i długość
|Motive|z|lista nut, generator deterministyczny i iterator
|Markov|z|impl łańcucha markova, generator stochastyczny
|Composer|z|builder, określa generatory z których zostanie stworzony Song
|Song|z|wewnętrznie Motive[], umożliwia zapis do midi
|Int[],Double[], Bool[]|z|mutowalna lista, iterator
|Pitch[], Rythm[]
|()->T1|z|funkcja anonimowa bez argumentowa
|(T1)->T2|z|funkcja anonimowa z jednym argumentem
|(T1, T2)->T3|z|funkcja anonimowa z dwoma argumentami

#### Opt
- implementacja Optional'a
- `fun of(T a)->Opt[T]` - where `T in [Int, Double, Bool, Opt, Pitch, Rythm, Note, Motive, Markov, Composer, Song, T[]]` - metoda wytwórcza
- `fun map((T1)->T2)->Opt[T2]` - transformacja
- `fun filter((T1)->Bool)->Opt[T1]` - filtrowanie przez warunek
- `fun get()->T1` - gdy puste `panic`
- `fun or(()->T1)->T1` - gdy puste rezultat `()->T1`, w przeciwnym wypadku zawartość `this`

#### Iter
- implementacja iteratora
- `Iter[T]` where `T in [Int, Double, Bool, Opt, Pitch, Rythm, Note, Motive, Markov, Composer, Song, T[]]`
- `fun next()->T`, zwraca aktualny element i przechodzi do kolejnego węzła, `panic` gdy empty
- `fun hasNext()->Bool`
  
#### Pitch
- wysokośc nuty `([A-G](#)?[1-7])|EMPTY`, `EMPTY` umożliwia utworzenie pauzy
- `fun transpose(Int a)->Unit` - zmienia wysokość nuty o `a`, `panic`, gdy brak wspieranej wysokości
- `Pitch[]` - udostępnia `fun iter()->Iter[Pitch]`

#### Rythm
- długość trwania nuty

|Token|Długość
|-|-|
|R_DL|cztery takty
|R_L|dwa takty
|R_W|takt
|R_H|pół taktu
|R_Q|ćwierć taktu
|R_E|1/8 taktu
|R_S|1/16 taktu
|X_T|1/3 X
|X_D|3/2 X

- `fun speed(Int a)->Rythm` - zmiana długości rytmu o `a`, `panic` gdy nie wspierany

#### Note
- udostępnia metedy dla Rythm i Pitch


#### Gen
- generator nut, udostępnia nieskończony iterator
- przechowuje funkcję tworzącą iterator `()->Iter[Note]`, obecny iterator i gdy `hasNext()` zwraca false, tworzy nowy iterator
- `fun iter()->Iter[Note]`

#### Motive
- generator deterministyczny i iterator po Note[]
- `fun iter()->Iter[Note]`
- `fun gen()->Gen`

#### Markov
- generator stochastyczny
- `fun gen()->Gen`
- wewnętrznie reprezentowany przez dwa słowniki o typie `Map<Pitch, Pair<Int, List<Pair <Pitch, Int>>>` i `Map<Rythm, Pair<Int, List<Pair <Rythm, Int>>>`, gdzie np `map.get(Pitch.C1).first()` zawiera akumulowaną sumę `map.get(Pitch.C1).second()` 
- `fun gen(Note initialState)->Gen` - przy utworzeniu generatora macierze zostają skopiowane do iteratora, przy czym każda wartość jest dzielona przez akumulowaną sumę, otrzymując w ten sposób macierz prawdopodobieństwa `Map<Pitch, List<Pair <Pitch, Double>>>`, `hasNext()` zawsze zwraca true, iterator posiada aktualny stan `(Pitch, Rythm)`, wywołanie `next()`, wybiera wiersz z macierzy i zadanymi prawdopodobieństwami losuje stan następny modifkując `(Pitch, Rythm)` w iteratorze (w implementacji złożone typy w strukturach danych oczywiście zostaną zastąpione własnymi typami)

#### Composer
- pozwala wskazać które generatory będą użyte do stworzenia piosenki - przechowuje referencje
- `fun with(Motive m) ->Composer` oraz `fun with(Markov m, Note initState)` - zostają appendowani odpowiednio do `List<Motive>` i `List<Pair<Markov, Note>>` w obiekcie
- `fun gen(Int tempo, Int duration) ->Song` - wewnętrznie dla  przypisanych obiektów do generacji tworzy `List<Gen>`, następnie dla określonego `tempo, duration` określa długość trwania odpowiednich tokenów nut i w pętli z każdego generatora uzyskuje kolejne nuty, finalnie otrzymując `List<List<Note>>` konstruując `Song`


#### Song
- obiekt przechowujący metadane, oraz nuty piosenki, użyty zostaje do wygenerowania midi

### Język wspiera operatory arytmetyczne/logiczne/znakowe/relacji porządku/specjalne muzyczne

#### Operatory


||Result|
|-|-|
|C1 + C2|[C1, C2]
|R_Q + R_H|[R_Q, R_H]|
|-[C1, C2]|[C2, C1]|
|[C1, C2, C3]*[R_Q, R_H]|[{C1, R_Q}, {C2, R_H}]//Note
|[C1]+[C2]|[C1, C2]|
|[{C1, R_Q}]+[{C2, R_Q}]|[{C1, R_Q}, {C2, R_Q}]|
|[{C1, R_Q}]*2|[{C1, R_Q}, {C1, R_Q}]|
|"a"*2|"aa"
|"a"+" "+"a"|"a a"|
|Markov()<< [{C1, R_Q}, {C2, R_Q}]|Increase count for C1->C2, R_Q->R_Q
|Markov()<< [{R_Q}, {C2, R_Q}]|Increase count for EMPTY->C2, R_Q->R_Q|
|Markov()+Markov()|Sum count matricies|
|[{R_Q}, {C2, R_Q}]==[{R_Q}, {C2, R_Q}]|true|
|[{R_Q}]==[{R_Q}, {C2, R_Q}]|false|

- język wspiera dla danej jednostki kontekst globalny, nie ma konieczności deklaracji funkcji `main()`, zmienne przesłaniane do najbliższego kontekstu
- język wspiera operację warunkową `if` oraz pętlę `for` - iteracja tylko po typach udostępniających iterator
- język wspiera deklarację funkcji:
  - typy prymitywne przekazywane przez wartość, złożone przez referencję
  - brak parametrów domyślnych
  - możliwość przeciążenia
  - wsparcie dla funkcji wyższego rzędu
  - wsparcie dla rekurencji
- język nie wspiera obsługi błędów w czasie wykonania programu - wystąpienie wyjątku kończy program z komunikatem
### Język wspiera operacje odczytu i zapisu plików MIDI, oraz manipulację danymi poprzez typy wbudowane

#### Odczyt

- `with (String, String, ...)` lub `with String` - konieczność przypisania do zmiennej by wskazać typ
- mając wskazany typ, wewnętrznie interpreter czytając plik:

|Typ|Wynik|
|-|-|
|Pitch[]|Przeczyta tylko wysokości nut

- język wspiera operacje wypisania na standardowe wyjście


Język będzie udostępniał:
- typ wyliczeniowy `Pitch` wysokości nut będący nazwami zastrzeżonymi w języku - `[A-G](#)?[1-7]`
- typ `Pitch[]` przechowujący sekwencje wysokości nut `Pitch[] a = [C5, D2, E4]`
- generator deterministyczny- treść generowana będzie pochodziła z sekwencji załadowanej do obiektu
- generator stochastyczny - treść generowana będzie wynikała z dwóch macierzy prawdopodobieństw przejść(dla melodii i rytmu) pomiędzy stanami, utworzona poprzez załadowanie obiektu (melodia/rytm) i zliczenie wystąpień danego przejścia pomiedzy stanami, oraz podanego stanu początkowego.



### Wymagania funkcjonalne

- język jest statycznie silnie typowany
- zmienne są domyślnie mutowalne
- język obsługuje podstawowe typy danych i odpowiednio operacje arytmetyczne/logiczne/znakowe
  - int, long, float, double - operacje: +,-,*,/, ()
  - bool - &&, ||
  - string - +, *
- język obsługuje relację równości, porządku
- zmienne mogą występować w kontekście globalnym, oraz ograniczone przez zakres {}
  - obowiązuje przesłanianie zmiennych do najbliższego kontekstu
- język obługuje instrukcję warunkową if
- język obługuje instrukcję warunkową for in
  - iteracja tylko po obiekcie będącym generatorem
- język pozwala na zdefiniowanie funkcji
  - typy primitywne przekazywane przez wartość
  - typy złożone przez referencję
  - brak możliwości definiowania parametrów domyślnych -> można przeciążać funkcje
- język zapewnia mechanizm błędów
  - `throw Exception` - możliwość obsłużenie przez program
  - `panic Excpetion` - brak możliwości obsłużenia
- język zapewnia wbudowane typy do zapisu nutowego - możliwość określenia wysokości oraz długości nuty
  - `Pitch a = Pitch.C1` // [A-G]\d możliwe wartości z góry określone
  - `Duration a = Duration.E` // {F, H, Q, E, S}
  - typy posiadają metody typowe dla danego typu - transpose, speed_up
- język zapewnia możliwość operacji pseudoarytmetycznych na typach muzycznych
  - dodawanie tworzy listę obiektów
  - mnożenie Pitch i Duration /list Pitch i Duration efektywnie wykonuje zip i tworzy Motive
- język zapewnia deterministyczny generatory nut
  - `Motive b=[{C1, H}, {B1, E}]` //b.gen() przy iteracji zwraca `b[i%len(b)]`
- język zapewnia stochastyczny generator nut `Markov`
  - generator posiada dwie macierze prawdopodobieństw przejść pomiędzy stanami - osobne dla wysokości i długości
  - modyfikacja macierzy odbywa się poprzez operator `<<` - do macierzy zostają dodane wystąpienia danego przejścia np `C1->B1`
  - `a.gen({C1, H})` zwraca generator stochastyczny, który dla aktualnego stanu losuje z wagami z macierzy stan kolejny
```
Motive m = [{C1, H}, {B1, E}]
Markov a
a << m
```

- język zapewnia wewnętrzy mechanizm odczytu i zapisu plików midi
  - `Motive a = with Motive 'lick.mid'` - parsing pliku `lick.mid` na typ Motive
  - `Markov b = with Markov ('lick.mid', 'melody.mid')` - parsing plików i wywołanie `b <<  'lick.mid' << 'melody.mid'` 
- język zapewnia stworzenie piosenki z generatorów nut
  - wywołanie `to_midi` pobiera kolejne nuty z każdego z podpietych generatorów do osobnych ścieżek, aż zostanie osiągnięta określona długość utworu

```
Markov a
Motive b
//...
Song s = make_song(125, 60) //tempo and duration

gen a s
gen b s

s.to_midi("file.mid")
```


### Przykłady kodu



```
fun createSnareMachine() -> motive{
    //[Q] - Q is quoter note pause
    //[{C1, Q}] - represents Note of Pitch C1 and duration quoter
    const motive simpleSnare = [Q, {C#5, Q}, Q, {C#5, Q}]
    const motive doubleSnare = [Q, {C#5, E}, {C#5, E}, Q, {C#5, Q}]
    motive snareMachine
    for i in range(4):
        if(i % 2 == 0){
            snareMachine += simpleSnare
        } else {
            snareMachine += doubleSnare
        }
    return snareMachine
}

fun fractalMelody(motive melody,(motive)->motive genRule, int depth)->motive{
    if(depth < 0){
        panic IllegalArgument("Depth cannot be less than 0")
    }
    if(depth == 0){
        return melody
    }
    transformedMelody = genRule(melody)
    return melody + fractalMelody(transformedMelody, genRule, depth - 1)
}

Markov randomMelody = with Markov "random_melody"

Song song = make_song(125, 60)

gen randomMelody song
gen createSnareMachine song
gen fractalMelody([{C5, Q}, {B5, E}, {F1, H}, E], (melody)->melody.transpose(1), 2) song

song.to_midi("song)
```

```
motive a = [{C2, Q}, {C4, Q}]

print(a.transpose(1)) //{C#2, Q}, {C#4, Q}
print(a.speed(2)) //{C#2, E}, {C#4, E}

motive b = [{C1, E}, E] //Note C1, pause

print(a+b) //{C2, Q}, {C4, Q}, {C1, E}, E
print(-b) //E, {C1, E}

it = a.gen()
for(i in range(4)){
    print(it.next())
} //{C2, Q}, {C4, Q}, {C2, Q}, {C4, Q}

for note in a{
    print(note)
}//{C2, Q}, {C4, Q}
```

```
Markov a //without filling will produce pause

Pitch b = [C5, C1, D4]
Duration c = [Q, E, H]

a << b //Make possible to transition from C5 to C1, and from C1 to D4
a << c

motive d = b*c //Hadamard product, trimmed to min(len(b), len(c))
melody e = [D4, F5]
b*c+e //Panic cannot add melody to motive
b*c+(e+G1)
```

```
Markov rockSolo = with Markov ("lick1", "lick2") as rockSoloMarkov{
    print(rockSoloMarkov)
    motive lick3 = [{C5, Q}, {B5, E}, {F1, H}, E]
    rockSoloMarkov << lick3

    motive lick4 = lick3 + {C3, E}
    
    motive lick5 = with "lick3"

    rockSoloMarkov << lick4 << lick5
}

mv rockRythm = with motive "chords"

song = make_song(125)

gen rockSolo song
gen rockRythm song

song.to_midi("song")
```


### Moduły

- interpreter języka
- obługa plików midi
- obluga łańcuchów markova