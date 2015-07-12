# js的自定义事件
## jquery中的自定义事件
在jquery中，我们可以通过 `on`方法进行自定义事件的注册

通过`trigger`方法触发已注册的自定义事件

``` javascript
$(document).on('msg', function (name) {
    console.log('a msg for ' + name);
});

$(document).click(function () {
    $(this).trigger('msg', 'zhangfengqi');
});
```

## 自定义事件实现机制的模拟
``` javascript
var Event = (function () {
    // 存放事件队列
    // {事件名: [fn, fn, fn], 事件名2: [fn, fn, fn]}
    var events = {};

    return function () {
        this.events = events;
    };
})();

/**
 * 自定义事件注册
 * @params {String}   name 事件名
 * @params {Function} fn   注册函数
 */
Event.prototype.on = function (name, fn) {
    if (!this.events[name]) {
        this.events[name] = [];
    }
    // 处理存在同名事件的情况
    this.events[name].push(fn);
}

/**
 * 自定义事件触发fire
 * @params {String} name 事件名
 * @params {...*}   args 要传入的参数 个数不限
 */
Event.prototype.trigger = Event.prototype.fire =  function (name) {
    var ev = this.events;

    if (ev[name]) {
        // 存储除了事件名以外的所有参数
        var args = [].slice.call(arguments, 1);
        var fns = ev[name];

        // 依次执行事件队列中的函数
        for (var i = 0; i < fns.length; i++) {
            fns[i].apply(null, args);
        }
        return true;
    }

    return false;
}

/**
 * 注销自定义事件
 * @params {String} name 事件名
 */
Event.prototype.off = function (name) {
    if (this.events[name]) {
        delete this.events[name];
        return true;
    }
    return false;
}
```
### 测试
``` javascript
var ev = new Event();
ev.on('msg', function(a, b){
    console.log(a, b);
});

ev.on('msg', function(a, b){
    alert('secondEvent:'+ a, b);
});

console.log(ev.emit('msg', 1, 2)); // true
```
