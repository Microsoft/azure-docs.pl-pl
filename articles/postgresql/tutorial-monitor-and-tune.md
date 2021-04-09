---
title: 'Samouczek: monitorowanie i dostrajanie-Azure Database for PostgreSQL-pojedynczy serwer'
description: Ten samouczek przeprowadzi Cię przez monitorowanie i dostrajanie w Azure Database for PostgreSQL-pojedynczym serwerze.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336983"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Samouczek: monitorowanie i dostrajanie Azure Database for PostgreSQL-pojedynczego serwera

Usługa Azure Database for PostgreSQL zawiera funkcje, które pomagają zrozumieć i poprawić wydajność serwera. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Włączanie zbierania statystyk zapytań i oczekiwania
> * Uzyskiwanie dostępu do zebranych danych i korzystanie z nich
> * Wyświetlanie wydajności zapytań i statystyk oczekiwania w czasie
> * Analizowanie bazy danych w celu uzyskania zaleceń dotyczących wydajności
> * Stosowanie zaleceń dotyczących wydajności

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebujesz serwera usługi Azure Database for PostgreSQL z programem PostgreSQL w wersji 9.6 lub 10. Możesz wykonać kroki opisane w [samouczku dotyczącym tworzenia](tutorial-design-database-using-azure-portal.md), aby utworzyć serwer.

> [!IMPORTANT]
> Funkcje **Magazyn zapytań**, **Szczegółowe informacje o wydajności zapytań** i **Zalecenia dotyczące wydajności** są dostępne w publicznej wersji zapoznawczej.

## <a name="enabling-data-collection"></a>Włączanie zbierania danych
[Magazyn zapytań](concepts-query-store.md) przechwytuje historię zapytań i statystyki oczekiwania na serwerze i przechowuje je w bazie danych **azure_sys** na serwerze. Jest to funkcja opcjonalna. Aby ją włączyć:

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. Wybierz pozycję **Parametry serwera**, znajdującą się w sekcji **Ustawienia** menu po lewej stronie.

4. Ustaw parametr **pg_qs.query_capture_mode** na wartość **TOP**, aby rozpocząć zbieranie danych o wydajności zapytań. Ustaw parametr **pgms_wait_sampling.query_capture_mode** na wartość **ALL**, aby rozpocząć zbieranie statystyk oczekiwania. Zapisz zmiany.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Parametry serwera magazynu zapytań":::

5. Poczekaj do 20 minut na utrwalenie pierwszej partii danych w bazie danych **azure_sys**.


## <a name="performance-insights"></a>Szczegółowe informacje o wydajności
W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań. 

1. Na stronie portalu serwera usługi Azure Database for PostgreSQL wybierz pozycję **Szczegółowe informacje o wydajności zapytań** w sekcji **Pomoc techniczna i rozwiązywanie problemów** menu po lewej stronie.

2. Na karcie **Długotrwałe zapytania** jest wyświetlane pięć pierwszych zapytań według średniego czasu trwania na wykonanie, agregowanych w piętnastominutowych interwałach. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Szczegółowe informacje o wydajności zapytań, strona docelowa":::

   Możesz wyświetlić więcej zapytań, wybierając wartość z listy rozwijanej **Liczba zapytań**. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

3. Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu.

4. Użyj ikon powiększenia i pomniejszenia, aby wyświetlić odpowiednio mniejszy lub większy okres.

5. Wyświetl tabelę poniżej wykresu, aby uzyskać więcej szczegółów na temat długotrwałych zapytań w tym przedziale czasu.

6. Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Szczegółowe informacje o wydajności zapytań — statystyki oczekiwania":::

### <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.


## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

1. Otwórz **Zalecenia dotyczące wydajności** w sekcji **Pomoc techniczna i rozwiązywanie problemów** paska menu na stronie serwera PostgreSQL w witrynie Azure Portal.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Zalecenia dotyczące wydajności, strona docelowa":::

2. Wybierz opcję **Analiza** i wybierz bazę danych. Spowoduje to rozpoczęcie analizy.

3. W zależności od obciążenia jej ukończenie może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie.

4. W oknie **Zalecenia dotyczące wydajności** zostanie wyświetlona lista zaleceń, jeśli jakieś znaleziono. 

5. Zalecenia będą zawierały informacje o odpowiedniej **bazie danych**, **tabeli**, **kolumnie** i **rozmiarze indeksu**.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Zalecenia dotyczące wydajności, wyniki":::

6. Aby wdrożyć zalecenie, skopiuj tekst zapytania i uruchom je z poziomu wybranego klienta.

### <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały utworzone zasoby platformy Azure w grupie serwerów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę serwerów. Naciśnij przycisk *Usuń* na stronie *Przegląd* dla swojej grupy serwerów. Po wyświetleniu monitu na stronie podręcznej Potwierdź nazwę grupy serwerów, a następnie kliknij przycisk *Usuń* końcowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.