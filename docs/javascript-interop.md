---
title: Взаимодействие с JavaScript в Blazor
author: danroth27
description: Узнайте, как вызывать функции JavaScript из .NET и .NET методы из JavaScript.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/javascript-interop
---
# Взаимодействие с JavaScript в Blazor

От [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), и [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Приложение Blazor может вызывать функции JavaScript из .NET и .NET методы из кода JavaScript.

## Вызов функций JavaScript из методов .NET

Бывают случаи, когда в коде .NET Blazor требуется вызов функций JavaScript. Вызов JavaScript может добавлять возможности работы с браузером или функциональные возможности из библиотеки JavaScript в приложение Blazor.

Чтобы вызвать JavaScript из .NET, используйте абстракцию `IJSRuntime`, доступную из `JSRuntime.Current`. Метод `InvokeAsync<T>` на `IJSRuntime` принимает идентификатор функции JavaScript, которую вы хотите вызывать, и любое количество сериализуемых аргументов JSON. Идентификатор функции относится к глобальной области (`window`). Например, если вы хотите вызвать `window.someScope.someFunction`, идентификатором является` someScope.someFunction`. Больше нет необходимости регистрировать функцию до ее вызова. Тип возврата `T` также должен быть сериализуемым JSON.

In the sample app, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:

* `showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.
* `displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/2.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Абстракция `IJSRuntime` является асинхронной, позволяющей использовать сценарии вне процесса. Если приложение запускается в процессе и вы хотите синхронно вызывать функцию JavaScript, отключите их до `IJSInProcessRuntime` и вызовите `Invoke<T>` вместо этого. Мы рекомендуем использовать асинхронные API для обеспечения доступности библиотек во всех сценариях Blazor, на стороне клиента или на стороне сервера.

[!code-html[](./common/samples/2.x/BlazorSample/wwwroot/index.html?highlight=16)]

Don't place a script tag in a component file because the script tag can't be updated dynamically.

.NET methods interop with the JavaScript functions by calling `InvokeAsync<T>` method on `IJSRuntime`.

The sample app uses a pair of C# methods, `Prompt` and `Display`, to invoke the `showPrompt` and `displayWelcome` JavaScript functions:

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/2.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=6-8,14-16)]

The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios. If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead. We recommend that most JavaScript interop libraries use the async APIs to ensure the libraries are available in all Blazor scenarios, client-side or server-side.

The sample app includes a component to demonstrate JS interop. The component:

* Receives user input via a JS prompt.
* Returns the text to the component for processing.
* Calls a second JS function that interacts with the DOM to display a welcome message.

*Pages/JSInterop.cshtml*:

[!code-cshtml[](./common/samples/2.x/BlazorSample/Pages/JsInterop.cshtml?start=1&end=21&highlight=2-3,9-11,13,16-20)]

1. When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the `ExampleJsInterop.Prompt` method in C# code is called.
1. The `Prompt` method executes the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file.
1. The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the `Prompt` method and ultimately back to the component. The component stores the user's name in a local variable, `name`.
1. The string stored in `name` is incorporated into a welcome message, which is passed to a second C# method, `ExampleJsInterop.Display`.
1. `Display` calls a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.

## Capture references to elements

Некоторые сценарии [взаимодействия с JavaScript](xref:client-side/blazor/javascript-interop) требуют ссылки на элементы HTML. Например, для библиотеки UI может потребоваться ссылка на элемент для инициализации, или вам может потребоваться вызвать командные API-интерфейсы для элемента, такие как `focus` или `play`.

Вы можете захватывать ссылки на элементы HTML в компоненте, добавляя атрибут `ref` к элементу HTML, а затем определяя поле типа `ElementRef`, имя которого соответствует значению атрибута `ref`.

В следующем примере показана запись ссылки на элемент ввода имени пользователя:

```csharp
<input ref="username" ... />

@functions {
    ElementRef username;
}
```

> [!NOTE]
> **Не используйте** ссылки на захваченные элементы как способ заполнения DOM. Это может повлиять на модель декларативного рендеринга Blazor.

Что касается кода .NET, то `ElementRef` является непрозрачным дескриптором. Всё что вы можете сделать с ней, - это передать код JavaScript через JavaScript. Когда вы это сделаете, код на стороне JavaScript получает экземпляр `HTMLElement`, который он может использовать с обычными API-интерфейсами DOM.

Например, следующий код определяет метод расширения .NET, который позволяет настроить фокус на элементе:

*mylib.js*:

```javascript
window.myLib = {
  focusElement : function (element) {
    element.focus();
  }
}
```

*ElementRefExtensions.cs*:

```csharp
using Microsoft.AspNetCore.Blazor;
using Microsoft.JSInterop;
using System.Threading.Tasks;

namespace MyLib
{
    public static class MyLibElementRefExtensions
    {
        public static Task Focus(this ElementRef elementRef)
        {
            return JSRuntime.Current.InvokeAsync<object>("myLib.focusElement", elementRef);
        }
    }
}
```

Теперь вы можете сфокусировать входные данные в любом из ваших компонентов:

```cshtml
@using MyLib

<input ref="username" />
<button onclick="@SetFocus">Set focus</button>

@functions {
    ElementRef username;

    void SetFocus()
    {
        username.Focus();
    }
}
```

> [!IMPORTANT]
> Переменная `username` заполняется только после рендеринга компонента, а его вывод включает элемент `<input>`. Если вы попытаетесь передать непосещенный код `ElementRef` на JavaScript, код JavaScript получит `null`. Чтобы манипулировать ссылками на элементы после того, как компонент завершил рендеринг (для установки начального фокуса на элемент), используйте методы `OnAfterRenderAsync` или `OnAfterRender` [методы жизненного цикла компонентов](xref:client-side/blazor/components/index#жизненный-цикл-методов).

## Вызов .NET методов из функций JavaScript

### Static .NET method call
Для вызова статических .NET методов из JavaScript используются функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`. Передайте идентификатор статического метода, который вы хотите вызвать, имя сборки, содержащей функцию, и любые аргументы. Опять же, асинхронная версия предпочтительна для поддержки сценариев вне процесса. Чтобы быть вызываемым из JavaScript, метод .NET должен быть общедоступным, статическим и помеченным `[JSInvokable]`. По умолчанию идентификатор метода - это имя метода, но вы можете указать другой идентификатор, используя конструктор `JSInvokableAttribute`. Вызов открытых общих методов в настоящее время не поддерживается.

To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions. Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments. Again, the async version is preferred to support server-side scenarios. To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`. By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor. Calling open generic methods isn't currently supported.

The sample app includes a C# method to return an array of `int`s. The method is decorated with the `JSInvokable` attribute.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/2.x/BlazorSample/Pages/JsInterop.cshtml?start=47&end=58&highlight=7-11)]

JavaScript served to the client invokes the C# .NET method.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/2.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-12)]

When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools:

```console
Array(4) [ 1, 2, 3, 4 ]
```

The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.

### Instance method call

You can also call .NET instance methods from JavaScript. To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance. The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions. The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.

> [!NOTE]
> The sample app logs messages to the client-side console. For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.

When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/2.x/BlazorSample/Pages/JsInterop.cshtml?start=60&end=69&highlight=8)]

`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/2.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=19-25)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/2.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-17)]

The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property. When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/2.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Console output in the browser's web developer tools:

```console
Hello, Blazor!
```

## Использовать код взаимодействия в библиотеке классов Blazor

JavaScript-код может быть включен в библиотеку классов Blazor (`dotnet new blazorlib`), которая позволяет вам делиться кодом в пакете NuGet.

Библиотека классов Blazor обрабатывает встраивание ресурсов JavaScript в встроенную сборку. Файлы JavaScript помещаются в папку *wwwroot*, а инструментарий заботится о вложении ресурсов при создании библиотеки.

Созданный пакет NuGet ссылается в файле проекта приложения Blazor так же, как и на любой нормальный пакет NuGet. После восстановления приложения, код приложения может вызвать JavaScript, как если бы он был C# кодом.
