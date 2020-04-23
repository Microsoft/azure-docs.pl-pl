---
title: Tworzenie kontrolera transferu danych przychodzących z istniejącą bramą aplikacji
description: Ten artykuł zawiera informacje dotyczące sposobu wdrażania kontrolera transferu danych przychodzących bramy aplikacji z istniejącą bramą aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869895"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instalowanie kontrolera transferu danych (AGIC) przy użyciu istniejącej bramy aplikacji

Kontroler transferu danych przychodzących bramy aplikacji (AGIC) jest zasobnikiem w klastrze usługi Kubernetes.
Agic monitoruje zasoby usługi Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) i tworzy i stosuje config bramy aplikacji na podstawie stanu klastra Kubernetes.

## <a name="outline"></a>Konspektu:
- [Wymagania wstępne](#prerequisites)
- [Uwierzytelnianie usługi Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opcja 1: [Konfigurowanie tożsamości aad-pod-strąka](#set-up-aad-pod-identity) i tworzenie tożsamości platformy Azure w witrynach ARM
    - Opcja 2: [Korzystanie z jednostki usługi](#using-a-service-principal)
- [Instalowanie kontrolera transferu danych przychodzących przy użyciu funkcji Helm](#install-ingress-controller-as-a-helm-chart)
- [Brama aplikacji wieloawersowych / współdzielonych:](#multi-cluster--shared-application-gateway)Zainstaluj środowisko AGIC w środowisku, w którym brama aplikacji jest współużytkowana między jednym lub kilkoma klastrami AKS i/lub innymi składnikami platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie założono, że masz już zainstalowane następujące narzędzia i infrastrukturę:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) z włączoną [obsługą zaawansowanej sieci](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Brama aplikacji w wersji 2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) w tej samej sieci wirtualnej co AKS
- [Tożsamość zasobnika usługi AAD](https://github.com/Azure/aad-pod-identity) zainstalowana w klastrze AKS
- [Usługa Cloud Shell](https://shell.azure.com/) to środowisko `az` powłoki `kubectl`platformy `helm` Azure, które ma cli i zainstalowane. Narzędzia te są wymagane dla poniższych poleceń.

Przed zainstalowaniem usługi AGIC __należy wykonać kopię zapasową konfiguracji bramy aplikacji:__
  1. korzystanie z [witryny Azure Portal](https://portal.azure.com/) przejdź do twojego `Application Gateway` wystąpienia
  2. od `Export template` kliknięcia`Download`

Pobrany plik zip będzie miał szablony JSON, bash i skrypty programu PowerShell, których można użyć do przywrócenia bramy aplikacji, jeśli stanie się to konieczne

## <a name="install-helm"></a>Zainstaluj helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) jest menedżerem pakietów dla Kubernetes. Wykorzystamy go do `application-gateway-kubernetes-ingress` zainstalowania pakietu.
Użyj [Cloud Shell,](https://shell.azure.com/) aby zainstalować Helm:

1. Zainstaluj [helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) i uruchom `application-gateway-kubernetes-ingress` następujące czynności, aby dodać pakiet helm:

    - *RbAC włączony* Klaster AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC wyłączony* Klaster AKS

    ```bash
    helm init
    ```

1. Dodaj repozytorium AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Uwierzytelnianie usługi Azure Resource Manager

AGIC komunikuje się z serwerem interfejsu API kubernetes i usługi Azure Resource Manager. Wymaga tożsamości, aby uzyskać dostęp do tych interfejsów API.

## <a name="set-up-aad-pod-identity"></a>Konfigurowanie tożsamości pod aad

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) jest kontrolerem, podobnie jak AGIC, który działa również na AKS. Wiąże tożsamości usługi Azure Active Directory z zasobnikami usługi Kubernetes. Tożsamość jest wymagana dla aplikacji w zasobniku Kubernetes, aby móc komunikować się z innymi składnikami platformy Azure. W tym konkretnym przypadku potrzebujemy autoryzacji dla zasobnika AGIC, aby składać żądania HTTP do [ARM.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

Postępuj zgodnie z [instrukcjami instalacji tożsamości zasobnika usługi AAD,](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) aby dodać ten składnik do usługi AKS.

Następnie musimy utworzyć tożsamość platformy Azure i nadać jej uprawnienia ARM.
Użyj [usługi Cloud Shell,](https://shell.azure.com/) aby uruchomić wszystkie następujące polecenia i utworzyć tożsamość:

1. Utwórz tożsamość platformy Azure **w tej samej grupie zasobów co węzły AKS**. Wybranie poprawnej grupy zasobów jest ważne. Grupa zasobów wymagana w poniższym poleceniu *nie* jest grupą, do którą odwołuje się w okienku portalu AKS. Jest to grupa zasobów `aks-agentpool` maszyn wirtualnych. Zazwyczaj ta grupa zasobów `MC_` rozpoczyna się od nazwy systemu AKS i zawiera. Na przykład:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Dla poniżej poleceń przypisania `principalId` roli musimy uzyskać dla nowo utworzonej tożsamości:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Nadaj `Contributor` tożsamości dostęp do bramy aplikacji. Do tego potrzebny jest identyfikator bramy aplikacji, który będzie wyglądał mniej więcej tak:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Pobierz listę identyfikatorów bramy aplikacji w ramach subskrypcji za pomocą:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Nadaj `Reader` tożsamości dostęp do grupy zasobów bramy aplikacji. Identyfikator grupy zasobów będzie wyglądał `/subscriptions/A/resourceGroups/B`następująco: . Wszystkie grupy zasobów można uzyskać za pomocą:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Korzystanie z jednostki usługi
Możliwe jest również zapewnienie agic dostęp do ARM za pośrednictwem tajemnicy Kubernetes.

1. Utwórz jednostkę usługi Active Directory i zakoduj za pomocą base64. Kodowanie base64 jest wymagane, aby obiekt blob JSON został zapisany w usłudze Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Dodaj do pliku zakodowany obiekt blob `helm-config.yaml` JSON z kodem base64. Więcej informacji `helm-config.yaml` na ten temat znajduje się w następnej sekcji.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instalowanie kontrolera transferu danych przychodzących jako wykresu helm
W pierwszych kilku krokach instalujemy kultywator Helma w klastrze Kubernetes. Użyj [Cloud Shell,](https://shell.azure.com/) aby zainstalować pakiet AGIC Helm:

1. Dodaj `application-gateway-kubernetes-ingress` repozytorium steru i wykonaj aktualizację steru

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Pobierz helm-config.yaml, który skonfiguruje AGIC:
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
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edytuj helm-config.yaml i wypełnij `appgw` `armAuth`wartości dla i .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > I `<identity-resource-id>` `<identity-client-id>` są właściwości usługi Azure AD Identity, które zostały skonfigurowane w poprzedniej sekcji. Te informacje można pobrać, uruchamiając `az identity show -g <resourcegroup> -n <identity-name>`następujące `<resourcegroup>` polecenie: , gdzie znajduje się grupa zasobów, w której wdrożony jest obiekt klastra AKS najwyższego poziomu, brama aplikacji i identyfikator zarządzany.

1. Zainstaluj wykres `application-gateway-kubernetes-ingress` Helm `helm-config.yaml` z konfiguracją z poprzedniego kroku

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternatywnie można połączyć `helm-config.yaml` i helm polecenia w jednym kroku:
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

1. Sprawdź dziennik nowo utworzonego zasobnika, aby sprawdzić, czy został poprawnie uruchomiony

Zapoznaj się z [tym przewodnikiem, aby](ingress-controller-expose-service-over-http-https.md) dowiedzieć się, jak można udostępnić usługę AKS za pośrednictwem protokołu HTTP lub HTTPS w Internecie przy użyciu bramy aplikacji platformy Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Brama aplikacji wieloas klastrowej / udostępnionej
Domyślnie AGIC przejmuje pełną własność bramy aplikacji, z nią jest połączony. AGIC w wersji 0.8.0 i nowszych można udostępnić jedną bramę aplikacji z innymi składnikami platformy Azure. Na przykład możemy użyć tej samej bramy aplikacji dla aplikacji hostowane na zestaw skalowania maszyny wirtualnej, a także klastra AKS.

Przed włączeniem tego ustawienia __należy wykonać kopię zapasową konfiguracji bramy aplikacji:__
  1. korzystanie z [witryny Azure Portal](https://portal.azure.com/) przejdź do twojego `Application Gateway` wystąpienia
  2. od `Export template` kliknięcia`Download`

Pobrany plik zip będzie miał szablony JSON, skrypty bash i powershell, których można użyć do przywrócenia bramy aplikacji

### <a name="example-scenario"></a>Przykładowy scenariusz
Przyjrzyjmy się wyimaginowanej bramie aplikacji, która zarządza ruchem w dwóch witrynach sieci Web:
  - `dev.contoso.com`- hostowane na nowym AKS, przy użyciu bramy aplikacji i AGIC
  - `prod.contoso.com`- hostowane w [zestawie skalowania maszyny wirtualnej platformy Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Przy ustawieniach domyślnych AGIC przejmuje 100% własność bramy aplikacji, na które jest wskazywowy. AGIC zastępuje całą konfigurację bramy aplikacji. Jeśli mieliśmy ręcznie utworzyć odbiornik `prod.contoso.com` (na bramie aplikacji), bez definiowania go w uchoniaków `prod.contoso.com` dostępu Kubernetes, AGIC usunie config w ciągu kilku sekund.

Aby zainstalować AGIC, `prod.contoso.com` a także obsługiwać z naszych maszyn Virtual `dev.contoso.com` Machine Scale Set, musimy ograniczyć AGIC tylko do konfiguracji. Jest to ułatwione przez tworzenie wystąpienia następujących [CRD:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

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

Powyższe polecenie `AzureIngressProhibitedTarget` tworzy obiekt. Dzięki temu AGIC (wersja 0.8.0 lub nowsza) informuje `prod.contoso.com` o istnieniu konfiguracji bramy aplikacji i jawnie nakazuje jej unikać zmiany konfiguracji związanej z tą nazwa hosta.


### <a name="enable-with-new-agic-installation"></a>Włącz dzięki nowej instalacji AGIC
Aby ograniczyć AGIC (wersja 0.8.0 i nowsze) do `helm-config.yaml` podzbioru konfiguracji bramy aplikacji zmodyfikuj szablon.
W `appgw:` sekcji dodaj `shared` klawisz i ustaw `true`go na .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Zastosuj zmiany helm:
  1. Upewnij `AzureIngressProhibitedTarget` się, że crd jest zainstalowany z:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Aktualizacja Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

W rezultacie Twój AKS będzie miał `AzureIngressProhibitedTarget` `prohibit-all-targets`nowe wystąpienie o nazwie:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Obiekt `prohibit-all-targets`, jak sama nazwa wskazuje, zabrania AGIC zmiany konfiguracji dla *dowolnego* hosta i ścieżki.
Instalacja `appgw.shared=true` helm z wdroży AGIC, ale nie będzie wprowadzać żadnych zmian w bramie aplikacji.


### <a name="broaden-permissions"></a>Poszerz uprawnienia
Ponieważ Helm `appgw.shared=true` z `prohibit-all-targets` i domyślne blokuje AGIC stosowania dowolnego config.

Poszerz uprawnienia AGIC o:
1. Utwórz `AzureIngressProhibitedTarget` nowy z określoną konfiguracją:
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

2. Dopiero po utworzeniu własnego niestandardowego zakazu można usunąć domyślny, który jest zbyt szeroki:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Włącz istniejącą instalację AGIC
Załóżmy, że mamy już działający system AKS, bramę aplikacji i skonfigurowaną usługę AGIC w naszym klastrze. Mamy ingress dla `prod.contosor.com` i z powodzeniem obsługujących ruch dla niego z AKS. Chcemy dodać `staging.contoso.com` do naszej istniejącej bramy aplikacji, ale trzeba hostować go na maszynie [wirtualnej.](https://azure.microsoft.com/services/virtual-machines/) Zamierzamy ponownie użyć istniejącej bramy aplikacji i ręcznie skonfigurować pulę `staging.contoso.com`odbiornika i wewnętrznej bazy danych dla . Ale ręcznie szczypanie application gateway config (za pośrednictwem [portalu,](https://portal.azure.com) [INTERFEJSÓW API ARM](https://docs.microsoft.com/rest/api/resources/) lub [Terraform](https://www.terraform.io/)) byłoby sprzeczne z założeniami AGIC pełnej własności. Wkrótce po zastosowaniu zmian AGIC zastąpi je lub usunie.

Możemy zabronić AGIC wprowadzania zmian w podzbiór konfiguracji.

1. Tworzenie `AzureIngressProhibitedTarget` obiektu:
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

3. Modyfikuj config bramy aplikacji za pośrednictwem portalu - dodaj detektory, reguły routingu, zaplecze itp. Nowy obiekt, który`manually-configured-staging-environment`stworzyliśmy ( ) zabroni AGIC `staging.contoso.com`zastępowania konfiguracji bramy aplikacji związanej z .
