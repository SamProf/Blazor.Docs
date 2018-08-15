---
title: Внедрение зависимостей в Blazor
author: rstropek
description: Посмотрите, как приложения Blazor могут использовать встроенные сервисы, введя их в состав компонентов.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/dependency-injection
---
# Внедрение зависимостей в Blazor

От [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Blazor имеет встроенное [внедрение зависимостей (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Приложения могут использовать встроенные службы, вставляя их в компоненты. Приложения также могут определять пользовательские сервисы и предоставлять их через DI.

## Что такое внедрение зависимостей?

DI - это метод доступа к службам, настроенным в центральном расположении. Это может быть полезно для:

* Разделите один экземпляр класса службы между многими компонентам (известен как *singleton*).
* Разъедините компоненты из конкретных классов оставив только опорные абстракции. Например, интерфейс `IDataAccess` реализуется конкретным классом `DataAccess`. Когда компонент использует DI для получения реализации `IDataAccess`, компонент не связан с конкретным типом. Реализация может быть заменена, например, до реализации в модульных тестах.

Система DI Blazor отвечает за предоставление экземпляров услуг компонентам. DI также рекурсивно решает зависимости, чтобы сами сервисы могли зависеть от дальнейших услуг. DI настраивается во время запуска приложения. Пример показан ниже в этом разделе.

## Добавление сервисов к DI

После создания нового приложения, исследуйте метод `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Метод `ConfigureServices` передаётся [IServiceCollection](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection), который представляет собой список объектов дескриптора службы ([ServiceDescriptor](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor)). Сервисы добавляются путем предоставления дескрипторов служб в коллекцию услуг. Следующий пример кода демонстрирует концепцию:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Сервисы могут быть настроены со следующими сроками жизни:

| Метод      | Описание |
| ----------- | ----------- |
| [Singleton](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.singleton#Microsoft_Extensions_DependencyInjection_ServiceDescriptor_Singleton__1_System_Func_System_IServiceProvider___0__) | DI создаёт *один экземпляр* сервиса. Все компоненты, требующие этот сервис, получают ссылку на этот экземпляр. |
| [Transient](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.transient) |Всякий раз, когда компонент требует эту услугу, он получает *новый экземпляр* сервиса. |
| [Scoped](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.scoped) | Blazor в настоящее время не имеет концепции областей DI. `Scoped` ведет себя так же как и `Singleton`. Поэтому лучше использовать `Singleton` и изберать `Scoped`. |

DI Blazor основана на DI в ASP.NET Core. Для получения дополнительной информации смотрите [Внедрение зависимости в in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

## Сервисы по умолчанию

Blazor предоставляет службы по умолчанию, которые автоматически добавляются в коллекцию услуг приложения. В следующей таблице показан список услуг по умолчанию, предоставляемых в Blazor в настоящее время [BrowserServiceProvider](/api/Microsoft.AspNetCore.Blazor.Browser.Services.BrowserServiceProvider.html).

| Метод       | Описание |
| ------------ | ----------- |
| [IUriHelper](/api/Microsoft.AspNetCore.Blazor.Services.IUriHelper.html) | Помощники для работы с URI и навигационным состоянием (singleton). |
| [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) | Предоставляет методы отправки HTTP-запросов и получения ответов HTTP от ресурса, идентифицированного URI (singleton). Обратите внимание, что этот экземпляр `HttpClient` использует браузер для обработки HTTP-трафика в фоновом режиме. [HttpClient.BaseAddress](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.baseaddress) автоматически устанавливается на базовый URI-префикс приложения. |

Обратите внимание, что вместо пользовательского `BrowserServiceProvider`, который добавляется шаблоном по умолчанию, можно использовать поставщик настраиваемых услуг. Пользовательский поставщик услуг автоматически не предоставляет службы по умолчанию, перечисленные в таблице. Эти службы должны быть добавлены к новому поставщику услуг явно.

## Запрос сервиса в компоненте

После того как сервисы добавляются в коллекцию служб, их можно внедрять в шаблоны Razor компонентов, используя директиву Razor `@inject`. `@inject` имеет два параметра:

* Имя типа: тип услуги для инъекций.
* Имя свойства: имя свойства, получающего инъецированного приложения. Обратите внимание: свойство не требует ручного создания. Компилятор создает свойство.

Несколько операторов `@inject` могут использоваться для внедрения различных сервисов.

В следующем примере показано, как использовать `@inject`. Служба, реализующая `Services.IDataAccess`, вводится в свойство компонента `DataRepository`. Обратите внимание, что код использует только абстракцию `IDataAccess`:

```csharp
@page "/customer-list"
@using Services
@inject IDataAccess DataRepository

<ul>
    @if (Customers != null)
    {
        @foreach (var customer in Customers)
        {
            <li>@customer.FirstName @customer.LastName</li>
        }
    }
</ul>

@functions {
    private IReadOnlyList<Customer> Customers;

    protected override async Task OnInitAsync()
    {
        // The property DataRepository received an implementation
        // of IDataAccess through dependency injection. Use 
        // DataRepository to obtain data from the server.
        Customers = await DataRepository.GetAllCustomersAsync();
    }
}
```

Созданное свойство (`DataRepository`) помечено атрибутом [InjectAttribute](/api/Microsoft.AspNetCore.Blazor.Components.InjectAttribute.html). Как правило, этот атрибут не используется напрямую. Если для компонентов требуется базовый класс, а требуемые свойства также необходимы для базового класса, `InjectAttribute` можно добавить вручную:

```csharp
public class ComponentBase : BlazorComponent
{
    // Blazor's dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

В компонентах, полученных из базового класса, директива `@inject` не требуется. `InjectAttribute` базового класса достаточно:

```csharp
@page "/demo"
@inherits ComponentBase

<h1>...</h1>
...
```

## Внедрение зависимостей в сервисах

Для комплексных сервисов могут потребоваться дополнительные сервисы. В предыдущем примере `DataAccess` может потребовать сервис Blazor по умолчанию `HttpClient`. `@inject` или` InjectAttribute` не могут использоваться в сервисах. Вместо этого следует использовать *конструкторскую инъекцию*. Необходимые сервисы добавляются путем добавления параметров в конструктор службы. Когда инъекция зависимостей создает сервис, она распознает сервисы, которые она требует в конструкторе, и соответственно предоставляет их.

Следующий пример кода демонстрирует концепцию:

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service offered by Blazor.
    public DataAccess(HttpClient client)
    {
        ...
    }
    ...
}
```

Обратите внимание на следующие предпосылки для инъекций конструктора:

* Должен быть один конструктор, аргументы которого могут быть выполнены путем инъекции зависимостей. Обратите внимание, что дополнительные параметры, не указанные в DI, разрешены, если для них указаны значения по умолчанию.
* Применимый конструктор должен быть *public*.
* Должен быть только один применимый конструктор. В случае двусмысленности DI генерирует исключение.

## Дополнительные ресурсы

* [Внедрение зависимостей в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
