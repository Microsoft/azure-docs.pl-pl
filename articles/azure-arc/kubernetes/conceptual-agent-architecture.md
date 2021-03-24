---
title: Architektura agenta Kubernetes z włączoną funkcją Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera omówienie architektury agentów Kubernetes włączonych przy użyciu usługi Azure Arc
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: ec95efdfef871777e7f53617b057529e301739dd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953072"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architektura agenta Kubernetes z włączoną funkcją Azure Arc

[Kubernetes](https://kubernetes.io/) może samodzielnie wdrażać obciążenia zwirtualizowane w środowiskach hybrydowych i wielochmurowych. Usługa Azure Arc Kubernetes, jednak działa jako scentralizowana, spójna płaszczyzna kontroli, która zarządza zasadami, nadzorem i zabezpieczeniami w środowiskach heterogenicznych. Ten artykuł zawiera:

* Omówienie architektury łączącej klaster z usługą Azure Arc.
* Wzorzec łączności, po którym następuje Agent.
* Opis danych wymienianych między środowiskiem klastra i platformą Azure.

## <a name="deploy-agents-to-your-cluster"></a>Wdrażanie agentów w klastrze

Większość Premium centrów danych wymusza ścisłe reguły sieciowe uniemożliwiające komunikację przychodzącą przez zaporę granic sieci. Usługa Azure Arc Kubernetes współpracuje z tymi ograniczeniami, nie wymagając portów przychodzących na zaporze i włącza tylko selektywne punkty końcowe dla komunikacji wychodzącej. Agenci Kubernetes z włączonym usługą Azure Arc inicjują tę komunikację wychodzącą. 

![Omówienie architektury](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Łączenie klastra z usługą Azure Arc

1. Utwórz klaster Kubernetes na wybranej infrastrukturze (VMware vSphere, Amazon Web Services, Google Cloud Platform itp.). 

    > [!NOTE]
    > Ponieważ usługa Azure Arc włączona Kubernetes obecnie obsługuje tylko dołączanie istniejących klastrów Kubernetes do usługi Azure ARC, klienci są zobowiązani do tworzenia i zarządzania cyklem życia klastra Kubernetes.  

1. Rozpocznij rejestrację usługi Azure ARC dla klastra przy użyciu interfejsu wiersza polecenia platformy Azure.
    * Interfejs wiersza polecenia platformy Azure używa Helm do wdrożenia wykresu Helm agenta w klastrze.
    * Węzły klastra inicjują komunikację wychodzącą do [Container Registry firmy Microsoft](https://github.com/microsoft/containerregistry) i ściągają obrazy potrzebne do utworzenia następujących agentów w `azure-arc` przestrzeni nazw:

        | Agent | Opis |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Usługa Azure ARC z włączonym Kubernetes obecnie obsługuje tylko [tożsamości przypisane do systemu](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` Inicjuje pierwszą komunikację wychodzącą. Ta pierwsza komunikacja pobiera certyfikat tożsamość usługi zarządzanej (MSI) używany przez innych agentów do komunikacji z platformą Azure. |
        | `deployment.apps/config-agent` | Obserwuje podłączony klaster pod kątem zasobów konfiguracji kontroli źródła zastosowanych w klastrze. Aktualizuje stan zgodności. |
        | `deployment.apps/controller-manager` | Operator operatorów, który organizuje interakcje między składnikami usługi Azure Arc. |    
        | `deployment.apps/metrics-agent` | Zbiera metryki innych agentów Arc w celu sprawdzenia optymalnej wydajności. |
        | `deployment.apps/cluster-metadata-operator` | Zbiera metadane klastra, w tym wersję klastra, liczbę węzłów i wersję agenta usługi Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Synchronizuje powyższe metadane klastra z platformą Azure. |
        | `deployment.apps/flux-logs-agent` | Zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła. |
    
1. Gdy wszystkie usługi Azure ARC z włączonym modułem Agent Kubernetes są w `Running` stanie, sprawdź, czy klaster jest połączony z usługą Azure Arc. Powinny pojawić się następujące tematy:
    * Zasób Kubernetes z włączoną funkcją Azure Arc w [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Platforma Azure śledzi ten zasób jako projekcję klastra Kubernetes zarządzanego przez klienta, a nie rzeczywistego klastra Kubernetes.
    * Metadane klastra (takie jak wersja Kubernetes, wersja agenta i liczba węzłów) pojawiają się na zasobie usługi Azure ARC z włączonym zasobem Kubernetes jako metadane.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Wymiana danych między środowiskiem klastra i platformą Azure

| Typ danych | Scenariusz | Tryb komunikacji |
| --------- | -------- | ------------------ |
| Wersja klastra Kubernetes | Metadane klastra | Wypychanie agenta do platformy Azure |
| Liczba węzłów w klastrze | Metadane klastra | Wypychanie agenta do platformy Azure |
| Wersja agenta | Metadane klastra | Wypychanie agenta do platformy Azure |
| Typ dystrybucji Kubernetes | Metadane klastra | Wypychanie interfejsu wiersza polecenia platformy Azure do platformy Azure |
| Typ infrastruktury (AWS/GCP/vSphere/...) | Metadane klastra | Wypychanie interfejsu wiersza polecenia platformy Azure do platformy Azure |
| vCPU liczba węzłów w klastrze | Rozliczenia | Wypychanie interfejsu wiersza polecenia platformy Azure do platformy Azure |
| Puls agenta | Kondycja zasobów | Wypychanie agenta do platformy Azure |
| Użycie zasobów (pamięć/procesor CPU) przez agentów | Diagnostyka i obsługa techniczna | Wypychanie agenta do platformy Azure |
| Dzienniki wszystkich kontenerów agentów | Diagnostyka i obsługa techniczna | Wypychanie agenta do platformy Azure |
| Dostępność uaktualnienia agenta | Uaktualnienie agenta | Ściąganie agentów z platformy Azure |
| Żądany stan konfiguracji: adres URL repozytorium git, parametry operatora strumienia, klucz prywatny, znane hosty zawartości, nazwa użytkownika protokołu HTTPS, token lub hasło | Konfigurowanie | Ściąganie agentów z platformy Azure |
| Stan instalacji operatora strumienia | Konfigurowanie | Wypychanie agenta do platformy Azure |
| Azure Policy przypisań, które wymagają wymuszania strażnika w klastrze | Azure Policy | Ściąganie agentów z platformy Azure |
| Inspekcja i stan zgodności wymuszania zasad w klastrze | Azure Policy | Wypychanie agenta do platformy Azure |
| Metryki i dzienniki obciążeń klientów | Azure Monitor | Agent wypchnij do Log Analytics zasobu obszaru roboczego w ramach dzierżawy i subskrypcji klienta |

## <a name="connectivity-status"></a>Stan łączności

| Stan | Opis |
| ------ | ----------- |
| Łączenie | Zasób Kubernetes z włączonym Azure Arc został utworzony w Azure Resource Manager, ale usługa nie otrzymała jeszcze pulsu agenta. |
| Połączone | Usługa Kubernetes usługi Azure Arc umożliwiła odebranie pulsu agenta w ciągu ostatnich 15 minut. |
| Tryb offline | Zasób Kubernetes z włączoną funkcją Azure Arc został wcześniej połączony, ale usługa nie otrzymała pulsu agenta przez 15 minut. |
| Wygasłe | Certyfikat MSI ma przedział czasu wygaśnięcia wynoszący 90 dni od jego wystawienia. Po wygaśnięciu tego certyfikatu zasób jest brany pod uwagę, `Expired` a wszystkie funkcje, takie jak konfiguracja, monitorowanie i przetrzymywanie działania zasad w tym klastrze. Więcej informacji na temat sposobu adresowania wygasłych zasobów usługi Azure ARC z obsługą Kubernetes można znaleźć [w artykule dotyczącym często zadawanych pytań](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Informacje o trybach łączności

| Tryb łączności | Opis |
| ----------------- | ----------- |
| W pełni połączone | Agenci mogą spójnie komunikować się z platformą Azure z niewielkim opóźnieniem do propagowania konfiguracji GitOps, egzekwowania zasad Azure Policy i strażnika oraz zbierania metryk obciążeń i dzienników w Azure Monitor. |
| Częściowo połączone | Certyfikat MSI ściągnięty przez program `clusteridentityoperator` jest ważny przez maksymalnie 90 dni przed wygaśnięciem certyfikatu. Po wygaśnięciu usługa Azure Arc Kubernetes zasób nie działa. Aby ponownie uaktywnić wszystkie funkcje usługi Azure Arc w klastrze, Usuń i ponownie utwórz Kubernetes zasobów i agentów usługi Azure Arc. W ciągu 90 dni Podłącz klaster co najmniej raz na 30 dni. |
| Odłączony | Klastry Kubernetes w odłączonych środowiskach nie mogą uzyskać dostępu do platformy Azure, ponieważ nie są obecnie obsługiwane przez usługę Azure Arc Kubernetes. Jeśli ta funkcja jest interesująca, Prześlij lub Zanotuj pomysł na [forum UserVoice usługi Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z naszym przewodnikiem Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* Dowiedz się więcej na temat tworzenia połączeń między klastrem a repozytorium git jako [zasobu konfiguracji z włączonym usługą Azure Arc Kubernetes](./conceptual-configurations.md).