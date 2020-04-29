---
title: Zalecenia dotyczące wydajności — Azure Database for MariaDB
description: W tym artykule opisano funkcję rekomendacji wydajności w Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528102"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Zalecenia dotyczące wydajności w usłudze Azure Database for MariaDB

**Dotyczy:** Azure Database for MariaDB 10,2

Funkcja zalecenia dotyczące wydajności analizuje bazy danych w celu utworzenia niestandardowych sugestii zwiększających wydajność. Aby utworzyć zalecenia, analiza analizuje różne cechy bazy danych, w tym schemat. Włącz [Magazyn zapytań](concepts-query-store.md) na serwerze, aby w pełni wykorzystać funkcję zaleceń dotyczących wydajności. Jeśli schemat wydajności jest wyłączony, włączenie magazynu zapytań umożliwia performance_schema i podzbiór instrumentów schematu wydajności wymaganych przez funkcję. Po wdrożeniu wszelkich zaleceń dotyczących wydajności należy przetestować wydajność, aby oszacować wpływ tych zmian.

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Zaleceń dotyczących **wydajności** z **inteligentnej wydajności** na pasku menu na stronie Azure Portal serwera MariaDB.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz pozycję **Analizuj** i wybierz bazę danych, która rozpocznie analizę. W zależności od obciążenia analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje głębokie badanie bazy danych. Zalecamy przeprowadzanie analiz poza okresami szczytu.

W oknie **rekomendacje** zostanie wyświetlona lista zaleceń, jeśli zostały znalezione i powiązanego identyfikatora zapytania, które wygenerowało to zalecenie. Korzystając z identyfikatora zapytania, można dowiedzieć się więcej o zapytaniu za pomocą widoku [MySQL. query_store](concepts-query-store.md#mysqlquery_store) .

![Zaleceń dotyczących wydajności — Nowa strona](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są automatycznie stosowane. Aby zastosować zalecenie, skopiuj tekst zapytania i uruchom go z wybranego klienta. Należy pamiętać o przetestowaniu i monitorowaniu w celu ocenienia zalecenia.

## <a name="recommendation-types"></a>Typy rekomendacji

Obecnie obsługiwane są tylko zalecenia dotyczące *tworzenia indeksów* .

### <a name="create-index-recommendations"></a>Tworzenie zaleceń dotyczących indeksów

*Tworzenie* zaleceń dotyczących indeksów Sugeruj nowe indeksy, aby przyspieszyć najczęściej wykonywane lub czasochłonne zapytania w obciążeniu. Ten typ rekomendacji wymaga włączenia [magazynu zapytań](concepts-query-store.md) . Magazyn zapytań zbiera informacje o zapytaniach i zawiera szczegółowe dane dotyczące środowiska uruchomieniowego zapytań i częstotliwości, których analiza używa do wykonania zalecenia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [monitorowaniu i dostrajania](concepts-monitoring.md) w Azure Database for MariaDB.