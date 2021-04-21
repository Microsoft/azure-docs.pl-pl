---
title: Szybki start — Azure Key Vault klienta certyfikatów dla języka JavaScript (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z usługi Azure Key Vault przy użyciu biblioteki klienta języka JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3f933a564e62bf3aae1ec05c6dde048100c22967
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815850"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Szybki start: Azure Key Vault klienta certyfikatów dla języka JavaScript (wersja 4)

Wprowadzenie do biblioteki klienta Azure Key Vault dla języka JavaScript. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn certyfikatów. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać certyfikaty z usługi Azure Key Vault przy użyciu biblioteki klienta języka JavaScript

Key Vault zasobów biblioteki klienta:

[Dokumentacja referencyjna interfejsu API](/javascript/api/overview/azure/key-vault-index)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pakiet (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Aby uzyskać więcej informacji na Key Vault i certyfikatów, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Certificates Overview (Omówienie certyfikatów).](about-certificates.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Bieżące [Node.js](https://nodejs.org) dla systemu operacyjnego.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [wiersza Azure Portal](../general/quick-create-portal.md) [platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli)

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

W oknie konsoli zainstaluj bibliotekę certyfikatów Azure Key Vault [dla](https://www.npmjs.com/package/@azure/keyvault-certificates) Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do certyfikatu kontu użytkownika

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Przykłady kodu

Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić certyfikat, pobrać certyfikat i usunąć certyfikat. 

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Utwórz nowy plik tekstowy i zapisz go jako "index.js"

1. Dodawanie wywołań require w celu załadowania platformy Azure i Node.js modułów

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie jest to klasa ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w magazynie kluczy, zobacz [Developer's Guide (Przewodnik dewelopera).](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Dodaj następujący kod do funkcji "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Zapisywanie certyfikatu

Teraz, gdy aplikacja jest uwierzytelniona, możesz umieścić certyfikat w układzie kluczy przy użyciu [metody beginCreateCertificate.](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) Wymaga to nazwy certyfikatu i zasad certyfikatu zasad certyfikatów z właściwościami zasad [certyfikatów](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties) [](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Jeśli nazwa certyfikatu istnieje, powyższy kod utworzy nową wersję tego certyfikatu.
### <a name="retrieve-a-certificate"></a>Pobieranie certyfikatu

Teraz możesz pobrać wcześniej ustawioną wartość za pomocą [metody getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Na koniec usuńmy i przeczyścimy certyfikat z magazynu kluczy za pomocą metod [beginDeleteCertificate] i https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) [purgeDeletedCertificate.](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_)

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

W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano certyfikat i pobrano ten certyfikat. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zapoznaj się [z omówieniem certyfikatów](about-certificates.md)
- Zobacz [samouczek dotyczący dostępu Key Vault z App Service aplikacji](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [samouczek dotyczący Key Vault dostępu z maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
