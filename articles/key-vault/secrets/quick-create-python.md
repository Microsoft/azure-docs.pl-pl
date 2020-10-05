---
title: Szybki Start — Azure Key Vault biblioteki klienckiej języka Python — zarządzanie wpisami tajnymi
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489208"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Korzystając z Key Vault do przechowywania wpisów tajnych, unikaj przechowywania wpisów tajnych w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Zainstaluj bibliotekę Key Vault Secret:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Uruchom następujące polecenie [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , aby autoryzować nazwę główną usługi dla operacji get, list i Set na wpisach tajnych. To polecenie opiera się na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` zmiennych środowiskowych i utworzonych w poprzednich krokach.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

To polecenie opiera się na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` zmiennych środowiskowych i utworzonych w poprzednich krokach.

Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu — interfejs wiersza polecenia](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Tworzenie przykładowego kodu

Biblioteka klienta Azure Key Vault dla języka Python umożliwia zarządzanie kluczami tajnymi i powiązanymi zasobami, takimi jak certyfikaty i klucze kryptograficzne. Poniższy przykład kodu demonstruje sposób tworzenia klienta, ustawiania wpisu tajnego, pobierania klucza tajnego i usuwania klucza tajnego.

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

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#give-the-service-principal-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie tajnej może spowodować wystąpienie błędu, "(konflikt) klucz tajny <name> jest obecnie w stanie usunięty, ale jest możliwy do odzyskania". Użyj innej nazwy wpisu tajnego.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W poprzednim kodzie [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) obiekt używa zmiennych środowiskowych utworzonych dla jednostki usługi. To poświadczenie należy podać za każdym razem, gdy utworzysz obiekt klienta z biblioteki platformy Azure, na przykład [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) wraz z identyfikatorem URI zasobu, z którym chcesz korzystać, za pośrednictwem tego klienta:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Zapisz klucz tajny

Po uzyskaniu obiektu klienta dla magazynu kluczy można przechowywać wpis tajny przy użyciu metody [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Wywołanie `set_secret` powoduje wygenerowanie wywołania interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.

Sprawdza również, czy obiekt wywołujący jest autoryzowany do wykonywania żądanych akcji. Przyznano tę autoryzację do jednostki usługi wcześniej przy użyciu [ `az keyvault set-policy` polecenia](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Aby odczytać wpis tajny z Key Vault, użyj metody [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) :

```python
retrieved_secret = client.get_secret(secretName)
 ```

Wartość wpisu tajnego jest zawarta w `retrieved_secret.value` .

Możesz również pobrać klucz tajny z poleceniem interfejsu wiersza polecenia platformy Azure [AZ klucz tajny show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Usuń klucz tajny

Aby usunąć wpis tajny, użyj metody [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Możesz sprawdzić, czy wpis tajny został usunięty za pomocą polecenia interfejsu CLI platformy Azure [AZ klucz tajny show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Po usunięciu wpis tajny pozostaje w stanie usunięty, ale można go odzyskać. Jeśli ponownie uruchomisz kod, użyj innej nazwy klucza tajnego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również eksperymentować z [certyfikatami](../certificates/quick-create-python.md) i [kluczami](../keys/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu tworzenia zasobów utworzonych w tym artykule Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- [Azure Key Vault najlepszych praktyk](../general/best-practices.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
