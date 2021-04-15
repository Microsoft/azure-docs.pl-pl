---
title: Szybki start — biblioteka klienta Azure Key Vault Key dla języka Java
description: Zawiera przewodnik Szybki start dla biblioteki klienta Azure Key Vault Keys dla języka Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 124e56fad35be0f3ac5b08ee9dd66454b9d077c5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374696"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Szybki start: biblioteka klienta Azure Key Vault Key dla języka Java
Wprowadzenie do biblioteki klienta Azure Key Vault Key dla języka Java. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Dodatkowe zasoby:

* [Kod źródłowy](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Dokumentacja referencyjna interfejsu API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Dokumentacja produktu](index.yml)
* [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Zestaw Java Development Kit (JDK) w](/java/azure/jdk/) wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku Szybki start założono, że używasz interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i narzędzia Apache [Maven](https://maven.apache.org) w oknie terminalu systemu Linux.

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
W oknie konsoli użyj polecenia , aby utworzyć nową aplikację konsoli `mvn` Java o nazwie `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Dane wyjściowe wygenerowania projektu będą wyglądać podobnie do tych:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Zmień katalog na nowo utworzony `akv-keys-java/` folder.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Instalowanie pakietu
Otwórz plik *pom.xml* w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
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
Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień klucza do konta użytkownika.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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
Biblioteka klienta Azure Key Vault Key dla języka Java umożliwia zarządzanie kluczami. W [sekcji Przykłady](#code-examples) kodu pokazano, jak utworzyć klienta, utworzyć klucz, pobrać klucz i usunąć klucz.

Cała aplikacja konsolowa znajduje się [poniżej .](#sample-code)

## <a name="code-examples"></a>Przykłady kodu
### <a name="add-directives"></a>Dodawanie dyrektyw
Dodaj następujące dyrektywy na początku kodu:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta
W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w Key Vault, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna zostać przypisana do App Service wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy ["DefaultAzureCredential()",](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [Domyślne uwierzytelnianie poświadczeń platformy Azure.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Utwórz klucz
Po uwierzytelnieniu aplikacji możesz utworzyć klucz w magazynie kluczy przy użyciu `keyClient.createKey` metody . Wymaga to nazwy klucza i typu klucza — przypisaliśmy wartość "myKey" do zmiennej i w tym przykładzie użyjemy `keyName` `KeyType` RSA.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Możesz sprawdzić, czy klucz został ustawiony za pomocą [polecenia az keyvault key show:](/cli/azure/keyvault/key?#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Pobieranie klucza
Teraz możesz pobrać wcześniej utworzony klucz za pomocą `keyClient.getKey` metody .

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Teraz możesz uzyskać dostęp do szczegółów pobranego klucza za pomocą operacji, takich `retrievedKey.getProperties` `retrievedKey.getKeyOperations` jak , itp.

### <a name="delete-a-key"></a>Usuń klucz
Na koniec usuńmy klucz z magazynu kluczy przy użyciu `keyClient.beginDeleteKey` metody .

Usuwanie klucza to długotrwała operacja, dla której można sondować postęp lub czekać na jej zakończenie.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Możesz sprawdzić, czy klucz został usunięty, za pomocą polecenia [az keyvault key show:](/cli/azure/keyvault/key?#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
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
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono magazyn kluczy, utworzono klucz, pobrano go, a następnie usunięto. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Przeczytaj omówienie [Key Vault zabezpieczeń](../general/security-overview.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Jak zabezpieczyć [dostęp do magazynu kluczy](../general/secure-your-key-vault.md)