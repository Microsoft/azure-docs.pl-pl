---
title: Szybki start — Azure Key Vault klienta secret dla języka Java
description: Zawiera przewodnik Szybki start dla biblioteki Azure Key Vault klienta Secret dla języka Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: f1a502722156bc010dab917afdbd1d49ae93681a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375988"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Szybki start: Azure Key Vault klienta secret dla języka Java
Wprowadzenie do biblioteki klienta Azure Key Vault Secret dla języka Java. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Dodatkowe zasoby:

* [Kod źródłowy](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [Dokumentacja referencyjna interfejsu API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Dokumentacja produktu](index.yml)
* [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Zestaw Java Development Kit (JDK) w](/java/azure/jdk/) wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) i narzędzia Apache [Maven](https://maven.apache.org) w oknie terminalu systemu Linux.

## <a name="setting-up"></a>Konfigurowanie
Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również używać Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą biblioteki klienta tożsamości [platformy Azure).](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

   W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="create-a-new-java-console-app"></a>Tworzenie nowej aplikacji konsolowej Java
W oknie konsoli użyj polecenia , aby utworzyć nową aplikację konsoli `mvn` Java o nazwie `akv-secrets-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Dane wyjściowe z generowania projektu będą wyglądać podobnie do tych:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Zmień katalog na nowo utworzony `akv-secrets-java/` folder.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Instalowanie pakietu
Otwórz plik *pom.xml* w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.2.3</version>
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
Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do kluczy tajnych kontu użytkownika.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
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

## <a name="object-model"></a>Model obiektów
Biblioteka Azure Key Vault klienta secret dla języka Java umożliwia zarządzanie wpisami tajnymi. W [sekcji Przykłady](#code-examples) kodu pokazano, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny.

Cała aplikacja konsolowa znajduje się [poniżej .](#sample-code)

## <a name="code-examples"></a>Przykłady kodu
### <a name="add-directives"></a>Dodawanie dyrektyw
Dodaj następujące dyrektywy na początku kodu:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta
W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w Key Vault, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna zostać przypisana do App Service wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy ["DefaultAzureCredential()",](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Zapisywanie tajnego
Teraz, gdy aplikacja jest uwierzytelniona, możesz umieścić klucz tajny w magazynie kluczy przy użyciu `secretClient.setSecret` metody . Wymaga to nazwy dla tajnego — przypisaliśmy wartość "mySecret" do zmiennej `secretName` w tym przykładzie.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Możesz sprawdzić, czy klucz tajny został ustawiony za pomocą [polecenia az keyvault secret show:](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobieranie tajnego
Teraz możesz pobrać wcześniej ustawiony klucz tajny za pomocą `secretClient.getSecret` metody .

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Teraz możesz uzyskać dostęp do wartości pobranego tajnego klucz tajny za `retrievedSecret.getValue()` pomocą .

### <a name="delete-a-secret"></a>Usuń klucz tajny
Na koniec usuńmy klucz tajny z magazynu kluczy za pomocą `secretClient.beginDeleteSecret` metody .

Usuwanie kluczem tajnym to długotrwała operacja, dla której można sondować postęp lub czekać na ukończenie.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

Możesz sprawdzić, czy klucz tajny został usunięty, za pomocą [polecenia az keyvault secret show:](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy magazyn kluczy i odpowiednia grupa zasobów nie będą już potrzebne, możesz Azure PowerShell interfejsu wiersza polecenia platformy Azure lub grupy zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod
```java
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano klucz tajny, pobrano go, a następnie usunięto. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Jak zabezpieczyć [dostęp do magazynu kluczy](../general/secure-your-key-vault.md)
