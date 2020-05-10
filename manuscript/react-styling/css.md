# Styling in React

Es gibt viele Möglichkeiten, eine React-Anwendung zu gestalten, und es gibt ausgiebige Debatten über die effektivste **Styling-Strategie** und den unübertrefflichsten **Styling-Ansatz**. Wir werden einige dieser Strategien und Ansätze durchgehen. Wie überall im Leben gibt es für jede Sichtweise Vor- und Nachteile. Hier gehe ich darauf ein, wie Entwickler und Teams herausfinde, was für sie am besten passt.

Wir fangen mit allgemeinem CSS an und sehen uns dann fortgeschrittene Alternativen an: **CSS-in-CSS** (**CSS Modules**) und **CSS-in-JS** (**Styled Components**). Dies sind nur zwei von vielen Strategien/Ansätzen. Außerdem lernst du, wie du skalierbare Vektorgrafiken (SVGs) in deine React-Anwendung integrierst, zum Beispiel ein Logo oder Icons.

Wähle eine [beliebte React-Bibliothek für React](https://www.robinwieruch.de/react-libraries), wenn du planst allgemeine UI-Komponenten (beispielsweise eine Schaltfläche, ein Dialog oder ein Auswahlfeld) nicht selbst einzufügen. Bedenke dabei aber: Du befindest dich auf einer soliden Basis, wenn du React lernst und diese Komponenten eigenhändig erstellst. Daher werden wir hier im Buch keine der UI-Komponentenbibliotheken nutzen.

Die nachfolgenden Ansätze für das Styling sind in der `create-react-app` vorkonfiguriert. Wenn du die Build-Tools (beispielsweise Webpack) selbst verwaltest, sind unter Umständen weitere Konfigurationsschritte erforderlich. Da wir die `create-react-app` verwenden, nutzen wir alles als Assets.

## CSS in React

CSS in React ähnelt dem Standard-CSS. Jede Webanwendung gibt HTML-Elementen ein Attribut `class` (in React ist es `className`), welches später in einer CSS-Datei mit Eigenschaften konkretisiert wird.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className="container">
      <h1 className="headline-primary">My Hacker Stories</h1>
# leanpub-end-insert

      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
    </div>
  );
};
~~~~~~~

Das HTML-Element `<hr />` haben ich entfernt, da CSS sich zukünftig um den Randbereich kümmern wird. In der Vergangenheit wurden Inhalte aus semantischen (bedeutungsmäßigen) und gestalterischen Gründen mittels [`<hr />`](https://wiki.selfhtml.org/index.php?title=HTML/Textstrukturierung/hr&oldid=55963) getrennt. Heute dienen HTML-Element ausschließlich er [semantischen Strukturierung](https://wiki.selfhtml.org/index.php?title=HTML&oldid=61624). Verwende immer CSS, um zwei Bereiche aus gestalterischen Gründen zu trennen. Importieren die CSS-Datei mithilfe von `create-react-app`:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import './App.css';
# leanpub-end-insert
~~~~~~~

In der *src/App.css*-Datei definieren wir die CSS-Klassen, die wir in der App-Komponente verwenden:

{title="src/App.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headline-primary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

Öffne deine Anwendung im Browser und sieh dir an, wie die ersten Stile die Ausgabe verändern. Als Nächstes stylen wir die Item-Komponente. Einige ihrer Elemente erhalten das Attribut `className`. Beachte: Wir verwenden hier eine neue Styling-Technik:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div className="item">
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
# leanpub-start-insert
        className="button button_small"
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Wie du siehst, ist es möglich, das `style`-Attribut direkt bei einem HTML-Elemente einzufügen. JSX übergibt den Stil als Inline-Objekt. So definieren wir dynamisch Stileigenschaften in JavaScript-Dateien anstatt statische in CSS-Dateien. Dieser Ansatz wird als **Inline-Stil** bezeichnet. Verwende den Inline-Stil sparsam, da eine separate Stildefinition dein JSX präziser hält. Außerdem ist die Trennung von [Inhalt und Design](https://wiki.selfhtml.org/index.php?title=HTML/Tutorials/Trennung_von_Inhalt,_Pr%C3%A4sentation_und_Verhalten&oldid=64476) sauberer.

Definiere in der Datei *src/App.css* die neuen CSS-Klassen. Erweiterte CSS-Funktionen (zum Beispiel Verschachtelung) von CSS-Erweiterungen (beispielsweise Sass) sind in diesem Beispiel nicht enthalten, da es sich um [optionale Konfigurationen](https://create-react-app.dev/docs/adding-a-sass-stylesheet/) handelt. 

{title="src/App.css",lang="css"}
~~~~~~~
.item {
  display: flex;
  align-items: center;
  padding-bottom: 5px;
}

.item > span {
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item > span > a {
  color: inherit;
}
~~~~~~~

Der Stil für die Schaltflächen fehlt. Daher definieren wir einen Basisstil und zwei spezifische Stile (small und large). Einen verwenden wir im nächsten Schritt, den andere heben wir uns für später auf:

{title="src/App.css",lang="css"}
~~~~~~~
.button {
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;
}

.button:hover {
  background: #171212;
  color: #ffffff;
}

.button_small {
  padding: 5px;
}

.button_large {
  padding: 10px;
}
~~~~~~~

Neben den Styling-Ansätzen in React sind Namenskonventionen ([CSS-Richtlinien](https://developer.mozilla.org/de/docs/MDN/Contribute/Guidelines/Code_guidelines/CSS)) ein Thema. Das letzte CSS-Snippet folgte den [BEM](http://getbem.com/naming/)-Regeln. Dies erkennst du an den Unterstrichen (`_`). Wähle für dich die Namenskonvention, die zu dir und deinem Team passt. Weil dies subjektiv und individuell ist, werde ich hier nicht weiter auf das Thema eingehen. Stylen wir die nächste React-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className="search-form">
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className="button button_large"
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

Es ist möglich das Attribut `className` als Eigenschaft (props) an eine React-Komponenten zu übergeben, wie du im nächsten Beispiel siehst. Auf diese Art übergibst du der SearchForm-Komponente beispielsweise einen flexiblen Stil mithilfe einer `className`-Eigenschaft. Wähle dabei aus einer Anzahl von Klassen, die in einer CSS-Datei vordefiniert sind. Abschließend formatieren wir die InputWithLabel-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className="label">
# leanpub-end-insert
        {children}
      </label>
      &nbsp;
      <input
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
# leanpub-start-insert
        className="input"
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

Ergänze die restlichen Klassen in der *src/App.css*-Datei:

{title="src/App.css",lang="css"}
~~~~~~~
.search-form {
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
}

.label {
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
}

.input {
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
}
~~~~~~~

Der Einfachheit halber haben wir Elemente wie Beschriftung und Eingabe einzeln in die Datei *src/App.css* gestylt. In einer realen Anwendung ist es besser, diese Elemente einmal global in der Datei *src/index.css* zu definieren. Da React-Komponenten normalerweise in mehrere Dateien aufgeteilt sind, erleichtert eine überlegte und strukturierte gemeinsame Verwendung von Stilen die Verwaltung der Website.

Soweit zum grundlegenden CSS, das die meisten von uns kennen und das ich hier mit einem dynamischeren Inline-Stil kombiniert habe. Aus eigener Erfahrung weiß ich, dass die Arbeit mit komplexen Websites ohne erweiterte Techniken wie [Sass](https://sass-lang.com/) (Syntactically Awesome Style Sheets) aufwendig ist, da pures CSS Vereinfachungen wie Verschachtelung nicht unterstützt. Im folgenden gehe ich deshalb auf diese Techniken ein.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React).
  * Bestätige die [Änderungen](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-in-React?expand=1).
* Lese mehr zum Thema [CSS-Stylesheets in der create-react-app](https://create-react-app.dev/docs/adding-a-stylesheet).
* Lese mehr zu [Sass in der create-react-app](https://create-react-app.dev/docs/adding-a-sass-stylesheet) um erweiterte CSS-Funktionen wie das Verschachteln zu nutzen.
* Versuche, die Eigenschaft `className` von der App an die SearchForm-Komponente zu übergeben, entweder mit dem Wert `button_small` oder `button_large`, und verwende diese als `className` für die Schaltfläche.
