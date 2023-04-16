# Step 3 : Controllers

We are going to continue to move quickly here, we have tacos el carbon waiting...&#x20;

We are going to create in this system three Controllers. One to handle all basic requests, another for identity logic and lastly one for all user maintenance operations. This is good stuff, once you go through the process of creating a controller that creates, saves, edits, updates and lists a model, its as simple as copy & past for new entry points or models.&#x20;

Let's start with the IndexController.cs

## IndexController.cs

`Location: Envato/Controller/IndexController.cs`

````csharp
```csharp
using System;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

namespace Controller{

    [NetworkController]
    public class IndexController{

        public IndexController(){}

        ApplicationAttributes applicationAttributes;
        
        public IndexController(ApplicationAttributes applicationAttributes){
            this.applicationAttributes = applicationAttributes;
        }
    
        [Layout(file="Pages/Default.ux")]        
        [Get(route="/")]
        public String index(NetworkRequest req, ViewCache cache){

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "Pages/Index.ux";
        }

    }

}
```
````

## `IdentityController.cs`

`Location: Envato/Controller/IdentityController.cs`

````csharp
```csharp
using System;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

using Model;
using Repo;

namespace Controller{

    [NetworkController]
    public class IdentityController{

        [Bind]
        public UserRepo userRepo;

        [Bind]
        public RoleRepo roleRepo;

        [Bind]
        public PermissionRepo permissionRepo;


        public IdentityController(){}

        ApplicationAttributes applicationAttributes;
        
        public IdentityController(ApplicationAttributes applicationAttributes){
            this.applicationAttributes = applicationAttributes;
        }

        [Layout(file="Pages/Default.ux")]        
        [Get(route="/signin")]
        public String signin(NetworkRequest req, ViewCache cache){           
            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);
            return "Pages/Signin.ux";
        }

        [Layout(file="Pages/Default.ux")]        
        [Get(route="/signup")]
        public String signup(NetworkRequest req, ViewCache cache){           
            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);
            return "Pages/Signup.ux";
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
                return "redirect:/";
            }
            cache.set("message", "please try again.");
            return "redirect:/signin";
        }

        [Get(route="/signout")]
        public String signout(NetworkRequest req, NetworkResponse resp, SecurityManager manager){
            manager.signout(req, resp);
            return "redirect:/signin";
        }

        [Post(route="/register")]
        public String register(NetworkRequest req, NetworkResponse resp, SecurityManager manager, ViewCache cache){            
            String email = req.getValue("email");
            String password = req.getValue("password");
            
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

            cache.set("message", "successfully registered.");
            return "redirect:/signin";
        }
    }
}
```
````

## `UserController.cs`

`Location: Envato/Controller/UserController.cs`

````csharp
```csharp
using System;

using System.Collections;

using Skyline.Model;
using Skyline.Security;
using Skyline.Annotation;

using Model;
using Repo;

namespace Controller{

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

        [Layout(file="Pages/Default.ux")]        
        [Get(route="/users")]
        public String index(NetworkRequest req, ViewCache cache){

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            ArrayList users = userRepo.getList();
            cache.set("users", users);

            return "Pages/Users/Index.ux";
        }

        [Layout(file="Pages/Default.ux")]        
        [Get(route="/users/create")]
        public String create(NetworkRequest req, SecurityManager manager, ViewCache cache){
            
            if(!manager.isAuthenticated(req)){
                cache.set("message", "authorization required.");
                return "redirect:/signin";
            }

            String sessionuser = req.getUserCredential();
            cache.set("sessionuser", sessionuser);

            return "Pages/Users/Create.ux";
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
            return "redirect:/users";
        }
        
        [Layout(file="Pages/Default.ux")]
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

            return "Pages/Users/Edit.ux";
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



