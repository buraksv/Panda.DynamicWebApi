# Panda.DynamicWebApi

`Panda.DynamicWebApi`  is a component that dynamically generates WebApi. The generated API conforms to the Restful style and is inspired by ABP. It can generate WebApi based on qualified classes. The logic is directly called by the MVC framework. There is no performance problem. It is perfectly compatible with Swagger to build the API documentation. There is no difference compared with manually writing the Controller.

Application Scenario: The application logic layer in the DDD architecture, which can be used to directly generate WebApi without using the Controller.

[![Latest version](https://img.shields.io/nuget/v/Panda.DynamicWebApi.svg)](https://www.nuget.org/packages/Panda.DynamicWebApi/)

## 1.Quick Start

（1）Create a new ASP.NET Core WebApi (or MVC) project

（2）Install Package

````shell
Install-Package Panda.DynamicWebApi
````

（3）Create a class named `AppleAppService`, implement the `IDynamicWebApi` interface, and add the attribute `[DynamicWebApi]`

````csharp
[DynamicWebApi]
public class AppleAppService: IDynamicWebApi
{
    private static readonly Dictionary<int,string> Apples=new Dictionary<int, string>()
    {
        [1]="Big Apple",
        [2]="Small Apple"
    };

    /// <summary>
    /// Get An Apple.
    /// </summary>
    /// <param name="id"></param>
    /// <returns></returns>
    [HttpGet("{id:int}")]
    public string Get(int id)
    {
        if (Apples.ContainsKey(id))
        {
            return Apples[id];
        }
        else
        {
            return "No Apple!";
        }
    }

    /// <summary>
    /// Get All Apple.
    /// </summary>
    /// <returns></returns>
    public IEnumerable<string> Get()
    {
        return Apples.Values;
    }

    public void Update(UpdateAppleDto dto)
    {
        if (Apples.ContainsKey(dto.Id))
        {
            Apples[dto.Id] =dto.Name;
        }
    }

    /// <summary>
    /// Delete Apple
    /// </summary>
    /// <param name="id">Apple Id</param>
    [HttpDelete("{id:int}")]
    public void Delete(int id)
    {
        if (Apples.ContainsKey(id))
        {
            Apples.Remove(id);
        }
    }

}
````

（4）Register DynamicWebApi in Startup

````csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDynamicWebApi();
}
````

（5）Add Swagger

（6）Run

After running the browser, visit `<your project address>/swagger/index.html` and you will see the WebApi generated for our `AppleAppService`.

![1560265120580](assets/1560265120580.png)

This quick start Demo Address: [link](/samples/Panda.DynamicWebApiSample)

## 2.Advanced

（1）There are two conditions that need to be met for a class to generate a dynamic API. One is that the class **direct** or **indirect** implements `IDynamicWebApi`, and the class **itself** or **parent abstract class** or **Implemented interface** has the property `DynamicWebApi`

（2）Adding the attribute `[NonDynamicWebApi]` allows a class or a method to not generate an API, and `[NonDynamicWebApi]` has the highest priority.

（3）The suffix of the dynamic API **class name**  that conforms to the rule is deleted. For example, our quick start `AppleAppService` will delete the AppService suffix. This rule can be dynamically configured.

（4）The API route prefix is automatically added, and the `api` prefix is added by default for all APIs.

（5）The default HTTP verb is `POST`, which can be understood as the Http Method of the API. But it can be overridden by `HttpGet/HttpPost/HttpDelete ` and other ASP.NET Core built-in attribute.

（6）You can override the default route with built-in attribute such as `HttpGet/HttpPost/HttpDelete `

（7）By default, HTTP verbs are set according to your method name. For example, the API verb generated by CreateApple or Create is `POST`. The comparison table is as follows. If you hit (ignore uppercase) the comparison table, then this part of the API name will be Omitted, such as CreateApple will become Apple, if not in the following comparison table, will use the default verb `POST`

| MethodName Start With | Http Verb |
| --------------------- | --------- |
| create                | POST      |
| add                   | POST      |
| post                  | POST      |
| get                   | GET       |
| find                  | GET       |
| fetch                 | GET       |
| query                 | GET       |
| update                | PUT       |
| put                   | PUT       |
| delete                | DELETE    |
| remove                | DELETE    |

（8）It is highly recommended that the method name use the PascalCase specification and use the verbs from the above table. Such as:

Create Apple Info-> Add/AddApple/Create/CreateApple

Update Apple Info -> Update/UpdateApple

...

（9）The `[DynamicWebApi]` attribute can be inherited, so it is forbidden to be placed on a parent class other than an abstract class or interface for the parent class to be misidentified.

## 3.Configuration

All configurations are in the object `DynamicWebApiOptions`, as explained below:

| Property | Require | 说明                                                      |
| --------------------------- | -------- | --------------------------------------------------------- |
| DefaultHttpVerb             | False  | Default：POST.                            |
| DefaultAreaName             | False | Default：Empty.                        |
| DefaultApiPrefix            | False | Default：api. Web API route prefix.     |
| RemoveControllerPostfixes          | False | Default：AppService/ApplicationService. The suffix of the class name that needs to be removed. |
| RemoveActionPostfixes     | False   | Default：Async. Method name needs to be removed from the suffix. |
| FormBodyBindingIgnoredTypes | False | Default：IFormFile。Ignore type for MVC Form Binding. |

## 4.Q&A

If you encounter problems, you can use [Issues](https://github.com/dotnetauth/Panda.DynamicWebApi/issues) to ask questions.

## 5.Reference project

> This project directly or indirectly refers to the following items

- [ABP](https://github.com/aspnetboilerplate/aspnetboilerplate)
