---
title: Szybki start — biblioteka klienta usługi Azure Key Vault dla pliku Node.js (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienta Node.js
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 609076cd204457bb7c952c91cd249b1aece58022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425014"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Szybki start: biblioteka klienta usługi Azure Key Vault dla pliku Node.js (wersja 4)

Wprowadzenie do biblioteki klienta usługi Azure Key Vault dla pliku Node.js. Wykonaj poniższe czynności, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta usługi Key Vault dla pliku Node.js, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Tworzenie i importowanie kluczy szyfrowania w ciągu kilku minut.
- Zmniejsz opóźnienia dzięki skalowaniu chmury i nadmiarowości globalnej.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj modułów HSM z walidacji FIPS 140-2 Poziomu 2.

[Dokumentacja](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | referencyjna interfejsu API[Pakiet źródłowy kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [źródłowego biblioteki (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Bieżący [plik Node.js](https://nodejs.org) dla systemu operacyjnego.
- [Narzędzie interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest) sieci owych lub [programu Azure PowerShell platformy Azure](/powershell/azure/overview)

Ten przewodnik Szybki start zakłada, że używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) w oknie terminala systemu Linux.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli zainstaluj bibliotekę wpisów tajnych usługi Azure Key Vault dla pliku Node.js.

```console
npm install @azure/keyvault-secrets
```

W przypadku tego przewodnika Szybki start należy również zainstalować pakiet azure.identity:

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki start korzysta z wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując kroki w [przewodniku Szybki start interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [przewodniku Szybki start usługi Azure PowerShell](quick-create-powershell.md)lub [przewodniku Szybki start w portalu Azure.](quick-create-portal.md) Alternatywnie można po prostu uruchomić polecenia interfejsu wiersza polecenia platformy Azure poniżej.

> [!Important]
> Każdy magazyn kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji opartej na chmurze jest przy użyciu tożsamości zarządzanej; Zobacz [Korzystanie z tożsamości zarządzanej usługi App Service, aby uzyskać dostęp do usługi Azure Key Vault, aby](../general/managed-identity.md) uzyskać szczegółowe informacje. Jednak ze względu na prostotę ten przewodnik Szybki start tworzy aplikację konsoli. Uwierzytelnianie aplikacji klasycznej za pomocą platformy Azure wymaga użycia jednostki usługi i zasad kontroli dostępu.

Utwórz zasadę usługi przy użyciu polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ta operacja zwróci serię par kluczy / wartości. 

```azurecli
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

Utwórz zasady dostępu dla magazynu kluczy, który udziela uprawnień do jednostki usługi, przekazując clientId do [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) polecenia. Nadaj jednostce usługi get, list i set permissions for both keys and secrets.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ustawianie zmiennych środowiskowych

Metoda DefaultAzureCredential w naszej aplikacji opiera się `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`na `AZURE_TENANT_ID`trzech zmiennych środowiskowych: , , i . Ustaw te zmienne na clientId, clientSecret i tenantId wartości, które zostały `export VARNAME=VALUE` odnotowane w [kroku Utwórz jednostkę usługi](#create-a-service-principal) przy użyciu formatu. (Spowoduje to ustawienie tylko zmiennych bieżącej powłoki i procesów utworzonych z powłoki; `/etc/environment ` aby trwale dodać te zmienne do środowiska, edytować plik). 

Należy również zapisać nazwę magazynu kluczy jako `KEY_VAULT_NAME`zmienną środowiskową o nazwie .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Model obiektu

Biblioteka klienta usługi Azure Key Vault dla pliku Node.js umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokażą, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny.

Cała aplikacja konsoli jest https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appdostępna pod adresem .

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy do górnej części kodu:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnij i utwórz klienta

Uwierzytelnianie do magazynu kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych z [kroku Ustaw zmienne środowiskowe](#set-environmental-variables) powyżej i [konstruktora SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Nazwa magazynu kluczy jest rozwijana do identyfikatora URI magazynu kluczy w formacie `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Zapisywanie tajemnicy

Teraz, gdy aplikacja jest uwierzytelniona, można umieścić klucz tajny w keyvault przy użyciu [client.setSecret metody](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) Wymaga nazwy klucza tajnego — używamy "mySecret" w tym przykładzie.  

```javascript
await client.setSecret(secretName, secretValue);
```

Można sprawdzić, czy klucz tajny został ustawiony za pomocą polecenia [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobieranie klucza tajnego

Teraz można pobrać wcześniej ustawioną wartość za pomocą [client.getSecret metody](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Twój sekret jest `retrievedSecret.value`teraz zapisywany jako .

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec usuńmy klucz tajny z magazynu kluczy za pomocą [metody client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Można sprawdzić, czy klucz tajny zniknął za pomocą polecenia [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
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

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, zapis tajny i pobrano ten klucz tajny. Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dla deweloperów usługi Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)