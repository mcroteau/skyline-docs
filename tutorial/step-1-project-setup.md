---
description: Data Driven Systems Setup
---

# Step 1 : Project Setup

What do you need to get started?&#x20;

* .Net 7
* Visual Studio Code
* Skyline

We assume you have .Net installed and can run the following command:&#x20;

`$ dotnet`&#x20;

and see something like the following:

<figure><img src="../.gitbook/assets/dotnet.png" alt=""><figcaption></figcaption></figure>

Now we will create our project structure, meaning all of the directories and build file. Go ahead and create all of the following directories.

You are going to need a Model, Repo, and Controller folders.

<figure><img src="../.gitbook/assets/project-structure-envato.png" alt=""><figcaption></figcaption></figure>

&#x20;&#x20;

We will create all of the necessary class files before we do, let's create our **Envato.csproj** build file and add Skyline to our project.

## Envato.csproj

The most important parts to this: the Persistent.Launcher \<StartupObject> reference to the Skyline.dll which you downloaded and the **SQLlite** definition.

````xml
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net7.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <WarningLevel>0</WarningLevel>
    <StartupObject>Launcher</StartupObject>
  </PropertyGroup>

  <ItemGroup>
    <Reference Include="Skyline">
      <HintPath>Libs\Skyline.dll</HintPath>
    </Reference>
    <!-- <ProjectReference Include="..\Skyline\Skyline.csproj" /> -->
    <PackageReference Include="System.Data.SQLite.Core" Version="1.0.117" />
  </ItemGroup>

</Project>
```
````

What's important is the **Skyline.dll** definition in the Libs/ directory. Now would be a good time to add it to your project, just copy and paste the **Skyline.dll** file into the Libs/ directory:







Next, we will create all of our necessary Model objects otherwise known as Domains.&#x20;
