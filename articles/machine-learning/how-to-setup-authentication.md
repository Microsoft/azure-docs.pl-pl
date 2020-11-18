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
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperfq2
ms.openlocfilehash: 7fa6beacf4456145e312494a72dad321dfef3754
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843931"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning


Dowiedz się, jak skonfigurować uwierzytelnianie w obszarze roboczym Azure Machine Learning. Uwierzytelnianie w obszarze roboczym Azure Machine Learning jest oparte na __Azure Active Directory__ (Azure AD) w większości rzeczy. Ogólnie rzecz biorąc, istnieją trzy przepływy pracy uwierzytelniania, których można używać podczas nawiązywania połączenia z obszarem roboczym:

* __Interaktywny__: konto jest używane w Azure Active Directory do bezpośredniego uwierzytelniania lub do uzyskania tokenu używanego do uwierzytelniania. Uwierzytelnianie interakcyjne jest używane podczas _eksperymentowania i iteracyjnego programowania_. Uwierzytelnianie interakcyjne umożliwia kontrolowanie dostępu do zasobów (takich jak usługa sieci Web) dla poszczególnych użytkowników.

* Nazwa __główna usługi__: Utwórz konto jednostki usługi w Azure Active Directory i użyj go do uwierzytelnienia lub pobrania tokenu. Nazwa główna usługi jest używana, gdy potrzebny jest _zautomatyzowany proces do uwierzytelniania_ w usłudze, bez konieczności interakcji z użytkownikiem. Na przykład ciągły skrypt integracji i wdrażania, który pociąga i testuje model przy każdym zmianie kodu szkoleniowego.

* __Tożsamość zarządzana__: w przypadku korzystania z zestawu SDK Azure Machine Learning _na maszynie wirtualnej platformy Azure_ można zarządzać tożsamością dla platformy Azure. Ten przepływ pracy umożliwia łączenie się z obszarem roboczym przez maszynę wirtualną przy użyciu tożsamości zarządzanej bez zapisywania poświadczeń w kodzie języka Python ani monitowania użytkownika o uwierzytelnienie. Azure Machine Learning klastrów obliczeniowych można także skonfigurować do korzystania z tożsamości zarządzanej w celu uzyskania dostępu do obszaru roboczego w przypadku _modeli szkoleniowych_.

> [!IMPORTANT]
> Niezależnie od używanego przepływu uwierzytelniania usługa kontroli dostępu opartej na rolach (Azure RBAC) jest używana do określania zakresu dostępu (autoryzacji) dozwolonych dla zasobów. Na przykład administrator lub proces automatyzacji może mieć dostęp do tworzenia wystąpienia obliczeniowego, ale nie z niego korzystać, podczas gdy analityk danych może go użyć, ale nie można go usunąć ani utworzyć. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).
* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) z już zainstalowanym zestawem SDK.

## <a name="azure-active-directory"></a>Azure Active Directory

Wszystkie przepływy pracy uwierzytelniania dla obszaru roboczego są zależne od Azure Active Directory. Jeśli chcesz, aby użytkownicy byli uwierzytelniani przy użyciu poszczególnych kont, muszą mieć konta w usłudze Azure AD. Jeśli chcesz używać jednostek usługi, muszą one znajdować się w usłudze Azure AD. Tożsamości zarządzane są również funkcją usługi Azure AD. 

Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [co to jest Azure Active Directory Authentication](..//active-directory/authentication/overview-authentication.md).

Po utworzeniu kont usługi Azure AD Zobacz temat [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md) , aby uzyskać informacje na temat udzielania im dostępu do obszaru roboczego i innych operacji w programie Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Konfigurowanie nazwy głównej usługi

Aby użyć nazwy głównej usługi (SP), należy najpierw utworzyć SP i udzielić im dostępu do obszaru roboczego. Jak wspomniano wcześniej, kontrola dostępu oparta na rolach na platformie Azure (RBAC) służy do kontroli dostępu, dlatego należy również zdecydować, jaki jest dostęp do udzielenia SP.

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

    Jeśli masz wiele subskrypcji platformy Azure, możesz użyć polecenia, `az account set -s <subscription name or ID>` Aby ustawić subskrypcję. Aby uzyskać więcej informacji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

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
    > Dostęp właściciela umożliwia jednostce usługi wykonywanie niemal każdej operacji w obszarze roboczym. Jest on używany w tym dokumencie, aby zademonstrować sposób udzielania dostępu; w środowisku produkcyjnym firma Microsoft zaleca przyznanie jednostce usługi minimalnego dostępu potrzebnego do przeprowadzenia roli, którą zamierzasz. Aby uzyskać informacje na temat tworzenia roli niestandardowej z dostępem potrzebnym dla danego scenariusza, zobacz [Zarządzanie dostępem do Azure Machine Learning obszaru roboczego](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    To wywołanie nie produkuje żadnych danych wyjściowych w przypadku powodzenia.

## <a name="configure-a-managed-identity"></a>Konfigurowanie tożsamości zarządzanej

> [!IMPORTANT]
> Tożsamość zarządzana jest obsługiwana tylko w przypadku korzystania z zestawu SDK Azure Machine Learning z maszyny wirtualnej platformy Azure lub z klastrem obliczeniowym Azure Machine Learning. Korzystanie z tożsamości zarządzanej z klastrem obliczeniowym jest obecnie w wersji zapoznawczej.

### <a name="managed-identity-with-a-vm"></a>Zarządzana tożsamość z maszyną wirtualną

1. Włącz [zarządzaną przez system tożsamość skojarzoną z zasobami platformy Azure na maszynie wirtualnej](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. W [Azure Portal](https://portal.azure.com)wybierz obszar roboczy, a następnie wybierz pozycję __Access Control (IAM)__, __Dodaj przypisanie roli__ i wybierz pozycję __maszyna wirtualna__ na liście rozwijanej __Przypisz dostęp do__ . Na koniec wybierz tożsamość maszyny wirtualnej.

1. Wybierz rolę, która ma zostać przypisana do tej tożsamości. Na przykład współautor lub rola niestandardowa. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do zasobów](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Zarządzana tożsamość z klastrem obliczeniowym

Aby uzyskać więcej informacji, zobacz [Konfigurowanie tożsamości zarządzanej klastra obliczeniowego](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Użyj uwierzytelniania interaktywnego

> [!IMPORTANT]
> Uwierzytelnianie interakcyjne używa przeglądarki i wymaga plików cookie (w tym plików cookie innych firm). Jeśli pliki cookie zostały wyłączone, może wystąpić błąd, na przykład "nie możemy cię zalogować". Ten błąd może również wystąpić, jeśli włączono [usługę Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md).

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

W przypadku korzystania z interfejsu wiersza polecenia platformy Azure `az login` polecenie jest używane do uwierzytelniania sesji CLI. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Jeśli używasz zestawu SDK ze środowiska, w którym wcześniej uwierzytelniono się interaktywnie przy użyciu interfejsu wiersza polecenia platformy Azure, możesz użyć `AzureCliAuthentication` klasy do uwierzytelnienia w obszarze roboczym przy użyciu poświadczeń w pamięci podręcznej interfejsu wiersza polecenia:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania nazwy głównej usługi

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

## <a name="use-managed-identity-authentication"></a>Korzystanie z uwierzytelniania tożsamości zarządzanej

Aby uwierzytelnić się w obszarze roboczym z maszyny wirtualnej lub klastra obliczeniowego, który został skonfigurowany przy użyciu tożsamości zarządzanej, użyj `MsiAuthentication` klasy. Poniższy przykład ilustruje sposób użycia tej klasy do uwierzytelniania w obszarze roboczym:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Następne kroki

* [Jak używać wpisów tajnych w szkoleniu](how-to-use-secrets-in-runs.md).
* [Jak skonfigurować uwierzytelnianie dla modeli wdrożonych jako usługa sieci Web](how-to-authenticate-web-service.md).
* [Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa internetowa](how-to-consume-web-service.md).