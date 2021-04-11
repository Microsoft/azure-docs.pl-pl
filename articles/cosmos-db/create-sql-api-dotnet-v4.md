---
title: Zarządzanie Azure Cosmos DB zasobami interfejsu API SQL przy użyciu zestawu .NET v4 SDK
description: Przewodnik Szybki Start dotyczący tworzenia aplikacji konsolowej przy użyciu zestawu .NET v4 SDK do zarządzania Azure Cosmos DB zasobów konta interfejsu API SQL.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8afa0ec51f86bd77e5539840f3419eaff89171f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107197"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Szybki Start: Tworzenie aplikacji konsolowej przy użyciu zestawu .NET v4 SDK (wersja zapoznawcza) w celu zarządzania Azure Cosmos DB zasobów konta interfejsu API SQL.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Wprowadzenie do Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET. Wykonaj kroki opisane w tym dokumencie, aby zainstalować pakiet .NET v4 (Azure. Cosmos), skompilować aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w Azure Cosmos DB.

> [!IMPORTANT]
> Zestaw SDK dla programu .NET v4 dla Azure Cosmos DB jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB to Microsoft Fast NoSQL Database z otwartymi interfejsami API dla dowolnej skali. Za pomocą Azure Cosmos DB można szybko tworzyć i badać bazy danych kluczy/wartości, dokumentów i grafów. Użyj Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET, aby:

* Tworzenie bazy danych i kontenera usługi Azure Cosmos
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Kod](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/) lub [wypróbuj bezpłatnie bezpłatne Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. 
* [Zestaw .NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Aby sprawdzić, która wersja jest dostępna w danym środowisku, należy uruchomić program `dotnet --version` .

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera szczegółowe instrukcje dotyczące tworzenia konta usługi Azure Cosmos oraz konfigurowania projektu korzystającego z Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym artykule tworzy `FamilyDatabase` bazę danych i członków rodziny (każdy członek rodziny jest elementem) w tej bazie danych. Każdy członek rodziny ma właściwości, takie jak `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Właściwość jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>tworzenie konta usługi Azure Cosmos

Jeśli używasz opcji [wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) , aby utworzyć konto usługi Azure Cosmos, musisz utworzyć konto Azure Cosmos DB typu **SQL API**. Konto testowe Azure Cosmos DB zostało już utworzone. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub masz subskrypcję bezpłatnie, należy utworzyć konto usługi Azure Cosmos jawnie. Poniższy kod utworzy konto usługi Azure Cosmos z spójnością sesji. Konto jest replikowane w `South Central US` i `North Central US` .  

Za pomocą Azure Cloud Shell można utworzyć konto usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Na potrzeby tego przewodnika Szybki Start wybierz tryb **bash** . Azure Cloud Shell również wymaga konta magazynu, można je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok poniższego kodu, wybierz tryb **bash** wybierz pozycję **Utwórz konto magazynu** i zaloguj się do Cloud Shell. Następnie skopiuj i wklej poniższy kod w celu Azure Cloud Shell i uruchomienia go. Nazwa konta usługi Azure Cosmos musi być globalnie unikatowa, pamiętaj o zaktualizowaniu `mysqlapicosmosdb` wartości przed uruchomieniem polecenia.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Tworzenie konta usługi Azure Cosmos zajmuje trochę czasu, po pomyślnym zakończeniu operacji można zobaczyć dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu wykonywania polecenia Zaloguj się do [Azure Portal](https://portal.azure.com/) i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Po utworzeniu zasobu możesz zamknąć okno Azure Cloud Shell. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji platformy .NET

Utwórz nową aplikację platformy .NET w preferowanym edytorze lub środowisku IDE. Otwórz wiersz polecenia systemu Windows lub okno terminalu na komputerze lokalnym. Wszystkie polecenia zostaną uruchomione w następnych sekcjach z poziomu wiersza polecenia lub terminalu.  Uruchom następujące polecenie dotnet New, aby utworzyć nową aplikację o nazwie `todo` . Parametr--langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

   ```bash
   cd todo
   dotnet build
   ```

Oczekiwane dane wyjściowe kompilacji powinny wyglądać następująco:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie pakietu Azure Cosmos DB

Nadal w katalogu aplikacji Zainstaluj Azure Cosmos DBą bibliotekę kliencką dla platformy .NET Core za pomocą polecenia dotnet Add Package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Skopiuj poświadczenia konta usługi Azure Cosmos z Azure Portal

Przykładowa aplikacja wymaga uwierzytelnienia na koncie usługi Azure Cosmos. Aby przeprowadzić uwierzytelnianie, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz okienko **klucze** i skopiuj **Identyfikator URI** i **klucz podstawowy** konta. W następnym kroku dodasz wartości identyfikatora URI i kluczy do zmiennej środowiskowej.

## <a name="object-model"></a><a id="object-model"></a>Model obiektów

Przed rozpoczęciem tworzenia aplikacji Przyjrzyjmy się hierarchii zasobów w Azure Cosmos DB i modelu obiektów używanym do tworzenia tych zasobów i uzyskiwania do nich dostępu. Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Elementy

Aby dowiedzieć się więcej o hierarchii różnych jednostek, zobacz [Praca z bazami danych, kontenerami i elementami w Azure Cosmos DB](account-databases-containers-items.md) artykule. Do korzystania z tych zasobów będziesz używać następujących klas platformy .NET:

* CosmosClient — Ta klasa udostępnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos DB. Obiekt klienta służy do konfigurowania i wykonywania żądań dotyczących usługi.
* CreateDatabaseIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczną. 
* CreateContainerIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) kontener jako operację asynchroniczną. Możesz sprawdzić kod stanu z odpowiedzi, aby określić, czy kontener został nowo utworzony (201) czy został zwrócony istniejący kontener (200). 
* CreateItemAsync — ta metoda tworzy element w kontenerze.
* UpsertItemAsync — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje, lub zastępuje element, jeśli już istnieje. 
* GetItemQueryIterator — ta metoda tworzy zapytanie dla elementów w kontenerze w bazie danych Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowane. 
* DeleteAsync — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync` Metoda usuwa tylko bazę danych.

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy bazę danych rodziny w Azure Cosmos DB. Baza danych rodziny zawiera szczegóły rodziny, takie jak nazwa, adres, lokalizacja, skojarzone elementy nadrzędne, dzieci i zwierzęta domowe. Przed zapełnieniem danych na koncie usługi Azure Cosmos Zdefiniuj właściwości elementu rodziny. Utwórz nową klasę o nazwie `Family.cs` na poziomie głównym aplikacji przykładowej i Dodaj do niej następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie dyrektyw using & Definiowanie obiektu klienta

W katalogu projektu Otwórz `Program.cs` plik w edytorze i Dodaj następujące dyrektywy using w górnej części aplikacji:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Dodaj następujące zmienne globalne do `Program` klasy. Będą to między innymi klucze punktu końcowego i autoryzacji, nazwa bazy danych i kontener, który zostanie utworzony. Pamiętaj, aby zastąpić wartości punktów końcowych i kluczy autoryzacji zgodnie ze środowiskiem. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Na koniec Zastąp `Main` metodę:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Tworzenie bazy danych 

Zdefiniuj `CreateDatabaseAsync` metodę w `program.cs` klasie. Ta metoda tworzy, `FamilyDatabase` Jeśli jeszcze nie istnieje.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tworzenie kontenera

Zdefiniuj `CreateContainerAsync` metodę w `Program` klasie. Ta metoda tworzy, `FamilyContainer` Jeśli jeszcze nie istnieje. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Tworzenie elementu

Utwórz element rodziny poprzez dodanie metody do `AddItemsToContainerAsync` następującego kodu. Możesz użyć `CreateItemAsync` metod lub, `UpsertItemAsync` Aby utworzyć element:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Zbadaj elementy

Po wstawieniu elementu można uruchomić zapytanie w celu uzyskania szczegółów rodziny "Andersen". Poniższy kod przedstawia sposób wykonywania zapytania bezpośrednio przy użyciu zapytania SQL. Zapytanie SQL, aby uzyskać szczegółowe informacje dotyczące rodziny "Anderson": `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Zdefiniuj `QueryItemsAsync` metodę w `Program` klasie i Dodaj do niej następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Zastępowanie elementu 

Odczytaj element rodziny, a następnie zaktualizuj go, dodając `ReplaceFamilyItemAsync` metodę do poniższego kodu.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Usuwanie elementu 

Usuń element rodziny poprzez dodanie `DeleteFamilyItemAsync` metody do następującego kodu.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Usuwanie bazy danych 

Na koniec można usunąć bazę danych, dodając do niej `DeleteDatabaseAndCleanupAsync` następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Po dodaniu wszystkich wymaganych metod Zapisz `Program` plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następnie Skompiluj i uruchom aplikację, aby utworzyć zasoby Azure Cosmos DB.

   ```bash
   dotnet run
   ```

Po uruchomieniu aplikacji generowane są następujące dane wyjściowe. Możesz również zalogować się do Azure Portal i sprawdzić, czy zasoby są tworzone:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Możesz sprawdzić, czy dane zostały utworzone, logując się do Azure Portal i zobacz wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć konto usługi Azure Cosmos i odpowiednią grupę zasobów. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, tworzenia bazy danych i kontenera przy użyciu aplikacji platformy .NET Core. Teraz możesz zaimportować dodatkowe dane do konta usługi Azure Cosmos za pomocą instrukcji int w następującym artykule. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
