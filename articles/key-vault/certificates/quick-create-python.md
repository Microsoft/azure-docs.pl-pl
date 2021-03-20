---
title: Szybki Start — Azure Key Vault biblioteki klienckiej języka Python — zarządzanie certyfikatami
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 6ae20c55a16b12f7543af3f08d96400a8e5f20c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934921"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Szybki Start: Azure Key Vault Biblioteka kliencka certyfikatów dla języka Python

Wprowadzenie do biblioteki klienta Azure Key Vault Certificate dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Przy użyciu Key Vault do przechowywania certyfikatów można uniknąć przechowywania certyfikatów w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja](/python/api/overview/azure/keyvault-certificates-readme)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + lub 3.5.3 +](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Ten przewodnik Szybki Start korzysta z biblioteki Azure Identity Library z interfejsem wiersza polecenia platformy Azure w celu uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również używać programu Visual Studio lub Visual Studio Code do uwierzytelniania wywołań, aby uzyskać więcej informacji, zobacz [uwierzytelnianie klienta przy użyciu biblioteki klienta tożsamości platformy Azure](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="install-the-packages"></a>Zainstaluj pakiety

1. W terminalu lub wierszu polecenia Utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w temacie [Korzystanie z środowisk wirtualnych języka Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Zainstaluj bibliotekę tożsamości Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```


1. Zainstaluj bibliotekę klienta Key Vault certyfikatów:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, który przyznaje uprawnienia certyfikatu do konta użytkownika

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
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

Biblioteka klienta certyfikatu Azure Key Vault dla języka Python umożliwia zarządzanie certyfikatami. Poniższy przykład kodu demonstruje sposób tworzenia klienta, ustawiania certyfikatu, pobierania certyfikatu i usuwania certyfikatu.

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

- W przypadku wystąpienia błędów uprawnień upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu o tej samej nazwie klucza może spowodować wystąpienie błędu, "(konflikt) certyfikat <name> jest obecnie usunięty, ale stan odzyskiwalny". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy  ["DefaultAzureCredential ()"](/python/api/azure-identity/azure.identity.defaultazurecredential) , która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [domyślne uwierzytelnianie poświadczeń platformy Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Zapisz certyfikat

Po uzyskaniu obiektu klienta dla magazynu kluczy można utworzyć certyfikat przy użyciu metody [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

W tym miejscu certyfikat wymaga zasad uzyskanych z użyciem metody [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) .

Wywołanie `begin_create_certificate` metody generuje asynchroniczne wywołanie interfejsu API REST platformy Azure dla magazynu kluczy. Wywołanie asynchroniczne zwraca obiekt sondowający. Aby poczekać na wynik operacji, wywołaj metodę sondowania `result` .

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość wywołującego (nazwę główną usługi) przy użyciu obiektu Credential dostarczonego do klienta.


### <a name="retrieve-a-certificate"></a>Pobierz certyfikat

Aby odczytać certyfikat z Key Vault, użyj metody [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Możesz również sprawdzić, czy certyfikat został ustawiony za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ, Certificate webmagazynu show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Aby usunąć certyfikat, użyj metody [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoda jest asynchroniczna i zwraca obiekt sondowaer. Wywołanie metody sondy `result` oczekuje na jego zakończenie.

Możesz sprawdzić, czy certyfikat został usunięty za pomocą polecenia interfejsu CLI platformy Azure [AZ, Certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

Po usunięciu certyfikat pozostaje w stanie usunięty, ale jest w nim możliwy do odzyskania. Jeśli ponownie uruchomisz kod, użyj innej nazwy certyfikatu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby eksperymentować z wpisami [tajnymi](../secrets/quick-create-python.md) i [kluczami](../keys/quick-create-python.md), możesz ponownie użyć Key Vault utworzonych w tym artykule.

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