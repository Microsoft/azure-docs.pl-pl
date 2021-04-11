---
title: Azure Advisor PostgreSQL
description: Dowiedz się więcej o Azure Advisor zaleceniach dotyczących usługi PostgreSQL.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9d736fe77e41b80abaf76d13e918b7d51bde09c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251017"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor PostgreSQL
Dowiedz się, jak Azure Advisor ma zastosowanie do Azure Database for PostgreSQL i uzyskiwać odpowiedzi na często zadawane pytania.
## <a name="what-is-azure-advisor-for-postgresql"></a>Co to jest Azure Advisor PostgreSQL?
System Azure Advisor używa telemetrii do wystawiania zaleceń dotyczących wydajności i niezawodności dla bazy danych PostgreSQL. Zalecenia usługi Advisor są podzielone na nasze oferty bazy danych PostgreSQL:
* Azure Database for PostgreSQL — pojedynczy serwer
* Azure Database for PostgreSQL — elastyczny serwer
* Azure Database for PostgreSQL — Hiperskala (Citus)

Niektóre zalecenia są wspólne dla wielu ofert produktu, podczas gdy inne zalecenia są oparte na optymalizacjach specyficznych dla produktu.
## <a name="where-can-i-view-my-recommendations"></a>Gdzie mogę przeglądać moje rekomendacje?
Zalecenia są dostępne na pasku bocznym nawigacji **przeglądowej** w Azure Portal. Wersja zapoznawcza będzie wyświetlana jako powiadomienie banerowe, a szczegółowe informacje można wyświetlić w sekcji **powiadomienia** znajdującej się tuż poniżej wykresów użycia zasobów.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym jest wyświetlany Azure Advisor zalecenie.":::

## <a name="recommendation-types"></a>Typy rekomendacji
Azure Database for PostgreSQL ustalanie priorytetów następujących typów zaleceń:
* **Wydajność**: aby zwiększyć szybkość działania serwera PostgreSQL. Obejmuje to użycie procesora CPU, wykorzystanie pamięci, buforowanie połączeń, użycie dysku i parametry serwera specyficznego dla produktu. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące wydajności usługi Advisor](../advisor/advisor-performance-recommendations.md).
* **Niezawodność**: aby zapewnić i poprawić ciągłość baz danych o kluczowym znaczeniu dla firmy. Obejmuje to limity magazynu, limity połączeń i zalecenia dotyczące dystrybucji danych w skali. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące niezawodności usługi Advisor](../advisor/advisor-high-availability-recommendations.md).
* **Koszt**: aby zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure. Obejmuje to zalecenia dotyczące wielkości liter po prawej stronie serwera. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące kosztów usługi Advisor](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Informacje na temat zaleceń
* **Dzienny harmonogram**: usługi Azure PostgreSQL Database sprawdzają dane telemetryczne serwera i wystawiają zalecenia w codziennym harmonogramie. Jeśli wprowadzisz zmiany w konfiguracji serwera, istniejące zalecenia pozostaną widoczne do czasu ponownego sprawdzenia telemetrii w następnym dniu. 
* **Historia wydajności**: niektóre z naszych zaleceń opierają się na historii wydajności. Te zalecenia będą wyświetlane tylko wtedy, gdy serwer działa z taką samą konfiguracją przez 7 dni. Pozwala to wykrywać wzorce intensywnego użycia (np. duże działanie procesora CPU lub duże ilości połączeń) przez dłuższy czas. Jeśli zainicjujesz nowy serwer lub zmienisz konfigurację rdzeń wirtualny, te zalecenia zostaną tymczasowo wstrzymane. Zapobiega to wyzwoleniu przez starszej telemetrię zaleceń na nowo skonfigurowanym serwerze. Oznacza to jednak, że zalecenia dotyczące historii wydajności mogą nie zostać zidentyfikowane od razu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Azure Advisor przegląd](../advisor/advisor-overview.md).
