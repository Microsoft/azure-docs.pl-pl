---
title: Omówienie centrum kopii zapasowych
description: Ten artykuł zawiera omówienie centrum kopii zapasowych na platformie Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 2857433e00a678603e30c8e5fc276020c4658f2e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614290"
---
# <a name="overview-of-backup-center"></a>Omówienie centrum kopii zapasowych

Centrum kopii zapasowych zapewnia **jednolite środowisko zarządzania** na platformie Azure dla przedsiębiorstw, które ułatwiają monitorowanie, obsługę i analizowanie kopii zapasowych na dużą skalę. W związku z tym jest to zgodne z natywnymi środowiskami zarządzania platformy Azure.

Niektóre z najważniejszych zalet centrum kopii zapasowych obejmują:

* **Pojedyncze okienko szkła do zarządzania kopiami zapasowymi** — centrum kopii zapasowych jest przeznaczone do prawidłowego działania w ramach dużego i rozproszonego środowiska platformy Azure. Możesz użyć centrum usługi Backup do wydajnego zarządzania kopiami zapasowymi obejmującymi wiele typów obciążeń, magazynów, subskrypcje, regiony i dzierżawy [usługi Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) .
* **Zarządzanie zorientowane na źródła danych** — centrum kopii zapasowych udostępnia widoki i filtry, które są wyśrodkowane w źródłach danych, których kopię zapasową utworzono Dzięki temu Właściciel zasobu lub administrator kopii zapasowej może monitorować i obsługiwać kopie zapasowe elementów bez potrzeby skoncentrowania się na tym magazynie, w którym jest tworzona kopia zapasowa elementu. Kluczową cechą tego projektu jest możliwość filtrowania widoków według właściwości specyficznych dla źródła danych, takich jak subskrypcja źródła danych, Grupa zasobów źródła danych i Tagi DataSource. Na przykład jeśli organizacja korzysta z rozwiązania do przypisywania różnych tagów do maszyn wirtualnych należących do różnych działów, można użyć centrum kopii zapasowych do filtrowania informacji o kopii zapasowej na podstawie tagów źródłowych maszyn wirtualnych, których kopię zapasową nie trzeba skupić na tagu magazynu.
* **Połączone środowiska** — centrum kopii zapasowych zapewnia natywne integracje z istniejącymi usługami platformy Azure, które umożliwiają zarządzanie na dużą skalę. Na przykład centrum kopii zapasowych używa środowiska [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) , aby ułatwić zarządzanie kopiami zapasowymi. Używa również [skoroszytów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) i [dzienników Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) , aby ułatwić Wyświetlanie szczegółowych raportów dotyczących kopii zapasowych. Dlatego nie musisz uczyć się żadnych nowych zasad, aby korzystać z różnych funkcji oferowanych przez centrum kopii zapasowych.

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
