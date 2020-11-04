---
title: Konfigurowanie uwierzytelniania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować i skonfigurować uwierzytelnianie dla różnych zasobów i przepływów pracy w programie Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli
ms.openlocfilehash: fd6f933e1b3c1e7c003f62e03215273e3d28ea5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318532"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy


Dowiedz się, jak uwierzytelniać się w obszarze roboczym Azure Machine Learning i do modeli wdrożonych jako usługi sieci Web.

Ogólnie rzecz biorąc, istnieją dwa typy uwierzytelniania, których można używać z Azure Machine Learning:

* __Interaktywny__ : konto jest używane w Azure Active Directory do bezpośredniego uwierzytelniania lub do uzyskania tokenu używanego do uwierzytelniania. Uwierzytelnianie interakcyjne jest używane podczas eksperymentowania i iteracyjnego programowania. Lub, gdzie chcesz kontrolować dostęp do zasobów (takich jak usługa sieci Web) dla poszczególnych użytkowników.
* Nazwa __główna usługi__ : Utwórz konto jednostki usługi w Azure Active Directory i użyj go do uwierzytelnienia lub pobrania tokenu. Nazwa główna usługi jest używana, gdy potrzebny jest zautomatyzowany proces do uwierzytelniania w usłudze, bez konieczności interakcji z użytkownikiem. Na przykład ciągły skrypt integracji i wdrażania, który pociąga i testuje model przy każdym zmianie kodu szkoleniowego. Możesz również użyć jednostki usługi, aby pobrać token do uwierzytelniania w usłudze sieci Web, jeśli nie chcesz wymagać uwierzytelniania użytkownika końcowego usługi. Lub w przypadku, gdy uwierzytelnianie użytkownika końcowego nie jest wykonywane bezpośrednio przy użyciu Azure Active Directory.

Niezależnie od używanego typu uwierzytelniania kontrola dostępu oparta na rolach na platformie Azure (RBAC) służy do określania zakresu dostępu do zasobów. Na przykład konto, które jest używane w celu uzyskania tokenu dostępu dla wdrożonego modelu, wymaga tylko dostępu do odczytu w obszarze roboczym. Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).
* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [maszyny wirtualnej Azure Machine Learning notesu](concept-azure-machine-learning-architecture.md#compute-instance) z już zainstalowanym zestawem SDK.

## <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

> [!IMPORTANT]
> Uwierzytelnianie interakcyjne używa przeglądarki i wymaga plików cookie (w tym plików cookie innych firm). Jeśli pliki cookie zostały wyłączone, może wystąpić błąd, na przykład "nie możemy cię zalogować". Ten błąd może również wystąpić, jeśli włączono [usługę Azure MFA Authentication](../active-directory/authentication/concept-mfa-howitworks.md).

Większość przykładów w dokumentacji i przykładach używa uwierzytelniania interakcyjnego. Na przykład podczas korzystania z zestawu SDK istnieją dwa wywołania funkcji, które automatycznie monitują o przepływ uwierzytelniania oparty na interfejsie użytkownika:

* Wywołanie `from_config()` funkcji spowoduje wydanie monitu.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Funkcja `from_config()` szuka pliku JSON zawierającego informacje o połączeniu z obszarem roboczym.

* Użycie `Workspace` konstruktora w celu udostępnienia informacji o subskrypcji, grupie zasobów i obszarze roboczym spowoduje również wyświetlenie monitu o uwierzytelnienie interaktywne.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Jeśli masz dostęp do wielu dzierżawców, może zaistnieć konieczność zaimportowania klasy i jawne zdefiniowanie docelowej dzierżawy. Wywołanie konstruktora dla programu `InteractiveLoginAuthentication` spowoduje również wyświetlenie monitu o zalogowanie się podobne do powyższych wywołań.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby można było korzystać z uwierzytelniania przy użyciu nazwy głównej usługi (SP), należy najpierw utworzyć SP i udzielić im dostępu do obszaru roboczego. Jak wspomniano wcześniej, kontrola dostępu oparta na rolach na platformie Azure (RBAC) służy do kontroli dostępu, dlatego należy również zdecydować, jaki jest dostęp do udzielenia SP.

> [!IMPORTANT]
> W przypadku korzystania z jednostki usługi należy przyznać jej __minimalny dostęp wymagany dla zadania__ , które jest używane przez program. Na przykład nie można przyznać właścicielowi jednostki usługi ani dostępu współautora, jeśli jest on używany do odczytu tokenu dostępu dla wdrożenia w sieci Web.
>
> Przyczyną uzyskania najmniejszego dostępu jest fakt, że nazwa główna usługi używa hasła do uwierzytelniania, a hasło może być przechowywane jako część skryptu automatyzacji. W przypadku przecieku hasła, gdy minimalny dostęp wymagany do określonych zadań minimalizuje złośliwe użycie programu SP.

Najprostszym sposobem utworzenia SP i udzielenia dostępu do obszaru roboczego jest użycie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest). Aby utworzyć jednostkę usługi i udzielić jej dostępu do obszaru roboczego, wykonaj następujące czynności:

> [!NOTE]
> Aby wykonać wszystkie te kroki, musisz być administratorem w ramach subskrypcji.

1. Uwierzytelnianie w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacji.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Aby poznać inne metody uwierzytelniania, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

1. Zainstaluj rozszerzenie Azure Machine Learning:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Utwórz nazwę główną usługi. W poniższym przykładzie jest tworzone uwierzytelnianie SP o nazwie **ml** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Dane wyjściowe będą wyglądać podobnie jak w formacie JSON podobnym do poniższego. Zanotuj `clientId` `clientSecret` pola, i `tenantId` , ponieważ będą one potrzebne do wykonania innych czynności opisanych w tym artykule.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Pobierz szczegóły dla jednostki usługi przy użyciu `clientId` wartości zwracanej w poprzednim kroku:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Poniższy kod JSON to uproszczony przykład danych wyjściowych polecenia. Zanotuj `objectId` pole, ponieważ będzie potrzebne jego wartość dla kolejnego kroku.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Zezwól usłudze SP na dostęp do obszaru roboczego Azure Machine Learning. Wymagana jest nazwa obszaru roboczego i jego nazwa grupy zasobów `-w` `-g` odpowiednio dla parametrów i. Dla `--user` parametru Użyj `objectId` wartości z poprzedniego kroku. `--role`Parametr pozwala ustawić rolę dostępu dla jednostki usługi. W poniższym przykładzie SP jest przypisany do roli **właściciela** . 

    > [!IMPORTANT]
    > Dostęp właściciela umożliwia jednostce usługi wykonywanie niemal każdej operacji w obszarze roboczym. Jest on używany w tym dokumencie, aby zademonstrować sposób udzielania dostępu; w środowisku produkcyjnym firma Microsoft zaleca przyznanie jednostce usługi minimalnego dostępu potrzebnego do przeprowadzenia roli, którą zamierzasz. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    To wywołanie nie produkuje żadnych danych wyjściowych w przypadku powodzenia.

### <a name="use-a-service-principal-from-the-sdk"></a>Korzystanie z jednostki usługi z zestawu SDK

Aby uwierzytelnić się w obszarze roboczym z zestawu SDK przy użyciu nazwy głównej usługi, użyj `ServicePrincipalAuthentication` konstruktora klasy. Użyj wartości uzyskanych podczas tworzenia dostawcy usług jako parametrów. `tenant_id`Parametr mapuje do `tenantId` z powyżej, `service_principal_id` mapuje do `clientId` i `service_principal_password` mapuje na `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Zmienna zawiera teraz obiekt uwierzytelniania, który jest używany bezpośrednio w zestawie SDK. Ogólnie rzecz biorąc, dobrym pomysłem jest przechowywanie identyfikatorów/wpisów tajnych użytych powyżej w zmiennych środowiskowych, jak pokazano w poniższym kodzie. Przechowywanie w zmiennych środowiskowych zapobiega przypadkowemu sprawdzeniu informacji w repozytorium GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

W przypadku zautomatyzowanych przepływów pracy, które są uruchamiane w języku Python i używają zestawu SDK głównie, można użyć tego obiektu w większości przypadków w przypadku uwierzytelniania. Następujący kod jest uwierzytelniany w obszarze roboczym przy użyciu utworzonego obiektu auth.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Używanie jednostki usługi w interfejsie wiersza polecenia platformy Azure

Można użyć jednostki usługi dla poleceń interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Korzystanie z jednostki usługi przy użyciu interfejsu API REST (wersja zapoznawcza)

Nazwa główna usługi może być również używana do uwierzytelniania w [interfejsie API REST](/rest/api/azureml/) Azure Machine Learning (wersja zapoznawcza). Używasz [przepływu przyznawania poświadczeń klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)Azure Active Directory, który zezwala na wywołania usługi do usługi dla bezobsługowego uwierzytelniania w zautomatyzowanych przepływach pracy. Przykłady są implementowane za pomocą [biblioteki ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) w języku Python i Node.js, ale można również użyć dowolnej biblioteki Open Source, która obsługuje openid connect Connect 1,0.

> [!NOTE]
> MSAL.js jest nowszą biblioteką niż ADAL, ale nie można przeprowadzić uwierzytelniania między usługami przy użyciu poświadczeń klienta z MSAL.js, ponieważ jest to przede wszystkim Biblioteka po stronie klienta przeznaczona do uwierzytelniania interaktywnego/interfejsu użytkownika powiązanego z określonym użytkownikiem. Zalecamy użycie biblioteki ADAL, jak pokazano poniżej, aby utworzyć zautomatyzowane przepływy pracy za pomocą interfejsu API REST.

#### <a name="nodejs"></a>Node.js

Wykonaj następujące kroki, aby wygenerować token uwierzytelniania przy użyciu Node.js. W środowisku programu uruchom polecenie `npm install adal-node` . Następnie użyj `tenantId` , `clientId` , i z jednostki `clientSecret` usługi utworzonej w powyższych krokach jako wartości pasujących zmiennych w poniższym skrypcie.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Zmienna `tokenResponse` jest obiektem, który zawiera token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i mogą być odświeżane przez ponowne uruchomienie tego samego wywołania w celu pobrania nowego tokenu. Poniższy fragment kodu jest odpowiedzią przykładową.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Użyj `accessToken` właściwości, aby pobrać token uwierzytelniania. Zapoznaj się z [dokumentacją interfejsu API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , aby zapoznać się z przykładami dotyczącymi używania tokenu w celu wykonywania wywołań interfejsu API.

#### <a name="python"></a>Python

Wykonaj następujące kroki, aby wygenerować token uwierzytelniania przy użyciu języka Python. W środowisku programu uruchom polecenie `pip install adal` . Następnie użyj `tenantId` , `clientId` , i z jednostki `clientSecret` usługi utworzonej w powyższych krokach jako wartości dla odpowiednich zmiennych w poniższym skrypcie.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Zmienna `token_response` jest słownikiem zawierającym token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i mogą być odświeżane przez ponowne uruchomienie tego samego wywołania w celu pobrania nowego tokenu. Poniższy fragment kodu jest odpowiedzią przykładową.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Służy `token_response["accessToken"]` do pobierania tokenu uwierzytelniania. Zapoznaj się z [dokumentacją interfejsu API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , aby zapoznać się z przykładami dotyczącymi używania tokenu w celu wykonywania wywołań interfejsu API.

#### <a name="java"></a>Java

W języku Java Pobierz token okaziciela przy użyciu standardowego wywołania REST:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Poprzedni kod powinien obsługiwać wyjątki i kody stanu inne niż `200 OK` , ale pokazuje wzorzec: 

- Użyj identyfikatora klienta i klucza tajnego, aby sprawdzić, czy program powinien mieć dostęp
- Użyj identyfikatora dzierżawy, aby określić, gdzie `login.microsoftonline.com` należy szukać
- Użyj Azure Resource Manager jako źródła tokenu autoryzacji

## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

Wdrożenia modelu utworzone przez Azure Machine Learning mają dwie metody uwierzytelniania:

* **oparte na kluczach** : klucz statyczny jest używany do uwierzytelniania w usłudze sieci Web.
* **oparta na tokenach** : tymczasowy token musi zostać uzyskany z obszaru roboczego i użyty do uwierzytelnienia w usłudze sieci Web. Ten token wygasa po upływie okresu czasu i musi zostać odświeżony, aby kontynuować pracę z usługą sieci Web.

    > [!NOTE]
    > Uwierzytelnianie oparte na tokenach jest dostępne tylko w przypadku wdrażania w usłudze Azure Kubernetes Service.

### <a name="key-based-web-service-authentication"></a>Uwierzytelnianie usługi sieci Web oparte na kluczach

Usługi sieci Web wdrożone w usłudze Azure Kubernetes Service (AKS) mają domyślnie *włączone* uwierzytelnianie oparte na kluczach. W przypadku wdrożonych usług Azure Container Instances (ACI) uwierzytelnianie oparte na kluczach jest domyślnie *wyłączone* , ale można je włączyć przy użyciu ustawienia `auth_enabled=True` podczas tworzenia usługi sieci Web ACI. Poniższy kod stanowi przykład tworzenia konfiguracji wdrożenia ACI z włączoną funkcją uwierzytelniania opartego na kluczach.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Następnie można użyć konfiguracji niestandardowej ACI we wdrożeniu przy użyciu `Model` klasy.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Aby pobrać klucze uwierzytelniania, użyj `aci_service.get_keys()` . Aby ponownie wygenerować klucz, użyj `regen_key()` funkcji i przekaż wartość **podstawową** lub **pomocniczą**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Aby uzyskać więcej informacji na temat uwierzytelniania do wdrożonego modelu, zobacz [Tworzenie klienta dla modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Uwierzytelnianie usługi sieci Web oparte na tokenach

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownicy muszą przedstawić Azure Machine Learning token sieci Web JSON dla usługi sieci Web, aby uzyskać do niej dostęp. Token wygasa po określonym czasie i musi zostać odświeżony w celu dalszego wykonywania wywołań.

* Uwierzytelnianie tokenu jest **domyślnie wyłączone** w przypadku wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu **nie jest obsługiwane** w przypadku wdrażania programu w celu Azure Container Instances.
* Uwierzytelnianie tokenu **nie może być używane w tym samym czasie, co uwierzytelnianie oparte na kluczach**.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody, `get_token` Aby pobrać token sieci Web JSON (JWT) i czas wygaśnięcia tego tokenu:

> [!TIP]
> Jeśli używasz nazwy głównej usługi do pobrania tokenu i chcesz, aby miał minimalny wymagany dostęp do pobierania tokenu, przypisz go do roli **czytelnik** dla obszaru roboczego.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Będzie konieczne zażądanie nowego tokenu po upłynięciu czasu odświeżania `refresh_by` tokenu. Aby odświeżyć tokeny poza zestawem SDK języka Python, jedną z opcji jest użycie interfejsu API REST z uwierzytelnianiem podmiotu zabezpieczeń usługi w celu okresowego wykonania `service.get_token()` wywołania, jak opisano wcześniej.
>
> Zdecydowanie zalecamy utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes.
>
> W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie dostępny ponownie.
>
> Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="next-steps"></a>Następne kroki

* [Jak używać wpisów tajnych w szkoleniu](how-to-use-secrets-in-runs.md).
* [Uczenie i wdrażanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md).
* [Korzystaj z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).