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
