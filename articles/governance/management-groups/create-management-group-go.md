---
title: 'Szybki Start: Tworzenie grupy zarządzania za pomocą języka go'
description: W tym przewodniku szybki start utworzysz grupę zarządzania w celu zorganizowania zasobów w hierarchii zasobów.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101774"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Szybki Start: Tworzenie grupy zarządzania za pomocą języka go

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Nazwa główna usługi platformy Azure, w tym _clientId_ i _clientSecret_. Jeśli nie masz nazwy głównej usługi do użycia z usługą Azure Policy lub chcesz utworzyć nową, zobacz [biblioteki zarządzania platformy Azure dla uwierzytelniania platformy .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Pomiń ten krok, aby zainstalować pakiety .NET Core, jak opisano w następnych krokach.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Dodawanie pakietu grupy zarządzania

Aby włączyć funkcję przejdź do zarządzania grupami zarządzania, należy dodać pakiet. Ten pakiet działa wszędzie tam, gdzie można użyć języka go, [w tym bash w systemie Windows 10](/windows/wsl/install-win10) lub zainstalowanym lokalnie.

1. Sprawdź, czy jest zainstalowana najnowsza wersja języka go (co najmniej **1,15**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [golang.org](https://golang.org/dl/).

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1**). Jeśli nie jest jeszcze zainstalowana, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby można było używać `auth.NewAuthorizerFromCLI()` metody go w poniższym przykładzie. Aby uzyskać informacje o innych opcjach, zobacz [Azure SDK dla języka go-więcej informacji o uwierzytelnianiu](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W wybranym środowisku środowiska go Zainstaluj wymagane pakiety dla grup zarządzania:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Konfiguracja aplikacji

Korzystając z pakietów języka go dodanych do wybranego środowiska, można skonfigurować aplikację go do tworzenia grupy zarządzania.

1. Utwórz aplikację go i Zapisz następujące źródło jako `mgCreate.go` :

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Kompiluj aplikację go:

   ```bash
   go build mgCreate.go
   ```

1. Utwórz grupę zarządzania przy użyciu skompilowanej aplikacji go. Zamień `<Name>` na nazwę nowej grupy zarządzania:

   ```bash
   mgCreate "<Name>"
   ```

Wynik jest nową grupą zarządzania w głównej grupie zarządzania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane pakiety ze środowiska go, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)