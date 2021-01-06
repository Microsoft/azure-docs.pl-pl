---
title: Szybki Start — Azure Key Vault kluczową bibliotekę klienta dla języka Java
description: Udostępnia Przewodnik Szybki Start dla biblioteki klienta kluczy Azure Key Vault dla języka Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: cb5abf59c446ef0835375bac45d1e852144a6f28
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935278"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Szybki Start: Azure Key Vault kluczową bibliotekę klienta dla języka Java
Zacznij korzystać z biblioteki klienta Key Azure Key Vault dla języka Java. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Dodatkowe zasoby:

* [Kod źródłowy](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Dokumentacja referencyjna interfejsu API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Dokumentacja produktu](index.yml)
* [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej
- [Apache Maven](https://maven.apache.org)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Apache Maven](https://maven.apache.org) w oknie terminalu systemu Linux.

## <a name="setting-up"></a>Konfigurowanie
Ten przewodnik Szybki Start korzysta z biblioteki Azure Identity Library z interfejsem wiersza polecenia platformy Azure w celu uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również używać programu Visual Studio lub Visual Studio Code do uwierzytelniania wywołań, aby uzyskać więcej informacji, zobacz [uwierzytelnianie klienta przy użyciu biblioteki klienta tożsamości platformy Azure](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

   W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="create-a-new-java-console-app"></a>Tworzenie nowej aplikacji konsolowej Java
W oknie konsoli Użyj `mvn` polecenia, aby utworzyć nową aplikację konsolową Java o nazwie `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Dane wyjściowe generowania projektu będą wyglądać następująco:

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

### <a name="install-the-package"></a>Zainstaluj pakiet
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
Utwórz zasady dostępu dla magazynu kluczy, które przyznaje kluczowe uprawnienia do konta użytkownika.

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
Biblioteka klienta klucza Azure Key Vault dla języka Java umożliwia zarządzanie kluczami. W sekcji [przykłady kodu](#code-examples) pokazano, jak utworzyć klienta, utworzyć klucz, pobrać klucz i usunąć klucz.

Cała Aplikacja konsolowa znajduje się [poniżej](#sample-code).

## <a name="code-examples"></a>Przykłady kodu
### <a name="add-directives"></a>Dodaj dyrektywy
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
W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w Key Vault, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy ["DefaultAzureCredential ()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) , która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji, zobacz [domyślne uwierzytelnianie poświadczeń platformy Azure](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Utwórz klucz
Po uwierzytelnieniu aplikacji można utworzyć klucz w magazynie kluczy przy użyciu `keyClient.createKey` metody. Wymaga to nazwy klucza i typu klucza — przypisano wartość "klucze" do `keyName` zmiennej i użyj RSA `KeyType` w tym przykładzie.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Możesz sprawdzić, czy klucz został ustawiony za pomocą polecenia [AZ Key magazynu klucz show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Pobierz klucz
Teraz można pobrać poprzednio utworzony klucz za pomocą `keyClient.getKey` metody.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Teraz możesz uzyskać dostęp do szczegółów pobranego klucza za pomocą operacji takich jak `retrievedKey.getProperties` , `retrievedKey.getKeyOperations` itp.

### <a name="delete-a-key"></a>Usuń klucz
Na koniec usuńmy klucz z magazynu kluczy za pomocą `keyClient.beginDeleteKey` metody.

Usuwanie klucza jest długotrwałą operacją, dla której można sondować postęp lub poczekać na jego zakończenie.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Możesz sprawdzić, czy klucz został usunięty za pomocą polecenia [AZ Key magazynu klucz show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
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
W tym przewodniku szybki start utworzono Magazyn kluczy, utworzono klucz, pobrano go, a następnie został usunięty. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Przeczytaj [Omówienie zabezpieczeń Key Vault](../general/security-overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Jak [zabezpieczyć dostęp do magazynu kluczy](../general/secure-your-key-vault.md)