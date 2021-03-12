---
title: Często zadawane pytania dotyczące migracji z modelu klasycznego do modelu opartego na usłudze Azure Resource Manager
description: Często zadawane pytania dotyczące migrowania z klasycznego do Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 034f09a8b616450e472dcc11427191f187cf489f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615163"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Często zadawane pytania dotyczące migracji z modelu klasycznego do modelu opartego na usłudze Azure Resource Manager

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Co to jest platforma Azure Service Manager i co to znaczy przez klasyczne?

Słowo "klasyczne" w maszynie wirtualnej IaaS (klasyczny) odnosi się do maszyn wirtualnych zarządzanych przez usługę Azure Service Manager (ASM). Azure Service Manager (ASM) to stara płaszczyzna kontroli platformy Azure odpowiedzialna za tworzenie, zarządzanie, usuwanie maszyn wirtualnych i wykonywanie innych operacji na płaszczyźnie kontroli. 

## <a name="what-is-azure-resource-manager"></a>Co to jest usługa Azure Resource Manager?

[Azure Resource Manager](../azure-resource-manager/management/overview.md) to najnowsza płaszczyzna kontroli platformy Azure odpowiedzialna za tworzenie, zarządzanie, usuwanie maszyn wirtualnych i wykonywanie innych operacji na płaszczyźnie kontroli. 

## <a name="what-is-the-time-required-for-migration"></a>Jaki jest czas wymagany do migracji?

Planowanie i wykonywanie migracji znacznie zależy od złożoności architektury i może trwać kilka miesięcy.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Jaka jest definicja nowego klienta na maszynach wirtualnych IaaS (klasyczny)?

Klienci, którzy nie zainstalowali maszyn wirtualnych IaaS (klasyczny) w swoich subskrypcjach w miesiącu lutego 2020 (miesiąc przed rozpoczęciem wycofania), są uważani za nowych klientów. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Jaka jest definicja istniejącego klienta w programie IaaS Virtual Machines (klasyczny)?

Klient, który miał aktywną lub zatrzymaną maszynę wirtualną IaaS (klasyczną) w swoich subskrypcjach w miesiącu lutego 2020, jest traktowany jako istniejący klient. Tylko Ci klienci otrzymują do 1 marca 2023 migracji swoich maszyn wirtualnych z usługi Azure Service Manager do Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Dlaczego otrzymuję komunikat o błędzie "NewClassicVMCreationNotAllowedForSubscription"?

W ramach procesu wycofywania maszyny wirtualne IaaS (klasyczne) nie są już dostępne dla nowych klientów. Firma Microsoft zidentyfikowała Ciebie jako nowych klientów, dlatego nie jest autoryzowana operacja. Zdecydowanie zalecamy korzystanie z Azure Resource Manager. Jeśli nie możesz użyć maszyn wirtualnych platformy Azure przy użyciu Azure Resource Manager, skontaktuj się z pomocą techniczną, aby dodać subskrypcję do listy dozwolonych.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Czy ten plan migracji wpływa na moje istniejące usługi lub aplikacje uruchomione na maszynach wirtualnych platformy Azure? 

Nie do 1 marca, 2023 dla maszyn wirtualnych IaaS (klasyczny). Maszyny wirtualne IaaS (klasyczne) to w pełni obsługiwane usługi ogólnie dostępne. Możesz nadal używać tych zasobów do zwiększenia Twojej obecności na platformie Microsoft Azure. 1 marca 2023 te maszyny wirtualne zostaną w pełni wycofane, a wszystkie aktywne lub przydzieloną maszyny wirtualne zostaną zatrzymane & cofania przydziału.

Nie będzie to miało wpływu na inne zasoby klasyczne, takie jak Cloud Services (klasyczne), konta magazynu (klasyczne) itp.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Co stanie się z moimi maszynami wirtualnymi, jeśli nie planuję migracji w najbliższej przyszłości? 

1 marca 2023 maszyny wirtualne IaaS (klasyczne) zostaną w pełni wycofane, a wszystkie aktywne lub przydzieloną maszyny wirtualne zostaną zatrzymane & cofania przydziału. Aby zapobiec wpływowi na działalność biznesową, należy wysoce odkomentować, aby rozpocząć planowanie migracji i zakończyć ją przed 1 marca, 2023. Istniejące klasyczne interfejsy API, Cloud Services i model zasobów nie są przestarzałe. Chcemy ułatwić migrację, biorąc pod uwagę zaawansowane funkcje, które są dostępne w modelu wdrażania usługi Resource Manager. Zalecamy rozpoczęcie planowania migracji tych zasobów do Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Co oznacza ten plan migracji dla moich istniejących narzędzi? 

Aktualizacja narzędzi do modelu wdrażania usługi Resource Manager to jedna z najważniejszych zmian, które należy uwzględnić w planach migracji.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Jak długo będzie trwał przestój płaszczyzny zarządzania? 

To zależy od liczby migrowanych zasobów. W przypadku mniejszych wdrożeń (kilkadziesiąt maszyn wirtualnych) cała migracja powinna trwać krócej niż godzinę. W przypadku wdrożeń na dużą skalę (setki maszyn wirtualnych) migracja może zająć kilka godzin.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Czy mogę wykonać wycofanie po zatwierdzeniu migrowanych zasobów w usłudze Resource Manager? 

Możesz przerwać migrację, o ile zasoby są w stanie przygotowania. Wycofanie nie jest obsługiwane po pomyślnym zmigrowaniu zasobów za pomocą operacji zatwierdzenia.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Czy mogę wycofać migrację w przypadku niepowodzenia operacji zatwierdzenia? 

Nie można przerwać migracji, jeśli operacja zatwierdzenia nie powiedzie się. Wszystkie operacje migracji, w tym operacja zatwierdzenia, są idempotentne. Dlatego zalecamy podjęcie ponownej próby wykonania operacji po chwili. Jeśli nadal wystąpi błąd, Utwórz bilet pomocy technicznej.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Czy muszę kupić kolejny obwód usługi ExpressRoute, jeśli muszę używać infrastruktury IaaS w ramach usługi Resource Manager? 

Nie. Ostatnio umożliwiliśmy [przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager](../expressroute/expressroute-move.md). Nie musisz kupować nowego obwodu usługi ExpressRoute, jeśli masz już taki.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Co zrobić, jeśli skonfigurowano zasady kontroli dostępu opartej na rolach platformy Azure dla moich klasycznych zasobów IaaS? 

Podczas migracji zasoby są przekształcane z klasycznych na zasoby usługi Resource Manager. Dlatego zalecamy zaplanowanie aktualizacji zasad RBAC platformy Azure, które muszą wystąpić po migracji.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Utworzono kopię zapasową moich klasycznych maszyn wirtualnych w magazynie. Czy mogę migrować maszyny wirtualne z trybu klasycznego do trybu usługi Resource Manager i chronić je w magazynie usługi Recovery Services?

Po przeniesieniu maszyny wirtualnej z klasycznej do Menedżer zasobów trybu kopie zapasowe wykonane przed migracją nie zostaną zmigrowane do nowo zmigrowanej Menedżer zasobów maszyny wirtualnej. Jeśli jednak chcesz zachować kopie zapasowe klasycznych maszyn wirtualnych, wykonaj następujące kroki przed migracją. 

1. W magazynie Recovery Services przejdź do bloku **elementy kopii zapasowej** i wybierz maszynę wirtualną. 
2. Kliknij przycisk Zatrzymaj kopię zapasową. W menu rozwijanym wybierz pozycję "Zachowaj dane kopii zapasowej".

> [!NOTE]
> Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej na podstawie ochrony maszyny wirtualnej. Jednak usługa Azure Backup będzie zachować kopie zapasowe punktów odzyskiwania.  Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w magazynie (zobacz [Azure Backup cennika](https://azure.microsoft.com/pricing/details/backup/) , aby uzyskać szczegółowe informacje). W razie potrzeby będzie można przywrócić maszynę wirtualną. Jeśli zdecydujesz się na wznowienie ochrony maszyny wirtualnej, możesz użyć opcji *Wznów tworzenie kopii zapasowej* .
>
>

Aby przeprowadzić migrację maszyny wirtualnej do trybu Menedżer zasobów, 

1. Usuń rozszerzenie kopii zapasowej/migawki z maszyny wirtualnej.
2. Przeprowadź migrację maszyny wirtualnej z trybu klasycznego do trybu usługi Resource Manager. Upewnij się, że informacje o magazynie i sieci odpowiadające maszynie wirtualnej również zostały zmigrowane do trybu usługi Resource Manager.

Ponadto jeśli chcesz utworzyć kopię zapasową zmigrowanej maszyny wirtualnej, przejdź do bloku zarządzanie maszynami wirtualnymi, aby [włączyć tworzenie kopii zapasowej](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Czy mogę zwalidować moją subskrypcję lub moje zasoby, aby sprawdzić, czy można je zmigrować? 

Tak. W przypadku opcji migracji obsługiwanej przez platformę pierwszym krokiem przygotowania do migracji jest zwalidowanie zasobów pod kątem możliwości ich migracji. Jeśli operacja walidacji nie powiedzie się, zostaną zgłoszone komunikaty dla wszystkich przyczyn uniemożliwiających wykonanie migracji.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Co stanie się, jeśli wystąpi błąd limitu przydziału podczas przygotowywania zasobów IaaS do migracji? 

Zalecamy przerwanie migracji, a następnie zgłoszenie żądania pomocy technicznej w celu zwiększenia limitów przydziału w regionie, w którym są migrowane maszyny wirtualne. Po zatwierdzeniu żądania dotyczącego limitu przydziału możesz ponownie rozpocząć wykonywanie kroków migracji.

## <a name="how-do-i-report-an-issue"></a>Jak mogę zgłosić problem? 

Opublikuj swoje problemy i pytania dotyczące migracji do naszej [firmy Microsoft&pytań i odpowiedzi na stronę pytania dla maszyny wirtualnej](/answers/topics/azure-virtual-machines.html)z tagiem classiciaasmigrationem słowa kluczowego. Zalecamy publikowanie wszystkich pytań na tym forum. Jeśli masz umowę pomocy technicznej, możesz także zgłosić bilet pomocy technicznej.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Co zrobić, jeśli nie podobają mi się nazwy zasobów wybrane przez platformę podczas migracji? 

Wszystkie zasoby z nazwami jawnie podanymi w klasycznym modelu wdrażania zostaną zachowane podczas migracji. W niektórych przypadkach zostaną utworzone nowe zasoby. Na przykład dla każdej maszyny wirtualnej zostanie utworzony interfejs sieciowy. Obecnie nie obsługujemy możliwości kontrolowania nazw nowych zasobów tworzonych podczas migracji. Zagłosuj na tę funkcję na [forum opinii platformy Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Czy mogę zmigrować obwody usługi ExpressRoute używane w subskrypcjach wraz z łączami autoryzacji? 

Obwodów usługi ExpressRoute używających łącz autoryzacji między subskrypcjami nie można zmigrować automatycznie bez przestoju. Udostępniamy przewodnik z opisem wykonywania takiej migracji ręcznie. Kroki i więcej informacji zawiera temat [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../expressroute/expressroute-migration-classic-resource-manager.md) (Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z modelu wdrażania klasycznego do modelu usługi Resource Manager).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Po otrzymaniu komunikatu *"maszyna wirtualna zgłasza ogólny stan agenta jako niegotowy. W związku z tym nie można migrować maszyny wirtualnej. Upewnij się, że Agent maszyny wirtualnej zgłasza ogólny stan agenta jako gotowy "* lub *" maszyna wirtualna zawiera rozszerzenie, którego stan nie jest raportowany przez maszynę wirtualną. W związku z tym nie można migrować tej maszyny wirtualnej.*

Ten komunikat jest zgłaszany, jeśli maszyna wirtualna nie ma połączenia wychodzącego do Internetu. Agent maszyny wirtualnej używa połączenia wychodzącego, aby uzyskać dostęp do konta usługi Azure Storage na potrzeby aktualizacji stanu agenta co pięć minut.


## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](./migration-classic-resource-manager-overview.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
