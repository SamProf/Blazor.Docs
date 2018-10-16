---
title: Маршрутизация в Blazor
author: guardrex
description: Узнайте, как маршрутизировать запросы в клиентском приложении Blazor и о компоненте NavLink.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 05/01/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/routing
---
# Маршрутизация в Blazor

От [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Узнайте, как маршрутизировать запросы в клиентском приложении Blazor и о компоненте NavLink.

[Просмотр или загрузка примера кода](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) ([как скачать](xref:client-side/blazor/index#просмотр-и-загрузка-примеров)). Смотрите [Начать](xref:client-side/blazor/get-started) для предпосылок.

## Шаблон маршрутов

Компонент **&lt;Router&gt;** обеспечивает маршрутизацию, а шаблон маршрута предоставляется каждому доступному компоненту. Компонент **&lt;Router&gt;** отображается в файле *App.cshtml*:

```cshtml
<Router AppAssembly=typeof(Program).Assembly />
```

Когда файл *\*.cshtml* с директивой `@page` компилируется, генерируемому классу присваивается атрибут [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) с указанием шаблона маршрута. Во время выполнения маршрутизатор ищет классы компонентов с атрибутом `RouteAttribute` и отображает, какой компонент имеет шаблон маршрута, который соответствует запрашиваемому URL.

К компоненту может быть применено несколько шаблонов маршрута. В [примере приложения](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/), компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.

*Pages/BlazorRoute.cshtml*:

[!code-cshtml[](common/samples/2.x/BlazorSample/Pages/BlazorRoute.cshtml?start=1&end=4)]

> [!IMPORTANT]
> To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/" />`). For more information, see [Host and deploy: App base path](xref:client-side/blazor/host-and-deploy/index#базовый-путь-приложения).

## Параметры маршрута

Клиентский маршрутизатор Blazor использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).

*Pages/RouteParameter.cshtml*:

[!code-cshtml[](common/samples/2.x/BlazorSample/Pages/RouteParameter.cshtml?start=1&end=8)]

Дополнительные параметры пока не поддерживаются, поэтому в приведенном выше примере применяются две директивы `@page`. Первая разрешает навигацию к компоненту без параметра. Вторая директива `@page` принимает параметр `{text}` в маршруте и присваивает значение свойству `Text`.

## Route constraints

A route constraint enforces type matching on a route segment to a Blazor component.

In the following example, the route to the Users component only matches if:

* An `Id` route segment is present on the request URL.
* The `Id` segment is an integer (`int`).

```cshtml
@page "/Users/{Id:int}"

<h1>The user Id is @Id!</h1>

@functions {
    [Parameter]
    private int Id { get; set; }
}
```

The route constraints shown in the following table are available for use. For the route constraints that match with the invariant culture, see the warning below the table for more information.

| Constraint | Example           | Example Matches                                                                  | Invariant<br>culture<br>matching |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |

> [!WARNING]
> Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture. These constraints assume that the URL is non-localizable.

## Компонент NavLink

Используйте компонент NavLink вместо HTML **\<a>** элементов при создании навигационных ссылок. Компонент NavLink ведет себя как элемент **\<a>**, за исключением того, что он переключает класс CSS `active` на основе того, соответствует ли его` href` текущему URL-адресу. Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых навигационных ссылок.

Компонент NavMenu в [примере приложения](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) создаёт [Bootstrap](https://getbootstrap.com/docs/) навигационную панель, которая демонстрирует, как использовать компоненты NavLink. Следующая разметка показывает первые два NavLinks в файле *Shared/NavMenu.cshtml*.

[!code-cshtml[](common/samples/2.x/BlazorSample/Shared/NavMenu.cshtml?start=13&end=24&highlight=4-6,9-11)]

Есть две [NavLinkMatch](/api/Microsoft.AspNetCore.Blazor.Routing.NavLinkMatch.html) опции:

* `NavLinkMatch.All` &ndash; Указывает, что NavLink должен быть активным, когда он совпадает со всем текущим URL-адресом.
* `NavLinkMatch.Prefix` &ndash; Указывает, что NavLink должен быть активным, если он соответствует любому префиксу текущего URL-адреса.

В предыдущем примере NavLink на главной странице (`href=""`) соответствует всем URL-адресам и всегда получает класс CSS `active`. Второй NavLink получает активный класс, только когда пользователь посещает компонент BlazorRoute (`href="BlazorRoute").
