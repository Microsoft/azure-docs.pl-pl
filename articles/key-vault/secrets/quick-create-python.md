---
title: Szybki start — Azure Key Vault klienta języka Python — zarządzanie wpisami tajnymi
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu biblioteki klienta języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: e06881d078b4e881174c3e931f7898cb622ad7f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766357"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Szybki start: Azure Key Vault klienta dla języka Python

Wprowadzenie do biblioteki klienta Azure Key Vault tajnego dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Dzięki użyciu Key Vault do przechowywania wpisów tajnych można uniknąć przechowywania wpisów tajnych w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja referencyjna interfejsu API](/python/api/overview/azure/keyvault-secrets-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Package (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ lub 3.6+](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.


## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego
Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również używać Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą biblioteki klienta tożsamości [platformy Azure).](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="install-the-packages"></a>Instalowanie pakietów

1. W terminalu lub wierszu polecenia utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w tece Use Python virtual environments (Używanie środowisk [wirtualnych języka Python).](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Zainstaluj bibliotekę Azure Active Directory tożsamości:

    ```terminal
    pip install azure-identity
    ```


1. Zainstaluj bibliotekę Key Vault wpisów tajnych:

    ```terminal
    pip install azure-keyvault-secrets
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do klucza tajnego kontu użytkownika.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ta aplikacja używa nazwy magazynu kluczy jako zmiennej środowiskowej o nazwie `KEY_VAULT_NAME` .

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Tworzenie przykładowego kodu

Biblioteka Azure Key Vault klienta dla języka Python umożliwia zarządzanie wpisami tajnymi. W poniższym przykładzie kodu pokazano, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny.

Utwórz plik o *nazwie kv_secrets.py zawierający* ten kod.

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

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku *o nazwie kv_secrets.py.* Następnie uruchom kod za pomocą następującego polecenia:

```terminal
python kv_secrets.py
```

- Jeśli wystąpią błędy uprawnień, upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie secrete może spowodować błąd "(Konflikt) Klucz tajny jest obecnie w stanie usuniętym, ale <name> można go odzyskać". Użyj innej nazwy tajnej.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy  ["DefaultAzureCredential()",](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Zapisywanie tajnego

Po uzyskaniu obiektu klienta dla magazynu kluczy możesz zapisać klucz tajny przy użyciu [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) metody: 

```python
client.set_secret(secretName, secretValue)
```

Wywołanie `set_secret` funkcji generuje wywołanie interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość obiektu wywołującego (jednostkę usługi) przy użyciu obiektu poświadczeń podanego klientowi.

### <a name="retrieve-a-secret"></a>Pobieranie tajnego

Aby odczytać klucz tajny z Key Vault, użyj [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) metody :

```python
retrieved_secret = client.get_secret(secretName)
 ```

Wartość tajnego jest zawarta w `retrieved_secret.value` .

Możesz również pobrać klucz tajny za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show).

### <a name="delete-a-secret"></a>Usuń klucz tajny

Aby usunąć klucz tajny, użyj [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) metody :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

Metoda `begin_delete_secret` jest asynchroniczna i zwraca obiekt poller. Wywołanie metody `result` pollera czeka na jego ukończenie.

Możesz sprawdzić, czy klucz tajny został usunięty, za pomocą polecenia [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show)interfejsu wiersza polecenia platformy Azure.

Po usunięciu klucz tajny pozostaje w stanie usuniętym, ale można go odzyskać przez jeden czas. Jeśli ponownie uruchamiasz kod, użyj innej nazwy tajnego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również poeksperymentować z [certyfikatami](../certificates/quick-create-python.md) [i](../keys/quick-create-python.md)kluczami, możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu pracy z zasobami utworzonymi w tym artykule użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Bezpieczny dostęp do magazynu kluczy](../general/security-overview.md)
- [Azure Key Vault dewelopera](../general/developers-guide.md)
- [Key Vault omówienie zabezpieczeń](../general/security-overview.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
