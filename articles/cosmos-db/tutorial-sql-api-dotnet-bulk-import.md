---
title: Zbiorcze importowanie danych do Azure Cosmos DB konta interfejsu API SQL przy użyciu zestawu .NET SDK
description: Dowiedz się, jak importować lub pozyskiwanie danych do Azure Cosmos DB przez utworzenie aplikacji konsolowej .NET, która optymalizuje przepływność (RU/s) wymaganą do importowania danych
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 09/21/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cf0e77657175449b126eeca02a12c164478e568
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548073"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Zbiorcze importowanie danych do Azure Cosmos DB konta interfejsu API SQL przy użyciu zestawu .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym samouczku przedstawiono sposób tworzenia aplikacji konsolowej .NET, która optymalizuje przepływność (RU/s) wymaganą do zaimportowania danych do Azure Cosmos DB. Ten artykuł zawiera informacje dotyczące odczytywania danych z przykładowego źródła danych i importowania ich do kontenera usługi Azure Cosmos.
W tym samouczku jest używana [wersja 3.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) programu Azure Cosmos DB .NET SDK, która może być przeznaczona dla .NET Framework lub .NET Core.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Konfigurowanie projektu
> * Nawiązywanie połączenia z kontem usługi Azure Cosmos z włączoną obsługą zbiorczą
> * Wykonywanie importu danych przez współbieżne operacje tworzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Zestaw .NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Aby sprawdzić, która wersja jest dostępna w danym środowisku, należy uruchomić program `dotnet --version` .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

[Utwórz konto Azure Cosmos DB interfejsu API SQL](create-cosmosdb-resources-portal.md) z Azure Portal lub możesz utworzyć konto za pomocą [emulatora Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Krok 2. Konfigurowanie projektu .NET

Otwórz wiersz polecenia systemu Windows lub okno terminalu na komputerze lokalnym. Wszystkie polecenia zostaną uruchomione w następnych sekcjach z poziomu wiersza polecenia lub terminalu. Uruchom następujące polecenie dotnet New, aby utworzyć nową aplikację o nazwie *Bulk-import-demonstracyjny*. `--langVersion`Parametr ustawia właściwość *LangVersion* w utworzonym pliku projektu.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Oczekiwane dane wyjściowe kompilacji powinny wyglądać następująco:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Krok 3. Dodawanie pakietu Azure Cosmos DB

Nadal w katalogu aplikacji Zainstaluj Azure Cosmos DBą bibliotekę kliencką dla platformy .NET Core za pomocą polecenia dotnet Add Package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Krok 4. Uzyskiwanie poświadczeń konta usługi Azure Cosmos

Przykładowa aplikacja wymaga uwierzytelnienia na koncie usługi Azure Cosmos. Aby przeprowadzić uwierzytelnianie, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące czynności:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1.  Przejdź do swojego konta usługi Azure Cosmos.
1.  Otwórz okienko **klucze** i skopiuj **Identyfikator URI** i **klucz podstawowy** konta.

Jeśli używasz emulatora Azure Cosmos DB, uzyskaj [poświadczenia emulatora z tego artykułu](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Krok 5. inicjowanie obiektu CosmosClient z obsługą wykonywania zbiorczego

Otwórz wygenerowany `Program.cs` plik w edytorze kodu. Zostanie utworzone nowe wystąpienie elementu CosmosClient z włączonym wykonaniem zbiorczym i będzie ono używane do wykonywania operacji w odniesieniu do Azure Cosmos DB. 

Zacznijmy od zastąpienia `Main` metody domyślnej i definiowania zmiennych globalnych. Te zmienne globalne będą obejmowały punkt końcowy i klucze autoryzacji, nazwę bazy danych, kontener, który zostanie utworzony, oraz liczbę elementów, które będą wstawiane zbiorczo. Pamiętaj, aby zastąpić wartości endpointURL i klucza autoryzacji zgodnie ze środowiskiem. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Wewnątrz `Main` metody Dodaj następujący kod, aby zainicjować obiekt CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Po włączeniu wykonywania zbiorczego CosmosClient wewnętrznie grupuje współbieżne operacje w ramach pojedynczych wywołań usługi. W ten sposób optymalizuje wykorzystanie przepływności przez dystrybucję wywołań usługi między partycjami, a na koniec przypisuje poszczególne wyniki do oryginalnych wywołujących.

Następnie można utworzyć kontener do przechowywania wszystkich elementów.  Zdefiniuj `/pk` jako klucz partycji 50000 ru/s jako przepływność administracyjną, a także niestandardowe zasady indeksowania, które wykluczają wszystkie pola w celu zoptymalizowania przepływności zapisu. Dodaj następujący kod po instrukcji inicjowania CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Krok 6. wypełnienie listy współbieżnych zadań

Aby skorzystać z pomocy technicznej wykonywania zbiorczego, należy utworzyć listę zadań asynchronicznych na podstawie źródła danych i operacji, które mają zostać wykonane, i użyć `Task.WhenAll` ich do wykonania współbieżnie.
Zacznijmy od użycia "fałszywe" dane w celu wygenerowania listy elementów z naszego modelu danych. W świecie rzeczywistym elementy pochodzą z żądanego źródła danych.

Najpierw Dodaj fikcyjny pakiet do rozwiązania przy użyciu polecenia dotnet Add Package.

   ```bash
   dotnet add package Bogus
   ```

Zdefiniuj definicję elementów, które chcesz zapisać. Należy zdefiniować `Item` klasę w `Program.cs` pliku:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Następnie Utwórz funkcję pomocnika wewnątrz `Program` klasy. Ta funkcja pomocnika Pobiera liczbę elementów zdefiniowanych do wstawienia i wygenerowania losowych danych:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Odczytaj elementy i serializować je do wystąpień strumienia przy użyciu `System.Text.Json` klasy. Ze względu na charakter danych generowanych automatycznie, serializacja danych jest serializowana jako strumienie. Można również użyć wystąpienia elementu bezpośrednio, ale przez konwersję do strumieni, można wykorzystać wydajność interfejsów API strumienia w CosmosClient. Zwykle dane można używać bezpośrednio, o ile znasz klucz partycji. 


Aby przekonwertować dane na wystąpienia strumienia, w `Main` metodzie Dodaj następujący kod bezpośrednio po utworzeniu kontenera:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Następnie użyj strumieni danych, aby utworzyć współbieżne zadania i wypełnić listę zadań, aby wstawić elementy do kontenera. Aby wykonać tę operację, Dodaj następujący kod do `Program` klasy:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Wszystkie te współbieżne operacje na punktach zostaną wykonane razem (zbiorczo) zgodnie z opisem w sekcji wprowadzenie.

## <a name="step-7-run-the-sample"></a>Krok 7. Uruchamianie przykładu

Aby uruchomić przykład, można to zrobić po prostu za pomocą `dotnet` polecenia:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Pobierz kompletny przykład

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać przykłady kodu, możesz uzyskać je w serwisie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Po sklonowaniu projektu upewnij się, że Zaktualizowano wymagane poświadczenia w [program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25).

Przykład można uruchomić, zmieniając katalog repozytorium i używając `dotnet` :

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Konfigurowanie projektu
> * Nawiązywanie połączenia z kontem usługi Azure Cosmos z włączoną obsługą zbiorczą
> * Wykonywanie importu danych przez współbieżne operacje tworzenia

Teraz możesz przechodzić do następnego samouczka:

> [!div class="nextstepaction"]
>[wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API SQL](tutorial-query-sql-api.md)