---
title: 'Samouczek: ASP.NET aplikacji z Azure SQL Database'
description: Dowiedz się, jak wdrożyć aplikację C# ASP.NET na platformie Azure i Azure SQL Database
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606023"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Samouczek: wdrażanie aplikacji ASP.NET na platformie Azure za pomocą Azure SQL Database

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym samouczku pokazano, jak wdrożyć opartą na danych aplikację ASP.NET w usłudze App Service i połączyć ją z usługą [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Po zakończeniu masz aplikację ASP.NET działającą na platformie Azure i połączoną z SQL Database.

![Opublikowana aplikacja ASP.NET w aplikacji usługi App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie bazy danych w Azure SQL Database
> * Łączenie aplikacji ASP.NET z usługą SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników z platformy Azure do terminala
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> przy użyciu obciążeń **ASP.NET i Web Development** .

Jeśli program Visual Studio został już zainstalowany, Dodaj obciążenia w programie Visual Studio, klikając pozycję **Narzędzia**  >  **Pobierz narzędzia i funkcje**.

## <a name="download-the-sample"></a>Pobieranie przykładu

1. [Pobierz przykładowy projekt](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Wyodrębnij (Rozpakuj) plik  *dotnet-sqldb-tutorial-master.zip* .

Przykładowy projekt zawiera podstawową aplikację [ASP.NET MVC](https://www.asp.net/mvc) CRUD (create-read-update-delete, tworzenie-odczytywanie-aktualizowanie-usuwanie) korzystającą z modelu [Code First platformy Entity Framework](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Uruchamianie aplikacji

1. Otwórz plik *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* w programie Visual Studio.

1. Naciśnij klawisze `Ctrl+F5`, aby uruchomić aplikację bez debugowania. Aplikacja zostanie wyświetlona w domyślnej przeglądarce.

1. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu *zadanie do wykonania*.

    ![Okno dialogowe Nowy projekt ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.

Na potrzeby połączenia z bazą danych aplikacja używa kontekstu bazy danych. W tym przykładzie kontekst bazy danych używa parametrów połączenia o nazwie `MyDbConnection`. Parametry połączenia są ustawiane w pliku *Web.config*, a przywoływane w pliku *Models/MyDatabaseContext.cs*. Ta nazwa parametrów połączenia jest używana w dalszej części tego samouczka w celu połączenia aplikacji platformy Azure z usługą Azure SQL Database.

## <a name="publish-aspnet-application-to-azure"></a>Publikowanie aplikacji ASP.NET na platformie Azure

1. W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

    ![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Wybierz **platformę Azure** jako element docelowy, a następnie kliknij przycisk **dalej**.

1. Upewnij się, że wybrano **Azure App Service (Windows)** , a następnie kliknij przycisk **dalej**.

#### <a name="sign-in-and-add-an-app"></a>Logowanie i Dodawanie aplikacji

1. W oknie dialogowym **Publikowanie** kliknij pozycję **Dodaj konto** z listy rozwijanej Menedżer kont.

1. Zaloguj się do subskrypcji platformy Azure. Jeśli zalogowano się już do konta Microsoft, upewnij się, że to konto zawiera Twoją subskrypcję platformy Azure. Jeśli użyte do logowania konto Microsoft nie ma subskrypcji platformy Azure, kliknij je, aby dodać prawidłowe konto.

1. W okienku **App Service Instances (wystąpienia** ) kliknij pozycję **+** .

    ![Logowanie do platformy Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Konfigurowanie nazwy aplikacji internetowej

Wygenerowaną nazwę aplikacji internetowej można zachować lub zmienić na inną unikatową nazwę (prawidłowe znaki to `a-z`, `0-9` i `-`). Nazwa aplikacji internetowej jest używana jako część domyślnego adresu URL aplikacji (`<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji internetowej). Te nazwy muszą być unikatowe dla wszystkich aplikacji na platformie Azure.

> [!NOTE]
> Nie zaznaczaj jeszcze opcji **Utwórz** .

![Okno dialogowe Tworzenie usługi App Service](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Obok pozycji **Grupa zasobów** kliknij przycisk **Nowa**.

   ![Obok pozycji Grupa zasobów kliknij przycisk Nowa.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Nadaj grupie zasobów nazwę **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Obok pozycji **Plan hostingu** kliknij pozycję **Nowy**.

1. W oknie dialogowym **Konfigurowanie planu App Service** Skonfiguruj nowy plan App Service z następującymi ustawieniami i kliknij przycisk **OK**:

   | Ustawienie  | Sugerowana wartość | Więcej informacji |
   | ----------------- | ------------ | ----|
   |**Plan usługi App Service**| myAppServicePlan | [Plany usługi App Service](../app-service/overview-hosting-plans.md) |
   |**Lokalizacja**| West Europe | [Regiony platformy Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Rozmiar**| Bezpłatna | [Warstwy cenowe](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Tworzenie planu usługi App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobów platformy Azure.

1. W oknie dialogowym **Publikowanie** są wyświetlane skonfigurowane zasoby. Kliknij przycisk **Finish** (Zakończ).

   ![Utworzone zasoby](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Tworzenie serwera i bazy danych

Przed utworzeniem bazy danych potrzebny jest [logiczny serwer SQL](../azure-sql/database/logical-servers.md). Logiczny serwer SQL to konstrukcja logiczna, która zawiera grupę baz danych zarządzanych jako Grupa.

1. W oknie dialogowym **Publikowanie** przewiń w dół do sekcji **zależności usługi** . Obok pozycji **SQL Server Database** kliknij pozycję **Konfiguruj**.

   ![Konfigurowanie zależności SQL Database](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Wybierz pozycję **Azure SQL Database** i kliknij przycisk **dalej**.

1. W oknie dialogowym **konfigurowanie Azure SQL Database** kliknij pozycję **+** .

1. Obok pozycji **serwer bazy danych** kliknij pozycję **Nowy**.

   Nazwa serwera jest generowana. Ta nazwa jest używana jako część domyślnego adresu URL serwera programu `<server_name>.database.windows.net` . Musi być unikatowa dla wszystkich serwerów w usłudze Azure SQL. Nazwę serwera można zmienić, ale na potrzeby tego samouczka należy zachować wygenerowaną wartość.

1. Dodaj nazwę użytkownika i hasło administratora. Wymagania dotyczące złożoności hasła opisano w temacie [Password Policy](/sql/relational-databases/security/password-policy) (Zasady haseł).

   Zapamiętaj tę nazwę użytkownika i hasło. Są one potrzebne do późniejszego zarządzania serwerem.

   ![Tworzenie serwera](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Mimo że hasło w parametrach połączenia jest maskowane (w programie Visual Studio, a także w usłudze App Service), to fakt jego przechowywania zwiększa obszar aplikacji podatny na ataki. Usługa App Service może używać [tożsamości usługi zarządzanej](overview-managed-identity.md) do wyeliminowania tego ryzyka przez całkowite usunięcie konieczności przechowywania wpisów tajnych w kodzie lub konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [Następne kroki](#next-steps).

1. Kliknij przycisk **OK**.

1. W oknie dialogowym **Azure SQL Database** Zachowaj domyślną wygenerowaną **nazwę bazy danych**. Wybierz pozycję **Utwórz** i poczekaj na utworzenie zasobów bazy danych.

    ![Konfiguruj bazę danych](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Konfiguruj połączenie z bazą danych

1. Po zakończeniu tworzenia zasobów bazy danych przez kreatora kliknij przycisk **dalej**.

1. W **Nazwa parametry połączenia z bazą danych** wpisz _MyDbConnection_. Ta nazwa musi być zgodna z parametrami połączenia przywoływanymi w pliku _Models/MyDatabaseContext.cs_.

1. W polu **Nazwa użytkownika połączenia bazy danych** i **hasło połączenia z bazą danych** wpisz nazwę użytkownika i hasło administratora użyte podczas [tworzenia serwera](#create-a-server-and-database).

1. Upewnij się, że **Ustawienia aplikacji Azure** są zaznaczone, a następnie kliknij przycisk **Zakończ**.

    ![Konfigurowanie parametrów połączenia z bazą danych](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Zaczekaj na zakończenie działania Kreatora konfiguracji, a następnie kliknij przycisk **Zamknij**.

#### <a name="deploy-your-aspnet-app"></a>Wdrażanie aplikacji ASP.NET

1. Na karcie **Publikowanie** przewiń kopię zapasową do góry i kliknij pozycję **Publikuj**. Po wdrożeniu aplikacji ASP.NET na platformie Azure. Uruchamiana jest domyślna przeglądarka z adresem URL wdrożonej aplikacji.

1. Dodaj kilka elementów do wykonania.

    ![Opublikowana aplikacja ASP.NET w aplikacji platformy Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Gratulacje! Twoja oparta na danych aplikacja ASP.NET działa w usłudze Azure App Service.

## <a name="access-the-database-locally"></a>Dostęp do bazy danych lokalnie

Program Visual Studio umożliwia łatwe eksplorowanie i zarządzanie nową bazą danych na platformie Azure w **Eksplorator obiektów SQL Server**. Nowa baza danych została już otwarta w zaporze w utworzonej aplikacji App Service, ale aby uzyskać do niej dostęp z komputera lokalnego (na przykład z programu Visual Studio), należy otworzyć Zaporę dla publicznego adresu IP komputera lokalnego. Jeśli Twój dostawca usług internetowych zmieni publiczny adres IP, należy ponownie skonfigurować zaporę, aby uzyskać dostęp do bazy danych Azure.

#### <a name="create-a-database-connection"></a>Tworzenie połączenia z bazą danych

1. Z menu **Widok** wybierz opcję **Eksplorator obiektów SQL Server**.

1. W górnej części okna **Eksplorator obiektów SQL Server** kliknij przycisk **Dodaj serwer SQL**.

#### <a name="configure-the-database-connection"></a>Konfigurowanie połączenia z bazą danych

1. W oknie dialogowym **Połączenie** rozwiń węzeł **Azure**. Znajduje się tu lista wszystkich wystąpień bazy danych SQL na platformie Azure.

1. Wybierz utworzoną wcześniej bazę danych. W dolnej części zostanie automatycznie wypełnione utworzone wcześniej połączenie.

1. Wpisz hasło administratora bazy danych (utworzone wcześniej), a następnie kliknij przycisk **Połącz**.

    ![Konfigurowanie połączenia z bazą danych w programie Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Zezwalanie na połączenia klienckie z komputera

Zostanie otwarte okno dialogowe **Tworzenie nowej reguły zapory**. Domyślnie serwer zezwala tylko na połączenia ze swoimi bazami danych z usług platformy Azure, takich jak aplikacja platformy Azure. Aby nawiązać połączenie z bazą danych spoza platformy Azure, Utwórz regułę zapory na poziomie serwera. Reguła zapory zezwala na dostęp z publicznego adresu IP komputera lokalnego.

Okno dialogowe jest już wypełnione informacjami o publicznym adresie IP komputera.

1. Upewnij się, że zaznaczono opcję **Dodaj mój adres IP klienta** i kliknij przycisk **OK**.

    ![Tworzenie reguły zapory](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Po zakończeniu tworzenia ustawienia zapory dla wystąpienia bazy danych SQL w programie Visual Studio połączenie zostanie wyświetlone w narzędziu **Eksplorator obiektów SQL Server**.

    W tym miejscu można wykonywać najpopularniejsze operacje, takie jak uruchamianie zapytań, tworzenie widoków oraz procedur składowanych i inne.

1. Rozwiń połączenie > **baz** danych  >  **&lt; bazy danych>**  >  **tabele**. Kliknij prawym przyciskiem myszy tabelę `Todoes` i wybierz polecenie **Wyświetl dane**.

    ![Eksplorowanie obiektów bazy danych SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aktualizowanie aplikacji za pomocą funkcji Migracje Code First

Znanych narzędzi programu Visual Studio można używać do aktualizowania bazy danych i aplikacji na platformie Azure. W tym kroku użyjesz funkcji Migracje Code First platformy Entity Framework do zmiany schematu bazy danych i opublikowania go na platformie Azure.

Aby uzyskać więcej informacji o używaniu migracji Code First platformy Entity Framework, zobacz [Getting Started with Entity Framework 6 Code First using MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Wprowadzenie do migracji Code First platformy Entity Framework 6 za pomocą MVC 5).

#### <a name="update-your-data-model"></a>Aktualizowanie modelu danych

Otwórz plik _Models\Todo.cs_ w edytorze kodu. Dodaj następującą właściwość do klasy `ToDo`:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Lokalne uruchamianie migracji Code First

Uruchom kilka poleceń, aby zastosować aktualizacje w lokalnej bazie danych.

1. W menu **Narzędzia** kliknij kolejno pozycje **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.

1. W oknie Konsola menedżera pakietów włącz funkcję Migracje Code First:

    ```powershell
    Enable-Migrations
    ```
    
1. Dodaj migrację:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Zaktualizuj lokalną bazę danych:

    ```powershell
    Update-Database
    ```
    
1. Naciśnij klawisze `Ctrl+F5`, aby uruchomić aplikację. Przetestuj linki Edytuj, Szczegóły i Utwórz.

Jeśli aplikacja jest ładowana bez błędów, oznacza to, że działanie funkcji Migracje Code First powiodło się. Jednak Twoja strona nadal wygląda tak samo, ponieważ logika aplikacji nie korzysta jeszcze z tej nowej właściwości.

#### <a name="use-the-new-property"></a>Używanie nowej właściwości

Wprowadź zmiany kodu, aby użyć właściwości `Done`. Dla uproszczenia w tym samouczku zmienisz tylko widoki `Index` i `Create`, aby zobaczyć, jak działa właściwość.

1. Otwórz plik _Controllers\TodosController.cs_.

1. W wierszu 52 znajdź metodę `Create()` i dodaj element `Done` do listy właściwości w atrybucie `Bind`. Po zakończeniu podpis metody `Create()` będzie wyglądać podobnie do następującego kodu:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Otwórz plik _Views\Todos\Create.cshtml_.

1. W kodzie Razor powinien zostać wyświetlony element `<div class="form-group">` korzystający z metody `model.Description`, a następnie inny element `<div class="form-group">` korzystający z metody `model.CreatedDate`. Bezpośrednio po tych dwóch elementach dodaj kolejny element `<div class="form-group">` korzystający z metody `model.Done`:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Otwórz plik _Views\Todos\Index.cshtml_.

1. Wyszukaj pusty element `<th></th>`. Bezpośrednio nad tym elementem dodaj następujący kod Razor:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Znajdź element `<td>` zawierający metody pomocnicze `Html.ActionLink()`. _Powyżej_ tego elementu `<td>` dodaj kolejny element `<td>` z następującym kodem Razor:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    To już wszystko, czego potrzebujesz, aby zobaczyć zmiany w widokach `Index` i `Create`.

1. Naciśnij klawisze `Ctrl+F5`, aby uruchomić aplikację.

Teraz możesz dodać element do wykonania i zaznaczyć pole **Gotowe**. Następnie powinien zostać on wyświetlony na stronie głównej jako ukończony. Pamiętaj, że widok `Edit` nie zawiera pola `Done`, ponieważ nie zmieniono widoku `Edit`.

#### <a name="enable-code-first-migrations-in-azure"></a>Włączanie funkcji Migracje Code First na platformie Azure

Teraz, gdy zmiany kodu zaczęły obowiązywać (łącznie z migracją bazy danych), należy je opublikować w aplikacji platformy Azure, a także zaktualizować bazę danych SQL Database o funkcję Migracje Code First.

1. Podobnie jak wcześniej kliknij projekt prawym przyciskiem myszy i wybierz polecenie **Publikuj**.

1. Kliknij kolejno pozycje **więcej akcji**  >  **Edytuj** , aby otworzyć ustawienia publikowania.

    ![Otwieranie ustawień publikowania](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. Z listy rozwijanej **MyDatabaseContext** wybierz połączenie z bazą danych dla Azure SQL Database.

1. Wybierz pozycję **Wykonaj migracje Code First (wywoływane po uruchomieniu aplikacji)**, a następnie kliknij przycisk **Zapisz**.

    ![Włączanie funkcji Migracje Code First w aplikacji platformy Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Publikowanie zmian

Teraz, po włączeniu funkcji Migracje Code First w aplikacji platformy Azure, opublikuj zmiany kodu.

1. Na stronie publikowania kliknij przycisk **Publikuj**.

1. Spróbuj ponownie dodać elementy do wykonania i wybierz pozycję **Gotowe**. Elementy te powinny być teraz widoczne na stronie głównej jako elementy ukończone.

    ![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Nadal wyświetlane są wszystkie istniejące elementy do wykonania. Po ponownym opublikowaniu aplikacji ASP.NET dane istniejące w bazie danych SQL Database nie zostaną utracone. Ponadto funkcja Migracje Code First zmienia tylko schemat danych, a istniejące dane pozostają niezmienione.

## <a name="stream-application-logs"></a>Przesyłanie strumieniowe dzienników aplikacji

Komunikaty dotyczące śledzenia można przesyłać strumieniowo bezpośrednio z aplikacji platformy Azure do programu Visual Studio.

Otwórz plik _Controllers\TodosController.cs_.

Każda akcja rozpoczyna się od metody `Trace.WriteLine()`. Na przykładzie tego kodu pokazano, jak dodać komunikaty śledzenia do aplikacji platformy Azure.

#### <a name="enable-log-streaming"></a>Włączanie przesyłania strumieniowego dzienników

1. Z menu **Widok** wybierz opcję **Eksplorator chmury**.

1. W programie **Cloud Explorer** rozwiń subskrypcję platformy Azure, która ma swoją aplikację, i rozwiń **App Service**.

1. Kliknij prawym przyciskiem myszy aplikację platformy Azure i wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

    ![Włączanie przesyłania strumieniowego dzienników](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Dzienniki są teraz przesyłane strumieniowo do okna **Dane wyjściowe**.

    ![Przesyłanie strumieniowe dzienników w oknie Dane wyjściowe](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Żadne komunikaty śledzenia nie są jednak jeszcze widoczne. Dzieje się tak, ponieważ kiedy po raz pierwszy wybierzesz polecenie **Wyświetl dzienniki przesyłania strumieniowego**, aplikacja platformy Azure ustawia poziom śledzenia na wartość `Error`, co sprawia, że rejestrowane są tylko zdarzenia błędu (za pomocą metody `Trace.TraceError()`).

#### <a name="change-trace-levels"></a>Zmienianie poziomu śledzenia

1. Aby zmienić poziomy śledzenia w celu wygenerowania danych wyjściowych innych komunikatów śledzenia, Wróć do **Eksploratora chmury**.

1. Ponownie kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Otwórz w portalu**.

1. Na stronie Zarządzanie portalem swojej aplikacji w menu po lewej stronie wybierz pozycję **dzienniki App Service**.

1. W obszarze **Rejestrowanie aplikacji (system plików)** wybierz pozycję **pełny** **poziom**. Kliknij pozycję **Zapisz**.

    ![Zmienianie poziomu śledzenia na Pełne](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Możesz eksperymentować z różnymi poziomami śledzenia, aby zobaczyć, jakie rodzaje komunikatów są wyświetlane dla poszczególnych poziomów. Na przykład poziom **informacji** obejmuje wszystkie dzienniki utworzone przez `Trace.TraceInformation()` , `Trace.TraceWarning()` , i `Trace.TraceError()` , ale nie dzienniki utworzone przez program `Trace.WriteLine()` .

1. W przeglądarce przejdź ponownie do swojej aplikacji pod adresem *http://&lt;nazwa aplikacji>. azurewebsites.net*, a następnie spróbuj kliknąć kilka opcji aplikacji listy zadań do wykonania na platformie Azure. Komunikaty śledzenia są teraz przesyłane strumieniowo do okna **Dane wyjściowe** w programie Visual Studio.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Zatrzymywanie przesyłania strumieniowego dzienników

Aby zatrzymać usługę przesyłania strumieniowego dzienników, kliknij przycisk **Zatrzymaj monitorowanie** w oknie **Dane wyjściowe**.

![Zatrzymywanie przesyłania strumieniowego dzienników](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby zarządzać aplikacją internetową. Wyszukaj i wybierz **App Services**.

![Wyszukaj w usłudze Azure App Services](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Wybierz nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Zostanie otwarta strona Twojej aplikacji.

Domyślnie w portalu jest wyświetlana strona **Przegląd**. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie bazy danych w Azure SQL Database
> * Łączenie aplikacji ASP.NET z usługą SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników z platformy Azure do terminala
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak łatwo poprawić zabezpieczenia połączenia usługi Azure SQL Database.

> [!div class="nextstepaction"]
> [Zabezpieczony dostęp do usługi SQL Database przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](app-service-web-tutorial-connect-msi.md)

Więcej zasobów:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET](configure-language-dotnet-framework.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)