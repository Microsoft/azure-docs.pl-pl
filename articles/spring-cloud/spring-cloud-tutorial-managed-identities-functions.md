---
title: Samouczek — tożsamość zarządzana do wywoływania Azure Functions z aplikacji w chmurze platformy Azure
description: Używanie tożsamości zarządzanej do wywoływania usługi Azure Functions z aplikacji Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 861463b13c6ce8b29911432ad96f98fed527745b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501060"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Samouczek: używanie tożsamości zarządzanej do wywoływania Azure Functions z aplikacji w chmurze platformy Azure

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla aplikacji w chmurze platformy Azure i użyć jej do wywołania funkcji wyzwalanych przez protokół http.

Zarówno Azure Functions i App Services mają wbudowaną obsługę uwierzytelniania Azure Active Directory (Azure AD). Wykorzystując tę wbudowaną funkcję uwierzytelniania wraz z tożsamościami zarządzanymi dla chmury wiosennej platformy Azure, możemy wywoływać usługi RESTful przy użyciu nowoczesnej semantyki protokołu OAuth. Ta metoda nie wymaga przechowywania wpisów tajnych w kodzie i zapewnia bardziej szczegółowe kontrole kontroli dostępu do zasobów zewnętrznych. 


## <a name="prerequisites"></a>Wymagania wstępne

* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](/cli/azure/install-azure-cli)
* [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
* [Zainstaluj Azure Functions Core Tools w wersji 3.0.2009 lub nowszej](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, która będzie zawierać zarówno aplikację funkcji, jak i chmurę sprężynową przy użyciu polecenia [AZ Group Create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji
Aby utworzyć aplikację funkcji, należy najpierw utworzyć konto magazynu zapasowego. Użyj polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> Każda aplikacja funkcji i konto magazynu muszą mieć unikatową nazwę. Zastąp <functionapp-Name> nazwą swojej aplikacji funkcji, a następnie <> nazwę swojego konta magazynu, a następnie w poniższych przykładach skontaktuj się ze swoją nazwą.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Po utworzeniu konta magazynu możesz utworzyć aplikację funkcji.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Zanotuj zwrócone **nazwy hostów**, które będą miały format "https://<functionapp-Name>. azurewebsites.NET". Zostanie ona użyta w następnym kroku.


## <a name="enable-azure-active-directory-authentication"></a>Włącz uwierzytelnianie Azure Active Directory

Uzyskaj dostęp do nowo utworzonej aplikacji funkcji z [Azure Portal](https://portal.azure.com) i wybierz pozycję "uwierzytelnianie/autoryzacja" w menu Ustawienia. Włącz uwierzytelnianie App Service i ustaw opcję "Akcja do wykonania, gdy żądanie nie zostanie uwierzytelnione" do "Zaloguj się za pomocą Azure Active Directory". To ustawienie zapewni, że wszystkie nieuwierzytelnione żądania są odrzucane (401 odpowiedzi).

![Ustawienia uwierzytelniania pokazujące Azure Active Directory jako domyślny dostawca](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

W obszarze dostawcy uwierzytelniania wybierz pozycję Azure Active Directory, aby skonfigurować rejestrację aplikacji. Wybranie trybu zarządzania ekspresowego spowoduje automatyczne utworzenie rejestracji aplikacji w dzierżawie usługi Azure AD przy użyciu poprawnej konfiguracji.

![Dostawca Azure Active Directory ustawiony na tryb zarządzania ekspresowego](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Po zapisaniu ustawień aplikacja funkcji zostanie uruchomiona ponownie, a wszystkie kolejne żądania będą monitowani o zalogowanie się za pośrednictwem usługi Azure AD. Możesz sprawdzić, czy nieuwierzytelnione żądania są teraz odrzucane, przechodząc do głównego adresu URL aplikacji funkcji (zwrócone w **danych wyjściowych w** powyższym kroku). Należy nawiązać przekierowanie na ekran logowania organizacji usługi Azure AD.


## <a name="create-an-http-triggered-function"></a>Utwórz funkcję wyzwalaną przez protokół http

W pustym katalogu lokalnym Utwórz nową aplikację funkcji i Dodaj funkcję wyzwalaną przez protokół http.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Funkcje domyślne korzystają z uwierzytelniania opartego na kluczach w celu zabezpieczania punktów końcowych http. Ponieważ będziemy włączać uwierzytelnianie usługi Azure AD w celu zabezpieczenia dostępu do funkcji, chcemy [ustawić poziom uwierzytelniania funkcji anonimowy](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

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

Dane wyjściowe polecenia Publikuj powinny wyświetlić adres URL nowo utworzonej funkcji.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Tworzenie usługi i aplikacji w chmurze Azure wiosny
Po zainstalowaniu rozszerzenia usługi sprężyny w chmurze Utwórz wystąpienie chmury wiosennej na platformie Azure za pomocą polecenia platformy Azure `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

W poniższym przykładzie jest tworzona aplikacja o nazwie `msiapp` z zarządzaną przez system tożsamością żądaną przez `--assign-identity` parametr.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Tworzenie przykładowej aplikacji do rozruchu sprężynowego w celu wywołania funkcji

Ten przykład wywoła funkcję wyzwalaną przez protokół HTTP przez pierwsze żądanie tokenu dostępu z [punktu końcowego MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) i użycie tego tokenu do uwierzytelniania żądania HTTP funkcji.

1. Klonowanie przykładowego projektu. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Określ identyfikator URI funkcji i nazwę wyzwalacza we właściwościach aplikacji. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Aby korzystać z tożsamości zarządzanej dla aplikacji chmurowych platformy Azure, Dodaj właściwości z następującą zawartością do *src/Main/sources/Application. Properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Pakowanie przykładowej aplikacji. 

    ```console
    mvn clean package
    ```

4. Teraz należy wdrożyć aplikację na platformie Azure przy użyciu polecenia platformy Azure  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Uzyskaj dostęp do publicznego punktu końcowego lub testowego punktu końcowego, aby przetestować aplikację. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    W treści odpowiedzi zostanie wyświetlony następujący komunikat.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Możesz spróbować przekazać różne wartości do funkcji, zmieniając parametr path.

## <a name="next-steps"></a>Następne kroki

* [Jak włączyć tożsamość zarządzaną przypisaną przez system dla aplikacji w chmurze platformy Azure](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Skonfiguruj aplikacje klienckie, aby uzyskiwać dostęp do App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
