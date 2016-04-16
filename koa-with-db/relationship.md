# 关系（1对1，1对多）在mongoose里如何实现

## 了解关系（1对1，1对多）在mongoose里如何实现

```
UserSchema = new Schema({
    ...
    contacts:[]
});
```

## 了解关系（1对1，1对多，多对多）在mongoose里如何实现

```
ContactSchema = new Schema({
    ...
    owner: {
      type: Schema.ObjectId,
      required: true,
      index: true
    }
});
```

## 了解populate

```
ContactSchema = new Schema({
    ...
    owner: {
      type: Schema.ObjectId,
      ref: ‘user’ 
    }
});

ContactSchema.find({}).populate(‘owner’).exec(callback);
```

