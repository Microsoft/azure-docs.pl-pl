---
title: Co to są rozwiązania do uruchamiania programu Oracle webWebLogicc Server w usłudze Azure Kubernetes Service
description: Dowiedz się, jak uruchomić serwer Oracle WebLogic w usłudze Azure Kubernetes.
author: rezar
ms.service: container-service
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: c797148bdc07481cb312739ef020e88963599374
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928774"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Co to są rozwiązania do uruchamiania programu Oracle webWebLogicc Server w usłudze Azure Kubernetes Service?

Na tej stronie opisano rozwiązania do uruchamiania programu Oracle webWebLogicc Server (WLS) w usłudze Azure Kubernetes Service (AKS). Te rozwiązania są wspólnie opracowywane i obsługiwane przez oprogramowanie Oracle i firmę Microsoft.

Można również uruchomić serwer WebLogic na platformie Azure Virtual Machines. Rozwiązania, które należy wykonać, zostały opisane w [tym artykule firmy Microsoft](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

Serwer WebLogic to wiodący serwer aplikacji Java, na którym działają niektóre z najważniejszych aplikacji Java przedsiębiorstwa na całym świecie. Serwer WebLogic stanowi podstawę programu pośredniczącego dla pakietu oprogramowania Oracle. Firmy Oracle i Microsoft są zaangażowane w umożliwienie klientom serwerów WebLogic z możliwością wyboru i elastycznością uruchamiania obciążeń na platformie Azure jako wiodącej platformy w chmurze.

## <a name="wls-on-aks-certified-and-supported"></a>Usługi WLS w AKS Certified i obsługiwane
Serwer WebLogic jest certyfikowany przez oprogramowanie Oracle i firmę Microsoft do prawidłowego działania na AKS. Serwer WebLogic na rozwiązaniach AKS ma na celu łatwą możliwość uruchamiania kontenerów i zorganizowanych aplikacji Java EE w technologii Docker i Kubernetes. Rozwiązania koncentrują się na niezawodności, skalowalności, możliwości zarządzania i pomocy technicznej dla przedsiębiorstw.

Klastry serwerów WebLogic są w pełni włączone do uruchamiania na Kubernetes za pośrednictwem operatora WebLogic Kubernetes (zwanego po lewej stronie jako "operator"). Operator postępuje zgodnie ze standardowym wzorcem operatora Kubernetes. Upraszcza to zarządzanie i działanie WebLogic domen i wdrożeń na Kubernetes przez Automatyzowanie w inny sposób ręcznych zadań i Dodawanie dodatkowych funkcji niezawodności działania. Operator obsługuje funkcję Oracle webWebLogicc Server 12c, 12c i więcej niż infrastruktura oprogramowania pośredniczącego firmy Oracle. Przetestowano oficjalne obrazy platformy Docker dla WebLogic Server 12.2.1.3 i 12.2.1.4 z operatorem. Aby uzyskać szczegółowe informacje na temat operatora, zapoznaj się z [oficjalną dokumentacją firmy Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Wskazówki, skrypty i przykłady dla usługi WLS w systemie AKS
Poza certyfikowaniem serwera WebLogic na AKS, Oracle i Microsoft wspólnie udostępniają szczegółowe instrukcje, skrypty i przykłady dotyczące uruchamiania serwera WebLogic na AKS. Wskazówki są zawarte w sekcji przykładowej usługi Azure Kubernetes Service w [dokumentacji operatora](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Wskazówki dotyczą tworzenia serwera produkcyjnego WebLogic na wdrożeniach AKS jak najłatwiej. Wskazówki korzystają z oficjalnych obrazów platformy Docker WebLogic Server dostarczonych przez firmę Oracle. Tryb failover jest uzyskiwany za pośrednictwem Azure Files dostępnych za pośrednictwem trwałych oświadczeń woluminu Kubernetes. Usługi równoważenia obciążenia platformy Azure są obsługiwane w przypadku inicjowania obsługi administracyjnej przy użyciu usług Kubernetes typu "równoważenia obciążenia". Wskazówki umożliwiają wysoki stopień konfiguracji i dostosowywania.

Wskazówki obecnie zakładają wdrożenie domeny poza obrazem platformy Docker i używanie standardowych obrazów platformy Docker z programu Oracle. Dodamy wskazówki dotyczące włączania obrazów niestandardowych w domenie w ramach obrazu platformy Docker. Dalsze łatwość użycia i integracje usług platformy Azure są dostępne w przyszłości za pośrednictwem ofert w witrynie Marketplace dublowania serwera Oracle WebLogic Server w rozwiązaniach Virtual Machines platformy Azure.

_Te rozwiązania są_ gotowe do posiadania licencji. Zakładamy, że masz już odpowiednie licencje na oprogramowanie Oracle i są prawidłowo licencjonowane do uruchamiania ofert na platformie Azure.

_Jeśli interesujesz się ścisłym działaniem scenariuszy migracji z zespołem inżynieryjnym tworzącym te rozwiązania, Wypełnij [tę krótką ankietę](https://aka.ms/wls-on-azure-survey) i Dołącz informacje kontaktowe_ . Menedżery programów, architekty i inżynierowie będą wkrótce dotrzeć do Ciebie i zaczynają bliskią współpracę. Możliwość współpracy w ramach scenariusza migracji jest bezpłatna, gdy roztwory są objęte aktywnym programowaniem początkowym.

## <a name="deployment-architectures"></a>Architektury wdrażania

Rozwiązania do uruchamiania programu Oracle webWebLogicc Server w usłudze Azure Kubernetes umożliwiają szeroką gamę architektur wdrażania gotowych do użycia w środowisku produkcyjnym.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Złożone wdrożenia serwera WebLogic są włączone na AKS":::

Oprócz tego, jakie rozwiązania zapewniają klientom pełną elastyczność, aby jeszcze bardziej dostosować ich wdrożenia. Prawdopodobnie na podstawie wdrażania aplikacji klienci będą integrować dalsze zasoby platformy Azure ze swoimi wdrożeniami. Zachęcamy klientów do przekazywania opinii w ankiecie na temat dalszej poprawy rozwiązań.

## <a name="next-steps"></a>Następne kroki

Poznaj Uruchamianie programu Oracle webWebLogicc Server w usłudze Azure Kubernetes.

> [!div class="nextstepaction"]
> [Wskazówki, skrypty i przykłady dotyczące uruchamiania usługi WLS w systemie AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic — operator Kubernetes](https://oracle.github.io/weblogic-kubernetes-operator/)
