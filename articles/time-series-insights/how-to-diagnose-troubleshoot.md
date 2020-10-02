---
title: Diagnozowanie i rozwiązywanie problemów ze środowiskiem Gen2 — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak diagnozować i rozwiązywać problemy z Azure Time Series Insights środowiskiem Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b994e8ce34319da4827d389b49e23ed6e5bcde95
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653761"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnozowanie i rozwiązywanie problemów dotyczących środowiska Azure Time Series Insights Gen2

W tym artykule opisano kilka typowych problemów, które mogą wystąpić podczas pracy z Azure Time Series Insights środowiska Gen2. W tym artykule opisano również potencjalne przyczyny i rozwiązania każdego problemu.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problem: nie można znaleźć środowiska w Eksploratorze Gen2

Ten problem może wystąpić, jeśli nie masz uprawnień dostępu do środowiska Time Series Insights. Aby wyświetlić środowisko Time Series Insights, użytkownicy muszą mieć rolę dostępu na poziomie czytnika. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **zasady dostępu do danych** w zasobie Time Series Insights w [Azure Portal](https://portal.azure.com/).

  [![Sprawdź zasady dostępu do danych.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problem: w Eksploratorze Gen2 nie są widoczne żadne dane

Istnieje kilka typowych przyczyn, dla których dane mogą nie być wyświetlane w [Azure Time Series Insights Gen2 Explorer](https://insights.timeseries.azure.com/preview).

- Źródło zdarzenia może nie odbierać danych.

    Sprawdź, czy źródło zdarzeń, które jest centrum zdarzeń lub Centrum IoT, otrzymuje dane ze swoich tagów lub wystąpień. Aby sprawdzić, przejdź do strony Przegląd zasobu w Azure Portal.

    [![Przegląd metryk pulpitu nawigacyjnego.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Dane źródłowe zdarzenia nie są w formacie JSON.

    Time Series Insights obsługuje tylko dane JSON. W przypadku przykładów JSON Przeczytaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).

- Klucz źródła zdarzenia nie ma wymaganego uprawnienia.

  - W przypadku usługi IoT Hub należy podać klucz, który ma uprawnienia do **połączenia z usługą** .

    [![Sprawdź uprawnienia do centrum IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Zarówno zasady **iothubowner** , jak i działanie **usługi** , ponieważ mają uprawnienie do **łączenia usług** .

  - W przypadku centrum zdarzeń należy podać klucz, który ma uprawnienia do **nasłuchiwania** .
  
    [![Przejrzyj uprawnienia centrum zdarzeń.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Zasady **odczytu** i **zarządzania** działają, ponieważ mają uprawnienia do **nasłuchiwania** .

- Podana Grupa odbiorców nie ma wyłącznej Time Series Insights.

    Podczas rejestracji Centrum IoT Hub lub centrum zdarzeń należy określić grupę konsumentów, która jest używana do odczytywania danych. Ta grupa odbiorców musi być unikatowa dla danego środowiska. Jeśli grupa konsumentów jest udostępniona, w podstawowym centrum zdarzeń automatycznie rozłącza jednego z czytelników. Podaj unikatową grupę odbiorców, w której ma zostać odczytana Time Series Insights.

- Właściwość identyfikatora szeregów czasowych określona w chwili aprowizacji jest nieprawidłowa, nie istnieje lub ma wartość null.

    Ten problem może wystąpić, jeśli właściwość identyfikatora szeregów czasowych została skonfigurowana niepoprawnie w momencie aprowizacji środowiska. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). W tej chwili nie można zaktualizować istniejącego środowiska Time Series Insights, aby użyć innego identyfikatora szeregów czasowych.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: niektóre dane są widoczne, ale brakuje niektórych

Dane mogą być wysyłane bez identyfikatora szeregów czasowych.

- Ten problem może wystąpić, gdy wysyłasz zdarzenia bez pola identyfikatora szeregów czasowych w ładunku. Aby uzyskać więcej informacji, Przeczytaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).
- Ten problem może wystąpić, ponieważ Twoje środowisko jest ograniczone.

    > [!NOTE]
    > W tej chwili Time Series Insights obsługuje maksymalny współczynnik pozyskiwania wynoszący 6 MB/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problem: dane były wyświetlane, ale teraz trwa ich pozyskiwanie

- Klucz źródła zdarzeń mógł zostać ponownie wygenerowany i środowisko Gen2 wymaga nowego klucza źródła zdarzeń.

Ten problem występuje, gdy klucz podany podczas tworzenia źródła zdarzenia nie jest już prawidłowy. W centrum zobaczysz dane telemetryczne, ale w Time Series Insights nie odebrano komunikatów przychodzących. Jeśli nie masz pewności, czy klucz został wygenerowany ponownie, możesz wyszukać w dzienniku aktywności "Event Hubs" utworzyć lub zaktualizować reguły autoryzacji przestrzeni nazw lub wyszukać opcję "Utwórz lub zaktualizuj zasób IotHub" dla usługi IoT Hub.

Aby zaktualizować Time Series Insights środowisko Gen2 przy użyciu nowego klucza, Otwórz zasób centrum w Azure Portal i skopiuj nowy klucz. Przejdź do zasobu TSI i kliknij pozycję źródła zdarzeń.

   [![Zrzut ekranu przedstawia element T S I zasób ze źródłami zdarzeń o nazwie "out".](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Wybierz źródła zdarzeń, z których zostało zatrzymane pozyskiwanie, wklej nowy klucz i kliknij przycisk Zapisz.

   [![Zrzut ekranu przedstawia zasób T S I z wprowadzonym kluczem zasad centrum.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: Nazwa właściwości sygnatury czasowej źródła zdarzenia nie działa

Upewnij się, że nazwa i wartość są zgodne z następującymi regułami:

- W nazwie właściwości sygnatury czasowej jest uwzględniana wielkość liter.
- Wartość właściwości timestamp, która pochodzi ze źródła zdarzenia jako ciąg JSON, ma format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Przykładem takiego ciągu jest `"2008-04-12T12:53Z"` .

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej została przechwycona i działa prawidłowo, to użycie Eksploratora Time Series Insights Gen2. W Time Series Insights Eksploratorze Gen2 Użyj wykresu, aby wybrać okres czasu po podaniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie i wybierz opcję **Eksploruj zdarzenia** . Pierwszy nagłówek kolumny jest nazwą właściwości znacznika czasu. Powinien zawierać `($ts)` obok wyrazu `Timestamp` , a nie:

- `(abc)`, co oznacza, że Time Series Insights odczytuje wartości danych jako ciągi.
- Ikona **kalendarza** , która wskazuje, że Time Series Insights odczytuje wartość danych jako datę i godzinę.
- `#`, co oznacza, że Time Series Insights odczytuje wartości danych jako liczbę całkowitą.

Jeśli właściwość sygnatury czasowej nie zostanie określona jawnie, jako domyślny znacznik czasu zostanie użyta godzina zdarzenia w centrum IoT lub centrum zdarzeń.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: nie można wyświetlić danych ze sklepu ciepłego w Eksploratorze

- Być może ostatnio zainicjowano aprowizacji sklepu, a dane nadal przepływają.
- Prawdopodobnie usunięto magazyn ciepły. w takim przypadku dane zostaną utracone.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: nie można wyświetlić lub edytować mojego modelu szeregów czasowych

- Można uzyskać dostęp do środowiska Time Series Insights S1 lub S2.

   Modele szeregów czasowych są obsługiwane tylko w środowiskach z opcją płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do środowiska S1 lub S2 z Time Series Insights Gen2 Explorer, przeczytaj temat [Wizualizacja danych w Eksploratorze](./time-series-insights-update-explorer.md).

   [![Brak zdarzeń w środowisku.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Być może nie masz uprawnień do wyświetlania i edytowania modelu.

   Użytkownicy potrzebują dostępu na poziomie współautora, aby edytować i przeglądać swój model szeregów czasowych. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **zasady dostępu do danych** w zasobie Time Series Insights w Azure Portal.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problem: wszystkie moje wystąpienia w Eksploratorze Gen2 nie mają elementu nadrzędnego

Ten problem może wystąpić, jeśli środowisko nie ma zdefiniowanej hierarchii modelu czasu szeregów czasowych. Aby uzyskać więcej informacji, zapoznaj się z tematem jak [korzystać z modeli szeregów czasowych](/azure/time-series-insights/time-series-insights-overview).

  [![W wystąpieniach nienadrzędnych zostanie wyświetlone ostrzeżenie.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>Problem: Łącznik Power BI nie zawiera "nie można nawiązać połączenia"

Ten problem może wystąpić, jeśli nie używasz najnowszej wersji łącznika Power BI w programie Power BI Desktop.

[![Zrzut ekranu przedstawia okno dialogowe nie można nawiązać połączenia.](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

- Sprawdź wersję Power BI Desktop i upewnij się, że korzystasz z wersji 2020 lipca. W przeciwnym razie zaktualizuj Power BI Desktop i ponownie uruchom łącznik.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj, jak [korzystać z modeli szeregów czasowych](/azure/time-series-insights/time-series-insights-overview).

- Poznaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).

- Zapoznaj się z tematem [Planowanie i limity](./time-series-insights-update-plan.md) w Azure Time Series Insights Gen2.
