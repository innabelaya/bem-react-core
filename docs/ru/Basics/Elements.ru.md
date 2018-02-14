# Элементы

``` js
import {decl} from 'bem-react-core';

export default decl({
    block: 'my-block',
    elem: 'my-elem',
    content() {
        return 'elem content goes here';
    }
});
```
Результат:
``` html
<div class='my-block__my-elem'>elem content goes here</div>
```
