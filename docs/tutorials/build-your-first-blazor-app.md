---
title: Создайте свое первое приложение
author: guardrex
description: Build a Blazor app step-by-step and quickly learn the basic features of the Blazor framework.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/tutorials/first-app
---
# Создайте свое первое приложение

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Используя данную инструкцию, вы постепенно создаете приложение Blazor и быстро изуизучите основные функции фреймворка Blazor.

[Посмотрите или скачайте примеры кода](https://github.com/aspnet/Blazor.Docs/tree/master/docs/tutorials/build-your-first-blazor-app/samples/) ([как скачать](xref:client-side/blazor/index#view-and-download-samples)). Смотрите [Начало работы с Blazor](xref:client-side/blazor/get-started).

Чтобы создать проект в Visual Studio:

1. Выберите **File** > **New** > **Project**. Выберите **Web** > **ASP.NET Core Web Application**. Запишите название проекта "BlazorApp1" в поле **Name**. Нажмите **OK**.

    ![Новый ASP.NET Core проект](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. Появится диалог **New ASP.NET Core Web Application**. Убедимся что выбрано **.NET Core** в верху окна. Выберите либо **ASP.NET Core 2.0** либо **ASP.NET Core 2.1**. Выберите шаблон **Blazor** и нажмите **OK**.

    ![Диалог нового Blazor приложения](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. Когда проект будет создан, нажмите **Ctrl-F5** для запуска приложения *без отладчика*. Работа с отладчиком (**F5**) в настоящее время не поддерживается.

> [!NOTE]
> Если вы не используете Visual Studio, вы можете создавать Blazor приложения используя командную строку Windows, macOS или Linux:
>
> ```console
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> Перейдите в приложение используя адрес **localhost** и порт, который отобразится в окне консоли после выполнения команды `dotnet run`. Используйте сочетание клавиш **Ctrl-C** в окне консоли для завершения приложения.

Приложение Blazor запущено в браузере:

![Домашняя страница Blazor приложения](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## Сборка компонентов

1. Посетите каждую из 3 доступных страниц: *Home*, *Counter* и *Fetch data*.

    Эти три страницы реализованы тремя файлами Razor в каталоге *Pages*: *Index.cshtml*, *Counter.cshtml* и *FetchData.cshtml*. Каждый из этих файлов реализует компонент Blazor, который компилируется и выполняется на стороне клиента в браузере.

1. Нажмите кнопку на странице *Counter*.

    ![Страница Counter приложения Blazor](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    Каждый раз, когда нажимается кнопка, счетчик увеличивается без обновления страницы. Обычно такое поведение на стороне клиента реализуется в JavaScript; но здесь это реализовано используя C# в компоненте `Counter`.
    
1. Взгляните на реализацию компонента `Counter` в файле *Counter.cshtml*:

    ```cshtml
    @page "/counter"

    <h1>Counter</h1>

    <p>Current count: @currentCount</p>

    <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
    ```

    Пользовательский интерфейс для компонента `Counter` определяется обычным HTML. Динамическая логика визуализации (например, циклы, условия, выражения) добавляется с использованием синтаксиса C# [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). 
    HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки. Имя сгенерированного .NET-класса соответствует имени файла.

    Компоненты класса определяются в блоке `@functions`. В блоке `@functions`, компоненты состояния (свойства, поля) и методы могут быть указаны для обработки событий или для определения другой логики компонента. Эти элементы затем могут использоваться как часть логики рендеринга компонента и для обработки событий.

    Когда нажимается кнопка, в компоненте `Counter` вызывается зарегистрированный обработчик `onclick` (метод `IncrementCount`) и конпонент `Counter` регенерирует дерево рендеринга. Blazor сравнивает новое дерево рендеринга с предыдущим и применяет все модификации в Document Object Model (DOM) браузера. Отображаемый счетчик обновляется.

1. Обновите разметку для компонента `Counter` чтобы сделать заголовок верхнего уровня более *захватывающим*.

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. Также измените логику C# компонента `Counter` так, чтобы значение увиличивалось на 2 за раз.

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. Перезагрузите страницу в браузере чтобы увидеть изменения.

    ![Захватывающий счетчик](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## Используйте компоненты

После того как компонент определён, он может быть использован для реализации других компонентов. Разметка для использования компонента выглядит как HTML тег, где имя тега является типом компонента.

1. Добавьте компонент `Counter` на главную страницу приложения (*Index.cshtml*).

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. Перезагрузите домашнюю страницу в браузере. Обратите внимание на отдельный экземпляр компонента `Counter` на главной странице.

    ![Blazor домашняя страница с добавленным компонентом Counter](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## Параметры компонента

Компоненты также могут иметь параметры, который задаются используя свойства класса компонента, которые помечены атрибутом `[Parameter]`. Используйте атрибуты для указания аргументов для компонента в разметке.

1. Обновите компонент `Counter` добавив параметр `IncrementAmount` со значением по умолчанию `1`.

    ```cshtml
    @functions {
        int currentCount = 0;

        [Parameter]
        private int IncrementAmount { get; set; } = 1;

        void IncrementCount()
        {
            currentCount += IncrementAmount;
        }
    }
    ```

    > [!NOTE]
    > Используя Visual Studio, вы можете быстро добавлять параметры компонента, используя сниппет `para`. Наберите `para` и нажмите `Tab` дважды.

1. On the Home page (*Index.cshtml*), change the increment amount for the `Counter` to 10 by setting an attribute that matches the name of the component's property for `IncrementCount`.

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. Reload the page.

    The counter on the Home page now increments by 10, while the counter on the Counter page still increments by 1.

    ![Blazor count by ten](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## Route to components

The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a page to which requests can be routed. Specifically, the `Counter` component handles requests sent to `/Counter`. Without the `@page` directive, the component wouldn't handle routed requests, but the component could still be used by other components.

## Dependency injection

Services registered with the app's service provider are available to components via [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Services can be injected into a component using the `@inject` directive.

Take a look at the implementation of the `FetchData` component in *FetchData.cshtml*. The `@inject` directive is used to inject an [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance into the component.

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

The `FetchData` component uses the injected `HttpClient` to retrieve JSON data from the server when the component is initialized. Under the covers, the `HttpClient` provided by the Blazor runtime is implemented using JavaScript interop to call the underlying browser's Fetch API to send the request (from C#, it's possible to call any JavaScript library or browser API). The retrieved data is deserialized into the `forecasts` C# variable as an array of `WeatherForecast` objects.

```cshtml
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

A `@foreach` loop is used to render each forecast instance as a row in the weather table.

```cshtml
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## Build a todo list

Add a new page to the app that implements a simple todo list.

1. Add an empty text file to the *Pages* folder named *Todo.cshtml*.

1. Provide the initial markup for the page.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. Add the Todo page to the navigation bar by updating *Shared/NavMenu.cshtml*. Add a `NavLink` for the Todo page by adding the following list item markup below the existing list items.

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. Refresh the app in the browser. See the new Todo page.

    ![Blazor todo start](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. Add a *TodoItem.cs* file to the root of the project to hold a class to represent the todo items.

1. Use the following C# code for the `ToDoItem` class.

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. Go back to the `Todo` component in *Todo.cshtml* and add a field for the todos in a `@functions` block. The `Todo` component uses this field to maintain the state of the todo list.

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Add unordered list markup and a `foreach` loop to render each todo item as a list item.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>
    ```

1. The app requires UI elements for adding todos to the list. Add a text input and a button below the list.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input class placeholder="Something todo" />
    <button>Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Refresh the browser.

    ![Add todo button](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    Nothing happens when the **Add todo** button is selected because no event handler is wired up to the button.

1. Add an `AddTodo` method to the `Todo` component and register it for button clicks using the `onclick` attribute.

    ```cshtml
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: Add the todo
        }
    }
    ```

    The `AddTodo` C# method is called every time the button is selected.

1. To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute.

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. Update the `AddTodo` method to add the `TodoItem` with the specified title to the list. Don't forget to clear the value of the text input by setting `newTodo` to an empty string.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

1. Refresh the browser. Add some todos to the todo list.

    ![Add todos](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. Lastly, what's a todo list without check boxes? The title text for each todo item can be made editable as well. Add a check box input and text input for each todo item and bind their values to the `Title` and `IsDone` properties, respectively.

    ```cshtml
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

1. To verify that these values are bound, update the `h1` header to show a count of the number of todo items that are not yet done (`IsDone` is `false`).

    ```cshtml
    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>
    ```
    
1. The completed `Todo` component should look like this:

    ```cshtml
    @page "/todo"

    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

Refresh the app in the browser. Try adding some todo items.

![Finished Blazor todo list](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## Publish and deploy

When using Visual Studio, perform the following steps to publish the Todo Blazor app to Azure:

1. Right-click on the project in **Solution Explorer** and select **Publish**.

1. In the **Pick a publish target** dialog, select **App Service** and **Create New**. Select **Publish**.

    ![Pick a publish target](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. In the **Create App Service** dialog, choose a name for the app and select the subscription, resource group, and hosting plan. Select **Create** to create the app service and publish the app.

    ![Create app service](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

Wait a minute or so for the app to be deployed.

The app should now be running in Azure. Mark the todo item to build your first Blazor app as *done*. Nice job!

![Blazor on Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> If not using Visual Studio, publish the Blazor app at a command prompt on Windows, macOS, or Linux:
>
> ```console
> dotnet publish -c Release
> ```
>
> The deployment is created in the */bin/Release/\<target-framework>/publish* folder. Move the contents of the *publish* folder to the server or hosting service.
>
> For more information, see the [Host and deploy](xref:client-side/blazor/host-and-deploy/index#publish-the-app) topic.

## Additional resources

For a more involved Blazor sample app, check out the [Flight Finder sample](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) on GitHub.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
