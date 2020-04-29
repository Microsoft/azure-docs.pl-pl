---
title: Azure Monitor integrację z usługą Azure Red Hat OpenShift 4,3
description: Dowiedz się, jak włączyć Azure Monitor na Microsoft Azure klastrze Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082850"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor integrację z usługą Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Pamiętaj, że usługa Azure Red Hat OpenShift 4,3 jest obecnie dostępna tylko w prywatnej wersji zapoznawczej w regionie Wschodnie stany USA. Zaakceptowanie prywatnej wersji zapoznawczej odbywa się tylko przez zaproszenie. Przed podjęciem próby włączenia tej funkcji upewnij się, że subskrypcja została zarejestrowana: [Azure Red Hat OpenShift — Rejestracja w prywatnej wersji zapoznawczej](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkcje w wersji zapoznawczej są samoobsługowe i udostępniane jako dostępne i są wykluczone z umowy dotyczącej poziomu usług (SLA) i ograniczonej rękojmi. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego.

W tym artykule opisano, jak włączyć prywatną wersję zapoznawczą Azure Monitor dla kontenerów dla klastrów OpenShift 4,3 hostowanych w Premium lub w dowolnym środowisku chmury. Te same instrukcje mają również zastosowanie do włączania monitorowania dla klastrów usługi Azure Red Hat OpenShift (ARO) 4,3.  

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Dostęp do kubeconfig klastra Kubernetes
- Dostęp do subskrypcji platformy Azure
- Dostęp do klastra OpenShift 4,3 w celu zainstalowania Azure Monitor dla kontenerów Helm wykres
- Minimalne uprawnienie roli RBAC współautor w subskrypcji platformy Azure  
- Agent monitorowania wymaga następujących portów wychodzących i domen do wysłania danych monitorowania do Azure Monitor zaplecza (jeśli jest blokowany przez serwer proxy/zaporę):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Dołączanie

> [!TIP]
> Skrypt używa funkcji bash 4, więc upewnij się, że bash jest aktualna. Bieżącą wersję można sprawdzić za pomocą programu `bash --version`.

### <a name="download-the-onboarding-script"></a>Pobierz skrypt dołączania

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Wykonaj następujący skrypt z azureSubscriptionId, regionem obszaru roboczego, ClusterName i kontekstem klastra Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Przykład:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

Domyślnie Agent monitorowania zbiera dzienniki kontenerów {stdout; stderr} wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem polecenia-system.  Jeśli chcesz skonfigurować kolekcję dzienników kontenerów specyficzną dla konkretnej przestrzeni nazw lub przestrzeni nazw, możesz zapoznać się z [konfiguracją agenta usługi Container Insights](../azure-monitor/insights/container-insights-agent-config.md). W tym miejscu można skonfigurować agenta monitorowania z pożądanymi ustawieniami zbierania danych przy użyciu mapy konfiguracji.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurowanie wycinków metryk Prometheus

Azure Monitor dla kontenerów powoduje odzyskanie metryk Prometheus i pozyskanie do Azure Monitor zaplecza. Zapoznaj się z [konfiguracją Prometheus usługi Container Insights](../azure-monitor/insights/container-insights-prometheus-integration.md) , aby uzyskać instrukcje dotyczące konfigurowania odpadków Prometheus.

Po pomyślnym dołączeniu przejdź do [monitorowania hybrydowego](https://aka.ms/azmon-containers-hybrid) i wybierz środowisko jako **"wszystkie"** , aby wyświetlić nowo dołączony klaster OpenShift v4.

## <a name="disable-monitoring"></a>Wyłączanie monitorowania

Jeśli chcesz wyłączyć monitorowanie, możesz usunąć Azure Monitor kontenerów Helm for Containers, używając następującego polecenia, aby zatrzymać zbieranie i pozyskiwanie danych monitorowania do Azure Monitor zaplecza kontenerów.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitorowanie aktualizacji

Uruchom ponownie skrypt dołączania [zgodnie z opisem w sekcji](#onboarding) dołączania z tym samym parametrem, aby zaktualizować do ostatniego wykresu Helm.

## <a name="after-successful-onboarding"></a>Po pomyślnym dołączeniu

Przejdź do [monitorowania hybrydowego](https://aka.ms/azmon-containers-hybrid)i możesz zobaczyć nowo włączony klaster OPENSHIFT/ARO v4 ze stanem kondycji na karcie **monitorowane klastry** . W tym miejscu możesz uzyskać dokładniejsze informacje, takie jak metryki, spis i dzienniki, klikając kolumnę **klaster** .

## <a name="supported-features"></a>Obsługiwane funkcje

Aby uzyskać więcej informacji na temat obsługiwanych funkcji i funkcji, zobacz [Omówienie usługi Container Insights](../azure-monitor/insights/container-insights-overview.md).

Skontaktuj się z askcoin@microsoft.com nami, aby uzyskać opinie i pytania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu, zobacz:
- [Omówienie usługi Container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Przegląd zapytania dziennika](../azure-monitor/log-query/log-query-overview.md)
