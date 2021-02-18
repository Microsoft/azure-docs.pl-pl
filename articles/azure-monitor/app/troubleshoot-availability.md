---
title: Rozwiązywanie problemów z testami dostępności usługi Azure Application Insights
description: Rozwiązywanie problemów z testami sieci Web w usłudze Azure Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/19/2020
ms.reviewer: sdash
ms.openlocfilehash: 849701caf73a4fb289773c67bccaab2e0f39dbd1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583724"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten artykuł pomoże w rozwiązywaniu typowych problemów, które mogą wystąpić podczas korzystania z monitorowania dostępności.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Rozwiązywanie problemów z instrukcjami raportów dla testów ping

Raport Rozwiązywanie problemów umożliwia łatwe diagnozowanie typowych problemów, które powodują niepowodzenie **testów ping** .

![Animacja przechodzenia z karty dostępność poprzez wybranie niepowodzenia do końca szczegółowej transakcji w celu wyświetlenia raportu rozwiązywania problemów](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. Na karcie dostępność zasobu Application Insights wybierz pozycję ogólne lub jeden z testów dostępności.
2. Wybierz pozycję **Niepowodzenie** , a następnie test w obszarze "Drąż do" po lewej stronie lub wybierz jeden z punktów na powierzchni wykresu punktowego.
3. Na stronie Szczegóły końca transakcji wybierz zdarzenie, a następnie w obszarze "Podsumowanie raportu o rozwiązywaniu problemów" Wybierz pozycję **[Przejdź do kroku]** , aby wyświetlić raport dotyczący rozwiązywania problemów.

> [!NOTE]
>  Jeśli jest obecny krok ponowne użycie połączenia, nie będą obecne rozwiązanie DNS, ustanowienie połączenia i transport TLS.

|Krok | Komunikat o błędzie | Możliwa przyczyna |
|-----|---------------|----------------|
| Ponowne użycie połączenia | n/d | Zwykle zależy od wcześniej ustanowionego połączenia, co oznacza, że krok testu sieci Web jest zależny. W związku z tym nie jest wymagany żaden krok DNS, połączenie lub SSL. |
| Rozpoznawanie nazw DNS | Nie można rozpoznać nazwy zdalnej: "adres URL" | Proces rozpoznawania nazw DNS nie powiódł się, najprawdopodobniej z powodu błędnych konfiguracji rekordów DNS lub tymczasowych błędów serwera DNS. |
| Ustanowienie połączenia | Próba nawiązania połączenia nie powiodła się, ponieważ połączona Strona nie odpowiedziała prawidłowo po upływie czasu. | Ogólnie mówiąc, oznacza to, że serwer nie odpowiada na żądanie HTTP. Typową przyczyną jest to, że nasi agenci testowi są blokowani przez zaporę na serwerze. Jeśli chcesz przeprowadzić test w ramach Virtual Network platformy Azure, Dodaj tag usługi dostępności do środowiska.|
| Transport TLS  | Klient i serwer nie mogą komunikować się, ponieważ nie mają wspólnego algorytmu.| Obsługiwane są tylko protokoły TLS 1,0, 1,1 i 1,2. Protokół SSL nie jest obsługiwany. Ten krok nie weryfikuje certyfikatów SSL i ustanawia tylko bezpieczne połączenie. Ten krok będzie wyświetlany tylko wtedy, gdy wystąpi błąd. |
| Nagłówek odpowiedzi odbioru | Nie można odczytać danych z połączenia transportowego. Połączenie zostało zamknięte. | Serwer zatwierdził błąd protokołu w nagłówku odpowiedzi. Na przykład połączenie zamknięte przez serwer, gdy odpowiedź nie jest w pełni. |
| Treść odpowiedzi otrzymującej | Nie można odczytać danych z połączenia transportowego: połączenie zostało zamknięte. | Serwer zatwierdził błąd protokołu w treści odpowiedzi. Na przykład połączenie zamknięte przez serwer, gdy odpowiedź nie jest w pełni odczytywane lub rozmiar fragmentu w treści odpowiedzi fragmentarycznej jest nieprawidłowy. |
| Sprawdzanie poprawności limitu przekierowań | Ta strona sieci Web ma za dużo przekierowań. Ta pętla zostanie zakończona w tym miejscu, ponieważ to żądanie przekroczyło limit dla przekierowań. | Istnieje ograniczenie 10 przekierowań na test. |
| Sprawdzanie poprawności kodu stanu | `200 - OK` nie jest zgodny z oczekiwanym stanem `400 - BadRequest` . | Zwrócony kod stanu, który jest liczony jako powodzenie. Kod 200 oznacza, że została zwrócona normalna strona sieci Web. |
| Weryfikacja zawartości | W odpowiedzi nie pojawił się wymagany tekst "Hello". | Ciąg nie jest dokładnym odpowiednikiem uwzględniania wielkości liter w odpowiedzi, na przykład ciąg "Welcome!". Musi to być zwykły ciąg, bez symboli wieloznacznych (na przykład gwiazdka). Jeśli zawartość strony ulegnie zmianie, może być konieczne zaktualizowanie ciągu. Tylko znaki angielskie są obsługiwane z dopasowaniem zawartości. |
  
## <a name="common-troubleshooting-questions"></a>Często zadawane pytania dotyczące rozwiązywania problemów

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Witryna wygląda prawidłowo, ale występują niepowodzenia testów Dlaczego Application Insights alerty?

   * Czy test ma włączone **zależne żądania** ? Powoduje to ścisłą kontrolę nad zasobami, takimi jak skrypty, obrazy itp. Te typy błędów mogą nie być zauważalne w przeglądarce. Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli którekolwiek z nich nie powiedzie się, test zostanie zgłoszony jako zakończony niepowodzeniem, nawet jeśli główna strona HTML zostanie załadowana bez problemu. Aby desensitize test na takie błędy zasobów, po prostu usuń zaznaczenie opcji Analizuj zależne żądania z konfiguracji testu.

   * Aby zmniejszyć szanse szumu z przejściowej Blips sieci itp. Upewnij się, że jest zaznaczone pole wyboru Włącz ponowną próbę konfiguracji błędów testów. Możesz również testować z większej liczby lokalizacji i odpowiednio zarządzać progiem reguły alertu, aby zapobiec problemom związanym z lokalizacją powodującym niewłaściwe alerty.

   * Kliknij dowolny z czerwonych kropek w środowisku obsługi wykresu punktowego dostępności lub dowolny błąd dostępności z Eksploratora wyszukiwania, aby zobaczyć szczegóły przyczyny zgłoszenia błędu. Wynik testu wraz ze skorelowanej telemetrii po stronie serwera (jeśli jest włączony) powinien pomóc w zrozumieniu przyczyny niepowodzenia testu. Typowymi przyczynami problemów przejściowych są problemy z siecią lub połączeniem.

   * Czy Przekroczono limit czasu testu? Przerywamy testy po 2 minutach. Jeśli test polecenia ping lub wieloetapowego trwa dłużej niż 2 minuty, raport zostanie wysłany jako błąd. Rozważ przerwanie testu do wielu, które mogą być wykonane w krótszych okresach.

   * Czy wszystkie lokalizacje zgłaszają błąd lub tylko niektóre z nich? W przypadku niektórych zgłoszonych błędów może to być spowodowane problemami z siecią/usługą CDN. Ponownie klikając czerwoną kropkę, należy pomóc zrozumieć, dlaczego lokalizacja zgłasza błędy.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nie otrzymałem wiadomości e-mail po wyzwoleniu lub rozwiązaniu alertu?

Sprawdź konfigurację klasycznych alertów, aby potwierdzić, że poczta e-mail została wyświetlona bezpośrednio, lub listę dystrybucyjną, która jest skonfigurowana do odbierania powiadomień. Jeśli tak jest, sprawdź konfigurację listy dystrybucyjnej, aby potwierdzić, że może odbierać zewnętrzne wiadomości e-mail. Sprawdź również, czy administrator poczty mogą mieć skonfigurowane zasady, które mogą spowodować ten problem.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nie otrzymałem powiadomienia elementu webhook?

Upewnij się, że aplikacja otrzymująca powiadomienie elementu webhook jest dostępna i pomyślnie przetwarza żądania elementu webhook. Aby uzyskać więcej informacji, zobacz [ten](../alerts/alerts-log-webhook.md) temat.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Otrzymuję błędy dotyczące niedostępności do 403. co to znaczy?

Ten błąd wskazuje, że należy dodać wyjątki zapory, aby umożliwić agentom dostępności testowanie docelowego adresu URL. Aby uzyskać pełną listę adresów IP agentów do zezwalania, zapoznaj się z [artykułem dotyczącym wyjątku IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Sporadyczne niepowodzenia testu z błędem naruszenia protokołu

Błąd „Naruszenie protokołu (...) Po CR musi występować LF” oznacza problem związany z serwerem (lub zależnościami). Występuje w przypadku ustawienia nieprawidłowo sformułowanych nagłówków w odpowiedzi. Przyczyną mogą być moduły równoważenia obciążenia lub sieci dostarczania zawartości. W związku z tym niektóre nagłówki mogą nie używać CRLF, aby wskazać koniec wiersza, co narusza specyfikację protokołu HTTP i dlatego nie można przeprowadzić walidacji na poziomie żądania programu .NET WebRequest. Zbadaj odpowiedzi na nagłówki, które mogą naruszać naruszenie.

> [!NOTE]
> Adres URL może zakończyć się niepowodzeniem w przeglądarkach, które mają swobodną weryfikację nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nie widzę żadnej powiązanej telemetrii po stronie serwera w celu zdiagnozowania niepowodzeń testów? *

Jeśli usługa Application Insights została skonfigurowana dla aplikacji po stronie serwera, może to być spowodowane trwaniem [próbkowania](./sampling.md). Wybierz inny wynik dostępności.

### <a name="can-i-call-code-from-my-web-test"></a>Czy mogę wywołać kod z mojego testu sieci Web?

Nie. Kroki testu muszą być zawarte w pliku .webtest. Nie można też wywoływać innych testów sieci Web ani używać pętli. Istnieje jednak kilka wtyczek, które mogą być przydatne.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Czym różnią się „testy sieci Web” i „testy dostępności”?

Te dwa terminy mogą być używane zamiennie. Testy dostępności to bardziej ogólny termin, który, oprócz wieloetapowych testów witryny, obejmuje testy ping pojedynczego adresu URL.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chcę użyć testów dostępności na naszym serwerze wewnętrznym działającym za zaporą.

   Istnieją dwa możliwe rozwiązania:

   * Skonfiguruj zaporę, aby zezwolić na żądania przychodzące z [adresów IP naszych agentów testów sieci Web](./ip-addresses.md).
   * Napisz własny kod do okresowego testowania wewnętrznego serwera. Uruchom kod jako proces w tle na serwerze testowym za zaporą. Proces testowania może wysyłać wyniki do usługi Application Insights za pomocą interfejsu API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) w podstawowym zestawie SDK. Wymaga to, aby serwer testowy miał dostęp do połączeń wychodzących punktu końcowego pozyskiwania usługi Application Insights, ale stanowi to dużo mniejsze zagrożenie bezpieczeństwa niż alternatywne dopuszczenie żądań przychodzących. Wyniki pojawią się w blokach testów dostępności sieci Web, ale środowisko zostanie nieco uproszczone od tego, co jest dostępne dla testów utworzonych za pośrednictwem portalu. Niestandardowe testy dostępności będą również wyświetlane jako wyniki dostępności w obszarze Analiza, wyszukiwanie i metryki.

### <a name="uploading-a-multi-step-web-test-fails"></a>Przekazywanie wieloetapowego testu sieci web kończy się niepowodzeniem.

Przyczyny mogą być następujące:
   * Limit rozmiaru to 300 KB.
   * Pętle nie są obsługiwane.
   * Odwołania do innych testów sieci Web nie są obsługiwane.
   * Źródła danych nie są obsługiwane.

### <a name="my-multi-step-test-doesnt-complete"></a>Mój test wieloetapowy nie jest wykonywany w całości

Istnieje limit 100 żądań na test. Test zostanie zatrzymany, jeśli działa dłużej niż dwie minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak uruchomić test z wykorzystaniem certyfikatów klienta?

Nie jest to obecnie obsługiwane.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje powiadomienia o alertach (klasyczny)?

Ta sekcja ma zastosowanie tylko do klasycznych alertów i pomoże zoptymalizować swoje powiadomienia o alertach, aby upewnić się, że tylko żądani adresaci otrzymają powiadomienia. Aby dowiedzieć się więcej o różnicach między [klasycznymi alertami](../alerts/alerts-classic.overview.md)i nowym działaniem alertów, zapoznaj się z [artykułem przegląd alertów](../alerts/alerts-overview.md). Aby sterować powiadomieniami o alertach w nowych działaniach związanych z alertami, użyj [grup akcji](../alerts/action-groups.md).

* Zalecamy użycie określonych odbiorców w przypadku klasycznych powiadomień o alertach.

* W przypadku alertów dotyczących błędów z lokalizacji X poza Y, opcja **Zbiorcza/Grupa** pole wyboru, jeśli jest włączone, wysyła do użytkowników z rolami administratora/współadministratora.  Zasadniczo _Wszyscy_ Administratorzy _subskrypcji_ otrzymają powiadomienia.

* W przypadku alertów dotyczących metryk dostępności opcja pola wyboru **Zbiorcza/Grupa** , jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w subskrypcji. W efekcie _Wszyscy_ użytkownicy z dostępem do subskrypcji, w której znajduje się zasób Application Insights, znajdują się w zakresie i będą otrzymywać powiadomienia. 

> [!NOTE]
> Jeśli obecnie używasz opcji **zbiorczych/grupowych** pól wyboru i go wyłączysz, nie będzie można przywrócić zmiany.

Jeśli musisz powiadomić użytkowników na podstawie ich ról, Użyj nowego środowiska alertu/alertów w czasie rzeczywistym. Za pomocą [grup akcji](../alerts/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolnymi rolami współautor/właściciela/czytnika (nie razem ze sobą jako pojedynczą opcją).

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testowanie sieci Web](availability-multistep.md)
* [Testy ping adresu URL](monitor-web-app-availability.md)