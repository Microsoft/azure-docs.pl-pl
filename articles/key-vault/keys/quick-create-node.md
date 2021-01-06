---
title: Szybki Start — Azure Key Vault kluczowej biblioteki klienta dla języka JavaScript (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: db6bb5c204bfe79b9d7470f651081aa4f4dcf2ed
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932711"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Szybki Start: Azure Key Vault kluczowej biblioteki klienta dla języka JavaScript (wersja 4)

Zacznij korzystać z biblioteki klienta Key Azure Key Vault dla języka JavaScript. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny Magazyn kluczy kryptograficznych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku szybki start dowiesz się, jak tworzyć, pobierać i usuwać klucze z magazynu kluczy platformy Azure przy użyciu biblioteki klienta klucza JavaScript

Key Vault zasoby biblioteki klienta:

[Dokumentacja](/javascript/api/overview/azure/key-vault-index)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Aby uzyskać więcej informacji na temat Key Vault i kluczy, zobacz:
- [Przegląd Key Vault](../general/overview.md)
- [Omówienie kluczy](about-keys.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Bieżąca [Node.js](https://nodejs.org) dla danego systemu operacyjnego.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- Key Vault — można go utworzyć przy użyciu [Azure Portal](../general/quick-create-portal.md) [interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md)

Ten przewodnik Szybki Start zakłada, że korzystasz z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

## <a name="create-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Następnie Utwórz aplikację Node.js, którą można wdrożyć w chmurze. 

1. W powłoce poleceń Utwórz folder o nazwie `key-vault-node-app` :

```azurecli
mkdir key-vault-node-app
```

1. Przejdź do nowo utworzonego katalogu *Key-magazyn-Node-App* i uruchom polecenie "init", aby zainicjować projekt węzła:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Zainstaluj pakiety Key Vault

W oknie konsoli zainstaluj [bibliotekę klucze](https://www.npmjs.com/package/@azure/keyvault-keys) Azure Key Vault Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Zainstaluj pakiet [Azure. Identity](https://www.npmjs.com/package/@azure/identity) w celu uwierzytelnienia w Key Vault

```azurecli
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

Utwórz zasady dostępu dla magazynu kluczy, które przyznaje kluczowe uprawnienia do konta użytkownika

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Przykłady kodu

Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić klucz, pobrać klucz i usunąć klucz. 

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Utwórz nowy plik tekstowy i Zapisz go jako "index.js"

1. Dodaj wymagane wywołania do załadowania modułów platformy Azure i Node.js

1. Utwórz strukturę dla programu, w tym podstawową obsługę wyjątków

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

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z [biblioteki tożsamości platformy Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania do magazynu kluczy, zobacz [przewodnik dewelopera](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Dodaj następujący kod do funkcji "Main ()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Zapisz klucz

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz w magazynie kluczy przy użyciu [metody createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) parametry metody akceptują nazwę klucza i [Typ klucza](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Pobierz klucz

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody GetKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Usuń klucz

Na koniec Usuń i Przeczyść klucz z magazynu kluczy przy użyciu metod [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) i [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) .

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Przykładowy kod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Wykonaj następujące polecenia, aby uruchomić aplikację.

```azurecli
npm install
npm index.js
```

Zostanie wyświetlona odmiana następujących danych wyjściowych:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano klucz i pobrano ten klucz. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zapoznaj się [z omówieniem Azure Key Vault kluczy](about-keys.md)
- Jak [zabezpieczyć dostęp do magazynu kluczy](../general/secure-your-key-vault.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)
