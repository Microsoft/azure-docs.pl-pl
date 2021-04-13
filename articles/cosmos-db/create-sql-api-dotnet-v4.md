---
title: Zarządzanie zasobami Azure Cosmos DB API SQL przy użyciu zestawu SDK platformy .NET w wersji 4
description: 'Szybki start: tworzenie aplikacji konsolowej przy użyciu zestawu SDK platformy .NET w wersji 4 do zarządzania Azure Cosmos DB konta interfejsu API SQL.'
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 559ace3e9cb9f6daeb6dc3da581bb99d3ff9145e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365165"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Szybki start: tworzenie aplikacji konsolowej przy użyciu zestawu .NET V4 SDK (wersja zapoznawcza) do zarządzania Azure Cosmos DB konta interfejsu API SQL.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Rozpoczynanie pracy z Azure Cosmos DB klienta interfejsu API SQL dla platformy .NET. Postępuj zgodnie z instrukcjami w tym witrynie, aby zainstalować pakiet .NET V4 (Azure.Cosmos), skompilować aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w usłudze Azure Cosmos DB.

> [!IMPORTANT]
> Zestaw SDK platformy .NET w wersji 4 dla Azure Cosmos DB jest obecnie dostępny w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB to szybka baza danych NoSQL firmy Microsoft z otwartymi interfejsami API dla dowolnej skali. Za pomocą Azure Cosmos DB można szybko tworzyć bazy danych klucz/wartość, dokument i graf oraz tworzyć zapytania. Użyj biblioteki Azure Cosmos DB klienta interfejsu API SQL dla platformy .NET, aby:

* Tworzenie bazy danych i kontenera usługi Azure Cosmos
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Kod źródłowy biblioteki](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz](https://azure.microsoft.com/free/) [](https://azure.microsoft.com/try/cosmosdb/) ją bezpłatnie lub wypróbuj Azure Cosmos DB bezpłatnie bez subskrypcji platformy Azure — bezpłatnie i bez zobowiązań. 
* [ZESTAW SDK platformy NET Core 3.](https://dotnet.microsoft.com/download/dotnet-core) Możesz sprawdzić, która wersja jest dostępna w Twoim środowisku, uruchamiając program `dotnet --version` .

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera instrukcje tworzenia konta usługi Azure Cosmos i konfigurowania projektu, który używa biblioteki klienta interfejsu API SQL Azure Cosmos DB dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym artykule tworzy bazę danych i członków rodziny (każdy członek rodziny jest `FamilyDatabase` elementem) w tej bazie danych. Każdy członek rodziny ma właściwości takie jak `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . Właściwość `LastName` jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>tworzenie konta usługi Azure Cosmos

Jeśli używasz opcji [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) usługi Azure Cosmos, musisz utworzyć konto usługi Azure Cosmos DB typu INTERFEJS API **SQL.** Konto Azure Cosmos DB testowe zostało już utworzone. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub bezpłatnie utworzono subskrypcję, musisz jawnie utworzyć konto usługi Azure Cosmos. Poniższy kod spowoduje utworzenie konta usługi Azure Cosmos ze spójnością sesji. Konto jest replikowane w `South Central US` programach i `North Central US` .  

Konta usługi Azure Cosmos Azure Cloud Shell użyć do utworzenia konta usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Na przykład w tym przewodniku Szybki start wybierz **pozycję Tryb powłoki Bash.** Azure Cloud Shell wymaga również konta magazynu, możesz je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok poniższego kodu, wybierz pozycję Tryb **powłoki Bash,** a następnie wybierz pozycję **Utwórz konto** magazynu i zaloguj się do Cloud Shell. Następnie skopiuj i wklej następujący kod, aby Azure Cloud Shell i uruchom go. Nazwa konta usługi Azure Cosmos musi być globalnie unikatowa. Pamiętaj o zaktualizowaniu wartości przed `mysqlapicosmosdb` uruchomieniem polecenia.

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

Tworzenie konta usługi Azure Cosmos zajmuje trochę czasu. Po pomyślnym zakończeniu operacji można wyświetlić dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu polecenia zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Okno aplikacji można Azure Cloud Shell po utworzeniu zasobu. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji .NET

Utwórz nową aplikację .NET w preferowanym edytorze lub w preferowanym środowiskach IDE. Otwórz wiersz polecenia systemu Windows lub okno terminalu z komputera lokalnego. Wszystkie polecenia zostaną uruchomione w następnych sekcjach z wiersza polecenia lub terminalu.  Uruchom następujące polecenie dotnet new, aby utworzyć nową aplikację o nazwie `todo` . Parametr --langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować za pomocą:

   ```bash
   cd todo
   dotnet build
   ```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać podobnie do tych:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie Azure Cosmos DB pakietów

Nadal w katalogu aplikacji zainstaluj bibliotekę klienta Azure Cosmos DB dla programu .NET Core za pomocą polecenia dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Skopiuj poświadczenia konta usługi Azure Cosmos z Azure Portal

Przykładowa aplikacja wymaga uwierzytelnienia na koncie usługi Azure Cosmos. Aby się uwierzytelnić, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonać następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz okienko **Klucze** i skopiuj dane **URI** i **KLUCZ PODSTAWOWY** konta. W następnym kroku dodasz wartości URI i keys do zmiennej środowiskowej.

## <a name="object-model"></a><a id="object-model"></a>Model obiektów

Przed rozpoczęciem tworzenia aplikacji przyjrzyjmy się hierarchii zasobów w programie Azure Cosmos DB oraz modelowi obiektów używanego do tworzenia tych zasobów i uzyskiwania do nich dostępu. Ten Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Elementy

Aby dowiedzieć się więcej na temat hierarchii różnych jednostek, zobacz artykuł Praca z bazami danych, kontenerami [i elementami Azure Cosmos DB](account-databases-containers-items.md) artykule. Do interakcji z tymi zasobami użyjemy następujących klas .NET:

* CosmosClient — ta klasa zapewnia logiczną reprezentację usługi Azure Cosmos DB klienta. Obiekt klienta służy do konfigurowania i wykonywania żądań względem usługi.
* CreateDatabaseIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczną. 
* CreateContainerIfNotExistsAsync — ta metoda tworzy (jeśli nie istnieje) lub pobiera kontener (jeśli już istnieje) jako operację asynchroniczną. Możesz sprawdzić kod stanu z odpowiedzi, aby określić, czy kontener został nowo utworzony (201), czy został zwrócony istniejący kontener (200). 
* CreateItemAsync — ta metoda tworzy element w kontenerze.
* UpsertItemAsync — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje, lub zastępuje element, jeśli już istnieje. 
* GetItemQueryIterator — ta metoda tworzy zapytanie dotyczące elementów w kontenerze w bazie danych usługi Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowane. 
* DeleteAsync — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync` Metoda usuwa tylko bazę danych.

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy bazę danych rodziny w Azure Cosmos DB. Baza danych rodziny zawiera szczegóły rodziny, takie jak imię i nazwisko, adres, lokalizacja, powiązani z nią rodziców, dzieci i zwierzęta domowe. Przed wypełnieniem danych na koncie usługi Azure Cosmos zdefiniuj właściwości elementu rodziny. Utwórz nową klasę o nazwie na poziomie głównym przykładowej aplikacji i dodaj do niego `Family.cs` następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie dyrektyw using & definiowanie obiektu klienta

W katalogu projektu otwórz plik w edytorze i dodaj następujące dyrektywy `Program.cs` using w górnej części aplikacji:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Dodaj następujące zmienne globalne do `Program` swojej klasy. Będą one obejmować punkt końcowy i klucze autoryzacji, nazwę bazy danych i kontener, który utworzysz. Pamiętaj, aby zastąpić wartości punktu końcowego i kluczy autoryzacji zgodnie ze środowiskiem. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Na koniec zastąp `Main` metodę :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Tworzenie bazy danych 

`CreateDatabaseAsync`Zdefiniuj metodę w `program.cs` klasie . Ta metoda tworzy metodę `FamilyDatabase` , jeśli jeszcze nie istnieje.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Tworzenie kontenera

`CreateContainerAsync`Zdefiniuj metodę w `Program` klasie . Ta metoda tworzy metodę `FamilyContainer` , jeśli jeszcze nie istnieje. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Tworzenie elementu

Utwórz element rodziny, dodając `AddItemsToContainerAsync` metodę z następującym kodem. Aby utworzyć `CreateItemAsync` `UpsertItemAsync` element, można użyć metod lub :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Wykonywanie zapytań o elementy

Po wstawieniu elementu możesz uruchomić zapytanie, aby uzyskać szczegółowe informacje o rodzinie "Andersen". Poniższy kod pokazuje, jak wykonać zapytanie bezpośrednio przy użyciu zapytania SQL. Zapytanie SQL w celu uzyskania szczegółów rodziny "Anderson" to: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . `QueryItemsAsync`Zdefiniuj metodę `Program` w klasie i dodaj do niego następujący kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Zastępowanie elementu 

Odczytaj element rodziny, a następnie zaktualizuj go, dodając `ReplaceFamilyItemAsync` metodę za pomocą następującego kodu.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Usuwanie elementu 

Usuń element rodziny, dodając `DeleteFamilyItemAsync` metodę z następującym kodem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Usuwanie bazy danych 

Na koniec możesz usunąć bazę danych, `DeleteDatabaseAndCleanupAsync` dodając metodę za pomocą następującego kodu:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Po dodaniu wszystkich wymaganych metod zapisz `Program` plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następnie skompilować i uruchomić aplikację, aby utworzyć Azure Cosmos DB zasobów.

   ```bash
   dotnet run
   ```

Podczas uruchamiania aplikacji są generowane następujące dane wyjściowe. Możesz również zalogować się do Azure Portal i sprawdzić, czy zasoby zostały utworzone:

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

Możesz sprawdzić, czy dane zostały utworzone, logując się do Azure Portal i wyświetlić wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy konto usługi Azure Cosmos i odpowiednią grupę zasobów nie będą już potrzebne, możesz je usunąć za pomocą interfejsu wiersza polecenia platformy Azure Azure PowerShell usługi Azure. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Cosmos, tworzenia bazy danych i kontenera przy użyciu aplikacji platformy .NET Core. Teraz możesz zaimportować dodatkowe dane do konta usługi Azure Cosmos, zgodnie z instrukcjami podanymi w poniższym artykule. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
