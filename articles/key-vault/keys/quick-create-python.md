---
title: Szybki start — Azure Key Vault klienta języka Python — zarządzanie kluczami
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z usługi Azure Key Vault przy użyciu biblioteki klienta języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b1355acee5c115accb2515f797b402e66cd2ec0a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374730"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Szybki start: biblioteka klienta Azure Key Vault kluczy sieciowych dla języka Python

Wprowadzenie do biblioteki Azure Key Vault klienta dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Używając Key Vault do przechowywania kluczy kryptograficznych, unika się przechowywania takich kluczy w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja referencyjna interfejsu API](/python/api/overview/azure/keyvault-keys-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Pakiet (indeks pakietów języka Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ lub 3.6+](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą [biblioteki klienta tożsamości platformy Azure).](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="install-the-packages"></a>Instalowanie pakietów

1. W terminalu lub wierszu polecenia utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w tece Use Python virtual environments (Korzystanie ze środowisk [wirtualnych języka Python).](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Zainstaluj bibliotekę Azure Active Directory tożsamości:

    ```terminal
    pip install azure.identity
    ```


1. Zainstaluj bibliotekę Key Vault klienta klucza:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do kluczy tajnych kontu użytkownika.

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

Biblioteka Azure Key Vault klienta dla języka Python umożliwia zarządzanie kluczami kryptograficznymi. Poniższy przykład kodu pokazuje, jak utworzyć klienta, ustawić klucz, pobrać klucz i usunąć klucz.

Utwórz plik o *nazwie kv_keys.py zawierający* ten kod.

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

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku *o nazwie kv_keys.py.* Następnie uruchom kod za pomocą następującego polecenia:

```terminal
python kv_keys.py
```

- Jeśli wystąpią błędy uprawnień, upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu z taką samą nazwą klucza może spowodować błąd "(Konflikt) Klucz jest obecnie w stanie usunięcia, ale <name> można go odzyskać". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie jest to klasa  ["DefaultAzureCredential()",](/python/api/azure-identity/azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Zapisywanie klucza

Po uzyskaniu obiektu klienta dla magazynu kluczy możesz zapisać klucz przy użyciu [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) metody: 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Możesz również użyć [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) lub [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

Wywołanie metody `create` generuje wywołanie interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (jednostkę usługi) przy użyciu obiektu poświadczeń podanego klientowi.

## <a name="retrieve-a-key"></a>Pobieranie klucza

Aby odczytać klucz z Key Vault, użyj [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) metody :

```python
retrieved_key = client.get_key(keyName)
 ```

Możesz również sprawdzić, czy klucz został ustawiony za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

### <a name="delete-a-key"></a>Usuń klucz

Aby usunąć klucz, użyj [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) metody :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Metoda `begin_delete_key` jest asynchroniczna i zwraca obiekt Poller. Wywołanie metody `result` pollera czeka na jego ukończenie.

Możesz sprawdzić, czy klucz został usunięty, za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

Po usunięciu klucz pozostaje w stanie usuniętym, ale można go odzyskać przez jeden czas. Jeśli ponownie uruchamiasz kod, użyj innej nazwy klucza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również poeksperymentować z [certyfikatami](../certificates/quick-create-python.md) i wpisami [tajnymi,](../secrets/quick-create-python.md)możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu pracy z zasobami utworzonymi w tym artykule użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Bezpieczny dostęp do magazynu kluczy](../general/secure-your-key-vault.md)
- [Azure Key Vault dewelopera](../general/developers-guide.md)
- [Key Vault omówienie zabezpieczeń](../general/security-overview.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
