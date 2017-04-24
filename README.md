# js-guide

Этот гайд позволит вам быстро понять лучшие практики построения клиентских приложений в проектах.
Фронтовые приложения написаны на `TypeScript` и `JavaScript` редакции ES6.

Подробнее о `Typescript`: http://www.typescriptlang.org/
Подробнее о `JavaScript` редакции ES6: https://github.com/lukehoban/es6features

## Code Styles

Для `TypeScript` мы стараемся придерживаться официальных code-styles `TypeScript` от Microsoft https://github.com/Microsoft/TypeScript/wiki/Coding-guidelines

Для `JavaScript` code-styles от Airbnb https://github.com/airbnb/javascript

## React.js

Мы используем в основном React.js для наших проектов: https://facebook.github.io/react/
В качестве каркасса приложений чаще всего используется Redux: http://redux.js.org/

## Best Practices

Используйте пробел перед слешом в самозакрывающемся теги.

```
/* Bad */
<Foo/>

/* Good */
<Foo />
```

Всегда самозакрывайте пустые теги.

```
/* Bad */
<Foo className="stuff"></Foo>

/* Good */
<Foo className="stuff" />
```

Оборачивайте JSX-теги в круглые скобки, если для верстки требуется больше, чем одна строка.

```
/* Bad */
class SomeComponent extends React.Component {
    render() {
        return <MyComponent className="long body" foo="bar">
            <MyChild />
        </MyComponent>;
    }
}

/* Good */
class SomeComponent extends React.Component {
    render() {
        return (
            <MyComponent className="long body" foo="bar">
                <MyChild />
            </MyComponent>;
        );
    }
}
```

Префиксируйте все имена методов, используемых для создания верстки префиксом `render`.

```
/* Bad */
class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout">
                { this.getContent() }
            </div>
        );
    }

    getContent() {
        return <div className="content"> ... </div>;
    }
}

/* Good */
class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout">
                { this.renderContent() }
            </div>
        );
    }

    renderContent() {
        return <div className="content"> ... </div>;
    }
}
```

Префиксируйте все имена обработчиков внутри компонента префиксом `handle`.

```
/* Bad */
class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout" onClick={ this.click } />
        );
    }

    click() {
        ...
    }
}

/* Good */
class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout" onClick={ this.handleClick } />
        );
    }

    handleClick() {
        ...
    }
}
```

Используйте `autobind-decorator` для обработчиков вместо привязывания обработчиков в конструкторе.

```
/* Bad */
class SomeComponent extends React.Component {
    constructor(props) {
        super(props);

        this.handleClick = this.handleClick.bind(this);
    }

    render() {
        return (
            <div className="layout" onClick={ this.handleClick } />
        );
    }

    handleClick() {
        ...
    }
}

/* Good */
import autobind from 'autobind-decorator';

class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout" onClick={ this.handleClick } />
        );
    }

    @autobind
    handleClick() {
        ...
    }
}
```

Префиксируйте внешние обработчики префиксом `on`. Всегда проверяйте наличие обработчика перед вызовом.

```
/* Bad */
import autobind from 'autobind-decorator';

class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout" onClick={ this.handleClick } />
        );
    }

    @autobind
    handleClick() {
        this.props.click();
    }
}

/* Good */
import autobind from 'autobind-decorator';

class SomeComponent extends React.Component {
    render() {
        return (
            <div className="layout" onClick={ this.handleClick } />
        );
    }

    @autobind
    handleClick() {
        if (this.props.onClick) {
            this.props.onClick();
        }
    }
}
```

Props'ы простого компонента всегда наследуйте от `React.CommonAttributes`.

```
/* Bad */
import React from 'react';

export interface Component1Props {
    ...
}
export interface Component2Props extends React.DOMAttributes {
    ...
}
export interface Component3Props extends React.HTMLAttributes {
    ...
}

/* Good */
import React from 'react';

export interface ComponentProps extends React.CommonAttributes {
    ...
}
```

При описании props'ов коллбэки указывайте в самом конце.

```
/* Bad */
export interface ComponentProps {
    value?: string;
    onSomethingHappened?: () => void;
    category?: string;
}

/* Good */
export interface ComponentProps {
    value?: string;
    category?: string;
    onSomethingHappened?: () => void;
}
```

Всегда работайте с DOM через `react-dom` и `ref`. Не используйте jquery!

```
/* Bad */
import React from 'react';

class Component {
    handleSomething() {
        let firstTry = React.findDOMNode(...);
        let secondTry = $(...);
    }
}

/* Good */
import ReactDOM from 'react-dom';

class Component {
    refs: {
        [key: string]: React.Component<any, any>;
        someElement: React.Component<any, any>;
    }

    handleSomething() {
        let nodeElement = ReactDOM.findDOMNode(this.refs.someElement);
    }
}
```
