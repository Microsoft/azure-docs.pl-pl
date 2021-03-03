---
title: Wycofywanie maszyn wirtualnych platformy Azure (klasyczne) 1 marca 2023
description: Ten artykuł zawiera ogólne omówienie wycofania maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668198"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrowanie zasobów IaaS do Azure Resource Manager 1 marca 2023 

W systemie 2014 uruchomiono infrastrukturę jako usługę (IaaS) na [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Dodaliśmy możliwości kiedykolwiek od. Ponieważ Azure Resource Manager ma teraz pełne możliwości IaaS i inne zaawansowania, firma Microsoft zakończyła zarządzanie maszynami wirtualnymi IaaS za pośrednictwem [platformy Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) 28 lutego 2020. Ta funkcja zostanie całkowicie wycofana 1 marca 2023. 

Dzisiaj około 90 procent maszyn wirtualnych IaaS korzysta z Azure Resource Manager. Jeśli używasz zasobów IaaS za pomocą narzędzia ASM, Rozpocznij Planowanie migracji teraz. Aby skorzystać z [Azure Resource Manager](../azure-resource-manager/management/index.yml), uzupełnij je do 1 marca 2023.

Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania będą zgodne z [nowoczesnymi zasadami cyklu życia](https://support.microsoft.com/help/30881/modern-lifecycle-policy) dla wycofania.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie? 

- Od 28 lutego 2020 Klienci, którzy nie korzystali z maszyn wirtualnych IaaS za pośrednictwem ASM w miesiącu lutego 2020, nie mogą już tworzyć maszyn wirtualnych (klasycznych). 
- 1 marca 2023 klienci nie będą już mogli uruchamiać IaaS maszyn wirtualnych za pomocą ASM. Wszystkie nadal uruchomione lub przydzielono zostaną zatrzymane i cofnięte alokacje. 
- 1 marca 2023, subskrypcje, które nie zostały zmigrowane do Azure Resource Manager, będą otrzymywać informacje dotyczące osi czasu na potrzeby usuwania pozostałych maszyn wirtualnych (klasycznych).  

Wycofanie *nie ma wpływu na* następujące usługi i funkcje platformy Azure: 
- [Cloud Services platformy Azure (wersja klasyczna)](../cloud-services/cloud-services-choose-me.md)
- *Konta magazynu* nieużywane przez maszyny wirtualne (klasyczne) 
- *Sieci wirtualne* nieużywane przez maszyny wirtualne (klasyczne) 
- Inne zasoby klasyczne

## <a name="what-resources-are-available-for-this-migration"></a>Jakie zasoby są dostępne dla tej migracji?

- [Microsoft Q&a](/answers/topics/azure-virtual-machines-migration.html): Pomoc techniczna firmy Microsoft i społeczność dla migracji.

- [Obsługa migracji platformy Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): dedykowany zespół pomocy technicznej w zakresie pomocy technicznej podczas migracji. Klienci bez pomocy technicznej mogą korzystać z [bezpłatnej pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) dostępnej dla tej migracji. 

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): Funkcja Fast Track może pomóc uprawnionym klientom w planowaniu & wykonywania tej migracji. Wyznacz [siebie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) dla programu migracji kontrolera domeny.  

- Jeśli firma/organizacja ma partnerów firmy Microsoft lub współpracuje z przedstawicielami firmy Microsoft (takimi jak Architekt rozwiązań w chmurze (CSAs) lub menedżerami technicznymi (TAMs)), należy pracować z nimi w celu uzyskania dodatkowych zasobów na potrzeby migracji.

## <a name="what-actions-should-i-take"></a>Jakie akcje należy wykonać? 

Rozpocznij Planowanie migracji do Azure Resource Manager, dzisiaj. 

1. Utwórz listę wszystkich maszyn wirtualnych, których to dotyczy: 

   - Maszyny wirtualne typu **Virtual Machines (klasyczne)** w [okienku maszyny wirtualnej Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) są wszystkie maszyny wirtualne, których dotyczy ta subskrypcja. 
   - Możesz również wysyłać zapytania do grafu zasobów platformy Azure przy użyciu [portalu](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) lub [programu PowerShell](../governance/resource-graph/concepts/work-with-data.md) , aby wyświetlić listę wszystkich oflagowanych maszyn wirtualnych (klasycznych) i powiązanych informacji dotyczących wybranych subskrypcji. 
   - 8 lutego i 2 września 2020, wysłaliśmy wiadomości e-mail z tematem "Rozpocznij Planowanie migracji maszyn wirtualnych IaaS w celu Azure Resource Manager" do właścicieli subskrypcji. Wiadomość e-mail zawiera listę wszystkich subskrypcji i maszyn wirtualnych (klasycznych) maszyn wirtualnych. Użyj ich, aby skompilować tę listę. 

1. [Dowiedz się więcej](./migration-classic-resource-manager-overview.md) na temat migrowania maszyn wirtualnych z systemem [Linux](./migration-classic-resource-manager-plan.md) i [Windows](./migration-classic-resource-manager-plan.md) (klasyczny) do Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące klasycznej migracji Azure Resource Manager](./migration-classic-resource-manager-faq.md).

1. Zalecamy rozpoczęcie planowania przy użyciu [Narzędzia do migracji platformy obsługiwanej przez platformę](./migration-classic-resource-manager-overview.md) w celu migrowania istniejących maszyn wirtualnych z trzema łatwymi krokami: Weryfikuj, przygotuj i zatwierdzaj. Narzędzie jest przeznaczone do migrowania maszyn wirtualnych w minimalnym stopniu do przestoju. 

   - Pierwszy krok, weryfikowanie, nie ma wpływu na istniejące wdrożenie i zawiera listę wszystkich nieobsługiwanych scenariuszy migracji. 
   - Zapoznaj się z [listą obejść](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) , aby naprawić wdrożenie i przygotować je do migracji. 
   - Najlepiej po naprawieniu wszystkich błędów walidacji nie należy napotkać żadnych problemów podczas kroków przygotowywania i zatwierdzania. Po pomyślnym zatwierdzeniu wdrożenie jest migrowane na żywo do Azure Resource Manager i może być zarządzane za pomocą nowych interfejsów API udostępnianych przez Azure Resource Manager. 

   Jeśli narzędzie migracji nie jest odpowiednie do migracji, można zapoznać się z [innymi ofertami obliczeniowymi](/azure/architecture/guide/technology-choices/compute-decision-tree) dotyczącymi migracji. Ponieważ istnieje wiele ofert obliczeniowych platformy Azure, które różnią się od siebie, nie możemy udostępnić im ścieżki migracji obsługiwanej przez platformę.  

1. Aby uzyskać pytania techniczne, problemy i pomoc dotyczącą dodawania subskrypcji do listy dozwolonych, [skontaktuj się z pomocą techniczną](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Dokończ migrację najszybciej, jak to możliwe, aby zapobiec wpływowi na działalność biznesową i korzystać z ulepszonej wydajności, zabezpieczeń i nowych funkcji Azure Resource Manager. 
