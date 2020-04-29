## Erste Schritte mit der React-Komponente

Unsere erste React-Komponente findest du in der Datei *src/App.js*. Der Code weicht unter Umständen geringfügig ab, da [create-react-app](https://github.com/facebook/create-react-app) von Zeit zu Zeit die Struktur der Standardkomponente aktualisiert.

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
~~~~~~~

Sofern nicht anders angegeben, dreht sich hier alles um diese Datei. Als Erstes vereinfachen wir die Komponente. So baust du dein Projekt ohne unnötigen Boilerplate-Code aus der [create-react-app](https://github.com/facebook/create-react-app) auf.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
import React from 'react';

function App() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
# leanpub-end-insert
~~~~~~~

First, this React component, called App component, is just a JavaScript function. It's commonly called **function component**, because there are other variations of React components  (see **component types** later). Second, the App component doesn't receive any parameters in its function signature yet (see **props** later). And third, the App component returns code that resembles HTML which is called JSX (see **JSX** later).

Bemerkenswert sind drei Punkte: Zum einen ist diese React-Komponente, die als App-Komponente bezeichnet wird, nichts anderes als eine JavaScript-Funktion. Sie wird allgemein **Funktionskomponente** genannt, da es andersartig Variationen von React-Komponenten gibt (siehe **Komponententypen**). Zweitens enthält die App-Komponente keine Parameter in ihrer Funktionssignatur (siehe **Eigenschaften**). Und drittens gibt die App-Komponente Code zurück, der HTML ähnelt und JSX heißt (siehe **JSX** später).

Die Funktionskomponente verfügt wie jede andere JavaScript-Funktion über Implementierungsdetails. Du wirst dies in der Praxis während deines gesamten Ausflugs in die  React-Welt in Aktion sehen:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

function App() {
# leanpub-start-insert
  // do something in between
# leanpub-end-insert

  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

Wie bei allen JavaScript-Funktionen, werden im Funktionskörper festlegte Variablen bei jeder Ausführung neu definiert:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

function App() {
# leanpub-start-insert
  const title = 'React';
# leanpub-end-insert

  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

Da wir innerhalb der für diese Variable verwendeten App-Komponente nichts benötigen – zum Beispiel Parameter, die von der Funktionssignatur stammen – definieren wir die Variable außerhalb der App-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const title = 'React';
# leanpub-end-insert

function App() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

Let's use this variable in the next section.

### Exercises:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-the-React-Component).
* If you are unsure when to use `const`, `let` or `var` in JavaScript (or React) for variable declarations, make sure to [Lese mehr zum Thema their differences](https://www.robinwieruch.de/const-let-var).
  * Lese mehr zum Thema [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const).
  * Lese mehr zum Thema [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let).
* Think about ways to display the `title` variable in your App component's returned HTML. In the next section, we'll put this variable to use.