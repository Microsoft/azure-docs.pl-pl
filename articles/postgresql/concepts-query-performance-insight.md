---
title: Szczegółowe informacje o wydajności zapytań-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Szczegółowe informacje o wydajności zapytań w programie Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768388"
---
# <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań 

**Dotyczy:** Azure Database for PostgreSQL — jeden serwer w wersji 9,6, 10, 11

Szczegółowe informacje o wydajności zapytań pomaga szybko określić, co najdłuższe uruchomione zapytania, jak zmieniają się wraz z upływem czasu i co czeka na ich wpływ.

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
Aby Szczegółowe informacje o wydajności zapytań do funkcji, dane muszą znajdować się w [magazynie zapytań](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Wyświetlanie szczegółowych informacji o wydajności
W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań. 

Na stronie portalu serwera Azure Database for PostgreSQL wybierz pozycję **zapytanie wydajności szczegółowe informacje** w sekcji **Inteligentna wydajność** na pasku menu.

![Szczegółowe informacje o wydajności zapytań długotrwałych zapytań](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Karta **długotrwałe zapytania** pokazuje pięć pierwszych zapytań według średniego czasu trwania na wykonanie, zagregowane w odstępach 15-minutowych. Możesz wyświetlić więcej zapytań, wybierając wartość z listy rozwijanej **Liczba zapytań**. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Alternatywnie możesz użyć ikon powiększenia i powiększania, aby wyświetlić odpowiednio krótszy lub dłuższy okres.

Tabela poniżej wykresu zawiera więcej szczegółów na temat długotrwałych zapytań w tym przedziale czasu.

Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.

![Dane statystyczne Szczegółowe informacje o wydajności zapytań czeka](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Istotne zagadnienia
* Szczegółowe informacje o wydajności zapytań nie jest dostępna dla [replik odczytu](concepts-read-replicas.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.


