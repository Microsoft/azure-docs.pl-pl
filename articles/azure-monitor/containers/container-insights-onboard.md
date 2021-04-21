---
title: Włączanie usługi Container Insights | Microsoft Docs
description: W tym artykule opisano sposób włączania i konfigurowania usługi Container Insights w celu zrozumienia sposobu działania kontenera i zidentyfikowania problemów związanych z wydajnością.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e0544232f40e93cce0705fff6814d29697a96218
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782121"
---
# <a name="enable-container-insights"></a>Włączanie szczegółowych informacji o kontenerze

Ten artykuł zawiera omówienie dostępnych opcji konfigurowania usługi Container Insights w celu monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes i hostowanych w:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) wersji 3.x i 4.x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) w wersji 4.x  
- Klaster [Kubernetes z obsługą usługi Arc](../../azure-arc/kubernetes/overview.md)

Można również monitorować wydajność obciążeń wdrożonych w samodzielnie zarządzanych klastrach Kubernetes hostowanych w:
- Platforma Azure przy użyciu aparatu [AKS](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) lub lokalnie przy użyciu aparatu AKS.

Szczegółowe informacje o kontenerze można włączyć dla nowego wdrożenia lub dla co najmniej jednego istniejącego wdrożenia rozwiązania Kubernetes przy użyciu dowolnej z następujących obsługiwanych metod:

- Witryna Azure Portal
- Azure PowerShell
- Interfejs wiersza polecenia platformy Azure
- [Terraform i AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

> [!IMPORTANT]
> Konteneryzowany agent systemu Linux usługi Log Analytics (zasobnik zestawu replik) wykonuje wywołania interfejsu API do wszystkich węzłów systemu Windows na bezpiecznym porcie Kubelet (10250) w klastrze w celu zbierania metryk związanych z wydajnością węzłów i kontenerów. Bezpieczny port Kubelet (:10250) powinien być otwarty w sieci wirtualnej klastra, aby działała zarówno dla ruchu przychodzącego, jak i wychodzącego, aby działała kolekcja metryk związanych z wydajnością węzła systemu Windows i kontenera.
>
> Jeśli masz klaster Kubernetes z węzłami systemu Windows, przejrzyj i skonfiguruj sieciową grupę zabezpieczeń i zasady sieciowe, aby upewnić się, że bezpieczny port kubelet (:10250) jest otwarty dla ruchu przychodzącego i wychodzącego w sieci wirtualnej klastra.


- Masz obszar roboczy usługi Log Analytics.

   Usługa Container Insights obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w obszarze Produkty [dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   Obszar roboczy można utworzyć po włączeniu monitorowania dla nowego klastra usługi AKS lub umożliwić środowisko dołączania utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra usługi AKS. 
   
   Jeśli zdecydujesz się utworzyć obszar roboczy samodzielnie, możesz go utworzyć za pomocą: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [Program PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Witryna Azure Portal](../logs/quick-create-workspace.md) 
   
   Aby uzyskać listę obsługiwanych par mapowania do użycia dla domyślnego obszaru roboczego, zobacz [Mapowanie regionów dla usługi Container Insights.](container-insights-region-mapping.md)

- Jesteś członkiem grupy współautorów *usługi Log Analytics,* która umożliwia monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [Zarządzanie obszarami roboczymi.](../logs/manage-access.md)

- Jesteś członkiem grupy [ *Właściciel* w](../../role-based-access-control/built-in-roles.md#owner) zasobie klastra usługi AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Aby wyświetlić dane monitorowania, musisz mieć rolę czytelnika usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics skonfigurowaną za pomocą usługi Container Insights.

- Metryki Prometheus nie są zbierane domyślnie. Przed [skonfigurowaniem agenta](container-insights-prometheus-integration.md) do zbierania metryk należy zapoznać się z dokumentacją [prometheus,](https://prometheus.io/) aby zrozumieć, jakie dane można zebrać i jakie metody są obsługiwane.
- Klaster usługi AKS można dołączyć do obszaru roboczego usługi Log Analytics w innej subskrypcji platformy Azure w tej samej dzierżawie usługi Azure AD. Obecnie nie można tego zrobić za pomocą witryny Azure Portal, ale można to zrobić za pomocą interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Szczegółowe informacje o kontenerach oficjalnie obsługują następujące konfiguracje:

- Środowiska: Azure Red Hat OpenShift, lokalna platforma Kubernetes i aparat AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Wersje środowiska Kubernetes i zasad pomocy technicznej są takie same jak wersje obsługiwane w Azure Kubernetes Service [(AKS).](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory wymagane przez konteneryzowanego agenta do komunikowania się z usługą Container Insights. Cały ruch sieciowy z agenta jest wychodzący do Azure Monitor.

|Zasób agenta|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory dla Azure (Chiny) — 21Vianet:

|Zasób agenta|Port |Opis | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Wprowadzanie danych |
| `*.oms.opinsights.azure.cn` | 443 | Dołączanie do OMS |
| `dc.services.visualstudio.com` | 443 | W przypadku telemetrii agenta, która korzysta z usługi Azure Public Cloud Application Insights |

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory dla usługi Azure US Government:

|Zasób agenta|Port |Opis | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Wprowadzanie danych |
| `*.oms.opinsights.azure.us` | 443 | Dołączanie do OMS |
| `dc.services.visualstudio.com` | 443 | W przypadku telemetrii agenta, która korzysta z usługi Azure Public Cloud Application Insights |

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności zależy od konteneryzowanego agenta usługi Log Analytics dla systemu Linux, który został specjalnie opracowany dla usługi Container Insights. Ten wyspecjalizowany agent zbiera dane dotyczące wydajności i zdarzeń ze wszystkich węzłów w klastrze, a agent jest automatycznie wdrażany i rejestrowany w określonym obszarze roboczym usługi Log Analytics podczas wdrażania. 

Wersja agenta to microsoft/oms:ciprod04202018 lub nowsza i jest reprezentowana przez datę w następującym formacie: *mmddyyyy*.

>[!NOTE]
>Dzięki ogólnej dostępności obsługi systemu Windows Server dla usługi AKS klaster usługi AKS z węzłami systemu Windows Server ma zainstalowanego agenta w wersji zapoznawczej jako zasobnik demona w każdym węźle serwera systemu Windows w celu zbierania dzienników i przekazywania ich do usługi Log Analytics. W przypadku metryk wydajności węzeł systemu Linux, który jest automatycznie wdrażany w klastrze w ramach standardowego wdrożenia, zbiera i przekazuje dane do usługi Azure Monitor w imieniu wszystkich węzłów systemu Windows w klastrze.

Po zwolnieniu nowej wersji agenta jest on automatycznie uaktualniany w zarządzanych klastrach Kubernetes, które są hostowane w Azure Kubernetes Service (AKS). Aby śledzić, które wersje zostały wydane, zobacz [anonse dotyczące wydań agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Jeśli już wdrożono klaster usługi AKS, włączono monitorowanie przy użyciu interfejsu wiersza polecenia platformy Azure lub dostarczonego szablonu usługi Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Za pomocą programu nie `kubectl` można uaktualniać, usuwać, ponownie wdrażać ani wdrażać agenta.
>
> Szablon należy wdrożyć w tej samej grupie zasobów co klaster.

Aby włączyć usługę Container Insights, użyj jednej z metod opisanych w poniższej tabeli:

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster Kubernetes | [Tworzenie klastra usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Monitorowanie nowego klastra usługi AKS można włączyć przy użyciu interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra usługi AKS przy użyciu narzędzia Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Monitorowanie nowego klastra usługi AKS można włączyć przy użyciu narzędzia open source Terraform. |
| | [Tworzenie klastra OpenShift przy użyciu szablonu Azure Resource Manager szablonu](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Monitorowanie nowego klastra OpenShift można włączyć przy użyciu wstępnie skonfigurowanego Azure Resource Manager szablonu. |
| | [Tworzenie klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/openshift#az_openshift_create) | Monitorowanie można włączyć podczas wdrażania nowego klastra openshift przy użyciu interfejsu wiersza polecenia platformy Azure. |
| Istniejący klaster Kubernetes | [Włączanie monitorowania klastra usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Monitorowanie klastra usługi AKS, który został już wdrożony, można włączyć przy użyciu interfejsu wiersza polecenia platformy Azure. |
| |[Włączanie dla klastra AKS przy użyciu narzędzia Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Monitorowanie klastra usługi AKS, który został już wdrożony, można włączyć przy użyciu narzędzia open source Terraform. |
| | [Włączanie dla klastra AKS z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Monitorowanie można włączyć dla co najmniej jednego klastra usługi AKS, które zostały już wdrożone na stronie wielu klastrów w Azure Monitor. |
| | [Włączanie z klastra usługi AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra usługi AKS w Azure Portal. |
| | [Włączanie dla klastra usługi AKS przy użyciu Azure Resource Manager szablonu](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Monitorowanie klastra usługi AKS można włączyć przy użyciu wstępnie skonfigurowanego Azure Resource Manager szablonu. |
| | [Włączanie dla hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) | Możesz włączyć monitorowanie aparatu usługi AKS hostowanej w Azure Stack lub klastra Kubernetes, który jest hostowany lokalnie. |
| | [Włącz dla klastra Kubernetes z włączoną usługą Arc.](container-insights-enable-arc-enabled-clusters.md) | Możesz włączyć monitorowanie klastrów Kubernetes hostowanych poza platformą Azure i włączonych za pomocą Azure Arc. |
| | [Włączanie dla klastra OpenShift przy użyciu Azure Resource Manager szablonu](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Monitorowanie istniejącego klastra OpenShift można włączyć przy użyciu wstępnie skonfigurowanego Azure Resource Manager szablonu. |
| | [Włącz dla klastra OpenShift z Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Monitorowanie można włączyć dla co najmniej jednego klastra OpenShift, które zostały już wdrożone na stronie multicluster w Azure Monitor. |

## <a name="next-steps"></a>Następne kroki

Teraz, po włączeniu monitorowania, możesz rozpocząć analizowanie wydajności klastrów Kubernetes hostowanych w usługach Azure Kubernetes Service (AKS), Azure Stack lub innym środowisku. Aby dowiedzieć się, jak korzystać z usługi Container Insights, [zobacz Wyświetlanie wydajności klastra Kubernetes](container-insights-analyze.md).
