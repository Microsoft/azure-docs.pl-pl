---
title: Przewodnik Szybki Start dotyczący korzystania z usługi Azure App Configuration z aplikacjami języka Python | Microsoft Docs
description: W tym przewodniku szybki start utworzysz aplikację Python z konfiguracją aplikacji platformy Azure w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 954f4edcd10d701d00d9cd23280aaac7c287992d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91997468"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji w języku Python przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start będziesz używać konfiguracji aplikacji platformy Azure do scentralizowanego przechowywania i zarządzania ustawieniami aplikacji przy użyciu [biblioteki klienta konfiguracji aplikacji platformy Azure dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Python 2,7 lub 3,5 lub nowszy — Aby uzyskać informacje na temat konfigurowania języka Python w systemie Windows, zobacz [dokumentację języka Python w systemie Windows]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

8. Wybierz przycisk **Zastosuj**.

## <a name="setting-up-the-python-app"></a>Konfigurowanie aplikacji w języku Python

1. W tym samouczku utworzysz nowy katalog dla projektu o nazwie *App-Configuration-szybkiego startu*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Przejdź do nowo utworzonego katalogu *App-Configuration-szybkiego startu* .

    ```console
    cd app-configuration-quickstart
    ```

1. Zainstaluj bibliotekę klienta konfiguracji aplikacji platformy Azure za pomocą `pip install` polecenia.

    ```console
    pip install azure-appconfiguration
    ```

1. Utwórz nowy plik o nazwie *App-Configuration-quickstart.py* w katalogu *App-Configuration-szybkiego startu* i Dodaj następujący kod:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Fragmenty kodu w tym przewodniku szybki start pomogą Ci rozpocząć pracę z biblioteką klienta konfiguracji aplikacji dla języka Python. W przypadku aplikacji należy również rozważyć obsługę wyjątków zgodnie z potrzebami. Aby dowiedzieć się więcej o obsłudze wyjątków, zapoznaj się z [dokumentacją zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Konfigurowanie parametrów połączenia konfiguracji aplikacji

1. Ustaw zmienną środowiskową o nazwie **AZURE_APP_CONFIG_CONNECTION_STRING** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. W wierszu polecenia Uruchom następujące polecenie:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Ponownie uruchom wiersz polecenia, aby zmiany zaczęły obowiązywać. Wydrukuj wartość zmiennej środowiskowej, aby sprawdzić, czy została ona prawidłowo ustawiona.

## <a name="code-samples"></a>Przykłady kodu

Przykładowe fragmenty kodu w tej sekcji przedstawiają sposób wykonywania typowych operacji przy użyciu biblioteki klienta konfiguracji aplikacji dla języka Python. Dodaj te fragmenty kodu do `try` bloku w utworzonym wcześniej pliku *App-Configuration-quickstart.py* .

> [!NOTE]
> Biblioteka klienta konfiguracji aplikacji odwołuje się do obiektu klucz-wartość jako `ConfigurationSetting` . W związku z tym w tym artykule **wartości kluczy** w magazynie konfiguracji aplikacji będą określane jako **Ustawienia konfiguracji**.

* [Nawiązywanie połączenia z magazynem konfiguracji aplikacji](#connect-to-an-app-configuration-store)
* [Pobieranie ustawienia konfiguracji](#get-a-configuration-setting)
* [Dodaj ustawienie konfiguracji](#add-a-configuration-setting)
* [Pobierz listę ustawień konfiguracji](#get-a-list-of-configuration-settings)
* [Zablokuj ustawienie konfiguracji](#lock-a-configuration-setting)
* [Odblokuj ustawienie konfiguracji](#unlock-a-configuration-setting)
* [Aktualizowanie ustawienia konfiguracji](#update-a-configuration-setting)
* [Usuń ustawienie konfiguracji](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

Poniższy fragment kodu tworzy wystąpienie **AzureAppConfigurationClient** przy użyciu parametrów połączenia przechowywanych w zmiennych środowiskowych.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Pobieranie ustawienia konfiguracji

Poniższy fragment kodu pobiera ustawienia konfiguracji według `key` nazwy.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Dodaj ustawienie konfiguracji

Poniższy fragment kodu tworzy `ConfigurationSetting` obiekt z polami i `key` `value` wywołuje `add_configuration_setting` metodę. Ta metoda zgłosi wyjątek, jeśli spróbujesz dodać ustawienia konfiguracji, które już istnieje w sklepie. Jeśli chcesz uniknąć tego wyjątku, zamiast tego można użyć metody [set_configuration_setting](#update-a-configuration-setting) .

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Pobierz listę ustawień konfiguracji

Poniższy fragment kodu pobiera listę ustawień konfiguracji. `key_filter`Argumenty i `label_filter` mogą być podane w celu filtrowania wartości kluczy na podstawie `key` i `label` odpowiednio. Aby uzyskać więcej informacji na temat filtrowania, zobacz How to [Query Settings Configuration](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Zablokuj ustawienie konfiguracji

Stan blokady wartości klucz-wartość w konfiguracji aplikacji jest określany przez `read_only` atrybut `ConfigurationSetting` obiektu. Jeśli `read_only` jest `True` , ustawienie jest zablokowane. `set_read_only`Metodę można wywołać z `read_only=True` argumentem, aby zablokować ustawienie konfiguracji.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Odblokuj ustawienie konfiguracji

Jeśli `read_only` atrybut `ConfigurationSetting` ma wartość `False` , ustawienie jest odblokowane. `set_read_only`Metoda może być wywoływana z `read_only=False` argumentem w celu odblokowania ustawienia konfiguracji.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Aktualizowanie ustawienia konfiguracji

`set_configuration_setting`Metoda może służyć do aktualizowania istniejącego ustawienia lub tworzenia nowego ustawienia. Poniższy fragment kodu zmienia wartość istniejącego ustawienia konfiguracji.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Usuń ustawienie konfiguracji

Poniższy fragment kodu usuwa ustawienia konfiguracji według `key` nazwy.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Uruchamianie aplikacji

W tym przewodniku szybki start utworzono aplikację w języku Python korzystającą z biblioteki klienta konfiguracji aplikacji platformy Azure w celu pobrania ustawienia konfiguracji utworzonego za pośrednictwem Azure Portal, dodania nowego ustawienia, pobrania listy istniejących ustawień, zablokowania i odblokowania ustawienia, zaktualizowania ustawienia, a następnie usunięcia ustawienia.

W tym momencie plik *App-Configuration-quickstart.py* powinien mieć następujący kod:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

W oknie konsoli przejdź do katalogu zawierającego plik *App-Configuration-quickstart.py* i wykonaj następujące polecenie języka Python, aby uruchomić aplikację:

```console
python app-configuration-quickstart.py
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i dowiesz się, jak uzyskać dostęp do kluczowych wartości z aplikacji języka Python.

Aby uzyskać dodatkowe przykłady kodu, odwiedź stronę:

> [!div class="nextstepaction"]
> [Przykłady biblioteki klienta konfiguracji aplikacji platformy Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)