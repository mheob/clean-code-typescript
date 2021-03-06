# clean-code-typescript [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Clean%20Code%20Typescript&url=https://github.com/labs42io/clean-code-typescript)

## German translation / Deutsche Übersetzung

Clean-Code-Konzepte angepasst für TypeScript.  
Inspiriert von [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

## Inhaltsverzeichnis

1. [Einführung](#einführung)
2. [Variablen](#variablen)
3. [Funktionen](#funktionen)
4. [Objekte und Datenstrukturen](#objekte-und-datenstrukturen)
5. [Klassen](#klassen)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Parallelität](#parallelität)
9. [Fehlerbehandlung](#fehlerbehandlung)
10. [Formatierung](#formatierung)
11. [Kommentare](#kommentare)
12. [Übersetzungen](#übersetzungen)

## Einführung

![Humorvolles Bild der Software-Qualitätseinschätzung als Zählung, wie viele Schimpfwörter du beim Lesen von Code schreist](https://www.osnews.com/images/comics/wtfm.jpg)

Software-Engineering-Prinzipien, aus dem Handbuch von Robert C. Martins [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), angepasst für TypeScript. Dies ist kein Style Guide. Es ist ein Leitfaden für die Erstellung von [lesbarer, wiederverwendbare und überarbeitbarer](https://github.com/ryanmcdermott/3rs-of-software-architecture) Software in TypeScript.

Nicht jedes der folgenden Prinzipien muss strikt befolgt werden, und noch weniger werden sie allgemein anerkannt sein. Dies sind Richtlinien und nichts weiter. Aber sie sind solche, die über viele Jahre der kollektiven Erfahrung der Autoren von _Clean Code_ kodifiziert wurden.

Unser Handwerk des Software-Engineerings ist gerade mal etwas über 50 Jahre alt und wir lernen immer noch eine Menge. Wenn Softwarearchitektur so alt ist wie die Architektur selbst, dann werden wir vielleicht härtere Regeln haben, denen wir folgen können. Für den Moment sollen diese Richtlinien als Prüfstein dienen, um die Qualität des TypeScript-Codes zu beurteilen, den du und dein Team produzieren.

Und noch etwas: Wenn du diese Richtlinien kennst, wirst du nicht sofort ein besserer Softwareentwickler. Und wenn du jahrelang mit ihnen arbeitest, bedeutet das nicht, dass du keine Fehler machen wirst. Jedes Stück Code beginnt als erster Entwurf; wie nasser Ton, der in seine endgültige Form gebracht wird. Schließlich meißeln wir die Unvollkommenheiten weg, wenn wir es mit unseren Kollegen überprüfen. Mach dich nicht selbst fertig für erste Entwürfe, die verbessert werden müssen. Verprügle stattdessen den Code!

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Variablen

### Sinnvolle Variablennamen verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Aussprechbare Variablennamen verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Gleiches Vokabular für den gleichen Typ von Variablen verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Durchsuchbare Namen verwenden

Wir werden mehr Code lesen als wir jemals schreiben werden. Es ist wichtig, dass der Code, den wir schreiben, lesbar und durchsuchbar sein muss. Indem wir Variablen, die am Ende für das Verständnis unseres Programms von Bedeutung sind, _nicht_ vernünftig benennen, schaden wir unseren Lesern. Mache deine Namen durchsuchbar. Tools wie [ESLint](https://eslint.org/) können helfen, unbenannte Konstanten zu identifizieren.

**Schlecht:**

```ts
// What the heck is 86400000 for?
setTimeout(restart, 86400000);
```

**Gut:**

```ts
// Declare them as capitalized named constants.
const MILLISECONDS_IN_A_DAY = 24 * 60 * 60 * 1000;
setTimeout(restart, MILLISECONDS_IN_A_DAY);
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Erklärende Variablen verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Mental Mapping vermeiden

Explizit ist besser als implizit.\
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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Keinen überflüssigen Kontext hinzufügen

Wenn dir dein Klassen-/Typ-/Objektname etwas sagt, dann wiederhole das nicht in deinem Variablennamen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Standardargumente anstelle von Abfragen verwenden

Standardargumente sind oft sauberer als Abfragen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Enum verwenden, um Absicht zu dokumentieren

Enums können dir helfen, die Absicht des Codes zu dokumentieren. Zum Beispiel, wenn es darum geht, dass die Werte unterschiedlich sind, anstatt den genauen Wert von denen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Funktionen

### Funktionsargumente (idealerweise 2 oder weniger)

Die Begrenzung der Anzahl von Funktionsparametern ist unglaublich wichtig, weil es das Testen deiner Funktion einfacher macht. Mehr als drei Parameter zu haben, führt zu einer kombinatorischen Explosion, bei der du tonnenweise verschiedene Fälle mit jedem einzelnen Argument testen musst.

Ein oder zwei Argumente sind der Idealfall, drei sollten nach Möglichkeit vermieden werden. Alles, was darüber hinausgeht, sollte konsolidiert werden. Wenn du mehr als zwei Argumente hast, dann versucht deine Funktion normalerweise, zu viel zu machen. In den Fällen, in denen das nicht der Fall ist, reicht meistens ein übergeordnetes Objekt als Argument aus.

Erwäge die Verwendung von Objektliteralen, wenn du viele Argumente benötigst.

Um deutlich zu machen, welche Eigenschaften die Funktion erwartet, kannst du [syntax destructuring](https://basarat.gitbook.io/typescript/future-javascript/destructuring) verwenden.\
Dies hat ein paar Vorteile:

1. Wenn sich jemand die Funktionssignatur ansieht, ist sofort klar, welche Eigenschaften verwendet werden.
2. Sie kann verwendet werden, um benannte Parameter zu simulieren.
3. Die `destructuring` klont auch die spezifizierten primitiven Werte des Argumentobjekts, das an die Funktion übergeben wird. Dies kann helfen, Seiteneffekte zu vermeiden. Hinweis: Objekte und Arrays, die vom Argument-Objekt `destructuring` werden, werden NICHT geklont.
4. TypeScript warnt dich vor unbenutzten Eigenschaften, was ohne `destructuring` unmöglich wäre.

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

Du kannst die Lesbarkeit weiter verbessern, indem du [Typ-Aliase](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases) verwendest:

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Funktionen sollten nur eine Sache machen

Dies ist bei weitem die wichtigste Regel in der Softwareentwicklung. Wenn Funktionen mehr als eine Sache machen, sind sie schwieriger zu verwalten, zu testen und zu verstehen. Wenn du eine Funktion auf nur eine Aktion isolieren kannst, kann sie leicht überarbeitet werden und dein Code wird sich viel sauberer lesen. Wenn du nichts anderes aus diesem Leitfaden mitnimmst als das, wirst du vielen Entwicklern voraus sein.

**Schlecht:**

```ts
function emailClients(clients: Client[]) {
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
function emailClients(clients: Client[]) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client: Client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Duplizierten Code entfernen

Gib dein Bestes, um doppelten Code zu vermeiden. Doppelter Code ist schlecht, weil er bedeutet, dass es mehr als eine Stelle gibt, an der du etwas ändern kannst, wenn du eine Logik ändern musst.

Stell dir vor, du betreibst ein Restaurant und behältst den Überblick über dein Inventar: all deine Tomaten, Zwiebeln, Knoblauch, Gewürze, etc. Wenn du mehrere Listen hast, auf denen du dies festhältst, dann müssen alle aktualisiert werden, wenn du ein Gericht mit Tomaten darin servierst. Wenn du nur eine Liste hast, gibt es nur einen Ort zum Aktualisieren!

Oftmals hast du doppelten Code, weil du zwei oder mehr leicht unterschiedliche Dinge hast, die viel gemeinsam haben, aber ihre Unterschiede zwingen dich dazu, zwei oder mehr separate Funktionen zu haben, die viel vom Gleichen machen. Doppelten Code zu entfernen bedeutet, eine Abstraktion zu schaffen, die diese verschiedenen Dinge mit nur einer Funktion/Modul/Klasse behandeln kann.

Die Abstraktion richtig hinzubekommen ist entscheidend, deshalb solltest du den [SOLID](#solid) Prinzipien folgen. Schlechte Abstraktionen können schlimmer sein als doppelter Code, also sei vorsichtig! Wenn du also eine gute Abstraktion machen kannst, dann tu es! Wiederhole dich nicht, sonst wirst du dich dabei ertappen, dass du jedes Mal, wenn du eine Sache ändern willst, mehrere Stellen aktualisieren musst.

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

function showEmployeeList(employee: Developer | Manager) {
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

Du solltest kritisch gegenüber Code-Duplizierung sein. Manchmal gibt es einen Kompromiss zwischen doppeltem Code und erhöhter Komplexität durch die Einführung unnötiger Abstraktion. Wenn zwei Implementierungen aus zwei verschiedenen Modulen ähnlich aussehen, aber in verschiedenen Domänen leben, kann die Duplizierung akzeptabel sein und dem Extrahieren des gemeinsamen Codes vorgezogen werden. Der extrahierte gemeinsame Code führt in diesem Fall eine indirekte Abhängigkeit zwischen den beiden Modulen ein.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Standardobjekte mit `Object.assign` oder `destructuring` setzen

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

Alternativ kannst du das `destructuring` mit Standardwerten verwenden:

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

Um Seiteneffekte und unerwartetes Verhalten durch die explizite Übergabe eines `undefined` oder `null` zu vermeiden, kannst du dem TypeScript-Compiler sagen, dass er dies nicht zulassen soll. Siehe die [`--strictNullChecks`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#--strictnullchecks) Option in TypeScript.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Keine `Flags` als Funktionsparameter verwenden

`Flags` sagen deinem Benutzer, dass diese Funktion mehr als eine Sache macht. Funktionen sollten nur eine Sache machen. Teile deine Funktionen auf, wenn sie verschiedene Codepfade basierend auf einem Boolean verfolgen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Nebeneffekte vermeiden (Teil 1)

Eine Funktion erzeugt einen Seiteneffekt, wenn sie etwas anderes macht, als einen Wert aufzunehmen und einen anderen Wert oder andere Werte zurückzugeben. Ein Nebeneffekt könnte das Schreiben in eine Datei, das Ändern einer globalen Variable oder das versehentliche Überweisen deines gesamten Geldes an einen Fremden sein.

Nun, du brauchst gelegentlich Nebeneffekte in einem Programm. Wie im vorherigen Beispiel, musst du vielleicht in eine Datei schreiben. Was du machen willst, ist, den Ort, an dem du dies tust, zu zentralisieren. Du solltest nicht mehrere Funktionen und Klassen haben, die in eine bestimmte Datei schreiben. Habe einen Dienst, der das macht. Einen und nur einen.

Der wichtigste Punkt ist, die üblichen Fallstricke zu vermeiden, wie das Teilen von Zuständen zwischen Objekten ohne jegliche Struktur, die Verwendung von veränderbaren Datentypen, die von jedem geschrieben werden können, und die fehlende Zentralisierung der Nebeneffekte. Wenn du das schaffst, wirst du glücklicher sein als die große Mehrheit der anderen Programmierer.

**Schlecht:**

```ts
// Global variable referenced by following function.
let name = "Robert C. Martin";

function toBase64() {
  name = btoa(name);
}

toBase64();
// If we had another function that used this name, now it would be a Base64 value

console.log(name); // expected to print 'Robert C. Martin' but instead 'Um9iZXJ0IEMuIE1hcnRPbg=='
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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Nebeneffekte vermeiden (Teil 2)

In JavaScript werden Primitive als Wert und Objekte/Arrays als Referenz übergeben. Im Fall von Objekten und Arrays, wenn deine Funktion eine Änderung in einem Warenkorb-Array vornimmt, z.B. durch Hinzufügen eines Artikels zum Kauf, dann wird jede andere Funktion, die dieses Warenkorb-Array verwendet, vom Hinzufügen betroffen sein. Das kann großartig sein, es kann aber auch schlecht sein. Stellen wir uns eine schlechte Situation vor:

Der Nutzer klickt auf den Button "Kaufen", der eine Kauffunktion aufruft, die eine Netzwerkanfrage erzeugt und das Warenkorb-Array an den Server sendet. Aufgrund einer schlechten Netzwerkverbindung muss die Kauffunktion die Anfrage immer wieder neu versuchen. Was ist, wenn der Nutzer in der Zwischenzeit versehentlich auf den "In den Warenkorb"-Button eines Artikels klickt, den er eigentlich nicht haben will, bevor die Netzwerkanfrage beginnt? Wenn das passiert und die Netzwerkanforderung beginnt, dann wird die Kauffunktion den versehentlich hinzugefügten Artikel senden, weil sie eine Referenz auf ein Warenkorb-Array hat, das die `addItemToCart` Funktion durch das Hinzufügen eines unerwünschten Artikels verändert hat.

Eine großartige Lösung wäre es, wenn die `addItemToCart` Funktion den Warenkorb immer klont, ihn bearbeitet und den Klon zurückgibt. Dies stellt sicher, dass keine anderen Funktionen, die eine Referenz auf den Warenkorb halten, von Änderungen betroffen sind.

Zwei Vorbehalte sind bei diesem Ansatz zu erwähnen:

1. Es mag Fälle geben, in denen du das Eingabeobjekt tatsächlich verändern willst. Aber wenn du diese Programmierpraxis anwendest, wirst du feststellen, dass diese Fälle ziemlich selten sind. Die meisten Dinge können so überarbeitet werden, dass sie keine Nebeneffekte haben! (siehe [Pure Funktion](https://wiki.selfhtml.org/wiki/Pure_Funktion))
2. Das Klonen von großen Objekten kann sehr teuer in Bezug auf die Performance sein. Glücklicherweise ist das in der Praxis kein großes Problem, da es großartige Bibliotheken gibt, die diese Art von Programmieransatz schnell und nicht so speicherintensiv machen, wie es für dich wäre, wenn du Objekte und Arrays manuell klonen würdest.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Nicht in globale Funktionen schreiben

Globals zu verschmutzen ist eine schlechte Praxis in JavaScript, da du mit einer anderen Bibliothek kollidieren könntest und der Benutzer deiner API nichts davon mitbekommt, bis er in der Produktion einen Fehler bekommt. Lass uns über ein Beispiel nachdenken: Was wäre, wenn du JavaScript's native Array-Methode um eine `diff`-Methode erweitern wolltest, die den Unterschied zwischen zwei Arrays anzeigen könnte? Du könntest deine neue Funktion in den `Array.prototype` schreiben, aber sie könnte mit einer anderen Bibliothek kollidieren, die versucht, das Gleiche zu tun. Was wäre, wenn diese andere Bibliothek `diff` nur benutzt, um den Unterschied zwischen dem ersten und letzten Element eines Arrays zu finden? Deshalb wäre es viel besser, nur Klassen zu verwenden und einfach das Array global zu erweitern.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Abfragen kapseln

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Negative Abfragen vermeiden

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

if (!isEmailUsed(node)) {
  // ...
}
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Abfragen vermeiden

Das scheint eine unmögliche Aufgabe zu sein. Wenn man das zum ersten Mal hört, sagen die meisten Leute: "Wie soll ich etwas ohne eine `if`-Anweisung machen?" Die Antwort ist, dass du Polymorphismus verwenden kannst, um die gleiche Aufgabe in vielen Fällen zu erreichen. Die zweite Frage ist in der Regel: "Nun, das ist toll, aber warum sollte ich das tun wollen?" Die Antwort ist ein früheres Clean-Code-Konzept, das wir gelernt haben: Eine Funktion sollte nur eine Sache tun. Wenn du Klassen und Funktionen hast, die `if`-Anweisungen haben, sagst du deinem Benutzer, dass deine Funktion mehr als eine Sache macht. Denke daran, nur eine Sache zu machen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Typenprüfung vermeiden

TypeScript ist eine syntaktisch, strenge Obermenge von JavaScript und erweitert die Sprache um eine optionale statische Typüberprüfung. Bevorzuge es immer, die Typen von Variablen, Parametern und Rückgabewerten zu spezifizieren, um die volle Leistung der TypeScript Features zu nutzen. Es macht das Überarbeiten einfacher.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Nicht über-optimieren

Moderne Browser führen zur Laufzeit eine Menge Optimierungen unter der Haube durch. Wenn du optimierst, verschwendest du in vielen Fällen nur deine Zeit. Es gibt gute [Ressourcen](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers), um zu sehen, wo die Optimierung fehlt. Nimm diese in der Zwischenzeit ins Visier, bis sie behoben sind, falls dies möglich ist.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Toten Code entfernen

Toter Code ist genau so schlimm wie doppelter Code. Es gibt keinen Grund, ihn in deinem Quellcode zu behalten. Wenn er nicht aufgerufen wird, entferne ihn! Er wird immer noch sicher in deiner Versionsgeschichte sein, wenn du ihn noch brauchst.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Iteratoren und Generatoren verwenden

Verwende Generatoren und Iterables, wenn du mit Sammlungen von Daten arbeitest, die wie ein Stream verwendet werden.\
Dafür gibt es einige gute Gründe:

- entkoppelt den Aufrufer von der Generator-Implementierung in dem Sinne, dass der Aufrufer entscheidet, auf wie viele Elemente er zugreift
- Verzögerte Ausführung, Elemente werden bei Bedarf gerendert
- eingebaute Unterstützung für die Iteration von Elementen mit der `for-of`-Syntax
- Iterables ermöglichen die Implementierung optimierter Iteratormuster

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

Es gibt Bibliotheken, die es ermöglichen, mit Iterables auf ähnliche Weise wie mit nativen Arrays zu arbeiten, indem Methoden wie `map`, `slice`, `forEach` usw. verkettet werden.\
Siehe [itiriri](https://www.npmjs.com/package/itiriri) für ein Beispiel einer fortgeschrittenen Manipulation mit Iterables (oder [itiriri-async](https://www.npmjs.com/package/itiriri-async) für die Manipulation von asynchronen Iterables).

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Objekte und Datenstrukturen

### Getter und Setter verwenden

TypeScript unterstützt die Getter/Setter-Syntax. Getter und Setter zu verwenden, um auf Daten von Objekten zuzugreifen, die Verhalten kapseln, könnte besser sein, als einfach nach einer Eigenschaft auf einem Objekt zu suchen. "Warum?", magst du fragen. Nun, hier ist eine Liste von Gründen:

- Wenn du mehr machen willst, als eine Objekteigenschaft zu erhalten, musst du nicht jeden Accessor in deinem Quellcode nachschlagen und ändern.
- Macht das Hinzufügen einer Validierung einfach, wenn du ein `set` ausführst.
- Verkapselt die interne Repräsentation.
- Einfaches Hinzufügen von Logging und Fehlerbehandlung beim Holen und Setzen.
- Du kannst die Eigenschaften deines Objekts verzögert laden, z.B. indem du es von einem Server holst.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Objekte mit privaten/geschützten Membern versehen

TypeScript unterstützt `public` _(default)_, `protected` und `private` Accessoren für Klassenmitglieder.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Unveränderbarkeit bevorzugen

TypeScripts Typsystem erlaubt es dir, einzelne Eigenschaften auf einer Schnittstelle/Klasse als schreibgeschützt (_readonly_) zu markieren. Dies erlaubt es dir, auf funktionale Weise zu arbeiten (eine unerwartete Mutation ist schlecht).\
Für fortgeschrittenere Szenarien gibt es einen eingebauten Typ `Readonly`, der einen Typ `T` entgegen nimmt und alle seine Eigenschaften als `readonly` markiert, indem er gemappte Typen verwendet (siehe [gemappte Typen](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)).

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
array.push(100); // array will updated
```

**Gut:**

```ts
const array: ReadonlyArray<number> = [1, 3, 5];
array = []; // error
array.push(100); // error
```

Das Deklarieren von `read-only`-Argumenten ist [seit TypeScript 3.4 etwas einfacher](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#improvements-for-readonlyarray-and-readonly-tuples).

```ts
function hoge(args: readonly string[]) {
  args.push(1); // error
}
```

Bevorzuge [`const assertions`](https://github.com/microsoft/TypeScript/wiki/What's-new-in-TypeScript#const-assertions) für Literalwerte.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### type vs. interface

Verwende einen Typ, wenn du eine Vereinigung oder eine Schnittmenge brauchst. Verwende ein Interface, wenn du Erweiterungen (`extends`) oder Implementierungen (`implements`) brauchst. Es gibt keine strikte Regel, verwende jedoch die, die für dich funktioniert.\
Eine detailliertere Erklärung über die Unterschiede zwischen `type` und `interface` in TypeScript findest du in dieser [Antwort](https://stackoverflow.com/questions/37233735/typescript-interfaces-vs-types/54101543#54101543).

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Klassen

### Klassen sollten klein sein

Die Größe der Klasse wird an ihrer Verantwortung gemessen. Nach dem [Prinzip der eindeutigen Verantwortlichkeit](https://de.wikipedia.org/wiki/Single-Responsibility-Prinzip) (_Single Responsibility Prinzip_) sollte eine Klasse klein sein.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Hohe Kohäsion und geringe Kopplung

[Kohäsion](https://de.wikipedia.org/wiki/GRASP#High_Cohesion) definiert den Grad, in dem die Klassenmitglieder miteinander in Beziehung stehen. Idealerweise sollten alle Felder innerhalb einer Klasse von jeder Methode verwendet werden. Wir sagen dann, dass die Klasse _maximal kohäsiv_ ist. In der Praxis ist dies jedoch nicht immer möglich und auch nicht ratsam. Du solltest es jedoch bevorzugen, dass der Kohäsion hoch ist.

[Kopplung](https://de.wikipedia.org/wiki/GRASP#Low_Coupling) bezieht sich darauf, wie verwandt oder abhängig zwei Klassen zueinander sind. Man spricht von einer geringen Kopplung, wenn sich Änderungen in einer Klasse nicht auf die andere auswirken.

Gutes Softwaredesign hat **hohe Kohäsion** und **geringe Kopplung**.

**Schlecht:**

```ts
class UserManager {
  // Schlecht: each private variable is used by one or another group of methods.
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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Komposition gegenüber Vererbung bevorzugen

Wie schon in [Design Patterns](https://de.wikipedia.org/wiki/Entwurfsmuster) von der Gang of Four erwähnt, solltest du _Komposition der Vererbung vorziehen_, wo immer du kannst. Es gibt viele gute Gründe, Vererbung zu verwenden und viele gute Gründe, Komposition zu verwenden. Der Hauptpunkt für diese Maxime ist, dass wenn dein Verstand instinktiv zur Vererbung greift, versuche zu überlegen, ob Komposition dein Problem besser modellieren könnte. In einigen Fällen kann sie das.

Du fragst dich dann vielleicht: "Wann sollte ich Vererbung verwenden?" Es hängt von deinem Problem ab, aber dies ist eine gute Liste, wann Vererbung mehr Sinn macht als Komposition:

1. Deine Vererbung stellt eine "is-a"-Beziehung dar und nicht eine "has-a"-Beziehung (Mensch -> Tier vs. User -> UserDetails).
1. Du kannst Code aus den Basisklassen wiederverwenden (Menschen können sich wie alle Tiere bewegen).
1. Du willst globale Änderungen an abgeleiteten Klassen vornehmen, indem du eine Basisklasse änderst. (Ändere den Kalorienverbrauch aller Tiere, wenn sie sich bewegen).

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Methodenverkettung verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## SOLID

### Prinzip der eindeutigen Verantwortlichkeit - Single Responsibility Principle (SRP)

Wie es in Clean Code heißt: "Es sollte nie mehr als einen Grund für eine Klasse geben, sich zu ändern". Es ist verlockend, eine Klasse mit einer Menge Funktionalität vollzupacken, wie wenn du nur einen Koffer mit auf deinen Flug nehmen kannst. Das Problem dabei ist, dass deine Klasse nicht konzeptionell zusammenhängend sein wird und es viele Gründe gibt, sie zu ändern. Es ist wichtig, dass du die Zeit, die du brauchst, um eine Klasse zu ändern, minimierst. Es ist wichtig, denn wenn zu viel Funktionalität in einer Klasse ist und du einen Teil davon änderst, kann es schwierig sein zu verstehen, wie sich das auf andere abhängige Module in deinem Quellcode auswirkt.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Prinzip der Offen- und Verschlossenheit - Open/Closed Principle (OCP)

Wie Bertrand Meyer sagt: "Software-Entitäten (Klassen, Module, Funktionen usw.) sollten offen für Erweiterungen, aber geschlossen für Änderungen sein." Was bedeutet das aber? Dieses Prinzip besagt im Grunde, dass du den Benutzern erlauben solltest, neue Funktionalitäten hinzuzufügen, ohne den bestehenden Code zu verändern.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Liskovsche Substitutionsprinzip - Liskov Substitution Principle (LSP)

Dies ist ein beängstigender Begriff für ein sehr einfaches Konzept. Er ist formal definiert als "Wenn `S` ein Subtyp von `T` ist, dann können Objekte vom Typ `T` durch Objekte vom Typ `S` ersetzt werden (d.h. Objekte vom Typ `S` können Objekte vom Typ `T` ersetzen), ohne dass sich irgendeine der wünschenswerten Eigenschaften des Programms (Korrektheit, ausgeführte Aufgabe usw.) ändert." Das ist eine noch erschreckendere Definition.

Die beste Erklärung dafür ist, wenn du eine Elternklasse und eine Kindklasse hast, dann können die Elternklasse und die Kindklasse austauschbar verwendet werden, ohne falsche Ergebnisse zu erhalten. Das könnte immer noch verwirrend sein, also lass uns einen Blick auf das klassische Quadrat-Rechteck-Beispiel werfen: Mathematisch gesehen ist ein Quadrat ein Rechteck, aber wenn du es mit der "is-a"-Beziehung über Vererbung modellierst, kommst du schnell in Schwierigkeiten.

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
    const area = rectangle.setWidth(4).setHeight(5).getArea(); // Schlecht: Returns 25 for Square. Should be 20.
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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Schnittstellenaufteilungsprinzip - Interface Segregation Principle (ISP)

ISP besagt, dass "Clients nicht gezwungen werden sollten, von Schnittstellen abhängig zu sein, die sie nicht nutzen". Dieses Prinzip ist sehr verwandt mit dem _Single Responsibility Principle_. Was es wirklich bedeutet ist, dass du deine Abstraktionen immer so gestalten solltest, dass die Clients, die die exponierten Methoden nutzen, nicht den ganzen Kuchen abbekommen. Das bedeutet auch, dass du den Clients die Last auferlegst, Methoden zu implementieren, die sie eigentlich nicht brauchen.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Abhängigkeits-Umkehr-Prinzip - Dependency Inversion Principle (DIP)

Dieses Prinzip besagt zwei wesentliche Dinge:

1. High-Level-Module sollten nicht von Low-Level-Modulen abhängen. Beide sollten von Abstraktionen abhängen.
2. Abstraktionen sollten nicht von Details abhängen. Details sollten von Abstraktionen abhängen.

Dies kann anfangs schwer zu verstehen sein, aber wenn du mit Angular gearbeitet hast, hast du eine Implementierung dieses Prinzips in Form von [Dependency Injection (DI)](https://de.wikipedia.org/wiki/Dependency_Injection) gesehen. Es handelt sich zwar nicht um ein identisches Konzept, aber DI verhindert, dass High-Level-Module die Details der Low-Level-Module kennen und diese einrichten. Dies kann durch DI erreicht werden. Ein großer Vorteil davon ist, dass es die Kopplung zwischen Modulen reduziert. Kopplung ist ein sehr schlechtes Entwicklungsmuster, weil es deinen Code schwer zu überarbeiten macht.

DIP wird normalerweise durch die Verwendung eines IoC-Containers ([Inversion of Control](https://de.wikipedia.org/wiki/Inversion_of_Control)) erreicht. Ein Beispiel für einen leistungsfähigen IoC-Container für TypeScript ist [InversifyJs](https://www.npmjs.com/package/inversify)

**Schlecht:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

class XmlFormatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}

class ReportReader {

  // Schlecht: We have created a dependency on a specific request implementation.
  // We should just have ReportReader depend on a parse method: `parse`
  private readonly formatter = new XmlFormatter();

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader();
await report = await reader.read('report.xml');
```

**Gut:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

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
  constructor(private readonly formatter: Formatter) {
  }

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader(new XmlFormatter());
await report = await reader.read('report.xml');

// or if we had to read a json report
const reader = new ReportReader(new JsonFormatter());
await report = await reader.read('report.json');
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Testing

Testen ist wichtiger als Ausliefern. Wenn du keine oder nur eine unzureichende Menge von Tests hast, dann wirst du jedes Mal, wenn du Code auslieferst, nicht sicher sein, dass du nichts kaputt gemacht hast. Die Entscheidung darüber, was eine angemessene Menge ist, liegt bei deinem Team. Aber eine 100%ige Abdeckung (alle Anweisungen und Verzweigungen) zu haben, ist die Art und Weise, wie du ein sehr hohes Vertrauen und den Seelenfrieden der Entwickler erreichst. Das bedeutet, dass du nicht nur ein großartiges Test-Framework haben musst, sondern auch ein gutes [Abdeckungswerkzeug](https://github.com/gotwarlost/istanbul) verwenden musst.

Es gibt keine Ausrede, keine Tests zu schreiben. Es gibt [viele gute JS-Test-Frameworks](http://jstherightway.org/#testing-tools) mit Typisierungsunterstützung für TypeScript. Also finde eines, das dein Team bevorzugt. Wenn du eines gefunden hast, das für dein Team funktioniert, dann strebe an, immer Tests für jedes neue Feature/Modul zu schreiben, das du einführst. Wenn deine bevorzugte Methode [Test Driven Development (TDD)](https://de.wikipedia.org/wiki/Testgetriebene_Entwicklung) ist, ist das großartig. Aber der wichtigste Punkt ist, dass du einfach sicherstellst, dass du deine Abdeckungsziele erreichst, bevor du ein Feature einführst oder ein bestehendes überarbeitest.

### Die drei Gesetze von Test Driven Development (TDD)

1. Du darfst keinen produktiven Code schreiben, es sei denn, es geht darum, einen fehlgeschlagenen Unit-Test zu bestehen.
2. Du darfst nicht mehr von einem Unit-Test schreiben, als zum Scheitern ausreicht, und; Kompilierungsfehler sind Fehler.
3. Du darfst nicht mehr Produktionscode schreiben, als nötig ist, um den einen fehlgeschlagenen Unit-Test zu bestehen.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### F.I.R.S.T. Regeln

Saubere Tests sollten den Regeln folgen:

- **Fast**: Schnelle Tests sollten schnell sein, weil wir sie häufig ausführen wollen.
- **Independent**: Unabhängige Tests sollten nicht voneinander abhängen. Sie sollten die gleiche Ausgabe liefern, egal ob sie unabhängig voneinander oder alle zusammen in beliebiger Reihenfolge ausgeführt werden.
- **Repeatable**: Wiederholbar Tests sollten in jeder Umgebung wiederholbar sein und es sollte keine Ausrede geben, warum sie fehlschlagen.
- **Self-Validating**: Selbst-validierend sollte ein Test entweder mit _Passed_ oder _Failed_ antworten. Du musst keine Logdateien vergleichen, um festzustellen, ob ein Test bestanden wurde.
- **Timely**: Zeitnahe Unit-Tests sollten vor dem Produktionscode geschrieben werden. Wenn du Tests nach dem Produktionscode schreibst, könnte es dir zu schwer fallen, Tests zu schreiben.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Einzelkonzept pro Test

Auch Tests sollten dem _Single Responsibility Principle_ folgen. Machen Sie nur eine Assert pro Unit-Test.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Parallelität

### Promises vor Callbacks bevorzugen

Callbacks sind nicht sauber und verursachen exzessive Mengen an Verschachtlungen _(der Callback-Hölle)_.\
Es gibt Hilfsprogramme, die bestehende Funktionen im Callback-Stil in eine Version umwandeln, die `Promises` zurückgibt (siehe für Node.js [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original), für allgemeine Zwecke [pify](https://www.npmjs.com/package/pify) oder [es6-promisify](https://www.npmjs.com/package/es6-promisify))

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

`Promises` unterstützen einige Hilfsmethoden, die helfen, den Code übersichtlicher zu gestalten:

| Muster                   | Beschreibung                                                                                                                                                                            |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Promise.resolve(value)` | Einen Wert in ein aufgelöstes `Promises` umwandeln.                                                                                                                                     |
| `Promise.reject(error)`  | Wandelt einen Fehler in ein abgelehntes `Promises` um.                                                                                                                                  |
| `Promise.all(promises)`  | Gibt ein neues `Promises` zurück, das mit einem Array von Werten für die übergebenen `Promises` erfüllt wird oder mit dem Grund des ersten abgelehnten `Promises`s zurückgewiesen wird. |
| `Promise.race(promises)` | Gibt ein neues `Promises` zurück, das mit dem Ergebnis/Fehler des ersten abgewickelten `Promises`s aus dem Array der übergebenen `Promises` erfüllt/abgelehnt wird.                     |

`Promise.all` ist besonders nützlich, wenn es notwendig ist, Aufgaben parallel laufen zu lassen. `Promise.race` macht es einfacher, Dinge wie Timeouts für `Promises` zu implementieren.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Async/Await sind noch sauberer als Promises

Mit der `async`/`await` Syntax kannst du Code schreiben, der viel sauberer und verständlicher ist als verkettete `Promises`. Innerhalb einer Funktion, der das Schlüsselwort `async` vorangestellt ist, hast du die Möglichkeit, der JavaScript-Laufzeit zu sagen, dass sie die Ausführung des Codes auf das Schlüsselwort `await` (wenn es auf ein `Promises` angewendet wird) pausieren soll.

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

async function downloadPage(url: string, saveTo: string): Promise<string> {
  const response = await get(url);
  await write(saveTo, response);
  return response;
}

// somewhere in an async function
try {
  const content = await downloadPage(
    "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
    "article.html"
  );
  console.log(content);
} catch (error) {
  console.error(error);
}
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Fehlerbehandlung

Geworfene Fehler sind eine gute Sache! Sie bedeuten, dass die Laufzeitumgebung erfolgreich erkannt hat, dass etwas in deinem Programm schief gelaufen ist. Sie lässt es dich wissen, indem sie die Funktionsausführung auf dem aktuellen Stack stoppt, den Prozess (in Node) beendet und dich in der Konsole mit einem Stacktrace benachrichtigt.

### Immer Error zum Werfen oder Verwerfen verwenden

Sowohl JavaScript als auch TypeScript erlauben es dir, ein beliebiges Objekt zu werfen (`throw`). Ein Promise kann auch mit einem beliebigen Grundobjekt verworfen werden.\
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

Der Vorteil der Verwendung von `Error` Typen ist, dass es von der Syntax `try/catch/finally` unterstützt wird und implizit alle Fehler die Eigenschaft `stack` haben, was sehr mächtig für das Debugging ist.\
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

Eine detaillierte Erklärung dieser Idee findest du von [Dhruv Rajvanshi](https://medium.com/@dhruvrajvanshi/making-exceptions-type-safe-in-typescript-c4d200ee78e9).

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Gefangene Fehler nicht ignorieren

Wenn du nichts mit einem gefangenen Fehler machst, hast du nicht die Möglichkeit, den Fehler zu beheben oder darauf zu reagieren. Den Fehler auf der Konsole zu protokollieren (`console.log`) ist nicht viel besser, da er oft in einem Meer von Dingen, die auf der Konsole ausgegeben werden, verloren gehen kann. Wenn du ein Stück Code in ein `try/catch` verpackst, bedeutet das, dass du denkst, dass dort ein Fehler auftreten könnte und deshalb solltest du, für den Fall, dass er auftritt, einen Plan haben.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Abgelehnte Promises nicht ignorieren

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Formatierung

Formatierung ist subjektiv. Wie bei vielen Regeln hier gibt es keine feste Regel, die du befolgen musst. Der wichtigste Punkt ist, dass du dich _NICHT_ über die Formatierung streiten solltest. Es gibt tonnenweise Tools, um dies zu automatisieren. Benutze eins! Es ist eine Verschwendung von Zeit und Geld für Entwickler, sich über die Formatierung zu streiten. Die allgemeine Regel, die man befolgen sollte, ist: _nutze konsistente Formatierungsregeln_.

Für TypeScript gibt es ein mächtiges Werkzeug namens [TypeScript ESLint](https://typescript-eslint.io/). Es ist ein statisches Analysetool, das dir helfen kann, die Lesbarkeit und Wartbarkeit deines Codes dramatisch zu verbessern. Es gibt fertige ESLint-Konfigurationen, die du in deinen Projekten referenzieren kannst:

- [ESLint Config Standard](https://www.npmjs.com/package/eslint-config-standard-with-typescript) - ESLint config for TypeScript that is based on eslint-config-standard
- [ESLint Config Airbnb](https://www.npmjs.com/package/eslint-config-airbnb-typescript) - Airbnb's ESLint config with TypeScript support
- [ESLint Config ReactJS](https://www.npmjs.com/package/eslint-config-react-app) - ESLint configuration used by Create React App
- [ESLint + Prettier](https://www.npmjs.com/package/eslint-config-prettier) - Turns off all rules that are unnecessary or might conflict with [Prettier](https://github.com/prettier/prettier).
- [ESLint Plugin Functional](https://www.npmjs.com/package/eslint-plugin-functional) - Disable mutation and promote functional programming in JavaScript and TypeScript.

Siehe auch diesen großartigen [TypeScript StyleGuide und Coding-Konventionen](https://basarat.gitbook.io/typescript/styleguide).

### Konsistente Großschreibung verwenden

Die Großschreibung verrät dir viel über deine Variablen, Funktionen, usw. Diese Regeln sind subjektiv, also kann dein Team wählen, was immer sie wollen. Der Punkt ist, egal was ihr alle wählt, seid einfach _konsequent_ bei der Nutzung.

**Schlecht:**

```ts
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

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
Verwende bevorzugt `camelCase` für Variablen, Funktionen und Klassenmember.

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Funktionsaufrufer und -aufrufender sollten sich nahe sein

Wenn eine Funktion eine andere aufruft, halte diese Funktionen vertikal dicht in der Quelldatei. Idealerweise solltest du den Aufrufer direkt über dem Aufrufenden halten. Wir neigen dazu, Code von oben nach unten zu lesen, wie eine Zeitung. Deshalb solltest du dafür sorgen, dass sich dein Code auch so liest.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Imports organisieren

Mit sauberen und einfach zu lesenden Import-Anweisungen kannst du schnell die Abhängigkeiten des aktuellen Codes erkennen. Achte darauf, dass du die folgenden guten Praktiken für `import`-Anweisungen anwendest:

- Importanweisungen sollten alphabetisch geordnet und gruppiert werden.
- Unbenutzte Importe sollten entfernt werden.
- Benannte Importe müssen alphabetisch geordnet sein (z.B. `import {A, B, C} from 'foo';`)
- Importquellen müssen innerhalb von Gruppen alphabetisch geordnet sein, d.h.: `import * as foo from 'a'; import * as bar from 'b';`
- Gruppen von Importen werden durch Leerzeilen abgegrenzt.
- Gruppen müssen folgende Reihenfolge einhalten:
  - Polyfills (z.B. `import 'reflect-metadata';`)
  - Node builtin Module (z.B. `import fs from 'fs';`)
  - externe Module (z.B. `import { query } from 'itiriri';`)
  - interne Module (z.B. `import { UserService } from 'src/services/userService';`)
  - Module aus einem übergeordneten Verzeichnis (z.B. `import foo from '../foo'; import qux from '../../foo/qux';`)
  - Module aus dem gleichen oder einem Geschwisterverzeichnis (z.B. `import bar from './bar'; import baz from './bar/baz';`)

**Schlecht:**

```ts
import { TypeDefinition } from "../types/typeDefinition";
import { AttributeTypes } from "../model/attribute";
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

import { AttributeTypes } from "../model/attribute";
import { TypeDefinition } from "../types/typeDefinition";

import { ApiCredentials, Adapters } from "./common/api/authorization";
import { ConfigPlugin } from "./plugins/config/configPlugin";
```

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Typescript-Aliase verwenden

Erstelle hübschere Importe, indem du die Pfade und `baseUrl` Eigenschaften im `CompilerOptions` Abschnitt in der `tsconfig.json` definierst.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Kommentare

Die Verwendung eines Kommentars ist ein Hinweis darauf, dass man sich ohne ihn nicht ausdrücken kann. Der Code sollte die einzige Quelle der Wahrheit sein.

> Don’t comment bad code — rewrite it.  
> — _Brian W. Kernighan and P. J. Plaugher_

### Selbsterklärenden Code anstelle von Kommentaren bevorzugen

Kommentare sind eine Entschuldigung, keine Pflicht. Guter Code dokumentiert sich _meist_ selbst.

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Keinen auskommentierten Code im Quellcode lassen

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Keine Journal-Kommentare verwenden

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### Positionsmarkierungen vermeiden

Positionsmarkierungen fügen normalerweise nur Unruhe hinzu. Lass die Funktionen und Variablennamen zusammen mit der richtigen Einrückung und Formatierung deinem Code die visuelle Struktur geben.  
Die meisten IDEs unterstützen Code-Folding-Features, die es dir ermöglichen, Codeblöcke zu komprimieren/expandieren (siehe [Folding Regions in Visual Studio Code](https://code.visualstudio.com/updates/v1_17#_folding-regions)).

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

### TODO-Kommentare

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

**[⬆ zurück nach oben](#inhaltsverzeichnis)**

## Übersetzungen

Clean-Code-Konzepte sind auch in anderen Sprachen verfügbar:

- ![us](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/United-States.png) **Das original in Englisch**: [labs42io/clean-code-typescript](https://github.com/labs42io/clean-code-typescript)

- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brasilianisches Portugiesisch**: [vitorfreitas/clean-code-typescript](https://github.com/vitorfreitas/clean-code-typescript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinesisch**:
  - [beginor/clean-code-typescript](https://github.com/beginor/clean-code-typescript)
  - [pipiliang/clean-code-typescript](https://github.com/pipiliang/clean-code-typescript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **Französisch**: [ralflorent/clean-code-typescript](https://github.com/ralflorent/clean-code-typescript)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanisch**: [MSakamaki/clean-code-typescript](https://github.com/MSakamaki/clean-code-typescript)
- ![ko](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Koreanisch**: [738/clean-code-typescript](https://github.com/738/clean-code-typescript)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russisch**: [Real001/clean-code-typescript](https://github.com/Real001/clean-code-typescript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanisch**: [JoseDeFreitas/clean-code-typescript](https://github.com/JoseDeFreitas/clean-code-typescript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Türkisch**: [ozanhonamlioglu/clean-code-typescript](https://github.com/ozanhonamlioglu/clean-code-typescript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamesisch**: [hoangsetup/clean-code-typescript](https://github.com/hoangsetup/clean-code-typescript)

Referenzen werden hinzugefügt, sobald die Übersetzungen abgeschlossen sind.  
Schau dir diese [Diskussion](https://github.com/labs42io/clean-code-typescript/issues/15) für weitere Details und Fortschritte an. Du kannst einen unverzichtbaren Beitrag zur _Clean Code_ Community leisten, indem du dies in deine Sprache übersetzt.
