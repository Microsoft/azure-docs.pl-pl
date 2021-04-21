---
title: 'Samouczek: tożsamość zarządzana do łączenia Key Vault'
description: Konfigurowanie tożsamości zarządzanej w celu połączenia Key Vault z Azure Spring Cloud aplikacji
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 870a04af244d18826e1041316895f746e27870eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786589"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Samouczek: używanie tożsamości zarządzanej do nawiązywania Key Vault z Azure Spring Cloud aplikacji

**Ten artykuł dotyczy: ✔️** Java

W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla aplikacji Azure Spring Cloud i używać jej do uzyskiwania dostępu do Azure Key Vault.

Azure Key Vault służy do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych aplikacji. Tożsamość zarządzaną można utworzyć w usłudze Azure Active Directory (AAD) i uwierzytelnić w dowolnej usłudze obsługującej uwierzytelnianie usługi AAD, w tym w usłudze Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

## <a name="prerequisites"></a>Wymagania wstępne

* [Rejestracja w celu subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub wyższej](/cli/azure/install-azure-cli)
* [Zainstaluj program Maven 3.0 lub jego więcej](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, która będzie zawierać zarówno Key Vault, jak i Spring Cloud za pomocą polecenia [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Konfigurowanie Key Vault
Aby utworzyć Key Vault, użyj polecenia [az keyvault create](/cli/azure/keyvault#az_keyvault_create):

> [!Important]
> Każda Key Vault musi mieć unikatową nazwę. Zastąp <your-keyvault-name> nazwą swojej Key Vault w poniższych przykładach.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Zanotuj zwrócony element w formacie `vaultUri` "https://<-nazwa-klucza>.vault.azure.net". Zostanie on użyty w następnym kroku.

Teraz możesz umieścić klucz tajny w kluczu Key Vault za pomocą polecenia [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Tworzenie Azure Spring Cloud aplikacji i usługi
Po zainstalowaniu odpowiedniego rozszerzenia utwórz wystąpienie Azure Spring Cloud za pomocą polecenia interfejsu wiersza polecenia platformy Azure `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Poniższy przykład tworzy aplikację o nazwie z tożsamością zarządzaną przypisaną przez `springapp` system zgodnie z żądaniem `--assign-identity` parametru .

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Zanotuj zwrócony obiekt `url` , który będzie w formacie `https://<your-app-name>.azuremicroservices.io` . Zostanie on użyty w następnym kroku.


## <a name="grant-your-app-access-to-key-vault"></a>Udzielanie aplikacji dostępu do Key Vault
Użyj `az keyvault set-policy` , aby udzielić prawidłowego dostępu Key Vault aplikacji.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Użyj , aby usunąć dostęp do aplikacji po wyłączeniu tożsamości `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` zarządzanej przypisanej przez system.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Tworzenie przykładowej aplikacji Spring Boot za pomocą Spring Boot starter
Ta aplikacja będzie mieć dostęp do uzyskiwania wpisów tajnych z Azure Key Vault. Użyj aplikacji startowej: użyj [Azure Key Vault Secrets Spring Boot.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)  Azure Key Vault jest dodawany jako wystąpienie właściwości Spring **PropertySource**.  Wpisy tajne przechowywane w Azure Key Vault można wygodnie uzyskiwać do nich dostęp i używać ich tak jak każdej zewnętrznej właściwości konfiguracji, takiej jak właściwości w plikach. 

1. Wygeneruj przykładowy projekt na podstawie start.spring.io za pomocą Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Określ Key Vault w aplikacji. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Aby używać tożsamości zarządzanej dla Azure Spring Cloud, dodaj właściwości z poniższym zawartością do pliku src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Należy dodać adres URL magazynu kluczy w pliku `application.properties` , jak powspomniano powyżej. W przeciwnym razie adres URL magazynu kluczy może nie zostać przechwycony podczas wykonywania.

3. Dodaj przykładowy kod do pliku src/main/java/com/example/demo/DemoApplication.java. Pobiera ona ciąg połączenia z Key Vault. 

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

    Jeśli otworzysz pom.xml, zobaczysz zależność `azure-keyvault-secrets-spring-boot-starter` . Dodaj tę zależność do projektu w pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Spakuj przykładową aplikację. 

    ```azurecli
    mvn clean package
    ```

5. Teraz możesz wdrożyć aplikację na platformie Azure za pomocą polecenia interfejsu wiersza polecenia platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Aby przetestować aplikację, uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Zostanie wyświetlony komunikat "Pomyślnie wybraliśmy wartość ciągu połączenia tajnego z witryny Key Vault https://<nazwa-klucza>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Tworzenie przykładowej aplikacji Spring Boot za pomocą zestawu Java SDK

Ten przykład może ustawiać i pobierać wpisy tajne z Azure Key Vault. Biblioteka [klienta Azure Key Vault Secret dla języka Java](/java/api/overview/azure/security-keyvault-secrets-readme) Azure Active Directory obsługę uwierzytelniania tokenów w zestawie Azure SDK. Udostępnia zestaw implementacji **TokenCredential,** których można użyć do konstruowania klientów zestawu Azure SDK do obsługi uwierzytelniania tokenu usługi AAD.

Biblioteka klienta Azure Key Vault Secret umożliwia bezpieczne przechowywanie i kontrolowanie dostępu do tokenów, haseł, kluczy interfejsu API i innych wpisów tajnych. Biblioteka oferuje operacje tworzenia, pobierania, aktualizowania, usuwania, przeczyszczania, tworzenia kopii zapasowej, przywracania i tworzenia listy wpisów tajnych oraz ich wersji.

1. Sklonuj przykładowy projekt. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Określ Key Vault w aplikacji. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Aby używać tożsamości zarządzanej dla Azure Spring Cloud, dodaj właściwości o następującej zawartości do *pliku src/main/resources/application.properties.*

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Dołącz [wartość ManagedIdentityCredentialBuilder,](/java/api/com.azure.identity.managedidentitycredentialbuilder) aby uzyskać token z Azure Active Directory i [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) w celu ustawienia lub Key Vault wpisów tajnych w kodzie.

    Pobierz przykład z [pliku MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) sklonowanego przykładowego projektu.

    Uwzględnij `azure-identity` również i jako `azure-security-keyvault-secrets` zależność w pom.xml. Pobierz przykład z [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) sklonowanego przykładowego projektu. 

4. Spakuj przykładową aplikację. 

    ```azurecli
    mvn clean package
    ```

5. Teraz wd wdrażaj aplikację na platformie Azure za pomocą polecenia interfejsu wiersza polecenia platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego, aby przetestować aplikację. 

    Najpierw pobierz wartość swojego tajnego klucz tajny ustawioną w Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Zostanie wyświetlony komunikat "Pomyślnie wybraliśmy wartość ciągu połączenia tajnego z witryny Key Vault https://<nazwa-klucza>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

    Teraz utwórz klucz tajny, a następnie pobierz go przy użyciu zestawu Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Zostanie wyświetlony komunikat "Pomyślnie uzyskał wartość testu tajnego z Key Vault https://<your-keyvault-name>.vault.azure.net: success". 

## <a name="next-steps"></a>Następne kroki

* [Jak uzyskać dostęp do obiektu blob usługi Storage przy użyciu tożsamości zarządzanej w Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla Azure Spring Cloud aplikacji](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Uwierzytelnianie Azure Spring Cloud przy użyciu Key Vault w GitHub Actions](./spring-cloud-github-actions-key-vault.md)
