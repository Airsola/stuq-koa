## 了解virtual属性

```
UserSchema.virtual('is_valid').get(function(){
  console.log('phone_number = ' +this.phone_number)
  if(this.phone_number == undefined | this.invite_code == undefined){
    return false;
  }
  return this.invite_code.length >= 2 && this.phone_number > 0
});
```