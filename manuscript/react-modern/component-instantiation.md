## Instanziierung einer Komponente in React

Als Nächstes werde ich kurz die JavaScript-Klassen beleuchten, damit du die Komponenten in React besser verstehst. Technisch gesehen unterscheiden die beiden sich, was wichtig ist! Trotzdem bin ich der Meinung, dass die folgenden Erklärungen hilfreich sind, um das Konzept einer Komponente zu verstehen.

Klassen werden in der Regel in objektorientierten Programmiersprachen verwendet. JavaScript, ermöglicht es, das funktionale und objektorientierte Programmierung nebeneinander existiert. Sieh dir die folgende Klasse an, um dir die objektorientierte Programmierung in JavaScript zu vergegenwärtigen:

{title="Code Playground",lang="javascript"}
~~~~~~~
class Developer {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  getName() {
    return this.firstName + ' ' + this.lastName;
  }
}
~~~~~~~

Jede Klasse verfügt über einen Konstruktor, der Argumente als Eingabe annimmt und diese der Klasseninstanz zuweist. Eine Klasse definiert Funktionen (z. B. `getName`), diese nennt man **Methoden** oder **Klassenmethoden**.

Das einmalige Definieren der Klasse ist nur ein Teil, diese zu instanziieren ist ein anderer Teil. Die Klassendefinition ist vereinfacht ausgedrückt die Blaupause ihrer Eigenschaften und Methoden. Wenn eine Instanz mit der Anweisung `new` erstellt wird, ergo wenn die Klasse instanziiert wird, wird diese angewendet.

{title="Code Playground",lang="javascript"}
~~~~~~~
// class definition
class Developer { ... }

// class instantiation
const robin = new Developer('Robin', 'Wieruch');

console.log(robin.getName());
// "Robin Wieruch"

// another class instantiation
const dennis = new Developer('Dennis', 'Wieruch');

console.log(dennis.getName());
// "Dennis Wieruch"
~~~~~~~

Wenn eine Klassendefinition vorhanden ist, ist es möglich *mehrere* Instanzen davon zu erstellt. Dies ähnelt einer React-Komponente, die nur *eine* Komponentendefinition hat, aber in der Regel *mehrere* Komponenteninstanzen hat:

{title="src/App.js",lang="javascript"}
~~~~~~~
// definition of App component
function App() {
  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

      {/* creating an instance of List component */}
      <List />
      {/* creating another instance of List component */}
      <List />
    </div>
  );
}

// definition of List component
function List() { ... }
~~~~~~~

Sobald du eine **Komponente** definiert hast, ist es möglich diese wie ein HTML-**Element** überall in JSX zu verwenden. Das Element erzeugt eine **Komponenteninstanz** --- die Komponente wird "instanziiert". Es ist möglich, beliebig viele Komponenteninstanzen zu erstellen. Das "Instanziieren" unterscheidet sich nicht wesentlich von der Definition und Verwendung einer normalen Klasse.

### Übungen:

* Mache dich mit den Begriffen *Komponentendefinition*, *Komponenteninstanz* und *Element* vertraut.
* Experimentiere, indem du mehrere Komponenteninstanzen der List-Komponente erstellst.
* Überlege, wie es möglich ist, jeder List-Komponente eine eigene `list` zuzuordnen.
