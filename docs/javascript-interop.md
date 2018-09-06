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

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  showPrompt: function (message) {
    return prompt(message, 'Type anything here');
  }
};
```

*ExampleJsInterop.cs*:

```csharp
public class ExampleJsInterop
{
    public static Task<string> Prompt(string message)
    {
        // Implemented in exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<string>(
            "exampleJsFunctions.showPrompt",
            message);
    }
}
```

Абстракция `IJSRuntime` является асинхронной, позволяющей использовать сценарии вне процесса. Если приложение запускается в процессе и вы хотите синхронно вызывать функцию JavaScript, отключите их до `IJSInProcessRuntime` и вызовите `Invoke<T>` вместо этого. Мы рекомендуем использовать асинхронные API для обеспечения доступности библиотек во всех сценариях Blazor, на стороне клиента или на стороне сервера.

## Захват ссылок на элементы

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

Для вызова статических .NET методов из JavaScript используются функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`. Передайте идентификатор статического метода, который вы хотите вызвать, имя сборки, содержащей функцию, и любые аргументы. Опять же, асинхронная версия предпочтительна для поддержки сценариев вне процесса. Чтобы быть вызываемым из JavaScript, метод .NET должен быть общедоступным, статическим и помеченным `[JSInvokable]`. По умолчанию идентификатор метода - это имя метода, но вы можете указать другой идентификатор, используя конструктор `JSInvokableAttribute`. Вызов открытых общих методов в настоящее время не поддерживается.

*JavaScriptInteroperable.cs*:

```csharp
public class JavaScriptInvokable
{
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

*dotnetInterop.js*:

```javascript
DotNet.invokeMethodAsync(assemblyName, 'ReturnArrayAsync').then(data => ...)
```

Новое в Blazor 0.5.0, вы также можете вызвать методы экземпляра .NET из JavaScript. Чтобы вызвать метод экземпляра .NET из JavaScript, сначала передайте экземпляр .NET на JavaScript, завернув его в экземпляр `DotNetObjectRef`. Экземпляр .NET передается по ссылке на JavaScript, и вы можете вызывать методы экземпляра .NET в экземпляре, используя функции `invokeMethod` или` invokeMethodAsync`. Экземпляр .NET также может быть передан как аргумент при вызове других методов .NET из JavaScript.

*ExampleJsInterop.cs*:

```csharp
public class ExampleJsInterop
{
    public static Task SayHello(string name)
    {
        return JSRuntime.Current.InvokeAsync<object>(
            "exampleJsFunctions.sayHello", 
            new DotNetObjectRef(new HelloHelper(name)));
    }
}
```

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  sayHello: function (dotnetHelper) {
    return dotnetHelper.invokeMethodAsync('SayHello')
      .then(r => console.log(r));
  }
};
```

*HelloHelper.cs*:

```csharp
public class HelloHelper
{
    public HelloHelper(string name)
    {
        Name = name;
    }

    public string Name { get; set; }

    [JSInvokable]
    public string SayHello() => $"Hello, {Name}!";
}
```

*Output*:

```
Hello, Blazor!
```

## Использовать код взаимодействия в библиотеке классов Blazor

JavaScript-код может быть включен в библиотеку классов Blazor (`dotnet new blazorlib`), которая позволяет вам делиться кодом в пакете NuGet.

Библиотека классов Blazor обрабатывает встраивание ресурсов JavaScript в встроенную сборку. Файлы JavaScript помещаются в папку *wwwroot*, а инструментарий заботится о вложении ресурсов при создании библиотеки.

Созданный пакет NuGet ссылается в файле проекта приложения Blazor так же, как и на любой нормальный пакет NuGet. После восстановления приложения, код приложения может вызвать JavaScript, как если бы он был C# кодом.
