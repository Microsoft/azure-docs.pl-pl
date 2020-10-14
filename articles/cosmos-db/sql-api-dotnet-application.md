---
title: Samouczek aplikacji sieci Web ASP.NET Core MVC przy użyciu Azure Cosmos DB
description: Samouczek MVC ASP.NET Core do tworzenia aplikacji sieci Web MVC przy użyciu Azure Cosmos DB. Będziesz przechowywać dane JSON i uzyskiwać dostęp z aplikacji do zrobienia hostowanej na samouczku MVC Azure App Service-ASP NET Core.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: bd659ebd74b67a036c189cae763205e6b0371f7c
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058169"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Samouczek: opracowywanie aplikacji sieci Web ASP.NET Core MVC z Azure Cosmos DB przy użyciu zestawu SDK platformy .NET

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji ASP.NET MVC hostowanej na platformie Azure. W tym samouczku użyto zestawu .NET SDK v3. Na poniższej ilustracji przedstawiono stronę sieci Web, która zostanie utworzona za pomocą przykładu w tym artykule:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

Jeśli nie masz czasu na pracę z tym samouczkiem, możesz pobrać kompletny przykładowy projekt z witryny [GitHub][GitHub].

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie konta usługi Azure Cosmos
> * Tworzenie aplikacji ASP.NET Core MVC
> * Łączenie aplikacji z usługą Azure Cosmos DB
> * Wykonywanie operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych

> [!TIP]
> W tym samouczku założono, że masz wcześniejsze środowisko korzystania z ASP.NET Core MVC i Azure App Service. Jeśli dopiero zaczynasz korzystać z narzędzia ASP.NET Core lub [wstępnie wymaganych narzędzi](#prerequisites), zalecamy pobranie kompletnego przykładowego projektu z usługi [GitHub][GitHub], dodanie wymaganych pakietów NuGet i uruchomienie go. Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Program Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Wszystkie zrzuty ekranu w tym artykule pochodzą z Microsoft Visual Studio Community 2019. Jeśli używasz innej wersji, ekrany i opcje mogą nie być zgodne całkowicie. Rozwiązanie powinno być wykonane, jeśli spełniasz wymagania wstępne.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos

Zacznijmy od utworzenia konta usługi Azure Cosmos. Jeśli masz już konto interfejsu API SQL Azure Cosmos DB lub jeśli korzystasz z emulatora Azure Cosmos DB, przejdź do [kroku 2: Tworzenie nowej aplikacji ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

W następnej sekcji utworzysz nową aplikację ASP.NET Core MVC.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Krok 2. Tworzenie nowej aplikacji ASP.NET Core MVC

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**Znajdź i wybierz **ASP.NET Core aplikacji sieci Web** dla języka C#. Wybierz przycisk **Dalej**, aby kontynuować.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

1. W obszarze **Konfigurowanie nowego projektu**Nazwij projekt do *zrobienia* i wybierz pozycję **Utwórz**.

1. W obszarze **Utwórz nową aplikację sieci web ASP.NET Core**wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**. Wybierz pozycję **Utwórz** , aby kontynuować.

   Program Visual Studio tworzy pustą aplikację MVC.

1. Wybierz kolejno opcje **Debuguj**  >  **Rozpocznij debugowanie** lub F5, aby uruchomić aplikację ASP.NET lokalnie.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Krok 3. Dodawanie pakietu NuGet usługi Azure Cosmos DB do projektu

Teraz, gdy mamy najwięcej ASP.NET Core kodu platformy MVC dla tego rozwiązania, dodamy pakiety NuGet wymagane do nawiązania połączenia z Azure Cosmos DB.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**Wyszukaj i wybierz pozycję **Microsoft. Azure. Cosmos**. Wybierz pozycję **Zainstaluj**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

   Program Visual Studio pobiera i instaluje pakiet Azure Cosmos DB wraz z jego zależnościami.

   Do zainstalowania pakietu NuGet można także użyć **konsoli Menedżera pakietów** . W tym celu wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**. W wierszu polecenia wpisz następujące polecenie:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Krok 4. Konfigurowanie aplikacji ASP.NET Core MVC

Teraz Dodajmy modele, widoki i kontrolery do tej aplikacji MVC.

### <a name="add-a-model"></a><a name="add-a-model"></a> Dodawanie modelu

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **modele** , a następnie wybierz polecenie **Dodaj**  >  **klasę**.

1. W obszarze **Dodaj nowy element**nadaj nowej klasie nazwę *Item.cs* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość klasy *Item.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB używa formatu JSON do przenoszenia i przechowywania danych. Można użyć atrybutu, `JsonProperty` Aby kontrolować sposób serializacji kodu JSON i deserializacji obiektów. `Item`Klasa demonstruje `JsonProperty` atrybut. Ten kod steruje formatem nazwy właściwości, która znajduje się w formacie JSON. Zmienia również nazwę właściwości platformy .NET `Completed` .

### <a name="add-views"></a><a name="add-views"></a>Dodawanie widoków

Następnie Dodajmy następujące widoki.

* Widok tworzenia elementu
* Widok usuwania elementu
* Widok służący do pobierania szczegółów elementu
* Widok edycji elementu
* Widok, aby wyświetlić listę wszystkich elementów

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Widok tworzenia elementu

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **widoki** i wybierz polecenie **Dodaj**  >  **Nowy folder**. Nadaj nazwę *elementowi*folderu.

1. Kliknij prawym przyciskiem myszy folder pusty **element** , a następnie wybierz polecenie **Dodaj**  >  **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku**wpisz polecenie *Utwórz*.
   * W obszarze **szablon**wybierz pozycję **Utwórz**.
   * W obszarze **Klasa modelu**wybierz pozycję **element (do zrobienia. Modele)**.
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

1. Następnie wybierz pozycję **Dodaj** i Pozwól programowi Visual Studio utworzyć nowy widok szablonu. Zastąp kod w wygenerowanym pliku następującym zawartością:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Usuń widok elementu

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz polecenie **Dodaj**  >  **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku** wpisz *delete*.
   * W polu **szablon** wybierz pozycję **Usuń**.
   * W polu **Klasa modelu** wybierz pozycję **Item (todo.Models)**.
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

1. Następnie wybierz pozycję **Dodaj** i Pozwól programowi Visual Studio utworzyć nowy widok szablonu. Zastąp kod w wygenerowanym pliku następującym zawartością:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Dodaj widok, aby uzyskać szczegółowe informacje o elemencie

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz polecenie **Dodaj**  >  **Widok**.

1. W obszarze **Dodaj widok MVC**podaj następujące wartości:

   * W polu **Nazwa widoku**wprowadź *szczegóły*.
   * W obszarze **szablon**wybierz pozycję **szczegóły**.
   * W obszarze **Klasa modelu**wybierz pozycję **element (do zrobienia. Modele)**.
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.

1. Następnie wybierz pozycję **Dodaj** i Pozwól programowi Visual Studio utworzyć nowy widok szablonu. Zastąp kod w wygenerowanym pliku następującym zawartością:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Dodawanie widoku edycji elementu

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz polecenie **Dodaj**  >  **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku** wpisz *Edit* (Edycja).
   * W polu **Szablon** wybierz pozycję **Edycja**.
   * W polu **Klasa modelu** wybierz pozycję **Item (todo.Models)**.
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

1. Następnie wybierz pozycję **Dodaj** i Pozwól programowi Visual Studio utworzyć nowy widok szablonu. Zastąp kod w wygenerowanym pliku następującym zawartością:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Dodaj widok, aby wyświetlić listę wszystkich elementów

A wreszcie Dodaj widok, aby uzyskać wszystkie elementy, wykonując następujące czynności:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **elementu** , a następnie wybierz polecenie **Dodaj**  >  **Widok**.

1. W obszarze **Dodaj widok MVC**wprowadź następujące zmiany:

   * W polu **Nazwa widoku** wpisz *Index* (Indeks).
   * W polu **Szablon** wybierz pozycję **Lista**.
   * W polu **Klasa modelu** wybierz pozycję **Item (todo.Models)**.
   * Wybierz pozycję **Użyj strony układu** i wprowadź wartość *~/views/Shared/_Layout. cshtml*.
   * Wybierz pozycję **Dodaj**.

1. Następnie wybierz pozycję **Dodaj** i Pozwól programowi Visual Studio utworzyć nowy widok szablonu. Zastąp kod w wygenerowanym pliku następującym zawartością:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Po wykonaniu tych kroków zamknij wszystkie dokumenty *cshtml* w programie Visual Studio.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Zadeklaruj i zainicjuj usługi

Najpierw dodamy klasę zawierającą logikę umożliwiającą nawiązanie połączenia i użycie Azure Cosmos DB. W tym samouczku będziemy hermetyzować tę logikę do klasy o nazwie `CosmosDbService` i interfejsu o nazwie `ICosmosDbService` . Ta usługa wykonuje operacje CRUD. Wykonuje również operacje odczytu źródła danych, takie jak wyświetlanie niekompletnych elementów, tworzenie, edytowanie i usuwanie elementów.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**  >  **Nowy folder**. Nazwij *usługi*folderów.

1. Kliknij prawym przyciskiem myszy folder **usługi** , a następnie wybierz polecenie **Dodaj**  >  **klasę**. Nadaj nowej klasie nazwę *CosmosDbService* i wybierz pozycję **Dodaj**.

1. Zastąp zawartość *CosmosDbService.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Kliknij prawym przyciskiem myszy folder **usługi** , a następnie wybierz polecenie **Dodaj**  >  **klasę**. Nadaj nowej klasie nazwę *ICosmosDbService* i wybierz pozycję **Dodaj**.

1. Dodaj następujący kod do klasy *ICosmosDbService* :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Otwórz plik *Startup.cs* w rozwiązaniu i Dodaj poniższą metodę **InitializeCosmosClientInstanceAsync**, która odczytuje konfigurację i inicjuje klienta.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. W tym samym pliku Zastąp `ConfigureServices` metodę opcją:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   Kod w tym kroku inicjuje klienta w oparciu o konfigurację jako pojedyncze wystąpienie, które ma zostać dodane przez [iniekcję zależności w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

   Upewnij się, że domyślny kontroler MVC został zmieniony na `Item` , edytując trasy w `Configure` metodzie tego samego pliku:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Zdefiniuj konfigurację w *appsettings.jsprojektu na* pliku, jak pokazano w poniższym fragmencie kodu:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Dodawanie kontrolera

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **controllers** , a następnie wybierz pozycję **Dodaj**  >  **kontroler**.

1. W obszarze **Dodawanie szkieletu**wybierz pozycję **kontroler MVC — puste** i wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

1. Nazwij nowy kontroler *ItemController*.

1. Zastąp zawartość *ItemController.cs* następującym kodem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Atrybut **ValidateAntiForgeryToken** jest tu używany do ochrony aplikacji przed atakami polegającymi na fałszerstwie żądania międzywitrynowego. Twoje widoki powinny również współpracować z tym tokenem chroniącym przed fałszerstwem. Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [zapobieganie atakom przez wiele witryn (CSRF) w aplikacji ASP.NET MVC][Preventing Cross-Site Request Forgery]. Kod źródłowy dostępny w usłudze [GitHub][GitHub] zawiera pełną implementację.

Korzystamy również z atrybutu **Bind** dla parametru metody, aby ułatwić ochronę przed atakami polegającymi na przesyłaniu zmodyfikowanych akcji POST. Aby uzyskać więcej informacji, zobacz [Samouczek: Implementowanie funkcji CRUD przy użyciu Entity Framework w ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Krok 5. Uruchamianie aplikacji lokalnie

Aby przetestować aplikację na komputerze lokalnym, wykonaj następujące czynności:

1. Naciśnij klawisz F5 w programie Visual Studio, aby skompilować aplikację w trybie debugowania. Powinno to spowodować skompilowanie aplikacji i uruchomienie przeglądarki z wyświetloną stroną z pustą siatką, którą widzieliśmy wcześniej:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::
   
   Jeśli aplikacja zostanie otwarta na stronie głównej, Dołącz `/Item` do adresu URL.

1. Wybierz opcję **Utwórz nowe** łącze i Dodaj wartości do pól **Nazwa** i **Opis** . Pozostaw zaznaczone pole wyboru **ukończone** . W przypadku wybrania tej opcji aplikacja dodaje nowy element w stanie ukończone. Element nie jest już wyświetlany na liście początkowej.

1. Wybierz pozycję **Utwórz**. Aplikacja wyśle Cię z powrotem do widoku **indeksu** , a element zostanie wyświetlony na liście. Do listy **czynności do wykonania** można dodać kilka elementów.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::
  
1. Wybierz pozycję **Edytuj** obok **elementu** na liście. Aplikacja otwiera widok **edycji** , w którym można zaktualizować każdą właściwość obiektu, łącznie z flagą **ukończoną** . Jeśli wybierzesz pozycję **ukończono** i wybierzesz pozycję **Zapisz**, aplikacja wyświetli **element** jako wykonany na liście.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

1. Sprawdź stan danych w usłudze Azure Cosmos DB przy użyciu [Eksploratora Cosmos](https://cosmos.azure.com) lub Eksplorator danych emulatora Azure Cosmos DB.

1. Po przetestowaniu aplikacji wybierz kombinację klawiszy CTRL + F5, aby zatrzymać debugowanie aplikacji. Wszystko jest gotowe do wdrożenia.

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Krok 6. wdrażanie aplikacji

Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB, wdrożymy tę aplikację internetową w usłudze Azure App Service.  

1. Aby opublikować tę aplikację, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.

1. W obszarze **Wybierz element docelowy publikowania**wybierz pozycję **App Service**.

1. Aby użyć istniejącego profilu App Service, wybierz **pozycję Wybierz istniejące**, a następnie wybierz pozycję **Publikuj**.

1. W obszarze **App Service**wybierz **subskrypcję**. Za pomocą filtru **Widok** przeprowadź sortowanie według grupy zasobów lub typu zasobu.

1. Znajdź swój profil, a następnie wybierz przycisk **OK**. Następnie wyszukaj wymaganą usługę Azure App Service i wybierz przycisk **OK**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

Innym rozwiązaniem jest utworzenie nowego profilu:

1. Tak jak w poprzedniej procedurze, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.
  
1. W obszarze **Wybierz element docelowy publikowania**wybierz pozycję **App Service**.

1. W obszarze **Wybieranie elementu docelowego publikowania**wybierz pozycję **Utwórz nowy** i wybierz pozycję **Publikuj**.

1. W **App Service**wprowadź nazwę aplikacji sieci Web i odpowiednią subskrypcję, grupę zasobów i plan hostingu, a następnie wybierz pozycję **Utwórz**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Zrzut ekranu przedstawiający aplikację sieci Web MVC z listą zadań do zrobienia utworzoną w tym samouczku — Samouczek platformy ASP NET Core MVC krok po kroku":::

W ciągu kilku sekund program Visual Studio opublikuje aplikację sieci Web i uruchomi przeglądarkę, w której można zobaczyć swój projekt uruchomiony na platformie Azure.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia aplikacji sieci Web ASP.NET Core MVC. Aplikacja może uzyskiwać dostęp do danych przechowywanych w Azure Cosmos DB. Teraz możesz kontynuować z następującymi zasobami:

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](./partitioning-overview.md)
* [Wprowadzenie do zapytań SQL](./how-to-sql-query.md)
* [Jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu wziętego z życia](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
