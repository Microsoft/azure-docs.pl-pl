---
title: Szybki start — Azure Key Vault klienta klucza dla języka JavaScript (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z usługi Azure Key Vault przy użyciu biblioteki klienta języka JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c0066409732f4492186ea0bf604261e1ab59ca9f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750306"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Szybki start: Azure Key Vault klienta klucza dla języka JavaScript (wersja 4)

Wprowadzenie do biblioteki klienta Azure Key Vault dla języka JavaScript. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn kluczy kryptograficznych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać klucze z usługi Azure Key Vault przy użyciu biblioteki klienta kluczy języka JavaScript

Key Vault zasobów biblioteki klienta:

[Dokumentacja referencyjna interfejsu API](/javascript/api/overview/azure/key-vault-index)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pakiet (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Aby uzyskać więcej informacji na Key Vault kluczy, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Klucze — omówienie.](about-keys.md)

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

```azurecli
mkdir key-vault-node-app
```

1. Zmień katalog na nowo utworzony *katalog key-vault-node-app* i uruchom polecenie "init", aby zainicjować projekt węzła:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalowanie Key Vault pakietów

W oknie konsoli zainstaluj bibliotekę kluczy Azure Key Vault [dla](https://www.npmjs.com/package/@azure/keyvault-keys) Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Instalowanie pakietu [azure.identity w](https://www.npmjs.com/package/@azure/identity) celu uwierzytelnienia w Key Vault

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

Tworzenie zasad dostępu dla magazynu kluczy, które przyznaje uprawnienia klucza do konta użytkownika

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Przykłady kodu

Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić klucz, pobrać klucz i usunąć klucz. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w magazynie kluczy, zobacz [Developer's Guide (Przewodnik dewelopera).](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Dodaj następujący kod do funkcji "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Zapisywanie klucza

Teraz, gdy aplikacja została uwierzytelniona, możesz umieścić klucz w swoim kluczu przy użyciu metody [createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) Parametry metody akceptują nazwę klucza i [typ klucza](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Pobieranie klucza

Teraz możesz pobrać wcześniej ustawioną wartość za pomocą [metody getKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Usuń klucz

Na koniec usuńmy i przeczyścimy klucz z magazynu kluczy za pomocą metod [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) i [purgeDeletedKey.](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_)

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

W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano klucz i pobrano ten klucz. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zapoznaj się [z omówieniem kluczy Azure Key Vault kluczy](about-keys.md)
- Jak zabezpieczyć [dostęp do magazynu kluczy](../general/security-overview.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-overview.md)
