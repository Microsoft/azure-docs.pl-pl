---
title: Architektura agenta Kubernetes z włączoną funkcją Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera omówienie architektury agentów Kubernetes włączonych przy użyciu usługi Azure Arc
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561674"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architektura agenta Kubernetes z włączoną funkcją Azure Arc

[Kubernetes](https://kubernetes.io/) można użyć do wdrożenia zwirtualizowanych obciążeń w środowiskach hybrydowych i wielochmurowych w spójny sposób. Usługa Azure ARC z włączonym Kubernetes może służyć jako scentralizowana płaszczyzna kontroli do spójnego zarządzania zasadami, nadzorem i zabezpieczeniami w tych środowiskach heterogenicznych. Ten artykuł zawiera:

* Omówienie architektury łączącej klaster z usługą Azure Arc.
* Wzorzec łączności, po którym następuje Agent.
* Opis danych wymienianych między środowiskiem klastra i platformą Azure.

## <a name="deploy-agents-to-your-cluster"></a>Wdrażanie agentów w klastrze

Większość Premium centrów danych wymusza ścisłe reguły sieciowe uniemożliwiające komunikację przychodzącą przez zaporę używaną w granicach sieci. Usługa Azure ARC z włączonym Kubernetes współpracuje z tymi ograniczeniami przez włączenie tylko selektywnych punktów końcowych dla komunikacji wychodzącej i niewymaganie żadnych portów ruchu przychodzącego w zaporze. Agenci Kubernetes z włączonym usługą Azure Arc inicjują połączenia wychodzące.

![Omówienie architektury](./media/architectural-overview.png)

Połącz klaster z usługą Azure ARC, wykonując następujące czynności:

1. Utwórz klaster Kubernetes na wybranej infrastrukturze (VMware vSphere, Amazon Web Services, Google Cloud Platform itp.). 

    > [!NOTE]
    > Klienci są zobowiązani do tworzenia i zarządzania cyklem życia klastra Kubernetes, ponieważ usługa Azure Arc Kubernetes obecnie obsługuje tylko dołączanie istniejących klastrów Kubernetes do usługi Azure Arc.  

1. Zainicjuj rejestrację usługi Azure ARC dla klastra przy użyciu interfejsu wiersza polecenia platformy Azure.
    * Interfejs wiersza polecenia platformy Azure używa Helm do wdrożenia wykresu Helm agenta w klastrze.
    * Węzły klastra inicjują komunikację wychodzącą do [Container Registry firmy Microsoft](https://github.com/microsoft/containerregistry) i ściągają obrazy potrzebne do utworzenia następujących agentów w `azure-arc` przestrzeni nazw:

        | Agent | Opis |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Usługa Azure ARC z włączonym Kubernetes obecnie obsługuje tylko [tożsamości przypisane do systemu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator dokonuje pierwszej komunikacji wychodzącej, która jest wymagana do pobrania certyfikatu tożsamości usługi zarządzanej (MSI) używanego przez innych agentów do komunikacji z platformą Azure. |
        | `deployment.apps/config-agent` | Obserwuje podłączony klaster pod kątem zasobów konfiguracji kontroli źródła zastosowanych w klastrze i aktualizuje stan zgodności |
        | `deployment.apps/controller-manager` | Operator operatorów, który organizuje interakcje między składnikami usługi Azure Arc |    
        | `deployment.apps/metrics-agent` | Zbiera metryki innych agentów ARC, aby upewnić się, że te agenci wykazują optymalną wydajność |
        | `deployment.apps/cluster-metadata-operator` | Zbiera metadane klastra — wersję klastra, liczbę węzłów i wersję agenta usługi Azure Arc |
        | `deployment.apps/resource-sync-agent` | Synchronizuje wyżej wymienione metadane klastra z platformą Azure |
        | `deployment.apps/flux-logs-agent` | Zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła |
    
1. Gdy wszystkie usługi Azure ARC z włączonym modułem Kubernetes agenta są w `Running` stanie, sprawdź, czy klaster jest połączony z usługą Azure Arc. Powinny pojawić się następujące tematy:
    * Zasób Kubernetes z włączoną funkcją Azure Arc w [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Ten zasób jest śledzony na platformie Azure jako projekcja klastra Kubernetes zarządzanego przez klienta, a nie do rzeczywistego klastra Kubernetes.
    * Metadane klastra, takie jak wersja Kubernetes, wersja agenta i liczba węzłów, są wyświetlane na zasobie usługi Azure ARC z włączonym zasobem Kubernetes jako metadane.

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
| Żądany stan konfiguracji — adres URL repozytorium git, parametry operatora strumienia, klucz prywatny, znane hosty zawartości, nazwa użytkownika protokołu HTTPS, token/hasło | Konfigurowanie | Ściąganie agentów z platformy Azure |
| Stan instalacji operatora strumienia | Konfigurowanie | Wypychanie agenta do platformy Azure |
| Azure Policy przypisań, które wymagają wymuszania strażnika w klastrze | Azure Policy | Ściąganie agentów z platformy Azure |
| Inspekcja i stan zgodności wymuszania zasad w klastrze | Azure Policy | Wypychanie agenta do platformy Azure |
| Metryki i dzienniki obciążeń klientów | Azure Monitor | Agent wypchnij do Log Analytics zasobu obszaru roboczego w ramach dzierżawy i subskrypcji klienta |

## <a name="connectivity-status"></a>Stan łączności

| Stan | Opis |
| ------ | ----------- |
| Łączenie | Zasób platformy Azure z włączoną obsługą Kubernetes został utworzony w Azure Resource Manager, ale usługa nie otrzymała jeszcze pulsu agenta. |
| Połączone | Usługa Kubernetes usługi Azure Arc umożliwiła odebranie pulsu agenta w ciągu ostatnich 15 minut. |
| Tryb offline | Zasób Kubernetes z włączoną funkcją Azure Arc został wcześniej połączony, ale usługa nie otrzymała pulsu agenta przez 15 minut. |
| Wygasłe | Certyfikat tożsamości usługi zarządzanej (MSI) ma przedział czasu wygaśnięcia wynoszący 90 dni od jego wystawienia. Po wygaśnięciu tego certyfikatu zasób jest brany pod uwagę, `Expired` a wszystkie funkcje, takie jak konfiguracja, monitorowanie i zasady, przestają działać w tym klastrze. Więcej informacji na temat sposobu adresowania wygasłych zasobów usługi Azure ARC z włączonym Kubernetes można znaleźć [tutaj](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Informacje o trybach łączności

| Tryb łączności | Opis |
| ----------------- | ----------- |
| W pełni połączone | Agenci zawsze mogą skontaktować się z platformą Azure. Środowisko pracy jest idealne w tym przypadku, ponieważ występuje niewielkie opóźnienie w propagacji konfiguracji (dla GitOps), wymuszanie zasad (w Azure Policy i strażnik) oraz zbieranie metryk i dzienników obciążeń (w Azure Monitor) |
| Częściowo połączone | Certyfikat MSI ściągnięty przez program `clusteridentityoperator` jest ważny przez 90 dni przed wygaśnięciem certyfikatu. Po wygaśnięciu certyfikatu zasób usługi Azure ARC z obsługą Kubernetes nie będzie działać. Usuń i Utwórz ponownie Kubernetes zasobów i agentów usługi Azure ARC, aby uzyskać wszystkie funkcje Arc do działania w klastrze. W ciągu 90 dni zaleca się łączenie klastra co najmniej raz na 30 dni. |
| Odłączony | Klastry Kubernetes w odłączonych środowiskach bez dostępu do platformy Azure nie są obecnie obsługiwane przez usługę Azure ARC z włączonym Kubernetes. Jeśli ta funkcja jest interesująca, Prześlij lub Zanotuj pomysł na [forum UserVoice usługi Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra z usługą Azure Arc](./connect-cluster.md)
* [Omówienie pojęć dotyczących konfiguracji](./conceptual-configurations.md)