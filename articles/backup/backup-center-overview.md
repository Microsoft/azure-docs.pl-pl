---
title: Omówienie centrum kopii zapasowych
description: Ten artykuł zawiera omówienie centrum kopii zapasowych na platformie Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506058"
---
# <a name="overview-of-backup-center"></a>Omówienie centrum kopii zapasowych

Centrum kopii zapasowych zapewnia **jednolite środowisko zarządzania** na platformie Azure dla przedsiębiorstw, które ułatwiają monitorowanie, obsługę i analizowanie kopii zapasowych na dużą skalę. W związku z tym jest to zgodne z natywnymi środowiskami zarządzania platformy Azure.

Niektóre z najważniejszych zalet centrum kopii zapasowych obejmują:

* **Pojedyncze okienko szkła do zarządzania kopiami zapasowymi** — centrum kopii zapasowych jest przeznaczone do prawidłowego działania w ramach dużego i rozproszonego środowiska platformy Azure. Możesz użyć centrum usługi Backup do wydajnego zarządzania kopiami zapasowymi obejmującymi wiele typów obciążeń, magazynów, subskrypcje, regiony i dzierżawy [usługi Azure Lighthouse](../lighthouse/overview.md) .
* **Zarządzanie zorientowane na źródła danych** — centrum kopii zapasowych udostępnia widoki i filtry, które są wyśrodkowane w źródłach danych, których kopię zapasową utworzono Dzięki temu Właściciel zasobu lub administrator kopii zapasowej może monitorować i obsługiwać kopie zapasowe elementów bez potrzeby skoncentrowania się na tym magazynie, w którym jest tworzona kopia zapasowa elementu. Kluczową cechą tego projektu jest możliwość filtrowania widoków według właściwości specyficznych dla źródła danych, takich jak subskrypcja źródła danych, Grupa zasobów źródła danych i Tagi DataSource. Na przykład jeśli organizacja korzysta z rozwiązania do przypisywania różnych tagów do maszyn wirtualnych należących do różnych działów, można użyć centrum kopii zapasowych do filtrowania informacji o kopii zapasowej na podstawie tagów źródłowych maszyn wirtualnych, których kopię zapasową nie trzeba skupić na tagu magazynu.
* **Połączone środowiska** — centrum kopii zapasowych zapewnia natywne integracje z istniejącymi usługami platformy Azure, które umożliwiają zarządzanie na dużą skalę. Na przykład centrum kopii zapasowych używa środowiska [Azure Policy](../governance/policy/overview.md) , aby ułatwić zarządzanie kopiami zapasowymi. Używa również [skoroszytów platformy Azure](../azure-monitor/visualize/workbooks-overview.md) i [dzienników Azure monitor](../azure-monitor/logs/data-platform-logs.md) , aby ułatwić Wyświetlanie szczegółowych raportów dotyczących kopii zapasowych. Dlatego nie musisz uczyć się żadnych nowych zasad, aby korzystać z różnych funkcji oferowanych przez centrum kopii zapasowych. Możesz również odnajdywać zasoby społeczności z poziomu centrum kopii zapasowych.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Centrum kopii zapasowych jest obecnie obsługiwane w przypadku kopii zapasowych maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure, SAP HANA w ramach kopii zapasowej maszyny wirtualnej platformy Azure, Azure Files kopii zapasowej, tworzenia Azure Database for PostgreSQL Managed Disks kopii zapasowych Azure
* Zapoznaj się z [matrycą pomocy technicznej](backup-center-support-matrix.md) , aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć pracę z usługą Backup Center, Wyszukaj pozycję **centrum kopii zapasowych** w Azure Portal i przejdź do pulpitu nawigacyjnego **centrum kopii zapasowych** .

![Wyszukiwanie w centrum kopii zapasowych](./media/backup-center-overview/backup-center-search.png)

Pierwszy ekran, który widzisz, to **Przegląd**. Zawiera dwa kafelki — **zadania** i **wystąpienia kopii zapasowych**.

![Kafelki centrum kopii zapasowych](./media/backup-center-overview/backup-center-overview-widgets.png)

Na kafelku **zadania** uzyskasz podsumowanie wszystkich zadań związanych z tworzeniem kopii zapasowych i przywracania, które zostały wyzwolone w ramach danej kopii zapasowej w ciągu ostatnich 24 godzin. Można wyświetlić informacje o liczbie zadań zakończonych niepowodzeniem i w toku. Wybranie dowolnej liczby z tego kafelka pozwala wyświetlić więcej informacji na temat zadań dla określonego typu źródła danych, typu operacji i stanu.

Na kafelku **wystąpienia kopii zapasowej** można uzyskać widok podsumowania wszystkich wystąpień kopii zapasowej w ramach kopii zapasowej. Można na przykład zobaczyć liczbę wystąpień kopii zapasowych, które są w stanie nieusuniętym, w porównaniu z liczbą wystąpień, które są nadal skonfigurowane do ochrony. Wybranie dowolnej liczby z tego kafelka umożliwia wyświetlenie dodatkowych informacji o wystąpieniach kopii zapasowych dla określonego typu źródła danych i stanu ochrony. Możesz również wyświetlić wszystkie wystąpienia kopii zapasowej, których bazowe źródło danych nie zostało odnalezione (Źródło danych może zostać usunięte lub nie masz dostępu do źródła danych).

Obejrzyj poniższe wideo, aby poznać możliwości centrum kopii zapasowych:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Wykonaj [kolejne kroki](#next-steps) , aby poznać różne możliwości udostępniane przez centrum kopii zapasowych oraz jak można używać tych funkcji do wydajnego zarządzania funkcją kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i wykonywanie kopii zapasowych](backup-center-monitor-operate.md)
* [Zarządzanie brakiem kopii zapasowej](backup-center-govern-environment.md)
* [Uzyskaj wgląd w kopie zapasowe](backup-center-obtain-insights.md)
* [Wykonywanie akcji przy użyciu centrum kopii zapasowych](backup-center-actions.md)