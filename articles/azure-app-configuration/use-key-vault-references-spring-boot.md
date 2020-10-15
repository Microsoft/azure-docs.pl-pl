---
title: Samouczek dotyczący używania usługi Azure App Configuration Key Vault References w aplikacji rozruchu ze sprężyną Java | Microsoft Docs
description: W tym samouczku dowiesz się, jak używać odwołań Key Vault konfiguracji aplikacji platformy Azure z poziomu aplikacji z rozruchem ze sprężyną Java
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
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 3cdb7796f3f8c877f130f47b971dc66c113c4d36
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070116"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Samouczek: Używanie odwołań Key Vault w aplikacji ze sprężyną Java

W tym samouczku dowiesz się, jak korzystać z usługi Azure App Configuration razem z Azure Key Vault. Konfiguracja aplikacji i Key Vault to uzupełniające się usługi używane obok większości wdrożeń aplikacji.

Konfiguracja aplikacji ułatwia korzystanie z usług, tworząc klucze, które odwołują się do wartości przechowywanych w Key Vault. Gdy konfiguracja aplikacji tworzy takie klucze, przechowuje identyfikatory URI wartości Key Vault, a nie same wartości.

Aplikacja używa dostawcy klienta konfiguracji aplikacji do pobierania odwołań Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w konfiguracji aplikacji. W takim przypadku wartości przechowywane w konfiguracji aplikacji to identyfikatory URI, które odwołują się do wartości w Key Vault. Nie Key Vault wartości ani poświadczeń. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania Key Vault, używa Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować Key Vault odwołania w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw [utworzyć aplikację ze sprężyną Java z konfiguracją aplikacji](./quickstart-java-spring-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to Międzyplatformowy Edytor kodu, który jest dostępny dla systemów operacyjnych Windows, MacOS i Linux.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault.
> * Uzyskaj dostęp do wartości tego klucza z aplikacji ze sprężyną Java.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Obsługiwany [zestaw Java Development Kit (JDK)](/java/azure/jdk) w wersji 8.
* System [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3,0 lub nowszej.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu Azure Portal:

    ![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **Key Vault**.
1. Z listy wyników wybierz pozycję **magazyny kluczy** po lewej stronie.
1. W obszarze **magazyny kluczy**wybierz pozycję **Dodaj**.
1. Po prawej stronie w temacie **Tworzenie magazynu kluczy**podaj następujące informacje:
    * Wybierz pozycję **subskrypcja** , aby wybrać subskrypcję.
    * W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    * W **nazwie magazynu kluczy**wymagana jest unikatowa nazwa. Na potrzeby tego samouczka wprowadź **contoso-vault2**.
    * Z listy rozwijanej **region** wybierz lokalizację.
1. Pozostaw inne opcje **tworzenia magazynu kluczy** z wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym autoryzowanym dostępem do tego nowego magazynu.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, należy wykonać zaledwie kilka dodatkowych kroków. W takim przypadku Dodaj komunikat, którego można użyć do przetestowania pobierania Key Vault. Wiadomość jest nazywana **komunikatem**i przechowuje w niej wartość "Hello from Key Vault".

1. Na stronie właściwości Key Vault wybierz pozycję wpisy **tajne**.
1. Wybierz pozycję **Generuj/Importuj**.
1. W okienku **Utwórz wpis tajny** wprowadź następujące wartości:
    * **Opcje przekazywania**: wprowadź **Ręczne**.
    * **Nazwa**: wprowadź **komunikat**.
    * **Wartość**: wprowadź **Hello z Key Vault**.
1. Pozostaw inne właściwości **klucza tajnego** z wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodaj odwołanie Key Vault do konfiguracji aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji**.

1. Wybierz pozycję **+ Utwórz**  >  **odwołanie do magazynu kluczy**, a następnie określ następujące wartości:
    * **Klucz**: Wybierz **/Application/config.keyvaultmessage**
    * **Etykieta**: pozostaw tę wartość pustą.
    * **Subskrypcja**, **Grupa zasobów**i **Magazyn kluczy**: wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    * **Wpis tajny**: Wybierz **komunikat** o nazwie Secret utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Połącz z Key Vault

1. W tym samouczku użyjesz nazwy głównej usługi do uwierzytelniania do Key Vault. Aby utworzyć tę nazwę główną usługi, użyj interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

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

1. Uruchom następujące polecenie, aby umożliwić jednostce usługi dostęp do magazynu kluczy:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Uruchom następujące polecenie, aby uzyskać identyfikator obiektu, a następnie dodaj go do konfiguracji aplikacji.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Utwórz zmienne środowiskowe **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET**i **AZURE_TENANT_ID**. Użyj wartości dla jednostki usługi, która została wyświetlona w poprzednich krokach. W wierszu polecenia Uruchom następujące polecenia i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Te poświadczenia Key Vault są używane tylko w aplikacji.  Aplikacja uwierzytelnia się bezpośrednio za pomocą Key Vault przy użyciu tych poświadczeń bez uwzględniania usługi konfiguracji aplikacji.  Key Vault zapewnia uwierzytelnianie zarówno aplikacji, jak i usługi konfiguracji aplikacji bez udostępniania ani ujawniania kluczy.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Zaktualizuj kod, aby użyć odwołania Key Vault

1. Utwórz zmienną środowiskową o nazwie **APP_CONFIGURATION_ENDPOINT**. Ustaw jej wartość na punkt końcowy magazynu konfiguracji aplikacji. Punkt końcowy można znaleźć w bloku **klucze dostępu** w Azure Portal. Ponownie uruchom wiersz polecenia, aby zmiany zaczęły obowiązywać. 


1. Otwórz okno *Bootstrap. Properties* w folderze *resources* . Zaktualizuj ten plik, aby użyć wartości **APP_CONFIGURATION_ENDPOINT** . Usuń wszystkie odwołania do parametrów połączenia w tym pliku. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Otwórz *MessageProperties. Java*. Dodaj nową zmienną o nazwie *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otwórz *HelloController. Java*. Zaktualizuj metodę *GetMessage* , aby uwzględnić komunikat pobrany z Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Utwórz nowy plik o nazwie *AzureCredentials. Java* i Dodaj poniższy kod.

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

1. Utwórz nowy plik o nazwie *AppConfiguration. Java*. I Dodaj poniższy kod.

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

1. Utwórz nowy plik w katalogu META-INF zasobów o nazwie *sprężyn. Factors* i Dodaj poniższy kod.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Skompiluj aplikację do rozruchu ze sprężyną przy użyciu Maven i uruchom ją, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Po uruchomieniu aplikacji należy użyć *zazwinięcie* do przetestowania aplikacji, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Zobaczysz komunikat wprowadzony w magazynie konfiguracji aplikacji. Zobaczysz również komunikat wprowadzony w Key Vault.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault. Aby dowiedzieć się, jak używać flag funkcji w aplikacji ze sprężyną Java, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./quickstart-feature-flag-spring-boot.md)