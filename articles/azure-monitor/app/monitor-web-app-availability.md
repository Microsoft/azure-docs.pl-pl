---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: b0f66608c6e0f23b861e207d0dea07a546b41c2a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937422"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorowanie dostępności dowolnej witryny sieci Web

Po wdrożeniu aplikacji lub witryny sieci Web można skonfigurować testy cykliczne, aby monitorować dostępność i czas odpowiedzi. Usługa [Azure Application Insights](./app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Może to być alert, jeśli aplikacja nie odpowiada, lub jeśli reaguje zbyt wolno.

Testy dostępności możesz skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet. Nie musisz wprowadzać żadnych zmian w testowanej witrynie sieci Web. W rzeczywistości nie musi to być jeszcze posiadana witryna. Możesz przetestować dostępność interfejsu API REST, od którego zależy usługa.

### <a name="types-of-availability-tests"></a>Typy testów dostępności:

Istnieją trzy typy testów dostępności:

* [Test ping adresu URL](#create-a-url-ping-test): prosty test, który można utworzyć w portalu Azure.
* [Wieloetapowy test sieci Web](availability-multistep.md): nagrywanie sekwencji żądań sieci Web, które mogą być odtwarzane w celu przetestowania bardziej złożonych scenariuszy. Wieloetapowe testy sieci Web są tworzone w Visual Studio Enterprise i przekazywane do portalu w celu wykonania.
* [Testy dostępności niestandardowej ścieżki](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): Jeśli zdecydujesz się utworzyć niestandardową aplikację do uruchamiania testów dostępności, `TrackAvailability()` Metoda może zostać użyta do wysłania wyników do Application Insights.

**Można utworzyć maksymalnie 100 testów dostępności dla zasobu Application Insights.**

> [!IMPORTANT]
> Zarówno [test ping adresu URL](#create-a-url-ping-test) , jak i [wieloetapowy test sieci Web](availability-multistep.md) polegają na publicznej infrastrukturze internetowego DNS, aby rozpoznać nazwy domen przetestowanych punktów końcowych. Oznacza to, że jeśli używasz Prywatna strefa DNS, musisz upewnić się, że wszystkie nazwy domeny testu są również rozpoznawalne przez publiczne serwery nazw domen lub, gdy nie jest to możliwe, można użyć zamiast nich [testów dostępności niestandardowej ścieżki](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) .

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby można było utworzyć test dostępności, należy najpierw utworzyć zasób Application Insights. Jeśli zasób został już utworzony, przejdź do następnej sekcji, aby [utworzyć test ping adresu URL](#create-a-url-ping-test).

W Azure Portal wybierz pozycję **Utwórz zasób**  >  **Narzędzia deweloperskie**  >  **Application Insights** i [Utwórz zasób Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Tworzenie testu ping adresu URL

Nazwa "test ping adresu URL" jest bitem elementu Misnomer. Aby można było wyczyścić Ten test, nie jest możliwe użycie protokołu ICMP (Internet Control Message Protocol) do sprawdzenia dostępności witryny. Zamiast tego używa bardziej zaawansowanych funkcji żądania HTTP, aby sprawdzić, czy punkt końcowy odpowiada. Mierzy również wydajność skojarzoną z tą odpowiedzią i dodaje możliwość ustawienia niestandardowych kryteriów sukcesu, które są połączone z bardziej zaawansowanymi funkcjami, takimi jak analizowanie żądań zależnych i Zezwalanie na ponowne próby.

Aby utworzyć pierwsze żądanie dostępności, Otwórz okienko dostępność i wybierz pozycję **Utwórz test**.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Tworzenie testu

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Adres URL** |  Adres URL może odnosić się do dowolnej strony sieci Web, którą chcesz przetestować, ale musi być widoczny w publicznym Internecie. Adres URL może zawierać ciąg zapytania. Możesz więc np. szybko sprawdzić działanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.|
|**Analizuj zależne żądania**| Testuje obrazy, skrypty, pliki stylów i inne pliki, które są częścią strony sieci Web w teście. Rejestrowany czas odpowiedzi obejmuje czas poświęcony na pobieranie tych plików. Test kończy się niepowodzeniem, jeśli nie można pomyślnie pobrać któregokolwiek z tych zasobów w ramach limitu czasu dla całego testu. Jeśli pole opcji nie zostanie zaznaczone, test zażąda tylko pliku pod podanym adresem URL. Włączenie tej opcji skutkuje dokładniejszym sprawdzeniem. Test może zakończyć się niepowodzeniem, co może nie być zauważalne podczas ręcznego przeglądania witryny.
|**Włącz ponawianie prób**|gdy test zakończy się niepowodzeniem, zostanie ponowiony po krótkim czasie. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. **Zalecamy korzystanie z tej opcji**. Średnio około 80% błędów znika po ponowieniu testu.|
|**Częstotliwość testów**| Ustawia częstotliwość uruchamiania testu z każdej lokalizacji testowej. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testów**| Są miejsca, w których serwery wysyłają żądania sieci Web do adresu URL. **Minimalna liczba zalecanych lokalizacji testowych to pięć** , aby upewnić się, że problemy w witrynie sieci Web można odróżnić od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

**Jeśli Twój adres URL nie jest widoczny w publicznej sieci Internet, możesz wybrać opcję selektywnego otwierania zapory, aby zezwalać tylko na transakcje testowe za pośrednictwem programu**. Aby dowiedzieć się więcej o wyjątkach zapory dla naszych agentów testów dostępności, zapoznaj się z [przewodnikiem po adresie IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Zdecydowanie zalecamy testowanie z wielu lokalizacji z **co najmniej pięcioma lokalizacjami**. Ma to na celu uniknięcie fałszywych alarmów, które mogą powodować przejściowe problemy z określoną lokalizacją. Ponadto firma Microsoft ustaliła, że optymalna konfiguracja ma mieć **liczbę lokalizacji testowych równą progowi lokalizacji alertu + 2**.

### <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako powodzenie. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, na przykład "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Zgodność zawartości obsługuje tylko znaki angielskie** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Zalecamy korzystanie z alertów niemal w czasie rzeczywistym. Konfigurowanie tego typu alertu odbywa się po utworzeniu testu dostępności.  |
|**Motyw** | Nie zalecamy już używania klasycznych alertów dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalna relacja między progiem lokalizacji alertu a liczbą lokalizacji testowych to liczba lokalizacji testu w **lokalizacji alertów**  =  **-2 z co najmniej pięcioma lokalizacjami testów.**|

### <a name="location-population-tags"></a>Tagi populacji lokalizacji

Poniższe Tagi populacji mogą być używane dla atrybutu lokalizacji geograficznej podczas wdrażania testu ping adresu URL dostępności przy użyciu Azure Resource Manager.

#### <a name="azure-gov"></a>Azure gov

| Nazwa wyświetlana   | Nazwa populacji     |
|----------------|---------------------|
| USGov Wirginia | USGov-VA-AZR        |
| USGov Arizona  | USGov-PHX-AZR       |
| USGov Teksas    | USGov-TX-AZR        |
| USDoD wschód     | USGov-ddeast-AZR    |
| USDoD środkowe  | USGov-ddcentral-AZR |

#### <a name="azure"></a>Azure

| Nazwa wyświetlana                           | Nazwa populacji   |
|----------------------------------------|-------------------|
| Australia Wschodnia                         | EMEA-au-SYD-Edge  |
| Brazylia Południowa                           | Latam-br-gru-Edge |
| Central US                             | US-FL-Mia-Edge    |
| Azja Wschodnia                              | Azja i Pacyfik-HK-HKN-AZR   |
| East US                                | US-VA-Ash-AZR     |
| Francja Południowa (dawniej Francja Środkowa) | EMEA-ch-ZRH-Edge  |
| Francja Środkowa                         | EMEA — fr-pra-Edge  |
| Japonia Wschodnia                             | Azja i Pacyfik-JP-kaw-Edge  |
| Europa Północna                           | EMEA-GB-DB3-AZR   |
| Północno-środkowe stany USA                       | US-Il-CH1-AZR     |
| South Central US                       | US-TX-SN1-AZR     |
| Southeast Asia                         | Azja i Pacyfik-SG-Sin-AZR   |
| Zachodnie Zjednoczone Królestwo                                | EMEA — SE-konie-Edge  |
| West Europe                            | EMEA-NL-AMS-AZR   |
| Zachodnie stany USA                                | US-CA-SJC-AZR     |
| Południowe Zjednoczone Królestwo                               | EMEA-ru-MSA — brzeg  |

## <a name="see-your-availability-test-results"></a>Wyświetlanie wyników testów dostępności

Wyniki testu dostępności można wizualizować przy użyciu widoków liniowych i punktowych.

Po kilku minutach kliknij przycisk **Odśwież** , aby zobaczyć wyniki testu.

![Zrzut ekranu przedstawia stronę dostępność z wyróżnionym przyciskiem odświeżania.](./media/monitor-web-app-availability/availability-refresh-002.png)

Widok scatterplot pokazuje próbki wyników testu, które mają w nich szczegółową czynność testu diagnostycznego. Aparat testowy przechowuje szczegółowe informacje diagnostyczne dla testów z błędami. W przypadku udanych testów szczegółowe informacje diagnostyczne są przechowywane dla podzbioru wykonań. Umieść kursor na dowolnym z zielonych i czerwonych kropek, aby zobaczyć test, nazwę testu i lokalizację.

![Widok wiersza](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Wybierz określony test, lokalizację lub skróć okres czasu, aby zobaczyć więcej wyników w okolicy interesującego okresu czasu. Użyj Eksploratora wyszukiwania, aby zobaczyć wyniki z wszystkich wykonań, lub użyj zapytań analitycznych w celu uruchomienia niestandardowych raportów dla tych danych.

## <a name="inspect-and-edit-tests"></a> Sprawdzanie i edytowanie testów

Aby edytować, tymczasowo wyłączyć lub usunąć test, kliknij wielokropek obok nazwy testu. Propagowanie zmian konfiguracji do wszystkich agentów testowych po wprowadzeniu zmiany może potrwać do 20 minut.

![Wyświetl szczegóły testu. Edytuj i Wyłącz test sieci Web](./media/monitor-web-app-availability/edit.png)

Podczas przeprowadzania konserwacji swojej usługi możesz wyłączyć testy dostępności lub skojarzone z nimi reguły alertów.

## <a name="if-you-see-failures"></a>Jeśli widzisz błędy

Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/monitor-web-app-availability/open-instance-3.png)

W wyniku testu dostępności można zobaczyć szczegóły transakcji dla wszystkich składników. W tym miejscu możesz:

* Zbadać odpowiedź odebraną z serwera.
* Diagnozowanie błędów ze skorelowanej telemetrii po stronie serwera podczas przetwarzania testu dostępności zakończonej niepowodzeniem.
* Zarejestruj problem lub element roboczy w usłudze Git lub Azure Boards, aby śledzić problem. Błąd będzie zawierać link do tego zdarzenia.
* Otworzyć wynik testu sieci Web w programie Visual Studio.

Więcej informacji na temat kompleksowego środowiska diagnostyki transakcji można znaleźć [tutaj](./transaction-diagnostics.md).

Kliknij wiersz wyjątku, aby wyświetlić szczegóły wyjątku po stronie serwera, który spowodował niepowodzenie testu dostępności syntetycznej. Możesz również uzyskać [migawkę debugowania](./snapshot-debugger.md) na potrzeby bardziej zaawansowanej diagnostyki poziomu kodu.

![Diagnostyka po stronie serwera](./media/monitor-web-app-availability/open-instance-4.png)

Oprócz nieprzetworzonych wyników można także wyświetlić dwie metryki dostępności klucza w [Eksplorator metryk](../platform/metrics-getting-started.md):

1. Dostępność: procent testów, które zostały pomyślnie zakończone, dla wszystkich wykonań testów.
2. Czas trwania testu: średni czas trwania testu dla wszystkich wykonań testów.

## <a name="automation"></a>Automation

* Automatyczne [konfigurowanie testów dostępności za pomocą skryptów środowiska PowerShell](./powershell.md#add-an-availability-test).
* Konfigurowanie [elementu webhook](../platform/alerts-webhooks.md) który jest wywoływany przy zgłaszaniu alertu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany artykuł dotyczący [rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Alerty dostępności](availability-alerts.md)
* [Wieloetapowe testy sieci Web](availability-multistep.md)

