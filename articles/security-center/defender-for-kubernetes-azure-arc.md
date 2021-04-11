---
title: Ochrona hybrydowych i wielochmurowych wdrożeń Kubernetes za pomocą usługi Azure Defender dla Kubernetes
description: Korzystanie z usługi Azure Defender do Kubernetes z lokalnymi i wielochmurowymi klastrami Kubernetes
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029203"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Obrony klastrów Kubernetes z obsługą usługi Azure Arc w środowiskach lokalnych i wielu chmur

**Rozszerzenie klastrów usługi Azure Defender for Kubernetes** może chronić klastry lokalne przy użyciu tych samych możliwości wykrywania zagrożeń oferowanych przez klastry usługi Azure Kubernetes. Włącz [usługę Azure ARC z włączonym Kubernetes](../azure-arc/kubernetes/overview.md) w klastrach i Wdróż rozszerzenie zgodnie z opisem na tej stronie. 

Rozszerzenie może również chronić klastry Kubernetes w innych dostawcach chmury, ale nie na zarządzanych usługach Kubernetes.

> [!TIP]
> W [przykładach instalacji w witrynie GitHub](https://aka.ms/kubernetes-extension-installation-examples)umieszczono przykładowe pliki pomocne w procesie instalacji.

## <a name="availability"></a>Dostępność

| Aspekt | Szczegóły |
|--------|---------|
| Stan wydania | **Wersja zapoznawcza**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Wymagane role i uprawnienia | [Administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) może odrzucić alerty<br>[Czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader) może przeglądać wyniki |
| Ceny | Wymaga [usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md) |
| Obsługiwane dystrybucje Kubernetes | [Usługa Azure Kubernetes na Azure Stack HCL](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Aparat usługi AKS](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (wersja 4,6 lub nowsza) |
| Ograniczenia | Usługa Azure Arc Kubernetes i rozszerzenie usługi Azure Defender **nie obsługują** zarządzanych ofert Kubernetes, takich jak Google Kubernetes Engine i Elastic Kubernetes Service. [Usługa Azure Defender jest natywnie dostępna dla usługi Azure Kubernetes Service (AKS)](defender-for-kubernetes-introduction.md) i nie wymaga połączenia klastra z usługą Azure Arc. |
| Środowiska i regiony | Dostępność dla tego rozszerzenia jest taka sama jak w przypadku [usługi Azure Arc Kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Omówienie architektury

W przypadku wszystkich klastrów Kubernetes innych niż AKS należy połączyć klaster z usługą Azure Arc. Po nawiązaniu połączenia usługa Azure Defender for Kubernetes może zostać wdrożona w usłudze [Azure ARC z włączonymi zasobami Kubernetes](../azure-arc/kubernetes/overview.md) jako [rozszerzeniem klastra](../azure-arc/kubernetes/extensions.md).

Składniki rozszerzeń zbierają dane dzienników inspekcji Kubernetes ze wszystkich węzłów płaszczyzny kontroli w klastrze i wysyłają je do usługi Azure Defender dla zaplecza Kubernetes w chmurze w celu dalszej analizy. Rozszerzenie jest zarejestrowane w obszarze roboczym Log Analytics używanym jako Potok danych, ale dane dziennika inspekcji nie są przechowywane w obszarze roboczym Log Analytics.

Ten diagram przedstawia interakcję między usługą Azure Defender for Kubernetes a klastrem Kubernetes z włączonym usługą Azure ARC:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Diagram architektury wysokiego poziomu umożliwia tworzenie konspektu interakcji między usługą Azure Defender for Kubernetes oraz klastrami Kubernetes z obsługą usługi Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure Defender for Kubernetes jest [włączona w ramach Twojej subskrypcji](enable-azure-defender.md)
- Klaster Kubernetes jest [połączony z usługą Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Zostały spełnione wymagania wstępne wymienione w [dokumentacji ogólnych rozszerzeń klastra](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>Wdrażanie rozszerzenia usługi Azure Defender

Rozszerzenie usługi Azure Defender można wdrożyć przy użyciu szeregu metod. Aby uzyskać szczegółowe instrukcje, wybierz odpowiednią kartę.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Użyj opcji "szybkie rozwiązanie" z zalecenia Security Center

Dedykowane zalecenie w Azure Security Center zapewnia następujące informacje:

- **Widoczność** o tym, które z klastrów ma wdrożone rozszerzenie Defender for Kubernetes
- **Opcja "Szybka naprawa" służąca** do wdrażania jej w tych klastrach bez rozszerzenia

1. Na stronie zalecenia dotyczące programu Azure Security Center Otwórz pozycję **Włącz kontrolę zabezpieczeń usługi Azure Defender** .

1. Użyj filtru, aby znaleźć rekomendację o nazwie " **Azure Arc Enabled Kubernetes klastry powinny mieć zainstalowane rozszerzenie usługi Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center zalecenia dotyczące wdrażania rozszerzenia usługi Azure Defender dla klastrów Kubernetes z włączoną funkcją Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Zwróć uwagę na ikonę szybkiej naprawy w kolumnie akcje

1. Wybierz rozszerzenie, aby wyświetlić szczegóły dotyczące kondycji i nieprawidłowych zasobów — klastry z rozszerzeniem i bez niego.

1. Z listy zasoby o złej kondycji wybierz klaster i wybierz pozycję **Koryguj** , aby otworzyć okienko z opcjami korygowania.

1. Wybierz odpowiedni obszar roboczy Log Analytics i wybierz pozycję **Koryguj zasób x**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Wdróż rozszerzenie usługi Azure Defender dla usługi Azure ARC z opcją szybkiej naprawy Security Center.":::


### <a name="azure-cli"></a>[**Interfejs wiersza polecenia platformy Azure**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Wdrażanie rozszerzenia usługi Azure Defender przy użyciu interfejsu wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Upewnij się, że używasz tego samego identyfikatora subskrypcji, ``<your-subscription-id>`` który został użyty podczas łączenia klastra z usługą Azure Arc.

1. Uruchom następujące polecenie, aby wdrożyć rozszerzenie na szczycie klastra Kubernetes z włączoną funkcją Azure ARC:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Poniżej znajduje się opis wszystkich obsługiwanych ustawień konfiguracji w ramach typu rozszerzenia usługi Azure Defender:

    | Właściwość | Opis |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Opcjonalne**. Pełny identyfikator zasobu własnego obszaru roboczego Log Analytics.<br>Gdy nie zostanie podany, zostanie użyty domyślny obszar roboczy regionu.<br><br>Aby uzyskać pełny identyfikator zasobu, uruchom następujące polecenie, aby wyświetlić listę obszarów roboczych w subskrypcjach w domyślnym formacie JSON:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Identyfikator zasobu obszaru roboczego Log Analytics ma następującą składnię:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Dowiedz się więcej w [obszarze obszary robocze log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Opcjonalne**. Pełna ścieżka do plików dziennika inspekcji.<br>Gdy nie zostanie podany, ``/var/log/kube-apiserver/audit.log`` zostanie użyta ścieżka domyślna.<br>W przypadku aparatu AKS ścieżka standardowa to ``/var/log/kubeaudit/audit.log`` |

    Poniższe polecenie pokazuje przykładowe użycie wszystkich pól opcjonalnych:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Użyj Azure Resource Manager, aby wdrożyć rozszerzenie usługi Azure Defender

Aby użyć Azure Resource Manager do wdrożenia rozszerzenia usługi Azure Defender, musisz mieć Log Analytics obszar roboczy w ramach subskrypcji. Dowiedz się więcej w [obszarze obszary robocze log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

Możesz użyć **azure-defender-extension-arm-template.jsna** Menedżer zasobów szablon z [przykładów instalacji](https://aka.ms/kubernetes-extension-installation-examples)Security Center.

> [!TIP]
> Jeśli jesteś nowym szablonem Menedżer zasobów, Zacznij tutaj: [co to są szablony Azure Resource Manager?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**Interfejs API REST**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Wdrażanie rozszerzenia usługi Azure Defender przy użyciu interfejsu API REST 

Aby użyć interfejsu API REST do wdrożenia rozszerzenia usługi Azure Defender, musisz mieć Log Analytics obszar roboczy w ramach subskrypcji. Dowiedz się więcej w [obszarze obszary robocze log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> Najprostszym sposobem korzystania z interfejsu API w celu wdrożenia rozszerzenia usługi Azure Defender jest podano przykład **kodu JSON kolekcji Poster** z [przykładów instalacji](https://aka.ms/kubernetes-extension-installation-examples)Security Center.
- Aby zmodyfikować plik JSON kolekcji Poster lub ręcznie wdrożyć rozszerzenie za pomocą interfejsu API REST, uruchom następujące polecenie PUT:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Gdzie:

    | Nazwa            | W   | Wymagany | Typ   | Opis                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Identyfikator subskrypcji | path | True     | ciąg | Identyfikator subskrypcji zasobu Kubernetes z obsługą usługi Azure Arc |
    | Grupa zasobów  | path | True     | ciąg | Nazwa grupy zasobów zawierającej zasób Kubernetes z włączoną funkcją Azure Arc |
    | Nazwa klastra    | path | True     | ciąg | Nazwa zasobu Kubernetes z włączoną funkcją Azure Arc  |


    W przypadku **uwierzytelniania** nagłówek musi mieć token okaziciela (podobnie jak w przypadku innych interfejsów API platformy Azure). Aby uzyskać token okaziciela, uruchom następujące polecenie:

    ```az account get-access-token --subscription <your-subscription-id>``` Użyj następującej struktury treści wiadomości:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Opis właściwości znajduje się poniżej:

    | Właściwość | Opis | 
    | -------- | ----------- |
    | logAnalytics. identyfikator obszaru roboczego | Identyfikator obszaru roboczego zasobu Log Analytics |
    | logAnalytics. Key         | Klucz zasobu Log Analytics | 
    | auditLogPath             | **Opcjonalne**. Pełna ścieżka do plików dziennika inspekcji. Wartością domyślną jest ``/var/log/kube-apiserver/audit.log``. |

---

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby sprawdzić, czy w klastrze jest zainstalowane rozszerzenie usługi Azure Defender, wykonaj czynności opisane w jednej z poniższych kart:

### <a name="azure-portal---security-center"></a>[**Azure Portal — Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Użyj zalecenia Security Center, aby zweryfikować stan rozszerzenia

1. Na stronie zalecenia dotyczące programu Azure Security Center Otwórz pozycję  **Włącz kontrolę zabezpieczeń usługi Azure Defender** .

1. Wybierz zalecenie o nazwie Azure **ARC z włączonymi klastrami Kubernetes powinno mieć zainstalowane rozszerzenie usługi Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center zalecenia dotyczące wdrażania rozszerzenia usługi Azure Defender dla klastrów Kubernetes z włączoną funkcją Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Sprawdź, czy klaster, na którym wdrożono rozszerzenie, jest wymieniony jako w **dobrej kondycji**.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal — łuk systemu Azure**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Użyj stron usługi Azure ARC, aby zweryfikować stan rozszerzenia

1. W Azure Portal Otwórz pozycję **Azure Arc**.
1. Z listy infrastruktura wybierz pozycję **klastry Kubernetes** , a następnie wybierz konkretny klaster.
1. Otwórz stronę rozszerzenia. Zostaną wyświetlone rozszerzenia w klastrze. Sprawdź kolumnę **stan instalacji** , aby upewnić się, że rozszerzenie usługi Azure Defender zostało poprawnie zainstalowane.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Strona usługi Azure Arc służąca do sprawdzania stanu wszystkich zainstalowanych rozszerzeń w klastrze Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Aby uzyskać szczegółowe informacje, wybierz rozszerzenie.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Pełne szczegóły rozszerzenia usługi Azure Arc w klastrze Kubernetes.":::


### <a name="azure-cli"></a>[**Interfejs wiersza polecenia platformy Azure**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure w celu sprawdzenia, czy rozszerzenie zostało wdrożone

1. Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. W odpowiedzi poszukaj "ExtensionType": "Microsoft. azuredefender. Kubernetes" i "installState": "installed".

    > [!NOTE]
    > Może on pokazać "installState": "oczekiwanie" przez pierwsze kilka minut.
    
1. Jeśli stan jest **zainstalowany**, uruchom następujące polecenie na komputerze z `kubeconfig` plikiem wskazanym w klastrze, aby sprawdzić, czy pod nazwą "azuredefender-XXXXX" jest w stanie "uruchomiona":
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**Interfejs API REST**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Użyj interfejsu API REST, aby sprawdzić, czy rozszerzenie zostało wdrożone

Aby potwierdzić pomyślne wdrożenie lub sprawdzić stan rozszerzenia w dowolnym momencie:

1. Uruchom następujące polecenie GET:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. W odpowiedzi poszukaj w "ExtensionType": "Microsoft. azuredefender. Kubernetes" dla "installState": "installed".

    > [!TIP]
    > Może on pokazać "installState": "oczekiwanie" przez pierwsze kilka minut.
    
1. Jeśli stan jest **zainstalowany**, uruchom następujące polecenie na komputerze z `kubeconfig` plikiem wskazanym w klastrze, aby sprawdzić, czy pod nazwą "azuredefender-XXXXX" jest w stanie "uruchomiona":
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Symulowanie alertów zabezpieczeń z usługi Azure Defender dla Kubernetes

Pełna lista obsługiwanych alertów jest dostępna w [tabeli referencyjnej wszystkich alertów zabezpieczeń w Azure Security Center](alerts-reference.md#alerts-akscluster).

1. Aby zasymulować alert usługi Azure Defender, uruchom następujące polecenie:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    Oczekiwana odpowiedź to "nie znaleziono zasobów".

    W ciągu 30 minut usługa Azure Defender wykryje to działanie i wyzwoli alert zabezpieczeń.

1. Na stronie Azure Portal otwórz alerty zabezpieczeń Azure Security Center i poszukaj alertu dotyczącego odpowiedniego zasobu:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Przykładowy alert z usługi Azure Defender dla Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Usuwanie rozszerzenia usługi Azure Defender

Rozszerzenie można usunąć, korzystając z Azure Portal, interfejsu wiersza polecenia platformy Azure lub API REST, jak wyjaśniono na poniższych kartach.

### <a name="azure-portal---arc"></a>[**Azure Portal — łuk**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Użyj Azure Portal, aby usunąć rozszerzenie

1. W Azure Portal Otwórz pozycję Azure Arc.
1. Z listy infrastruktura wybierz pozycję **klastry Kubernetes** , a następnie wybierz konkretny klaster.
1. Otwórz stronę rozszerzenia. Zostaną wyświetlone rozszerzenia w klastrze.
1. Wybierz klaster i wybierz pozycję **Odinstaluj**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Usuwanie rozszerzenia z klastra Kubernetes z włączonym łukiem." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Interfejs wiersza polecenia platformy Azure**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Usuwanie rozszerzenia usługi Azure Defender przy użyciu interfejsu wiersza polecenia platformy Azure

1. Usuń rozszerzenie usługi Azure Defender for Kubernetes ARC przy użyciu następujących poleceń:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Usuwanie rozszerzenia może potrwać kilka minut. Zalecamy poczekanie przed podjęciem próby zweryfikowania, że zakończyło się pomyślnie.

1. Aby sprawdzić, czy rozszerzenie zostało pomyślnie usunięte, uruchom następujące polecenia:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    W przypadku usunięcia z Azure Resource Manager nie powinno być żadnych opóźnień. Następnie sprawdź, czy w klastrze nie ma żadnych zasobników o nazwie "azuredefender-XXXXX", uruchamiając następujące polecenie z `kubeconfig` plikiem wskazanym w klastrze: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Usunięcie z nich może potrwać kilka minut.

### <a name="rest-api"></a>[**Interfejs API REST**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Usuwanie rozszerzenia usługi Azure Defender przy użyciu interfejsu API REST 

Aby usunąć rozszerzenie przy użyciu interfejsu API REST, uruchom następujące polecenie usuwania:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Nazwa            | W   | Wymagany | Typ   | Opis                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Identyfikator subskrypcji | path | True     | ciąg | Identyfikator subskrypcji klastra Kubernetes z włączonym łukiem |
| Grupa zasobów  | path | True     | ciąg | Grupa zasobów klastra Kubernetes z włączonym łukiem  |
| Nazwa klastra    | path | True     | ciąg | Nazwa klastra z włączonym łukiem Kubernetes            |

W przypadku **uwierzytelniania** nagłówek musi mieć token okaziciela (podobnie jak w przypadku innych interfejsów API platformy Azure). Aby uzyskać token okaziciela, uruchom następujące polecenie:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Realizacja żądania może potrwać kilka minut.

---

## <a name="next-steps"></a>Następne kroki

Na tej stronie wyjaśniono, jak wdrożyć rozszerzenie usługi Azure Defender dla klastrów Kubernetes z włączoną funkcją Azure Arc. Dowiedz się więcej o funkcjach zabezpieczeń kontenerów usługi Azure Defender i Azure Security Center na następujących stronach:

- [Zabezpieczenia kontenerów w usłudze Security Center](container-security.md)
- [Wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)
- [Ochrona obciążeń Kubernetes](kubernetes-workload-protections.md)
