---
title: Jak włączyć Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób włączania i konfigurowania Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jak działa kontener i jakie problemy związane z wydajnością zostały zidentyfikowane.
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 0348d580a42d4a522ac05f929c96547a47e831a9
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84147906"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak włączyć Azure Monitor dla kontenerów

Ten artykuł zawiera omówienie opcji dostępnych w celu skonfigurowania Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes i hostowanych w następujący sposób:

- [Usługa Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS)

- Samozarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine).

- Samozarządzane klastry Kubernetes hostowane na [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) lub lokalnie za pomocą aparatu AKS.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) w wersji 3. x i 4. x

- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) w wersji 4. x

Azure Monitor dla kontenerów można włączyć dla nowych lub jednego lub kilku istniejących wdrożeń Kubernetes przy użyciu następujących obsługiwanych metod:

- Z Azure Portal, Azure PowerShell lub z interfejsem wiersza polecenia platformy Azure

- Korzystanie z [Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- **Obszar roboczy usługi Log Analytics.**

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Można utworzyć obszar roboczy po włączeniu monitorowania nowego klastra usługi AKS lub umożliwieniu funkcji dołączania tworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS. Jeśli wybrano opcję utworzenia jej samodzielnie, można ją utworzyć za pomocą [Azure Resource Manager](../platform/template-workspace-configuration.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md). Aby uzyskać listę obsługiwanych par mapowania używanych dla domyślnego obszaru roboczego, zobacz [Mapowanie regionów dla Azure monitor kontenerów](container-insights-region-mapping.md).

- Musisz być członkiem **roli współautor log Analytics** , aby umożliwić monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../platform/manage-access.md).

- Jesteś członkiem roli **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** w zasobie klastra AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metryki Prometheus nie są domyślnie zbierane. Przed [skonfigurowaniem agenta](container-insights-prometheus-integration.md) do zbierania danych należy zapoznać się z [dokumentacją](https://prometheus.io/) Prometheus, aby zrozumieć, jakie mogą być odpadków i obsługiwane metody.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Poniższe elementy są oficjalnie obsługiwane w przypadku kontenerów Azure Monitor.

- Środowiska: Azure Red Hat OpenShift, Kubernetes on-premises i AKS Engine na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Wersje programu Kubernetes i zasady pomocy technicznej są takie same, jak wersje programu [AKS obsługiwane](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

Informacje w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory wymagane dla agenta kontenera do komunikowania się z Azure Monitor dla kontenerów. Cały ruch sieciowy z agenta jest wychodzący do Azure Monitor.

|Zasób agenta|Porty |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 |
| dc.services.visualstudio.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

W poniższej tabeli przedstawiono informacje o konfiguracji serwera proxy i zapory dla Chin platformy Azure.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Wprowadzanie danych |
| *. oms.opinsights.azure.cn | 443 | Przechodzenie do pakietu OMS |
| dc.services.visualstudio.com | 443 | Dla programu na potrzeby telemetrii agenta przy użyciu publicznej chmury Application Insights platformy Azure. |

Informacje w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory dla instytucji rządowych USA platformy Azure.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.us | 443 | Wprowadzanie danych |
| *. oms.opinsights.azure.us | 443 | Przechodzenie do pakietu OMS |
| dc.services.visualstudio.com | 443 | W przypadku telemetrii agenta przy użyciu Application Insights publicznej chmury platformy Azure. |

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności opiera się na Log Analytics agencie dla systemu Linux opracowaną dla Azure Monitor dla kontenerów. Ten wyspecjalizowany Agent zbiera dane dotyczące wydajności i zdarzeń ze wszystkich węzłów w klastrze, a agent jest automatycznie wdrażany i rejestrowany przy użyciu określonego obszaru roboczego Log Analytics podczas wdrażania. Wersja agenta to Microsoft/OMS: ciprod04202018 lub nowsza. jest reprezentowana przez datę w następującym formacie: *mmddyyyy*.

>[!NOTE]
>W wersji zapoznawczej obsługi systemu Windows Server dla programu AKS klaster AKS z węzłami systemu Windows Server nie ma zainstalowanego agenta do zbierania danych i przesyłania dalej do Azure Monitor. Zamiast tego węzeł systemu Linux jest automatycznie wdrażany w klastrze w ramach standardowego wdrożenia zbiera dane i przekazuje je do Azure Monitor w imieniu wszystkich węzłów Windows w klastrze.  
>

Po wydaniu nowej wersji agenta zostanie ona automatycznie uaktualniona do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Aby postępować zgodnie z wydaną wersją, zobacz [anonse dotyczące wersji agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Jeśli klaster AKS został już wdrożony, należy włączyć monitorowanie za pomocą interfejsu wiersza polecenia platformy Azure lub dostarczonego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, usunięcia, ponownego wdrożenia lub wdrożenia agenta.
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Azure Monitor dla kontenerów można włączyć za pomocą jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster AKS Kubernetes | [Tworzenie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra AKS przy użyciu Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą narzędzia typu open source Terraform. |
| | [Tworzenie klastra OpenShift przy użyciu szablonu Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Można włączyć monitorowanie nowego klastra OpenShift utworzonego przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Tworzenie klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Monitorowanie można włączyć podczas wdrażania nowego klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure. |
| Istniejący klaster AKS Kubernetes | [Włącz dla klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Możesz włączyć monitorowanie klastra AKS już wdrożonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| |[Włącz dla klastra AKS przy użyciu Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Można włączyć monitorowanie klastra AKS już wdrożonego za pomocą narzędzia typu "open source" Terraform. |
| | [Włącz dla klastra AKS z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Można włączyć monitorowanie jednego lub więcej klastrów AKS już wdrożonych na stronie wielu klastrów w Azure Monitor. |
| | [Włącz z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra AKS w Azure Portal. |
| | [Włącz dla klastra AKS przy użyciu szablonu Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Można włączyć monitorowanie klastra AKS przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Włącz dla hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) | Można włączyć monitorowanie aparatu AKS hostowanego w Azure Stack lub dla Kubernetes hostowanego lokalnie. |
| | [Włącz dla klastra OpenShift przy użyciu szablonu Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Można włączyć monitorowanie istniejącego klastra OpenShift przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Włącz dla klastra OpenShift z Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Można włączyć monitorowanie jednego lub więcej klastrów OpenShift już wdrożonych na stronie wielu klastrów w Azure Monitor. |

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania można rozpocząć analizowanie wydajności klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), Azure Stack lub innym środowisku. Aby dowiedzieć się, jak używać Azure Monitor kontenerów, zobacz [Wyświetlanie wydajności klastra Kubernetes](container-insights-analyze.md).
