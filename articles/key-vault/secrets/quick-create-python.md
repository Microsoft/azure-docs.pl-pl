---
title: Szybki Start — Azure Key Vault biblioteki klienckiej języka Python — zarządzanie wpisami tajnymi
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1eb3728e9697cefc3621221e4a918656efcba3c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936638"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Szybki Start: Azure Key Vault Secret Client Library for Python

Rozpocznij pracę z biblioteką klienta tajnego Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Korzystając z Key Vault do przechowywania wpisów tajnych, unikaj przechowywania wpisów tajnych w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-secrets-readme)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + lub 3.5.3 +](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.


## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego
Ten przewodnik Szybki Start korzysta z biblioteki Azure Identity Library z interfejsem wiersza polecenia platformy Azure w celu uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również używać programu Visual Studio lub Visual Studio Code do uwierzytelniania wywołań, aby uzyskać więcej informacji, zobacz [uwierzytelnianie klienta przy użyciu biblioteki klienta tożsamości platformy Azure](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="install-the-packages"></a>Zainstaluj pakiety

1. W oknie terminalu lub wierszu polecenia Utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w temacie [Korzystanie z środowisk wirtualnych języka Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Zainstaluj bibliotekę tożsamości Azure Active Directory:

    ```terminal
    pip install azure-identity
    ```


1. Zainstaluj bibliotekę Key Vault Secret:

    ```terminal
    pip install azure-keyvault-secrets
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, które przyznaje poufne uprawnienia do Twojego konta użytkownika.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ta aplikacja używa nazwy magazynu kluczy jako zmiennej środowiskowej o nazwie `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS lub Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Tworzenie przykładowego kodu

Biblioteka klienta tajnego Azure Key Vault dla języka Python umożliwia zarządzanie kluczami tajnymi. Poniższy przykład kodu demonstruje sposób tworzenia klienta, ustawiania wpisu tajnego, pobierania klucza tajnego i usuwania klucza tajnego.

Utwórz plik o nazwie *kv_secrets. PR* , który zawiera ten kod.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku o nazwie *kv_secrets. PR*. Następnie uruchom kod przy użyciu następującego polecenia:

```terminal
python kv_secrets.py
```

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie tajnej może spowodować wystąpienie błędu, "(konflikt) klucz tajny <name> jest obecnie w stanie usunięty, ale jest możliwy do odzyskania". Użyj innej nazwy wpisu tajnego.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy  ["DefaultAzureCredential ()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) , która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [domyślne uwierzytelnianie poświadczeń platformy Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Zapisz klucz tajny

Po uzyskaniu obiektu klienta dla magazynu kluczy można przechowywać wpis tajny przy użyciu metody [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Wywołanie `set_secret` powoduje wygenerowanie wywołania interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Aby odczytać wpis tajny z Key Vault, użyj metody [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) :

```python
retrieved_secret = client.get_secret(secretName)
 ```

Wartość wpisu tajnego jest zawarta w `retrieved_secret.value` .

Możesz również pobrać klucz tajny z poleceniem interfejsu wiersza polecenia platformy Azure [AZ klucz tajny show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Usuń klucz tajny

Aby usunąć wpis tajny, użyj metody [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Możesz sprawdzić, czy wpis tajny został usunięty za pomocą polecenia interfejsu CLI platformy Azure [AZ klucz tajny show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

Po usunięciu wpis tajny pozostaje w stanie usunięty, ale można go odzyskać. Jeśli ponownie uruchomisz kod, użyj innej nazwy klucza tajnego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również eksperymentować z [certyfikatami](../certificates/quick-create-python.md) i [kluczami](../keys/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu tworzenia zasobów utworzonych w tym artykule Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Bezpieczny dostęp do magazynu kluczy](../general/secure-your-key-vault.md)
- [Przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- [Omówienie zabezpieczeń Key Vault](../general/security-overview.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
