---
title: Samouczek — tożsamość zarządzana umożliwiająca łączenie Key Vault ze sprężyną aplikacji w chmurze platformy Azure
description: Skonfiguruj tożsamość zarządzaną, aby połączyć Key Vault z aplikacją w chmurze Azure wiosną
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fc44dd6cf91d687f47afadf1c3378956d838bc9d
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579508"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Samouczek: używanie tożsamości zarządzanej do łączenia Key Vault z aplikacją Azure wiosennej w chmurze

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla aplikacji w chmurze Azure wiosennej i za jej pomocą uzyskać dostęp do Azure Key Vault.

Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych aplikacji. Można utworzyć tożsamość zarządzaną w usłudze Azure Active Directory (AAD) i uwierzytelniać się w dowolnej usłudze obsługującej uwierzytelnianie w usłudze AAD, w tym Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, która będzie zawierać zarówno Key Vault jak i chmurę sieciową za pomocą polecenia [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Skonfiguruj Key Vault
Aby utworzyć Key Vault, użyj polecenia [AZ Create webmagazynion](/cli/azure/keyvault?view=azure-cli-latest&preserve-view=true#az-keyvault-create):

> [!Important]
> Każdy Key Vault musi mieć unikatową nazwę. Zastąp <nazwą magazynu kluczy> nazwą Key Vault w poniższych przykładach.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Zanotuj zwracaną wartość `vaultUri` , która będzie w formacie "https://<nazwę magazynu kluczy>. Vault.Azure.NET". Zostanie ona użyta w następnym kroku.

Wpis tajny można teraz umieścić w Key Vault za pomocą polecenia [AZ klucz Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest&preserve-view=true#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Tworzenie usługi i aplikacji w chmurze Azure wiosny
Po zainstalowaniu odpowiedniego rozszerzenia Utwórz wystąpienie chmurowej usługi Azure wiosny przy użyciu polecenia platformy Azure `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
W poniższym przykładzie jest tworzona aplikacja o nazwie `springapp` z zarządzaną przez system tożsamością żądaną przez `--assign-identity` parametr.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Zanotuj zwracaną wartość `url` , która będzie w formacie "https://<nazwę aplikacji>. azuremicroservices.IO". Zostanie ona użyta w następnym kroku.


## <a name="grant-your-app-access-to-key-vault"></a>Przyznaj aplikacji dostęp do Key Vault
Użyj `az keyvault set-policy` , aby udzielić odpowiedniego dostępu w Key Vault aplikacji.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Użyj `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` , aby usunąć dostęp dla aplikacji po wyłączeniu tożsamości zarządzanej przypisanej do systemu.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Tworzenie przykładowej aplikacji do rozruchu sprężynowego z rozruchem sprężynowym Starter
Ta aplikacja będzie miała dostęp do pobierania wpisów tajnych z Azure Key Vault. Użyj Starter App: [Azure Key Vault Secret sprężynowego rozruchu Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault jest dodawany jako wystąpienie **PropertySource** sprężyny.  Wpisy tajne przechowywane w Azure Key Vault mogą być łatwo dostępne i używane jak każda zewnętrzna właściwość konfiguracji, taka jak właściwości w plikach. 

1. Generowanie przykładowego projektu z start.spring.io za pomocą Azure Key Vault sprężyny Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Określ Key Vault w aplikacji. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Aby korzystać z tożsamości zarządzanej dla aplikacji chmurowych platformy Azure, Dodaj właściwości z zawartością poniżej do elementu src/Main/sources/Application. Properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Należy dodać adres URL magazynu kluczy w `application.properties` jak powyżej. W przeciwnym razie adres URL magazynu kluczy może nie być przechwytywany podczas środowiska uruchomieniowego.

3. Dodaj przykładowy kod do src/Main/Java/com/example/demonstracyjny/DemoApplication. Java. Pobiera parametry połączenia z Key Vault. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Jeśli otworzysz pom.xml, zostanie wyświetlona zależność `azure-keyvault-secrets-spring-boot-starter` . Dodaj tę zależność do projektu w pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Pakowanie przykładowej aplikacji. 

    ```azurecli
    mvn clean package
    ```

5. Teraz możesz wdrożyć swoją aplikację na platformie Azure za pomocą polecenia interfejsu CLI platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Aby przetestować aplikację, uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Zostanie wyświetlony komunikat "pomyślnie pobrano wartość klucza tajnego z Key Vault https://<identyfikatorem magazynu kluczy>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; baza danych = baza danych;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Tworzenie przykładowej aplikacji rozruchowej sprężyny przy użyciu zestawu Java SDK

Ten przykład umożliwia ustawianie i pobieranie wpisów tajnych z Azure Key Vault. [Biblioteka klienta Azure Key Vault Secret dla języka Java](/java/api/overview/azure/security-keyvault-secrets-readme?preserve-view=true&view=azure-java-stablelibrary) zapewnia obsługę uwierzytelniania tokenów Azure Active Directory w ramach zestawu Azure SDK. Zawiera zestaw implementacji **TokenCredential** , które mogą służyć do konstruowania klientów zestawu Azure SDK do obsługi uwierzytelniania tokenów usługi AAD.

Biblioteka klienta Secret Azure Key Vault pozwala bezpiecznie przechowywać i kontrolować dostęp do tokenów, haseł, kluczy interfejsu API i innych wpisów tajnych. Biblioteka zawiera operacje tworzenia, pobierania, aktualizowania, usuwania, przeczyszczania, tworzenia kopii zapasowych, przywracania i wyświetlania listy wpisów tajnych oraz ich wersji.

1. Klonowanie przykładowego projektu. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Określ Key Vault w aplikacji. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Aby korzystać z tożsamości zarządzanej dla aplikacji chmurowych platformy Azure, Dodaj właściwości z następującą zawartością do *src/Main/sources/Application. Properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Uwzględnij [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder?preserve-view=true&view=azure-java-stable) , aby uzyskać token z Azure Active Directory i [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?preserve-view=true&view=azure-java-stable) , aby ustawić lub pobrać klucze tajne z Key Vault w kodzie.

    Pobierz przykład z [MainController. Java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) dla sklonowanego przykładowego projektu.

    Należy również uwzględnić `azure-identity` i `azure-security-keyvault-secrets` jako zależność w pom.xml. Pobierz przykład z [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) sklonowanego przykładowego projektu. 

4. Pakowanie przykładowej aplikacji. 

    ```azurecli
    mvn clean package
    ```

5. Teraz należy wdrożyć aplikację na platformie Azure przy użyciu polecenia platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego, aby przetestować aplikację. 

    Najpierw należy pobrać wartość klucza tajnego ustawioną w Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Zostanie wyświetlony komunikat "pomyślnie pobrano wartość klucza tajnego z Key Vault https://<identyfikatorem magazynu kluczy>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; baza danych = baza danych;".

    Teraz Utwórz klucz tajny, a następnie pobierz go przy użyciu zestawu Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Zostanie wyświetlony komunikat "pomyślnie otrzymano wartość testu tajnego z Key Vault https://<nazwę magazynu kluczy>. vault.azure.net: Success". 

## <a name="next-steps"></a>Następne kroki

* [Jak uzyskać dostęp do obiektu blob magazynu z tożsamością zarządzaną w chmurze Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla aplikacji w chmurze platformy Azure](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Uwierzytelnianie chmury Azure wiosennej za pomocą Key Vault w akcjach GitHub](./spring-cloud-github-actions-key-vault.md)
