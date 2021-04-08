---
title: Koncepcje odzyskiwania punktów kontrolnych i powtarzania w Azure Stream Analytics
description: W tym artykule opisano pojęcia dotyczące odzyskiwania punktów kontrolnych i powtarzania zadań w Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015322"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Pojęcia dotyczące punktów kontrolnych i powtarzania w zadaniach Azure Stream Analytics
W tym artykule opisano wewnętrzne punkty kontrolne i koncepcje powtarzania w programie Azure Stream Analytics oraz wpływ na odzyskiwanie zadań. Przy każdym uruchomieniu zadania Stream Analytics informacje o stanie są obsługiwane wewnętrznie. Informacje o stanie są zapisywane okresowo w punkcie kontrolnym. W niektórych scenariuszach informacje o punkcie kontrolnym są używane do odzyskiwania zadań, jeśli wystąpi błąd lub uaktualnienie zadania. W innych sytuacjach punkt kontrolny nie może być używany do odzyskiwania, a odtwarzanie jest konieczne.

## <a name="stateful-query-logic-in-temporal-elements"></a>Logika zapytań stanowych w elementach czasowych
Jedną z unikatowych możliwości Azure Stream Analytics zadania jest wykonywanie przetwarzania stanowego, takiego jak agregacje okienkowe, sprzężenia czasowe i funkcje analityczne. Każdy z tych operatorów utrzymuje informacje o stanie, gdy zadanie jest uruchomione. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Koncepcja okna danych czasowych pojawia się w kilku Stream Analytics elementach zapytania:
1. Zagregowane okna (Grupuj według wirowania, przeskoku i ruchomych okien)

2. Sprzężenia czasowe (dołączanie za pomocą DATEDIFF)

3. Tymczasowe funkcje analityczne (isfirst, LAST i LAG z LIMITem czasu trwania)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Odzyskiwanie zadania z awarii węzła, w tym uaktualnienia systemu operacyjnego
Za każdym razem, gdy zadanie Stream Analytics zostanie uruchomione, wewnętrznie jest skalowane do pracy w wielu węzłach procesu roboczego. Każdy węzeł procesu roboczego jest wyświetlany jako punkt kontrolny co kilka minut, co ułatwia odzyskiwanie systemu w przypadku wystąpienia awarii.

Czasami dany węzeł procesu roboczego może zakończyć się niepowodzeniem lub może wystąpić uaktualnienie systemu operacyjnego dla tego węzła procesu roboczego. Aby automatycznie odzyskiwać, Stream Analytics uzyskuje nowy węzeł w dobrej kondycji, a poprzedni stan węzła procesu roboczego zostanie przywrócony z najnowszego dostępnego punktu kontrolnego. Aby wznowić pracę, konieczne jest niewielka ilość powtórzeń w celu przywrócenia stanu od momentu utworzenia punktu kontrolnego. Zwykle przerwy przywracania to zaledwie kilka minut. Po wybraniu wystarczającej liczby jednostek przesyłania strumieniowego zadanie powtarzania powinno być wykonywane szybko. 

W pełni równoległe zapytanie czas potrzebny na przechwycenie po awarii węzła procesu roboczego jest proporcjonalny do:

[współczynnik zdarzeń wejściowych] x [długość przerwy]/[liczba partycji przetwarzania]

Jeśli kiedykolwiek wystąpi duże opóźnienie przetwarzania z powodu awarii węzła i uaktualnienia systemu operacyjnego, należy rozważyć przeprowadzenie zapytania w pełni równoległe i przeskalowanie zadania w celu przydzielenia większej liczby jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [skalowanie zadania Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md).

Bieżący Stream Analytics nie pokazuje raportu, gdy odbywa się ten rodzaj procesu odzyskiwania.

## <a name="job-recovery-from-a-service-upgrade"></a>Odzyskiwanie zadania z uaktualnienia usługi 
Firma Microsoft czasami uaktualnia pliki binarne, które uruchamiają zadania Stream Analytics w usłudze platformy Azure. W tych godzinach zadania wykonywane przez użytkowników są uaktualnione do nowszej wersji, a zadanie zostanie ponownie uruchomione automatycznie. 

Obecnie format punktu kontrolnego odzyskiwania nie jest zachowywany między uaktualnieniami. W związku z tym stan zapytania przesyłania strumieniowego musi być przywracany całkowicie przy użyciu techniki powtarzania. Aby umożliwić Stream Analytics zadania odtwarzania dokładnie tych samych danych wejściowych z wcześniej, należy ustawić zasady przechowywania dla danych źródłowych na co najmniej rozmiary okien w zapytaniu. Niewykonanie tej czynności może spowodować nieprawidłowe lub częściowe wyniki podczas uaktualniania usługi, ponieważ dane źródłowe mogą nie być przechowywane wystarczająco długo, aby uwzględnić pełny rozmiar okna.

Ogólnie rzecz biorąc, ilość wymaganego odtwarzania jest proporcjonalna do rozmiaru okna pomnożonego przez średnią częstotliwość zdarzeń. Przykładowo w przypadku zadania z szybkością wprowadzania 1000 zdarzeń na sekundę rozmiar okna o rozmiarze większym niż godzina jest traktowany jak rozmiar dużego powtórzenia. Do zainicjowania stanu może być konieczne ponowne przetworzenie danych do jednej godziny, aby umożliwić wygenerowanie pełnych i poprawnych wyników, co może spowodować opóźnione wyjście (Brak danych wyjściowych) przez dłuższy czas. Zapytania bez okien ani innych operatorów czasowych, takich jak `JOIN` lub `LAG` , nie będą odtwarzane.

## <a name="estimate-replay-catch-up-time"></a>Oszacowanie czasu przechwytywania ponownego odtwarzania
Aby oszacować długość opóźnienia z powodu uaktualnienia usługi, możesz wykonać tę technikę:

1. Załaduj dane wejściowe centrum zdarzeń z wystarczającą ilością danych, aby pokryć największy rozmiar okna w zapytaniu w oczekiwanym tempie zdarzeń. Sygnatura czasowa zdarzeń powinna być bliska godzinie zegara ściany w tym okresie, tak jakby była to na żywo źródło danych wejściowych. Na przykład jeśli w zapytaniu masz 3-dniowe okno, Wyślij zdarzenia do centrum zdarzeń przez trzy dni i Kontynuuj wysyłanie zdarzeń. 

2. Uruchom zadanie, używając **teraz** jako godziny rozpoczęcia. 

3. Zmierz czas między czasem rozpoczęcia i po wygenerowaniu pierwszego danych wyjściowych. Czas pozostały do poprawienia opóźnienia zadania podczas uaktualniania usługi.

4. Jeśli opóźnienie jest zbyt długie, spróbuj podzielić zadanie na partycje i zwiększyć liczbę serwerów, aby obciążenie było rozłożone na więcej węzłów. Alternatywnie należy rozważyć zmniejszenie rozmiarów okna w zapytaniu i przeprowadzenie dalszej agregacji lub innego przetwarzania stanowego na podstawie danych wyjściowych generowanych przez zadanie Stream Analytics w zlewie podrzędnym (na przykład przy użyciu Azure SQL Database).

Aby uzyskać ogólne problemy dotyczące stabilności usługi podczas uaktualniania zadań o znaczeniu krytycznym, należy rozważyć uruchomienie zduplikowanych zadań w sparowanych regionach platformy Azure. Aby uzyskać więcej informacji, zobacz temat [gwarancja Stream Analytics niezawodność zadania podczas aktualizacji usługi](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Odzyskiwanie zadania z zainicjowanego przez użytkownika zatrzymywania i uruchamiania
Aby edytować składnię zapytania w zadaniu przesyłania strumieniowego lub dostosować dane wejściowe i wyjściowe, należy zatrzymać zadanie, aby wprowadzić zmiany i uaktualnić projekt zadania. W takich scenariuszach, gdy użytkownik zatrzyma zadanie przesyłania strumieniowego i uruchomi je ponownie, scenariusz odzyskiwania jest podobny do uaktualnienia usługi. 

Dane punktu kontrolnego nie mogą być używane do ponownego uruchomienia zadania zainicjowane przez użytkownika. Aby oszacować opóźnienie danych wyjściowych podczas takiego ponownego uruchomienia, Użyj tej samej procedury, jak opisano w poprzedniej sekcji, i Zastosuj podobne środki zaradcze, jeśli opóźnienie jest zbyt długie.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat niezawodności i skalowalności, zobacz następujące artykuły:
- [Samouczek: Konfigurowanie alertów dla zadań Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Skalowanie zadania Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)
- [Gwarancja Stream Analytics niezawodność zadania podczas aktualizacji usługi](stream-analytics-job-reliability.md)
