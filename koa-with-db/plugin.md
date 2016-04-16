# 了解mongoose的插件机制

```
// lastMod.js
module.exports = exports = function lastModifiedPlugin (schema, options) {
  schema.add({ lastMod: Date })

  schema.pre('save', function (next) {
    this.lastMod = new Date
    next()
  })

  if (options && options.index) {
    schema.path('lastMod').index(options.index)
  }
}
```

调用

```
// game-schema.js
var lastMod = require('./lastMod');
var Game = new Schema({ ... });
Game.plugin(lastMod, { index: true });
```