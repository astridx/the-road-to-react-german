## Umgang mit Eigenschaften (Props)

Eigenschaften (Props) werden im Komponentenbaum von oben nach unten übergeben. Da wir Props verwenden, um Informationen von Komponente zu Komponente und manchmal mithilfe anderer dazwischen liegender Komponenten zu transportieren, ist es hilfreich, einige Tricks zu kennen, die das Übergeben von Props bequemer gestalten.

*Hinweis: Die nachfolgenden Tipps zur Überarbeitung zeige ich dir, damit du verschiedene JavaScript/React-Muster kennenlernst. Zum Erstellen einer React-Anwendungen sind diese keine Voraussetzung. Betrachte sie als fortgeschrittene React-Techniken, die deinen Quellcode verbessern.*

Eigenschaften (Props) in React sind ein JavaScript-Objekt, andernfalls wäre es nicht möglich, in einer React-Komponente auf `props.list` oder `props.onSearch` zuzugreifen. Da `props` ein Objekt ist, das nur Informationen von einer Komponente an eine andere weitergibt, ist es möglich JavaScript-Tricks darauf anwenden. Beispiel: Zugriff auf die Eigenschaften eines Objekts mithilfe von moderner [JavaScript-Objekt Destrukturierung](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment):

{title="Code Playground",lang="javascript"}
~~~~~~~
const user = {
  firstName: 'Robin',
  lastName: 'Wieruch',
};

// Ohne destrukturierende Zuweisung
const firstName = user.firstName;
const lastName = user.lastName;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"

// Mit destrukturierender Zuweisung
const { firstName, lastName } = user;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"
~~~~~~~

Wenn wir auf mehrere Eigenschaften eines Objekts zugreifen, ist die Verwendung einer Codezeile anstelle von mehreren Zeilen eleganter. Aus diesem Grund wird die Objekt- Destrukturierung in JavaScript gerne und häufig verwendet. Übertragen wir dieses Wissen auf die React-Eigenschaften (props) in unserer Suchkomponente. Dazu verwandeln wir zuerst die Pfeilfunktion der Suchkomponente vom prägnanten knappen Körper in den gewöhnlichen Blockkörper:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const Search = props => {
  return (
# leanpub-end-insert
    <div>
      <label htmlFor="search">Search: </label>
      <input
        id="search"
        type="text"
        value={props.search}
        onChange={props.onSearch}
      />
    </div>
# leanpub-start-insert
  );
};
# leanpub-end-insert
~~~~~~~

Danach ist es möglich die Destrukturierung des `props`-Objekts im Funktionskörper der Komponente anzuwenden:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = props => {
# leanpub-start-insert
  const { search, onSearch } = props;
# leanpub-end-insert

  return (
    <div>
      <label htmlFor="search">Search: </label>
      <input
        id="search"
        type="text"
# leanpub-start-insert
        value={search}
        onChange={onSearch}
# leanpub-end-insert
      />
    </div>
  );
};
~~~~~~~

Dies ist eine grundlegende Destrukturierung des `props`-Objekts in einer React-Komponente. Auf diese Weise ist es möglich, die Eigenschaften des Objekts bequem in der Komponente zu verwenden. Unumgänglich ist es, die Pfeilfunktion der Suchkomponente vom knappen prägnanten Körper in den gewöhnlichen Blockkörper umgestalten, um auf die Eigenschaften von `props` mithilfe der Destrukturierung zuzugreifen. Eine große Vereinfachung ist dies somit nicht, da wir unsere Komponenten ständig umgestalten. Gehen wir deshalb einen Schritt weiter, indem wir das `props`- Objekt sofort in der Funktionssignatur unserer Komponente destrukturieren und den Blockkörper erneut weglassen:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const Search = ({ search, onSearch }) => (
# leanpub-end-insert
  <div>
    <label htmlFor="search">Search: </label>
    <input
      id="search"
      type="text"
      value={search}
      onChange={onSearch}
    />
  </div>
 # leanpub-start-insert
);
# leanpub-end-insert
~~~~~~~

`props` werden selten nur innerhalb der eigenen Komponente verwendet. Stattdessen werden alle Informationen verwendet, die im Objekt `props` enthalten sind. Indem wir das Objekt `props` sofort in der Funktionssignatur destrukurieren, ist es möglich, bequem auf alle Informationen zuzugreifen, ohne das „props“-Objekt vorher zu bearbeiten. Das Erkennen und Verinnerlichen dieser Vereinfachung ist das Lernziel dieses Abschnitts. Im Weiteren werden wir hierauf aufbauen und die Art und Weise, wie wir destrukturieren verbessern. 

Schauen wir uns ein anderes Szenario an, und tauchen dabei tiefer in die Welt der `props` in React ein: Hierzu erstellen wir als erstes eine neue Item-Komponente. Dann nutzen wir unser gelerntes Wissen und setzen diese destrukturiert in der List-Komponente ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const Item = ({ item }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
  </div>
);
# leanpub-end-insert

# leanpub-start-insert
const List = ({ list }) =>
  list.map(item => <Item key={item.objectID} item={item} />);
# leanpub-end-insert
~~~~~~~

`item` in der Itemkomponente hat etwas mit den zuvor besprochenen `props` gemeinsam: Beide sind JavaScript-Objekte. Das Objekt `item` wurde aus den `props` in der Funktionssignatur der Item-Komponente destrukturiert. Es wird trotzdem  nicht direkt in der Item-Komponente verwendet. Das Objekt `item` gibt seine Informationen (Objekteigenschaften) einzig und allein an andere Elemente weiter.

Wie du im Folgen feststellen wirst, ist diese Lösung bestens. Dessen ungeachtet zeige ich dir zwei weitere Variationen, da du so nebenbei Vieles über JavaScript-Objekte lernst und es meiner Meinung nach nie schadet über den Tellerrand zu schauen. Sehen wir uns zunächst die *verschachtelte Destrukturierung* an:

{title="Code Playground",lang="javascript"}
~~~~~~~
const user = {
  firstName: 'Robin',
  pet: {
    name: 'Trixi',
  },
};

// without object destructuring
const firstName = user.firstName;
const name = user.pet.name;

console.log(firstName + ' has a pet called ' + name);
// "Robin has a pet called Trixi"

// with nested object destructuring
const {
  firstName,
  pet: {
    name,
  },
} = user;

console.log(firstName + ' has a pet called ' + name);
// "Robin has a pet called Trixi"
~~~~~~~

Die verschachtelte Destrukturierung hilft uns, auf Eigenschaften von Objekten zuzugreifen, die – wie der Name schon sagt – tiefer verschachtelt sind. Im Beispiel wäre dies der Name des Haustiers. Da das Objekt „item“ in unserer Item-Komponente niemals direkt verwendet wird, führen wir in der Funktionssignatur der Komponente eine *verschachtelte Destrukturierung* durch:

{title="src/App.js",lang="javascript"}
~~~~~~~
// Variation 1: Nested Destructuring

const Item = ({
# leanpub-start-insert
  item: {
    title,
    url,
    author,
    num_comments,
    points,
  },
# leanpub-end-insert
}) => (
  <div>
    <span>
# leanpub-start-insert
      <a href={url}>{title}</a>
# leanpub-end-insert
    </span>
# leanpub-start-insert
    <span>{author}</span>
    <span>{num_comments}</span>
    <span>{points}</span>
# leanpub-end-insert
  </div>
);
~~~~~~~

Die verschachtelte Destrukturierung hilft uns, alle erforderlichen Informationen des Objekts „item“ in der Funktionssignatur zu sammeln, um diese sofort in den Elementen der Komponente zu verwendet werden. Verschachtelte Destrukturierung führt unter Umständen zu Unordnung durch Einrückungen in der Funktionssignatur. Obwohl dies hier nicht die am besten lesbare Option ist, gibt es Szenarien, in denen sie nützlich ist.

Lassen Sie uns einen anderen Ansatz mit den [Spread-](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Spread_syntax) und [Rest](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Functions/rest_parameter)-Operatoren in JavaScript verfolgen. Um unser Beispiel darauf vorzubereiten, werden wir unsere Listen- und Item-Komponenten umgestalten. Anstatt das Element als Objekt von der Liste an die Itemkomponente zu übergeben, übergeben wir alle Eigenschaften des Objekts `item`:

{title="src/App.js",lang="javascript"}
~~~~~~~
// Variation 2: Spread and Rest Operators
// 1. Iteration

const List = ({ list }) =>
  list.map(item => (
    <Item
      key={item.objectID}
# leanpub-start-insert
      title={item.title}
      url={item.url}
      author={item.author}
      num_comments={item.num_comments}
      points={item.points}
# leanpub-end-insert
    />
  ));

# leanpub-start-insert
const Item = ({ title, url, author, num_comments, points }) => (
# leanpub-end-insert
  <div>
    <span>
      <a href={url}>{title}</a>
    </span>
    <span>{author}</span>
    <span>{num_comments}</span>
    <span>{points}</span>
  </div>
);
~~~~~~~
Obwohl die Funktionssignatur der Item-Komponente jetzt präziser ist, ist List-Komponente unübersichtlicher, da jede Eigenschaft einzeln an die Item-Komponente übergeben wird. Verbessern wir dies mithilfe des [JavaScript-Spread-Operators](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

{title="Code Playground",lang="javascript"}
~~~~~~~
const profile = {
  firstName: 'Robin',
  lastName: 'Wieruch',
};

const address = {
  country: 'Germany',
  city: 'Berlin',
  code: '10439',
};

const user = {
  ...profile,
  gender: 'male',
  ...address,
};

console.log(user);
// {
//   firstName: "Robin",
//   lastName: "Wieruch",
//   gender: "male"
//   country: "Germany,
//   city: "Berlin",
//   code: "10439"
// }
~~~~~~~

Mit dem Spread-Operator von JavaScript verteilen wir alle Schlüssel/Wert-Paare eines Objekts auf ein anderes Objekt. Dies ist ebenfalls mithilfe von Reacts JSX möglich. Anstatt, wie zuvor, jede Eigenschaft einzeln über props von der Liste an die Elementkomponente zu übergeben, verwenden wir den Spread-Operator von JavaScript, um alle Schlüssel/Wert-Paare des Objekts als Attribut/Wert-Paare an ein JSX-Element zu übergeben:

{title="src/App.js",lang="javascript"}
~~~~~~~
// Variation 2: Spread and Rest Operators
// 2. Iteration

const List = ({ list }) =>
# leanpub-start-insert
  list.map(item => <Item key={item.objectID} {...item} />);
# leanpub-end-insert

const Item = ({ title, url, author, num_comments, points }) => (
  <div>
    <span>
      <a href={url}>{title}</a>
    </span>
    <span>{author}</span>
    <span>{num_comments}</span>
    <span>{points}</span>
  </div>
);
~~~~~~~

Durch dieses Refactoring wurde die Übergabe der Informationen von der Liste an die Elementkomponente präziser. Wir verwenden [JavaScript-Restparameter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) als Sahnehäubchen. Der JavaScript-Restoperator tritt immer als letzter Teil einer Objekt Destrukturierung auf:

{title="Code Playground",lang="javascript"}
~~~~~~~
const user = {
  id: '1',
  firstName: 'Robin',
  lastName: 'Wieruch',
  country: 'Germany',
  city: 'Berlin',
};

const { id, country, city, ...userWithoutAddress } = user;

console.log(userWithoutAddress);
// {
//   firstName: "Robin",
//   lastName: "Wieruch"
// }

console.log(id);
// "1"

console.log(city);
// "Berlin"
~~~~~~~

Obwohl beide dieselbe Syntax haben (drei Punkte), unterscheidet der Restoperator sich vom Spread-Operator. Während der Restoperator auf der rechten Seite einer Zuweisung steht, wird der Spread-Operator auf der linken Seite eingefügt. Der Restoperator wird immer verwendet, um ein Objekt von einigen seiner Eigenschaften zu trennen.

Jetzt kann es in unserer Listenkomponente verwendet werden, um die `objectID` vom Element zu trennen, da die `objectID` nur als `key` und nicht in der Element-Komponente verwendet wird. Nur das verbleibende (Rest-) Element wird als Attribut/Wert-Paar in die Elementkomponente (wie zuvor) verteilt:

{title="src/App.js",lang="javascript"}
~~~~~~~
// Variation 2: Spread and Rest Operators (final)

const List = ({ list }) =>
# leanpub-start-insert
  list.map(({ objectID, ...item }) => <Item key={objectID} {...item} />);
# leanpub-end-insert

const Item = ({ title, url, author, num_comments, points }) => (
  <div>
    <span>
      <a href={url}>{title}</a>
    </span>
    <span>{author}</span>
    <span>{num_comments}</span>
    <span>{points}</span>
  </div>
);
~~~~~~~

In dieser letzten Variante wird der Restoperator verwendet, um die `objectID` vom Rest des `item` -Objekts zu destruktuieren. Anschließend wird das `item` mit seinen Schlüssel/Wert-Paaren in die Item-Komponente verteilt. Diese letzte Variante ist die prägnanteste, enthält dabei aber erweiterte JavaScript-Funktionen, die relativ unbekannt sind.


In diesem Abschnitt haben wir etwas über die Destrukturieung von JavaScript-Objekten gelernt, die häufig für das Objekt `props`, aber auch für andere Objekte wie das `item`-Objekt verwendet werden kann. Wir haben gesehen, wie verschachtelte Destrukturierung verwendet wird (Variante 1), dabei hat sie in unserem Fall keine Vorteile gebracht. Sie hat die Komponente nur vergrößert. In Zukunft werden Sie wahrscheinlich Anwendungsfälle für verschachtelte Destrukturierungen finden, die von Vorteil sind. Last but not least hast du die Spread- und Rest-Operatoren von Javascript kennengelernt, die nicht miteinander zu verwechselt sind. Sie werden verwendet um Operationen an JavaScript-Objekten auszuführen und das Objekt `props` auf präzise Weise von einer Komponente an eine andere Komponente zu übergeben. Am Ende komme ich auf die ursprüngliche Version des Codes zu unserer Anwendung zurück, auf die wir in den nächsten Kapiteln aufbauen werden:

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list }) =>
  list.map(item => <Item key={item.objectID} item={item} />);

const Item = ({ item }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
  </div>
);
~~~~~~~

Diese Version ist nicht die prägnanteste, aber die einfachsten. Variante 1 mit ihrer verschachtelten Destrukturierung hat keinen Nutzen gebracht, und Variante 2 fügt zu viele erweiterte JavaScript-Funktionen (Spread-Operator, Rest-Operator) hinzu, die nicht jedem bekannt sind. Alle haben ihre Vor- und Nachteile. Achte beim Refactoring einer Komponente immer auf Lesbarkeit, insbesondere wenn du in einem Team arbeiten, und stelle sicher, dass diese einen gemeinsamen React-Code-Stil verwenden.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Props-Handling).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Controlled-Components...hs/Props-Handling?expand=1).
* Lese mehr zum Thema [JavaScript's destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).
* Think about the difference between  JavaScript array destructuring -- which we used for React's `useState` hook -- and object destructuring.
* Lese mehr zum Thema [JavaScript's spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).
* Lese mehr zum Thema [JavaScript's rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).
* Get a good sense about JavaScript (e.g. spread operator, rest parameters, destructuring) and what's related to React (e.g. props) from the last lessons.
* Continue to use your favorite way to handle React's props. If you're still undecided, consider the variation used in the previous section.