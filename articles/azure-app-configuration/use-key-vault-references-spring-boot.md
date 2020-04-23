---
title: Samouczek dotyczący korzystania z odwołań do magazynu kluczy konfiguracji aplikacji Azure w aplikacji Java Spring Boot | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak korzystać z odwołań do usługi Azure App Configuration's Key Vault z aplikacji Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 6a5bc947c3ea414f197df9cfcdd5f233e4654cbc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085029"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Samouczek: Używanie odwołań do magazynu kluczy w aplikacji Java Spring

W tym samouczku dowiesz się, jak korzystać z usługi konfiguracji aplikacji platformy Azure wraz z usługą Azure Key Vault. Konfiguracja aplikacji i usługa Key Vault są usługami uzupełniającymi używanymi obok siebie w większości wdrożeń aplikacji.

Konfiguracja aplikacji ułatwia korzystanie z usług razem, tworząc klucze, które odwołują się do wartości przechowywanych w przechowalni kluczy. Gdy konfiguracja aplikacji tworzy takie klucze, przechowuje identyfikatory URI wartości magazynu kluczy, a nie same wartości.

Aplikacja używa dostawcy klienta konfiguracji aplikacji do pobierania odwołań do usługi Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w konfiguracji aplikacji. W takim przypadku wartości przechowywane w konfiguracji aplikacji są identyfikatory URI, które odwołują się do wartości w magazynie kluczy. Nie są to wartości ani poświadczenia usługi Key Vault. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania do magazynu kluczy, używa usługi Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i w magazynie kluczy. Dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować odwołania do usługi Key Vault w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw [ukończyć tworzenie aplikacji Java Spring z konfiguracją aplikacji.](./quickstart-java-spring-app.md)

Aby wykonać czynności opisane w tym samouczku, można użyć dowolnego edytora kodu. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to wieloplatformowy edytor kodu, który jest dostępny dla systemów operacyjnych Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w magazynie kluczy.
> * Dostęp do wartości tego klucza z aplikacji Java Spring.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Obsługiwany zestaw [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) w wersji 8.
* [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3.0 lub wyższej.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal:

    ![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **przechowalnię kluczy**.
1. Z listy wyników wybierz **pozycję Przechowalnia kluczy** po lewej stronie.
1. W **obszarze Przechowalnia kluczy**wybierz pozycję **Dodaj**.
1. Po prawej stronie w **programie Utwórz magazyn kluczy**podaj następujące informacje:
    * Wybierz **subskrypcję,** aby wybrać subskrypcję.
    * W **obszarze Grupa zasobów**wybierz pozycję **Utwórz nowy** i wprowadź nazwę grupy zasobów.
    * W **nazwie magazynu kluczy**wymagana jest unikatowa nazwa. W tym samouczku wprowadź **Contoso-vault2**.
    * Z listy rozwijanej **Region** wybierz lokalizację.
1. Pozostaw inne opcje **tworzenia magazynu kluczy** z wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

W tym momencie twoje konto platformy Azure jest jedynym autoryzowanym do uzyskania dostępu do tego nowego magazynu.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać klucz tajny do magazynu, musisz wykonać tylko kilka dodatkowych kroków. W takim przypadku należy dodać komunikat, którego można użyć do przetestowania pobierania usługi Key Vault. Wiadomość nosi nazwę **Message**i przechowujesz w niej wartość "Hello from Key Vault".

1. Na stronach właściwości magazynu kluczy wybierz pozycję **Wpisy tajne**.
1. Wybierz **pozycję Generuj/Importuj**.
1. W okienku **Utwórz klucz tajny** wprowadź następujące wartości:
    * **Opcje przesyłania**: Wprowadź **instrukcję**.
    * **Nazwa**: Wprowadź **wiadomość**.
    * **Wartość**: Wprowadź **Hello z przechowalni kluczy**.
1. Pozostaw inne Tworzenie właściwości **tajnych** z ich wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodawanie odwołania do usługi Key Vault do konfiguracji aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz wystąpienie sklepu konfiguracja aplikacji utworzone w przewodniku Szybki start.

1. Wybierz **Pozycję Eksplorator konfiguracji**.

1. Wybierz **+ Utwórz** > **odwołanie do przechowalni kluczy,** a następnie określ następujące wartości:
    * **Klucz**: Wybierz **/application/config.keyvaultmessage**
    * **Etykieta**: Pozostaw tę wartość pustą.
    * **Subskrypcja,** **Grupa zasobów**i **Magazyn kluczy**: Wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    * **Klucz tajny:** Wybierz klucz tajny o nazwie **Wiadomość** utworzona w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Łączenie się z przechowalnią kluczy

1. W tym samouczku używasz jednostki usługi do uwierzytelniania do usługi Key Vault. Aby utworzyć tego podmiotu usługi, użyj polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwraca serię par klucz/wartość:

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

1. Uruchom następujące polecenie, aby umożliwić podmiotowi usługi dostęp do magazynu kluczy:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Uruchom następujące polecenie, aby uzyskać identyfikator obiektu, a następnie dodaj go do konfiguracji aplikacji.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Utwórz następujące zmienne środowiskowe, używając wartości jednostki usługi, które były wyświetlane w poprzednim kroku:

    * **AZURE_CLIENT_ID**: *identyfikator klienta*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *identyfikator najemcy*

> [!NOTE]
> Te poświadczenia usługi Key Vault są używane tylko w aplikacji.  Aplikacja uwierzytelnia się bezpośrednio za pomocą usługi Key Vault przy użyciu tych poświadczeń bez korzystania z usługi konfiguracji aplikacji.  Usługa Key Vault zapewnia uwierzytelnianie zarówno dla aplikacji, jak i usługi konfiguracji aplikacji bez udostępniania lub udostępniania kluczy.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizowanie kodu w celu użycia odwołania do usługi Key Vault

1. Utwórz zmienną środowiskową o nazwie **APP_CONFIGURATION_ENDPOINT**. Ustaw jego wartość na punkt końcowy dla magazynu konfiguracji aplikacji. Punkt końcowy można znaleźć w bloku **kluczy dostępu** w witrynie Azure portal.

1. Otwórz *bootstrap.properties* w folderze *zasobów.* Zaktualizuj ten plik, aby użyć punktu końcowego konfiguracji aplikacji, a nie ciągu połączenia.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Otwórz *messageproperties.java*. Dodaj nową zmienną o nazwie *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otwórz *HelloController.java*. Zaktualizuj metodę *getMessage,* aby uwzględnić wiadomość pobraną z usługi Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Utwórz nowy plik o nazwie *AzureCredentials.java* i dodaj poniższy kod.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Utwórz nowy plik o nazwie *AppConfiguration.java*. I dodać poniższy kod.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Utwórz nowy plik w katalogu META-INF zasobów o nazwie *spring.factorys* i dodaj.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Zbuduj aplikację Spring Boot za pomocą Maven i uruchom ją, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Po uruchomieniu aplikacji użyj *curl,* aby przetestować aplikację, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Zostanie wyświetlony komunikat wprowadzony w sklepie Konfiguracja aplikacji. Zostanie również wyświetlony komunikat wprowadzony w przechowalni kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w magazynie kluczy. Aby dowiedzieć się, jak używać flag funkcji w aplikacji Java Spring, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./quickstart-feature-flag-spring-boot.md)
