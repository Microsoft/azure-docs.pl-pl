---
title: 'Samouczek: tożsamość zarządzana do wywoływania Azure Functions'
description: Używanie tożsamości zarządzanej do wywoływania usługi Azure Functions z aplikacji Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792511"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Samouczek: używanie tożsamości zarządzanej do wywoływania Azure Functions z Azure Spring Cloud aplikacji

W tym artykule przedstawiono sposób tworzenia tożsamości zarządzanej dla aplikacji Azure Spring Cloud i używania jej do wywoływania funkcji wyzwalanych przez protokół HTTP.

Obie Azure Functions i App Services mają wbudowaną obsługę uwierzytelniania Azure Active Directory (Azure AD). Korzystając z tej wbudowanej możliwości uwierzytelniania wraz z tożsamościami zarządzanymi dla usługi Azure Spring Cloud, możemy wywoływać usługi RESTful przy użyciu nowoczesnej semantyki OAuth. Ta metoda nie wymaga przechowywania wpisów tajnych w kodzie i zapewnia bardziej szczegółową kontrolę nad dostępem do zasobów zewnętrznych. 


## <a name="prerequisites"></a>Wymagania wstępne

* [Rejestracja w celu subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub wyższej](/cli/azure/install-azure-cli)
* [Zainstaluj program Maven 3.0 lub jego więcej](https://maven.apache.org/download.cgi)
* [Zainstaluj wersję Azure Functions Core Tools 3.0.2009 lub nowszą](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, która będzie zawierać zarówno aplikację funkcji, jak i Spring Cloud za pomocą polecenia [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji
Aby utworzyć aplikację funkcji, musisz najpierw utworzyć zapasowe konto magazynu, użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create):

> [!Important]
> Każda aplikacja funkcji i konto magazynu muszą mieć unikatową nazwę. Zastąp <your-functionapp-name> nazwą swojej aplikacji funkcji, <your-storageaccount-name> nazwą swojego konta magazynu w poniższych przykładach.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Po utworzeniu konta magazynu możesz utworzyć aplikację funkcji.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Zanotuj zwrócone **nazwy hostName,** które będą w formacie "https://<nazwa_aplikacji_funkcji>.azurewebsites.net". Zostanie on użyty w następnym kroku.


## <a name="enable-azure-active-directory-authentication"></a>Włączanie Azure Active Directory uwierzytelniania

Uzyskaj dostęp do nowo utworzonej aplikacji funkcji z [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję "Uwierzytelnianie/autoryzacja" z menu ustawień. Włącz App Service uwierzytelniania i ustaw opcję "Akcja do podjęcia, gdy żądanie nie jest uwierzytelnione" na wartość "Zaloguj się przy użyciu Azure Active Directory". To ustawienie zapewni, że wszystkie nieuwierzynione żądania zostaną odrzucone (odpowiedź 401).

![Ustawienia uwierzytelniania Azure Active Directory dostawcy domyślnego](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

W obszarze Dostawcy uwierzytelniania wybierz Azure Active Directory, aby skonfigurować rejestrację aplikacji. Wybranie trybu zarządzania ekspresowego spowoduje automatyczne utworzenie rejestracji aplikacji w dzierżawie usługi Azure AD z poprawną konfiguracją.

![Azure Active Directory ustawiono tryb zarządzania ekspresowego](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Po zapisaniu ustawień aplikacja funkcji zostanie ponownie uruchomiona, a wszystkie kolejne żądania będą monitowane o zalogowanie się za pośrednictwem usługi Azure AD. Możesz sprawdzić, czy nieuwierzynione żądania są teraz odrzucane, przechodząc do głównego adresu URL aplikacji funkcji (zwróconego w danych wyjściowych **hostNames** w powyższym kroku). Powinno zostać przekierowane do ekranu logowania organizacji w usłudze Azure AD.


## <a name="create-an-http-triggered-function"></a>Tworzenie funkcji wyzwalanych przez protokół HTTP

W pustym katalogu lokalnym utwórz nową aplikację funkcji i dodaj funkcję wyzwalaną przez protokół Http.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Domyślnie funkcje używają uwierzytelniania opartego na kluczach do zabezpieczania punktów końcowych http. Ponieważ w celu zabezpieczenia dostępu do usługi Functions zostanie wł. uwierzytelnianie usługi Azure AD, chcemy ustawić poziom [uwierzytelniania funkcji na anonimowy.](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Aplikację można teraz opublikować w wystąpieniu [aplikacji funkcji](#create-a-function-app) utworzonym w poprzednim kroku.

```console
func azure functionapp publish <your-functionapp-name>
```

Dane wyjściowe polecenia publish powinny wyświetlić adres URL nowo utworzonej funkcji.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Tworzenie Azure Spring Cloud aplikacji i usługi
Po zainstalowaniu rozszerzenia spring-cloud utwórz wystąpienie Azure Spring Cloud za pomocą polecenia interfejsu wiersza polecenia platformy Azure `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Poniższy przykład tworzy aplikację o nazwie z tożsamością zarządzaną przypisaną przez `msiapp` system zgodnie z żądaniem `--assign-identity` parametru .

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Kompilowanie przykładowej Spring Boot do wywoływania funkcji

Ten przykład wywoła funkcję wyzwalaną przez protokół Http, najpierw żądając tokenu dostępu z punktu końcowego tożsamości usługi [msi](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) i używając tego tokenu do uwierzytelniania żądania HTTP funkcji.

1. Sklonuj przykładowy projekt. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Określ swój adres URI funkcji i nazwę wyzwalacza we właściwościach aplikacji. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Aby używać tożsamości zarządzanej dla Azure Spring Cloud, dodaj właściwości o następującej zawartości do *pliku src/main/resources/application.properties.*

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Spakuj przykładową aplikację. 

    ```console
    mvn clean package
    ```

4. Teraz wd wdrażaj aplikację na platformie Azure za pomocą polecenia interfejsu wiersza polecenia platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego, aby przetestować aplikację. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    W treści odpowiedzi zostanie zwrócony następujący komunikat.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Możesz spróbować przekazując różne wartości do funkcji, zmieniając parametr path.

## <a name="next-steps"></a>Następne kroki

* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla Azure Spring Cloud aplikacji](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Konfigurowanie aplikacji klienckich w celu uzyskiwania dostępu do App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
