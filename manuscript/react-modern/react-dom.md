## React DOM

Nachdem wir die Komponentendefinitionen und das Instanziieren kennengelernt haben, wenden wir das Wissen auf die App-Komponente an. Dies war von Anfang an ein Bestandteil unserer Anwendung. Den Code findest du in der Datei *src/index.js*:

{title="src/index.js",lang="javascript"}
~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
~~~~~~~

Neben React gibt es eine weitere importierte Bibliothek namens `react-dom`. In dieser nutzt die Funktion `ReactDOM.render()` einen HTML-Knoten und ersetzt ihn mit JSX. Der Prozess integriert React in HTML. `ReactDOM.render ()` erwartet zwei Argumente: Das erste ist die Grundlage für das Rendern von JSX. Meist wird eine Instanz der App-Komponente verwendet. Das ist nicht zwingend. Das Übergeben von JSX ohne Komponenteninstanziierung ist völlig ausreichend.

{title="Code Playground",lang="javascript"}
~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~

Das zweite Argument legt fest, wo die React-Anwendung den fertigen HTML-Code einfügt. Erwartet wird ein Element, mit `id='root'`, welches in der Datei *public/index.html* vorhanden ist. Hierbei handelt es sich um eine normale HTML-Datei.

### Übungen:

* Öffnen die Datei *public/index.html* und finde heraus, wo die React-Anwendung deinen HTML-Code einfügt.
* Überlege, wie du eine React-Anwendung in eine externe HTML-Webanwendung integrierst.
* Lese mehr zum Thema [Rendern von Elementen in React](https://de.reactjs.org/docs/rendering-elements.html).
