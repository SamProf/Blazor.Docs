---
title: Компоненты Blazor
author: guardrex
description: Узнайте, как создавать и использовать компоненты Blazor, основные строительные блоки приложений Blazor, предоставляемые скомпилированными файлами Razor или C#.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/components/index
---
# Конпоненты Blazor

От [Luke Latham](https://github.com/guardrex) и [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

[Посмотреть или скачать примеры кода](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) ([как скачать](xref:client-side/blazor/index#view-and-download-samples)). Смотрите также [Начать](xref:client-side/blazor/get-started), для начала работы с Blazor.

Приложения Blazor создаются с использованием *компонентов*. Компонент представляет собой автономный фрагмент пользовательского интерфейса (UI), такой как страница, диалог или форма. Компонент включает как разметку HTML для рендеринга, так и логику обработки, необходимую для ввода данных или реагирования на события пользовательского интерфейса. Компоненты являются гибкими и легкими, и они могут быть вложенными, повторно использованными и совместно использоваться несколькими проектами.

## Классы компонентов

Компоненты Blazor обычно определяются в *\*.cshtml* файлах и используют комбинацию C# и HTML разметки. Внешний вид компонента определяется с помощью использования HTML. Динамическая логика визуализации (например, циклы, условия, выражения) добавляется с использованием встроенного синтаксиса C# [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). Когда приложение Blazor скомпилировано, HTML-разметка и логика отображения C# преобразуются в класс компонентов. Имя сгенерированного класса соответствует имени файла.

Члены класса компонента определены в блоке `@functions` (допускается больше одного блока`@functions`). В блоке`@functions`, компоненты состояния (свойства, поля) задаются вместе с методами обработки событий или для определения другой логики компонента.

Члены компонента затем могут использоваться как часть логики рендеринга компонента с использованием выражений C#, которые начинаются с `@`. Например, поле C# определяется префиксом `@` к имени поля. Следующий пример демонстрирует, как это работает:

* `_headingFontStyle` к значению свойства CSS для `font-style`.
* `_headingText` к содержанию элемента `<h1>`.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

После первоначальной визуализации компонента компонент регенерирует дерево рендеринга в ответ на события. Затем Blazor сравнивает новое дерево рендеринга с предыдущим и применяет любые изменения в Document Object Model (DOM) браузера.

## Использование компонентов

Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса элемента HTML. Разметка для использования компонента выглядит как тег HTML, где имя тега является типом компонента.

Следующая разметка отображает пример `HeadingComponent` (*HeadingComponent.cshtml*):

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/Index.cshtml?start=11&end=11)]

## Параметры компонента

Компоненты могут иметь *параметры компонента*, которые определяются с помощью *непубличных* свойств класса компонентов, помеченный аттрибутом `[Parameter]`. Используйте атрибуты для указания аргументов компонента в разметке.

В следующем примере, на странице `ParentComponent` задаётся значение свойства `Title` компонента `ChildComponent`:

*ParentComponent.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/ParentComponent.cshtml?start=1&end=7&highlight=5)]

*ChildComponent.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## Дочерний контент

Компоненты могут устанавливать контент в другом компоненте. Назначающий компонент предоставляет контент между тегами, которые определяет принимающий компонент. Намример, компонент `ParentComponent` может предоставлять контент, который должен быть в компоненте `ChildComponent` путем размещения содержимого внутри тега **\<ChildComponent>**.

*ParentComponent.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/ParentComponent.cshtml?start=1&end=7&highlight=6)]

Дочерний компонент содержит свойство `ChildContent` которое представляет [RenderFragment](/api/Microsoft.AspNetCore.Blazor.RenderFragment.html). Значение `ChildContent` позиционируется в разметке дочернего компонента, где должен отображаться контент. В следующем примере, значение `ChildContent` передаётся из родительского компонента и отображается внутри панели Bootstrap `panel-body`.

*ChildComponent.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> Свойство получающее контент `RenderFragment` должно быть названо `ChildContent` условно.

## Связывание данных

Связывание даннх компонента и элементов DOM происходит с помощью аттрибута `bind`. Следующий пример демонстрирует связывание свойства `ItalicsCheck` и флажок (check box):

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" bind="@_italicsCheck" />
```

Когда флажок установлен или убран, значение свойства меняется на `true` или `false` соответственно.

Флажок обновляется в пользовательском интерфейсе только тогда, когда компонент отображается, а не в ответ на изменение значения свойства. Поскольку компоненты отображают себя после выполнения кода обработчика событий, обновления свойств немедленно отражаются в пользовательском интерфейсе.

Используйте `bind` со свойством `CurrentValue` (`<input bind="@CurrentValue" />`) это будет эквивалентно следующему:

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIValueEventArgs __e) => CurrentValue = __e.Value)" />
```

Когда компонент отображает `value` элемента ввода из свойства `CurrentValue`. Когда пользователь вводит данные в текстовое поле запускается `onchange` и свойство `CurrentValue` устанавливается на изменённое значение. На самом деле генерация кода немного сложнее, потому что `bind` имеет дело с несколькими случаями, когда выполняются преобразования типов. В принципе, `bind` связывает текущее значение выражения с атрибутом` value` и обрабатывает изменения с помощью зарегистрированного обработчика.

**Форматирование строк**

Связывание данных работает с [DateTime](https://docs.microsoft.com/dotnet/api/system.datetime) форматом строк (но не для других, таких как формат валют или чисел):

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Аттрибут `format-value` определяет формат даты для задания `value` элемента `input`. Формат также используется для анализа значения, когда происходит событие `onchange`.

**Параметры компонента**

Связывание также распознает параметры компонента, где `bind-{property}` может связывать значение свойства между компонентами.

Следующий компонет использует компонент `ChildComponent` и связывает параметр `ParentYear` из родительского компонента с параметром `Year` из дочернего компонента:

Родительский компонент:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">Change Year to 1986</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Дочерний компонент:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

Параметр `Year` является связующим, потому что он имеет событие `YearChanged`, которое соответствует типу параметра `Year`.

Загрузка компонента `ParentComponent` производит следующую разметку:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Если значение свойства `ParentYear` изменяется путем выбора кнопки в `ParentComponent`, свойство `Year` компонента `ChildComponent` обновляется. Новое значение `Year` отображается в пользовательском интерфейсе когда `ParentComponent` перезагрузится:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

## Обработка событий

Blazor provides event handling features. For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Blazor treats the attribute's value as an event handler. The attribute's name always starts with `on`.

The following code calls the `UpdateHeading` method when the button is selected in the UI:

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

The following code calls the `CheckboxChanged` method when the check box is changed in the UI:

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

For some events, event-specific event argument types are permitted. If access to one of these event types isn't necessary, it isn't required in the method call.

The list of supported event arguments is:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

Lambda expressions can also be used:

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

## Lifecycle methods

`OnInitAsync` and `OnInit` execute code after the component has been initialized. To perform an asynchronous operation, use `OnInitAsync` and use the `await` keyword:

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

For a synchronous operation, use `OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties. These methods are executed after `OnInit` during component initialization.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` and `OnAfterRender` are called each time after a component has finished rendering. Element and component references are populated at this point. Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` can be overridden to execute code before parameters are set:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required. For example, the incoming parameters aren't required to be assigned to the properties on the class.

`ShouldRender` can be overridden to suppress refreshing of the UI. If the implementation returns `true`, the UI is refreshed. Even if `ShouldRender` is overridden, the component is always initially rendered.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## Component disposal with IDisposable

If a component implements [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable), the [Dispose method](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI. The following component uses `@implements IDisposable` and the `Dispose` method:

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## Routing

Routing in Blazor is achieved by providing a route template to each accessible component in the app.

When a *\*.cshtml* file with an `@page` directive is compiled, the generated class is given a [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) specifying the route template. At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.

Multiple route templates can be applied to a component. The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/BlazorRoute.cshtml?start=1&end=4)]

## Route parameters

Blazor components can receive route parameters from the route template provided in the `@page` directive. The Blazor client-side router uses route parameters to populate the corresponding component parameters.

*RouteParameter.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/RouteParameter.cshtml?start=1&end=9)]

Optional parameters aren't supported, so two `@page` directives are applied in the example above. The first permits navigation to the component without a parameter. The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.

## Base class inheritance for a "code-behind" experience

Blazor component files (*\*.cshtml*) mix HTML markup and C# processing code in the same file. The `@inherits` directive can be used to provide Blazor with a "code-behind" experience that separates component markup from processing code.

The [sample app](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.

*BlazorRocks.cshtml*:

[!code-cshtml[](../common/samples/2.x/BlazorSample/Pages/BlazorRocks.cshtml?start=1&end=8)]

*BlazorRocksBase.cs*:

[!code-csharp[](../common/samples/2.x/BlazorSample/Pages/BlazorRocksBase.cs)]

The base class should derive from [BlazorComponent](/api/Microsoft.AspNetCore.Blazor.Components.BlazorComponent.html).

## Razor support

**Razor directives**

Razor directives active with Blazor apps are shown in the following table.

| Directive | Description |
| --------- | ----------- |
| [@functions](https://docs.microsoft.com/aspnet/core/mvc/views/razor#functions) | Adds a C# code block to a component. |
| `@implements` | Implements an interface for the generated component class. |
| [@inherits](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inherits) | Provides full control of the class that the component inherits. |
| [@inject](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inject) | Enables service injection from the [service container](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). For more information, see [Dependency injection into views](https://docs.microsoft.com/aspnet/core/mvc/views/dependency-injection). |
| `@layout` | Specifies a layout component. Layout components are used to avoid code duplication and inconsistency. |
| [@page](https://docs.microsoft.com/aspnet/core/mvc/razor-pages#razor-pages) | Specifies that the component should handle requests directly. The `@page` directive can be specified with a route and optional parameters. Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file. For more information, see [Routing](xref:client-side/blazor/routing). |
| [@using](https://docs.microsoft.com/aspnet/core/mvc/views/razor#using) | Adds the C# `using` directive to the generated component class. |
| [@addTagHelper](https://docs.microsoft.com/aspnet/core/mvc/views/razor#tag-helpers) | Use `@addTagHelper` to use a component in a different assembly than the app's assembly. |

**Conditional attributes**

Blazor conditionally renders attributes based on the .NET value. If the value is `false` or `null`, Blazor won't render the attribute. If the value is `true`, the attribute is rendered minimized.

In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup.

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

If `IsCompleted` is `true`, the check box is rendered as:

```html
<input type="checkbox" checked />
```

If `IsCompleted` is `false`, the check box is rendered as:

```html
<input type="checkbox" />
```

**Additional information on Razor**

For more information on Razor, see the [Razor syntax reference](https://docs.microsoft.com/aspnet/core/mvc/views/razor). Note that not all of the features of Razor are available in Blazor at this time.

## Raw HTML

Blazor normally renders strings using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text. To render raw HTML, wrap the HTML content in a `MarkupString` value, which is then parsed as HTML or SVG and inserted into the DOM.

> [!WARNING]
> Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!

The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:

```html
@((MarkupString)myMarkup)

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```
