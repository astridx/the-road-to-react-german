## Reacts Legacy: Imperative

In eine Funktionskomponente verwendet man hauptsächlich Reacts `useRef`-Hook für die imperative Programmierung. `Refs` und ihre Anwendung wurden im Laufe der Zeit einige Male überarbeitet:

* String Refs (veraltet)
* Callback Refs
* `createRef ` Refs (exklusiv für Klassenkomponenten)
* `useRef ` Hook Refs (exklusiv für Funktionskomponenten)

Mit React 16.3 hat das React-Team die Funktion `createRef` eingeführt. Diese ist ein Äquivalent zum `useRef`-Hook einer Funktionskomponente, welcher mit Version 16.7 in React integriert wurde:

{title="Code Playground",lang="javascript"}
~~~~~~~
class InputWithLabel extends React.Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.inputRef = React.createRef();
# leanpub-end-insert
  }

  componentDidMount() {
    if (this.props.isFocused) {
# leanpub-start-insert
      this.inputRef.current.focus();
# leanpub-end-insert
    }
  }

  render() {
    ...

    return (
      <>
        ...
        <input
# leanpub-start-insert
          ref={this.inputRef}
# leanpub-end-insert
          id={id}
          type={type}
          value={value}
          onChange={onInputChange}
        />
      </>
    );
  }
}
~~~~~~~

Die Referenz `this.inputRef` wird im Konstruktor der Klasse erstellt und in einer Lebenszyklusmethode verwendet. Der Verweis ist an anderen Stelle verwendbar, beispielsweise um das Eingabefeld aufgrund eines Schaltflächenklick zu fokussieren.

Während `createRef` in den Klassenkomponenten von React verwendet wird, wird der `useRef`-Hook in Funktionskomponenten genutzt. Da Letztere vermehrt eingesetzt werden, ist der `useRef`-Hook heutzutage die gängige Funktion zum Verwalten von Referenzen.

### Übungen:

* Lese mehr zu den [verschiedenen Ref-Techniken in React](https://de.reactjs.org/docs/refs-and-the-dom.html).