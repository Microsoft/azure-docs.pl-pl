---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny internetowej — Azure Monitor
description: Konfigurowanie testów ping w Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: 60698862e26175425221940a4b69867cb414fe86
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598877"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorowanie dostępności dowolnej witryny internetowej

Nazwa "URL ping test" jest nieco błędna. Dla jasnej informacji te testy nie korzystają z protokołu ICMP (Internet Control Message Protocol) w celu sprawdzenia dostępności witryny. Zamiast tego używają bardziej zaawansowanej funkcji żądania HTTP, aby sprawdzić, czy punkt końcowy odpowiada. Mierzą one również wydajność skojarzoną z tą odpowiedzią i dodają możliwość ustawienia niestandardowych kryteriów sukcesu w połączeniu z bardziej zaawansowanymi funkcjami, takich jak analizowanie żądań zależnych i zezwalanie na ponowne próby.

Aby utworzyć test dostępności, należy użyć istniejącego zasobu usługi Application Insights lub utworzyć [Application Insights zasobów.](create-new-resource.md)

Aby utworzyć pierwsze żądanie dostępności, otwórz okienko Dostępność i wybierz  **pozycję Utwórz test**.

:::image type="content" source="./media/monitor-web-app-availability/availability-create-test-001.png" alt-text="Zrzut ekranu przedstawiający tworzenie testu.":::

## <a name="create-a-test"></a>Tworzenie testu

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Adres URL** |  Adres URL może odnosić się do dowolnej strony sieci Web, którą chcesz przetestować, ale musi być widoczny w publicznym Internecie. Adres URL może zawierać ciąg zapytania. Możesz więc np. szybko sprawdzić działanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.|
|**Analizowanie żądań zależnych**| Test żąda obrazów, skryptów, plików stylów i innych plików, które są częścią testowej strony internetowej. Rejestrowany czas odpowiedzi obejmuje czas poświęcony na pobieranie tych plików. Test kończy się niepowodzeniem, jeśli nie można pomyślnie pobrać żadnego z tych zasobów w ramach limitu czasu dla całego testu. Jeśli pole opcji nie zostanie zaznaczone, test zażąda tylko pliku pod podanym adresem URL. Włączenie tej opcji powoduje bardziej rygorystyczne sprawdzanie. Test może się nie powieść w przypadkach, które mogą nie być zauważalne podczas ręcznego przeglądania witryny.
|**Włączanie ponownych prób**|Jeśli test zakończy się niepowodzeniem, zostanie on ponoszony po krótkim interwale. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. **Zalecamy użycie tej opcji.** Średnio około 80% błędów znika po ponowieniu testu.|
|**Częstotliwość testowania**| Określa, jak często test jest uruchamiany z każdej lokalizacji testu. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testowe**| Są miejscami, z których nasze serwery wysyłają żądania internetowe do Twojego adresu URL. **Minimalna zalecana liczba lokalizacji testowych** wynosi pięć, aby zapewnić, że można odróżnić problemy w witrynie internetowej od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

Jeśli twój adres URL nie jest widoczny z publicznego Internetu, możesz selektywnie otworzyć zaporę, aby zezwolić tylko na **transakcje testowe za pośrednictwem usługi**. Aby dowiedzieć się więcej na temat wyjątków zapory dla naszych agentów testu dostępności, zapoznaj się z [przewodnikiem po adresach IP.](./ip-addresses.md#availability-tests)

> [!NOTE]
> Zdecydowanie zalecamy testowanie z wielu lokalizacji z **co najmniej pięcioma lokalizacjami**. Ma to na celu zapobieganie fałszywym alarmom, które mogą wynikać z przejściowych problemów z określoną lokalizacją. Ponadto ustaliliśmy, że optymalną konfiguracją jest to, że liczba lokalizacji testowych jest równa wartości progowej lokalizacji **alertu + 2**.

## <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby była powiadamiana o wolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako powodzenie. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, taki jak "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Dopasowanie zawartości obsługuje tylko znaki angielskie** |

## <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Zalecamy używanie alertów niemal w czasie rzeczywistym. Konfigurowanie tego typu alertu odbywa się po utworzeniu testu dostępności.  |
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalna relacja między progiem lokalizacji alertu a liczbą lokalizacji testowych to liczba progowa lokalizacji alertu dla lokalizacji testowych — 2 z co najmniej  =  **pięcioma lokalizacjami testów.**|

## <a name="location-population-tags"></a>Tagi populacji lokalizacji

Następujące tagi populacji mogą być używane dla atrybutu lokalizacji geograficznej podczas wdrażania testu ping adresu URL dostępności przy użyciu Azure Resource Manager.

### <a name="azure-gov"></a>Azure gov

| Nazwa wyświetlana   | Nazwa populacji     |
|----------------|---------------------|
| USGov Wirginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Teksas    | usgov-tx-azr        |
| USDoD East     | usgov-ddeast-azr    |
| USDoD (środkowy)  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Nazwa wyświetlana                           | Nazwa populacji   |
|----------------------------------------|-------------------|
| Australia Wschodnia                         | emea-au-syd-edge  |
| Brazylia Południowa                           | latam-br-gru-edge |
| Central US                             | us-fl-mia-edge    |
| Azja Wschodnia                              | apac-hk-hkn-azr   |
| East US                                | us-va-azr     |
| Francja Południowa (wcześniej Francja Środkowa) | emea-ch-zrh-edge  |
| Francja Środkowa                         | emea-fr-pra-edge  |
| Japonia Wschodnia                             | apac-jp-do-edge  |
| Europa Północna                           | emea-gb-db3-azr   |
| Północno-środkowe stany USA                       | us-il-ch1-azr     |
| South Central US                       | us-tx-sn1-azr     |
| Southeast Asia                         | apac-sg-sin-azr   |
| Zachodnie Zjednoczone Królestwo                                | emea-se-sto-edge  |
| West Europe                            | emea-nl-ams-azr   |
| Zachodnie stany USA                                | us-ca-sjc-azr     |
| Południowe Zjednoczone Królestwo                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Wyświetlanie wyników testów dostępności

Wyniki testu dostępności można wizualizować za pomocą widoków linii i wykresu punktowego.

Po kilku minutach kliknij przycisk **Odśwież,** aby wyświetlić wyniki testu.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Zrzut ekranu przedstawia stronę Dostępność z wyróżni przycisk Odśwież wyróżniona.":::

Widok wykresu punktowego przedstawia przykłady wyników testów, które mają szczegółowe informacje o kroku testu diagnostycznego. Aparat testowy przechowuje szczegółowe informacje diagnostyczne dla testów z błędami. W przypadku udanych testów szczegółowe informacje diagnostyczne są przechowywane dla podzbioru wykonań. Umieść kursor nad dowolną zieloną/czerwoną kropkami, aby wyświetlić test, nazwę testu i lokalizację.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Widok wiersza." border="false":::

Wybierz określony test, lokalizację lub skróć okres czasu, aby zobaczyć więcej wyników w okolicy interesującego okresu czasu. Użyj Eksploratora wyszukiwania, aby zobaczyć wyniki z wszystkich wykonań, lub użyj zapytań analitycznych w celu uruchomienia niestandardowych raportów dla tych danych.

## <a name="inspect-and-edit-tests"></a> Sprawdzanie i edytowanie testów

Aby edytować, tymczasowo wyłączyć lub usunąć test, kliknij wielokropek obok nazwy testu. Propagowanie zmian konfiguracji do wszystkich agentów testowych może potrwać do 20 minut.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Wyświetlanie szczegółów testu. Edytuj i wyłącz test sieci Web." border="false":::

Podczas przeprowadzania konserwacji swojej usługi możesz wyłączyć testy dostępności lub skojarzone z nimi reguły alertów.

## <a name="if-you-see-failures"></a>Jeśli widzisz błędy

Wybierz czerwoną kropkę.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Zrzut ekranu przedstawiający kartę szczegółów transakcji typu end-to-end." border="false":::

W wyniku testu dostępności można wyświetlić szczegóły transakcji dla wszystkich składników. W tym miejscu możesz:

* Przejrzyj raport rozwiązywania problemów, aby ustalić, co może spowodować niepowodzenie testu, ale aplikacja jest nadal dostępna.
* Zbadać odpowiedź odebraną z serwera.
* Diagnozowanie błędów za pomocą skorelowanych danych telemetrycznych po stronie serwera zebranych podczas przetwarzania testu dostępności, który zakończył się niepowodzeniem.
* Rejestrowanie problemu lub elementu roboczego w usłudze Git Azure Boards celu śledzenia problemu. Błąd będzie zawierać link do tego zdarzenia.
* Otworzyć wynik testu sieci Web w programie Visual Studio.

Aby dowiedzieć się więcej na temat end-to-end transaction diagnostics experience, odwiedź [dokumentację diagnostyki transakcji.](./transaction-diagnostics.md)

Kliknij wiersz wyjątku, aby wyświetlić szczegóły wyjątku po stronie serwera, który spowodował niepowodzenie syntetycznego testu dostępności. Możesz również pobrać migawkę [debugowania w celu uzyskania](./snapshot-debugger.md) bardziej rozbudowanych danych diagnostycznych na poziomie kodu.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostyka po stronie serwera.":::

Oprócz nieprzetworzonych wyników można również wyświetlić dwie kluczowe metryki dostępności w Eksplorator metryk [:](../essentials/metrics-getting-started.md)

1. Dostępność: procent testów, które zostały pomyślnie zakończone, dla wszystkich wykonań testów.
2. Czas trwania testu: średni czas trwania testu dla wszystkich wykonań testów.

## <a name="automation"></a>Automation

* Automatyczne [konfigurowanie testów dostępności za pomocą skryptów środowiska PowerShell](./powershell.md#add-an-availability-test).
* Konfigurowanie [elementu webhook](../alerts/alerts-webhooks.md) który jest wywoływany przy zgłaszaniu alertu.


## <a name="next-steps"></a>Następne kroki

* [Alerty dostępności](availability-alerts.md)
* [Wieloetapowe testy sieci Web](availability-multistep.md)
* [Rozwiązywanie problemów](troubleshoot-availability.md)
