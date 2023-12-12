# 특정 레거시 js에서 OOP를 시도했던 방법



```
(page = (function() {
    page = function(){};
    page.prototype = {
        init: async function() {
           common.ui.setTitle("test");

            ..
           this.bindEvent();
        },

    bindEvent: function() {  .. }
    }, ..

    } return new page();
})()).init();
```


일단 위 코드에서 주목할 점은 2가지다.

(1) 모듈화를 위한 IIFE [(즉시 실행 함수 표현)](https://web.archive.org/web/20171201033208/http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife) <br>
(2) 객체지향 구현을 위해 상속 작업 구현해 필요한 prototype
