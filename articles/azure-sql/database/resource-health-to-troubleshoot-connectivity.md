---
title: Używanie Azure Resource Health do monitorowania kondycji bazy danych
description: Użyj Azure Resource Health do monitorowania kondycji wystąpienia zarządzanego Azure SQL Database i usługi Azure SQL, pomaga diagnozować i uzyskiwać pomoc techniczną, gdy problem z platformą Azure ma wpływ na zasoby SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 02/26/2019
ms.openlocfilehash: a51840daa2c8b19c323ad761ff6e6d1dcef15f75
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497966"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Użyj Resource Health do rozwiązywania problemów z łącznością dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL ułatwiają diagnozowanie i uzyskiwanie pomocy technicznej, gdy problem z platformą Azure ma wpływ na zasoby SQL. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Resource Health zapewnia pomoc techniczną, gdy potrzebujesz pomocy dotyczącej problemów z usługą platformy Azure.

![Omówienie](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontrole kondycji

Resource Health określa kondycję zasobu SQL, sprawdzając powodzenie i niepowodzenie logowania do zasobu. Obecnie Resource Health dla zasobu SQL Database tylko analizuje błędy logowania z powodu błędu systemu, a nie błędu użytkownika. Stan Resource Health jest aktualizowany co 1 do 2 minut.

## <a name="health-states"></a>Stany kondycji

### <a name="available"></a>Dostępne

Stan **dostępne** oznacza, że Resource Health nie wykrył niepowodzeń logowania z powodu błędów systemowych w Twoim zasobie SQL.

![Dostępne](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Obniżona wydajność

Stan **Obniżona sprawność** oznacza, że usługa Resource Health zarejestrowała w większości zakończone sukcesem próby zalogowania się, ale wystąpiły pewne błędy. Najprawdopodobniej występują błędy przejściowe logowania. Aby zmniejszyć wpływ problemów z połączeniami spowodowanych przez przejściowe błędy logowania, należy wdrożyć [logikę ponowień](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) w kodzie.

![Obniżona wydajność](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niedostępny

Stan **niedostępne** oznacza, że Resource Health wykryła spójne błędy logowania do zasobu SQL. Jeśli zasób pozostaje w tym stanie przez dłuższy czas, skontaktuj się z pomocą techniczną.

![Niedostępny](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Nieznane

Stan kondycji **nieznany** wskazuje, że Resource Health nie otrzymał informacji o tym zasobie przez więcej niż 10 minut. Mimo że ten stan nie jest ostatecznym oznaczeniem stanu zasobu, jest to ważny punkt danych w procesie rozwiązywania problemów. Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na dostępny po kilku minutach. Jeśli występują problemy z zasobem, nieznany stan kondycji może zasugerować, że zdarzenie na platformie ma wpływ na zasób.

![Nieznane](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informacje historyczne

Możesz uzyskać dostęp do 14 dni historii kondycji w sekcji Historia kondycji Resource Health. Sekcja zawiera również przyczynę przestoju (jeśli jest dostępna) dla przestojów raportowanych przez Resource Health. Obecnie platforma Azure pokazuje przestoje dla zasobu bazy danych z dokładnością do dwóch minut. W rzeczywistości przestój trwa prawdopodobnie mniej niż minutę. Średnia wynosi 8 sekund.

### <a name="downtime-reasons"></a>Przyczyny przestoju

Gdy baza danych ulegnie awarii, analiza jest przeprowadzana w celu ustalenia przyczyny. W razie potrzeby przyczyny przestoju są raportowane w sekcji Historia kondycji Resource Health. Przyczyny przestojów są zazwyczaj publikowane w ciągu 30 minut po wystąpieniu zdarzenia.

#### <a name="planned-maintenance"></a>Planowana konserwacja

Infrastruktura platformy Azure okresowo przeprowadza planowaną konserwację — uaktualnienie składników sprzętowych lub programowych w centrum danych. Podczas gdy baza danych jest w trakcie konserwacji, usługa Azure SQL może zakończyć niektóre istniejące połączenia i odrzucić nowe. Błędy logowania występujące podczas planowanej konserwacji są zwykle przejściowe i [logika ponawiania](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) pomaga zmniejszyć wpływ. Jeśli nadal występują błędy logowania, skontaktuj się z pomocą techniczną.

#### <a name="reconfiguration"></a>Ponowne konfigurowanie

Ponowne konfiguracje są uznawane za przejściowe i oczekują od czasu do czasu. Te zdarzenia mogą być wyzwalane przez równoważenie obciążenia lub awarie oprogramowania/sprzętu. Dowolna aplikacja produkcyjna klienta, która łączy się z bazą danych w chmurze, powinna implementować niezawodną [logikę ponawiania](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)połączenia, ponieważ może to pomóc w ograniczeniu tych sytuacji i ogólnie mówiąc, że błędy będą widoczne dla użytkownika końcowego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [logice ponawiania prób w przypadku błędów przejściowych](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Rozwiązywanie problemów, diagnozowanie i zapobieganie błędom połączeń SQL](troubleshoot-common-connectivity-issues.md).
- Dowiedz się więcej o [konfigurowaniu alertów Resource Health](../../service-health/resource-health-alert-arm-template-guide.md).
- Zapoznaj się z omówieniem [Resource Health](../../application-gateway/resource-health-overview.md).
- Przejrzyj [Resource Health często zadawane pytania](../../service-health/resource-health-faq.md).
