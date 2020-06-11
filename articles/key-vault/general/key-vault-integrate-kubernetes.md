---
title: Integrowanie usługi Azure Key Vault z usługą Kubernetes
description: W ramach tego samouczka będziesz mieć dostęp do wpisów tajnych z Azure Key Vault przy użyciu sterownika magazynu informacji o kluczu kontenera (
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: 27d602f22aa3915f39f21ac924afa42b98e70720
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667158"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Samouczek: Konfigurowanie i uruchamianie dostawcy Azure Key Vault na potrzeby sterownika CSI magazynu Secret w systemie Kubernetes

W ramach tego samouczka będziesz mieć dostęp do wpisów tajnych z Azure Key Vault przy użyciu sterownika magazynu informacji o kluczu kontenera (

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych
> * Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure
> * Zainstaluj sterownik CSI magazynu Helm i wpisów tajnych
> * Tworzenie Azure Key Vault i Ustawianie wpisów tajnych
> * Utwórz własny obiekt SecretProviderClass
> * Przypisywanie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych
> * Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z Key Vault

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Tworzenie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych

Jeśli planujesz korzystanie z tożsamości zarządzanych, możesz przejść do następnej sekcji.

Utwórz jednostkę usługi, aby kontrolować zasoby, do których można uzyskiwać dostęp z Azure Key Vault. Dostęp do tej jednostki usługi jest ograniczony przez przypisane do niej role. Ta funkcja zapewnia kontrolę nad sposobem zarządzania kluczami tajnymi przez jednostkę usługi. W poniższym przykładzie nazwa nazwy głównej usługi to **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Ta operacja zwraca serię par klucz/wartość:

![Obraz](../media/kubernetes-key-vault-1.png)

Skopiuj **identyfikator appid** i **hasło**. Te poświadczenia będą potrzebne później.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure

Nie musisz używać Azure Cloud Shell, zostanie wyświetlony wiersz polecenia (Terminal) z zainstalowaną usługą Azure CLI. 

Postępuj zgodnie z tym [przewodnikiem](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) i wykonaj następujące sekcje: **Tworzenie grupy zasobów**, **Tworzenie klastra AKS**i **nawiązywanie połączenia z klastrem**.

**Uwaga:** Jeśli planujesz używać tożsamości pod, zamiast nazwy głównej usługi. Upewnij się, że podczas tworzenia klastra Kubernetes został on włączony, jak pokazano poniżej:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Ustaw zmienną środowiskową Path](https://www.java.com/en/download/help/path.xml) na pobrany plik "kubectl.exe".
1. Sprawdź wersję usługi Kubernetes przy użyciu poniższego polecenia. To polecenie spowoduje wyjście z wersji klienta i serwera. Wersja klienta to "kubectl.exe", który został zainstalowany, gdy wersja serwera to usługi Azure Kubernetes, na których działa klaster.
    ```azurecli
    kubectl version
    ```
1. Upewnij się, że wersja Kubernetes ma wartość **1.16.0** lub nowszą. To polecenie spowoduje uaktualnienie zarówno klastra Kubernetes, jak i puli węzłów. Wykonanie tej operacji może potrwać kilka minut. W tym przykładzie grupa zasobów to **contosoResourceGroup** , a klaster Kubernetes to **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Wyświetl metadane utworzonego klastra AKS przy użyciu poniższego polecenia. Skopiuj w dół **principalId**, **clientId**, identyfikator **subskrypcji**i **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    To są dane wyjściowe z wyróżnionymi obydwoma parametrami.
    
    ![](../media/kubernetes-key-vault-2.png) ![ Obraz obrazu](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Zainstaluj sterownik CSI magazynu Helm i wpisów tajnych

Aby zainstalować sterownik CSI magazynu kluczy tajnych, należy zainstalować [Helm](https://helm.sh/docs/intro/install/) .

Interfejs sterownika [CSI magazynu wpisów tajnych](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) umożliwia pobieranie zawartości wpisów tajnych przechowywanych w wystąpieniu Azure Key Vault i używanie interfejsu sterownika do instalowania tych wpisów tajnych w Kubernetesch.

1. Sprawdź wersję Helm i upewnij się, że jest to wersja v3 lub nowsza:
    ```azurecli
    helm version
    ```
1. Zainstaluj sterownik CSI magazynu Secret i dostawcę Azure Key Vault dla sterownika:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Tworzenie Azure Key Vault i Ustawianie wpisów tajnych

Postępuj zgodnie z tym [przewodnikiem](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) , aby utworzyć własne Key Vault i ustawić wpisy tajne.

**Uwaga:** Nie musisz używać Azure Cloud Shell ani utworzyć nowej grupy zasobów. Korzystanie z utworzonej wcześniej grupy zasobów dla klastra Kubernetes jest bardzo precyzyjne.

## <a name="create-your-own-secretproviderclass-object"></a>Utwórz własny obiekt SecretProviderClass

Użyj tego [szablonu](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) , aby utworzyć własny niestandardowy obiekt SecretProviderClass w celu zapewnienia parametrów specyficznych dla dostawcy dla sterownika CSI magazynu. Ten obiekt zapewni dostęp do tożsamości Key Vault.

Przy użyciu dostarczonego przykładowego pliku YAML SecretProviderClass. Zamierzasz wypełnić brakujące parametry. Wymagane są następujące parametry:

1.  **userAssignedIdentityID:** Identyfikator klienta jednostki usługi
1.  **Identyfikator magazynu kluczy:** Nazwa Key Vault
1.  **obiekty:** Ten obiekt będzie zawierać całą zawartość tajną, którą chcesz zainstalować
    1.  Nazwa obiektu **:** Nazwa tajnej zawartości
    1.  Typ obiektu **:** Typ obiektu (klucz tajny, klucz, certyfikat)
1.  **odsourceing:** Nazwa grupy zasobów
1.  **Identyfikator subskrypcji:** Identyfikator subskrypcji Key Vault
1.  **tenantID:** Identyfikator dzierżawy (czyli identyfikator katalogu) Key Vault

Poniżej znajduje się zaktualizowany szablon, który należy pobrać jako plik. YAML i wypełnić odpowiednie wymagane pola. W tym przykładzie Key Vault jest **contosoKeyVault5** i ma dwa klucze tajne, **secret1** i **secret2**.

**Uwaga:** Jeśli używasz tożsamości zarządzanych, pole **usePodIdentity** musi mieć **wartość true** i pozostawić pole **userAssignedIdentityID** z cudzysłowem **""**. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Poniżej znajduje się dane wyjściowe konsoli "AZ kluczy show--Name contosoKeyVault5" z odpowiednimi wyróżnionymi metadanymi:

![Obraz](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Przypisywanie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych

### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi

Jeśli jest używana jednostka usługi. Aby uzyskać dostęp do Key Vault i pobrać wpisy tajne, musisz nadać mu uprawnienia do nazwy głównej usługi. Przypisz rolę **"czytelnik"** i nadaj jej nazwę główną uprawnienia do **"pobrania"** wpisów tajnych z Key Vault, wykonując poniższe kroki:

1. Przypisz jednostkę usługi do istniejącej Key Vault. **$Azure parametr _CLIENT_ID** jest **identyfikatorem** użytkownika skopiowanym po utworzeniu nazwy głównej usługi.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Poniżej znajduje się dane wyjściowe polecenia: 

    ![Obraz](../media/kubernetes-key-vault-5.png)

1. Nadaj uprawnienia główne usługi do pobierania wpisów tajnych:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Po skonfigurowaniu jednostki usługi w celu uzyskania uprawnień do odczytywania wpisów tajnych z Key Vault. **_CLIENT_SECRET $Azure** jest **hasłem** nazwy głównej usługi. Dodaj poświadczenia nazwy głównej usługi jako Kubernetes klucz tajny dostępny dla sterownika CSI magazynu wpisów tajnych:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Uwaga:** Jeśli wystąpi błąd później podczas wdrażania Kubernetes pod informacjami o nieprawidłowym IDENTYFIKATORze wpisu tajnego klienta. Być może masz starszy identyfikator tajny klienta, którego ważność wygasła lub została zresetowana. Aby rozwiązać ten problem, Usuń wpisy tajne "wpisy tajne — poświadczenia" i Utwórz nowy identyfikator tajny klienta. Uruchom poniższe polecenie, aby usunąć "wpisy tajne magazynu-poświadczenia":
```azurecli
kubectl delete secrets secrets-store-creds
```

Jeśli nie pamiętasz identyfikatora tajnego klienta jednostki usługi, możesz zresetować go przy użyciu następującego polecenia:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Korzystanie z tożsamości zarządzanych

W przypadku używania tożsamości zarządzanych Przypisz określone role do utworzonego klastra AKS. 
1. Aby utworzyć/wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, do klastra AKS należy przypisać rolę [współautor tożsamości zarządzanej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) . Upewnij się, że **$clientId** znajduje się w klastrze Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Zainstaluj tożsamość usługi Azure Active Directory (AAD) w usłudze AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Utwórz tożsamość usługi AAD. Skopiuj **clientId** i **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Przypisz rolę czytelnika do tożsamości usługi AAD, która została właśnie utworzona dla Key Vault. Następnie nadaj tożsamości uprawnienia do pobierania wpisów tajnych z Key Vault. Zamierzasz korzystać z **clientId** i **principalId** z właśnie utworzonej tożsamości platformy Azure.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z Key Vault

Poniższe polecenie skonfiguruje obiekt SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi

Jeśli jest używana jednostka usługi. Poniższe polecenie spowoduje wdrożenie Kubernetes z magazynem z SecretProviderClass i wpisami tajnymi magazynu — skonfigurowanymi poświadczeniami. Oto szablon wdrożenia dla systemów [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) i [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) .
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Korzystanie z tożsamości zarządzanych

Jeśli są używane tożsamości zarządzane. Utworzysz **AzureIdentity** w klastrze, który będzie odwoływać się do utworzonej wcześniej tożsamości. Następnie Utwórz element **AzureIdentityBinding** , który będzie odwoływać się do utworzonego **AzureIdentity** . Użyj szablonu poniżej, Wypełnij odpowiednie parametry i Zapisz go jako **podIdentityAndBinding. YAML**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Uruchom następujące polecenie, aby wykonać powiązanie:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Następnie jest rzeczywistym wdrożeniem pod. Poniżej znajduje się plik wdrożenia YAML, który będzie używał powiązania tożsamości pod z ostatniego kroku. Zapisz ten plik jako **podBindingDeployment. YAML**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
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
### <a name="check-status-and-secret-content"></a>Sprawdź stan i zawartość wpisu tajnego 
Aby wyświetlić wdrożone zasobniki:
```azurecli
kubectl get pods
```

Aby sprawdzić stan Twojego elementu, użyj następującego polecenia:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Obraz](../media/kubernetes-key-vault-6.png)

Wdrożony pod powinien być w stanie "uruchomiona". W sekcji "events" u dołu wszystkie typy zdarzeń po lewej stronie są klasyfikowane jako "normalne".
Po sprawdzeniu, że jest uruchomiony, możesz sprawdzić, czy na twoim komputerze znajdują się wpisy tajne z Key Vault.

Aby wyświetlić wszystkie wpisy tajne, których to dotyczy:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Aby uzyskać zawartość z określonego klucza tajnego:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Sprawdź zawartość wyświetlanego wpisu tajnego.

## <a name="next-steps"></a>Następne kroki

Upewnij się, że Key Vault jest możliwe do odzyskania:
> [!div class="nextstepaction"]
> [Włącz usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
