---
description: >-
  If you haven't done so yet, create folders for your views/pages and your
  layout. This is what they should look like:
---

# Step 6: Pages, Layouts, Styles

If you haven't done so yet, create folders for your views/pages and your layout. This is what they should look like:

<figure><img src="../.gitbook/assets/Screenshot from 2023-04-15 22-46-10.png" alt=""><figcaption></figcaption></figure>

Let's create all of our pages. First lets create our layout file, we will call it Default.asp. We are going use .asp for the file extension, however you can use what ever extension you wish.

## Default.ux

`Location: Pages/Default.ux`

````html
```
<html>
    <head>
        <title>${headline}</title>
        <link rel="stylesheet" href="/Assets/Default.css">
    </head>
    <body>
        <div class="container">
            <a href="/" class="identity">
                <img src="/Assets/identity.png" style="width:130px;"/>
            </a>
            <div class="hrefs">
                <a href="/">Home</a> <a href="/users">Users</a> 
                
                <c:if spec="${sessionuser == ''}">
                    <a href="/signin">Signin</a> <a href="/signup">Signup!</a>
                </c:if>
                <c:if spec="${sessionuser != ''}">
                    <a href="/signout">Signout</a>
                </c:if>
            </div>
            <br class="clear"/>

            <c:if spec="${sessionuser != ''}">
                <span class="user-info">welcome ${sessionuser}!</span>
            </c:if>


            <c:if spec="${message != ''}">
                <p class="alert">${message}</p>
            </c:if>
            
            <div class="render">
                <c:render/>
            </div>
        </div>
    </body>
</html>
```
````

## Index.ux

`Location: Pages/Index.ux`

```html
<h1>Hi</h1>

<p>An example app</p>
```

## Signin.ux

`Location: Pages/Signin.ux`

````html
```
<h2>Signin</h2>

<form action="/signin" method="post">
    <div class="frm-group">
        <label>Email</label>
        <input type="text" name="email" value="abc@plsar.net"/>
    </div>
    <div class="frm-group">
        <label>Password</label>
        <input type="text" name="password" value="effort."/>
    </div>
    <input type="submit" value="signin"/>
</form>
```
````

## Create.ux

`Location: Pages/Users/Create.ux`

````html
```
<h1>Create User</h1>
<form action="/users/save" method="post">
    <label>Email</label>
    <input type="text" name="email" value=""/>

    <label>Password</label>
    <input type="text" name="password" value=""/>
    <input type="submit" value="save"/>
</form>
```
````

## Edit.ux

`Location: Pages/Users/Edit.ux`

````html
```
<h1>Edit User # ${user.id}</h1>
<form action="/users/update/${user.id}" method="post">
    <label>Email</label>
    <input type="text" name="email" value="${user.email}"/>

    <label>Password</label>
    <input type="text" name="password" value="${user.password}"/>
    <input type="submit" value="update"/>
</form>
```
````

## Index.ux

`Location: Pages/Users/Index.ux`

````html
```
<a href="/users/create">Create New User</a> 

<h1>Users</h1>
<table>
    <tr>
        <th>Email</th>
        <th></th>
        <th></th>
    </tr>
    <c:foreach items="${users}" var="user">
        <tr>
            <td>${user.email}</td> 
            <td><a href="/users/edit/${user.id}">Edit</a></td> 
            <td><a href="/users/delete/${user.id}">Delete</a></td>
        </tr>
    </c:foreach>
</table>
```
````

## Default.css

`Location: Assets/Default.css`

````css
```css

.container{ margin:20px 30px 200px 20px;}
.render{margin-top:20px;}
.user-info{float:right; clear:right;}

h1{color: #2f4435;font-size:60px;font-family:Arial, Helvetica, sans-serif; margin:0px; }
h2{color: #2f4435;font-size:31px;font-family:Arial, Helvetica, sans-serif;}

.alert{border:solid 1px #000; padding:3px 7px; border-radius:3px;}

p,ul,span,a,label,
input[type="text"],
input[type="password"],
input{ color: #2f4435;font-size:19px;font-family:Arial, Helvetica, sans-serif; text-decoration: none;}

.identity{float:left;}
.hrefs{float:right}
.hrefs a{display:inline-block; margin-left:20px;}

.frm-group{
    position:relative;
}

input[type="text"],
input[type="password"]{
    font-size:21px;
    padding:15px 10px;
    border-radius: 8px;
    border:solid 2px #000;
}

label{
    font-size: 21px;
    display:block;
}

input[type="submit"],
.button{
    color:#fff;
    cursor: hand;
    font-size:21px;
    margin-top:10px;
    padding:15px 21px;
    display:inline-block;
    border-radius: 8px;
    background-color:#0f7ce2;
    border:none;
}

table{width:100%;border-collapse:collapse;border:solid 1px #e2d4d4;}
table th,
table td{padding:10px;font-size:19px;font-family:Arial, Helvetica, sans-serif; border:solid 1px #e2d4d4;}

pre{
    color:#fff;
    background:#000;
    padding:10px 12px;
}

.thankyou{
    margin-bottom:300px;
}

.clear{clear:both;}
```
````



