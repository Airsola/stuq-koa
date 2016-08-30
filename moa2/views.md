## 庖丁解views

我们使用

```
rails g scaffold user name:string password:string

tree blog/app/views 
blog/app/views
├── layouts
│   └── application.html.erb
└── users
    ├── _form.html.erb
    ├── edit.html.erb
    ├── index.html.erb
    ├── index.json.jbuilder
    ├── new.html.erb
    ├── show.html.erb
    └── show.json.jbuilder

2 directories, 8 files
```

去掉jbuilder，api文件，和视图无关

```
tree blog/app/views 
blog/app/views
├── layouts
│   └── application.html.erb
└── users
    ├── _form.html.erb
    ├── edit.html.erb
    ├── index.html.erb
    ├── new.html.erb
    ├── show.html.erb

2 directories, 8 files
```

总结一下里面的特点

- index是显示列表
- new和edit都复用_form
- show显示出所有即可

我们想一下jade是否也可以呢？

jade特性

- block和extend是布局
- include是包含，可以实现引用_form类似的
- foreach类的也支持

所以jade实现erb的功能毫无问题

### 实现列表

index.html.erb

```
<h1>Listing users</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Password</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @users.each do |user| %>
      <tr>
        <td><%= user.name %></td>
        <td><%= user.password %></td>
        <td><%= link_to 'Show', user %></td>
        <td><%= link_to 'Edit', edit_user_path(user) %></td>
        <td><%= link_to 'Destroy', user, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New User', new_user_path %>

```

转成jade就无比简单了

```
extends ../layouts/layout

block content
  h1 Listing orders

  table
    thead
      tr
        each n in ['name','password']
          th #{n}
        th(colspan="3")
    tbody
      each order in orders
        tr
          each n in ['order.name','order.password']
            td #{ eval(n) }
          td 
            a(href='/orders/#{ order._id}') Show
          td 
            a(href='/orders/#{ order._id}/edit') Edit
          td 
            a(onclick="click_del('/orders/#{ order._id}/')") Delete

  br

  p 
    a(href='/orders/new') New Order

```

- erb里使用 @users
- 而jade里使用['name','password']

效果是一样的。

### 实现创建和编辑

那么创建呢？

new.jade

```
extends ../layouts/layout

block content
  h1 New order
  
  include order

  a(href='/orders') Back

```

其核心在`include order`的order.jade里

这里的order用_form也不是不可以，不过没啥大劲

jade里还有一个特性，include文件，可以把上下文里的文件同名对象也传进去。也就是我include 的order，如果上下文里有的order，的order.jade里。

即减少代码转换，又精简代码，何乐而不为呢？

创建和更新都是类似，我们先看一下更新的edit.jade

```
extends ../layouts/layout

block content
  h1 Editing order

  include order

  a(href='/orders/#{ order._id}') Show
  span |
  a(href='/orders') Back

```

和创建基本无大差别，文字和url稍有变化而已。

看一下order.jade

```
- var _action = order._action == 'edit' ? '#' : '/orders/'
- var _method = order._action == 'edit' ? ""  : "post"
- var _type   = order._action == 'edit' ? "button"  : "submit"
- var onClick  = order._action == 'edit' ?  "click_edit('order-" + order._action + "-form','/orders/" + order._id + "/')" : ""
form(id='order-#{ order._action}-form',action="#{_action}", method="#{_method}",role='form')
  each n in ['order.name','order.password']
    - m = eval(n);
    div(class="field")
      label #{n.split('.')[1]} #{m}
      br
      input(type='text',name="#{n.split('.')[1]}" ,value="#{ m == undefined ? '' : m }")
        
  div(class="actions") 
    input(type='#{_type}',value='Submit',onClick='#{onClick}')
```

这个代码有点丑陋，为了兼容创建和更新，所以定义了几个变量的。

说明

- form表单处理请求，form的action约定了
- 提交分2种
  - 创建，就直接post提交
  - 更新交给了onClick变量对应的click_edit方法（位于public/javascripts/app.js）

看一下这个前端js吧

```
function click_edit(id, url){
  // $('#' + id).attr('action','#');
  console.log(url);
  
  if (!confirm("确认要更新？")) {
    return window.event.returnValue = false;
  }
  
  var form = document.querySelector('form')
  var data = form2obj(form);
  console.log(data);
  
  // return false;
  $.ajax({
    type: "PATCH",
    url: url,
    data : data
  })
  .done(function( res ) {
    if(res.status.code == 0){
      // alert( "Data delete: success " + res.status.msg );
      window.location.href= res.data.redirect;
    }else{
      alert( "Data delete fail: " + res.status.msg );
    }
  });
  
  return false;
}

```

简单的更新，使用PATCH方法而已。这里有一个知识点

```
var data = form2obj(form);
```

把表单里的内容转换成ajax里的传值对象，算是个tips吧（具体见public/javascrips/form2obj.js）。


### 实现删除

首先index.jade里有删除功能

```
  td 
    a(onclick="click_del('/orders/#{ order._id}/')") Delete
```

根据rest路由，我们一般的做法

```
 DELETE /users/:id(.:format)      users#destroy
```

也就是我delete请求到/users/:id即可

即，对应的是click_del方法（位于public/javascripts/app.js）

```

function click_del(url){
  if (!confirm("确认要删除？")) {
    return window.event.returnValue = false;
  }
  
  console.log(url);
  
  $.ajax({
    type: "DELETE",
    url: url
  })
  .done(function( res ) {
    if(res.status.code == 0){
      // alert( "Data delete: success " + res.status.msg );
      window.location.href= location.href;
    }else{
      alert( "Data delete fail: " + res.status.msg );
    } 
  });
}
```

### jade版的crud

详情如下

```
➜  mvc git:(master) ✗ tree app/views/orders 
app/views/orders
├── edit.jade
├── index.jade
├── new.jade
├── order.jade
└── show.jade

0 directories, 5 files
```

### jade版的依赖js

- app.js
  - click_del删除方法
  - click_edit编辑方法
- form2obj.js

```
➜  mvc git:(master) ✗ tree public           
public
├── 404.html
├── 422.html
├── 500.html
├── favicon.ico
├── images
├── javascripts
│   ├── app.js
│   └── form2obj.js
├── robots.txt
├── stylesheets
│   └── style.css
└── uploads

4 directories, 8 files
```

### layout

最后再简单的介绍一下layout.jade

上面已知依赖

- app.js
  - click_del删除方法
  - click_edit编辑方法
- form2obj.js

如果我让生成的代码看不到app和form2obj的存在，生成的代码就可以复用了，是不是？

其实丢在布局文件里就可以

```
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
    link(href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet")
  body
    block content
script(src="http://libs.baidu.com/jquery/1.9.0/jquery.js")
script(src="http://libs.baidu.com/bootstrap/3.0.3/js/bootstrap.min.js")
script(src='/javascripts/form2obj.js')
script(src='/javascripts/app.js')
```

说明

- 引了jq和bootstrap
- 引了app.js
- 引了form2obj.js

简单吧？
