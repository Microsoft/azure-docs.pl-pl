---
title: 'Szybki Start: pierwsze zapytanie w języku Python'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające włączenie biblioteki grafu zasobów dla języka Python i uruchomienie pierwszego zapytania.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 6fc7b8f6a6fa7cde8d10dec6a4b2b834d41325e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920126"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu języka Python

Pierwszym krokiem do korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy są zainstalowane wymagane biblioteki dla języka Python. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania bibliotek do instalacji języka Python.

Po zakończeniu tego procesu dodaliśmy biblioteki do instalacji języka Python i uruchomimy pierwsze zapytanie dotyczące wykresu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Dodawanie biblioteki grafu zasobów

Aby umożliwić językowi Python zapytanie do grafu zasobów platformy Azure, należy dodać bibliotekę. Ta biblioteka działa wszędzie tam, gdzie można korzystać z języka Python, [w tym bash w systemie Windows 10](/windows/wsl/install-win10) lub zainstalowanym lokalnie.

1. Sprawdź, czy jest zainstalowana najnowsza wersja języka Python (co najmniej **3,8**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [Python.org](https://www.python.org/downloads/).

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1**). Jeśli nie jest jeszcze zainstalowana, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby można było włączyć w języku Python użycie **uwierzytelniania opartego na interfejsie wiersza polecenia** w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz [uwierzytelnianie przy użyciu bibliotek zarządzania platformy Azure dla języka Python](/azure/developer/python/azure-sdk-authenticate).

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W wybranym środowisku języka Python Zainstaluj wymagane biblioteki na potrzeby grafu zasobów platformy Azure:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Jeśli język Python jest zainstalowany dla wszystkich użytkowników, polecenia te należy uruchomić z poziomu konsoli z podwyższonym poziomem uprawnień.

1. Sprawdź, czy biblioteki zostały zainstalowane. `azure-mgmt-resourcegraph` powinna być **2.0.0** lub wyższa, `azure-mgmt-resource` powinna być **9.0.0** lub większa i `azure-cli-core` powinna być **2.5.0** lub wyższa.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Dzięki bibliotekom języka Python dodanych do wybranego środowiska można wypróbować prostą kwerendę grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure przy użyciu zainstalowanych bibliotek i `resources` metody:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj wywołanie do `getresources` i Zmień zapytanie na `order by` Właściwość **name** :

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zaktualizuj wywołanie do `getresources` i Zmień zapytanie, tak aby najpierw miało `order by` Właściwość **name** , a następnie `limit` na pięć pierwszych wyników:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki ze środowiska języka Python, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dodano biblioteki grafu zasobów do środowiska języka Python i uruchomiono pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
