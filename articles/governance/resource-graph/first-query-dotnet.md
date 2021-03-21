---
title: 'Szybki Start: pierwsze zapytanie w programie .NET Core'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające włączenie pakietów NuGet grafu zasobów dla platformy .NET Core i uruchomienie pierwszego zapytania.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917575"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu platformy .NET Core

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest sprawdzenie, czy są zainstalowane wymagane pakiety dla platformy .NET Core. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania pakietów do instalacji programu .NET Core.

Po zakończeniu tego procesu dodaliśmy pakiety do instalacji programu .NET Core i uruchomimy pierwsze zapytanie dotyczące wykresu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Nazwa główna usługi platformy Azure, w tym _clientId_ i _clientSecret_. Jeśli nie masz nazwy głównej usługi do użycia z wykresem zasobów lub chcesz utworzyć nowy, zobacz [biblioteki zarządzania platformy Azure dla uwierzytelniania platformy .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Pomiń ten krok, aby zainstalować pakiety .NET Core, jak opisano w następnych krokach.

## <a name="create-the-resource-graph-project"></a>Tworzenie projektu grafu zasobów

Aby włączyć program .NET Core do wysyłania zapytań do grafu zasobów platformy Azure, Utwórz nową aplikację konsolową i zainstaluj wymagane pakiety.

1. Sprawdź, czy jest zainstalowany najnowszy program .NET Core (co najmniej **3.1.5**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Zainicjuj nową aplikację konsolową .NET Core o nazwie "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Zmień katalogi na folder nowego projektu i zainstaluj wymagane pakiety dla wykresu zasobów platformy Azure:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Zastąp wartość domyślną `program.cs` następującym kodem i Zapisz zaktualizowany plik:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Kompilowanie i publikowanie `argQuery` aplikacji konsolowej:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Dzięki skompilowanej i opublikowanej aplikacji konsolowej .NET Core należy wypróbować prostą kwerendę grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

W każdym wywołaniu do `argQuery` , istnieją zmienne, które są używane, należy zamienić na własne wartości:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{clientId}` -Zamień na identyfikator klienta nazwy głównej usługi
- `{clientSecret}` -Zamień na klucz tajny klienta jednostki usługi
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

1. Zmień katalogi na `{run-folder}` zdefiniowane za pomocą poprzedniego `dotnet publish` polecenia.

1. Uruchom pierwsze zapytanie grafu zasobów platformy Azure za pomocą skompilowanej aplikacji konsolowej .NET Core:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zmień parametr końcowy na `argQuery.exe` i Zmień zapytanie na `order by` Właściwość **name** :

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zmień parametr końcowy na `argQuery.exe` i Zmień zapytanie, tak aby najpierw miało `order by` Właściwość **name** , a następnie `limit` na pięć najważniejszych wyników:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć aplikację konsolową .NET Core i zainstalowane pakiety, możesz to zrobić, usuwając `argQuery` folder projektu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono aplikację konsolową .NET Core z wymaganymi pakietami wykresów zasobów i uruchomimy pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)