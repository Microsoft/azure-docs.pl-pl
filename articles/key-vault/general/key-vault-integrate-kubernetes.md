---
title: Integrowanie usługi Azure Key Vault z usługą Kubernetes
description: W tym samouczku uzyskujesz dostęp do wpisów tajnych z magazynu kluczy platformy Azure i pobierasz je za pomocą sterownika Kubernetes magazynu kontenerów (CSI), aby zainstalować do zasobników.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 0398c035eeac7d02ac38f798cb58c513279fc709
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820004"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Samouczek: Konfigurowanie i uruchamianie dostawcy Azure Key Vault dla sterownika CSI magazynu wpisów tajnych w systemie Kubernetes

> [!IMPORTANT]
> Sterownik CSI to projekt open source, który nie jest obsługiwany przez pomoc techniczną platformy Azure. Zgłoś wszystkie opinie i problemy związane z sterownikiem CSI Key Vault integrację z linkiem GitHub w dolnej części strony. To narzędzie jest udostępniane użytkownikom do samodzielnego instalowania w klastrach i zbierania informacji zwrotnych z naszej społeczności.

Korzystając z tego samouczka, możesz uzyskać dostęp do wpisów tajnych z magazynu kluczy platformy Azure i pobrać je z niego przy użyciu sterownika Kubernetes magazynu kontenerów (CSI).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nazwę główną usługi lub użyj zarządzanych tożsamości.
> * Wdróż klaster usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
> * Zainstaluj Helm i sterownik CSI magazynu Secret.
> * Utwórz magazyn kluczy platformy Azure i ustaw wpisy tajne.
> * Utwórz własny obiekt SecretProviderClass.
> * Przypisz nazwę główną usługi lub użyj zarządzanych tożsamości.
> * Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z magazynu kluczy.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Tworzenie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych

Jeśli planujesz korzystanie z tożsamości zarządzanych, możesz przejść do następnej sekcji.

Tworzenie jednostki usługi w celu kontrolowania zasobów, do których można uzyskać dostęp z magazynu kluczy platformy Azure. Dostęp do tej jednostki usługi jest ograniczony przez przypisane do niej role. Ta funkcja zapewnia kontrolę nad sposobem zarządzania kluczami tajnymi przez jednostkę usługi. W poniższym przykładzie nazwa nazwy głównej usługi to *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Ta operacja zwraca serię par klucz/wartość:

![Zrzut ekranu przedstawiający identyfikator appId i hasło dla contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Skopiuj poświadczenia **AppID** i **Password** do późniejszego użycia.

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure

Nie musisz używać Azure Cloud Shell. Wystarczy, że zostanie wyświetlony wiersz polecenia (Terminal) z zainstalowaną usługą Azure CLI. 

Wypełnij sekcje "Tworzenie grupy zasobów", "Tworzenie klastra AKS" i "łączenie się z klastrem" w temacie [wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough). 

> [!NOTE] 
> Jeśli planujesz użycie tożsamości pod zamiast nazwy głównej usługi, pamiętaj, aby włączyć ją podczas tworzenia klastra Kubernetes, jak pokazano w następującym poleceniu:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Ustaw zmienną środowiskową Path](https://www.java.com/en/download/help/path.xml) na pobrany plik *kubectl.exe* .
1. Sprawdź wersję Kubernetes przy użyciu następującego polecenia, które powoduje wyjście z wersji klienta i serwera. Wersja klienta to plik *kubectl.exe* , który został zainstalowany, a wersja serwera to usługa Azure Kubernetes Services (AKS), na której działa klaster.
    ```azurecli
    kubectl version
    ```
1. Upewnij się, że wersja Kubernetes to 1.16.0 lub nowsza. W przypadku klastrów systemu Windows upewnij się, że wersja Kubernetes to 1.18.0 lub nowsza. Następujące polecenie uaktualnia zarówno klaster Kubernetes, jak i pulę węzłów. Wykonanie polecenia może potrwać kilka minut. W tym przykładzie grupa zasobów to *contosoResourceGroup*, a klaster Kubernetes to *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Aby wyświetlić metadane utworzonego klastra AKS, użyj następującego polecenia. Skopiuj **principalId**, **clientId**, identyfikator **subskrypcji**i **nodeResourceGroup** do późniejszego użycia. Jeśli klaster zaproszony nie został utworzony z włączonymi tożsamościami zarządzanymi, **principalId** i **clientId** będą mieć wartość null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    W danych wyjściowych są wyświetlane oba z wyróżnionych parametrów:
    
    ![Zrzut ekranu przedstawiający interfejs wiersza polecenia platformy Azure z wartościami principalId i clientId wyróżnionych ](../media/kubernetes-key-vault-2.png) ![ zrzutu ekranu interfejsu wiersza polecenia platformy Azure z wyróżnionymi wartościami subskrypcji i nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Zainstaluj Helm i sterownik CSI magazynu Secret

Aby zainstalować sterownik CSI magazynu kluczy tajnych, należy najpierw zainstalować [Helm](https://helm.sh/docs/intro/install/).

Przy użyciu interfejsu sterownika [magazynu kluczy tajnych](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) można uzyskać wpisy tajne przechowywane w wystąpieniu usługi Azure Key, a następnie użyć interfejsu sterownika do zainstalowania zawartości wpisu tajnego w Kubernetes.

1. Upewnij się, że wersja Helm ma wartość v3 lub nowszą:
    ```azurecli
    helm version
    ```
1. Zainstaluj sterownik CSI magazynu Secret i dostawcę Azure Key Vault dla sterownika:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Utwórz magazyn kluczy platformy Azure i ustaw wpisy tajne

Aby utworzyć własny magazyn kluczy i ustawić wpisy tajne, postępuj zgodnie z instrukcjami w temacie [Set and pobierając klucz tajny z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli).

> [!NOTE] 
> Nie musisz używać Azure Cloud Shell ani utworzyć nowej grupy zasobów. Możesz użyć grupy zasobów utworzonej wcześniej dla klastra Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Utwórz własny obiekt SecretProviderClass

Aby utworzyć własny niestandardowy obiekt SecretProviderClass z parametrami specyficznymi dla dostawcy dla sterownika CSI magazynu Secret, [Użyj tego szablonu](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Ten obiekt zapewni dostęp tożsamości do magazynu kluczy.

W przykładowym pliku SecretProviderClass YAML wprowadź brakujące parametry. Wymagane są następujące parametry:

* **userAssignedIdentityID**: # [wymagane] Jeśli używasz nazwy głównej usługi, użyj identyfikatora klienta, aby określić, która zarządzana tożsamość użytkownika ma być używana. Jeśli używasz tożsamości przypisanej przez użytkownika jako tożsamości zarządzanej maszyny wirtualnej, określ identyfikator klienta tożsamości. Jeśli wartość jest pusta, domyślnie zostanie użyta tożsamość przypisana do systemu na maszynie wirtualnej 
* **servicenamename: nazwa magazynu kluczy**
* **obiekty**: kontener dla całej zawartości tajnej, którą chcesz zainstalować
    * **ObjectName**: Nazwa tajnej zawartości
    * **ObjectType**: typ obiektu (klucz tajny, klucz, certyfikat)
* Grupa **zasobów: Nazwa**grupy zasobu # [wymagana dla wersji < 0.0.4] grupy zasobów magazynu kluczy
* **subskrypcji**: Identyfikator subskrypcji magazynu kluczy # [wymagany dla wersji < 0.0.4] Identyfikator subskrypcji magazynu klucza
* **tenantID**: Identyfikator dzierżawy lub identyfikator katalogu magazynu kluczy

Dokumentacja wszystkich wymaganych pól jest dostępna tutaj: [link](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Zaktualizowany szablon jest przedstawiony w poniższym kodzie. Pobierz go jako plik YAML i Wypełnij wymagane pola. W tym przykładzie Magazyn kluczy jest **contosoKeyVault5**. Ma dwa wpisy tajne, **secret1** i **secret2**.

> [!NOTE] 
> Jeśli używasz tożsamości zarządzanych, ustaw wartość **usePodIdentity** jako *true*i ustaw wartość **userAssignedIdentityID** jako parę cudzysłowów (**""**). 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Na poniższej ilustracji przedstawiono dane wyjściowe konsoli dla polecenia **AZ ServiceName show--Name contosoKeyVault5** z odpowiednimi wyróżnionymi metadanymi:

![Zrzut ekranu przedstawiający dane wyjściowe konsoli dla polecenia "AZ datamagazyn show--Name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Przypisywanie nazwy głównej usługi lub korzystanie z tożsamości zarządzanych

### <a name="assign-a-service-principal"></a>Przypisywanie nazwy głównej usługi

Jeśli używasz nazwy głównej usługi, Udziel uprawnień do uzyskiwania dostępu do magazynu kluczy i Pobierz wpisy tajne. Przypisz rolę *czytelnika* i przyznaj uprawnienia nazwy głównej usługi, aby *uzyskać* wpisy tajne z magazynu kluczy, wykonując następujące polecenie:

1. Przypisz jednostkę usługi do istniejącego magazynu kluczy. **$AZURE _CLIENT_ID** parametr jest **identyfikatorem AppID** skopiowanym po utworzeniu nazwy głównej usługi.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Dane wyjściowe polecenia są wyświetlane na poniższym obrazie: 

    ![Zrzut ekranu przedstawiający wartość principalId](../media/kubernetes-key-vault-5.png)

1. Przyznaj uprawnienia nazwy głównej usługi do uzyskiwania wpisów tajnych:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Nazwa główna usługi została skonfigurowana z uprawnieniami do odczytu wpisów tajnych z magazynu kluczy. **_CLIENT_SECRET $Azure** jest hasłem nazwy głównej usługi. Dodaj poświadczenia nazwy głównej usługi jako klucz tajny Kubernetes, który jest dostępny dla sterownika CSI magazynu wpisów tajnych:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Jeśli wdrażasz Kubernetes pod i wystąpi błąd dotyczący nieprawidłowego identyfikatora tajnego klienta, być może masz starszy identyfikator tajny klienta, którego ważność wygasła lub zresetowana. Aby rozwiązać ten problem, Usuń wpis tajny *magazynu — poświadczenia* i Utwórz nowy przy użyciu bieżącego identyfikatora tajnego klienta. Aby usunąć wpisy *tajne — poświadczenia magazynu*, uruchom następujące polecenie:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Jeśli nie pamiętasz identyfikatora tajnego klienta jednostki usługi, możesz go zresetować za pomocą następującego polecenia:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych

Jeśli używasz tożsamości zarządzanych, Przypisz określone role do utworzonego klastra AKS. 

1. Aby utworzyć, wyświetlić lub odczytać tożsamość zarządzaną przypisaną przez użytkownika, do klastra AKS należy przypisać rolę [operatora tożsamości zarządzanej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Upewnij się, że **$clientId** jest ClientId klastra Kubernetes. Zakres będzie objęty usługą subskrypcji platformy Azure, w odniesieniu do grupy zasobów węzła, która została utworzona podczas tworzenia klastra AKS. Ten zakres zapewni, że tylko zasoby w tej grupie mają wpływ role przypisane poniżej. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP

    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Zainstaluj tożsamość usługi Azure Active Directory (Azure AD) w usłudze AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Utwórz tożsamość usługi Azure AD. W danych wyjściowych Skopiuj **clientId** i **principalId** do późniejszego użycia.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Przypisz rolę *czytelnika* do tożsamości usługi Azure AD utworzonej w poprzednim kroku dla magazynu kluczy, a następnie Udziel uprawnień tożsamości do uzyskiwania wpisów tajnych z magazynu kluczy. Użyj **clientId** i **principalId** z tożsamości usługi Azure AD.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Wdróż swój program przy użyciu zainstalowanych wpisów tajnych z magazynu kluczy

Aby skonfigurować obiekt SecretProviderClass, uruchom następujące polecenie:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Korzystanie z nazwy głównej usługi

Jeśli używasz nazwy głównej usługi, użyj następującego polecenia, aby wdrożyć swoje Kubernetesy z systemem SecretProviderClass i wpisami tajnymi magazynu, które zostały wcześniej skonfigurowane. Oto szablony wdrażania:
* Dla systemu [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Dla [systemu Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych

Jeśli używasz tożsamości zarządzanych, Utwórz *AzureIdentity* w klastrze, który odwołuje się do utworzonej wcześniej tożsamości. Następnie Utwórz element *AzureIdentityBinding* , który odwołuje się do utworzonego AzureIdentity. Wypełnij parametry w następującym szablonie, a następnie zapisz go jako *podIdentityAndBinding. YAML*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Uruchom następujące polecenie, aby wykonać powiązanie:

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
        nodePublishSecretRef:
          name: secrets-store-creds 
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
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Aby wyświetlić zawartość określonego wpisu tajnego, uruchom następujące polecenie:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Sprawdź, czy jest wyświetlana zawartość wpisu tajnego.

## <a name="next-steps"></a>Następne kroki

Aby upewnić się, że Twój Magazyn kluczy jest możliwy do odzyskania, zobacz:
> [!div class="nextstepaction"]
> [Włącz usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/general/soft-delete-cli)
