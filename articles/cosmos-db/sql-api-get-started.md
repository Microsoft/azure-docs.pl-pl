---
title: 'Samouczek: Tworzenie aplikacji konsolowej .NET do zarządzania danymi w Azure Cosmos DB koncie interfejsu API SQL'
description: 'Samouczek: informacje na temat tworzenia Azure Cosmos DB zasobów interfejsu API SQL przy użyciu aplikacji konsolowej C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 912b4966312d8925f70deeed99042d2701641f49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801515"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Samouczek: Tworzenie aplikacji konsolowej .NET do zarządzania danymi w Azure Cosmos DB koncie interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Witamy w samouczku wprowadzenie do interfejsu API SQL Azure Cosmos DB. W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

W tym samouczku jest używane [oprogramowanie Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)w wersji 3,0 lub nowszej. Można korzystać z [.NET Framework lub .NET Core](https://dotnet.microsoft.com/download).

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie konta usługi Azure Cosmos i łączenie się z nim
> * Konfigurowanie projektu w programie Visual Studio
> * Tworzenie bazy danych i kontenera
> * Dodawanie elementów do kontenera
> * Wykonywanie zapytań względem kontenera
> * Wykonywanie operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na elemencie
> * Usuwanie bazy danych

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Przeskocz do sekcji [Pobieranie kompletnego rozwiązania samouczka](#GetSolution), aby uzyskać krótkie instrukcje.

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, Pomiń tę sekcję. Aby użyć emulatora Azure Cosmos DB, postępuj zgodnie z instrukcjami w [Azure Cosmos DB emulator](local-emulator.md) , aby skonfigurować emulator. Następnie przejdź do [kroku 2: Konfigurowanie projektu programu Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Krok 2. Konfigurowanie projektu programu Visual Studio

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C#, a następnie wybierz pozycję **dalej**.
1. Nazwij projekt *CosmosGettingStartedTutorial*, a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Konfigurowanie projektu":::

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w rozwiązaniu programu Visual Studio, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj** i wyszukaj ciąg *Microsoft. Azure. Cosmos*. Wybierz pozycję **Microsoft. Azure. Cosmos** i wybierz pozycję **Zainstaluj**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Konfigurowanie projektu":::

   Identyfikator pakietu biblioteki klienta interfejsu API SQL w usłudze Azure Cosmos DB to [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Świetnie! Teraz, po zakończeniu konfigurowania, zacznijmy pisanie kodu. Aby zapoznać się z kompletnym projektem tego samouczka, zobacz [Tworzenie aplikacji konsolowej .NET przy użyciu Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB

1. Zastąp odwołania na początku aplikacji C# w pliku *program.cs* , korzystając z następujących odwołań:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Dodaj te stałe i zmienne do `Program` klasy.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Jeśli znasz poprzednią wersję zestawu .NET SDK, możesz zapoznać się z tematem *zbieranie* terminów i *dokument*. Ponieważ Azure Cosmos DB obsługuje wiele modeli interfejsów API, wersja 3,0 zestawu .NET SDK używa *kontenera* warunków ogólnych i *elementu*. *Kontener* może być kolekcją, wykresem lub tabelą. *Element* może być dokumentem, krawędzią/wierzchołkiem lub wierszem, a jest zawartością wewnątrz kontenera. Aby uzyskać więcej informacji, zobacz [Working with Databases, Containers and items in Azure Cosmos DB](databases-containers-items.md).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Znajdź konto Azure Cosmos DB, a następnie wybierz pozycję **klucze**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Konfigurowanie projektu":::

1. W *program.cs*, Zastąp `<your endpoint URL>` wartość **identyfikatorem URI**. Zamień `<your primary key>` na wartość **klucza podstawowego**.

1. Poniżej metody **Main** Dodaj nowe zadanie asynchroniczne o nazwie **GetStartedDemoAsync**, które tworzy wystąpienie naszego nowego `CosmosClient` .

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Używamy **GetStartedDemoAsync** jako punktu wejścia, który wywołuje metody, które działają na Azure Cosmos DB zasobach.

1. Dodaj następujący kod w celu uruchomienia zadania asynchronicznego **GetStartedDemoAsync** w metodzie **Main**. Metoda **Main** przechwytuje wyjątki i wyświetla je w konsoli.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Wybierz klawisz F5, aby uruchomić aplikację.

    W konsoli programu jest wyświetlany komunikat: **koniec demonstracyjnej, naciśnij dowolny klawisz, aby wyjść.** Ten komunikat potwierdza, że aplikacja wykona połączenie z Azure Cosmos DB. Następnie można zamknąć okno konsoli.

Gratulacje! Pomyślnie nawiązano połączenie z kontem Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych

Baza danych jest logicznym kontenerem elementów podzielonym na partycje w kontenerach. Albo `CreateDatabaseIfNotExistsAsync` `CreateDatabaseAsync` metoda klasy [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) może utworzyć bazę danych.

1. Skopiuj i wklej `CreateDatabaseAsync` metodę poniżej `GetStartedDemoAsync` metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` tworzy nową bazę danych o IDENTYFIKATORze `FamilyDatabase` , jeśli jeszcze nie istnieje, która ma identyfikator określony na podstawie tego `databaseId` pola.

1. Skopiuj i wklej poniższy kod, gdzie tworzysz wystąpienie CosmosClient, aby wywołać metodę **CreateDatabaseAsync** , która właśnie została dodana.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Twoja *program.cs* powinna teraz wyglądać następująco, a punkt końcowy i wypełniony klucz podstawowy zostały wypełnione.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

   > [!NOTE]
   > Jeśli wystąpi błąd "wyjątek niedostępności usługi 503", istnieje możliwość, że wymagane [porty](performance-tips.md#networking) dla trybu łączności bezpośredniej są blokowane przez zaporę. Aby rozwiązać ten problem, należy otworzyć wymagane porty lub użyć łączności w trybie bramy, jak pokazano w poniższym kodzie:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gratulacje! Baza danych Azure Cosmos została pomyślnie utworzona.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Krok 5. Tworzenie kontenera

> [!WARNING]
> Metoda `CreateContainerIfNotExistsAsync` tworzy nowy kontener, który ma wpływ na ceny. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Kontener można utworzyć przy użyciu metody [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) lub [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) w `CosmosDatabase` klasie. Kontener składa się z elementów (dokumentów JSON, jeśli interfejs API SQL) i skojarzonej logiki aplikacji po stronie serwera w języku JavaScript, na przykład procedury składowane, funkcje zdefiniowane przez użytkownika i wyzwalacze.

1. Skopiuj i wklej `CreateContainerAsync` metodę poniżej `CreateDatabaseAsync` metody. `CreateContainerAsync`  Tworzy nowy kontener o IDENTYFIKATORze `FamilyContainer` , jeśli jeszcze nie istnieje, przy użyciu identyfikatora określonego w `containerId` polu partycjonowane według `LastName` właściwości.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Poniższy kod umożliwia wywołanie właśnie dodanej metody **CreateContainer**. Skopiuj go i wklej pod wierszem, w którym jest tworzone wystąpienie klasy CosmosClient.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono kontener usługi Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Krok 6. Dodawanie elementów do kontenera

Metoda [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) `CosmosContainer` klasy może utworzyć element. W przypadku korzystania z interfejsu API SQL elementy są rzutowane jako dokumenty zdefiniowane przez użytkownika jako dowolnie zawartość JSON. Teraz można wstawić element do kontenera usługi Azure Cosmos.

Najpierw Utwórzmy `Family` klasę reprezentującą obiekty przechowywane w Azure Cosmos DB w tym przykładzie. Utworzymy również `Parent` klasy, `Child` ,, `Pet` , `Address` które są używane w programie `Family` . Element musi mieć `Id` serializowaną właściwość w formacie `id` JSON.

1. Wybierz kombinację klawiszy Ctrl + Shift + A, aby otworzyć **pozycję Dodaj nowy element**. Dodaj nową klasę `Family.cs` do projektu.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Konfigurowanie projektu":::

1. Skopiuj i wklej `Family` klasy, `Parent` , `Child` , `Pet` i `Address` do `Family.cs` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Wróć do *program.cs*, Dodaj `AddItemsToContainerAsync` metodę po `CreateContainerAsync` metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Kod sprawdza, czy element o takim samym IDENTYFIKATORze jeszcze nie istnieje. Będziemy wstawiać dwa elementy, jeden dla *rodziny Andersen* i *rodziny Wakefield*.

1. Dodaj wywołanie metody `AddItemsToContainerAsync` w metodzie `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie utworzono dwa elementy usługi Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane zapytania względem dokumentów JSON przechowywanych w poszczególnych kontenerach. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z zapytaniami SQL](sql-api-sql-query.md). W następującym przykładowym kodzie pokazano, jak uruchomić zapytanie względem elementów wstawionych w poprzednim kroku.

1. Skopiuj i wklej `QueryItemsAsync` metodę po `AddItemsToContainerAsync` metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Dodaj wywołanie metody ``QueryItemsAsync`` w metodzie ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie wykonano zapytanie o kontener usługi Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Krok 8. Zastępowanie elementu JSON

Teraz zaktualizujemy element w Azure Cosmos DB. Zmienimy `IsRegistered` Właściwość `Family` i dla `Grade` jednego z elementów podrzędnych.

1. Skopiuj i wklej `ReplaceFamilyItemAsync` metodę po `QueryItemsAsync` metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Dodaj wywołanie metody `ReplaceFamilyItemAsync` w metodzie `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie zamieniono element usługi Azure Cosmos.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Krok 9. Usuwanie elementu

Teraz usuniemy element w Azure Cosmos DB.

1. Skopiuj i wklej `DeleteFamilyItemAsync` metodę po `ReplaceFamilyItemAsync` metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Dodaj wywołanie metody `DeleteFamilyItemAsync` w metodzie `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto element usługi Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych

Teraz usuniemy naszą bazę danych. Usunięcie utworzonej bazy danych spowoduje usunięcie bazy danych i wszystkich zasobów podrzędnych. Zasoby obejmują kontenery, elementy i wszystkie procedury składowane, funkcje zdefiniowane przez użytkownika i wyzwalacze. Usuwamy również `CosmosClient` wystąpienie.

1. Skopiuj i wklej `DeleteDatabaseAndCleanupAsync` metodę po `DeleteFamilyItemAsync` metodzie.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Dodaj wywołanie metody ``DeleteDatabaseAndCleanupAsync`` w metodzie ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Wybierz klawisz F5, aby uruchomić aplikację.

Gratulacje! Pomyślnie usunięto bazę danych usługi Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej C#

Naciśnij klawisz F5 w programie Visual Studio, aby skompilować i uruchomić aplikację w trybie debugowania.

Wszystkie dane wyjściowe aplikacji powinny zostać wyświetlone w oknie konsoli. Dane wyjściowe pokazują wyniki dodanych zapytań. Powinien być zgodny z przykładowym tekstem poniżej.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gratulacje! Pomyślnie ukończono ten samouczek i utworzono działającą aplikację konsolową C#.

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Pobieranie kompletnego rozwiązania samouczka

Jeśli nie masz czasu na wykonanie kroków opisanych w tym samouczku lub po prostu chcesz pobrać przykłady kodu, możesz ją pobrać.

Do skompilowania `GetStarted` rozwiązania potrzebne są następujące wymagania wstępne:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* [Konto usługi Azure Cosmos DB][cosmos-db-create-account].
* Rozwiązanie [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostępne w witrynie GitHub.

Aby przywrócić odwołania do zestawu SDK programu Azure Cosmos DB .NET w programie Visual Studio, kliknij prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań**, a następnie wybierz polecenie **Przywróć pakiety NuGet**. Następnie w pliku *App.config* zaktualizuj `EndPointUri` wartości i, zgodnie z `PrimaryKey` opisem w [sekcji Krok 3. nawiązywanie połączenia z kontem Azure Cosmos DB](#Connect).

To wszystko — skompiluj projekt i gotowe!

## <a name="next-steps"></a>Następne kroki

* Potrzebujesz bardziej złożonego samouczka platformy ASP.NET MVC? Zobacz [Samouczek: Tworzenie aplikacji sieci web ASP.NET Core MVC z Azure Cosmos dB przy użyciu zestawu .NET SDK](sql-api-dotnet-application.md).
* Chcesz przeprowadzić testowanie skalowalności i wydajności za pomocą Azure Cosmos DB? Zobacz [testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](performance-testing.md).
* Aby dowiedzieć się, jak monitorować żądania Azure Cosmos DB, użycie i magazyn, zobacz [monitorowanie metryk wydajności i magazynu w Azure Cosmos DB](monitor-accounts.md).
* Aby uruchamiać zapytania względem naszego przykładowego zestawu danych, zobacz [środowisko testowe zapytań](https://www.documentdb.com/sql/demo).
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](https://docs.microsoft.com/azure/cosmos-db/introduction)

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
