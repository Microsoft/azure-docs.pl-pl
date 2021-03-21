---
title: Utwórz kontroler transferu danych przychodzących z istniejącym Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu wdrażania Application Gateway kontroler danych przychodzących z istniejącym Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693729"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Zainstaluj Application Gateway kontroler transferu danych przychodzących (AGIC) przy użyciu istniejącej Application Gateway

Application Gateway kontroler transferu danych przychodzących (AGIC) znajduje się w klastrze Kubernetes.
AGIC monitoruje zasoby [transferu](https://kubernetes.io/docs/concepts/services-networking/ingress/) Kubernetes, a następnie tworzy i stosuje Application Gateway konfiguracji na podstawie stanu klastra Kubernetes.

## <a name="outline"></a>Krzyw
- [Wymagania wstępne](#prerequisites)
- [Uwierzytelnianie Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opcja 1: [Konfigurowanie usługi AAD-pod-Identity](#set-up-aad-pod-identity) i tworzenie tożsamości platformy Azure dla broni
    - Opcja 2: [Używanie nazwy głównej usługi](#using-a-service-principal)
- [Zainstaluj kontroler transferu danych przychodzących przy użyciu Helm](#install-ingress-controller-as-a-helm-chart)
- [Wiele klastrów/Application Gateway udostępnione](#multi-cluster--shared-application-gateway): Zainstaluj AGIC w środowisku, w którym Application Gateway jest współużytkowany przez jeden lub więcej klastrów AKS i/lub innych składników platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz już zainstalowane następujące narzędzia i infrastruktura:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) z włączoną funkcją [zaawansowanej sieci](../aks/configure-azure-cni.md)
- [Application Gateway v2](./tutorial-autoscale-ps.md) w tej samej sieci wirtualnej co AKS
- [Tożsamość usługi AAD pod](https://github.com/Azure/aad-pod-identity) zainstalowana w klastrze AKS
- [Cloud Shell](https://shell.azure.com/) jest środowiskiem usługi Azure Shell, który ma `az` interfejs wiersza polecenia, `kubectl` i został `helm` zainstalowany. Te narzędzia są wymagane dla poniższych poleceń.

Przed zainstalowaniem AGIC __Wykonaj kopię zapasową konfiguracji Application Gateway__ :
  1. Używanie [Azure Portal](https://portal.azure.com/) przejdź do `Application Gateway` wystąpienia
  2. od `Export template` kliknięcia `Download`

Pobrany plik zip będzie miał szablony JSON, bash i skrypty programu PowerShell, których można użyć do przywrócenia bramy App Gateway.

## <a name="install-helm"></a>Instalowanie narzędzia Helm
[Helm](../aks/kubernetes-helm.md) to Menedżer pakietów dla Kubernetes. Użyjemy go do zainstalowania `application-gateway-kubernetes-ingress` pakietu.
Użyj [Cloud Shell](https://shell.azure.com/) , aby zainstalować Helm:

1. Zainstaluj [Helm](../aks/kubernetes-helm.md) i uruchom następujące kroki, aby dodać `application-gateway-kubernetes-ingress` pakiet Helm:

    - *Włączono KUBERNETES RBAC* Klaster AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *KUBERNETES RBAC jest wyłączone* Klaster AKS

    ```bash
    helm init
    ```

1. Dodaj repozytorium AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Uwierzytelnianie Azure Resource Manager

AGIC komunikuje się z serwerem interfejsu API Kubernetes i Azure Resource Manager. Do uzyskiwania dostępu do tych interfejsów API wymagana jest tożsamość.

## <a name="set-up-aad-pod-identity"></a>Skonfiguruj tożsamość usługi AAD pod

[Tożsamość usługi AAD pod](https://github.com/Azure/aad-pod-identity) jest kontrolerem podobnym do AGIC, który również działa na AKS. Wiąże się ona z tożsamościami Azure Active Directory Kubernetes. Tożsamość jest wymagana dla aplikacji w Kubernetes pod, aby można było komunikować się z innymi składnikami platformy Azure. W konkretnym przypadku wymagana jest autoryzacja AGIC pod względem żądania HTTP do usługi [ARM](../azure-resource-manager/management/overview.md).

Postępuj zgodnie z [instrukcjami dotyczącymi instalacji tożsamości usługi AAD pod](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) , aby dodać ten składnik do AKS.

Następnie musimy utworzyć tożsamość platformy Azure i udzielić jej uprawnień ARM.
Użyj [Cloud Shell](https://shell.azure.com/) , aby uruchomić wszystkie następujące polecenia i utworzyć tożsamość:

1. Utwórz tożsamość platformy Azure **w tej samej grupie zasobów co węzły AKS**. Wybranie odpowiedniej grupy zasobów jest ważne. Grupa zasobów wymagana w poleceniu poniżej *nie* jest tą, do której odwołuje się okienko portalu AKS. Jest to grupa zasobów `aks-agentpool` maszyn wirtualnych. Zwykle Grupa zasobów rozpoczyna się od `MC_` i zawiera nazwę AKS. Na przykład: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. W przypadku poleceń przypisania roli poniżej będziemy musieli uzyskać dostęp do `principalId` nowo utworzonej tożsamości:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Nadaj tożsamości `Contributor` dostęp do Application Gateway. W takim przypadku wymagany jest identyfikator Application Gateway, który będzie wyglądać następująco: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Pobierz listę identyfikatorów Application Gateway w ramach subskrypcji za pomocą: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Nadaj tożsamości `Reader` dostęp do Application Gateway grupy zasobów. Identyfikator grupy zasobów będzie wyglądać następująco: `/subscriptions/A/resourceGroups/B` . Wszystkie grupy zasobów można uzyskać, korzystając z: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Korzystanie z nazwy głównej usługi
Istnieje również możliwość zapewnienia AGIC dostępu do ARM przy użyciu klucza tajnego Kubernetes.

1. Utwórz nazwę główną usługi Active Directory i Koduj przy użyciu algorytmu Base64. Kodowanie Base64 jest wymagane, aby obiekt BLOB JSON był zapisywany w Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Dodaj do pliku obiekt BLOB w formacie JSON zakodowany w formacie base64 `helm-config.yaml` . Więcej informacji na temat `helm-config.yaml` znajduje się w następnej sekcji.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Zainstaluj kontroler transferu danych przychodzących jako wykres Helm
W kilku pierwszych krokach instalujemy Helmer w klastrze Kubernetes. Użyj [Cloud Shell](https://shell.azure.com/) , aby zainstalować pakiet Helm AGIC:

1. Dodaj `application-gateway-kubernetes-ingress` repozytorium Helm i wykonaj aktualizację Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Pobierz Helm-config. YAML, który skonfiguruje AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Lub skopiuj plik YAML poniżej: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edytuj Helm-config. YAML i wypełnij wartości dla `appgw` i `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>`I `<identity-client-id>` są właściwościami tożsamości usługi Azure AD, którą konfigurujesz w poprzedniej sekcji. Te informacje można pobrać, uruchamiając następujące polecenie: `az identity show -g <resourcegroup> -n <identity-name>` , gdzie `<resourcegroup>` to grupa zasobów, w której wdrożono obiekt klastra AKS najwyższego poziomu, Application Gateway i zarządzanej identyfikacji.

1. Zainstaluj wykres Helm `application-gateway-kubernetes-ingress` z `helm-config.yaml` konfiguracją z poprzedniego kroku

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternatywnie można połączyć `helm-config.yaml` i Helm polecenie w jednym kroku:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Sprawdź dziennik nowo utworzonego pod kątem, aby sprawdzić, czy został uruchomiony prawidłowo

Zapoznaj się z [tym przewodnikiem](ingress-controller-expose-service-over-http-https.md) , aby dowiedzieć się, jak udostępnić usługę AKS za pośrednictwem protokołu HTTP lub HTTPS do Internetu przy użyciu Application Gateway platformy Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Wiele klastrów/Application Gateway udostępnione
Domyślnie AGIC przyjmuje pełną własność Application Gateway, z którą jest połączony. AGIC w wersji 0.8.0 i nowszych mogą współużytkować jeden Application Gateway z innymi składnikami platformy Azure. Na przykład możemy użyć tego samego Application Gateway dla aplikacji hostowanej w zestawie skalowania maszyn wirtualnych, jak również do klastra AKS.

Przed włączeniem tego ustawienia __Wykonaj kopię zapasową konfiguracji Application Gateway__ :
  1. Używanie [Azure Portal](https://portal.azure.com/) przejdź do `Application Gateway` wystąpienia
  2. od `Export template` kliknięcia `Download`

Pobrany plik zip będzie zawierał szablony JSON, bash i skrypty programu PowerShell, których można użyć do przywracania Application Gateway

### <a name="example-scenario"></a>Przykładowy scenariusz
Przyjrzyjmy się Application Gateway urojonym, który zarządza ruchem dla dwóch witryn sieci Web:
  - `dev.contoso.com` -hostowane na nowym AKS przy użyciu Application Gateway i AGIC
  - `prod.contoso.com` -hostowane w [zestawie skalowania maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Z ustawieniami domyślnymi AGIC zakłada, że ma 100% Application Gateway własności. AGIC zastępuje wszystkie konfiguracje bramy App Gateway. Jeśli udało Ci się ręcznie utworzyć odbiornik dla `prod.contoso.com` (na Application Gateway) bez definiowania go w Kubernetes, AGIC usunie `prod.contoso.com` konfigurację w ciągu kilku sekund.

Aby zainstalować program AGIC oraz korzystać `prod.contoso.com` z naszych maszyn wirtualnych zestawu skalowania maszyny wirtualnej, należy ograniczyć AGIC do konfiguracji `dev.contoso.com` . W tym celu należy utworzyć wystąpienie następujących [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Powyższe polecenie tworzy `AzureIngressProhibitedTarget` obiekt. To sprawia, że AGIC (wersja 0.8.0 i nowsze) wie o istnieniu konfiguracji Application Gateway `prod.contoso.com` i jawnie instruuje ją, aby uniknąć zmiany konfiguracji związanej z tą nazwą hosta.


### <a name="enable-with-new-agic-installation"></a>Włącz z nową instalacją AGIC
Aby ograniczyć AGIC (wersja 0.8.0 i nowsze) do podzbioru konfiguracji Application Gateway Modyfikuj `helm-config.yaml` szablon.
W `appgw:` sekcji Dodaj `shared` klucz i ustaw go na wartość `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Zastosuj Helm zmiany:
  1. Upewnij się, że `AzureIngressProhibitedTarget` CRD jest zainstalowana z:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Helm aktualizacji:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

W wyniku tego AKS będzie miało nowe wystąpienie o `AzureIngressProhibitedTarget` nazwie `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Obiekt `prohibit-all-targets` , jak nazwa wskazuje, zabrania AGIC zmiany konfiguracji dla *dowolnego* hosta i ścieżki.
Helm Install with `appgw.shared=true` spowoduje wdrożenie AGIC, ale nie wprowadzi żadnych zmian do Application Gateway.


### <a name="broaden-permissions"></a>Rozszerzanie uprawnień
Ponieważ Helm z `appgw.shared=true` i domyślne `prohibit-all-targets` bloki AGIC z zastosowania dowolnej konfiguracji.

Poszerzenie uprawnień AGIC z:
1. Utwórz nową `AzureIngressProhibitedTarget` z określoną konfiguracją:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Tylko po utworzeniu niestandardowego zakazu można usunąć domyślne, co jest zbyt szerokie:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Włącz dla istniejącej instalacji AGIC
Załóżmy, że mamy już działającą AKS, Application Gateway i skonfigurowany AGIC w naszym klastrze. Mamy przychodzący dla `prod.contoso.com` i pomyślnie obsługujący ruch dla niego z AKS. Chcemy dodać `staging.contoso.com` do istniejących Application Gateway, ale muszą one być hostowane na [maszynie wirtualnej](https://azure.microsoft.com/services/virtual-machines/). Będziemy ponownie używać istniejących Application Gateway i ręcznie skonfigurować odbiornik i pule zaplecza dla programu `staging.contoso.com` . Ale ręczne Dostosowywanie konfiguracji Application Gateway (za pośrednictwem [portalu](https://portal.azure.com), [interfejsów API ARM](/rest/api/resources/) lub [Terraform](https://www.terraform.io/)) koliduje z założeniami AGIC o pełnej własności. Wkrótce po zastosowaniu zmian AGIC zastąpią je lub usunie.

Możemy zabronić AGIC wprowadzania zmian w podzestawie konfiguracji.

1. Utwórz `AzureIngressProhibitedTarget` obiekt:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Wyświetl nowo utworzony obiekt:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modyfikowanie konfiguracji Application Gateway przy użyciu portalu — Dodawanie detektorów, reguł routingu, punktów końcowych itp. Nowy obiekt utworzony przez nas ( `manually-configured-staging-environment` ) zabroni AGIC z zastępowaniem Application Gateway konfiguracji związanej z programem `staging.contoso.com` .
