---
title: Zarządzanie platformą Azure — omówienie — zarządzanie platformą Azure
description: Przegląd obszarów zarządzania dla aplikacji i zasobów platformy Azure z linkami do zawartości w narzędziach zarządzania platformy Azure.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75980896"
---
# <a name="overview-of-management-services-in-azure"></a>Przegląd usług zarządzania na platformie Azure

Zarządzanie na platformie Azure to jeden aspekt zarządzania platformą Azure. W tym artykule opisano różne obszary zarządzania dotyczące wdrażania i utrzymywania zasobów na platformie Azure.

Zarządzanie dotyczy zadań i procesów wymaganych do obsługi aplikacji biznesowych i zasobów, które je obsługują. Platforma Azure oferuje wiele usług i narzędzi, które współpracują ze sobą w celu zapewnienia pełnego zarządzania. Te usługi nie dotyczą tylko zasobów platformy Azure, ale również w innych chmurach i lokalnych. Zrozumienie różnych narzędzi i ich współdziałania to pierwszy krok projektowania kompletnego środowiska zarządzania.

Poniższy diagram przedstawia różne obszary zarządzania wymagane do obsługi dowolnej aplikacji lub zasobu. Te różne obszary można traktować jako cykl życia. Każdy obszar jest wymagany w ciągłym pomyślnym przeniesieniu na cykl życia zasobów. Cykl życia zasobów rozpoczyna się od wstępnego wdrożenia, przez kontynuowanie operacji i ostatecznie po wycofaniu.

![Dyscypliny zarządzania na platformie Azure](../monitoring/media/management-overview/management-capabilities.png)

Żadna pojedyncza usługa platformy Azure nie wypełnia wymagań określonego obszaru zarządzania. Zamiast tego każdy jest realizowany przez kilka współpracujących ze sobą usług. Niektóre usługi, takie jak Application Insights, zapewniają funkcje monitorowania dla aplikacji sieci Web. Inne, takie jak dzienniki Azure Monitor, przechowują dane zarządzania dla innych usług. Ta funkcja umożliwia analizowanie danych różnych typów zbieranych przez różne usługi.

W poniższych sekcjach krótko opisano różne obszary zarządzania oraz podano linki do szczegółowych informacji o głównych usługach platformy Azure dotyczących tych obszarów.

## <a name="monitor"></a>Monitorowanie

Monitorowanie to czynność zbierania i analizowania danych w celu przeprowadzenia inspekcji wydajności, kondycji i dostępności zasobów. Skuteczna strategia monitorowania pomaga zrozumieć działanie składników i zwiększyć czas pracy przy użyciu powiadomień. Zapoznaj się z omówieniem monitorowania, który obejmuje różne usługi używane do [monitorowania aplikacji i zasobów platformy Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurowanie

Konfiguracja dotyczy początkowego wdrożenia i konfiguracji zasobów oraz trwającej konserwacji.
Automatyzacja tych zadań pozwala wyeliminować nadmiarowość, zminimalizować czas i nakład pracy oraz zwiększyć dokładność i wydajność. [Usługa Azure Automation](../automation/automation-intro.md) dostarcza większość usług do automatyzowania zadań konfiguracji. Chociaż elementy Runbook obsługują automatyzację procesów, konfigurację i zarządzanie aktualizacjami ułatwia zarządzanie konfiguracją.

## <a name="govern"></a>Ład

Ład dotyczy mechanizmów i procesów kontrolowania aplikacji i zasobów na platformie Azure. Obejmuje to planowanie inicjatyw i wyznaczanie strategicznych priorytetów.
Ład na platformie Azure jest zaimplementowany głównie za pomocą dwóch usług. [Azure Policy](./policy/overview.md) umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w celu wymuszania reguł dla zasobów. Ta funkcja zapewnia zgodność tych zasobów ze standardami firmowymi. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) umożliwia śledzenie użycia i wydatków w chmurze dla zasobów platformy Azure i innych dostawców chmury.

## <a name="secure"></a>Bezpieczeństwo

Zarządzanie zabezpieczeniami zasobów i danych. Program zabezpieczeń obejmuje ocenianie zagrożeń, zbieranie i analizowanie danych oraz zgodność aplikacji i zasobów. Monitorowanie zabezpieczeń i analiza zagrożeń są udostępniane przez [Azure Security Center](../security-center/security-center-intro.md), w tym ujednolicone Zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Aby uzyskać wyczerpujące informacje i wskazówki dotyczące zabezpieczania zasobów platformy Azure, zobacz [wprowadzenie do zabezpieczeń platformy Azure](../security/fundamentals/overview.md) .

## <a name="protect"></a>Ochrona

Ochrona dotyczy utrzymywania dostępu do aplikacji i danych, nawet w przypadku awarii, które wykraczają poza swoją kontrolę. Ochrona na platformie Azure jest zapewniana przez dwie usługi. [Usługa Azure Backup](../backup/backup-introduction-to-azure-backup.md) oferuje tworzenie kopii zapasowych i odzyskiwanie danych w chmurze lub lokalnie. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zapewnia ciągłość działania i natychmiastowe odzyskiwanie podczas awarii.

## <a name="migrate"></a>Migrate (Migracja)

Migracja dotyczy przenoszenia obciążeń działających obecnie lokalnie do chmury platformy Azure.
[Azure Migrate](../migrate/migrate-overview.md) to usługa, która ułatwia ocenę przydatności do migracji lokalnych maszyn wirtualnych na platformę Azure. Azure Site Recovery migruje maszyny wirtualne [z lokalizacji lokalnej](../site-recovery/migrate-tutorial-on-premises-azure.md) lub [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migracja bazy danych platformy Azure](../dms/dms-overview.md) ułatwia Migrowanie źródeł baz danych do platform danych platformy Azure.
