---
title: 'Szybki Start: zapytanie w pierwszej kolejności'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające włączenie pakietu grafu zasobów dla języka go i uruchomienie pierwszego zapytania.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920066"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Szybki Start: uruchamianie pierwszego zapytania wykresu zasobów przy użyciu języka go

Pierwszym krokiem do korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy są zainstalowane wymagane pakiety dla języka go. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania pakietów do instalacji języka go.

Po zakończeniu tego procesu dodaliśmy pakiety do instalacji języka go i uruchomimy pierwsze zapytanie dotyczące grafu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-package"></a>Dodawanie pakietu grafu zasobów

Aby włączyć funkcję przejdź do grafu zasobów platformy Azure, należy dodać pakiet. Ten pakiet działa wszędzie tam, gdzie można użyć języka go, [w tym bash w systemie Windows 10](/windows/wsl/install-win10) lub zainstalowanym lokalnie.

1. Sprawdź, czy jest zainstalowana najnowsza wersja języka go (co najmniej **1,14**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [golang.org](https://golang.org/dl/).

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1**). Jeśli nie jest jeszcze zainstalowana, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby można było używać `auth.NewAuthorizerFromCLI()` metody go w poniższym przykładzie. Aby uzyskać informacje o innych opcjach, zobacz [Azure SDK dla języka go-więcej informacji o uwierzytelnianiu](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W wybranym środowisku środowiska go Zainstaluj wymagane pakiety dla wykresu zasobów platformy Azure:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Korzystając z pakietów języka go dodanych do wybranego środowiska, można wypróbować prostą kwerendę grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

1. Utwórz aplikację go i Zapisz następujące źródło jako `argQuery.go` :

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Kompiluj aplikację go:

   ```bash
   go build argQuery.go
   ```

1. Uruchamiaj pierwsze zapytanie dotyczące wykresu zasobów platformy Azure za pomocą skompilowanej aplikacji języka go. Zamień `<SubID>` na identyfikator subskrypcji:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zmień pierwszy parametr na `argQuery` i Zmień zapytanie na `order by` Właściwość **name** . Zamień `<SubID>` na identyfikator subskrypcji:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zmień pierwszy parametr na `argQuery` i Zmień zapytanie na najpierw `order by` Właściwość **name** , a następnie `limit` na pięć pierwszych wyników. Zamień `<SubID>` na identyfikator subskrypcji:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane pakiety ze środowiska go, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start Dodano pakiety wykresów zasobów do środowiska go i uruchomimy pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)