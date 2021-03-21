---
title: Włączanie usługi Container Insights | Microsoft Docs
description: W tym artykule opisano sposób włączania i konfigurowania usługi Container Insights, dzięki czemu można zrozumieć, jak działa kontener i jakie problemy związane z wydajnością zostały zidentyfikowane.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 58797221fa3380e4f7533a710e2f8dc658cb676c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708360"
---
# <a name="enable-container-insights"></a>Włączanie usługi Container Insights

Ten artykuł zawiera omówienie opcji dostępnych w celu skonfigurowania usługi Container Insights w celu monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes i hostowanych na:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) wersje 3. x i 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) w wersji 4. x  
- [Klaster Kubernetes z włączoną funkcją Arc](../../azure-arc/kubernetes/overview.md)

Możesz również monitorować wydajność obciążeń wdrożonych w przypadku samodzielnych klastrów Kubernetes hostowanych w:
- Azure, przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) lub lokalnie, przy użyciu aparatu AKS.

Można włączyć usługi Container Insights dla nowego wdrożenia lub dla jednego lub kilku istniejących wdrożeń Kubernetes przy użyciu dowolnej z następujących obsługiwanych metod:

- Witryna Azure Portal
- Azure PowerShell
- Interfejs wiersza polecenia platformy Azure
- [Terraform i AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

> [!IMPORTANT]
> Log Analytics kontener z systemem Linux (REPLICASET pod) powoduje wywołania interfejsu API do wszystkich węzłów systemu Windows na Kubelet Secure port (10250) w klastrze w celu zebrania metryk związanych z wydajnością węzłów i kontenerów. Bezpieczny port Kubelet (: 10250) powinien zostać otwarty w sieci wirtualnej klastra dla ruchu przychodzącego i wychodzącego dla kolekcji metryk związanych z działami i kontenerami systemu Windows.
>
> Jeśli istnieje klaster Kubernetes z węzłami systemu Windows, należy przejrzeć i skonfigurować sieciową grupę zabezpieczeń i zasady sieciowe, aby upewnić się, że bezpieczny port Kubelet (: 10250) jest otwarty zarówno dla ruchu przychodzącego, jak i wychodzącego w sieci wirtualnej klastra.


- Masz obszar roboczy Log Analytics.

   Usługi Container Insights obsługują obszar roboczy Log Analytics w regionach, które są wymienione w obszarze [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   Można utworzyć obszar roboczy po włączeniu monitorowania dla nowego klastra usługi AKS lub można pozwolić, aby środowisko dołączania utworzyło domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra AKS. 
   
   Jeśli zdecydujesz się utworzyć obszar roboczy samodzielnie, możesz go utworzyć za pomocą: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [Program PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Witryna Azure Portal](../logs/quick-create-workspace.md) 
   
   Aby zapoznać się z listą obsługiwanych par mapowania do użycia w domyślnym obszarze roboczym, zobacz [Mapowanie regionów dla usługi Container Insights](container-insights-region-mapping.md).

- Musisz być członkiem grupy *współautor log Analytics* , aby włączyć monitorowanie kontenera. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../logs/manage-access.md).

- Jesteś członkiem [grupy *właścicieli*](../../role-based-access-control/built-in-roles.md#owner) w zasobie klastra AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Aby wyświetlić dane monitorowania, należy mieć rolę [*czytnika log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, skonfigurowany za pomocą usługi Container Insights.

- Metryki Prometheus nie są zbierane domyślnie. Przed [skonfigurowaniem agenta](container-insights-prometheus-integration.md) do zbierania metryk należy zapoznać się z [dokumentacją Prometheus](https://prometheus.io/) , aby zrozumieć, jakie dane mogą być odpadków i jakie metody są obsługiwane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Usługi Container Insights oficjalnie obsługują następujące konfiguracje:

- Środowiska: Azure Red Hat OpenShift, Kubernetes lokalnie i aparat AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Wersje programu Kubernetes i zasady pomocy technicznej są takie same jak te, które są [obsługiwane w usłudze Azure Kubernetes Service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory, które są wymagane dla agenta kontenera w celu komunikowania się z usługą Container Insights. Cały ruch sieciowy z agenta jest wychodzący do Azure Monitor.

|Zasób agenta|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory dla platformy Azure w Chinach:

|Zasób agenta|Port |Opis | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Wprowadzanie danych |
| `*.oms.opinsights.azure.cn` | 443 | Przechodzenie do pakietu OMS |
| `dc.services.visualstudio.com` | 443 | Dla danych telemetrycznych agenta korzystającego z chmury publicznej platformy Azure Application Insights |

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory dla instytucji rządowych USA platformy Azure:

|Zasób agenta|Port |Opis | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Wprowadzanie danych |
| `*.oms.opinsights.azure.us` | 443 | Przechodzenie do pakietu OMS |
| `dc.services.visualstudio.com` | 443 | Dla danych telemetrycznych agenta korzystającego z chmury publicznej platformy Azure Application Insights |

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności polega na zaLog Analyticsym agencie agenta dla systemu Linux, który został zaprojektowany z myślą o usłudze Container Insights. Ten wyspecjalizowany Agent zbiera dane dotyczące wydajności i zdarzeń ze wszystkich węzłów w klastrze, a agent jest automatycznie wdrażany i rejestrowany przy użyciu określonego obszaru roboczego Log Analytics podczas wdrażania. 

Wersja agenta to Microsoft/OMS: ciprod04202018 lub nowsza. jest reprezentowana przez datę w następującym formacie: *mmddyyyy*.

>[!NOTE]
>Ogólnie dostęp do obsługi systemu Windows Server dla programu AKS, klaster AKS z węzłami systemu Windows Server ma agenta wersji zapoznawczej zainstalowanego jako elementu daemonset pod każdym węzłem systemu Windows Server do zbierania dzienników i przekazywania go do Log Analytics. W przypadku metryk wydajności węzeł systemu Linux, który jest automatycznie wdrażany w klastrze w ramach wdrożenia standardowego, zbiera dane i przekazuje je do Azure Monitor w imieniu wszystkich węzłów Windows w klastrze.

Po wydaniu nowej wersji agenta zostanie ona automatycznie uaktualniona do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Aby śledzić, które wersje są wydane, zobacz [anonse dotyczące wersji agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Jeśli klaster AKS został już wdrożony, włączono monitorowanie przy użyciu interfejsu wiersza polecenia platformy Azure lub dostarczonego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` , aby uaktualnić, usunąć, ponownie wdrożyć lub wdrożyć agenta.
>
> Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Aby włączyć usługi Container Insights, użyj jednej z metod opisanych w poniższej tabeli:

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster Kubernetes | [Tworzenie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Możesz włączyć monitorowanie dla nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra AKS za pomocą Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Możesz włączyć monitorowanie dla nowego klastra AKS utworzonego za pomocą narzędzia typu open source Terraform. |
| | [Tworzenie klastra OpenShift przy użyciu szablonu Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Można włączyć monitorowanie dla nowego klastra OpenShift utworzonego przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Tworzenie klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/openshift#az-openshift-create) | Monitorowanie można włączyć podczas wdrażania nowego klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure. |
| Istniejący klaster Kubernetes | [Włączanie monitorowania klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Można włączyć monitorowanie klastra AKS, który został już wdrożony za pomocą interfejsu wiersza polecenia platformy Azure. |
| |[Włącz dla klastra AKS przy użyciu Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Można włączyć monitorowanie klastra AKS, który jest już wdrożony za pomocą narzędzia Open Source Terraform. |
| | [Włącz dla klastra AKS z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Można włączyć monitorowanie dla co najmniej jednego klastra AKS, który został już wdrożony na stronie wielu klastrów w Azure Monitor. |
| | [Włącz z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra AKS w Azure Portal. |
| | [Włącz dla klastra AKS przy użyciu szablonu Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Monitorowanie klastra AKS można włączyć przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Włącz dla hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) | Można włączyć monitorowanie aparatu AKS hostowanego w Azure Stack lub dla klastra Kubernetes hostowanego lokalnie. |
| | [Włącz dla klastra Kubernetes z włączonym łukiem](container-insights-enable-arc-enabled-clusters.md). | Możesz włączyć monitorowanie klastrów Kubernetes hostowanych poza platformą Azure i włączonych przy użyciu usługi Azure Arc. |
| | [Włącz dla klastra OpenShift przy użyciu szablonu Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Można włączyć monitorowanie istniejącego klastra OpenShift przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Włącz dla klastra OpenShift z Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Można włączyć monitorowanie dla co najmniej jednego klastra OpenShift, który jest już wdrożony na stronie wieloklastrowej w Azure Monitor. |

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania można rozpocząć analizowanie wydajności klastrów Kubernetes, które są hostowane w usłudze Azure Kubernetes Service (AKS), Azure Stack lub innym środowisku. Aby dowiedzieć się, jak korzystać z usługi Container Insights, zobacz [Wyświetlanie wydajności klastra Kubernetes](container-insights-analyze.md).