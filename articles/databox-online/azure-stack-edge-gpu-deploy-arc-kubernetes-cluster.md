---
title: Włączanie usługi Azure Arc w systemie Kubernetes na urządzeniu z systemem Azure Stack Edge Microsoft Docs
description: Zawiera opis sposobu włączania usługi Azure Arc w istniejącym klastrze Kubernetes na urządzeniu GPU z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 4d75986880075f6eb07aa31b9322bdae15535802
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437608"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Włącz usługę Azure Arc w klastrze Kubernetes na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób włączania usługi Azure Arc w istniejącym klastrze Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro. 

Ta procedura jest przeznaczona dla osób, które dokonały przeglądu [obciążeń Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) i znają koncepcje [co to jest usługa Azure ARC z włączonym Kubernetes (wersja zapoznawcza)?](../azure-arc/kubernetes/overview.md).


## <a name="prerequisites"></a>Wymagania wstępne

Przed włączeniem usługi Azure Arc w klastrze Kubernetes upewnij się, że zostały spełnione następujące wymagania wstępne na urządzeniu z systemem Azure Stack EDGE Pro oraz z klientem, który będzie używany do uzyskiwania dostępu do urządzenia:

### <a name="for-device"></a>Na potrzeby urządzenia

1. Poświadczenia logowania są dostępne na urządzeniu z 1 węzłem Azure Stack Edge.
    1. Urządzenie zostało aktywowane. Zobacz [Aktywowanie urządzenia](azure-stack-edge-gpu-deploy-activate.md).
    1. Urządzenie ma rolę obliczeniową skonfigurowaną za pośrednictwem Azure Portal i ma klaster Kubernetes. Zobacz [Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Masz właściciela dostępu do subskrypcji. Ten dostęp będzie potrzebny podczas kroku przypisania roli dla nazwy głównej usługi.
 

### <a name="for-client-accessing-the-device"></a>Do uzyskiwania dostępu do urządzenia przez klienta

1. Masz system klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z systemem Azure Stack Edge.
  
    - Klient korzysta z programu Windows PowerShell 5,0 lub nowszego. Aby pobrać najnowszą wersję programu Windows PowerShell, przejdź do obszaru [Instalowanie programu Windows PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Możesz również mieć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . W tym artykule opisano procedurę w przypadku korzystania z klienta systemu Windows. 
    
1. Procedura opisana w artykule [Uzyskiwanie dostępu do klastra Kubernetes na urządzeniu z systemem Azure Stack Edge w systemie](azure-stack-edge-gpu-create-kubernetes-cluster.md). Masz:
    
    - Zainstalowane `kubectl` na kliencie programu.    
    - Upewnij się, że `kubectl` wersja klienta jest skośna nie więcej niż jedna wersja z wersji głównej Kubernetes działającej na urządzeniu Azure Stack EDGE Pro. 
      - Użyj, `kubectl version` Aby sprawdzić wersję polecenia kubectl działającą na kliencie. Zanotuj pełną wersję.
      - W lokalnym interfejsie użytkownika urządzenia z usługą Azure Stack Edge, przejdź do strony **Aktualizacja oprogramowania** i zanotuj numer wersji serwera Kubernetes. 
    
        ![Sprawdź numer wersji serwera Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Sprawdź, czy te dwie wersje są zgodne. 


## <a name="register-kubernetes-resource-providers"></a>Zarejestruj dostawców zasobów Kubernetes

Przed włączeniem usługi Azure Arc w klastrze Kubernetes należy włączyć i zarejestrować `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` swoją subskrypcję. 

1. Aby włączyć dostawcę zasobów, w Azure Portal przejdź do subskrypcji, której planujesz użyć do wdrożenia. Przejdź do pozycji **dostawcy zasobów**. 
1. W okienku po prawej stronie Wyszukaj dostawców, których chcesz dodać. W tym przykładzie `Microsoft.Kubernetes` i `Microsoft.KubernetesConfiguration` .

    ![Zarejestruj dostawców zasobów Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Wybierz dostawcę zasobów i w górnej części paska poleceń wybierz pozycję **zarejestruj**. Rejestracja trwa kilka minut. 

    ![Zarejestruj dostawców zasobów Kubernetes 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Odśwież interfejs użytkownika, dopóki nie zobaczysz, że dostawca zasobów został zarejestrowany. Powtórz ten proces dla obu dostawców zasobów.
    
    ![Zarejestruj dostawców zasobów Kubernetes 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Dostawców zasobów można rejestrować również za pośrednictwem `az cli` . Aby uzyskać więcej informacji, zobacz [Rejestrowanie dwóch dostawców dla usługi Azure ARC z włączonym Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Tworzenie nazwy głównej usługi, przypisywanie roli

1. Upewnij się, że masz `Subscription ID` i nazwę grupy zasobów, która została użyta do wdrożenia zasobów dla usługi Azure Stack Edge. Aby uzyskać identyfikator subskrypcji, przejdź do zasobu brzegowego Azure Stack w Azure Portal. Przejdź do **omówienia > Essentials**.

    ![Pobierz identyfikator subskrypcji](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Aby uzyskać nazwę grupy zasobów, przejdź do pozycji **Właściwości**.

    ![Pobierz nazwę grupy zasobów](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Aby utworzyć jednostkę usługi, użyj następującego polecenia za pośrednictwem `az cli` .

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    Aby uzyskać informacje dotyczące sposobu logowania się do programu `az cli` , [Uruchom Cloud Shell w Azure Portal](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Oto przykład: 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Zanotuj `appID` ,, `name` `password` , i `tenantID` jak będziesz używać jako danych wejściowych w następnym poleceniu.

1. Po utworzeniu nowej jednostki usługi Przypisz `Kubernetes Cluster - Azure Arc Onboarding` rolę do nowo utworzonego podmiotu zabezpieczeń. Jest to wbudowana rola platformy Azure (Użyj identyfikatora roli w poleceniu) z ograniczonymi uprawnieniami. Użyj następującego polecenia:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Oto przykład:
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Aby uzyskać więcej informacji na temat tworzenia nazwy głównej usługi i wykonywania przypisania roli, zobacz kroki opisane w temacie [Tworzenie jednostki usługi dołączania z włączoną funkcją Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Włączanie usługi Arc w klastrze platformy Kubernetes

Wykonaj następujące kroki, aby skonfigurować klaster Kubernetes na potrzeby zarządzania usługą Azure ARC:

1. [Nawiąż połączenie z interfejsem programu PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) urządzenia.

1. Wpisz:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Aby wdrożyć łuk platformy Azure na urządzeniu, upewnij się, że używasz [obsługiwanego regionu dla usługi Azure Arc](../azure-arc/kubernetes/overview.md#supported-regions). 
    > - Użyj `az account list-locations` polecenia, aby ustalić dokładną nazwę lokalizacji do przekazania do `Set-HcsKubernetesAzureArcAgent` polecenia cmdlet. Nazwy lokalizacji są zwykle sformatowane bez spacji.
    
    Oto przykład:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    W Azure Portal należy utworzyć zasób o nazwie podanej w poprzednim poleceniu.

    ![Przejdź do zasobu usługi Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Aby sprawdzić, czy usługa Azure Arc została włączona pomyślnie, uruchom następujące polecenie w interfejsie programu PowerShell:

    `kubectl get deployments -n azure-arc`

    To polecenie odnajduje wszystkie aplikacje wdrożone w `azure-arc` przestrzeni nazw odpowiadające usłudze Azure Arc.

    Oto przykładowe dane wyjściowe pokazujące agentów usługi Azure Arc wdrożonych w klastrze Kubernetes w `azure-arc` przestrzeni nazw. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    W przestrzeni nazw można również uzyskać listę zasobników uruchomionych w klastrze Kubernetes `azure-arc` . Jest to kontener aplikacji lub proces uruchomiony w klastrze Kubernetes. 

    Użyj następującego polecenia:
    
    `kubectl get pods -n azure-arc`
    
    Oto przykładowe dane wyjściowe.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Zgodnie z powyższymi danymi wyjściowymi usługa Azure ARC z włączonym Kubernetes składa się z kilku agentów (operatorów) uruchomionych w klastrze wdrożonym w `azure-arc` przestrzeni nazw.

- `config-agent`: Obserwujący połączony klaster dla zasobów konfiguracji kontroli źródła zastosowanych w klastrze i aktualizacji stanu zgodności
- `controller-manager`: jest operatorem operatorów i organizuje interakcje między składnikami usługi Azure Arc
- `metrics-agent`: zbiera metryki innych agentów ARC, aby upewnić się, że te agenci wykazują optymalną wydajność
- `cluster-metadata-operator`: zbiera metadane klastra — wersję klastra, liczbę węzłów i wersję agenta usługi Azure Arc
- `resource-sync-agent`: synchronizuje powyższe metadane klastra z platformą Azure
- `clusteridentityoperator`: Usługa Azure ARC z włączonym Kubernetes obecnie obsługuje tożsamość przypisaną do systemu. clusteridentityoperator zachowuje certyfikat tożsamości usługi zarządzanej (MSI) używany przez innych agentów do komunikacji z platformą Azure.
- `flux-logs-agent`: zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła.
- `connect-agent`: rozmowy z zasobem usługi Azure Arc.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Usuwanie łuku z klastra Kubernetes

Aby usunąć usługę Azure Arc Management, wykonaj następujące kroki:

1. 1. [Nawiąż połączenie z interfejsem programu PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) urządzenia.
2. Wpisz:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Domyślnie po `yamls` usunięciu zasobu z repozytorium git odpowiednie zasoby nie są usuwane z klastra Kubernetes. Należy ustawić `--sync-garbage-collection`  w łuku OperatorParams, aby zezwolić na usunięcie zasobów po usunięciu z repozytorium git. Aby uzyskać więcej informacji, zobacz [Usuwanie konfiguracji](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>Następne kroki

Aby zrozumieć, jak uruchomić wdrożenie usługi Azure ARC, zobacz [wdrażanie bezstanowej `Guestbook` aplikacji PHP z Redis za pośrednictwem GitOps na urządzeniu z systemem Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
