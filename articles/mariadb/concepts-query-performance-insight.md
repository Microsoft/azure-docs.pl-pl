---
title: Szczegółowe informacje o wydajności zapytań — Azure Database for MariaDB
description: W tym artykule opisano funkcję Szczegółowe informacje o wydajności zapytań w programie Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527848"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Analiza wydajności zapytań w usłudze Azure Database for MariaDB

**Dotyczy:** Azure Database for MariaDB 10,2

Szczegółowe informacje o wydajności zapytań pomaga szybko określić, co najdłuższe uruchomione zapytania, jak zmieniają się wraz z upływem czasu i co czeka na ich wpływ.

## <a name="common-scenarios"></a>Typowe scenariusze

### <a name="long-running-queries"></a>Długotrwałe zapytania

- Identyfikowanie najdłuższych uruchomionych zapytań w ciągu ostatnich X godzin
- Identyfikowanie pierwszych N zapytań, które oczekują na zasoby
 
### <a name="wait-statistics"></a>Statystyka oczekiwania

- Zrozumienie charakteru oczekiwania zapytania
- Zrozumienie trendów dotyczących zasobów i miejsce, w którym istnieje rywalizacja o zasoby

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

Aby Szczegółowe informacje o wydajności zapytań do funkcji, dane muszą znajdować się w [magazynie zapytań](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Wyświetlanie szczegółowych informacji o wydajności

W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań.

Na stronie portalu serwera Azure Database for MariaDB wybierz pozycję **szczegółowe informacje o wydajności zapytań** w sekcji **Intelligent Performance** na pasku menu.

### <a name="long-running-queries"></a>Długotrwałe zapytania

Karta **długotrwałe zapytania** przedstawia 5 pierwszych zapytań według średniego czasu trwania na wykonanie, zagregowane w odstępach 15-minutowych. Więcej zapytań można wyświetlić, wybierając pozycję z listy rozwijanej **Liczba zapytań** . Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Alternatywnie możesz użyć ikon powiększenia i powiększania, aby wyświetlić odpowiednio krótszy lub dłuższy okres.

![Szczegółowe informacje o wydajności zapytań długotrwałych zapytań](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statystyka oczekiwania 

> [!NOTE]
> Statystyki oczekiwania są przeznaczone do rozwiązywania problemów z wydajnością zapytań. Zaleca się włączenie tylko do celów związanych z rozwiązywaniem problemów. <br>Jeśli zostanie wyświetlony komunikat o błędzie w Azure Portal "*napotkano problem dotyczący elementu" Microsoft. DBforMariaDB "; nie można zrealizować żądania. Jeśli ten problem będzie się powtarzał lub jest nieoczekiwany, skontaktuj się z pomocą techniczną, podając te informacje.* podczas przeglądania statystyk oczekiwania należy użyć mniejszego okresu.

Statystyka oczekiwania zawiera widok zdarzeń oczekiwania występujących podczas wykonywania określonego zapytania. Dowiedz się więcej o typach zdarzeń oczekiwania w [dokumentacji aparatu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.

Zapytania wyświetlane w widoku Statystyka oczekiwania są pogrupowane według zapytań, które wykazują największe oczekiwania w określonym przedziale czasu.

![Dane statystyczne Szczegółowe informacje o wydajności zapytań czeka](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [monitorowaniu i dostrajania](concepts-monitoring.md) w Azure Database for MariaDB.