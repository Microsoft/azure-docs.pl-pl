---
title: Szybki start — Azure Key Vault klienta języka Python — zarządzanie certyfikatami
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z usługi Azure Key Vault przy użyciu biblioteki klienta języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 598bbef0ceb24f3eb5932239a4146e1693521f24
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751332"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Szybki start: Azure Key Vault klienta certyfikatów dla języka Python

Wprowadzenie do biblioteki klienta Azure Key Vault certyfikatów dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Używając Key Vault do przechowywania certyfikatów, unika się przechowywania certyfikatów w kodzie, co zwiększa bezpieczeństwo aplikacji.

[Dokumentacja referencyjna interfejsu API](/python/api/overview/azure/keyvault-certificates-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Pakiet (indeks pakietów języka Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ lub 3.6+](/azure/developer/python/configure-local-development-environment)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) w oknie terminalu systemu Linux.

## <a name="set-up-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również używać Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą [biblioteki klienta tożsamości platformy Azure).](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="install-the-packages"></a>Instalowanie pakietów

1. W terminalu lub wierszu polecenia utwórz odpowiedni folder projektu, a następnie utwórz i aktywuj środowisko wirtualne języka Python zgodnie z opisem w [tece Use Python virtual environments (Korzystanie](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) ze środowisk wirtualnych języka Python)

1. Zainstaluj bibliotekę Azure Active Directory tożsamości:

    ```terminal
    pip install azure.identity
    ```


1. Zainstaluj bibliotekę Key Vault klienta certyfikatu:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, które przyznaje uprawnienia do certyfikatu do konta użytkownika

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

Biblioteka Azure Key Vault klienta certyfikatów dla języka Python umożliwia zarządzanie certyfikatami. Poniższy przykład kodu pokazuje, jak utworzyć klienta, ustawić certyfikat, pobrać certyfikat i usunąć certyfikat.

Utwórz plik o *nazwie kv_certificates.py* zawierający ten kod.

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

Upewnij się, że kod w poprzedniej sekcji znajduje się w pliku *o nazwie kv_certificates.py.* Następnie uruchom kod za pomocą następującego polecenia:

```terminal
python kv_certificates.py
```

- Jeśli wystąpią błędy uprawnień, upewnij się, że uruchomiono [ `az keyvault set-policy` polecenie](#grant-access-to-your-key-vault).
- Ponowne uruchomienie kodu z taką samą nazwą klucza może spowodować błąd "(Konflikt) Certyfikat jest obecnie w stanie <name> usunięcia, ale można go odzyskać". Użyj innej nazwy klucza.

## <a name="code-details"></a>Szczegóły kodu

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy  ["DefaultAzureCredential()",](/python/api/azure-identity/azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Zapisywanie certyfikatu

Po uzyskaniu obiektu klienta dla magazynu kluczy możesz utworzyć certyfikat przy użyciu [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) metody: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

W tym miejscu certyfikat wymaga zasad uzyskanych za pomocą [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) metody .

Wywołanie metody `begin_create_certificate` generuje asynchroniczne wywołanie interfejsu API REST platformy Azure dla magazynu kluczy. Wywołanie asynchroniczne zwraca obiekt poller. Aby zaczekać na wynik operacji, wywołaj metodę `result` pollera.

Podczas obsługi żądania platforma Azure uwierzytelnia tożsamość obiektu wywołującego (jednostkę usługi) przy użyciu obiektu poświadczeń podanego klientowi.


### <a name="retrieve-a-certificate"></a>Pobieranie certyfikatu

Aby odczytać certyfikat z Key Vault, użyj [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) metody :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Możesz również sprawdzić, czy certyfikat został ustawiony za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Aby usunąć certyfikat, użyj [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) metody :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

Metoda `begin_delete_certificate` jest asynchroniczna i zwraca obiekt poller. Wywołanie metody `result` pollera czeka na jego ukończenie.

Możesz sprawdzić, czy certyfikat został usunięty, za pomocą polecenia [az keyvault certificate](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show)show interfejsu wiersza polecenia platformy Azure .

Po usunięciu certyfikat pozostaje w stanie usuniętym, ale można go odzyskać przez jeden czas. Jeśli kod zostanie uruchomiony ponownie, użyj innej nazwy certyfikatu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz również poeksperymentować z [wpisami tajnymi](../secrets/quick-create-python.md) i kluczami, [](../keys/quick-create-python.md)możesz ponownie użyć Key Vault utworzonych w tym artykule.

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
