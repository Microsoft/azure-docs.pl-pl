---
title: Szybki start — biblioteka klienta usługi Azure Key Vault dla języka Java
description: Zawiera kryteria formatu i zawartości do pisania przewodników Szybki start dla bibliotek klienta SDK platformy Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 7e0d05ce97def0a255e6ac9909544b04fb13bb37
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425028"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Szybki start: biblioteka klienta usługi Azure Key Vault dla oprogramowania Java

Wprowadzenie do biblioteki klienta usługi Azure Key Vault dla oprogramowania Java. Wykonaj poniższe czynności, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta usługi Key Vault dla oprogramowania Java, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Tworzenie i importowanie kluczy szyfrowania w ciągu kilku minut.
- Zmniejsz opóźnienia dzięki skalowaniu chmury i nadmiarowości globalnej.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj modułów HSM z walidacji FIPS 140-2 Poziomu 2.

[Kod źródłowy](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)[API dokumentacja referencyjna](https://azure.github.io/azure-sdk-for-java) | [Dokumentacja](index.yml) | [produktu Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)  | 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub wyższej
- [Apache Maven](https://maven.apache.org)
- [Narzędzie interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest) sieci owych lub [programu Azure PowerShell platformy Azure](/powershell/azure/overview)

Ten przewodnik Szybki start zakłada, że używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) i [Apache Maven](https://maven.apache.org) w oknie terminala systemu Linux.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-new-java-console-app"></a>Tworzenie nowej aplikacji konsoli Java

W oknie konsoli użyj `mvn` polecenia, aby utworzyć nową aplikację `akv-java`konsoli Java o tej nazwie .

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Dane wyjściowe z generowania projektu będą wyglądać mniej więcej tak:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Zmień katalog na nowo utworzony folder akv-java/.

```console
cd akv-java
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik *pom.xml* w edytorze tekstu. Dodaj następujące elementy zależności do grupy zależności.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki start korzysta z wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując kroki w [przewodniku Szybki start interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [przewodniku Szybki start usługi Azure PowerShell](quick-create-powershell.md)lub [przewodniku Szybki start w portalu Azure.](quick-create-portal.md) Alternatywnie można uruchomić polecenia interfejsu wiersza polecenia platformy Azure poniżej.

> [!Important]
> Każdy magazyn kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji opartej na chmurze jest przy użyciu tożsamości zarządzanej; Zobacz [Korzystanie z tożsamości zarządzanej usługi App Service, aby uzyskać dostęp do usługi Azure Key Vault, aby](../general/managed-identity.md) uzyskać szczegółowe informacje. Ze względu na prostotę jednak ten szybki start tworzy aplikację klasyczną, która wymaga użycia jednostki usługi i zasad kontroli dostępu.

Utwórz zasadę usługi przy użyciu polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ta operacja zwróci serię par kluczy / wartości. 

```console
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

Zanotuj identyfikator klienta, clientSecret i identyfikator dzierżawy, ponieważ użyjemy ich w kolejnych dwóch krokach.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udzielanie podmiotowi usługi dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który udziela uprawnień do jednostki usługi, przekazując clientId do [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) polecenia. Nadaj jednostce usługi get, list i set permissions for both keys and secrets.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ustawianie zmiennych środowiskowych

Metoda DefaultAzureCredential w naszej aplikacji opiera się `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`na `AZURE_TENANT_ID`trzech zmiennych środowiskowych: , , i . użyj ustawić te zmienne do clientId, clientSecret i tenantId wartości, które zostały odnotowane w [Wykonaj jednostki usługi](#create-a-service-principal) krok powyżej. Użyj `export VARNAME=VALUE` formatu, aby ustawić zmienne środowiskowe. (Ta metoda ustawia tylko zmienne dla bieżącej powłoki i procesów utworzonych z powłoki; aby trwale dodać te zmienne do środowiska, edytować `/etc/environment ` plik). 

Należy również zapisać nazwę magazynu kluczy jako `KEY_VAULT_NAME`zmienną środowiskową o nazwie .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Model obiektu

Biblioteka klienta usługi Azure Key Vault dla języka Java umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokażą, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny.

Cała aplikacja konsoli znajduje się [poniżej](#sample-code).

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy do górnej części kodu:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnij i utwórz klienta

Uwierzytelnianie w magazynie kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych w kroku [Ustaw zmienne środowiskowe](#set-environmental-variables) powyżej. Nazwa magazynu kluczy jest rozwijana do identyfikatora URI magazynu kluczy w formacie `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Zapisywanie tajemnicy

Teraz, gdy aplikacja jest uwierzytelniona, można umieścić klucz tajny `secretClient.setSecret` w keyvault przy użyciu metody. Wymaga to nazwy klucza tajnego — przydzieliliśmy wartość "mySecret" do zmiennej `secretName` w tym przykładzie.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Można sprawdzić, czy klucz tajny został ustawiony za pomocą polecenia [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobieranie klucza tajnego

Teraz można pobrać wcześniej ustawioną wartość `secretClient.getSecret` za pomocą metody.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Teraz można uzyskać dostęp do wartości `retrievedSecret.getValue()`pobranego klucza tajnego za pomocą programu .

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec usuńmy klucz tajny z magazynu `secretClient.beginDeleteSecret` kluczy za pomocą metody.

```java
secretClient.beginDeleteSecret(secretName);
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

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, zapis tajny i pobrano ten klucz tajny. Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dla deweloperów usługi Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)
