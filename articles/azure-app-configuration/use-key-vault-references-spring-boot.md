---
title: Samouczek dotyczący używania odwołań Azure App Configuration Key Vault w aplikacji Java Spring Boot do | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać Azure App Configuration odwołań Key Vault z aplikacji Java Spring Boot aplikacji
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7c5534ab836968bc4e72a54db1ddb9667d366558
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768853"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Samouczek: używanie Key Vault w aplikacji Java Spring

Z tego samouczka dowiesz się, jak używać usługi Azure App Configuration razem z Azure Key Vault. App Configuration i Key Vault to uzupełniające się usługi używane obok siebie w większości wdrożeń aplikacji.

App Configuration usług, tworząc klucze odwołujące się do wartości przechowywanych w programie Key Vault. Gdy App Configuration takie klucze, przechowuje ona Key Vault, a nie same wartości.

Aplikacja używa dostawcy App Configuration klienta do pobierania odwołań Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w App Configuration. W tym przypadku wartości przechowywane w App Configuration są AMI odwołującymi się do wartości w Key Vault. Nie są to Key Vault ani poświadczenia. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania Key Vault, używa Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w App Configuration, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować odwołania Key Vault w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem ukończ tworzenie [aplikacji Java Spring przy użyciu App Configuration](./quickstart-java-spring-app.md) najpierw.

Możesz użyć dowolnego edytora kodu, aby wykonać kroki opisane w tym samouczku. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to międzyplatformowy edytor kodu dostępny dla systemów operacyjnych Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz App Configuration, który odwołuje się do wartości przechowywanej w Key Vault.
> * Uzyskaj dostęp do wartości tego klucza z aplikacji Java Spring.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
* Obsługiwany zestaw [Java Development Kit (JDK) w](/java/azure/jdk) wersji 8.
* [Program Apache Maven](https://maven.apache.org/download.cgi) w wersji 3.0 lub nowszej.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz **opcję Utwórz zasób** w lewym górnym rogu okna Azure Portal:

    ![Zrzut ekranu przedstawia opcję Utwórz zasób w Azure Portal.](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **magazyn kluczy**.
1. Z listy wyników wybierz pozycję **Magazyny kluczy** po lewej stronie.
1. W **chmurze Magazyny kluczy** wybierz pozycję **Dodaj**.
1. Po prawej stronie w **chmurze Create key vault (Tworzenie** magazynu kluczy) podaj następujące informacje:
    * Wybierz **pozycję Subskrypcja,** aby wybrać subskrypcję.
    * W **grupie zasobów** wybierz pozycję Utwórz **nową** i wprowadź nazwę grupy zasobów.
    * W **nazwie magazynu kluczy** wymagana jest unikatowa nazwa. W tym samouczku wprowadź **wartość Contoso-vault2**.
    * Z **listy rozwijanej** Region wybierz lokalizację.
1. Pozostaw wartości domyślne pozostałych opcji **Utwórz magazyn** kluczy.
1. Wybierz przycisk **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym kontem z uprawnieniami dostępu do tego nowego magazynu.

![Zrzut ekranu przedstawia magazyn kluczy.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać klucz tajny do magazynu, należy wykonać tylko kilka dodatkowych kroków. W takim przypadku dodaj komunikat, który umożliwia przetestowanie Key Vault pobierania. Komunikat ma nazwę **Message** i przechowujesz w nim wartość "Hello from Key Vault".

1. Na stronie Key Vault właściwości wybierz pozycję **Wpisy tajne.**
1. Wybierz **pozycję Wygeneruj/Zaimportuj.**
1. W **okienku Tworzenie wpisów** tajnych wprowadź następujące wartości:
    * **Opcje przekazywania:** wprowadź **ręczne .**
    * **Nazwa:** Wprowadź **komunikat**.
    * **Wartość:** wprowadź **Hello z Key Vault**.
1. Pozostaw pozostałe **właściwości Utwórz klucz tajny** z ich wartościami domyślnymi.
1. Wybierz przycisk **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodawanie odwołania Key Vault do App Configuration

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **pozycję Wszystkie** zasoby, a następnie wybierz App Configuration magazynu utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Configuration Explorer**.

1. Wybierz **pozycję + Utwórz** odwołanie do magazynu  >  **kluczy,** a następnie określ następujące wartości:
    * **Klucz:** Wybierz **pozycję /application/config.keyvaultmessage**
    * **Etykieta:** pozostaw tę wartość pustą.
    * **Subskrypcja,** **grupa zasobów** i **magazyn kluczy:** wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    * **Klucz** tajny: wybierz klucz tajny o nazwie **Message** utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Nawiązywanie połączenia z Key Vault

1. W tym samouczku użyjemy jednostki usługi do uwierzytelniania w Key Vault. Aby utworzyć tę jednostkę usługi, użyj polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwraca serię par klucz/wartość:

    ```json
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

1. Uruchom następujące polecenie, aby pozwolić jednostki usługi na dostęp do magazynu kluczy:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Uruchom następujące polecenie, aby uzyskać identyfikator obiektu, a następnie dodaj go do App Configuration.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Utwórz zmienne środowiskowe **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** i **AZURE_TENANT_ID**. Użyj wartości jednostki usługi, które zostały wyświetlone w poprzednich krokach. W wierszu polecenia uruchom następujące polecenia i uruchom ponownie wiersz polecenia, aby umożliwić zmianę w celu jej zmiany:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Jeśli używasz Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Te Key Vault są używane tylko w aplikacji.  Aplikacja uwierzytelnia się bezpośrednio za Key Vault przy użyciu tych poświadczeń bez angażowania App Configuration usługi.  Usługa Key Vault uwierzytelnianie dla aplikacji i usługi App Configuration bez udostępniania ani ujawniania kluczy.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizowanie kodu w celu używania odwołania Key Vault danych

1. Utwórz zmienną środowiskową o **nazwie APP_CONFIGURATION_ENDPOINT**. Ustaw jej wartość na punkt końcowy magazynu App Configuration magazynu. Punkt końcowy można znaleźć w bloku **Klucze dostępu** w Azure Portal. Uruchom ponownie wiersz polecenia, aby zezwolić na zmianę. 


1. Otwórz *plik bootstrap.properties* w *folderze resources.* Zaktualizuj ten plik, aby użyć **APP_CONFIGURATION_ENDPOINT** wartości. Usuń wszystkie odwołania do parametrów połączenia w tym pliku. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Otwórz *skrypt MessageProperties.java.* Dodaj nową zmienną o *nazwie keyVaultMessage:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otwórz *bibliotekę HelloController.java.* Zaktualizuj metodę *getMessage,* aby uwzględnić komunikat pobrany z Key Vault.

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

1. Utwórz nowy plik o nazwie *AppConfiguration.java.* Dodaj poniższy kod.

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

1. Utwórz nowy plik w katalogu META-INF zasobów o nazwie *spring.factories* i dodaj poniższy kod.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Skompilowanie Spring Boot za pomocą programu Maven i uruchomienie jej, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Po uruchomieniu aplikacji użyj programu *curl,* aby przetestować aplikację, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Zostanie wyświetlony komunikat wprowadzony w App Configuration magazynu. Zostanie również wyświetlony komunikat wprowadzony w Key Vault.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono klucz App Configuration, który odwołuje się do wartości przechowywanej w Key Vault. Aby dowiedzieć się, jak używać flag funkcji w aplikacji Java Spring, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./quickstart-feature-flag-spring-boot.md)
