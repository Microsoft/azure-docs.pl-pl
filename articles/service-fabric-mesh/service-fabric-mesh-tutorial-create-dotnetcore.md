---
title: Tworzenie i wdrażanie aplikacji wielousługowej w celu Service Fabric siatki
description: W tym samouczku utworzysz wielousługową aplikację usługi Azure Service Fabric Mesh składającą się z witryny internetowej ASP.NET Core, która komunikuje się z usługą internetową zaplecza, a następnie zdebugujesz ją lokalnie i opublikujesz na platformie Azure.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: fa15432072f6478100a378fdbdb697556fdf1dc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842550"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Samouczek: tworzenie, debugowanie, wdrażanie i aktualizowanie aplikacji usługi Service Fabric Mesh dla wielu usług

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak za pomocą programu Visual Studio można utworzyć aplikację usługi Azure Service Fabric Mesh z frontonem internetowym platformy ASP.NET i usługą zaplecza internetowego interfejsu API platformy ASP.NET Core. Następnie przeprowadzisz debugowanie aplikacji w lokalnym klastrze projektowym. Opublikujesz aplikację na platformie Azure, a następnie dokonasz zmian w konfiguracji i kodzie i uaktualnisz aplikację. Na koniec wyczyścisz nieużywane zasoby platformy Azure, aby zapobiec naliczaniu opłat za nie.

Zanim zakończysz, przejdziesz przez większość faz zarządzania cyklem życia aplikacji i zbudujesz aplikację demonstrującą wywołanie typu usługa-usługa w aplikacji usługi Service Fabric Mesh.

Jeśli nie chcesz ręcznie tworzyć aplikacji z zadaniami do wykonania, możesz [pobrać kod źródłowy](https://github.com/azure-samples/service-fabric-mesh) gotowej aplikacji i od razu przejść do sekcji [Lokalne debugowanie aplikacji](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji usługi Service Fabric Mesh składającej się z frontonu internetowego ASP.NET w programie Visual Studio.
> * Tworzenie modelu reprezentującego elementy do wykonania.
> * Tworzenie usługi zaplecza i pobieranie danych z niej.
> * Dodawanie kontrolera i elementu DataContext w ramach wzorca model-widok-kontroler dla usługi zaplecza.
> * Tworzenie strony internetowej do wyświetlania elementów do wykonania.
> * Tworzenie zmiennych środowiskowych, identyfikujących usługę zaplecza

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio
> * [Debugowanie aplikacji usługi Service Fabric Mesh działającej w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uaktualnianie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md) z zainstalowanym środowiskiem uruchomieniowym usługi Service Fabric, zestawem SDK, platformą Docker i programem Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Tworzenie projektu usługi Service Fabric Mesh w programie Visual Studio

Uruchom program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt...**

W oknie dialogowym **Nowy projekt** w polu **Wyszukaj** u góry wpisz `mesh`. Wybierz szablon **Aplikacja Service Fabric Mesh**. Jeśli go nie widzisz, upewnij się, że zainstalowano zestaw SDK usługi Mesh i narzędzia VS w wersji zapoznawczej zgodnie z opisem w temacie [Konfigurowanie środowiska projektowego](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

W polu **Nazwa** wpisz `todolistapp`, a w polu **Lokalizacja** ustaw ścieżkę do folderu, w którym chcesz przechowywać pliki projektu.

Upewnij się, że opcja **Utwórz katalog dla rozwiązania** jest zaznaczona, a następnie kliknij przycisk **OK**, aby utworzyć projekt usługi Service Fabric Mesh.

![Zrzut ekranu pokazujący sposób tworzenia projektu siatki Service Fabric.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Pojawi się okno dialogowe **Nowa usługa Service Fabric**.

### <a name="create-the-web-front-end-service"></a>Tworzenie usługi frontonu internetowego

W oknie dialogowym **Nowa usługa Service Fabric** wybierz typ projektu **ASP.NET Core** i upewnij się, że w obszarze **System operacyjny kontenera** wybrano opcję **Windows**.

W polu **Nazwa usługi** wpisz **WebFrontEnd**. Naciśnij przycisk **OK**, aby utworzyć usługę platformy ASP.NET Core.

![Okno dialogowe nowego projektu usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Zostanie wyświetlone okno dialogowe Aplikacja internetowa platformy ASP.NET Core. Wybierz pozycję **Aplikacja internetowa**, a następnie kliknij przycisk **OK**.

![Zrzut ekranu, który podświetla szablon Appliccation sieci Web.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Masz już aplikację usługi Service Fabric Mesh. Kolejnym krokiem jest utworzenie modelu z informacjami o zadaniach do wykonania.

## <a name="create-the-to-do-items-model"></a>Tworzenie modelu elementów do wykonania

Dla uproszczenia elementy do wykonania są przechowywane na liście w pamięci. Utwórz bibliotekę klas do obsługi elementów do wykonania oraz listę służącą do ich przechowywania. W programie Visual Studio przy załadowanym rozwiązaniu **todolistapp** wybierz pozycję **Plik** > **Dodaj** > **Nowy projekt**.

W oknie dialogowym **Dodawanie nowego projektu** w polu **Wyszukaj** u góry wpisz `C# .net core class`. Wybierz szablon **Biblioteka klas (.NET Core)**.

W polu **Nazwa** wpisz `Model`. Kliknij przycisk **OK**, aby utworzyć bibliotekę klas.

W eksploratorze rozwiązań w obszarze **Model** kliknij prawym przyciskiem myszy pozycję **Class1.cs** i wybierz polecenie **Zmień nazwę**. Zmień nazwę klasy **ToDoItem.cs**. Gdy pojawi się monit o zmianę nazw wszystkich odwołań, kliknij przycisk **Tak**.

Do pustego elementu `class ToDoItem` skopiuj następującą zawartość:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Ta klasa reprezentuje elementy do wykonania.

W programie Visual Studio kliknij prawym przyciskiem myszy bibliotekę klas **Model**, a następnie wybierz polecenie **Dodaj** > **Klasa...**, aby utworzyć listę do przechowywania elementów do wykonania. Zostanie wyświetlone okno dialogowe **Dodawanie nowego elementu**. Opcję **Nazwa** ustaw na wartość `ToDoList.cs` i kliknij przycisk **Dodaj**.

W pliku **ToDoList.cs** do pustego elementu `class ToDoList` skopiuj następującą zawartość:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Następnym krokiem jest utworzenie usługi Service Fabric, która będzie śledzić elementy do wykonania.

## <a name="create-the-back-end-service"></a>Tworzenie usługi zaplecza

W programie Visual Studio w oknie **eksploratora rozwiązań** kliknij prawym przyciskiem myszy pozycję **todolistapp** i kliknij polecenie **Dodaj** > **Nowa usługa Service Fabric...**

Pojawi się okno dialogowe **Nowa usługa Service Fabric**. Wybierz typ projektu **ASP.NET Core** i upewnij się, że opcja **System operacyjny kontenera** jest ustawiona na wartość **Windows**. W polu **Nazwa usługi** wpisz **ToDoService**. Kliknij przycisk **OK**, aby utworzyć usługę platformy ASP.NET Core.

Pojawi się okno dialogowe **Nowa aplikacja internetowa platformy ASP.NET Core**. W tym oknie dialogowym wybierz pozycję **API**, a następnie przycisk **OK** — projekt usługi zostanie dodany do rozwiązania.

![Nowa aplikacja platformy ASP.NET Core w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Ponieważ usługa zaplecza nie udostępnia żadnego interfejsu użytkownika, wyłącz opcję uruchamiania przeglądarki po uruchomieniu usługi. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **ToDoService**, a następnie wybierz polecenie **Właściwości**. W wyświetlonym oknie właściwości wybierz kartę **Debugowanie** po lewej stronie, a następnie usuń zaznaczenie pola wyboru **Uruchom przeglądarkę**. Naciśnij klawisze **Ctrl+S**, aby zapisać zmiany.

Ponieważ ta usługa przechowuje informacje o elementach do wykonania, dodaj odwołanie do biblioteki klas modelu. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję **ToDoService** , a następnie wybierz pozycję **Dodaj**  >  **odwołanie.**... Zostanie wyświetlone okno dialogowe **Menedżer odwołań** .

W oknie dialogowym **Menadżer odwołań** zaznacz pole wyboru obok pozycji **Model** i kliknij przycisk **OK**.

### <a name="add-a-data-context"></a>Dodawanie kontekstu danych

Kolejnym krokiem jest utworzenie kontekstu danych, który umożliwia koordynowanie wysyłania danych z modelu danych.

Aby dodać klasę kontekstu danych, w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **ToDoService**, a następnie kliknij polecenie **Dodaj** > **Klasa**.
W wyświetlonym oknie dialogowym **Dodawanie nowego elementu** upewnij się, że wybrano opcję **Klasa**, w polu **Nazwa** ustaw wartość `DataContext.cs`, a następnie kliknij przycisk **Dodaj**.

W pliku **DataContext.cs** do pustego elementu `class DataContext` skopiuj następującą zawartość:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Ten minimalny kontekst danych umożliwia wypełnienie przykładowych elementów do wykonania i zapewnia dostęp do nich.

### <a name="add-a-controller"></a>Dodawanie kontrolera

Domyślny kontroler, który obsługuje żądania HTTP i tworzy odpowiedzi HTTP, został udostępniony w momencie utworzenia projektu **ToDoService** przy użyciu szablonu. W **Eksploratorze rozwiązań** w obszarze **ToDoService** otwórz folder **Controllers**, aby wyświetlić plik **ValuesController.cs**. 

Kliknij prawym przyciskiem myszy plik **ValuesController.cs**, a następnie wybierz polecenie **Zmień nazwę**. Zmień nazwę pliku na `ToDoController.cs`. Jeśli pojawi się monit o zmianę nazw wszystkich odwołań, kliknij przycisk **Tak**.

Otwórz plik **ToDoController.cs** i zastąp zawartość elementu `class ToDoController` następującą zawartością:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

W tym samouczku skupiono się na komunikacji z inną usługą — operacje dodawania, usuwania itp. nie są implementowane.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Tworzenie strony internetowej służącej do wyświetlania elementów do wykonania

Po zaimplementowaniu usługi zaplecza należy opracować kod witryny internetowej, w której będą wyświetlane udostępnione elementy do wykonania. Projekt **WebFrontEnd** obejmuje następujące kroki.

Strona internetowa służąca do wyświetlania elementów do wykonania wymaga dostępu do klasy **ToDoItem** i listy.
W **Eksplorator rozwiązań**Dodaj odwołanie do projektu modelu przez kliknięcie prawym przyciskiem myszy elementu **webfronton** i wybranie pozycji **Dodaj**  >  **odwołanie...** Zostanie wyświetlone okno dialogowe **Menedżer odwołań** .

W oknie dialogowym **Menadżer odwołań** kliknij pole wyboru obok pozycji **Model** i kliknij przycisk **OK**.

W **Eksploratorze rozwiązań** otwórz stronę Index.cshtml, wybierając pozycję **WebFrontEnd** > **Strony** > **Index.cshtml**. Otwórz plik **Index.cshtml**.

Zastąp całą zawartość pliku poniższym kodem HTML, który definiuje prostą tabelę, umożliwiającą wyświetlenie elementów do wykonania:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Kliknij ikonę listy rozwijanej dla pliku **Index.cshtml** w **Eksploratorze rozwiązań**, a następnie otwórz plik **Index.cshtml.cs**.

W górnej części pliku **Index.cshtml.cs** dodaj element `using System.Net.Http;`

Zastąp zawartość elementu `public class IndexModel` następującym kodem:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Tworzenie zmiennych środowiskowych

Do komunikowania się z usługą zaplecza jest wymagany adres URL tej usługi. W tym samouczku w poniższym fragmencie kodu (zdefiniowanym powyżej jako część klasy IndexModel) są odczytywane zmienne środowiskowe zawierające części adresu URL:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Adres URL składa się z nazwy usługi i portu. Wszystkie te informacje można znaleźć w pliku service.yaml znajdującym się w projekcie **ToDoService**.

> [!IMPORTANT]
> W poniższych krokach zostaną zmodyfikowane pliki YAML.
> Do uzyskania efektu wcięcia zmiennych w pliku service.yaml należy użyć spacji, a nie znaków tabulacji. W przeciwnym razie plik nie zostanie skompilowany. Program Visual Studio może wstawiać znaki tabulacji podczas tworzenia zmiennych środowiskowych. Wszystkie znaki tabulacji należy zastąpić spacjami. Mimo że dane wyjściowe debugowania **kompilacji** będą zawierać błędy, aplikacja zostanie uruchomiona, lecz nie będzie działać poprawnie do momentu przekonwertowania tabulatorów na spacje i ponownego skompilowania. Aby upewnić się, że żadne karty nie znajdują się w pliku Service. YAML, można w edytorze programu Visual Studio wyświetlić odstępy, korzystając z **edycji**   >  **zaawansowanego**   >  **wyświetlania białych**znaków.
> Należy pamiętać, że pliki service.yaml są przetwarzane przy użyciu ustawień regionalnych języka angielskiego. Na przykład dla separatora dziesiętnego należy zastosować kropkę, a nie przecinek.

W **Eksploratorze rozwiązań** przejdź do projektu **ToDoService**, a następnie otwórz plik **Zasoby usługi** > **service.yaml**.

![Rysunek 1. Plik service.yaml projektu ToDoService](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 Nazwa usługi, `ToDoService`, znajduje się w sekcji `services:` — patrz (1) na powyższej ilustracji.

* Port, `80`, znajduje się w sekcji `endpoints:` — patrz (2) na powyższej ilustracji. Numer portu w Twoim projekcie będzie prawdopodobnie inny.

Następnie należy zdefiniować zmienne środowiskowe reprezentujące nazwę usługi i numer portu w projekcie usługi WebFrontEnd, aby umożliwić wywoływanie usługi zaplecza.

W **Eksploratorze rozwiązań** przejdź do pozycji **WebFrontEnd** > **Zasoby usługi** > **service.yaml**, aby zdefiniować zmienne, które określają adres usługi zaplecza.

W pliku service.yaml dodaj następujące zmienne w ramach elementu `environmentVariables:` (najpierw należy usunąć znak komentarza `#`, aby usunąć oznaczenie elementu `environmentVariables:` jako komentarza). Odstępy są uwzględniane, więc należy wyrównać zmienne do innych zmiennych w ramach elementu `environmentVariables:`. Bardzo ważne jest, aby wartość elementu ApiHostPort odpowiadała wartości portu elementu ToDoServiceListener dostępnej w pliku service.yaml usługi ToDoService.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Istnieją dwa sposoby określenia wartości dla elementu `ToDoServiceName`: 
> - Za pomocą nazwy usługi rozpoznawanej zarówno w scenariuszu debugowania w systemie Windows 10, jak i podczas wdrażania usługi w usłudze Azure Service Fabric Mesh.
> - W pełni kwalifikowana nazwa to servicename.appname. Ta opcja działa tylko podczas debugowania w systemie Windows 10.
> Dobrym rozwiązaniem jest używanie tylko nazwy usługi do rozpoznawania usługi.

Plik **service.yaml** w Twoim projekcie **WebFrontEnd** powinien wyglądać mniej więcej tak, mimo że wartość `ApiHostPort` prawdopodobnie będzie inna:

![Plik service.yaml w projekcie WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Teraz możesz przystąpić do tworzenia i wdrażania obrazu aplikacji usługi Service Fabric Mesh oraz usługi internetowej zaplecza w lokalnym klastrze.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji usługi Service Fabric Mesh składającej się z frontonu internetowego ASP.NET.
> * Tworzenie modelu reprezentującego elementy do wykonania.
> * Tworzenie usługi zaplecza i pobieranie danych z niej.
> * Dodawanie kontrolera i elementu DataContext w ramach wzorca model-widok-kontroler dla usługi zaplecza.
> * Tworzenie strony internetowej do wyświetlania elementów do wykonania.
> * Tworzenie zmiennych środowiskowych, identyfikujących usługę zaplecza

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Debugowanie aplikacji usługi Service Fabric Mesh w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)