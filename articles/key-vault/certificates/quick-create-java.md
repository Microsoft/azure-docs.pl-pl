---
title: Przewodnik Szybki start dla Azure Key Vault klienta certyfikatów — Java
description: Dowiedz się więcej o Azure Key Vault klienta certyfikatu dla języka Java, instrukcje opisane w tym przewodniku Szybki start.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: db69258a774343af18e683444d22530a32f85555
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374951"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Szybki start: Azure Key Vault klienta certyfikatów dla języka Java (certyfikaty)
Wprowadzenie do biblioteki klienta Azure Key Vault certyfikatów dla języka Java. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Dodatkowe zasoby:

* [Kod źródłowy](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [Dokumentacja referencyjna interfejsu API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Dokumentacja produktu](index.yml)
* [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Zestaw Java Development Kit (JDK) w](/java/azure/jdk/) wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) i narzędzia Apache [Maven](https://maven.apache.org) w oknie terminalu systemu Linux.

## <a name="setting-up"></a>Konfigurowanie
Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą [biblioteki klienta tożsamości platformy Azure).](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

   W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="create-a-new-java-console-app"></a>Tworzenie nowej aplikacji konsolowej Java
W oknie konsoli użyj polecenia , aby utworzyć nową aplikację konsoli `mvn` Java o nazwie `akv-certificates-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Dane wyjściowe wygenerowania projektu będą wyglądać podobnie do tych:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Zmień katalog na nowo utworzony `akv-certificates-java/` folder.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Instalowanie pakietu
Otwórz plik *pom.xml* w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy
Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do certyfikatów kontu użytkownika.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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

## <a name="object-model"></a>Model obiektu
Biblioteka klienta Azure Key Vault Certificate dla języka Java umożliwia zarządzanie certyfikatami. W [sekcji Przykłady](#code-examples) kodu pokazano, jak utworzyć klienta, utworzyć certyfikat, pobrać certyfikat i usunąć certyfikat.

Cała aplikacja konsolowa znajduje się [poniżej .](#sample-code)

## <a name="code-examples"></a>Przykłady kodu
### <a name="add-directives"></a>Dodawanie dyrektyw
Dodaj następujące dyrektywy na początku kodu:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta
W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w Key Vault, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna zostać przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie jest to klasa ["DefaultAzureCredential()",](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Zapisywanie tajnego
Teraz, po uwierzytelnieniu aplikacji, możesz utworzyć certyfikat w magazynie kluczy przy użyciu `certificateClient.beginCreateCertificate` metody . Wymaga to nazwy certyfikatu i zasad certyfikatu — przypisaliśmy wartość "myCertificate" do zmiennej w tym przykładzie i użyjemy `certificateName` zasad domyślnych.

Tworzenie certyfikatu jest długotrwałą operacją, dla której można sondować postęp lub czekać na jej zakończenie.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Certyfikat można uzyskać po zakończeniu tworzenia za pomocą następującego wywołania:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Pobieranie certyfikatu
Teraz możesz pobrać wcześniej utworzony certyfikat za pomocą `certificateClient.getCertificate` metody .

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Teraz możesz uzyskać dostęp do szczegółów pobranego certyfikatu za pomocą operacji, takich `retrievedCertificate.getName` `retrievedCertificate.getProperties` jak , itp. A także jego zawartość `retrievedCertificate.getCer` .

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu
Na koniec usuńmy certyfikat z magazynu kluczy przy użyciu metody , która jest `certificateClient.beginDeleteCertificate` również długotrwałą operacją.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy magazyn kluczy i odpowiednia grupa zasobów nie będą już potrzebne, możesz Azure PowerShell interfejsu wiersza polecenia platformy Azure lub usługi Azure Key Vault.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod
```java
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono magazyn kluczy, utworzono certyfikat, pobrano go, a następnie usunięto. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Jak zabezpieczyć [dostęp do magazynu kluczy](../general/secure-your-key-vault.md)