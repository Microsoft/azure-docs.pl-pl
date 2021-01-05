---
title: Tworzenie kontrolera transferu danych przychodzących przy użyciu nowego Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu wdrażania Application Gateway kontroler danych przychodzących z nowym Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 9f69f89f565b2d98e408b06e300ff781c13680ef
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693667"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Jak zainstalować Application Gateway kontroler transferu danych przychodzących (AGIC) przy użyciu nowego Application Gateway

W poniższych instrukcjach przyjęto założenie, Application Gateway kontroler transferu danych (AGIC) zostanie zainstalowany w środowisku bez istniejących składników.

## <a name="required-command-line-tools"></a>Wymagane narzędzia wiersza polecenia

Zalecamy używanie [Azure Cloud Shell](https://shell.azure.com/) dla wszystkich operacji wiersza polecenia poniżej. Uruchom powłokę z shell.azure.com lub klikając łącze:

[![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

Alternatywnie można uruchomić Cloud Shell z Azure Portal przy użyciu następującej ikony:

![Uruchamianie portalu](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure Cloud Shell](https://shell.azure.com/) masz już wszystkie niezbędne narzędzia. Jeśli zdecydujesz się użyć innego środowiska, upewnij się, że są zainstalowane następujące narzędzia wiersza polecenia:

* `az` — Interfejs wiersza polecenia platformy Azure: [instrukcje dotyczące instalacji](/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` -Kubernetes narzędzia wiersza polecenia: [instrukcje dotyczące instalacji](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` -Kubernetes Package Manager: [instrukcje dotyczące instalacji](https://github.com/helm/helm/releases/latest)
* `jq` -line wiersza polecenia JSON: [instrukcje dotyczące instalacji](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Tworzenie tożsamości

Wykonaj poniższe kroki, aby utworzyć [obiekt główny usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)Azure Active Directory (AAD). Zapisz `appId` `password` wartości, i `objectId` — zostaną one użyte w poniższych krokach.

1. Utwórz nazwę główną usługi AD ([Dowiedz się więcej o usłudze Azure RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    `appId`Wartości i `password` z danych wyjściowych JSON będą używane w następujących krokach


1. Użyj `appId` z danych wyjściowych poprzedniego polecenia, aby uzyskać `objectId` nową nazwę główną usługi:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Dane wyjściowe tego polecenia to `objectId` , które będą używane w szablonie Azure Resource Manager poniżej

1. Utwórz plik parametrów, który zostanie użyty w późniejszym wdrożeniu szablonu Azure Resource Manager.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Aby wdrożyć klaster z obsługą kontroli **RBAC Kubernetes** , należy ustawić `aksEnableRBAC` pole na `true`

## <a name="deploy-components"></a>Wdróż składniki
Ten krok spowoduje dodanie do subskrypcji następujących składników:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtual Network](../virtual-network/virtual-networks-overview.md) z 2 [podsieciami](../virtual-network/virtual-networks-overview.md)
- [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
- [Tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md), która będzie używana przez [tożsamość usługi AAD pod](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Pobierz szablon Azure Resource Manager i zmodyfikuj szablon zgodnie z wymaganiami.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Wdróż szablon Azure Resource Manager za pomocą polecenia `az cli` . Może to potrwać do 5 minut.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Po zakończeniu wdrożenia Pobierz dane wyjściowe wdrożenia do pliku o nazwie `deployment-outputs.json` .
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Skonfiguruj Application Gateway kontroler transferu danych przychodzących

Zgodnie z instrukcjami podanymi w poprzedniej sekcji utworzyliśmy i skonfigurowano nowy klaster AKS oraz Application Gateway. Teraz jesteśmy gotowi do wdrożenia przykładowej aplikacji i kontrolera transferu danych przychodzących do naszej nowej infrastruktury Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Skonfiguruj poświadczenia Kubernetes
W przypadku następujących kroków potrzebujemy Instalatora [polecenia kubectl](https://kubectl.docs.kubernetes.io/) polecenie, które zostanie użyte do nawiązania połączenia z naszym nowym klastrem Kubernetes. [Cloud Shell](https://shell.azure.com/) jest `kubectl` już zainstalowany. Użyjemy `az` interfejsu wiersza polecenia, aby uzyskać poświadczenia dla Kubernetes.

Uzyskaj poświadczenia dla nowo wdrożonego AKS ([Dowiedz się więcej](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalowanie usługi AAD pod tożsamością
  Azure Active Directory pod tożsamością zapewnia dostęp oparty na tokenach do [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

  [Tożsamość usługi AAD pod](https://github.com/Azure/aad-pod-identity) zostanie dodana do klastra Kubernetes następujące składniki:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity` , `AzureAssignedIdentity` , `AzureIdentityBinding`
   * Składnik [zarządzanego kontrolera tożsamości (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Składnik [tożsamości zarządzanej przez węzeł (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Aby zainstalować tożsamość usługi AAD pod względem klastra:

   - *Włączono KUBERNETES RBAC* Klaster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC jest wyłączone* Klaster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalowanie narzędzia Helm
[Helm](../aks/kubernetes-helm.md) to Menedżer pakietów dla Kubernetes. Użyjemy go do zainstalowania `application-gateway-kubernetes-ingress` pakietu:

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

### <a name="install-ingress-controller-helm-chart"></a>Instalowanie wykresu Helm kontrolera danych wejściowych

1. Użyj `deployment-outputs.json` utworzonego powyżej pliku i Utwórz następujące zmienne.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edytuj nowo pobrane Helm-config. YAML i wypełnij sekcje `appgw` i `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Wartości:
     - `verbosityLevel`: Określa poziom szczegółowości infrastruktury rejestrowania AGIC. Zobacz [poziomy rejestrowania](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) dla możliwych wartości.
     - `appgw.subscriptionId`: Identyfikator subskrypcji platformy Azure, w którym znajduje się Application Gateway. Przykład: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nazwa grupy zasobów platformy Azure, w której utworzono Application Gateway. Przykład: `app-gw-resource-group`
     - `appgw.name`: Nazwa Application Gateway. Przykład: `applicationgatewayd0f0`
     - `appgw.shared`: Ta flaga logiczna powinna być domyślną wartością `false` . Ustaw, aby `true` była potrzebna [Application Gateway udostępnione](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Określ przestrzeń nazw, która AGIC powinna być obserwowana. Może to być jedna wartość ciągu lub rozdzielona przecinkami lista przestrzeni nazw.
    - `armAuth.type`: może być `aadPodIdentity` lub `servicePrincipal`
    - `armAuth.identityResourceID`: Identyfikator zasobu tożsamości zarządzanej platformy Azure
    - `armAuth.identityClientId`: Identyfikator klienta tożsamości. Aby uzyskać więcej informacji na temat tożsamości, zobacz poniżej.
    - `armAuth.secretJSON`: Jest to konieczne tylko wtedy, gdy wybrano typ wpisu tajnego jednostki usługi (gdy ustawiono `armAuth.type` wartość `servicePrincipal` ) 


   > [!NOTE]
   > `identityResourceID`I `identityClientID` są wartościami, które zostały utworzone podczas kroków [wdrażania składników](ingress-controller-install-new.md#deploy-components) i mogą zostać uzyskane ponownie przy użyciu następującego polecenia:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` w powyższym poleceniu jest Grupa zasobów Application Gateway. `<identity-name>` jest nazwą utworzonej tożsamości. Wszystkie tożsamości dla danej subskrypcji można wyświetlić przy użyciu: `az identity list`


1. Zainstaluj pakiet Application Gateway transferu danych przychodzących:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalowanie przykładowej aplikacji
Teraz, gdy mamy Application Gateway, AKS i AGIC, możemy zainstalować przykładową aplikację za pośrednictwem [Azure Cloud Shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternatywnie możesz:

* Pobierz plik YAML powyżej:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Zastosuj plik YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Inne przykłady
Ten [Przewodnik](ingress-controller-expose-service-over-http-https.md) zawiera więcej przykładów dotyczących udostępniania usługi AKS za pośrednictwem protokołu HTTP lub HTTPS do Internetu za pomocą Application Gateway.