---
title: Szybki Start — Azure Key Vault Biblioteka kliencka certyfikatów dla języka JavaScript (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930858"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Szybki Start: Azure Key Vault Biblioteka kliencka certyfikatów dla języka JavaScript (wersja 4)

Rozpocznij pracę z biblioteką klienta Azure Key Vault Certificate dla języka JavaScript. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla certyfikatów. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku szybki start dowiesz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka JavaScript

Key Vault zasoby biblioteki klienta:

[Dokumentacja](/javascript/api/overview/azure/key-vault-index)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Aby uzyskać więcej informacji o Key Vault i certyfikatach, zobacz:
- [Przegląd Key Vault](../general/overview.md)
- [Omówienie certyfikatów](about-certificates.md).

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

W oknie konsoli programu zainstaluj [bibliotekę Azure Key Vault certyfikatów](https://www.npmjs.com/package/@azure/keyvault-certificates) Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Tworzenie zasad dostępu dla magazynu kluczy, który przyznaje uprawnienia certyfikatów do konta użytkownika

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Przykłady kodu

Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić certyfikat, pobrać certyfikat i usunąć certyfikat. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z [biblioteki tożsamości platformy Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania do magazynu kluczy, zobacz [przewodnik dewelopera](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Dodaj następujący kod do funkcji "Main ()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Zapisz certyfikat

Po uwierzytelnieniu aplikacji można umieścić certyfikat w magazynie kluczy przy użyciu [metody beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) . wymaga to nazwy certyfikatu i[zasad certyfikatów](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) zasad certyfikatów z [właściwościami zasad certyfikatów](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Jeśli nazwa certyfikatu istnieje, powyżej kodu zostanie utworzona nowa wersja tego certyfikatu.
### <a name="retrieve-a-certificate"></a>Pobierz certyfikat

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody getcertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Na koniec Usuń i Przeczyść certyfikat z magazynu kluczy przy użyciu metod [beginDeleteCertificate] https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) i [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) .

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Przykładowy kod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano certyfikat i pobrano ten certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zapoznaj się z [omówieniem certyfikatów](about-certificates.md)
- Wyświetlanie [Key Vault dostępu z poziomu samouczka aplikacji App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [Key Vault dostępu z poziomu samouczka maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)
