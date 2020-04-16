---
title: Szybki start — biblioteka klienta usługi Azure Key Vault dla języka Python
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienta języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 726cd76916510f38c7c14cb29e64449fb8fd539f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424706"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Szybki start: biblioteka klienta usługi Azure Key Vault dla języka Python

Wprowadzenie do biblioteki klienta usługi Azure Key Vault dla języka Python. Wykonaj poniższe czynności, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta usługi Key Vault dla języka Python, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Tworzenie i importowanie kluczy szyfrowania w ciągu kilku minut.
- Zmniejsz opóźnienia dzięki skalowaniu chmury i nadmiarowości globalnej.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj modułów HSM z walidacji FIPS 140-2 Poziomu 2.

[Dokumentacja](/python/api/overview/azure/key-vault?view=azure-python) | referencyjna interfejsu API[Pakiet źródłowy kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [(indeks pakietu Języka Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Python 2.7, 3.5.3 lub nowszy
- [Narzędzie interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest) sieci owych lub [programu Azure PowerShell platformy Azure](/powershell/azure/overview)

Ten przewodnik Szybki start zakłada, że używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) w oknie terminala systemu Linux.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli zainstaluj bibliotekę certyfikatów usługi Azure Key Vault dla języka Python.

```console
pip install azure-keyvault-certificates
```

W przypadku tego przewodnika Szybki start należy również zainstalować pakiet azure.identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki start korzysta z wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując kroki w [przewodniku Szybki start interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [przewodniku Szybki start usługi Azure PowerShell](quick-create-powershell.md)lub [przewodniku Szybki start w portalu Azure.](quick-create-portal.md) Alternatywnie można uruchomić polecenia interfejsu wiersza polecenia platformy Azure poniżej.

> [!Important]
> Każdy magazyn kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji .NET opartej na chmurze jest z tożsamością zarządzaną; Zobacz [Korzystanie z tożsamości zarządzanej usługi App Service, aby uzyskać dostęp do usługi Azure Key Vault, aby](../general/managed-identity.md) uzyskać szczegółowe informacje. Jednak ze względu na prostotę ten przewodnik Szybki start tworzy aplikację konsoli .NET. Uwierzytelnianie aplikacji klasycznej za pomocą platformy Azure wymaga użycia jednostki usługi i zasad kontroli dostępu.

Utwórz zasadę usługi przy użyciu polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ta operacja zwróci serię par kluczy / wartości. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zanotuj clientId i clientSecret, ponieważ użyjemy ich w [kroku Ustaw zmienną środowiskową](#set-environmental-variables) poniżej.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udzielanie podmiotowi usługi dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który udziela uprawnień do jednostki usługi, przekazując clientId do [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) polecenia. Nadaj podmiotowi usługi get, list i utwórz uprawnienia dla certyfikatów.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Ustawianie zmiennych środowiskowych

Metoda DefaultAzureCredential w naszej aplikacji opiera się `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`na `AZURE_TENANT_ID`trzech zmiennych środowiskowych: , , i . Ustaw te zmienne na clientId, clientSecret i tenantId wartości, które zostały `export VARNAME=VALUE` odnotowane w [kroku Utwórz jednostkę usługi](#create-a-service-principal) przy użyciu formatu. (Ta metoda ustawia tylko zmienne dla bieżącej powłoki i procesów utworzonych z powłoki; aby trwale dodać te zmienne do środowiska, edytować `/etc/environment ` plik). 

Należy również zapisać nazwę magazynu kluczy jako `KEY_VAULT_NAME`zmienną środowiskową o nazwie .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Model obiektu

Biblioteka klienta usługi Azure Key Vault dla języka Python umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokażą, jak utworzyć klienta, utworzyć certyfikat, pobrać certyfikat i usunąć certyfikat.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy do górnej części kodu:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnij i utwórz klienta

Uwierzytelnianie w magazynie kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych w kroku [Ustaw zmienne środowiskowe](#set-environmental-variables) powyżej. Nazwa magazynu kluczy zostanie rozwinięta do identyfikatora URI magazynu kluczy w formacie "https://<your-key-vault-name>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Zapisywanie certyfikatu

Teraz, gdy aplikacja jest uwierzytelniona, można umieścić certyfikat z podpisem własnym do keyvault 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Można sprawdzić, czy certyfikat został ustawiony za pomocą polecenia [az keyvault certificate show:](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Pobieranie certyfikatu

Teraz można pobrać wcześniej utworzony certyfikat

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Certyfikat jest teraz `retrieved_certificate`zapisywany jako .

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Na koniec usuńmy certyfikat z magazynu kluczy

```python
client.delete_certificate(certificateName)
```

Można sprawdzić, czy certyfikat zniknął za pomocą polecenia [az keyvault certyfikat show:](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, magazyn kluczy i pobrano ten certyfikat. Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dla deweloperów usługi Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)
