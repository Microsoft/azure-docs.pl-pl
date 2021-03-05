---
title: Integrowanie usługi Azure Key Vault z usługą Kubernetes
description: W tym samouczku uzyskujesz dostęp do wpisów tajnych z magazynu kluczy platformy Azure i pobierasz je za pomocą sterownika Kubernetes magazynu kontenerów (CSI), aby zainstalować do zasobników.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b130fd3f85b676f0a394ad95730181ff499dac96
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216500"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Samouczek: Konfigurowanie i uruchamianie dostawcy Azure Key Vault dla sterownika CSI magazynu wpisów tajnych w systemie Kubernetes

> [!IMPORTANT]
> Sterownik CSI magazynu kluczy tajnych to projekt open source, który nie jest obsługiwany przez pomoc techniczną platformy Azure. Zgłoś wszystkie opinie i problemy związane z sterownikiem CSI Key Vault integrację z linkiem GitHub w dolnej części strony. To narzędzie jest udostępniane użytkownikom do samodzielnego instalowania w klastrach i zbierania informacji zwrotnych z naszej społeczności.

Korzystając z tego samouczka, możesz uzyskać dostęp do wpisów tajnych z magazynu kluczy platformy Azure i pobrać je z niego przy użyciu sterownika Kubernetes magazynu kontenerów (CSI).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Użyj zarządzanych tożsamości.
> * Wdróż klaster usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
> * Zainstaluj Helm, sterownik i dostawca Azure Key Vault magazynu kluczy tajnych dla sterownika CSI.
> * Utwórz magazyn kluczy platformy Azure i ustaw wpisy tajne.
> * Utwórz własny obiekt SecretProviderClass.
> * Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z magazynu kluczy.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-windows).

W tym samouczku przyjęto założenie, że uruchomiono usługę Azure Kubernetes w węzłach systemu Linux.

## <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych

Ten diagram ilustruje przepływ integracji AKS — Key Vault dla tożsamości zarządzanej:

![Diagram przedstawiający przepływ integracji AKS — Key Vault dla tożsamości zarządzanej](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure

Nie musisz używać Azure Cloud Shell. Wystarczy, że zostanie wyświetlony wiersz polecenia (Terminal) z zainstalowaną usługą Azure CLI. 

Wypełnij sekcje "Tworzenie grupy zasobów", "Tworzenie klastra AKS" i "łączenie się z klastrem" w temacie [wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Jeśli planujesz korzystanie z tożsamości pod, zalecana wtyczka sieciowa to `azure` . Aby uzyskać więcej informacji, zapoznaj się z [dokumentem](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) . Utwórz klaster Kubernetes, jak pokazano w następującym poleceniu:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Ustaw zmienną środowiskową Path](https://www.java.com/en/download/help/path.xml) na pobrany plik *kubectl.exe* .
2. Sprawdź wersję Kubernetes przy użyciu następującego polecenia, które powoduje wyjście z wersji klienta i serwera. Wersja klienta to plik *kubectl.exe* , który został zainstalowany, a wersja serwera to usługa Azure Kubernetes Services (AKS), na której działa klaster.
    ```azurecli
    kubectl version
    ```
3. Upewnij się, że wersja Kubernetes to 1.16.0 lub nowsza. W przypadku klastrów systemu Windows upewnij się, że wersja Kubernetes to 1.18.0 lub nowsza. Następujące polecenie uaktualnia zarówno klaster Kubernetes, jak i pulę węzłów. Wykonanie polecenia może potrwać kilka minut. W tym przykładzie grupa zasobów to *contosoResourceGroup*, a klaster Kubernetes to *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Aby wyświetlić metadane utworzonego klastra AKS, użyj następującego polecenia. Skopiuj **principalId**, **clientId**, identyfikator **subskrypcji** i **nodeResourceGroup** do późniejszego użycia. Jeśli klaster AKS nie został utworzony z włączonymi tożsamościami zarządzanymi, **principalId** i **clientId** będą mieć wartość null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    W danych wyjściowych są wyświetlane oba z wyróżnionych parametrów:
    
    ![Zrzut ekranu przedstawiający interfejs wiersza polecenia platformy Azure z wartościami principalId i clientId wyróżnionych ](../media/kubernetes-key-vault-2.png) ![ zrzutu ekranu interfejsu wiersza polecenia platformy Azure z wyróżnionymi wartościami subskrypcji i nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Zainstaluj Helm i sterownik CSI magazynu Secret
> [!NOTE]
> Poniższa instalacja działa tylko na AKS w systemie Linux. Aby uzyskać więcej informacji na temat instalacji sterownika CSI magazynu kluczy tajnych, zobacz [Azure Key Vault Provider for Secret przechowuj sterownik CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

Aby zainstalować sterownik i dostawca Azure Key Vault magazynu danych tajnych, należy najpierw zainstalować [Helm](https://helm.sh/docs/intro/install/).

Przy użyciu interfejsu sterownika w [magazynie kluczy tajnych](https://azure.github.io/secrets-store-csi-driver-provider-azure/) można uzyskać wpisy tajne przechowywane w wystąpieniu Azure Key Vault, a następnie użyć interfejsu sterownika, aby zainstalować zawartość klucza tajnego w Kubernetesch.

1. Upewnij się, że wersja Helm ma wartość v3 lub nowszą:
    ```azurecli
    helm version
    ```
1. Zainstaluj sterownik CSI magazynu Secret i dostawcę Azure Key Vault dla sterownika:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Jeśli planujesz użycie sterownika CSI magazynu i dostawcy Azure Key Vault w węzłach systemu Windows, Włącz sterownik i dostawcę w węzłach systemu Windows przy użyciu [wartości konfiguracyjnych Helm](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration)

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Utwórz magazyn kluczy platformy Azure i ustaw wpisy tajne

Aby utworzyć własny magazyn kluczy i ustawić wpisy tajne, postępuj zgodnie z instrukcjami w temacie [Set and pobierając klucz tajny z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md).

> [!NOTE] 
> Nie musisz używać Azure Cloud Shell ani utworzyć nowej grupy zasobów. Możesz użyć grupy zasobów utworzonej wcześniej dla klastra Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Utwórz własny obiekt SecretProviderClass

Aby utworzyć własny niestandardowy obiekt SecretProviderClass z parametrami specyficznymi dla dostawcy dla sterownika CSI magazynu Secret, [Użyj tego szablonu](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml). Ten obiekt zapewni dostęp tożsamości do magazynu kluczy.

W przykładowym pliku SecretProviderClass YAML wprowadź brakujące parametry. Wymagane są następujące parametry:

* **servicenamename: nazwa magazynu kluczy**
* **obiekty**: Lista wszystkich wpisów tajnych, które mają zostać zainstalowane
    * **ObjectName**: Nazwa tajnej zawartości
    * **ObjectType**: typ obiektu (klucz tajny, klucz, certyfikat)
* **tenantID**: Identyfikator dzierżawy lub identyfikator katalogu magazynu kluczy

Dokumentacja wszystkich wymaganych pól i obsługiwanych konfiguracji jest dostępna tutaj: [link](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Zaktualizowany szablon jest przedstawiony w poniższym kodzie. Pobierz go jako plik YAML i Wypełnij wymagane pola. W tym przykładzie Magazyn kluczy jest **contosoKeyVault5**. Ma dwa wpisy tajne, **secret1** i **secret2**.

> [!NOTE] 
> Jeśli używasz tożsamości na początku, ustaw wartość **usePodIdentity** jako *true* i ustaw wartość **userAssignedIdentityID** jako parę cudzysłowów (**""**). Jeśli używasz tożsamości zarządzanych, ustaw wartość **useVMManagedIdentity** jako *true* i ustaw wartość **userAssignedIdentityID** jako clientID dla tożsamości przypisanej do użytkownika.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
Na poniższej ilustracji przedstawiono dane wyjściowe konsoli dla polecenia **AZ ServiceName show--Name contosoKeyVault5** z odpowiednimi wyróżnionymi metadanymi:

![Zrzut ekranu przedstawiający dane wyjściowe konsoli dla polecenia "AZ datamagazyn show--Name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Zainstaluj Azure Active Directory (Azure AD) pod tożsamością

1. Przypisz określone role do utworzonego klastra AKS. 

    Dokumentacja dotycząca wszystkich wymaganych przypisań ról z usługą Azure Active Directory (Azure AD) pod tożsamością jest dostępna tutaj: [link](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Zainstaluj tożsamość usługi Azure Active Directory (Azure AD) w usłudze AKS.

    > [!NOTE] 
    > Jeśli używany jest klaster AKS z wtyczką sieci korzystającą wtyczki kubenet, zapoznaj się z tym [dokumentem](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) , jak wdrożyć tożsamość pod tożsamością w klastrze.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Utwórz User-Assigned tożsamość zarządzaną. W danych wyjściowych Skopiuj **clientId** do późniejszego użycia.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Przyznaj tożsamości uprawnienia do pobierania wpisów tajnych z magazynu kluczy. Użyj **clientId** z tożsamości zarządzanej przypisanej przez użytkownika.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z magazynu kluczy

Aby skonfigurować obiekt SecretProviderClass, uruchom następujące polecenie:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych

Jeśli używasz tożsamości pod, Utwórz *AzureIdentity* w klastrze, który odwołuje się do utworzonej wcześniej tożsamości. Następnie Utwórz element *AzureIdentityBinding* , który odwołuje się do utworzonego AzureIdentity. Wypełnij parametry w następującym szablonie, a następnie zapisz go jako *podIdentityAndBinding. YAML*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Uruchom następujące polecenie, aby skonfigurować powiązanie:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Następnie należy wdrożyć usługę pod. Poniższy kod to plik wdrożenia YAML, który używa powiązania tożsamości pod powyższym krokiem. Zapisz ten plik jako *podBindingDeployment. YAML*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Uruchom następujące polecenie, aby wdrożyć swój system:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Sprawdź stan i zawartość wpisu tajnego. 

Aby wyświetlić wdrożone zasobniki, uruchom następujące polecenie:
```azurecli
kubectl get pods
```

Aby sprawdzić stan Twojego obszaru, uruchom następujące polecenie:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Zrzut ekranu przedstawiający dane wyjściowe interfejsu wiersza polecenia platformy Azure, w którym jest wyświetlany stan "uruchomiona" pod i pokazując wszystkie zdarzenia jako "normalne" ](../media/kubernetes-key-vault-6.png)

W oknie dane wyjściowe wdrożony pod powinien znajdować się w stanie *uruchomionym* . W sekcji **zdarzenia** u dołu wszystkie typy zdarzeń są wyświetlane jako *normalne*.

Po sprawdzeniu, że jest uruchomiony, możesz sprawdzić, czy w obszarze znajdują się wpisy tajne z magazynu kluczy.

Aby wyświetlić wszystkie wpisy tajne zawarte w obszarze, uruchom następujące polecenie:
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Aby wyświetlić zawartość określonego wpisu tajnego, uruchom następujące polecenie:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Sprawdź, czy jest wyświetlana zawartość wpisu tajnego.

## <a name="next-steps"></a>Następne kroki

Aby upewnić się, że Twój Magazyn kluczy jest możliwy do odzyskania, zobacz:
> [!div class="nextstepaction"]
> [Włącz usuwanie nietrwałe](./key-vault-recovery.md)
