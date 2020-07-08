---
title: Zalecenia dotyczące wydajności — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję rekomendacji dotyczących wydajności w ramach Azure Database for PostgreSQL-jednego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768473"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Zalecenia dotyczące wydajności w Azure Database for PostgreSQL-pojedynczym serwerze

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer w wersji 9,6, 10, 11

Funkcja zalecenia dotyczące wydajności analizuje bazy danych w celu utworzenia niestandardowych sugestii zwiększających wydajność. Aby utworzyć zalecenia, analiza analizuje różne cechy bazy danych, w tym schemat. Włącz [Magazyn zapytań](concepts-query-store.md) na serwerze, aby w pełni wykorzystać funkcję zaleceń dotyczących wydajności. Po wdrożeniu wszelkich zaleceń dotyczących wydajności należy przetestować wydajność, aby oszacować wpływ tych zmian. 

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Zaleceń dotyczących **wydajności** z **inteligentnej wydajności** na pasku menu na stronie Azure Portal serwera PostgreSQL.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz pozycję **Analizuj** i wybierz bazę danych, która rozpocznie analizę. W zależności od obciążenia analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje głębokie badanie bazy danych. Zalecamy przeprowadzanie analiz poza okresami szczytu. 

W oknie **rekomendacje** zostanie wyświetlona lista zaleceń, jeśli zostały znalezione.

![Zaleceń dotyczących wydajności — Nowa strona](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są automatycznie stosowane. Aby zastosować zalecenie, skopiuj tekst zapytania i uruchom go z wybranego klienta. Należy pamiętać o przetestowaniu i monitorowaniu w celu ocenienia zalecenia. 

## <a name="recommendation-types"></a>Typy rekomendacji

Obecnie obsługiwane są dwa typy rekomendacji: *create index* i *Drop index*.

### <a name="create-index-recommendations"></a>Tworzenie zaleceń dotyczących indeksów
*Tworzenie* zaleceń dotyczących indeksów Sugeruj nowe indeksy, aby przyspieszyć najczęściej wykonywane lub czasochłonne zapytania w obciążeniu. Ten typ rekomendacji wymaga włączenia [magazynu zapytań](concepts-query-store.md) . Magazyn zapytań zbiera informacje o zapytaniach i zawiera szczegółowe dane dotyczące środowiska uruchomieniowego zapytań i częstotliwości, których analiza używa do wykonania zalecenia.

### <a name="drop-index-recommendations"></a>Porzuć zalecenia dotyczące indeksów
Oprócz wykrywania brakujących indeksów Azure Database for PostgreSQL analizuje wydajność istniejących indeksów. Jeśli indeks jest rzadko używany lub nadmiarowy, Analizator zaleca jego usunięcie.

## <a name="considerations"></a>Istotne zagadnienia
* Zalecenia dotyczące wydajności nie są dostępne dla [replik odczytu](concepts-read-replicas.md).
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.

