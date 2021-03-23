---
title: Szybki Start — Azure Key Vault Biblioteka kliencka języka Python — zarządzanie kluczami
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b2c69b4358b99bfd4fff2a3434ff8fe956a2a1f5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801166"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Szybki Start: Biblioteka klienta kluczy Azure Key Vault dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Przy użyciu Key Vault do przechowywania kluczy kryptograficznych można uniknąć przechowywania takich kluczy w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-keys-readme)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + lub 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Ten przewodnik Szybki Start korzysta z biblioteki Azure Identity Library z interfejsem wiersza polecenia platformy Azure w celu uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również używać programu Visual Studio lub Visual Studio Code do uwierzytelniania wywołań, aby uzyskać więcej informacji, zobacz [uwierzytelnianie klienta przy użyciu biblioteki klienta tożsamości platformy Azure](/python/api/overview/azure/identity-readme).

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
    pip install azure.identity
    ```


1. Zainstaluj Key Vault kluczową bibliotekę klienta:

    ```terminal
    pip install azure-keyvault-keys
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

Biblioteka klienta klucza Azure Key Vault dla języka Python umożliwia zarządzanie kluczami kryptograficznymi. Poniższy przykład kodu demonstruje, jak utworzyć klienta, ustawić klucz, pobrać klucz i usunąć klucz.

Utwórz plik o nazwie *kv_keys. PR* , który zawiera ten kod.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku o nazwie *kv_keys. PR*. Następnie uruchom kod przy użyciu następującego polecenia:

```terminal
python kv_keys.py
```

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie klucza może spowodować wystąpienie błędu, "(konflikt) klucz <name> jest obecnie w stanie usunięty, ale". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy  ["DefaultAzureCredential ()"](/python/api/azure-identity/azure.identity.defaultazurecredential) , która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [domyślne uwierzytelnianie poświadczeń platformy Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Zapisz klucz

Po uzyskaniu obiektu klienta dla magazynu kluczy można przechowywać klucz za pomocą metody [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Można również użyć [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) lub [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

Wywołanie `create` metody generuje wywołanie interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.

## <a name="retrieve-a-key"></a>Pobierz klucz

Aby odczytać klucz z Key Vault, użyj metody [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) :

```python
retrieved_key = client.get_key(keyName)
 ```

Możesz również sprawdzić, czy klucz został ustawiony za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ Key magazynu show](/cli/azure/keyvault/key?#az-keyvault-key-show).

### <a name="delete-a-key"></a>Usuń klucz

Aby usunąć klucz, użyj metody [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Można sprawdzić, czy klucz jest usuwany za pomocą polecenia interfejsu CLI platformy Azure [AZ Key magazynu show](/cli/azure/keyvault/key?#az-keyvault-key-show).

Po usunięciu klucz pozostaje w stanie usunięty, ale jest w nim odwracalny. Jeśli ponownie uruchomisz kod, użyj innej nazwy klucza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również eksperymentować z [certyfikatami](../certificates/quick-create-python.md) i wpisami [tajnymi](../secrets/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

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
