## Erste Schritte mit der React-Komponente

Unsere erste React-Komponente findest du in der Datei *src/App.js*. Der Code weicht unter Umständen geringfügig ab, da die [Create-React-App](https://github.com/facebook/create-react-app) von Zeit zu Zeit die Struktur der Standardkomponente aktualisiert.

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

Sofern nicht anders angegeben, dreht sich hier alles um die Datei *src/App.js*. Als Erstes vereinfachen wir die Komponente. So baust du dein Projekt ohne unnötigen Boilerplate-Code aus der [Create-React-App](https://github.com/facebook/create-react-app) auf.

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

Bemerkenswert sind drei Punkte: Zum einen ist diese React-Komponente, die als App-Komponente bezeichnet wird, nichts anderes als eine JavaScript-Funktion. Sie wird allgemein **Funktionskomponente** genannt, da es andersartig Variationen von React-Komponenten gibt (siehe **Komponententypen**). Zweitens enthält die App-Komponente keine Parameter in ihrer Funktionssignatur (siehe **Eigenschaften**). Und drittens gibt die App-Komponente Code zurück, der HTML ähnelt und JSX heißt (siehe Kapitel **JSX**).

Die Funktionskomponente verfügt über Implementierungsdetails, wie jede andere JavaScript-Funktion. Du wirst diese während deines gesamten Ausflugs in die React-Welt praktisch anwenden:

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

Da wir innerhalb der für diese Variable verwendeten App-Komponente nichts benötigen --- zum Beispiel Parameter der Funktionssignatur --- definieren wir sie außerhalb:

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

Im nächsten Abschnitt verwenden wir diese Variable.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-the-React-Component).
* Wenn du nicht sicher bist, wann du `const`, `let` oder `var` in JavaScript (oder React) für Variablendeklarationen verwendest, lese [mehr meinen Text zu diesem Thema](https://www.robinwieruch.de/const-let-var).
  * Lese mehr zum [Schlüsselwort const](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/const).
  * Lese mehr zum [Schlüsselwort let](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/let).
* Überlege dir, wie du die Variable `title` im zurückgegebenen HTML-Code der App-Komponente anzeigst.
