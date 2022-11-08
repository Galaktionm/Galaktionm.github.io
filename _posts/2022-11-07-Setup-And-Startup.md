---
layout: post
title: "Setup And Startup"
date: 2022-11-07
categories: BackendFrameworks
---


Spring and ASP.NET Core have very different architectures, therefore their setup process and startup configurations differ as well. In this post
I will try to describe both of them and point out the features that I like and dislike. 

For starters, most of ASP.NET Core’s setup and configuration happens in Program.cs file and appsettings.json file, while for spring we have completely different picture. There is not a single, main class responsible for configuration. Much like .Net’s appsettings.json file, you can define database connection strings and other settings in application.yaml/application.properties file, however, when it comes to classes that are used for configuration, you often have many separate classes responsible for configuring different parts of the program. 

##### To discuss each of them separately, let’s start with ASP.NET Core. 
Before releasing .NET 6, application startup was defined in two classes: Program.cs and Startup.cs. Program class had the main method, and the host builder method, which defined the startup class that was to be used-usually Startup.cs, which was used to configure application builder and services. 
Starting from .NET 6, all of those code in contained within single Program.cs class, which now does the job of both of those classes. At first, someone might think that writing all those code in a single file makes it bloated and overcomplicated, however that is not the case. 
Not only it is convenient to have all the configuration in a single file, it is also very intuitive. Not only that, when I tried to do the startup configuration the “old” way-by having both Program.cs and Startup.cs files, I find it much less maintainable, error-prone and counter-productive. Not to say that it is not possible or a “bad” idea, after all, since .NET 6 was released just a year ago, many applications still use both files.   However, I found having a single Program.cs file much better.
It is very easy and intuitive to understand the code that way: You create a WebApplicationBuilder, add services, build the application, optionally create service scope, and tell application which services to use. You can separate the code with new lines to make it more readable. 

##### Spring’s component-based structure
Spring application has different structure. Configuration is scattered across multiple classes in multiple packages. Often, you will find that the class responsible for running the application contains only few lines of code and looks like this:

![SpringApplication](/assets/SpringAppStartup.png)

Spring offers a container called Spring application context, which manages application components, also called beans, for us. In order to define a class as a component, we have to use @Component annotation. However, spring also provides specialized annotations such as @Controller, @Service and @Repository. They are all derived from @Component and provide the same functionality: They allow the class to be scanned as a component and used in dependency injection. (More about that in the next  paragraph) Although technically possible to use only @Component, it is a better idea and established practice to use specialized annotations. 

In the above picture, the class is annotated with @SpringBootApplication. In the official documentation page about the annotation, we read that @SpringBootApplication “Indicates a configuration class that declares one or more @Bean methods and also triggers auto-configuration and component scanning.”
This means that the classes that have @Component or it’s specialized annotation, will be candidates to be scanned for auto-configuration and dependency injection. Component scanning occurs in the same package or sub-packages, therefore it is important to structure the project correctly.
For simplicity, let’s say that @SpringBootApplication class is contained in the package called “main”. In that case, I often find it helpful and intuitive to structure the project like this:

![SpringStructure](/assets/SpringPackages.png)

Otherwise, we have to explicitly use @ComponentScan annotation to tell application context which packages we would like to be scanned. 
As of configuration, we write code in the appropriate class annotated with @Configuration. Database configuration happens in one class, Authorization and Authentication configuration in another, and so on. I won’t be writing about concrete cases here. Please check other articles for that. 

##### CommandLineRunner and Service Scope

Often, especially in development phases, we need some methods to run when the application starts. Personally, most of the time, I need to populate database with dummy data, either to find out if the entity relationships are configured properly or to use the data to test other functionality. Either way, I need to have the data ready when the application starts. Both frameworks give you the ability to do just that, in  different ways.

Spring offers CommandLineRunner method (Which has to be annotated with @Bean annotation), that will execute statements contained inside it as soon as the application starts. In order for it to work, the method has to be contained inside a class that is annotated with @Component or it’s derived annotations. There is one detail, however: If you need to use the services of the application, often times you have to inject them in the class only for the CommandLineRunner method to use them. 

In ASP.NET Core, there is no need to use dependency injection for this. Since most of the time this Is done in Program.cs file, application and application builder variables are already defined, all you have to do is create a scope, which returns instance of IServiceScope interface. The interface can be used to get a ServiceProvider, from which we can get the service that we need. For example:

<code>
using(var scope = app.Services.CreateScope())
{
    var dbContext = scope.ServiceProvider.GetService<DatabaseContext>();
}
 </code>



#### Conclusion
In this post I tried to compare startup and configuration process of Spring and ASP.NET Core applications. Although they differ in a lot of ways, one can not say that one is better that the other. In Spring, it is very convenient and handy to have explicitly defined configuration classes, you can check and optionally change the code anytime. Although, In ASP.NET Core, if you understand what you are doing and what each service’s responsibility is, it isn’t much of a drawback to not have classes and their functionality explicitly defined, and often keeps program files concise. Every framework has different structure, it’s better to define their value by their concrete implementations of persistence, user managing, MVC and API capabilities for example. Both Spring and ASP.NET Core are very powerful frameworks that offer wide range of capabilities. Thanks for reading, please check out other articles too : - )


