# Modifiers

## Declarative modifiers definition

[Модификатор](https://ru.bem.info/methodology/key-concepts/#Модификатор) —  одно из ключевых понятий методологии БЭМ. Разделить сложные компоненты на отдельные части, разложить каждую вариацию компонента в отдельный модификатор и декларировать поведение компонента в зависимости от подключенного модификатора. Это дает возможность сочетать любые модификтаоры в рантайме в производльной комбинации на одном инстансе, избавиться от конструкций `if` или `switch`.



С помощью Modifiers are supposed to help you make variations of the same component. `bem-react-core` enables you to declare additional behaviour for modifiers easily ([see more in documentation](REFERENCE.md#declmodpredicate-prototypeprops-staticprops)).

#### Before

```jsx
import React from 'react';

export default class Button extends React.Component {
    render() {
        const { size, theme, children } = this.props;
        let className = 'Button',
            content = [children];

        if(size === 'large') {
            className += `Button_size_${size}`;
            content.unshift('Modification for size with value \'large\'.');
        }

        if(theme === 'primary') {
            className += `Button_theme_${theme}`;
            content.unshift('Modification for theme with value \'primary\'.');
        }

        return (
            <button className={className}>
                {content}
            </button>
        );
    }
};
```

Usually declaration of additional behaviour requires extra conditions in main code.
As a different way you could use inheritance, but it's awkward to compose many modifiers of one component
at the same time.

#### After

```jsx
// Button.js

import { decl } from 'bem-react-core';

export default decl({
    block : 'Button',
    tag: 'button',
    mods({ size, theme }) {
        return { size, theme };
    }
});

// Button_size_large.js

import { declMod } from 'bem-react-core';

export default declMod({ size : 'large' }, {
    block : 'Button',
    content() {
        return [
            'Modification for size with value \'large\'.',
            this.__base(...arguments)
        ];
    }
});

// Button_theme_primary.js

import { declMod } from 'bem-react-core';

export default declMod({ theme : 'primary' }, {
    block : 'Button',
    content() {
        return [
            'Modification for theme with value \'primary\'.',
            this.__base(...arguments)
        ];
    }
});
```

__NB__ `bem-react-core` uses [Inherit](https://github.com/dfilatov/inherit) library for declaration. Unlike ES2015 classes it adds ability to dynamically create and modify JS class. It also helps to make super-call (`this.__base(...arguments)`) without specifying method name (`super.content(...arguments)`).




Декларация модификатора принимает первым аргументом функцию-матчер, которая возвращает значение булева типа. Функция-матчер в качестве аргумента принимает объект пропсов и может содержать любые условия. Если функция-матчер возвращает положительное значение, декларация применяется к сущности.

``` js
import {declMod} from 'bem-react-core';

export default declMod(({theme}) => theme === 'normal', {
    block: 'my-block',
    mods({theme}) {
        return {theme};
    },
    content() {
        return 'content with theme normal goes here';
    }
});

// <MyBlock theme='normal' />
```
Результат:
``` html
<div class='my-block my-block_theme_normal'>content with theme normal goes here</div>
```

#### Булевые модификаторы

``` js
import {declMod} from 'bem-react-core';

export default declMod(({simple}) => simple, {
    block: 'my-block',
    mods({simple}) {
        return {simple};
    },
    content() {
        return 'content with simple goes here';
    }
});

// <MyBlock simple />
```
Результат:
``` html
<div class='my-block my-block_simple'>content with simple goes here</div>
```

#### Несколько модификаторов

``` js
export default declMod(({theme}) => theme === 'normal', {
    // ...
    mods({theme}) {
        return {...this.__base.apply(this, arguments), theme};
    },
    // ...
});

export default declMod(({simple}) => simple, {
    // ...
    mods({simple}) {
        return {...this.__base.apply(this, arguments), simple};
    },
    // ...
});

// <MyBlock theme='normal' simple />
```
Результат:
``` html
<div class='my-block my-block_theme_normal my-block_simple'>content with simple goes here</div>
```


