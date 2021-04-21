---
title: Szybki start — Azure Key Vault tajnej biblioteki klienta dla języka JavaScript (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu biblioteki klienta języka JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 90e505a4d505cc52920f1c7727afb321f4eda462
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814158"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Szybki start: Azure Key Vault tajnej biblioteki klienta dla języka JavaScript (wersja 4)

Wprowadzenie do biblioteki klienta Azure Key Vault tajnego dla języka JavaScript. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu biblioteki klienta języka JavaScript

Key Vault zasobów biblioteki klienta:

[Dokumentacja referencyjna interfejsu API](/javascript/api/overview/azure/key-vault-index)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pakiet (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Aby uzyskać więcej informacji na Key Vault i wpisów tajnych, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Omówienie wpisów tajnych.](about-secrets.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Bieżące [Node.js](https://nodejs.org) dla twojego systemu operacyjnego.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [wiersza Azure Portal](../general/quick-create-portal.md) [platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md)

W tym przewodniku Szybki start założono, że używasz interfejsu [wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

## <a name="create-new-nodejs-application"></a>Tworzenie nowej Node.js aplikacji

Następnie utwórz aplikację Node.js, która może zostać wdrożona w chmurze. 

1. W powłoki poleceń utwórz folder o nazwie `key-vault-node-app` :

```terminal
mkdir key-vault-node-app
```

1. Zmień katalog na nowo utworzony *katalog key-vault-node-app* i uruchom polecenie "init", aby zainicjować projekt węzła:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalowanie Key Vault pakietów

W oknie konsoli zainstaluj bibliotekę Azure Key Vault [wpisów tajnych dla](https://www.npmjs.com/package/@azure/keyvault-secrets) Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Instalowanie pakietu [azure.identity w](https://www.npmjs.com/package/@azure/identity) celu uwierzytelnienia w Key Vault

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

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

## <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, które przyznaje uprawnienia do kluczy tajnych do konta użytkownika

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Przykłady kodu

Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny. 

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Utwórz nowy plik tekstowy i zapisz go jako "index.js"

1. Dodawanie wywołań wymagania w celu załadowania platformy Azure Node.js modułów

1. Tworzenie struktury programu, w tym podstawowej obsługi wyjątków

```javascript
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
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy na początku kodu:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używamy klasy ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w Key Vault, zobacz [Developer's Guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)(Przewodnik dewelopera).

Dodaj następujący kod do funkcji "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Zapisywanie tajnego

Teraz, gdy aplikacja jest uwierzytelniona, możesz umieścić klucz tajny w kluczu przy użyciu metody [setSecret.](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) Wymaga to nazwy klucza tajnego — w tym przykładzie używamy nazwy "mySecret".  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Pobieranie tajnego

Możesz teraz pobrać wcześniej ustawioną wartość za pomocą [metody getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Twój klucz tajny jest teraz zapisany jako `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec usuńmy i przeczyścimy klucz tajny z magazynu kluczy za pomocą metod [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) i [purgeDeletedSecret.](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_)

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
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
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

1. Wykonaj następujące polecenia, aby uruchomić aplikację.

    ```terminal
    npm install
    node index.js
    ```

1. Po wyświetleniu monitu wprowadź wartość wpisów tajnych. Na przykład mySecretPassword.

    Zostanie wyświetlona odmiana następujących danych wyjściowych:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano klucz tajny i pobrano ten klucz tajny. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zapoznaj się z [omówieniem Azure Key Vault tajnych](about-secrets.md)
- Jak zabezpieczyć [dostęp do magazynu kluczy](../general/security-features.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
