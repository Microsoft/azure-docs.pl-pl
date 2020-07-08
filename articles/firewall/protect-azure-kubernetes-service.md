---
title: Używanie zapory platformy Azure do ochrony wdrożeń usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak chronić wdrożenia usługi Azure Kubernetes Service (AKS) przy użyciu zapory platformy Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/02/2020
ms.author: victorh
ms.openlocfilehash: 81d65954197c0ebe0de77dc2fea63239d4c3f17b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056671"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Używanie zapory platformy Azure do ochrony wdrożeń usługi Azure Kubernetes Service (AKS)

Usługa Azure Kubernetes Service (AKS) oferuje zarządzany klaster Kubernetes na platformie Azure. Zmniejsza to złożoność i koszty operacyjne związane z zarządzaniem Kubernetes przez odciążenie większości tej odpowiedzialności do platformy Azure. AKS obsługuje krytyczne zadania, takie jak monitorowanie kondycji i konserwacja, a także oferuje klaster klasy korporacyjnej i bezpieczny z ułatwieniami zarządzania.

Kubernetes organizuje klastry maszyn wirtualnych i planuje uruchamianie kontenerów na tych maszynach wirtualnych na podstawie dostępnych zasobów obliczeniowych i wymagań dotyczących zasobów dla każdego kontenera. Kontenery są pogrupowane w podstawowe jednostki operacyjne dla Kubernetes i są skalowane do żądanego stanu.

W celach zarządzania i operacyjnych węzły w klastrze AKS muszą uzyskiwać dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te akcje mogą być komunikowane się z serwerem interfejsu API lub do pobrania, a następnie zainstalowania podstawowych składników klastra Kubernetes i aktualizacji zabezpieczeń węzła. Zapora platformy Azure może pomóc w zablokowaniu środowiska i przefiltrowania ruchu wychodzącego.

Postępuj zgodnie z wytycznymi w tym artykule, aby zapewnić dodatkową ochronę klastra usługi Azure Kubernetes za pomocą zapory platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Wdrożony klaster Azure Kubernetes z uruchomioną aplikacją.

   Aby uzyskać więcej informacji, zobacz [Samouczek: Wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) i [Samouczek: uruchamianie aplikacji w usłudze Azure KUBERNETES Service (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Zabezpieczanie AKS

Zapora platformy Azure udostępnia tag AKS FQDN, aby uprościć konfigurację. Wykonaj następujące kroki, aby zezwolić na wychodzący ruch platformy AKS:

- W przypadku używania zapory platformy Azure do ograniczania ruchu wychodzącego i tworzenia trasy zdefiniowanej przez użytkownika (UDR) do kierowania całego ruchu wychodzącego upewnij się, że utworzono odpowiednią regułę DNAT w zaporze, aby prawidłowo zezwolić na ruch przychodzący. 

   Użycie zapory platformy Azure z UDR powoduje przerwanie konfiguracji ruchu przychodzącego z powodu routingu asymetrycznego. Ten problem występuje, gdy podsieć AKS ma trasę domyślną, która przechodzi do prywatnego adresu IP zapory, ale używasz publicznego modułu równoważenia obciążenia. Na przykład usługa ruchu przychodzącego lub usługi Kubernetes typu *modułu równoważenia obciążenia*.

   W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka zwrotna przechodzi przez prywatny adres IP zapory. Ze względu na to, że Zapora jest stanowa, opuszcza pakiet, ponieważ Zapora nie rozpoznaje ustanowionej sesji. Aby dowiedzieć się, jak zintegrować zaporę platformy Azure z ruchem przychodzącym lub usługą równoważenia obciążenia, zobacz [integrowanie zapory platformy Azure z usługą azure usługa Load Balancer w warstwie Standardowa](integrate-lb.md).
- Utwórz kolekcję reguł aplikacji i Dodaj regułę, aby włączyć tag *AzureKubernetesService* FQDN. Zakresem źródłowych adresów IP jest sieć wirtualna puli hostów, protokół to https, a lokalizacja docelowa to AzureKubernetesService.
- Następujące porty wychodzące/reguły sieciowe są wymagane dla klastra AKS:

   - Port TCP 443
   - TCP [*IPAddrOfYourAPIServer*]: 443 jest wymagane, jeśli masz aplikację, która musi komunikować się z serwerem interfejsu API. Tę zmianę można ustawić po utworzeniu klastra.
   - Port TCP 9000 i port UDP 1194 dla frontonu tunelu w celu komunikowania się z końcem tunelu na serwerze interfejsu API.

      Aby uzyskać bardziej szczegółowe informacje, zobacz **. HCP. <location> .. azmk8s.io* i adresy w poniższej tabeli.
   - Port UDP 123 dla synchronizacji czasu protokołu NTP (Network Time Protocol) (węzły systemu Linux).
   - Port UDP 53 dla systemu DNS jest wymagany również w przypadku bezpośredniego dostępu do serwera interfejsu API.
- Skonfiguruj Tagi usługi AzureMonitor i Storage. Azure Monitor otrzymuje dane usługi log Analytics. 

   Możesz również zezwolić na adres URL obszaru roboczego osobno: `<worksapceguid>.ods.opinsights.azure.com` , i `<worksapceguid>.oms.opinsights.azure.com` . Można to rozwiązać w jeden z następujących sposobów:

    - Zezwalaj na dostęp HTTPS z podsieci puli hostów do `*. ods.opinsights.azure.com` , i `*.oms. opinsights.azure.com` . Te symbole wieloznaczne umożliwiają dostęp, ale są mniej restrykcyjne.
    - Użyj poniższego zapytania usługi log Analytics, aby wyświetlić dokładnie wymagane nazwy FQDN, a następnie jawnie je włączyć w regułach aplikacji zapory:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o usłudze Azure Kubernetes Service, zobacz [podstawowe pojęcia dotyczące Kubernetes usługi Azure Kubernetes Service (AKS)](../aks/concepts-clusters-workloads.md).
