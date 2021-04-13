---
title: Azure Advisor for MariaDB
description: Dowiedz się więcej Azure Advisor zaleceń dotyczących bazy danych MariaDB.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368652"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor for MariaDB
Dowiedz się, Azure Advisor są stosowane do Azure Database for MariaDB i uzyskaj odpowiedzi na często zadawane pytania.
## <a name="what-is-azure-advisor-for-mariadb"></a>Co to jest Azure Advisor mariaDB?
System Azure Advisor korzysta z telemetrii do wydawania zaleceń dotyczących wydajności i niezawodności bazy danych MariaDB. 

Niektóre rekomendacje są wspólne dla wielu ofert produktów, a inne są oparte na optymalizacjach specyficznych dla produktu.
## <a name="where-can-i-view-my-recommendations"></a>Gdzie mogę wyświetlić moje rekomendacje?
Zalecenia są dostępne na **pasku** bocznym nawigacji Przegląd w Azure Portal. Podgląd zostanie wyświetlony jako powiadomienie na banerze, a  szczegóły można wyświetlić w sekcji Powiadomienia znajdującej się tuż poniżej wykresów użycia zasobów.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Zrzut ekranu przedstawiający Azure Portal zalecenia Azure Advisor aplikacji.":::

## <a name="recommendation-types"></a>Typy rekomendacji
Azure Database for MariaDB priorytetów dla następujących typów zaleceń:
* **Wydajność:** w celu zwiększenia szybkości serwera MariaDB. Obejmuje to użycie procesora CPU, wykorzystanie pamięci, wykorzystanie dysku i parametry serwera specyficzne dla produktu. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące wydajności usługi Advisor.](../advisor/advisor-performance-recommendations.md)
* **Niezawodność:** aby zapewnić i poprawić ciągłość baz danych o krytycznym znaczeniu dla firmy. Obejmuje to limit magazynu i zalecenia dotyczące limitu połączeń. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące niezawodności usługi Advisor](../advisor/advisor-high-availability-recommendations.md).
* **Koszt:** Aby zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure. Obejmuje to rekomendacje dotyczące zmiany rozmiaru serwera. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące kosztów usługi Advisor.](../advisor/advisor-cost-recommendations.md)

## <a name="understanding-your-recommendations"></a>Opis zaleceń
* **Harmonogram dzienny:** w przypadku baz danych Azure MariaDB sprawdzamy dane telemetryczne serwera i wydajemy zalecenia zgodnie z harmonogramem dziennym. Jeśli zmienisz konfigurację serwera, istniejące rekomendacje pozostaną widoczne do momentu ponownego zbadania telemetrii następnego dnia. 
* **Historia wydajności:** Niektóre z naszych zaleceń są oparte na historii wydajności. Te zalecenia będą wyświetlane tylko po tym, jak serwer działa z taką samą konfiguracją przez 7 dni. Dzięki temu możemy wykrywać wzorce intensywnego użycia (np. dużej aktywności procesora CPU lub dużej liczby połączeń) w dłuższym okresie. Jeśli aprowizujesz nowy serwer lub zmieniasz nową konfigurację rdzeni wirtualnych, te zalecenia zostaną tymczasowo wstrzymane. Zapobiega to wyzwalaniu rekomendacji na nowo skonfigurowanym serwerze przez starsze dane telemetryczne. Jednak oznacza to również, że zalecenia oparte na historii wydajności mogą nie zostać zidentyfikowane natychmiast.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, [zobacz Azure Advisor Omówienie.](../advisor/advisor-overview.md)
