---
title: Konfigurowanie uwierzytelniania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować i skonfigurować uwierzytelnianie dla różnych zasobów i przepływów pracy w programie Azure Machine Learning. Istnieje wiele sposobów konfigurowania i używania uwierzytelniania w ramach usługi, od prostego uwierzytelniania opartego na interfejsie użytkownika na potrzeby tworzenia i testowania, do pełnego Azure Active Directory uwierzytelniania głównego usługi.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 57160088c283b1f2c686429168cc858fee58324a
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433109"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak skonfigurować i skonfigurować uwierzytelnianie dla różnych zasobów i przepływów pracy w programie Azure Machine Learning. Istnieje wiele sposobów na uwierzytelnianie w usłudze, od prostego uwierzytelniania opartego na interfejsie użytkownika na potrzeby tworzenia i testowania do pełnego Azure Active Directory uwierzytelniania głównego usługi. W tym artykule wyjaśniono także różnice w sposobie działania uwierzytelniania usługi sieci Web oraz sposób uwierzytelniania w interfejsie API REST Azure Machine Learning.

W tym przykładzie przedstawiono sposób wykonywania następujących zadań:

* Używanie interakcyjnego uwierzytelniania interfejsu użytkownika do testowania/programowania
* Konfigurowanie uwierzytelniania jednostki usługi
* Uwierzytelnianie w obszarze roboczym
* Pobieranie tokenów typu okaziciela OAuth 2.0 dla interfejsu API REST Azure Machine Learning
* Opis uwierzytelniania usługi sieci Web

Zapoznaj się z [artykułem koncepcji](concept-enterprise-security.md) , aby zapoznać się z ogólnym omówieniem zabezpieczeń i uwierzytelniania w ramach programu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).
* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [maszyny wirtualnej Azure Machine Learning notesu](concept-azure-machine-learning-architecture.md#compute-instance) z już zainstalowanym zestawem SDK.

## <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

Większość przykładów w dokumentacji tej usługi używa uwierzytelniania interaktywnego w notesach Jupyter jako prostą metodę testowania i demonstracji. Jest to lekki sposób na przetestowanie tego, co tworzysz. Istnieją dwa wywołania funkcji, które automatycznie monitują o przepływ uwierzytelniania oparty na interfejsie użytkownika.

Wywołanie `from_config()` funkcji spowoduje wydanie monitu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()`Funkcja szuka pliku JSON zawierającego informacje o połączeniu z obszarem roboczym. Możesz również jawnie określić szczegóły połączenia przy użyciu `Workspace` konstruktora, który również będzie monitować o uwierzytelnienie interaktywne. Oba wywołania są równoważne.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Jeśli masz dostęp do wielu dzierżawców, może zaistnieć konieczność zaimportowania klasy i jawne zdefiniowanie docelowej dzierżawy. Wywołanie konstruktora dla programu `InteractiveLoginAuthentication` spowoduje również wyświetlenie monitu o zalogowanie się podobne do powyższych wywołań.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Chociaż jest to przydatne do testowania i uczenia, uwierzytelnianie interaktywne nie pomoże Ci w tworzeniu zautomatyzowanych lub bezużytecznych przepływów pracy. Konfigurowanie uwierzytelniania jednostki usługi jest najlepszym rozwiązaniem dla zautomatyzowanych procesów korzystających z zestawu SDK.

## <a name="set-up-service-principal-authentication"></a>Konfigurowanie uwierzytelniania jednostki usługi

Ten proces jest niezbędny do włączenia uwierzytelniania, które jest oddzielone od określonej nazwy logowania użytkownika, co pozwala na uwierzytelnianie w programie Azure Machine Learning Python SDK w zautomatyzowanych przepływach pracy. Uwierzytelnianie jednostki usługi również umożliwi [uwierzytelnianie w interfejsie API REST](#azure-machine-learning-rest-api-auth).

Aby skonfigurować uwierzytelnianie jednostki usługi, należy najpierw utworzyć rejestrację aplikacji w Azure Active Directory, a następnie udzielić dostępu opartego na rolach aplikacji do obszaru roboczego ML. Najprostszym sposobem przeprowadzenia tej konfiguracji jest użycie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w Azure Portal. Po zalogowaniu się do portalu kliknij `>_` ikonę w prawym górnym rogu strony obok nazwy, aby otworzyć powłokę.

Jeśli nie korzystasz z usługi Cloud Shell przed kontem platformy Azure, musisz utworzyć zasób konta magazynu na potrzeby przechowywania dowolnych plików, które są zapisywane. Ogólnie rzecz biorąc, to konto magazynu będzie miało niewielki koszt miesięczny. Ponadto zainstaluj rozszerzenie Uczenie maszynowe, jeśli wcześniej nie było używane przy użyciu poniższego polecenia.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Aby wykonać poniższe kroki, musisz być administratorem w ramach subskrypcji.

Następnie uruchom następujące polecenie, aby utworzyć nazwę główną usługi. Nadaj mu nazwę, w tym przypadku z **uwierzytelnianiem ml**.

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

Następnie uruchom następujące polecenie, aby uzyskać szczegółowe informacje dotyczące jednostki usługi, która została właśnie utworzona, przy użyciu `clientId` wartości z powyżej jako dane wejściowe do `--id` parametru.

```azurecli-interactive
az ad sp show --id your-client-id
```

Poniżej przedstawiono uproszczony przykład danych wyjściowych JSON z polecenia. Zanotuj `objectId` pole, ponieważ będzie potrzebne jego wartość dla kolejnego kroku.

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

Następnie użyj poniższego polecenia, aby przypisać nazwę główną usługi do obszaru roboczego uczenia maszynowego. Wymagana jest nazwa obszaru roboczego i jego nazwa grupy zasobów `-w` `-g` odpowiednio dla parametrów i. Dla `--user` parametru Użyj `objectId` wartości z poprzedniego kroku. `--role`Parametr pozwala ustawić rolę dostępu dla jednostki usługi, a w ogólny sposób będzie używany **właściciel** lub **współautor**. Oba mają dostęp do zapisu do istniejących zasobów, takich jak Klastry obliczeniowe i magazyny danych, ale tylko **właściciel** może udostępniać te zasoby.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

To wywołanie nie produkuje żadnych danych wyjściowych, ale masz teraz skonfigurowane uwierzytelnianie jednostki usługi dla Twojego obszaru roboczego.

## <a name="authenticate-to-your-workspace"></a>Uwierzytelnianie w obszarze roboczym

Teraz, gdy jest włączona funkcja uwierzytelniania jednostki usługi, można uwierzytelnić się w obszarze roboczym w zestawie SDK bez fizycznego logowania jako użytkownik. Użyj `ServicePrincipalAuthentication` konstruktora klasy i użyj wartości uzyskanych z poprzednich kroków jako parametrów. `tenant_id`Parametr mapuje do `tenantId` z powyżej, `service_principal_id` mapuje do `clientId` i `service_principal_password` mapuje na `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Zmienna zawiera teraz obiekt uwierzytelniania, który jest używany bezpośrednio w zestawie SDK. Ogólnie rzecz biorąc, dobrym pomysłem jest przechowywanie identyfikatorów/wpisów tajnych użytych powyżej w zmiennych środowiskowych, jak pokazano w poniższym kodzie.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

W przypadku zautomatyzowanych przepływów pracy, które są uruchamiane w języku Python i używają zestawu SDK głównie, można użyć tego obiektu w większości przypadków w przypadku uwierzytelniania. Poniższy kod jest uwierzytelniany w obszarze roboczym przy użyciu obiektu auth, który został właśnie utworzony.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning uwierzytelnianie interfejsu API REST

Nazwa główna usługi utworzona w powyższych krokach może być również używana do uwierzytelniania w [interfejsie API REST](https://docs.microsoft.com/rest/api/azureml/)Azure Machine Learning. Używasz [przepływu przyznawania poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, który zezwala na wywołania usługi do usługi dla bezobsługowego uwierzytelniania w zautomatyzowanych przepływach pracy. Przykłady są implementowane za pomocą [biblioteki ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) w języku Python i Node. js, ale można również użyć dowolnej biblioteki Open Source, która obsługuje openid connect Connect 1,0.

> [!NOTE]
> MSAL. js jest nowszą biblioteką niż ADAL, ale nie można przeprowadzić uwierzytelniania między usługami przy użyciu poświadczeń klienta z MSAL. js, ponieważ jest to przede wszystkim Biblioteka po stronie klienta przeznaczona do uwierzytelniania interaktywnego/interfejsu użytkownika powiązanego z określonym użytkownikiem. Zalecamy użycie biblioteki ADAL, jak pokazano poniżej, aby utworzyć zautomatyzowane przepływy pracy za pomocą interfejsu API REST.

### <a name="nodejs"></a>Node.js

Wykonaj następujące kroki, aby wygenerować token uwierzytelniania przy użyciu środowiska Node. js. W środowisku programu uruchom polecenie `npm install adal-node` . Następnie użyj `tenantId` , `clientId` , i z jednostki `clientSecret` usługi utworzonej w powyższych krokach jako wartości pasujących zmiennych w poniższym skrypcie.

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

Zmienna `tokenResponse` jest obiektem, który zawiera token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i mogą być odświeżane przez ponowne uruchomienie tego samego wywołania w celu pobrania nowego tokenu. Poniżej przedstawiono przykładową odpowiedź.

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

### <a name="python"></a>Python

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

Zmienna `token_response` jest słownikiem zawierającym token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i mogą być odświeżane przez ponowne uruchomienie tego samego wywołania w celu pobrania nowego tokenu. Poniżej przedstawiono przykładową odpowiedź.

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

## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

Usługi sieci Web w Azure Machine Learning używają innego wzorca uwierzytelniania niż opisano powyżej. Najprostszym sposobem uwierzytelniania w ramach wdrożonych usług sieci Web jest użycie **uwierzytelniania opartego na kluczach**, które generuje klucze uwierzytelniania statycznego typu okaziciela, które nie muszą być odświeżane. Jeśli konieczne jest tylko uwierzytelnianie do wdrożonej usługi sieci Web, nie trzeba konfigurować uwierzytelniania przy użyciu zasad usługi, jak pokazano powyżej.

Usługi sieci Web wdrożone w usłudze Azure Kubernetes Service mają domyślnie *włączone* uwierzytelnianie oparte na kluczach. Azure Container Instances wdrożone usługi mają domyślnie uwierzytelnianie oparte na kluczach, ale można je *włączyć przy użyciu* ustawienia `auth_enabled=True` podczas tworzenia usługi sieci Web ACI. Poniżej przedstawiono przykład tworzenia konfiguracji wdrożenia ACI z włączoną funkcją uwierzytelniania opartego na kluczach.

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

Usługi sieci Web obsługują również uwierzytelnianie oparte na tokenach, ale tylko w przypadku wdrożeń usługi Azure Kubernetes. Dodatkowe informacje na temat uwierzytelniania można znaleźć w temacie [jak](how-to-consume-web-service.md) korzystać z usług internetowych.

### <a name="token-based-web-service-authentication"></a>Uwierzytelnianie usługi sieci Web oparte na tokenach

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownicy muszą przedstawić Azure Machine Learning token sieci Web JSON dla usługi sieci Web, aby uzyskać do niej dostęp. Token wygasa po określonym czasie i wymaga odświeżenia, aby kontynuować wywoływanie.

* Uwierzytelnianie tokenu jest **domyślnie wyłączone** w przypadku wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu **nie jest obsługiwane** w przypadku wdrażania programu w celu Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody, `get_token` Aby pobrać token sieci Web JSON (JWT) i czas wygaśnięcia tego tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po upływie czasu tokenu musisz zażądać nowego tokenu `refresh_by` . Aby odświeżyć tokeny poza zestawem SDK języka Python, jedną z opcji jest użycie interfejsu API REST z uwierzytelnianiem podmiotu zabezpieczeń usługi w celu okresowego wykonania `service.get_token()` wywołania, jak opisano wcześniej.
>
> Zdecydowanie zalecamy utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes.
>
> W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie dostępny ponownie.
>
> Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="next-steps"></a>Następne kroki

* [Uczenie i wdrażanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md).
* [Korzystaj z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).
