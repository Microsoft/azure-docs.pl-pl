---
title: Szybki Start — Azure Key Vault Biblioteka kliencka języka Python — zarządzanie kluczami
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 1352d3fe4a13202a296b79fe82343c50a9e281d1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400419"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Szybki Start: Biblioteka klienta kluczy Azure Key Vault dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla języka Python, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[Dokumentacja](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  źródłowy biblioteki [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 lub nowszy
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) w oknie terminalu systemu Linux.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli Zainstaluj bibliotekę klucze Azure Key Vault dla języka Python.

```console
pip install azure-keyvault-keys
```

W tym przewodniku szybki start konieczne będzie zainstalowanie pakietu Azure. Identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki Start używa wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując czynności opisane w [przewodniku szybki start dotyczącego interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [Azure PowerShell szybki start](quick-create-powershell.md)lub [Azure Portal przewodniku szybki start](quick-create-portal.md). Alternatywnie możesz uruchomić poniższe polecenia interfejsu CLI platformy Azure.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz Uwierzytelnianie w Azure Key Vault](../general/authentication.md) . 

W tym przewodniku szybki start można jednak utworzyć aplikację klasyczną, która wymaga użycia nazwy głównej usługi i zasad kontroli dostępu. Nazwa główna usługi wymaga unikatowej nazwy w formacie "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

Utwórz nazwę główną usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Ta operacja zwróci serię par klucz/wartość. 

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

Zwróć uwagę na clientId i clientSecret, ponieważ będziemy z nich korzystać w kroku [Ustaw zmienną środowiskową](#set-environmental-variables) poniżej.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi przez przekazanie clientId do polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Nadaj nazwę głównej usługi uprawnienia Get, list i Create dla kluczy.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Ustaw zmienne środowiskowe

Metoda DefaultAzureCredential w naszej aplikacji opiera się na trzech zmiennych środowiskowych: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` i `AZURE_TENANT_ID` . Ustaw te zmienne na wartości clientId, clientSecret i tenantId zanotowane w kroku [Tworzenie jednostki usługi](#create-a-service-principal) przy użyciu `export VARNAME=VALUE` formatu. (Ta metoda ustawia tylko zmienne dla bieżącej powłoki i procesów utworzonych na podstawie powłoki; aby trwale dodać te zmienne do środowiska, Edytuj `/etc/environment ` plik). 

Należy również zapisać nazwę magazynu kluczy jako zmienną środowiskową o nazwie `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla języka Python umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak utworzyć klienta, utworzyć klucz, pobrać klucz i usunąć klucz.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

Uwierzytelnianie w magazynie kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych w powyższym kroku [Ustaw zmienne środowiskowe](#set-environmental-variables) . Nazwa magazynu kluczy zostanie rozszerzona na identyfikator URI magazynu kluczy w formacie "https://<nazwę magazynu kluczy>. vault.azure.net".

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Zapisz klucz

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz w magazynie kluczy 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Możesz sprawdzić, czy klucz został ustawiony za pomocą polecenia [AZ Key magazynu klucz show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Pobierz klucz

Teraz można pobrać utworzony wcześniej klucz

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Twój klucz jest teraz zapisany jako `retrieved_key` .

### <a name="delete-a-key"></a>Usuń klucz

Na koniec Usuń klucz z magazynu kluczy

```python
client.delete_key(keyName)
```

Możesz sprawdzić, czy klucz został usunięty za pomocą polecenia [AZ Key magazynu klucz show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano klucz i pobrano ten klucz. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- [Omówienie usługi Azure Key Vault](../general/overview.md)
- [Przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- [Azure Key Vault najlepszych praktyk](../general/best-practices.md)
