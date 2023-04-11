# Step 3 : Controllers

We are going to continue to move quickly here, we have tacos el carbon waiting...&#x20;

We are going to create in this system three Controllers. One to handle all basic requests, another for identity logic and lastly one for all user maintenance operations. This is good stuff, once you go through the process of creating a controller that creates, saves, edits, updates and lists a model, its as simple as copy & past for new entry points or models.&#x20;

Let's start with the IndexController.cs

## IndexController.cs

`Location: src/`Persistence`/Controller/IndexController.cs`

````csharp
```csharp
using System;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

namespace Persistence.Controller{

    [NetworkController]
    public class IndexController{

        public IndexController(){}

        ApplicationAttributes applicationAttributes;
        
        public IndexController(ApplicationAttributes applicationAttributes){
            this.applicationAttributes = applicationAttributes;
        }
    
        [Layout(file="pages/Default.asp")]        
        [Get(route="/")]
        public String index(NetworkRequest req, ViewCache cache){

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "pages/Index.asp";
        }

        [Layout(file="pages/Default.asp")]        
        [Get(route="/secured")]
        public String secured(NetworkRequest req, ViewCache cache){

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "pages/Secured.asp";
        }
    }
}
```
````

## `IdentityController.cs`

`Location: src/`Persistence`/Controller/IdentityController.cs`

````csharp
```csharp
using System;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

namespace Persistence.Controller{

    [NetworkController]
    public class IdentityController{

        public IdentityController(){}

        ApplicationAttributes applicationAttributes;
        
        public IdentityController(ApplicationAttributes applicationAttributes){
            this.applicationAttributes = applicationAttributes;
        }

        [Layout(file="pages/Default.asp")]        
        [Get(route="/signin")]
        public String signin(NetworkRequest req, ViewCache cache){           
            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "pages/Signin.asp";
        }

        [Post(route="/signin")]
        public String signin(NetworkRequest req, 
                            NetworkResponse resp, 
                            SecurityManager manager, 
                            ViewCache cache){            
            String email = req.getValue("email");
            String password = req.getValue("password");
            
            manager.signout(req, resp);

            if(manager.signin(email, password, req, resp)){
                return "redirect:/secured";
            }
            cache.set("message", "fail.");
            return "redirect:/signin";
        }

        [Get(route="/signout")]
        public String signout(NetworkRequest req, NetworkResponse resp, SecurityManager manager){
            manager.signout(req, resp);
            return "redirect:/signin";
        }
    }
}
```
````

## `UserController.cs`

`Location: src/`Persistence`/Controller/UserController.cs`

````csharp
```csharp
using System;

using System.Collections;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

using Persistence.Model;
using Persistence.Repo;

namespace Persistence.Controller{

    [NetworkController]
    public class UserController{

        [Bind]
        public UserRepo userRepo;

        [Bind]
        public RoleRepo roleRepo;

        [Bind]
        public PermissionRepo permissionRepo;

        ApplicationAttributes applicationAttributes;
        
        public UserController(){}

        public UserController(ApplicationAttributes applicationAttributes){
            this.applicationAttributes = applicationAttributes;
        }

        [Layout(file="pages/Default.asp")]        
        [Get(route="/users")]
        public String index(NetworkRequest req, ViewCache cache){

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            ArrayList users = userRepo.getList();
            cache.set("users", users);

            return "pages/Users/Index.asp";
        }

        [Layout(file="pages/Default.asp")]        
        [Get(route="/users/create")]
        public String create(NetworkRequest req, SecurityManager manager, ViewCache cache){
            
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "pages/Users/Create.asp";
        }

        [Post(route="/users/save")]
        public String save(NetworkRequest req, NetworkResponse resp, SecurityManager manager, ViewCache cache){            
            String email = req.getValue("email");
            String password = req.getValue("password");
        
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            if(!manager.hasRole("super-role", req)){
                cache.set("message", "authorization required.");
                return "redirect:/";
            }
            
            User user = new User();
            user.setEmail(email);
            user.setPassword(password);
            long id = userRepo.save(user);

            UserRole userRole = new UserRole();
            userRole.setUserId(Convert.ToInt32(id));
            userRole.setRoleId(1);
            roleRepo.save(userRole);

            Permission permission = new Permission();
            permission.setUserId(Convert.ToInt32(id));
            permission.setPermission("users:maintenance:" + id);
            permissionRepo.save(permission);

            cache.set("message", "success.");
            return "redirect:/users/edit/" + id;
        }
        
        [Layout(file="pages/Default.asp")]
        [Get(route="/users/edit/{id}")]
        public String edit([Variable] Int32 id,
                            NetworkRequest req, 
                            NetworkResponse resp, 
                            SecurityManager manager, 
                            ViewCache cache){
                                     
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            String permission = "users:maintentance:" + id;
            if(!manager.hasRole("super-role", req) && 
                    !manager.hasPermission(permission, req)){
                cache.set("message", "authorization required.");
                return "redirect:/";
            }
            
            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            User user = userRepo.getId(id);
            cache.set("user", user);

            return "pages/Users/Edit.asp";
        }

        [Post(route="/users/update/{id}")]
        public String update([Variable] Int32 id,
                            NetworkRequest req, 
                            NetworkResponse resp, 
                            SecurityManager manager, 
                            ViewCache cache){
                                     
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            String permission = "users:maintentance:" + id;
            if(!manager.hasRole("super-role", req) && 
                    !manager.hasPermission(permission, req)){
                cache.set("message", "authorization required.");
                return "redirect:/";
            }
            
            String email = req.getValue("email");
            String password = req.getValue("password");

            User user = userRepo.getId(id);
            user.setEmail(email);
            user.setPassword(password);

            userRepo.update(user);

            cache.set("message", "success.");
            return "redirect:/users/edit/" + id;
        }

        [Get(route="/users/delete/{id}")]
        public String delete([Variable] Int32 id,
                            NetworkRequest req, 
                            NetworkResponse resp, 
                            SecurityManager manager, 
                            ViewCache cache){
                                     
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            String permission = "users:maintentance:" + id;
            if(!manager.hasRole("super-role", req) && 
                    !manager.hasPermission(permission, req)){
                cache.set("message", "authorization required.");
                return "redirect:/";
            }
            
            userRepo.delete(id);
            
            cache.set("message", "success.");
            return "redirect:/users";
        }
    }
}
```
````



