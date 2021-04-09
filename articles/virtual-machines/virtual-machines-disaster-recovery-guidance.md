---
title: Przerwy w działaniu usługi platformy Azure
description: Dowiedz się, co należy zrobić w przypadku przerwania działania usługi platformy Azure na maszyny wirtualne platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701037"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Co się stało z zakłóceniem działania usługi platformy Azure na maszynach wirtualnych platformy Azure

Firma Microsoft chce, aby upewnić się, że nasze usługi są zawsze dostępne dla Ciebie, gdy będą potrzebne. Siły wykraczające poza nasze kontrolki czasami wpływają na metody, które powodują nieplanowane zakłócenia usługi.

Firma Microsoft oferuje Umowa dotycząca poziomu usług (SLA) dla usług jako zobowiązanie do pracy i łączności. Umowę SLA dla poszczególnych usług platformy Azure można znaleźć na stronie [umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/).

Platforma Azure ma już wiele wbudowanych funkcji platformy, które obsługują aplikacje o wysokiej dostępności. Aby uzyskać więcej informacji na temat tych usług, Przeczytaj [odzyskiwanie po awarii i wysoką dostępność dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

W tym artykule opisano prawdziwe scenariusze odzyskiwania po awarii, gdy cały region napotyka awarię z powodu poważnych awarii lub szerokiej przerwy w działaniu usługi. Są to rzadkie wystąpienia, ale należy przygotować się na możliwość wystąpienia awarii całego regionu. Jeśli w całym regionie wystąpi zakłócenia usługi, lokalnie nadmiarowe kopie danych byłyby tymczasowo niedostępne. Jeśli włączono replikację geograficzną, trzy dodatkowe kopie obiektów blob i tabel usługi Azure Storage są przechowywane w innym regionie. W przypadku kompletnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania, platforma Azure ponownie mapuje wszystkie wpisy DNS do regionu replikowanego geograficznie.

Aby pomóc Ci w obsłudze tych rzadkich wystąpień, oferujemy następujące wskazówki dotyczące maszyn wirtualnych platformy Azure w przypadku przerwania działania usługi w całym regionie, w którym wdrożono aplikację maszyny wirtualnej platformy Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opcja 1: inicjowanie trybu failover przy użyciu Azure Site Recovery
Można skonfigurować Azure Site Recovery dla maszyn wirtualnych, aby umożliwić odzyskanie aplikacji za pomocą jednego kliknięcia w kilka minut. Możesz przeprowadzić replikację do wybranego regionu platformy Azure i nie ograniczyć się do sparowanych regionów. Możesz rozpocząć od [replikowania maszyn wirtualnych](../site-recovery/azure-to-azure-quickstart.md). Można [utworzyć plan odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md) , dzięki czemu można zautomatyzować cały proces trybu failover dla aplikacji. Przełączenie w [tryb failover można przetestować](../site-recovery/site-recovery-test-failover-to-azure.md) wcześniej bez wpływu na aplikację produkcyjną lub trwającą replikację. W przypadku przerwania działania w regionie podstawowym wystarczy [zainicjować tryb failover](../site-recovery/site-recovery-failover.md) i przenieść swoją aplikację w region docelowy.


## <a name="option-2-wait-for-recovery"></a>Opcja 2: Poczekaj na odzyskanie
W takim przypadku nie jest wymagana żadna akcja z Twojej strony. Należy pamiętać, że pracujemy z pracujemy, aby przywrócić dostępność usługi. Bieżący stan usługi można zobaczyć na naszym [pulpicie nawigacyjnym Azure Service Health](https://azure.microsoft.com/status/).

Jest to najlepsza opcja, jeśli nie skonfigurowano Azure Site Recovery, magazynu geograficznie nadmiarowego do odczytu lub magazynu geograficznie nadmiarowego przed przerwą w działaniu. Jeśli skonfigurowano magazyn Geograficznie nadmiarowy lub magazyn Geograficznie nadmiarowy z dostępem do odczytu dla konta magazynu, w którym są przechowywane wirtualne dyski twarde (VHD) maszyny wirtualnej, można odszukać plik VHD obrazu podstawowego i spróbować zainicjować dla niego nową maszynę wirtualną. Nie jest to preferowana opcja, ponieważ nie ma żadnych gwarancji synchronizacji danych. W związku z tym ta opcja nie jest gwarantowana.


> [!NOTE]
> Należy pamiętać, że nie masz żadnej kontroli nad tym procesem i nastąpi tylko w przypadku przerw w działaniu usługi. W związku z tym należy również korzystać z innych strategii tworzenia kopii zapasowych specyficznych dla aplikacji, aby osiągnąć najwyższy poziom dostępności. Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej [strategii danych dotyczących odzyskiwania po awarii](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Następne kroki

- Zacznij [chronić aplikacje działające na maszynach wirtualnych platformy Azure](../site-recovery/azure-to-azure-quickstart.md) przy użyciu Azure Site Recovery

- Aby dowiedzieć się więcej o implementowaniu strategii odzyskiwania po awarii i wysokiej dostępności, zobacz [odzyskiwanie po awarii i wysoka dostępność dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

- Aby opracować szczegółowe informacje techniczne na temat możliwości platformy w chmurze, zobacz [Wskazówki techniczne dotyczące odporności na platformie Azure](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).


- Jeśli instrukcje nie są jasne lub jeśli chcesz, aby firma Microsoft wykonał operacje w Twoim imieniu, skontaktuj się z [pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).