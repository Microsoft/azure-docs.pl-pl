---
title: 'Szybki start: pierwsze zapytanie w języku Python'
description: W tym przewodniku Szybki start wykonaj kroki, aby włączyć bibliotekę Resource Graph dla języka Python i uruchomić pierwsze zapytanie.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0bae0639e3f9913bc47b18fbc0b1d3d9ef1ad324
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533011"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Szybki start: uruchamianie pierwszego zapytania Resource Graph przy użyciu języka Python

Pierwszym krokiem do korzystania z Azure Resource Graph jest sprawdzenie, czy są zainstalowane wymagane biblioteki dla języka Python. Ten przewodnik Szybki start umożliwia rozpoczęcie procesu dodawania bibliotek do instalacji języka Python.

Po zakończeniu tego procesu biblioteki zostaną dodane do instalacji języka Python i uruchomione pierwsze Resource Graph zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Dodawanie Resource Graph biblioteki

Aby umożliwić językowi Python wykonywanie Azure Resource Graph, należy dodać bibliotekę. Ta biblioteka działa wszędzie tam, gdzie można używać języka Python, w tym [bash na Windows 10](/windows/wsl/install-win10) lub lokalnie zainstalowany.

1. Sprawdź, czy jest zainstalowany najnowszy język Python (co najmniej **3.8).** Jeśli nie jest jeszcze zainstalowany, pobierz go ze [strony](https://www.python.org/downloads/)Python.org .

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1).** Jeśli nie jest jeszcze zainstalowany, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby umożliwić językowi Python korzystanie **z** uwierzytelniania opartego na interfejsie wiersza polecenia w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz Authenticate using the Azure management libraries for Python (Uwierzytelnianie [za pomocą bibliotek zarządzania platformy Azure dla języka Python).](/azure/developer/python/azure-sdk-authenticate)

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W swoim środowisku języka Python zainstaluj wymagane biblioteki dla języka Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Jeśli język Python jest zainstalowany dla wszystkich użytkowników, te polecenia muszą być uruchamiane z poziomu konsoli z podwyższonym poziomem uprawnień.

1. Sprawdź, czy biblioteki zostały zainstalowane. `azure-mgmt-resourcegraph` powinna mieć **wartość 2.0.0** lub wyższą, powinna mieć wartość `azure-mgmt-resource` **9.0.0** lub wyższą i powinna mieć wartość `azure-cli-core` **2.5.0 lub** wyższą.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Po dodaniu bibliotek języka Python do wybranego środowiska możesz wypróbować proste Resource Graph zapytania. Zapytanie zwraca pięć pierwszych zasobów platformy Azure z **nazwą i** **typem zasobu** każdego zasobu.

1. Uruchom pierwsze zapytanie Azure Resource Graph użyciu zainstalowanych bibliotek i `resources` metody :

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

1. Zaktualizuj wywołanie i `getresources` zmień zapytanie na właściwość `order by` Name: 

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie je zamówień.

1. Zaktualizuj wywołanie i zmień zapytanie na pierwszą właściwość Name, a `getresources` następnie na pięć pierwszych `order by`  `limit` wyników:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Gdy końcowe zapytanie jest uruchamiane kilka razy, przy założeniu, że nic się nie zmienia w twoim środowisku, zwrócone wyniki są spójne i uporządkowane według właściwości **Name,** ale nadal ograniczone do pięciu najlepszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki ze środowiska języka Python, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dodano biblioteki Resource Graph języka Python i uruchamiasz pierwsze zapytanie. Aby dowiedzieć się więcej o języku Resource Graph, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
