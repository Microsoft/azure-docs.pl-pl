---
title: Obsługa przerw w działaniu usługi platformy Azure mających wpływ na Cloud Services platformy Azure (wersja klasyczna)
description: Dowiedz się, co należy zrobić w przypadku przerw w działaniu usługi platformy Azure mających wpływ na Cloud Services platformy Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742169"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Co zrobić w przypadku przerw w działaniu usługi platformy Azure mających wpływ na Cloud Services platformy Azure (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Firma Microsoft chce, aby upewnić się, że nasze usługi są zawsze dostępne dla Ciebie, gdy będą potrzebne. Siły wykraczające poza nasze kontrolki czasami wpływają na metody, które powodują nieplanowane zakłócenia usługi.

Firma Microsoft oferuje Umowa dotycząca poziomu usług (SLA) dla usług jako zobowiązanie do pracy i łączności. Umowę SLA dla poszczególnych usług platformy Azure można znaleźć na stronie [umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji na temat tych usług, Przeczytaj [odzyskiwanie po awarii i wysoką dostępność dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

W tym artykule opisano prawdziwe scenariusze odzyskiwania po awarii, gdy cały region napotyka awarię z powodu poważnych awarii lub szerokiej przerwy w działaniu usługi. Są to rzadkie wystąpienia, ale należy przygotować się na możliwość wystąpienia awarii całego regionu. Jeśli w całym regionie wystąpi zakłócenia usługi, lokalnie nadmiarowe kopie danych byłyby tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob i tabel usługi Azure Storage są przechowywane w innym regionie. W przypadku kompletnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania, platforma Azure ponownie mapuje wszystkie wpisy DNS do regionu replikowanego geograficznie.

> [!NOTE]
> Należy pamiętać, że nie masz żadnej kontroli nad tym procesem i wystąpi tylko w przypadku przerw w działaniu usługi w całym centrum danych. W związku z tym należy również korzystać z innych strategii tworzenia kopii zapasowych specyficznych dla aplikacji, aby osiągnąć najwyższy poziom dostępności. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i wysoka dostępność dla aplikacji utworzonych na Microsoft Azure](/azure/architecture/framework/resiliency/backup-and-recovery). Jeśli chcesz mieć możliwość wpływania na własne przejście w tryb failover, warto rozważyć użycie [magazynu geograficznie nadmiarowego do odczytu (RA-GRS)](../storage/common/storage-redundancy.md), który tworzy kopię danych tylko do odczytu w innym regionie.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opcja 1. Korzystanie z wdrożenia kopii zapasowej za pomocą usługi Azure Traffic Manager
Najbardziej niezawodne rozwiązanie odzyskiwania po awarii obejmuje konserwację wielu wdrożeń aplikacji w różnych regionach, a następnie za pomocą [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do kierowania ruchu między nimi. Usługa Azure Traffic Manager udostępnia wiele [metod routingu](../traffic-manager/traffic-manager-routing-methods.md), dzięki czemu można zdecydować, czy zarządzać wdrożeniami przy użyciu modelu podstawowego/zapasowego, czy też podzielić ruch między nimi.

![Równoważenie Cloud Services platformy Azure między regionami przy użyciu usługi Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

W celu uzyskania najszybszej reakcji na utratę regionu należy skonfigurować [monitorowanie punktu końcowego](../traffic-manager/traffic-manager-monitoring.md)Traffic Manager.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opcja 2: wdrażanie aplikacji w nowym regionie
Obsługa wielu aktywnych wdrożeń zgodnie z opisem w poprzedniej opcji wiąże się z dodatkowymi bieżącymi kosztami. Jeśli cel czasu odzyskiwania (RTO) jest wystarczająco elastyczny i masz oryginalny kod lub skompilowany pakiet Cloud Services, możesz utworzyć nowe wystąpienie aplikacji w innym regionie i zaktualizować rekordy DNS, aby wskazywały nowe wdrożenie.

Aby uzyskać szczegółowe informacje na temat sposobu tworzenia i wdrażania aplikacji usługi w chmurze, zobacz [jak utworzyć i wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).

W zależności od źródeł danych aplikacji może być konieczne sprawdzenie procedur odzyskiwania dla źródła danych aplikacji.

* W przypadku źródeł danych usługi Azure Storage zapoznaj się z opcjami [nadmiarowości usługi Azure Storage](../storage/common/storage-redundancy.md) , aby sprawdzić dostępne opcje w oparciu o wybrany model nadmiarowości dla aplikacji.
* Aby uzyskać informacje o SQL Database źródłach, przeczytaj artykuł [Omówienie: ciągłość działania w chmurze i odzyskiwanie po awarii bazy danych za pomocą SQL Database](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) , aby sprawdzić dostępne opcje w oparciu o wybrany model replikacji dla aplikacji.


## <a name="option-3-wait-for-recovery"></a>Opcja 3: Zaczekaj na odzyskanie
W takim przypadku żadna akcja nie jest wymagana, ale usługa będzie niedostępna, dopóki region nie zostanie przywrócony. Bieżący stan usługi można zobaczyć na [pulpicie nawigacyjnym Azure Service Health](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o implementowaniu strategii odzyskiwania po awarii i wysokiej dostępności, zobacz [odzyskiwanie po awarii i wysoka dostępność dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

Aby opracować szczegółowe informacje techniczne na temat możliwości platformy w chmurze, zobacz [Wskazówki techniczne dotyczące odporności na platformie Azure](/azure/architecture/checklist/resiliency-per-service).