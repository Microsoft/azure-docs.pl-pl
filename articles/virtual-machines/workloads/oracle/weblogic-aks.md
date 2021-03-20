---
title: Co to są rozwiązania do uruchamiania programu Oracle webWebLogicc Server w usłudze Azure Kubernetes Service
description: Dowiedz się, jak uruchomić serwer Oracle WebLogic w usłudze Azure Kubernetes.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669010"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Co to są rozwiązania do uruchamiania programu Oracle webWebLogicc Server w usłudze Azure Kubernetes Service?

Na tej stronie opisano rozwiązania do uruchamiania programu Oracle webWebLogicc Server (WLS) w usłudze Azure Kubernetes Service (AKS). Te rozwiązania są wspólnie opracowywane i obsługiwane przez oprogramowanie Oracle i firmę Microsoft.

Można również uruchomić serwer WebLogic na platformie Azure Virtual Machines. Rozwiązania, które należy wykonać, zostały opisane w [tym artykule firmy Microsoft](./oracle-weblogic.md).

Serwer WebLogic to wiodący serwer aplikacji Java, na którym działają niektóre z najważniejszych aplikacji Java przedsiębiorstwa na całym świecie. Serwer WebLogic stanowi podstawę programu pośredniczącego dla pakietu oprogramowania Oracle. Firmy Oracle i Microsoft są zaangażowane w umożliwienie klientom serwerów WebLogic z możliwością wyboru i elastycznością uruchamiania obciążeń na platformie Azure jako wiodącej platformy w chmurze.

## <a name="wls-on-aks-certified-and-supported"></a>Usługi WLS w AKS Certified i obsługiwane
Serwer WebLogic jest certyfikowany przez oprogramowanie Oracle i firmę Microsoft do prawidłowego działania na AKS. Serwer WebLogic na rozwiązaniach AKS ma na celu łatwe uruchamianie kontenerów i zorganizowanych aplikacji Java w infrastrukturze Docker i Kubernetes. Rozwiązania koncentrują się na niezawodności, skalowalności, możliwości zarządzania i pomocy technicznej dla przedsiębiorstw.

Klastry serwerów WebLogic są w pełni włączone do uruchamiania na Kubernetes za pośrednictwem operatora WebLogic Kubernetes (zwanego po lewej stronie jako "operator"). Operator postępuje zgodnie ze standardowym wzorcem operatora Kubernetes. Upraszcza to zarządzanie i działanie WebLogic domen i wdrożeń na Kubernetes przez Automatyzowanie w inny sposób ręcznych zadań i Dodawanie dodatkowych funkcji niezawodności działania. Operator obsługuje funkcję Oracle webWebLogicc Server 12c, 12c i więcej niż infrastruktura oprogramowania pośredniczącego firmy Oracle. Przetestowano oficjalne obrazy platformy Docker dla WebLogic Server 12.2.1.3 i 12.2.1.4 z operatorem. Aby uzyskać szczegółowe informacje na temat operatora, zapoznaj się z [oficjalną dokumentacją firmy Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Wskazówki, skrypty i przykłady dla usługi WLS w systemie AKS
Poza certyfikowaniem serwera WebLogic na AKS, Oracle i Microsoft wspólnie udostępniają szczegółowe instrukcje, skrypty i przykłady dotyczące uruchamiania serwera WebLogic na AKS. Wskazówki są zawarte w sekcji przykładowej usługi Azure Kubernetes Service w [dokumentacji operatora](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Wskazówki dotyczą tworzenia serwera produkcyjnego WebLogic na wdrożeniach AKS jak najłatwiej. Wskazówki korzystają z oficjalnych obrazów platformy Docker WebLogic Server dostarczonych przez firmę Oracle. Tryb failover jest uzyskiwany za pośrednictwem Azure Files dostępnych za pośrednictwem trwałych oświadczeń woluminu Kubernetes. Usługi równoważenia obciążenia platformy Azure są obsługiwane w przypadku inicjowania obsługi administracyjnej przy użyciu usług Kubernetes typu "równoważenia obciążenia". Azure Container Registry (ACR) jest obsługiwana w przypadku wdrażania domen usługi WLS wewnątrz niestandardowych obrazów platformy Docker. Wskazówki umożliwiają wysoki stopień konfiguracji i dostosowywania.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Za pomocą przykładowych skryptów można wdrożyć serwer WebLogic na AKS":::

Rozwiązania te obejmują dwa sposoby wdrażania domen usługi WLS do AKS. Domeny można wdrażać bezpośrednio w woluminach trwałych Kubernetes. Ta opcja wdrażania jest dobra, jeśli chcesz przeprowadzić migrację do usługi AKS, ale nadal chcesz administrować usługą WLS przy użyciu konsoli administracyjnej lub narzędzia do obsługi skryptów WebLogic (WLST). Opcja umożliwia również przechodzenie do AKS bez przyjmowania programowania platformy Docker. Bardziej Kubernetes natywny sposób wdrażania domen usługi WLS na AKS polega na tworzeniu niestandardowych obrazów platformy Docker opartych na oficjalnych obrazach usługi WLS z programu Oracle Container Registry, opublikowaniu niestandardowych obrazów w ACR i wdrożeniu domeny do AKS przy użyciu operatora. Ta opcja w rozwiązaniu umożliwia również aktualizację domeny za pomocą Kubernetes ConfigMaps po zakończeniu wdrażania.

Dalsze łatwość użycia i integracje usług platformy Azure są dostępne w przyszłości za pośrednictwem ofert w witrynie Marketplace dublowania serwera Oracle WebLogic Server w rozwiązaniach Virtual Machines platformy Azure.

_Te rozwiązania są_ gotowe do posiadania licencji. Zakładamy, że masz już odpowiednie licencje na oprogramowanie Oracle i są prawidłowo licencjonowane do uruchamiania ofert na platformie Azure.

_Jeśli interesujesz się ścisłym działaniem scenariuszy migracji z zespołem inżynieryjnym tworzącym te rozwiązania, Wypełnij [tę krótką ankietę](https://aka.ms/wls-on-azure-survey) i Dołącz informacje kontaktowe_. Menedżery programów, architekty i inżynierowie będą wkrótce dotrzeć do Ciebie i zaczynają bliskią współpracę. Możliwość współpracy w ramach scenariusza migracji jest bezpłatna, gdy roztwory są objęte aktywnym programowaniem początkowym.

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
