---
title: Szybki Start — Azure Key Vault Biblioteka kliencka języka Python — zarządzanie kluczami
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482139"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Szybki Start: Biblioteka klienta kluczy Azure Key Vault dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Przy użyciu Key Vault do przechowywania kluczy kryptograficznych można uniknąć przechowywania takich kluczy w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Zainstaluj bibliotekę kluczy Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Uruchom następujące polecenie [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , aby autoryzować nazwę główną usługi do operacji Delete, get, list i Create dla kluczy. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

To polecenie opiera się na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` zmiennych środowiskowych i utworzonych w poprzednich krokach.

Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu — interfejs wiersza polecenia](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Tworzenie przykładowego kodu

Biblioteka klienta Azure Key Vault dla języka Python umożliwia zarządzanie kluczami kryptograficznymi i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższy przykład kodu demonstruje sposób tworzenia klienta, ustawiania wpisu tajnego, pobierania klucza tajnego i usuwania klucza tajnego.

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

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#give-the-service-principal-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie klucza może spowodować wystąpienie błędu, "(konflikt) klucz <name> jest obecnie w stanie usunięty, ale". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W poprzednim kodzie [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) obiekt używa zmiennych środowiskowych utworzonych dla jednostki usługi. To poświadczenie należy podać za każdym razem, gdy utworzysz obiekt klienta z biblioteki platformy Azure, na przykład [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) wraz z identyfikatorem URI zasobu, z którym chcesz korzystać, za pośrednictwem tego klienta:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Zapisz klucz

Po uzyskaniu obiektu klienta dla magazynu kluczy można przechowywać klucz za pomocą metody [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Można również użyć [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) lub [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Wywołanie `create` metody generuje wywołanie interfejsu API REST platformy Azure dla magazynu kluczy.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.

Sprawdza również, czy obiekt wywołujący jest autoryzowany do wykonywania żądanych akcji. Przyznano tę autoryzację do jednostki usługi wcześniej przy użyciu [ `az keyvault set-policy` polecenia](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Pobierz klucz

Aby odczytać klucz z Key Vault, użyj metody [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) :

```python
retrieved_key = client.get_key(keyName)
 ```

Możesz również sprawdzić, czy klucz został ustawiony za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ Key magazynu show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Usuń klucz

Aby usunąć klucz, użyj metody [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Można sprawdzić, czy klucz jest usuwany za pomocą polecenia interfejsu CLI platformy Azure [AZ Key magazynu show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

Po usunięciu klucz pozostaje w stanie usunięty, ale jest w nim odwracalny. Jeśli ponownie uruchomisz kod, użyj innej nazwy klucza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również eksperymentować z [certyfikatami](../certificates/quick-create-python.md) i wpisami [tajnymi](../secrets/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu tworzenia zasobów utworzonych w tym artykule Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- [Azure Key Vault najlepszych praktyk](../general/best-practices.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
