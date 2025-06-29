# clean-code-typescript [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Clean%20Code%20Typescript&url=https://github.com/mheob/clean-code-typescript)

Clean-Code-Konzepte angepasst für TypeScript.
Inspiriert von [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

## Inhalt<!-- omit in toc -->

- [Einführung](#einführung)
- [Variablen](#variablen)
- [Funktionen](#funktionen)
- [Objekte und Datenstrukturen](#objekte-und-datenstrukturen)
- [Klassen](#klassen)
- [SOLID](#solid)
- [Testen](#testen)
- [Asynchronität](#asynchronität)
- [Fehlerbehandlung](#fehlerbehandlung)
- [Formatierung](#formatierung)
- [Kommentare](#kommentare)
- [Übersetzungen](#übersetzungen)

## Einführung

![Humorvolles Bild der Software-Qualitätseinschätzung als Zählung, wie viele Schimpfwörter du beim Lesen von Code schreist](https://www.osnews.com/images/comics/wtfm.jpg)

Software-Entwicklungs-Prinzipien, aus Robert C. Martins Buch [_Clean Code_](https://amzn.to/33HgLXZ) (\* affiliate link), angepasst für TypeScript. Dies ist kein Style Guide. Es ist ein Leitfaden zur Erstellung von [lesbarer, wiederverwendbarer und refaktorierbarer](https://github.com/ryanmcdermott/3rs-of-software-architecture) Software in TypeScript.

Nicht jedes Prinzip hierin muss strikt befolgt werden, und noch weniger werden sie allgemein anerkannt sein. Dies hier sind Richtlinien und nichts weiter. Aber sie sind solche, die über viele Jahre kollektiver Erfahrung von den Autoren von _Clean Code_.

Unser Handwerk der Softwareentwicklung ist etwas mehr als 50 Jahre alt und wir lernen immer noch viel. Wenn die Softwarearchitektur so alt ist wie die Architektur selbst, dann werden wir vielleicht härtere Regeln haben, denen wir folgen können. Für den Moment sollen diese Richtlinien als Prüfstein dienen, um die Qualität des TypeScript-Codes zu beurteilen, den du und dein Team produzieren.

Und noch etwas: Wenn du diese Richtlinien kennst, wirst du nicht sofort ein besserer Softwareentwickler! Und wenn du jahrelang mit ihnen arbeitest, bedeutet das nicht, dass du keine Fehler machen wirst. Jedes Stück Code beginnt als erster Entwurf - wie nasser Ton - der in seine endgültige Form gebracht wird. Schließlich meißeln wir die Unvollkommenheiten weg, wenn wir es mit unseren Kollegen überprüfen. Mach dich nicht selbst fertig für deine ersten Entwürfe, die verbessert werden müssen. Verprügle stattdessen den Code!

**[⬆ zum Anfang](#inhalt)**

## Variablen

### Verwende aussagekräftige Variablennamen

Unterscheide die Namen so, dass der Leser weiß, was die Unterschiede bieten.

**Schlecht:**

```ts
function between<T>(a1: T, a2: T, a3: T): boolean {
  return a2 <= a1 && a1 <= a3;
}
```

**Gut:**

```ts
function between<T>(value: T, left: T, right: T): boolean {
  return left <= value && value <= right;
}
```

**[⬆ zum Anfang](#inhalt)**

### Verwende aussprechbare Variablennamen

Wenn du es nicht aussprechen kannst, kannst du nicht darüber diskutieren, ohne wie ein Idiot zu klingen.

**Schlecht:**

```ts
type DtaRcrd102 = {
  genymdhms: Date;
  modymdhms: Date;
  pszqint: number;
};
```

**Gut:**

```ts
type Customer = {
  generationTimestamp: Date;
  modificationTimestamp: Date;
  recordId: number;
};
```

**[⬆ zum Anfang](#inhalt)**

### Benutze das gleiche Vokabular für den gleichen Typ von Variablen

**Schlecht:**

```ts
function getUserInfo(): User;
function getUserDetails(): User;
function getUserData(): User;
```

**Gut:**

```ts
function getUser(): User;
```

**[⬆ zum Anfang](#inhalt)**

### Verwende durchsuchbare Namen

Wir werden mehr Code lesen als wir jemals schreiben werden. Es ist wichtig, dass der Code, den wir schreiben, lesbar und durchsuchbar sein muss. Indem wir Variablen, die für das Verständnis unseres Programms von Bedeutung sind, nicht gut benennen, schaden wir unseren Lesern. Mache deine Namen durchsuchbar. Tools wie [ESLint](https://typescript-eslint.io/) können helfen, unbenannte Konstanten und Variablen (auch bekannt als Magic Strings und Magic Numbers) zu identifizieren.

**Schlecht:**

```ts
// What the heck is 86400000 for?
setTimeout(restart, 86400000);
```

**Gut:**

```ts
// Declare them as capitalized named constants.
const MILLISECONDS_PER_DAY = 24 * 60 * 60 * 1000; // 86400000

setTimeout(restart, MILLISECONDS_PER_DAY);
```

**[⬆ zum Anfang](#inhalt)**

### Verwende erklärende Variablen

**Schlecht:**

```ts
declare const users: Map<string, User>;

for (const keyValue of users) {
  // iterate through users map
}
```

**Gut:**

```ts
declare const users: Map<string, User>;

for (const [id, user] of users) {
  // iterate through users map
}
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide Mental Mapping

Explizit ist besser als implizit.
_Klarheit ist der König._

**Schlecht:**

```ts
const u = getUser();
const s = getSubscription();
const t = charge(u, s);
```

**Gut:**

```ts
const user = getUser();
const subscription = getSubscription();
const transaction = charge(user, subscription);
```

**[⬆ zum Anfang](#inhalt)**

### Füge keinen unnötigen Kontext hinzu

Wenn dein Klassen-/Typ-/Objektname dir etwas sagt, dann wiederhole das nicht in deinem Variablennamen.

**Schlecht:**

```ts
type Car = {
  carMake: string;
  carModel: string;
  carColor: string;
};

function print(car: Car): void {
  console.log(`${car.carMake} ${car.carModel} (${car.carColor})`);
}
```

**Gut:**

```ts
type Car = {
  make: string;
  model: string;
  color: string;
};

function print(car: Car): void {
  console.log(`${car.make} ${car.model} (${car.color})`);
}
```

**[⬆ zum Anfang](#inhalt)**

### Verwende Standardargumente statt Kurzschlüsse oder Konditionale

Standardargumente sind oft sauberer als Kurzschlüsse.

**Schlecht:**

```ts
function loadPages(count?: number) {
  const loadCount = count !== undefined ? count : 10;
  // ...
}
```

**Gut:**

```ts
function loadPages(count: number = 10) {
  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

### Verwende `enum` um den Sinn zu dokumentieren

`Enums` können dir helfen, den Sinn des Codes zu dokumentieren. Zum Beispiel, wenn es darum geht, dass die Werte unterschiedlich sind, anstatt den genauen Wert von denen.

**Schlecht:**

```ts
const GENRE = {
  ROMANTIC: "romantic",
  DRAMA: "drama",
  COMEDY: "comedy",
  DOCUMENTARY: "documentary",
};

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
      // some logic to be executed
    }
  }
}
```

**Gut:**

```ts
enum GENRE {
  ROMANTIC,
  DRAMA,
  COMEDY,
  DOCUMENTARY,
}

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
      // some logic to be executed
    }
  }
}
```

**[⬆ zum Anfang](#inhalt)**

## Funktionen

### Funktionsargumente (idealerweise 2 oder weniger)

Die Begrenzung der Anzahl von Funktionsparametern ist unglaublich wichtig. Es macht das Testen deiner Funktion einfacher. Mehr als drei Parameter führen zu einer kombinatorischen Explosion, bei der du tonnenweise verschiedene Fälle mit jedem einzelnen Argument testen musst.

Ein oder zwei Argumente sind der Idealfall. Drei sollten wenn möglich vermieden werden. Alles, was darüber hinausgeht, sollte auf keinen Fall angewandt werden. Normalerweise, wenn du mehr als zwei Argumente hast, dann versucht deine Funktion zu viel zu machen. In den Fällen, in denen das nicht der Fall ist, reicht meistens ein übergeordnetes Objekt als Argument aus.

Ziehe die Verwendung von Objektliteralen in Betracht, wenn du feststellst, dass du viele Argumente brauchst.

Um deutlich zu machen, welche Eigenschaften die Funktion erwartet, kannst du die [destructuring](https://basarat.gitbook.io/typescript/future-javascript/destructuring)-Syntax verwenden. Dies hat ein paar Vorteile:

1. Wenn sich jemand die Funktionssignatur ansieht, ist sofort klar, welche Eigenschaften verwendet werden.
2. Es kann verwendet werden, um benannte Parameter zu simulieren.
3. Die Destrukturierung klont auch die angegebenen primitiven Werte des in die Funktion übergebenen Argumentobjekts. Dies kann helfen, Seiteneffekte zu vermeiden. Hinweis: Objekte und Arrays, die vom Argument-Objekt destrukturiert werden, werden NICHT geklont.
4. TypeScript warnt dich vor unbenutzten Eigenschaften, was ohne Destrukturierung unmöglich wäre.

**Schlecht:**

```ts
function createMenu(
  title: string,
  body: string,
  buttonText: string,
  cancellable: boolean
) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);
```

**Gut:**

```ts
function createMenu(options: {
  title: string;
  body: string;
  buttonText: string;
  cancellable: boolean;
}) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

Du kannst die Lesbarkeit weiter verbessern, indem du [type aliases](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases) verwendest:

```ts
type MenuOptions = {
  title: string;
  body: string;
  buttonText: string;
  cancellable: boolean;
};

function createMenu(options: MenuOptions) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

**[⬆ zum Anfang](#inhalt)**

### Funktionen sollten eine Sache machen

Dies ist bei weitem die wichtigste Regel in der Softwareentwicklung. Wenn Funktionen mehr als eine Sache machen, sind sie schwieriger zu verwalten, zu testen und zu verstehen. Wenn du eine Funktion auf nur eine Aktion isolieren kannst, kann sie leicht refaktorisiert werden und dein Code wird sich viel sauberer lesen. Wenn du nichts anderes aus diesem Leitfaden mitnimmst als das, wirst du vielen Entwicklern voraus sein.

**Schlecht:**

```ts
function emailActiveClients(clients: Client[]) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Gut:**

```ts
function emailActiveClients(clients: Client[]) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client: Client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ zum Anfang](#inhalt)**

### Funktionsnamen sollten sagen was sie machen

**Schlecht:**

```ts
function addToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Gut:**

```ts
function addMonthToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();
addMonthToDate(date, 1);
```

**[⬆ zum Anfang](#inhalt)**

### Funktionen sollten nur eine Abstraktionsebene sein

Wenn du mehr als eine Abstraktionsebene hast, macht deine Funktion normalerweise zu viel. Das Aufteilen von Funktionen führt zu Wiederverwendbarkeit und einfacherem Testen.

**Schlecht:**

```ts
function parseCode(code: string) {
  const REGEXES = [
    /* ... */
  ];
  const statements = code.split(" ");
  const tokens = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Gut:**

```ts
const REGEXES = [
  /* ... */
];

function parseCode(code: string) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);

  syntaxTree.forEach((node) => {
    // parse...
  });
}

function tokenize(code: string): Token[] {
  const statements = code.split(" ");
  const tokens: Token[] = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens: Token[]): SyntaxTree {
  const syntaxTree: SyntaxTree[] = [];
  tokens.forEach((token) => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ zum Anfang](#inhalt)**

### Entferne duplizierten Code

Gib dein Bestes, um doppelten Code zu vermeiden. Doppelter Code ist schlecht, weil er bedeutet, dass es mehr als eine Stelle gibt, an der du etwas ändern kannst, wenn du eine Logik ändern musst.

Stell dir vor, du betreibst ein Restaurant und behältst den Überblick über dein Inventar: all deine Tomaten, Zwiebeln, Knoblauch, Gewürze, etc. Wenn du mehrere Listen hast, auf denen du dies festhältst, dann müssen alle aktualisiert werden, wenn du ein Gericht mit Tomaten darin servierst. Wenn du nur eine Liste hast, gibt es nur einen Ort zum Aktualisieren!

Oftmals hast du doppelten Code, weil du zwei oder mehr leicht unterschiedliche Dinge hast, die viel gemeinsam haben, aber ihre Unterschiede zwingen dich dazu, zwei oder mehr separate Funktionen zu haben, die viel vom Gleichen machen. Doppelten Code zu entfernen bedeutet, eine Abstraktion zu schaffen, die diese verschiedenen Dinge mit nur einer Funktion/Modul/Klasse behandeln kann.

Die richtige Abstraktion zu finden ist kritisch, deshalb solltest du den [SOLID](#solid) Prinzipien folgen. Schlechte Abstraktionen können schlimmer sein als doppelter Code, also sei vorsichtig! Wenn du also eine gute Abstraktion machen kannst, dann tu es! Wiederhole dich nicht, sonst wirst du dich dabei ertappen, dass du jedes Mal, wenn du eine Sache ändern willst, mehrere Stellen aktualisieren musst.

**Schlecht:**

```ts
function showDeveloperList(developers: Developer[]) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();

    const data = {
      expectedSalary,
      experience,
      githubLink,
    };

    render(data);
  });
}

function showManagerList(managers: Manager[]) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();

    const data = {
      expectedSalary,
      experience,
      portfolio,
    };

    render(data);
  });
}
```

**Gut:**

```ts
class Developer {
  // ...
  getExtraDetails() {
    return {
      githubLink: this.githubLink,
    };
  }
}

class Manager {
  // ...
  getExtraDetails() {
    return {
      portfolio: this.portfolio,
    };
  }
}

function showEmployeeList(employee: (Developer | Manager)[]) {
  employee.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();
    const extra = employee.getExtraDetails();

    const data = {
      expectedSalary,
      experience,
      extra,
    };

    render(data);
  });
}
```

Du kannst auch einen Union-Typ oder eine gemeinsame Elternklasse hinzufügen, wenn das zu deiner Abstraktion passt.

```ts
class Developer {
  // ...
}

class Manager {
  // ...
}

type Employee = Developer | Manager;

function showEmployeeList(employee: Employee[]) {
  // ...
}
```

Du solltest kritisch gegenüber Code-Duplizierung sein. Manchmal gibt es einen Kompromiss zwischen doppeltem Code und erhöhter Komplexität durch die Einführung unnötiger Abstraktion. Wenn zwei Implementierungen aus zwei verschiedenen Modulen ähnlich aussehen, aber in verschiedenen Domänen leben, kann die Duplizierung akzeptabel sein und dem Extrahieren des gemeinsamen Codes vorgezogen werden. Der extrahierte gemeinsame Code führt in diesem Fall eine indirekte Abhängigkeit zwischen den beiden Modulen ein.

**[⬆ zum Anfang](#inhalt)**

### Setze Standardobjekte mit `Object.assign` oder Destrukturierung

**Schlecht:**

```ts
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

function createMenu(config: MenuConfig) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;

  // ...
}

createMenu({ body: "Bar" });
```

**Gut:**

```ts
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

function createMenu(config: MenuConfig) {
  const menuConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true,
    },
    config
  );

  // ...
}

createMenu({ body: "Bar" });
```

Oder du kannst den Spread-Operator verwenden:

```ts
function createMenu(config: MenuConfig) {
  const menuConfig = {
    title: "Foo",
    body: "Bar",
    buttonText: "Baz",
    cancellable: true,
    ...config,
  };

  // ...
}
```

Der Spread-Operator und `Object.assign()` sind sich sehr ähnlich.
Der Hauptunterschied besteht darin, dass "Spreading" neue Eigenschaften definiert, während `Object.assign()` sie festlegt. Ausführlicher wird der Unterschied in [diesem Thread erklärt](https://stackoverflow.com/questions/32925460/object-spread-vs-object-assign).

Alternativ kannst du auch eine Destrukturierung mit Standardwerten verwenden:

```ts
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

function createMenu({
  title = "Foo",
  body = "Bar",
  buttonText = "Baz",
  cancellable = true,
}: MenuConfig) {
  // ...
}

createMenu({ body: "Bar" });
```

Um Seiteneffekte und unerwartetes Verhalten durch die explizite Übergabe von `undefined` oder `null` Werten zu vermeiden, kannst du dem TypeScript Compiler sagen, dass er dies nicht zulassen soll. Siehe die [`--strictNullChecks`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#--strictnullchecks)-Option in TypeScript.

**[⬆ zum Anfang](#inhalt)**

### Verwende keine Flags als Funktionsparameter

Flags sagen deinem Benutzer, dass diese Funktion mehr als eine Sache macht. Funktionen sollten nur eine Sache machen. Teile deine Funktionen auf, wenn sie verschiedene Codepfade basierend auf einem Boolean verfolgen.

**Schlecht:**

```ts
function createFile(name: string, temp: boolean) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Gut:**

```ts
function createTempFile(name: string) {
  createFile(`./temp/${name}`);
}

function createFile(name: string) {
  fs.create(name);
}
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide Nebenwirkungen (Teil 1)

Eine Funktion erzeugt einen Seiteneffekt, wenn sie etwas anderes macht, als einen Wert aufzunehmen und einen anderen Wert oder Werte zurückzugeben. Ein Nebeneffekt könnte das Schreiben in eine Datei, das Ändern einer globalen Variable oder das versehentliche Überweisen deines gesamten Geldes an einen Fremden sein.

Nun, du brauchst gelegentlich Seiteneffekte in einem Programm. Wie im vorherigen Beispiel, musst du vielleicht in eine Datei schreiben. Was du machen willst, ist, den Ort, an dem du dies tust, zu zentralisieren. Du solltest nicht mehrere Funktionen und Klassen haben, die in eine bestimmte Datei schreiben. Habe einen Dienst, der das macht. Einen und nur einen.

Der wichtigste Punkt ist, die üblichen Fallstricke zu vermeiden, wie das Teilen von Zuständen zwischen Objekten ohne jegliche Struktur, die Verwendung von veränderbaren Datentypen, die von jedem geschrieben werden können, und die fehlende Zentralisierung der Seiteneffekte. Wenn du das schaffst, wirst du glücklicher sein als die große Mehrheit der anderen Programmierer.

**Schlecht:**

```ts
// Global variable referenced by following function.
let name = "Robert C. Martin";

function toBase64() {
  name = btoa(name);
}

toBase64();
// If we had another function that used this name, now it'd be a Base64 value

console.log(name); // expected to print 'Robert C. Martin' but instead 'Um9iZXJ0IEMuIE1hcnRpbg=='
```

**Gut:**

```ts
const name = "Robert C. Martin";

function toBase64(text: string): string {
  return btoa(text);
}

const encodedName = toBase64(name);
console.log(name);
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide Nebenwirkungen (Teil 2)

Browser und Node.js verarbeiten nur JavaScript, daher muss jeder TypeScript-Code vor dem Ausführen oder Debuggen kompiliert werden. In JavaScript sind einige Werte unveränderlich (immutable) und andere veränderbar (mutable). Objekte und Arrays sind zwei Arten von veränderbaren Werten, daher ist es wichtig, sie sorgfältig zu behandeln, wenn sie als Parameter an eine Funktion übergeben werden. Eine JavaScript-Funktion kann die Eigenschaften eines Objekts oder den Inhalt eines Arrays ändern, was leicht zu Fehlern an anderer Stelle führen kann.

Nehmen wir an, es gibt eine Funktion, die einen Array-Parameter akzeptiert, der einen Warenkorb darstellt. Wenn die Funktion eine Änderung in diesem Warenkorb-Array vornimmt - z. B. indem sie einen Artikel zum Kauf hinzufügt -, dann wird jede andere Funktion, die dasselbe Warenkorb-Array verwendet, von dieser Änderung betroffen sein. Das mag toll sein, kann aber auch schlecht sein. Stellen wir uns eine schlechte Situation vor:

Der Benutzer klickt auf den "Purchase" Button, der eine `purchase` Funktion aufruft, die eine Netzwerkanfrage stellt und das `cart` Array an den Server sendet. Aufgrund einer schlechten Netzwerkverbindung muss die Funktion `purchase` die Anfrage immer wieder neu versuchen. Was passiert, wenn der Nutzer in der Zwischenzeit versehentlich auf einen Artikel klickt, den er eigentlich gar nicht haben will, bevor die Netzwerkanfrage gestartet wird? Wenn das passiert und die Netzwerkanfrage beginnt, sendet die `purchase` Funktion den versehentlich hinzugefügten Artikel, weil das Array `cart` geändert wurde.

Eine gute Lösung wäre, wenn die Funktion `addItemToCart` immer `cart` klont, ihn bearbeiten und den Klon zurückgeben würde. Das würde sicherstellen, dass Funktionen, die noch den alten Warenkorb verwenden, nicht von den Änderungen betroffen sind.

Zwei Vorbehalte sind bei diesem Ansatz zu erwähnen:

1. Es kann Fälle geben, in denen du das Eingabeobjekt tatsächlich ändern möchtest, aber wenn du diese Programmierpraxis anwendest, wirst du feststellen, dass diese Fälle ziemlich selten sind. Die meisten Dinge können so refaktorisiert werden, dass sie keine Seiteneffekte haben! (siehe [pure function](https://en.wikipedia.org/wiki/Pure_function))
2. Das Klonen von großen Objekten kann sehr teuer in Bezug auf die Performance sein. Glücklicherweise ist dies in der Praxis kein großes Problem, da es [großartige Bibliotheken](https://github.com/immutable-js/immutable-js) gibt, die es ermöglichen, dass diese Art von Programmieransatz schnell und nicht so speicherintensiv ist, wie es für dich wäre, wenn du Objekte und Arrays manuell klonen würdest.

**Schlecht:**

```ts
function addItemToCart(cart: CartItem[], item: Item): void {
  cart.push({ item, date: Date.now() });
}
```

**Gut:**

```ts
function addItemToCart(cart: CartItem[], item: Item): CartItem[] {
  return [...cart, { item, date: Date.now() }];
}
```

**[⬆ zum Anfang](#inhalt)**

### Schreibe nicht in globale Funktionen

Globals zu verunreinigen ist eine schlechte Praxis in JavaScript, da du mit einer anderen Bibliothek kollidieren könntest und der Benutzer deiner API nichts davon mitbekommt, bis er in der Produktion eine Ausnahme bekommt. Lass uns über ein Beispiel nachdenken: Was wäre, wenn du JavaScript's native Array-Methode um eine `diff`-Methode erweitern wolltest, die den Unterschied zwischen zwei Arrays anzeigen könnte? Du könntest deine neue Funktion in den `Array.prototype` schreiben, aber sie könnte mit einer anderen Bibliothek kollidieren, die versucht, das Gleiche zu tun. Was wäre, wenn diese andere Bibliothek einfach `diff` benutzen würde, um den Unterschied zwischen dem ersten und letzten Element eines Arrays zu finden? Deshalb wäre es viel besser, nur Klassen zu verwenden und einfach das `Array` global zu erweitern.

**Schlecht:**

```ts
declare global {
  interface Array<T> {
    diff(other: T[]): Array<T>;
  }
}

if (!Array.prototype.diff) {
  Array.prototype.diff = function <T>(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter((elem) => !hash.has(elem));
  };
}
```

**Gut:**

```ts
class MyArray<T> extends Array<T> {
  diff(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter((elem) => !hash.has(elem));
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Funktionale Programmierung gegenüber imperativer Programmierung bevorzugen

Bevorzuge diesen Stil der Programmierung, wenn du kannst.

**Schlecht:**

```ts
const contributions = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

let totalOutput = 0;

for (let i = 0; i < contributions.length; i++) {
  totalOutput += contributions[i].linesOfCode;
}
```

**Gut:**

```ts
const contributions = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

const totalOutput = contributions.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ zum Anfang](#inhalt)**

### Konditionale kapseln

**Schlecht:**

```ts
if (subscription.isTrial || account.balance > 0) {
  // ...
}
```

**Gut:**

```ts
function canActivateService(subscription: Subscription, account: Account) {
  return subscription.isTrial || account.balance > 0;
}

if (canActivateService(subscription, account)) {
  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide negative Konditionale

**Schlecht:**

```ts
function isEmailNotUsed(email: string): boolean {
  // ...
}

if (isEmailNotUsed(email)) {
  // ...
}
```

**Gut:**

```ts
function isEmailUsed(email: string): boolean {
  // ...
}

if (!isEmailUsed(email)) {
  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

### Konditionale vermeiden

Das scheint eine unmögliche Aufgabe zu sein. Wenn man das zum ersten Mal hört, sagen die meisten Leute: "Wie soll ich etwas ohne eine `if`-Anweisung machen?" Die Antwort ist, dass du Polymorphismus verwenden kannst, um die gleiche Aufgabe in vielen Fällen zu erreichen. Die zweite Frage ist in der Regel: "Nun, das ist toll, aber warum sollte ich das tun wollen?" Die Antwort ist ein früheres Clean Code Konzept, das wir gelernt haben: eine Funktion sollte nur eine Sache tun. Wenn du Klassen und Funktionen hast, die `if` Anweisungen haben, sagst du deinem Benutzer, dass deine Funktion mehr als eine Sache macht. Denke daran, nur eine Sache zu tun.

**Schlecht:**

```ts
class Airplane {
  private type: string;
  // ...

  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
      default:
        throw new Error("Unknown airplane type.");
    }
  }

  private getMaxAltitude(): number {
    // ...
  }
}
```

**Gut:**

```ts
abstract class Airplane {
  protected getMaxAltitude(): number {
    // shared logic with subclasses ...
  }

  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide Typenprüfung

TypeScript ist ein strenges syntaktisches Superset von JavaScript und fügt der Sprache eine optionale statische Typüberprüfung hinzu. Bevorzuge es immer, die Typen von Variablen, Parametern und Rückgabewerten zu spezifizieren, um die volle Leistung der TypeScript Features zu nutzen. Es macht das Refactoring einfacher.

**Schlecht:**

```ts
function travelToTexas(vehicle: Bicycle | Car) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(currentLocation, new Location("texas"));
  }
}
```

**Gut:**

```ts
type Vehicle = Bicycle | Car;

function travelToTexas(vehicle: Vehicle) {
  vehicle.move(currentLocation, new Location("texas"));
}
```

**[⬆ zum Anfang](#inhalt)**

### Nicht über-optimieren

Moderne Browser machen eine Menge Optimierungen unter der Haube zur Laufzeit. Wenn du optimierst, verschwendest du in vielen Fällen nur deine Zeit. Es gibt gute [Ressourcen](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers), um zu sehen, wo es an Optimierung mangelt. Nimm diese in der Zwischenzeit ins Visier, bis sie behoben sind, wenn sie es können.

**Schlecht:**

```ts
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Gut:**

```ts
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

### Entferne totem Code

Toter Code ist genau so schlimm wie doppelter Code. Es gibt keinen Grund, ihn in deiner Codebase zu behalten. Wenn er nicht aufgerufen wird, entferne ihn! Er wird immer noch sicher in deiner Versionsgeschichte sein, wenn du ihn noch brauchst.

**Schlecht:**

```ts
function oldRequestModule(url: string) {
  // ...
}

function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Gut:**

```ts
function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ zum Anfang](#inhalt)**

### Verwende Iteratoren und Generatoren

Verwende Generatoren und Iterables, wenn du mit Sammlungen von Daten arbeitest, die wie ein Stream verwendet werden.
Dafür gibt es einige gute Gründe:

- entkoppelt den Aufrufer von der Generatorimplementierung in dem Sinne, dass der Aufrufer entscheidet, auf wie viele Items er zugreift
- Lazy Execution, Items werden on-demand gestreamt
- eingebaute Unterstützung für die Iteration von Items mit der `for-of` Syntax
- Iterables erlauben die Implementierung optimierter Iteratormuster

**Schlecht:**

```ts
function fibonacci(n: number): number[] {
  if (n === 1) return [0];
  if (n === 2) return [0, 1];

  const items: number[] = [0, 1];
  while (items.length < n) {
    items.push(items[items.length - 2] + items[items.length - 1]);
  }

  return items;
}

function print(n: number) {
  fibonacci(n).forEach((fib) => console.log(fib));
}

// Print first 10 Fibonacci numbers.
print(10);
```

**Gut:**

```ts
// Generates an infinite stream of Fibonacci numbers.
// The generator doesn't keep the array of all numbers.
function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

function print(n: number) {
  let i = 0;
  for (const fib of fibonacci()) {
    if (i++ === n) break;
    console.log(fib);
  }
}

// Print first 10 Fibonacci numbers.
print(10);
```

Es gibt Bibliotheken, die es erlauben, mit Iterables auf ähnliche Weise wie mit nativen Arrays zu arbeiten, indem Methoden wie `map`, `slice`, `forEach` etc. verkettet werden. Siehe [itiriri](https://www.npmjs.com/package/itiriri) für ein Beispiel der fortgeschrittenen Manipulation mit Iterables (oder [itiriri-async](https://www.npmjs.com/package/itiriri-async) für die Manipulation von asynchronen Iterables).

```ts
import itiriri from "itiriri";

function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

itiriri(fibonacci())
  .take(10)
  .forEach((fib) => console.log(fib));
```

**[⬆ zum Anfang](#inhalt)**

## Objekte und Datenstrukturen

### Verwende Getter und Setter

TypeScript unterstützt die Getter/Setter-Syntax. Getter und Setter zu verwenden, um auf Daten von Objekten zuzugreifen, die Verhalten kapseln, könnte besser sein, als einfach nach einer Eigenschaft auf einem Objekt zu suchen. "Warum?", magst du fragen. Nun, hier ist eine Liste von Gründen:

- Wenn du mehr tun willst, als nur eine Objekteigenschaft zu erhalten, musst du nicht jeden Accessor in deiner Codebase nachschlagen und ändern.
- Macht das Hinzufügen einer Validierung einfach, wenn du ein `set` machst.
- Verkapselt die interne Repräsentation.
- Einfaches Hinzufügen von Logging und Fehlerbehandlung beim Holen und Setzen.
- Du kannst die Eigenschaften deines Objekts "lazy" laden, z.B. wenn du es von einem Server holst.

**Schlecht:**

```ts
type BankAccount = {
  balance: number;
  // ...
};

const value = 100;
const account: BankAccount = {
  balance: 0,
  // ...
};

if (value < 0) {
  throw new Error("Cannot set negative balance.");
}

account.balance = value;
```

**Gut:**

```ts
class BankAccount {
  private accountBalance: number = 0;

  get balance(): number {
    return this.accountBalance;
  }

  set balance(value: number) {
    if (value < 0) {
      throw new Error("Cannot set negative balance.");
    }

    this.accountBalance = value;
  }

  // ...
}

// Now `BankAccount` encapsulates the validation logic.
// If one day the specifications change, and we need extra validation rule,
// we would have to alter only the `setter` implementation,
// leaving all dependent code unchanged.
const account = new BankAccount();
account.balance = 100;
```

**[⬆ zum Anfang](#inhalt)**

### Versehe Objekte mit privaten/geschützten Mitgliedern

TypeScript unterstützt `public` _(default)_, `protected` und `private` Accessoren auf Klassenmitglieder.

**Schlecht:**

```ts
class Circle {
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**Gut:**

```ts
class Circle {
  constructor(private readonly radius: number) {}

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Bevorzuge Unveränderbarkeit

TypeScripts Typsystem erlaubt es dir, einzelne Eigenschaften auf einer Schnittstelle/Klasse als _readonly_ zu markieren. Dies erlaubt es dir, auf funktionale Weise zu arbeiten (eine unerwartete Mutation ist schlecht).
Für fortgeschrittenere Szenarien gibt es einen eingebauten Typ `Readonly`, der einen Typ `T` nimmt und alle seine Eigenschaften als readonly markiert, indem er gemappte Typen verwendet (siehe [gemappte Typen](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)).

**Schlecht:**

```ts
interface Config {
  host: string;
  port: string;
  db: string;
}
```

**Gut:**

```ts
interface Config {
  readonly host: string;
  readonly port: string;
  readonly db: string;
}
```

Im Fall von Array kannst du ein schreibgeschütztes Array erstellen, indem du `ReadonlyArray<T>` verwendest. Sie erlauben keine Änderungen wie `push()` und `fill()`, können aber Funktionen wie `concat()` und `slice()` verwenden, die den Wert nicht verändern.

**Schlecht:**

```ts
const array: number[] = [1, 3, 5];
array = []; // error
array.push(100); // array will be updated
```

**Gut:**

```ts
const array: ReadonlyArray<number> = [1, 3, 5];
array = []; // error
array.push(100); // error
```

Das Deklarieren von Nur-Lese-Argumenten in [TypeScript 3.4 ist ein bisschen einfacher](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#improvements-for-readonlyarray-and-readonly-tuples).

```ts
function hoge(args: readonly string[]) {
  args.push(1); // error
}
```

Bevorzuge [const-assertions](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#const-assertions) für literalische Werte.

**Schlecht:**

```ts
const config = {
  hello: "world",
};
config.hello = "world"; // value is changed

const array = [1, 3, 5];
array[0] = 10; // value is changed

// writable objects is returned
function readonlyData(value: number) {
  return { value };
}

const result = readonlyData(100);
result.value = 200; // value is changed
```

**Gut:**

```ts
// read-only object
const config = {
  hello: "world",
} as const;
config.hello = "world"; // error

// read-only array
const array = [1, 3, 5] as const;
array[0] = 10; // error

// You can return read-only objects
function readonlyData(value: number) {
  return { value } as const;
}

const result = readonlyData(100);
result.value = 200; // error
```

**[⬆ zum Anfang](#inhalt)**

### `type` vs. `interface`

Verwende `type`, wenn du eine Vereinigung oder Kreuzung brauchst. Verwende ein `Interface`, wenn du `extends` oder `implements` brauchst. Es gibt keine strikte Regel, verwende die, die für dich funktioniert.
Für eine detailliertere Erklärung siehe diese [Antwort](https://stackoverflow.com/questions/37233735/typescript-interfaces-vs-types/54101543#54101543) über die Unterschiede zwischen `type` und `interface` in TypeScript.

**Schlecht:**

```ts
interface EmailConfig {
  // ...
}

interface DbConfig {
  // ...
}

interface Config {
  // ...
}

//...

type Shape = {
  // ...
};
```

**Gut:**

```ts
type EmailConfig = {
  // ...
};

type DbConfig = {
  // ...
};

type Config = EmailConfig | DbConfig;

// ...

interface Shape {
  // ...
}

class Circle implements Shape {
  // ...
}

class Square implements Shape {
  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

## Klassen

### Die Klassen sollten klein sein

Die Größe der Klasse wird an ihrer Verantwortung gemessen. Nach dem _Single Responsibility Prinzip_ sollte eine Klasse klein sein.

**Schlecht:**

```ts
class Dashboard {
  getLanguage(): string {
    /* ... */
  }
  setLanguage(language: string): void {
    /* ... */
  }
  showProgress(): void {
    /* ... */
  }
  hideProgress(): void {
    /* ... */
  }
  isDirty(): boolean {
    /* ... */
  }
  disable(): void {
    /* ... */
  }
  enable(): void {
    /* ... */
  }
  addSubscription(subscription: Subscription): void {
    /* ... */
  }
  removeSubscription(subscription: Subscription): void {
    /* ... */
  }
  addUser(user: User): void {
    /* ... */
  }
  removeUser(user: User): void {
    /* ... */
  }
  goToHomePage(): void {
    /* ... */
  }
  updateProfile(details: UserDetails): void {
    /* ... */
  }
  getVersion(): string {
    /* ... */
  }
  // ...
}
```

**Gut:**

```ts
class Dashboard {
  disable(): void {
    /* ... */
  }
  enable(): void {
    /* ... */
  }
  getVersion(): string {
    /* ... */
  }
}

// split the responsibilities by moving the remaining methods to other classes
// ...
```

**[⬆ zum Anfang](#inhalt)**

### Hohe Kohäsion und geringe Kopplung

Kohäsion definiert den Grad, in dem die Klassenmitglieder miteinander in Beziehung stehen. Idealerweise sollten alle Felder innerhalb einer Klasse von jeder Methode verwendet werden. Wir sagen dann, dass die Klasse _maximal kohäsiv_ ist. In der Praxis ist dies jedoch nicht immer möglich und auch nicht ratsam. Du solltest es jedoch bevorzugen, dass die Kohäsion hoch ist.

Kopplung bezieht sich darauf, wie verwandt oder abhängig zwei Klassen zueinander sind. Man sagt, dass Klassen niedrig gekoppelt sind, wenn Änderungen in einer von ihnen keine Auswirkungen auf die andere haben.

Gutes Softwaredesign hat **hohe Kohäsion** und **niedrige Kopplung**.

**Schlecht:**

```ts
class UserManager {
  // Bad: each private variable is used by one or another group of methods.
  // It makes clear evidence that the class is holding more than a single responsibility.
  // If I need only to create the service to get the transactions for a user,
  // I'm still forced to pass and instance of `emailSender`.
  constructor(
    private readonly db: Database,
    private readonly emailSender: EmailSender
  ) {}

  async getUser(id: number): Promise<User> {
    return await db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await db.transactions.find({ userId });
  }

  async sendGreeting(): Promise<void> {
    await emailSender.send("Welcome!");
  }

  async sendNotification(text: string): Promise<void> {
    await emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**Gut:**

```ts
class UserService {
  constructor(private readonly db: Database) {}

  async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }
}

class UserNotifier {
  constructor(private readonly emailSender: EmailSender) {}

  async sendGreeting(): Promise<void> {
    await this.emailSender.send("Welcome!");
  }

  async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Bevorzuge Komposition über Vererbung

Wie schon in [Design Patterns](https://en.wikipedia.org/wiki/Design_Patterns) von der "Gang of Four" gesagt, solltest du _Komposition gegenüber Vererbung bevorzugen_, wo du kannst. Es gibt viele gute Gründe, Vererbung zu verwenden und viele gute Gründe, Komposition zu verwenden. Der Hauptpunkt für diese Maxime ist, dass wenn dein Verstand instinktiv zu Vererbung greift, versuche zu überlegen, ob Komposition dein Problem besser modellieren könnte. In einigen Fällen kann sie das.

Du fragst dich dann vielleicht: "Wann sollte ich Vererbung verwenden?" Das hängt von deinem Problem ab, aber dies ist eine anständige Liste, wann Vererbung mehr Sinn macht als Komposition:

1. Deine Vererbung repräsentiert eine "is-a"-Beziehung und nicht eine "hat-a"-Beziehung (Mensch &rarr; Tier vs. Benutzer &rarr; BenutzerDetails).
2. Du kannst Code aus den Basisklassen wiederverwenden (Menschen können sich wie alle Tiere bewegen).
3. Du willst globale Änderungen an abgeleiteten Klassen vornehmen, indem du eine Basisklasse änderst. (Ändere den Kalorienverbrauch aller Tiere, wenn sie sich bewegen).

**Schlecht:**

```ts
class Employee {
  constructor(private readonly name: string, private readonly email: string) {}

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(
    name: string,
    email: string,
    private readonly ssn: string,
    private readonly salary: number
  ) {
    super(name, email);
  }

  // ...
}
```

**Gut:**

```ts
class Employee {
  private taxData: EmployeeTaxData;

  constructor(private readonly name: string, private readonly email: string) {}

  setTaxData(ssn: string, salary: number): Employee {
    this.taxData = new EmployeeTaxData(ssn, salary);
    return this;
  }

  // ...
}

class EmployeeTaxData {
  constructor(public readonly ssn: string, public readonly salary: number) {}

  // ...
}
```

**[⬆ zum Anfang](#inhalt)**

### Verwende Methodenverkettung

Dieses Muster ist sehr nützlich und wird häufig in vielen Bibliotheken verwendet. Es erlaubt deinem Code, ausdrucksstark und weniger langatmig zu sein. Aus diesem Grund solltest du die Methodenverkettung nutzen und dir ansehen, wie sauber dein Code sein wird.

**Schlecht:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): void {
    this.collection = collection;
  }

  page(number: number, itemsPerPage: number = 100): void {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
  }

  orderBy(...fields: string[]): void {
    this.orderByFields = fields;
  }

  build(): Query {
    // ...
  }
}

// ...

const queryBuilder = new QueryBuilder();
queryBuilder.from("users");
queryBuilder.page(1, 100);
queryBuilder.orderBy("firstName", "lastName");

const query = queryBuilder.build();
```

**Gut:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): this {
    this.collection = collection;
    return this;
  }

  page(number: number, itemsPerPage: number = 100): this {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
    return this;
  }

  orderBy(...fields: string[]): this {
    this.orderByFields = fields;
    return this;
  }

  build(): Query {
    // ...
  }
}

// ...

const query = new QueryBuilder()
  .from("users")
  .page(1, 100)
  .orderBy("firstName", "lastName")
  .build();
```

**[⬆ zum Anfang](#inhalt)**

## SOLID

### Prinzip der einzigen Verantwortung - Single Responsibility Principle (SRP)

Wie es in Clean Code heißt: "Es sollte nie mehr als einen Grund für eine Klasse geben, sich zu ändern". Es ist verlockend, eine Klasse mit einer Menge Funktionalität vollzupacken, wie wenn du nur einen Koffer mit auf deinen Flug nehmen kannst. Das Problem dabei ist, dass deine Klasse nicht konzeptionell zusammenhängend sein wird und es viele Gründe gibt, sie zu ändern. Es ist wichtig, dass du die Zeit, die du brauchst, um eine Klasse zu ändern, minimierst. Es ist wichtig, denn wenn zu viel Funktionalität in einer Klasse ist und du einen Teil davon änderst, kann es schwierig sein zu verstehen, wie sich das auf andere abhängige Module in deiner Codebasis auswirkt.

**Schlecht:**

```ts
class UserSettings {
  constructor(private readonly user: User) {}

  changeSettings(settings: UserSettings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Gut:**

```ts
class UserAuth {
  constructor(private readonly user: User) {}

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  private readonly auth: UserAuth;

  constructor(private readonly user: User) {
    this.auth = new UserAuth(user);
  }

  changeSettings(settings: UserSettings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Open/Closed-Prinzip - Open/Closed Principle (OCP)

Wie Bertrand Meyer sagt: "Software-Entitäten (Klassen, Module, Funktionen, etc.) sollten offen für Erweiterungen, aber geschlossen für Änderungen sein." Was bedeutet das aber? Dieses Prinzip besagt im Grunde, dass du den Benutzern erlauben solltest, neue Funktionalitäten hinzuzufügen, ohne den bestehenden Code zu verändern.

**Schlecht:**

```ts
class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {}

  async fetch<T>(url: string): Promise<T> {
    if (this.adapter instanceof AjaxAdapter) {
      const response = await makeAjaxCall<T>(url);
      // transform response and return
    } else if (this.adapter instanceof NodeAdapter) {
      const response = await makeHttpCall<T>(url);
      // transform response and return
    }
  }
}

function makeAjaxCall<T>(url: string): Promise<T> {
  // request and return promise
}

function makeHttpCall<T>(url: string): Promise<T> {
  // request and return promise
}
```

**Gut:**

```ts
abstract class Adapter {
  abstract async request<T>(url: string): Promise<T>;

  // code shared to subclasses ...
}

class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T> {
    // request and return promise
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T> {
    // request and return promise
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {}

  async fetch<T>(url: string): Promise<T> {
    const response = await this.adapter.request<T>(url);
    // transform response and return
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Liskov Substitutionsprinzip - Liskov Substitution Principle (LSP)

Dies ist ein beängstigender Begriff für ein sehr einfaches Konzept. Er ist formal definiert als "Wenn S ein Subtyp von T ist, dann können Objekte vom Typ T durch Objekte vom Typ S ersetzt werden (d.h. Objekte vom Typ S können Objekte vom Typ T ersetzen), ohne dass sich irgendeine der wünschenswerten Eigenschaften des Programms (Korrektheit, ausgeführte Aufgabe, etc.) ändert." Das ist eine noch gruseligere Definition.

Die beste Erklärung dafür ist, wenn du eine Elternklasse und eine Kindklasse hast, dann können die Elternklasse und die Kindklasse austauschbar verwendet werden, ohne falsche Ergebnisse zu erhalten. Das könnte immer noch verwirrend sein, also lass uns einen Blick auf das klassische Quadrat-Rechteck Beispiel werfen. Mathematisch gesehen ist ein Quadrat ein Rechteck, aber wenn du es mit der "is-a" Beziehung über Vererbung modellierst, kommst du schnell in Schwierigkeiten.

**Schlecht:**

```ts
class Rectangle {
  constructor(protected width: number = 0, protected height: number = 0) {}

  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  setWidth(width: number): this {
    this.width = width;
    return this;
  }

  setHeight(height: number): this {
    this.height = height;
    return this;
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number): this {
    this.width = width;
    this.height = width;
    return this;
  }

  setHeight(height: number): this {
    this.width = height;
    this.height = height;
    return this;
  }
}

function renderLargeRectangles(rectangles: Rectangle[]) {
  rectangles.forEach((rectangle) => {
    const area = rectangle.setWidth(4).setHeight(5).getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Gut:**

```ts
abstract class Shape {
  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  abstract getArea(): number;
}

class Rectangle extends Shape {
  constructor(private readonly width = 0, private readonly height = 0) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(private readonly length: number) {
    super();
  }

  getArea(): number {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes: Shape[]) {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ zum Anfang](#inhalt)**

### Prinzip der Schnittstellentrennung - Interface Segregation Principle (ISP)

ISP besagt, dass "Clients nicht gezwungen werden sollten, von Schnittstellen abhängig zu sein, die sie nicht nutzen". Dieses Prinzip ist sehr verwandt mit dem Single Responsibility Principle. Was es wirklich bedeutet ist, dass du deine Abstraktionen immer so gestalten solltest, dass die Clients, die die exponierten Methoden nutzen, nicht den ganzen Kuchen abbekommen. Das bedeutet auch, dass du den Clients die Last auferlegst, Methoden zu implementieren, die sie eigentlich nicht brauchen.

**Schlecht:**

```ts
interface SmartPrinter {
  print();
  fax();
  scan();
}

class AllInOnePrinter implements SmartPrinter {
  print() {
    // ...
  }

  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements SmartPrinter {
  print() {
    // ...
  }

  fax() {
    throw new Error("Fax not supported.");
  }

  scan() {
    throw new Error("Scan not supported.");
  }
}
```

**Gut:**

```ts
interface Printer {
  print();
}

interface Fax {
  fax();
}

interface Scanner {
  scan();
}

class AllInOnePrinter implements Printer, Fax, Scanner {
  print() {
    // ...
  }

  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements Printer {
  print() {
    // ...
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### Prinzip der Inversion von Abhängigkeiten - Dependency Inversion Principle (DIP)

Dieses Prinzip besagt zwei wesentliche Dinge:

1. High-Level Module sollten nicht von Low-Level Modulen abhängen. Beide sollten von Abstraktionen abhängen.
2. Abstraktionen sollten nicht von Details abhängen. Details sollten von Abstraktionen abhängen.

Das kann am Anfang schwer zu verstehen sein, aber wenn du mit Angular gearbeitet hast, hast du eine Implementierung dieses Prinzips in Form von Dependency Injection (DI) gesehen. Obwohl es sich nicht um identische Konzepte handelt, hält DIP High-Level-Module davon ab, die Details der Low-Level-Module zu kennen und diese einzurichten. Dies kann durch DI erreicht werden. Ein großer Vorteil davon ist, dass es die Kopplung zwischen den Modulen reduziert. Kopplung ist ein sehr schlechtes Entwicklungsmuster, weil es deinen Code schwer zu refaktorisieren macht.

DIP wird normalerweise durch die Verwendung eines Inversion of Control (IoC) Containers erreicht. Ein Beispiel für einen mächtigen IoC-Container für TypeScript ist [InversifyJs](https://www.npmjs.com/package/inversify).

**Schlecht:**

```ts
import { readFile as readFileCb } from "fs";
import { promisify } from "util";

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
};

class XmlFormatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}

class ReportReader {
  // BAD: We have created a dependency on a specific request implementation.
  // We should just have ReportReader depend on a parse method: `parse`
  private readonly formatter = new XmlFormatter();

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, "UTF8");
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader();
const report = await reader.read("report.xml");
```

**Gut:**

```ts
import { readFile as readFileCb } from "fs";
import { promisify } from "util";

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
};

interface Formatter {
  parse<T>(content: string): T;
}

class XmlFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}

class JsonFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts a JSON string to an object T
  }
}

class ReportReader {
  constructor(private readonly formatter: Formatter) {}

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, "UTF8");
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader(new XmlFormatter());
const report = await reader.read("report.xml");

// or if we had to read a json report
const reader = new ReportReader(new JsonFormatter());
const report = await reader.read("report.json");
```

**[⬆ zum Anfang](#inhalt)**

## Testen

Testen ist wichtiger als Ausliefern. Wenn du keine oder nur unzureichende Tests hast, kannst du nicht sicher sein, dass du nichts kaputt gemacht hast, wenn du den Code auslieferst. Die Entscheidung darüber, was eine angemessene Menge ist, liegt bei deinem Team, aber eine 100%ige Abdeckung (alle Anweisungen und Verzweigungen) ist der Weg, um ein sehr hohes Vertrauen und den Seelenfrieden der Entwickler zu erreichen. Das bedeutet, dass du zusätzlich zu einem guten Testframework auch ein gutes [Abdeckungswerkzeug](https://github.com/gotwarlost/istanbul) verwenden musst.

Es gibt keine Ausrede, keine Tests zu schreiben. Es gibt [viele gute JS-Testframeworks](http://jstherightway.org/#testing-tools) mit Typisierungsunterstützung für TypeScript, also finde eines, das dein Team bevorzugt. Wenn du eines gefunden hast, das für dein Team funktioniert, dann strebe an, für jedes neue Feature/Modul, das du einführst, immer Tests zu schreiben. Wenn deine bevorzugte Methode Test Driven Development (TDD) ist, ist das großartig, aber der Hauptpunkt ist, sicherzustellen, dass du deine Abdeckungsziele erreichst, bevor du ein Feature einführst oder ein bestehendes überarbeitest.

### Die drei Gesetze von TDD

1. Du darfst keinen produktiven Code schreiben, es sei denn, es geht darum, einen fehlgeschlagenen Unit-Test zu bestehen.
2. Du darfst nicht mehr von einem Unit-Test schreiben, als zum Scheitern ausreicht, und; Kompilierungsfehler sind Fehler.
3. Du darfst nicht mehr Produktionscode schreiben, als nötig ist, um den einen fehlgeschlagenen Unit-Test zu bestehen.

**[⬆ zum Anfang](#inhalt)**

### F.I.R.S.T. Regeln

Saubere Tests sollten den Regeln folgen:

- **Fast**: Schnelle Tests sollten schnell sein, weil wir sie häufig ausführen wollen.
- **Independent**: Unabhängige Tests sollten nicht voneinander abhängen. Sie sollten die gleiche Ausgabe liefern, egal ob sie unabhängig voneinander oder alle zusammen in beliebiger Reihenfolge ausgeführt werden.
- **Repeatable**: Wiederholbar Tests sollten in jeder Umgebung wiederholbar sein und es sollte keine Ausrede geben, warum sie fehlschlagen.
- **Self-Validating**: Selbst-validierend sollte ein Test entweder mit _Passed_ oder _Failed_ antworten. Du musst keine Logdateien vergleichen, um festzustellen, ob ein Test bestanden wurde.
- **Timely**: Zeitnahe Unit-Tests sollten vor dem Produktionscode geschrieben werden. Wenn du Tests nach dem Produktionscode schreibst, könnte es dir zu schwer fallen, Tests zu schreiben.

**[⬆ zum Anfang](#inhalt)**

### Einzelnes Konzept pro Test

Tests sollten auch dem _Single Responsibility Principle_ folgen. Mache nur eine Assert pro Unit Test.

**Schlecht:**

```ts
import { assert } from "chai";

describe("AwesomeDate", () => {
  it("handles date boundaries", () => {
    let date: AwesomeDate;

    date = new AwesomeDate("1/1/2015");
    assert.equal("1/31/2015", date.addDays(30));

    date = new AwesomeDate("2/1/2016");
    assert.equal("2/29/2016", date.addDays(28));

    date = new AwesomeDate("2/1/2015");
    assert.equal("3/1/2015", date.addDays(28));
  });
});
```

**Gut:**

```ts
import { assert } from "chai";

describe("AwesomeDate", () => {
  it("handles 30-day months", () => {
    const date = new AwesomeDate("1/1/2015");
    assert.equal("1/31/2015", date.addDays(30));
  });

  it("handles leap year", () => {
    const date = new AwesomeDate("2/1/2016");
    assert.equal("2/29/2016", date.addDays(28));
  });

  it("handles non-leap year", () => {
    const date = new AwesomeDate("2/1/2015");
    assert.equal("3/1/2015", date.addDays(28));
  });
});
```

**[⬆ zum Anfang](#inhalt)**

### Der Name des Tests sollte seine Absicht verraten

Wenn ein Test fehlschlägt, ist sein Name der erste Hinweis darauf, was falsch gelaufen sein könnte.

**Schlecht:**

```ts
describe("Calendar", () => {
  it("2/29/2020", () => {
    // ...
  });

  it("throws", () => {
    // ...
  });
});
```

**Gut:**

```ts
describe("Calendar", () => {
  it("should handle leap year", () => {
    // ...
  });

  it("should throw when format is invalid", () => {
    // ...
  });
});
```

**[⬆ zum Anfang](#inhalt)**

## Asynchronität

### Bevorzuge `promises` vor `callbacks`

Callbacks sind nicht sauber und verursachen exzessive Mengen an Verschachtlungen _(die Callback-Hölle)_.
Es gibt Hilfsprogramme, die bestehende Funktionen im Callback-Stil in eine Version umwandeln, die Versprechen zurückgibt (für Node.js siehe [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original), für allgemeine Zwecke siehe [pify](https://www.npmjs.com/package/pify), [es6-promisify](https://www.npmjs.com/package/es6-promisify)).

**Schlecht:**

```ts
import { get } from "request";
import { writeFile } from "fs";

function downloadPage(
  url: string,
  saveTo: string,
  callback: (error: Error, content?: string) => void
) {
  get(url, (error, response) => {
    if (error) {
      callback(error);
    } else {
      writeFile(saveTo, response.body, (error) => {
        if (error) {
          callback(error);
        } else {
          callback(null, response.body);
        }
      });
    }
  });
}

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html",
  (error, content) => {
    if (error) {
      console.error(error);
    } else {
      console.log(content);
    }
  }
);
```

**Gut:**

```ts
import { get } from "request";
import { writeFile } from "fs";
import { promisify } from "util";

const write = promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url).then((response) => write(saveTo, response));
}

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html"
)
  .then((content) => console.log(content))
  .catch((error) => console.error(error));
```

Promises unterstützen ein paar Hilfsmethoden, die helfen, den Code übersichtlicher zu gestalten:

| Muster                   | Beschreibung                                                                                                                                                                                         |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Promise.resolve(value)` | Konvertiert einen Wert in ein aufgelöstes versprechen.                                                                                                                                               |
| `Promise.reject(error)`  | Konvertiert einen Fehler in ein abgelehntes Versprechen.                                                                                                                                             |
| `Promise.all(promises)`  | Gibt ein neues Versprechen zurück, das mit einem Array von Erfüllungswerten für die übergebenen Versprechen erfüllt wird oder mit dem Grund des ersten abgelehnten Versprechens zurückgewiesen wird. |
| `Promise.race(promises)` | Gibt ein neues Versprechen zurück, das mit dem Ergebnis/Fehler des ersten erledigten Versprechens aus dem Array der übergebenen Versprechen erfüllt/abgelehnt wird.                                  |

`Promise.all` ist besonders nützlich, wenn es notwendig ist, Aufgaben parallel laufen zu lassen. `Promise.race` macht es einfacher, Dinge wie Timeouts für Promises zu implementieren.

**[⬆ zum Anfang](#inhalt)**

### Async/Await sind noch sauberer als Promises

Mit der `async`/`await` Syntax kannst du Code schreiben, der viel sauberer und verständlicher ist als verkettete Versprechen. Innerhalb einer Funktion, der das Schlüsselwort `async` vorangestellt ist, hast du die Möglichkeit, der JavaScript-Laufzeit zu sagen, dass sie die Ausführung des Codes auf das Schlüsselwort `await` (wenn es auf ein Versprechen angewendet wird) pausieren soll.

**Schlecht:**

```ts
import { get } from "request";
import { writeFile } from "fs";
import { promisify } from "util";

const write = util.promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url).then((response) => write(saveTo, response));
}

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html"
)
  .then((content) => console.log(content))
  .catch((error) => console.error(error));
```

**Gut:**

```ts
import { get } from "request";
import { writeFile } from "fs";
import { promisify } from "util";

const write = promisify(writeFile);

async function downloadPage(url: string): Promise<string> {
  const response = await get(url);
  return response;
}

// somewhere in an async function
try {
  const content = await downloadPage(
    "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
  );
  await write("article.html", content);
  console.log(content);
} catch (error) {
  console.error(error);
}
```

**[⬆ zum Anfang](#inhalt)**

## Fehlerbehandlung

Ausgelöste Fehler sind eine gute Sache! Sie bedeuten, dass die Laufzeitumgebung erfolgreich erkannt hat, dass etwas in deinem Programm schief gelaufen ist und es dich wissen lässt, indem sie die Ausführung der Funktion auf dem aktuellen Stack stoppt, den Prozess (in Node) beendet und dich in der Konsole mit einem Stacktrace benachrichtigt.

### Werfe immer Fehler

Sowohl JavaScript als auch TypeScript erlauben es dir, ein beliebiges Objekt zu werfen. Ein Promise kann auch mit einem beliebigen Grundobjekt verworfen werden.
Es ist ratsam, die `throw` Syntax mit einem `Error` Typ zu verwenden. Das liegt daran, dass dein Fehler in höherem Code mit einer `catch` Syntax abgefangen werden könnte. Es wäre sehr verwirrend, dort eine String-Meldung zu fangen und würde das [Debugging schmerzhafter machen](https://basarat.gitbook.io/typescript/type-system/exceptions#always-use-error).
Aus dem gleichen Grund solltest du Promises mit `Error` Typen ablehnen.

**Schlecht:**

```ts
function calculateTotal(items: Item[]): number {
  throw "Not implemented.";
}

function get(): Promise<Item[]> {
  return Promise.reject("Not implemented.");
}
```

**Gut:**

```ts
function calculateTotal(items: Item[]): number {
  throw new Error("Not implemented.");
}

function get(): Promise<Item[]> {
  return Promise.reject(new Error("Not implemented."));
}

// or equivalent to:

async function get(): Promise<Item[]> {
  throw new Error("Not implemented.");
}
```

Der Vorteil der Verwendung von `Error` Typen ist, dass sie von der Syntax `try/catch/finally` unterstützt werden und implizit alle Fehler die Eigenschaft `stack` haben, was
sehr mächtig für das Debugging ist.
Es gibt auch andere Alternativen, die `throw`-Syntax nicht zu verwenden und stattdessen immer eigene Fehlerobjekte zurückzugeben. TypeScript macht dies noch einfacher. Betrachte das folgende Beispiel:

```ts
type Result<R> = { isError: false; value: R };
type Failure<E> = { isError: true; error: E };
type Failable<R, E> = Result<R> | Failure<E>;

function calculateTotal(items: Item[]): Failable<number, "empty"> {
  if (items.length === 0) {
    return { isError: true, error: "empty" };
  }

  // ...
  return { isError: false, value: 42 };
}
```

Eine detaillierte Erklärung dieser Idee findest du im [Originalbeitrag](https://medium.com/@dhruvrajvanshi/making-exceptions-type-safe-in-typescript-c4d200ee78e9).

**[⬆ zum Anfang](#inhalt)**

### Ignoriere gefangene Fehler nicht

Wenn du nichts mit einem gefangenen Fehler machst, hast du nicht die Möglichkeit, den Fehler zu beheben oder darauf zu reagieren. Den Fehler auf der Konsole zu protokollieren (`console.log`) ist nicht viel besser, da er oft in einem Meer von Dingen, die auf der Konsole ausgegeben werden, verloren gehen kann. Wenn du ein Stück Code in ein `try/catch` verpackst, bedeutet das, dass du denkst, dass dort ein Fehler auftreten könnte und deshalb solltest du einen Plan haben, oder einen Codepfad erstellen, für den Fall, dass er auftritt.

**Schlecht:**

```ts
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}

// or even worse

try {
  functionThatMightThrow();
} catch (error) {
  // ignore error
}
```

**Gut:**

```ts
import { logger } from "./logging";

try {
  functionThatMightThrow();
} catch (error) {
  logger.log(error);
}
```

**[⬆ zum Anfang](#inhalt)**

### Ignoriere abgelehnte Versprechen nicht

Aus dem gleichen Grund solltest du gefangene Fehler von `try/catch` nicht ignorieren.

**Schlecht:**

```ts
getUser()
  .then((user: User) => {
    return sendEmail(user.email, "Welcome!");
  })
  .catch((error) => {
    console.log(error);
  });
```

**Gut:**

```ts
import { logger } from "./logging";

getUser()
  .then((user: User) => {
    return sendEmail(user.email, "Welcome!");
  })
  .catch((error) => {
    logger.log(error);
  });

// or using the async/await syntax:

try {
  const user = await getUser();
  await sendEmail(user.email, "Welcome!");
} catch (error) {
  logger.log(error);
}
```

**[⬆ zum Anfang](#inhalt)**

## Formatierung

Formatierung ist subjektiv. Wie bei vielen Regeln hier gibt es keine feste Regel, die du befolgen musst. Der wichtigste Punkt ist, dass du dich NICHT über die Formatierung streiten solltest. Es gibt tonnenweise Tools, um dies zu automatisieren. Benutze eins! Es ist eine Verschwendung von Zeit und Geld für Ingenieure, sich über die Formatierung zu streiten. Die allgemeine Regel, die man befolgen sollte, ist _konsistente Formatierungsregeln_.

Für TypeScript gibt es ein mächtiges Werkzeug namens [ESLint](https://typescript-eslint.io/). Es ist ein statisches Analysetool, das dir helfen kann, die Lesbarkeit und Wartbarkeit deines Codes dramatisch zu verbessern. Es gibt fertige ESLint-Konfigurationen, die du in deinen Projekten referenzieren kannst:

- [ESLint Config Airbnb](https://www.npmjs.com/package/eslint-config-airbnb-typescript) - Airbnb Style Guide
- [ESLint Base Style Config](https://www.npmjs.com/package/eslint-plugin-base-style-config) - ein Set von essentiellen ESLint Regeln für JS, TS und React
- [ESLint + Prettier](https://www.npmjs.com/package/eslint-config-prettier) - Lint-Regeln für [Prettier](https://github.com/prettier/prettier) Code-Formatierer

Siehe auch diese großartige [TypeScript StyleGuide and Coding Conventions](https://basarat.gitbook.io/typescript/styleguide) Quelle.

### Migrieren von TSLint zu ESLint

Wenn du nach Hilfe bei der Migration von TSLint zu ESLint suchst, kannst du dir dieses Projekt ansehen: <https://github.com/typescript-eslint/tslint-to-eslint-config>

### Verwende konsistente Großschreibung

Die Großschreibung verrät dir viel über deine Variablen, Funktionen, etc. Diese Regeln sind subjektiv, also kann dein Team wählen, was immer sie wollen. Der Punkt ist, egal was ihr alle wählt, seid einfach _konsequent_.

**Schlecht:**

```ts
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

const discography = getArtistDiscography("ACDC");
const beatlesSongs = SONGS.filter((song) => isBeatlesSong(song));

function eraseDatabase() {}
function restore_database() {}

type animal = {
  /* ... */
};
type Container = {
  /* ... */
};
```

**Gut:**

```ts
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

type Animal = {
  /* ... */
};
type Container = {
  /* ... */
};
```

Verwende bevorzugt `PascalCase` für Klassen-, Interface-, Typ- und Namensraumnamen.
Verwende bevorzugt `camelCase` für Variablen, Funktionen und Klassenmitglieder.
Verwende bevorzugt `SNAKE_CASE` für Konstanten.

**[⬆ zum Anfang](#inhalt)**

### Funktionsaufrufer und -aufrufende sollten sich nahe sein

Wenn eine Funktion eine andere aufruft, halte diese Funktionen vertikal dicht in der Quelldatei. Idealerweise solltest du den Aufrufer direkt über dem Aufrufer halten. Wir neigen dazu, Code von oben nach unten zu lesen, wie eine Zeitung. Deshalb solltest du dafür sorgen, dass sich dein Code auch so liest.

**Schlecht:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {}

  private lookupPeers() {
    return db.lookup(this.employee.id, "peers");
  }

  private lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**Gut:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {}

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, "peers");
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**[⬆ zum Anfang](#inhalt)**

### Organisieren Importe

Mit sauberen und einfach zu lesenden Import-Anweisungen kannst du schnell die Abhängigkeiten des aktuellen Codes sehen. Achte darauf, dass du die folgenden guten Praktiken für `import`-Anweisungen anwendest:

- Importanweisungen sollten alphabetisch geordnet und gruppiert werden.
- Unbenutzte Importe sollten entfernt werden.
- Benannte Importe müssen alphabetisch geordnet sein (z.B. `import {A, B, C} from 'foo';`)
- Importquellen müssen innerhalb von Gruppen alphabetisch geordnet werden, z.B.: `import * as foo from 'a'; import * as bar from 'b';`
- Bevorzuge die Verwendung von `import type` anstelle von `import`, wenn du nur Typen aus einer Datei importierst, um Abhängigkeitszyklen zu vermeiden, da diese Importe zur Laufzeit gelöscht werden
- Gruppen von Importen werden durch Leerzeilen abgegrenzt.
- Gruppen müssen folgende Reihenfolge einhalten:
  - Polyfills (d.h. `import 'reflect-metadata';`)
  - Node builtin Module (z.B. `import fs from 'fs';`)
  - externe Module (z.B. `import { query } from 'itiriri';`)
  - interne Module (z.B. `import { UserService } from 'src/services/userService';`)
  - Module aus einem übergeordneten Verzeichnis (z.B. `import foo from '../foo'; import qux from '../../foo/qux';`)
  - Module aus dem gleichen oder einem Geschwisterverzeichnis (z.B. `import bar from './bar'; import baz from './bar/baz';`)

**Schlecht:**

```ts
import { TypeDefinition } from "../types/typeDefinition";
import { AttributeTypes } from "../model/attribute";
import { Customer, Credentials } from "../model/types";
import { ApiCredentials, Adapters } from "./common/api/authorization";
import fs from "fs";
import { ConfigPlugin } from "./plugins/config/configPlugin";
import { BindingScopeEnum, Container } from "inversify";
import "reflect-metadata";
```

**Gut:**

```ts
import "reflect-metadata";

import fs from "fs";
import { BindingScopeEnum, Container } from "inversify";

import type { AttributeTypes } from "../model/attribute";
import type { Customer, Credentials } from "../model/types";
import type { TypeDefinition } from "../types/typeDefinition";

import { ApiCredentials, Adapters } from "./common/api/authorization";
import { ConfigPlugin } from "./plugins/config/configPlugin";
```

**[⬆ zum Anfang](#inhalt)**

### Typescript-Aliase verwenden

Erstelle hübschere Importe, indem du die Pfade und baseUrl Eigenschaften im CompilerOptions Abschnitt in der `tsconfig.json` definierst.

Dadurch werden lange relative Pfade beim Importieren vermieden.

**Schlecht:**

```ts
import { UserService } from "../../../services/UserService";
```

**Gut:**

```ts
import { UserService } from "@services/UserService";
```

```js
// tsconfig.json
...
  "compilerOptions": {
    ...
    "baseUrl": "src",
    "paths": {
      "@services": ["services/*"]
    }
    ...
  }
...
```

**[⬆ zum Anfang](#inhalt)**

## Kommentare

Die Verwendung eines Kommentars ist ein Hinweis darauf, dass man sich ohne ihn nicht ausdrücken kann. Der Code sollte die einzige Quelle der Wahrheit sein.

> Don’t comment bad code—rewrite it.
> — _Brian W. Kernighan and P. J. Plaugher_

### Bevorzuge selbsterklärenden Code anstelle von Kommentaren

Kommentare sind eine Entschuldigung, keine Voraussetzung. Guter Code dokumentiert sich _meistens_ selbst.

**Schlecht:**

```ts
// Check if subscription is active.
if (subscription.endDate > Date.now) {
}
```

**Gut:**

```ts
const isSubscriptionActive = subscription.endDate > Date.now;
if (isSubscriptionActive) {
  /* ... */
}
```

**[⬆ zum Anfang](#inhalt)**

### Lass keinen auskommentierten Code in deiner Codebase

Versionskontrolle existiert aus einem bestimmten Grund. Lass alten Code in deiner Historie.

**Schlecht:**

```ts
type User = {
  name: string;
  email: string;
  // age: number;
  // jobPosition: string;
};
```

**Gut:**

```ts
type User = {
  name: string;
  email: string;
};
```

**[⬆ zum Anfang](#inhalt)**

### Habe keine Journal-Kommentare

Denke daran, Versionskontrolle zu benutzen! Es gibt keinen Grund für toten Code, kommentierten Code und besonders Journal-Kommentare. Benutze `git log` um die Historie zu erhalten!

**Schlecht:**

```ts
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Added type-checking (LI)
 * 2015-03-14: Implemented combine (JR)
 */
function combine(a: number, b: number): number {
  return a + b;
}
```

**Gut:**

```ts
function combine(a: number, b: number): number {
  return a + b;
}
```

**[⬆ zum Anfang](#inhalt)**

### Vermeide Positionsmarkierungen

Sie fügen normalerweise nur Lärm hinzu. Lass die Funktionen und Variablennamen zusammen mit der richtigen Einrückung und Formatierung deinem Code die visuelle Struktur geben.
Die meisten IDEs unterstützen Code-Folding-Features, die es dir ermöglichen, Codeblöcke zu komprimieren/expandieren (siehe Visual Studio Code [folding regions](https://code.visualstudio.com/updates/v1_17#_folding-regions)).

**Schlecht:**

```ts
////////////////////////////////////////////////////////////////////////////////
// Client class
////////////////////////////////////////////////////////////////////////////////
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  ////////////////////////////////////////////////////////////////////////////////
  // public methods
  ////////////////////////////////////////////////////////////////////////////////
  public describe(): string {
    // ...
  }

  ////////////////////////////////////////////////////////////////////////////////
  // private methods
  ////////////////////////////////////////////////////////////////////////////////
  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
}
```

**Gut:**

```ts
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  public describe(): string {
    // ...
  }

  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
}
```

**[⬆ zum Anfang](#inhalt)**

### TODO Kommentare

Wenn du feststellst, dass du Notizen im Code für spätere Verbesserungen hinterlassen musst, mache das mit `// TODO` Kommentaren. Die meisten IDEs haben spezielle Unterstützung für diese Art von Kommentaren, so dass du schnell die gesamte Liste der Todos durchgehen kannst.

Behalte jedoch im Hinterkopf, dass ein _TODO_ Kommentar keine Entschuldigung für schlechten Code ist.

**Schlecht:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**Gut:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // TODO: ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**[⬆ zum Anfang](#inhalt)**

## Übersetzungen

Diese Prinzipien sind auch in anderen Sprachen verfügbar:

- ![us](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/United-States.png) Das Original in **Englisch**: [labs42io/clean-code-typescript](https://github.com/labs42io/clean-code-typescript)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brasilianisches Portugiesisch**: [vitorfreitas/clean-code-typescript](https://github.com/vitorfreitas/clean-code-typescript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinesisch**:
  - [beginor/clean-code-typescript](https://github.com/beginor/clean-code-typescript)
  - [pipiliang/clean-code-typescript](https://github.com/pipiliang/clean-code-typescript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **Französisch**: [ralflorent/clean-code-typescript](https://github.com/ralflorent/clean-code-typescript)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **Deutsch**: [mheob/clean-code-typescript](https://github.com/mheob/clean-code-typescript)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italienisch**: [Kornil/clean-code-typescript](https://github.com/kornil/clean-code-typescript)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanisch**: [MSakamaki/clean-code-typescript](https://github.com/MSakamaki/clean-code-typescript)
- ![ko](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Koreanisch**: [738/clean-code-typescript](https://github.com/738/clean-code-typescript)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russisch**: [Real001/clean-code-typescript](https://github.com/Real001/clean-code-typescript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanisch**: [3xp1o1t/clean-code-typescript](https://github.com/3xp1o1t/clean-code-typescript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Türkisch**: [ozanhonamlioglu/clean-code-typescript](https://github.com/ozanhonamlioglu/clean-code-typescript)
- ![uk](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainisch**: [KirillPd/clean-code-typescript](https://github.com/KirillPd/clean-code-typescript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamesisch**: [hoangsetup/clean-code-typescript](https://github.com/hoangsetup/clean-code-typescript)

Referenzen werden hinzugefügt, sobald die Übersetzungen abgeschlossen sind.
Schau dir diese [Diskussion](https://github.com/labs42io/clean-code-typescript/issues/15) für weitere Details und Fortschritte an. Du kannst einen unverzichtbaren Beitrag zur _Clean Code_ Community leisten, indem du dies in deine Sprache übersetzt.

**[⬆ zum Anfang](#inhalt)**
