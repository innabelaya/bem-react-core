# Блоки

``` js
import {decl} from 'bem-react-core';

export default decl({
    block: 'my-block',
    content() {
        return 'content goes here';
    }
});
```
Результат:
``` html
<div class='my-block'>content goes here</div>
```
