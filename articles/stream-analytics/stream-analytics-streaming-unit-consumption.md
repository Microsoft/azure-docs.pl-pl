---
title: Jednostki przesyłania strumieniowego w Azure Stream Analytics
description: W tym artykule opisano ustawienia jednostek przesyłania strumieniowego i inne czynniki wpływające na wydajność w Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 70b5e85c99184b890d2b5269f483785a82340255
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127556"
---
# <a name="understand-and-adjust-streaming-units"></a>Opis i dostosowywanie jednostek przesyłania strumieniowego

Jednostki przesyłania strumieniowego (SUs) reprezentują zasoby obliczeniowe, które są przydzieleni do wykonywania zadania Stream Analytics. Im większa liczba jednostek przesyłania strumieniowego, tym większa ilość zasobów procesora i pamięci jest przydzielana do zadania. Ta pojemność umożliwia skoncentrowanie się na logice zapytania i umożliwia streszczenie konieczności zarządzania sprzętem w celu uruchomienia Stream Analytics zadania w odpowiednim czasie.

W celu uzyskania małych opóźnień przetwarzania strumieni całe przetwarzanie dla zadań usługi Azure Stream Analytics jest wykonywane w pamięci. W przypadku braku pamięci zadanie przesyłania strumieniowego kończy się niepowodzeniem. W związku z tym ważne jest, aby dla zadania produkcyjnego monitorować użycie zasobów zadania przesyłania strumieniowego i upewnić się, że przydzielono zasoby wystarczające do zachowania zadań 24/7.

Metryka użycia funkcji SU%, która obejmuje wartości z zakresu od 0 do 100%, opisuje użycie pamięci przez obciążenie. W przypadku zadania przesyłania strumieniowego z minimalnym wpływem ta Metryka jest zwykle z przedziału od 10% do 20%. Jeśli użycie SU% jest wysokie (powyżej 80%) lub jeśli zdarzenia wejściowe są rejestrowane (nawet przy niskim użyciu wartości SU%, ponieważ nie pokazuje użycia procesora), obciążenie prawdopodobnie wymaga większej ilości zasobów obliczeniowych, co wymaga zwiększenia liczby usług SUs. Najlepiej zachować metrykę SU poniżej 80%, aby uwzględnić sporadyczne osiągnięcia. Firma Microsoft zaleca ustawienie alertu dotyczącego metryki użycia funkcji SU (80%) w celu zapobieżenia wyczerpaniu zasobów. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie alertów dla Azure Stream Analytics zadań](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurowanie Stream Analytics jednostek przesyłania strumieniowego (SUs)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/)

2. Na liście zasobów Znajdź zadanie Stream Analytics, które chcesz skalować, a następnie otwórz je. 

3. Na stronie zadanie w obszarze **Konfiguruj** nagłówek wybierz pozycję **Skala** . Domyślna liczba usług SUs to 3 podczas tworzenia zadania.

    ![Konfiguracja zadania Stream Analytics Azure Portal][img.stream.analytics.preview.portal.settings.scale]
    
4. Użyj suwaka, aby ustawić program SUs dla tego zadania. Zwróć uwagę, że masz ograniczone do określonych ustawień SU. 
5. Można zmienić liczbę programów SUs przypisanych do zadania nawet wtedy, gdy jest on uruchomiony. Nie jest to możliwe, jeśli zadanie używa [niepodzielonych danych wyjściowych](./stream-analytics-parallelization.md#query-using-non-partitioned-output) lub zawiera [zapytanie wieloetapowe z inną partycją przez wartości](./stream-analytics-parallelization.md#multi-step-query-with-different-partition-by-values). Możesz ograniczyć wybór z zestawu wartości SU, gdy zadanie jest uruchomione. 

## <a name="monitor-job-performance"></a>Monitorowanie wydajności zadania
Za pomocą Azure Portal można śledzić przepływność zadania:

![Zadania monitorowania Azure Stream Analytics][img.stream.analytics.monitor.job]

Oblicz oczekiwaną przepływność obciążenia. Jeśli przepływność jest mniejsza niż oczekiwana, Dostosuj partycję wejściową, Dostosuj zapytanie i Dodaj program SUs do zadania.

## <a name="how-many-sus-are-required-for-a-job"></a>How many SUs are required for a job? (Ile jednostek przesyłania strumieniowego jest wymaganych dla zadania?)

Wybór liczby wymaganych usług SUs dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i zapytania zdefiniowanego w ramach zadania. Na stronie **Skala** można ustawić właściwą liczbę usług SUs. Najlepszym rozwiązaniem jest przydzielenie większej liczby usług SUs niż jest to konieczne. Aparat przetwarzania Stream Analytics optymalizuje czas oczekiwania i przepływność przy kosztach przydziału dodatkowej pamięci.

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest rozpoczęcie od 6 usług SUs dla zapytań, które nie używają **partycji przez** program. Następnie należy określić dane słodkie przy użyciu metody próbnej i błędu, w której można zmodyfikować liczbę programów SUs po przekazanie reprezentatywnych ilości danych i sprawdzić metrykę wykorzystania SU%. Maksymalna liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie Stream Analytics, zależy od liczby kroków zapytania zdefiniowanego dla zadania i liczby partycji w każdym kroku. Więcej informacji na temat limitów można znaleźć [tutaj](./stream-analytics-parallelization.md#calculate-the-maximum-streaming-units-of-a-job).

Aby uzyskać więcej informacji na temat wybierania odpowiedniej liczby usług SUs, zobacz Tę stronę: [skalowanie Azure Stream Analytics zadania w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

> [!Note]
> Wybór liczby programów SUs wymaganych do określonego zadania zależy od konfiguracji partycji dla danych wejściowych i zapytania zdefiniowanego dla tego zadania. Możesz wybrać do przydziału w programie SUs dla zadania. Domyślnie każda subskrypcja platformy Azure ma przydział do 500 usług SUs dla wszystkich zadań analitycznych w określonym regionie. Aby zwiększyć liczbę usług programu SUs dla subskrypcji wykraczających poza ten limit przydziału, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com). Prawidłowe wartości dla programu SUs na zadanie to 1, 3, 6 i maksymalnie 6.

## <a name="factors-that-increase-su-utilization"></a>Factors that increase SU% utilization (Czynniki zwiększające procentowe wykorzystanie jednostek przesyłania strumieniowego) 

Dane czasowe (zorientowane czasie) są podstawowym zestawem operatorów stanowych udostępnianych przez Stream Analytics. Stream Analytics zarządza stanem tych operacji wewnętrznie w imieniu użytkownika, przez zarządzanie użyciem pamięci, tworzenie punktów kontrolnych dla odporności i odzyskiwanie stanu podczas uaktualniania usług. Mimo że Stream Analytics w pełni zarządza stanami, istnieją pewne zalecenia dotyczące najlepszych rozwiązań, które użytkownicy powinni wziąć pod uwagę.

Należy pamiętać, że zadanie ze złożoną logiką zapytań może mieć wysokie użycie elementu SU% nawet wtedy, gdy nie będzie on ciągle otrzymywał zdarzeń wejściowych. Może to nastąpić po nagłym pojściu w zdarzeniach wejściowych i wyjściowych. Zadanie może nadal utrzymać stan w pamięci, jeśli zapytanie jest złożone.

Użycie SU% może nagle porzucić do 0 przez krótki czas przed powrotem do oczekiwanego poziomu. Dzieje się tak z powodu błędów przejściowych lub uaktualnień inicjowanych przez system. Zwiększenie liczby jednostek przesyłania strumieniowego dla zadania może nie zmniejszyć użycia elementu SU%, jeśli zapytanie nie jest w [pełni równoległe](./stream-analytics-parallelization.md).

Porównując użycie w danym okresie czasu, użyj [metryk współczynnika zdarzeń](stream-analytics-monitoring.md). Metryki InputEvents i OutputEvents pokazują, ile zdarzeń zostało odczytanych i przetworzonych. Istnieją metryki wskazujące liczbę zdarzeń błędów, jak również błędy deserializacji. Po zwiększeniu liczby zdarzeń na jednostkę czasu SU% rośnie w większości przypadków.

## <a name="stateful-query-logic-in-temporal-elements"></a>Logika zapytań stanowych w elementach czasowych
Jedną z unikatowych możliwości Azure Stream Analytics zadania jest wykonywanie przetwarzania stanowego, takiego jak agregacje okienkowe, sprzężenia czasowe i funkcje analityczne. Każdy z tych operatorów przechowuje informacje o stanie. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Koncepcja okna danych czasowych pojawia się w kilku Stream Analytics elementach zapytania:
1. Zagregowane okna: Grupuj według wirowania, przeskoku i ruchomych okien

2. Sprzężenia czasowe: SPRZĘŻENIe z funkcją DATEDIFF

3. Tymczasowe funkcje analityczne: isfirst, LAST i LAG z LIMITem czasu trwania

Poniższe czynniki wpływają na użycie używanej pamięci (część metryk jednostek przesyłania strumieniowego) przez Stream Analytics zadania:

## <a name="windowed-aggregates"></a>Zagregowane okna
Używana pamięć (rozmiar stanu) dla agregacji okienkowej nie zawsze jest bezpośrednio proporcjonalna do rozmiaru okna. Zamiast tego używana pamięć jest proporcjonalna do kardynalności danych lub liczby grup w każdym przedziale czasu.


Na przykład w poniższym zapytaniu liczba skojarzona z `clusterid` jest kardynalnością zapytania. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Aby uniknąć problemów spowodowanych przez dużą Kardynalność w poprzedniej kwerendzie, można wysyłać zdarzenia do centrum zdarzeń partycjonowane według `clusterid` i skalować zapytanie, umożliwiając systemowi przetworzenie każdej partycji wejściowej osobno przy użyciu **partycji** , jak pokazano w poniższym przykładzie:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym liczba `clusterid` wartości w każdym węźle jest zmniejszana, co zmniejsza Kardynalność operatora Group by. 

Partycje centrum zdarzeń należy podzielić przez klucz grupowania, aby uniknąć konieczności zmniejszenia kroku. Aby uzyskać więcej informacji, zobacz [Event Hubs przegląd](../event-hubs/event-hubs-about.md). 

## <a name="temporal-joins"></a>Sprzężenia czasowe
Zajęta pamięć (rozmiar stanu) sprzężenia danych czasowych jest proporcjonalna do liczby zdarzeń w pokoju wiggle czasowego przyłączenia, który jest wskaźnikiem nakładu zdarzeń pomnożonym przez rozmiar pokoju wiggle. Innymi słowy, pamięć wykorzystywana przez sprzężenia jest proporcjonalna do zakresu czasu DateDiff pomnożonego przez średni współczynnik zdarzeń.

Liczba niedopasowanych zdarzeń w sprzężeniu wpływa na użycie pamięci przez zapytanie. Następujące zapytanie służy do znajdowania wyświetleń reklam, które generują kliknięcia:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

W tym przykładzie możliwe jest wyświetlenie wielu reklam, a kilka osób klika je i jest wymagane, aby zachować wszystkie zdarzenia w przedziale czasu. Używana pamięć jest proporcjonalna do wielkości okna i szybkości zdarzeń. 

Aby rozwiązać ten problemy, należy wysłać zdarzenia do centrum zdarzeń partycjonowane przez klucze sprzężenia (w tym przypadku) i skalować zapytanie, umożliwiając systemowi przetworzenie każdej partycji wejściowej osobno przy użyciu  **partycji** w następujący sposób:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W efekcie liczba zdarzeń przychodzących do każdego węzła jest ograniczana, co zmniejsza rozmiar stanu przechowywanego w oknie sprzężenia. 

## <a name="temporal-analytic-functions"></a>Funkcje analityczne danych czasowych
Używana pamięć (rozmiar stanu) funkcji analitycznej danych czasowych jest proporcjonalna do współczynnika zdarzeń pomnożyć przez czas trwania. Pamięć wykorzystywana przez funkcje analityczne nie jest proporcjonalna do rozmiaru okna, ale raczej liczba partycji w każdym przedziale czasu.

Korygowanie jest podobne do sprzężenia czasowego. Zapytanie można skalować w poziomie za pomocą **partycji** . 

## <a name="out-of-order-buffer"></a>Bufor poza kolejnością 
Użytkownik może skonfigurować rozmiar buforu poza kolejnością w okienku Konfiguracja porządkowania zdarzeń. Bufor jest używany do przechowywania danych wejściowych w czasie trwania okna i zmieniania ich kolejności. Rozmiar buforu jest proporcjonalny do współczynnika danych wejściowych zdarzenia pomnożyć przez rozmiar okna poza kolejnością. Domyślny rozmiar okna to 0. 

Aby skorygować przepełnienie buforu poza kolejnością, Skaluj zapytanie w poziomie przy użyciu **partycji przez** . Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym liczba zdarzeń przychodzących do każdego węzła jest ograniczana, co zmniejsza liczbę zdarzeń w każdym buforze zmiany kolejności. 

## <a name="input-partition-count"></a>Liczba partycji wejściowych 
Każda partycja wejściowa danych wejściowych zadania ma bufor. Większa liczba partycji wejściowych, tym więcej zasobów zużywanych przez zadanie. Dla każdej jednostki przesyłania strumieniowego Azure Stream Analytics może przetworzyć około 1 MB/s danych wejściowych. W związku z tym można zoptymalizować się, dopasowując liczbę Stream Analytics jednostek przesyłania strumieniowego do liczby partycji w centrum zdarzeń. 

Zwykle zadanie skonfigurowane z jedną jednostką przesyłania strumieniowego jest wystarczające dla centrum zdarzeń z dwiema partycjami (co jest minimum dla centrum zdarzeń). Jeśli centrum zdarzeń ma więcej partycji, zadanie Stream Analytics zużywa więcej zasobów, ale niekoniecznie wykorzystuje dodatkową przepływność zapewnioną przez centrum zdarzeń. 

W przypadku zadania z 6 jednostkami przesyłania strumieniowego może być konieczne posiadanie 4 lub 8 partycji z centrum zdarzeń. Należy jednak unikać zbyt wielu niepotrzebnych partycji, ponieważ powoduje nadmierne użycie zasobów. Na przykład centrum zdarzeń z 16 partycjami lub większym w Stream Analytics zadania z 1 jednostką przesyłania strumieniowego. 

## <a name="reference-data"></a>Dane referencyjne 
Dane referencyjne w ASA są ładowane do pamięci w celu szybkiego odszukania. W przypadku bieżącej implementacji każda operacja sprzężenia z danymi referencyjnymi przechowuje kopię danych referencyjnych w pamięci, nawet jeśli dołączysz te same dane referencyjne wielokrotnie. W przypadku zapytań z **partycją przez** program każda partycja ma kopię danych referencyjnych, więc partycje są całkowicie oddzielone. Dzięki efektowi mnożnika użycie pamięci może szybko uzyskać wysoką wartość, jeśli dołączysz dane odwołań wiele razy z wieloma partycjami.  

### <a name="use-of-udf-functions"></a>Korzystanie z funkcji UDF
Po dodaniu funkcji UDF Azure Stream Analytics ładuje środowisko uruchomieniowe JavaScript do pamięci. Będzie to miało wpływ na wartość% SU.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie zapytań działania równoległego w Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalowanie zadań Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png