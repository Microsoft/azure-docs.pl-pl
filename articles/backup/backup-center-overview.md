---
title: Omówienie centrum kopii zapasowych
description: Ten artykuł zawiera omówienie centrum kopii zapasowych na platformie Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 7eab2e8f9694ca83d0e050f55bc6f6dd802a7331
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173730"
---
# <a name="overview-of-backup-center"></a>Omówienie centrum kopii zapasowych

Centrum kopii zapasowych zapewnia **jednolite środowisko zarządzania** na platformie Azure dla przedsiębiorstw, które ułatwiają monitorowanie, obsługę i analizowanie kopii zapasowych na dużą skalę. W związku z tym jest to zgodne z natywnymi środowiskami zarządzania platformy Azure.

Niektóre z najważniejszych zalet centrum kopii zapasowych obejmują:

* **Pojedyncze okienko szkła do zarządzania kopiami zapasowymi** — centrum kopii zapasowych jest przeznaczone do prawidłowego działania w ramach dużego i rozproszonego środowiska platformy Azure. Możesz użyć centrum usługi Backup do wydajnego zarządzania kopiami zapasowymi obejmującymi wiele typów obciążeń, magazynów, subskrypcje, regiony i dzierżawy [usługi Azure Lighthouse](../lighthouse/overview.md) .
* **Zarządzanie zorientowane na źródła danych** — centrum kopii zapasowych udostępnia widoki i filtry, które są wyśrodkowane w źródłach danych, których kopię zapasową utworzono Dzięki temu Właściciel zasobu lub administrator kopii zapasowej może monitorować i obsługiwać kopie zapasowe elementów bez potrzeby skoncentrowania się na tym magazynie, w którym jest tworzona kopia zapasowa elementu. Kluczową cechą tego projektu jest możliwość filtrowania widoków według właściwości specyficznych dla źródła danych, takich jak subskrypcja źródła danych, Grupa zasobów źródła danych i Tagi DataSource. Na przykład jeśli organizacja korzysta z rozwiązania do przypisywania różnych tagów do maszyn wirtualnych należących do różnych działów, można użyć centrum kopii zapasowych do filtrowania informacji o kopii zapasowej na podstawie tagów źródłowych maszyn wirtualnych, których kopię zapasową nie trzeba skupić na tagu magazynu.
* **Połączone środowiska** — centrum kopii zapasowych zapewnia natywne integracje z istniejącymi usługami platformy Azure, które umożliwiają zarządzanie na dużą skalę. Na przykład centrum kopii zapasowych używa środowiska [Azure Policy](../governance/policy/overview.md) , aby ułatwić zarządzanie kopiami zapasowymi. Używa również [skoroszytów platformy Azure](../azure-monitor/platform/workbooks-overview.md) i [dzienników Azure monitor](../azure-monitor/platform/data-platform-logs.md) , aby ułatwić Wyświetlanie szczegółowych raportów dotyczących kopii zapasowych. Dlatego nie musisz uczyć się żadnych nowych zasad, aby korzystać z różnych funkcji oferowanych przez centrum kopii zapasowych.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Centrum kopii zapasowych jest obecnie obsługiwane w przypadku kopii zapasowej maszyny wirtualnej platformy Azure i kopii zapasowej serwera Azure Database for PostgreSQL.
* Zapoznaj się z [matrycą pomocy technicznej](backup-center-support-matrix.md) , aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy.

## <a name="get-started"></a>Wprowadzenie

Aby rozpocząć pracę z usługą Backup Center, Wyszukaj pozycję **centrum kopii zapasowych** w Azure Portal i przejdź do pulpitu nawigacyjnego **centrum kopii zapasowych (wersja zapoznawcza)** .

![Wyszukiwanie w centrum kopii zapasowych](./media/backup-center-overview/backup-center-search.png)

Pierwszy ekran, który widzisz, to **Przegląd**. Zawiera dwa kafelki — **zadania** i **wystąpienia kopii zapasowych**.

![Kafelki centrum kopii zapasowych](./media/backup-center-overview/backup-center-overview-widgets.png)

Na kafelku **zadania** uzyskasz podsumowanie wszystkich zadań związanych z tworzeniem kopii zapasowych i przywracania, które zostały wyzwolone w ramach danej kopii zapasowej w ciągu ostatnich 24 godzin. Można wyświetlić informacje o liczbie zadań zakończonych niepowodzeniem i w toku. Wybranie dowolnej liczby z tego kafelka pozwala wyświetlić więcej informacji na temat zadań dla określonego typu źródła danych, typu operacji i stanu.

Na kafelku **wystąpienia kopii zapasowej** można uzyskać widok podsumowania wszystkich wystąpień kopii zapasowej w ramach kopii zapasowej. Można na przykład zobaczyć liczbę wystąpień kopii zapasowych, które są w stanie nieusuniętym, w porównaniu z liczbą wystąpień, które są nadal skonfigurowane do ochrony. Wybranie dowolnej liczby z tego kafelka umożliwia wyświetlenie dodatkowych informacji o wystąpieniach kopii zapasowych dla określonego typu źródła danych i stanu ochrony.

Obejrzyj poniższe wideo, aby poznać możliwości centrum kopii zapasowych:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Wykonaj [kolejne kroki](#next-steps) , aby poznać różne możliwości udostępniane przez centrum kopii zapasowych oraz jak można używać tych funkcji do wydajnego zarządzania funkcją kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i wykonywanie kopii zapasowych](backup-center-monitor-operate.md)
* [Zarządzanie brakiem kopii zapasowej](backup-center-govern-environment.md)
* [Uzyskaj wgląd w kopie zapasowe](backup-center-obtain-insights.md)
* [Wykonywanie akcji przy użyciu centrum kopii zapasowych](backup-center-actions.md)