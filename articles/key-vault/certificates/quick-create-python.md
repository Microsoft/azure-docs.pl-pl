---
title: Szybki Start — Azure Key Vault biblioteki klienckiej języka Python — zarządzanie certyfikatami
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488630"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Szybki Start: Biblioteka klienta Azure Key Vault Certificates dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Przy użyciu Key Vault do przechowywania certyfikatów można uniknąć przechowywania certyfikatów w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Zainstaluj bibliotekę certyfikatów Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Uruchom następujące polecenie [AZ Certificate Certificate Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , aby autoryzować nazwę główną usługi na potrzeby operacji get, list i Create na certyfikatach.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

To polecenie opiera się na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` zmiennych środowiskowych i utworzonych w poprzednich krokach.

Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu — interfejs wiersza polecenia](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Tworzenie przykładowego kodu

Biblioteka klienta Azure Key Vault dla języka Python umożliwia zarządzanie certyfikatami i powiązanymi zasobami, takimi jak wpisy tajne i klucze kryptograficzne. Poniższy przykład kodu demonstruje sposób tworzenia klienta, ustawiania wpisu tajnego, pobierania klucza tajnego i usuwania klucza tajnego.

Utwórz plik o nazwie *kv_certificates. PR* , który zawiera ten kod.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku o nazwie *kv_certificates. PR*. Następnie uruchom kod przy użyciu następującego polecenia:

```terminal
python kv_certificates.py
```

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#give-the-service-principal-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie klucza może spowodować wystąpienie błędu, "(konflikt) certyfikat <name> jest obecnie usunięty, ale stan odzyskiwalny". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W poprzednim kodzie [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) obiekt używa zmiennych środowiskowych utworzonych dla jednostki usługi. To poświadczenie należy podać za każdym razem, gdy utworzysz obiekt klienta z biblioteki platformy Azure, na przykład [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) wraz z identyfikatorem URI zasobu, z którym chcesz korzystać, za pośrednictwem tego klienta:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Zapisz certyfikat

Po uzyskaniu obiektu klienta dla magazynu kluczy można utworzyć certyfikat przy użyciu metody [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

W tym miejscu certyfikat wymaga zasad uzyskanych z użyciem metody [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) .

Wywołanie `begin_create_certificate` metody generuje asynchroniczne wywołanie interfejsu API REST platformy Azure dla magazynu kluczy. Wywołanie asynchroniczne zwraca obiekt sondowający. Aby poczekać na wynik operacji, wywołaj metodę sondowania `result` .

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.

Sprawdza również, czy obiekt wywołujący jest autoryzowany do wykonywania żądanych akcji. Przyznano tę autoryzację do jednostki usługi wcześniej przy użyciu [ `az keyvault set-policy` polecenia](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Pobierz certyfikat

Aby odczytać certyfikat z Key Vault, użyj metody [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Możesz również sprawdzić, czy certyfikat został ustawiony za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ, Certificate webmagazynu show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Aby usunąć certyfikat, użyj metody [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Możesz sprawdzić, czy certyfikat został usunięty za pomocą polecenia interfejsu CLI platformy Azure [AZ, Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Po usunięciu certyfikat pozostaje w stanie usunięty, ale jest w nim możliwy do odzyskania. Jeśli ponownie uruchomisz kod, użyj innej nazwy certyfikatu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby eksperymentować z wpisami [tajnymi](../secrets/quick-create-python.md) i [kluczami](../keys/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

W przeciwnym razie po zakończeniu tworzenia zasobów utworzonych w tym artykule Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- [Azure Key Vault najlepszych praktyk](../general/best-practices.md)
- [Uwierzytelnianie za pomocą Key Vault](../general/authentication.md)
