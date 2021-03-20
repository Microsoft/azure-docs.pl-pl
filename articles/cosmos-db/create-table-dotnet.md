---
title: 'Szybki start: interfejs API tabel za pomocą platformy .NET — Azure Cosmos DB'
description: Ten przewodnik szybkiego startu przedstawia wykorzystanie interfejsu API tabeli usługi Azure Cosmos DB do tworzenia aplikacji przy użyciu witryny Azure Portal i platformy .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096941"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Szybki Start: Tworzenie aplikacji interfejs API tabel przy użyciu zestawu .NET SDK i Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Ten przewodnik szybkiego startu pokazuje, jak używać programu .NET i [interfejsu API tabeli](table-introduction.md) usługi Azure Cosmos DB do tworzenia aplikacji przez sklonowanie przykładu z usługi GitHub. Ten przewodnik Szybki start pokazuje również, jak utworzyć konto usługi Azure Cosmos DB i jak korzystać z Eksploratora danych do tworzenia tabel i jednostek w witrynie internetowej Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Dodawanie tabeli

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Tabela z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

   ```bash
   md "C:\git-samples"
   ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

   ```bash
   cd "C:\git-samples"
   ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> Aby zapoznać się z bardziej szczegółowym omówieniem podobnego kodu, zobacz artykuł [Cosmos DB interfejs API tabel Sample](./tutorial-develop-table-dotnet.md) .

## <a name="open-the-sample-application-in-visual-studio"></a>Otwieranie przykładowej aplikacji w programie Visual Studio

1. W programie Visual Studio wybierz z menu **Plik** pozycję **Otwórz**, a następnie wybierz pozycję **Projekt/Rozwiązanie**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="Otwieranie rozwiązania"::: 

2. Przejdź do folderu, w którym Sklonowano przykładową aplikację, a następnie otwórz plik TableStorage. sln.

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz przejść do sekcji [aktualizowanie parametrów połączenia](#update-your-connection-string) tego dokumentu.

* Poniższy kod przedstawia sposób tworzenia tabeli w ramach usługi Azure Storage:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* Poniższy kod ilustruje sposób wstawiania danych do tabeli:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* Poniższy kod przedstawia sposób wykonywania zapytań dotyczących danych z tabeli:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* Poniższy kod przedstawia sposób usuwania danych z tabeli:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych. 

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Parametry połączenia**. Użyj przycisku kopiowania po prawej stronie okna, aby skopiować **PODSTAWOWE PARAMETRY POŁĄCZENIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Wyświetlanie i kopiowanie PODSTAWOWYCH PARAMETRÓW POŁĄCZENIA w okienku parametrów połączenia":::

2. W programie Visual Studio Otwórz **Settings.js** pliku. 

3. Wklej **podstawowe parametry połączenia** z portalu do wartości StorageConnectionString. Wklej parametry wewnątrz cudzysłowów.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Naciśnij kombinację klawiszy CTRL + S, aby zapisać **Settings.js** pliku.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Kompilowanie i wdrażanie aplikacji

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **CosmosTableSamples** w **Eksplorator rozwiązań** a następnie kliknij pozycję **Zarządzaj pakietami NuGet**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="Zarządzanie pakietami NuGet":::

2. W polu **wyszukiwania** NuGet wpisz Microsoft. Azure. Cosmos. Table. Spowoduje to wyszukanie biblioteki klienta interfejsu API tabeli usługi Cosmos DB. Ta biblioteka jest obecnie dostępna dla .NET Framework i .NET Standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Karta Przeglądaj pakietu NuGet":::

3. Kliknij przycisk **Instaluj** , aby zainstalować bibliotekę **Microsoft. Azure. Cosmos. Table** . Spowoduje to zainstalowanie pakietu interfejsu API tabeli usługi Azure Cosmos DB oraz wszystkich zależności.

4. Po uruchomieniu całej aplikacji przykładowe dane są wstawiane do jednostki tabeli i usuwane na końcu, aby nie były widoczne żadne dane wstawione w przypadku uruchomienia całego przykładu. Można jednak wstawić kilka punktów przerwania, aby wyświetlić dane. Otwórz plik BasicSamples. cs i kliknij prawym przyciskiem myszy wiersz 52, wybierz **punkt przerwania**, a następnie wybierz polecenie **Wstaw punkt przerwania**. Wstaw kolejny punkt przerwania w wierszu 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Dodawanie punktu przerwania"::: 

5. Naciśnij klawisz F5, aby uruchomić aplikację. W oknie konsoli jest wyświetlana nazwa nowej tabeli bazy danych (w tym przypadku demoa13b1) w Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Dane wyjściowe konsoli":::

   Po dotarciu do pierwszego punktu przerwania wróć do Eksploratora danych w witrynie Azure Portal. Kliknij przycisk **Odśwież**, rozwiń tabelę demo* i kliknij pozycję **Jednostki**. Karta **Jednostki** po prawej stronie zawiera nową jednostkę dodaną dla Waltera Harpa. Zauważ, że numer telefonu nowej jednostki to 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Nowa jednostka":::
    
   Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że nie można odnaleźć Settings.jsw pliku podczas uruchamiania projektu, można go rozwiązać, dodając następujący wpis XML do ustawień projektu. Kliknij prawym przyciskiem myszy pozycję CosmosTableSamples, wybierz pozycję Edytuj CosmosTableSamples. csproj i Dodaj następujący element: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zamknij kartę **jednostki** w Eksplorator danych.
    
7. Naciśnij klawisz F5, aby kontynuować pracę aplikacji do następnego punktu przerwania. 

    Po dotarciu do punktu przerwania wróć do witryny Azure Portal, kliknij ponownie pozycję **Jednostki** w celu otwarcia karty **Jednostki** i zauważ, że numer telefonu został zaktualizowany do 425-555-0105.

8. Naciśnij klawisz F5, aby kontynuować pracę aplikacji. 
 
   Aplikacja doda jednostki na potrzeby zaawansowanej przykładowej aplikacji, której interfejs API tabeli obecnie nie obsługuje. Następnie aplikacja usunie tabelę utworzoną przez przykładową aplikację.

9. W oknie konsoli naciśnij klawisz Enter, aby zakończyć wykonywanie aplikacji. 
  

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia tabeli za pomocą Eksploratora danych i uruchamiania aplikacji.  Teraz można tworzyć zapytania do danych przy użyciu interfejsu API tabel.  

> [!div class="nextstepaction"]
> [Importowanie danych tabeli do interfejsu API tabeli](table-import.md)