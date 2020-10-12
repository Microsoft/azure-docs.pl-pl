---
title: Rozwiązywanie problemów z testami dostępności usługi Azure Application Insights
description: Rozwiązywanie problemów z testami sieci Web w usłudze Azure Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318151"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten artykuł pomoże w rozwiązywaniu typowych problemów, które mogą wystąpić podczas korzystania z monitorowania dostępności.

## <a name="ssltls-errors"></a>Błędy protokołu SSL/TLS

|Objaw/komunikat o błędzie| Możliwe przyczyny|
|--------|------|
|Nie można utworzyć bezpiecznego kanału SSL/TLS  | Wersja protokołu SSL. Obsługiwane są tylko protokoły TLS 1,0, 1,1 i 1,2. **Protokół SSLv3 nie jest obsługiwana.**
|Warstwa rekordu TLSv 1.2: Alert (poziom: krytyczny, opis: zły rekord MAC)| Aby uzyskać [więcej informacji](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake), zobacz wątek stackexchange.
|Adres URL, który kończy się niepowodzeniem, to sieć CDN (Content Delivery Network) | Przyczyną może być niepodzielna konfiguracja w sieci CDN |  

### <a name="possible-workaround"></a>Możliwe obejście

* Jeśli adresy URL, na których występuje problem, są zawsze zasobami zależnymi, zalecane jest wyłączenie **zależnych od analizy żądań** dla testu sieci Web.

## <a name="test-fails-only-from-certain-locations"></a>Test kończy się niepowodzeniem z określonych lokalizacji

|Objaw/komunikat o błędzie| Możliwe przyczyny|
|----|---------|
|Próba nawiązania połączenia nie powiodła się, ponieważ połączona Strona nie odpowiedziała prawidłowo po upływie czasu  | Agenci testowi w określonych lokalizacjach są blokowane przez zaporę.|
|    |Przekierowywanie określonych adresów IP odbywa się za pośrednictwem usług równoważenia obciążenia, menedżerów ruchu geograficznego, trasy usługi Azure Express. 
|    |W przypadku korzystania z usługi Azure ExpressRoute istnieją scenariusze, w których można porzucić pakiety w przypadkach, gdy [występuje Routing asymetryczny](../../expressroute/expressroute-asymmetric-routing.md).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Niepowodzenie testu z powodu błędu naruszenia protokołu

|Objaw/komunikat o błędzie| Możliwe przyczyny| Możliwe rozwiązania |
|----|---------|-----|
|Serwer zatwierdził naruszenie protokołu. Sekcja = ResponseHeader detail = CR musi następować LF | Dzieje się tak w przypadku wykrycia źle sformułowanych nagłówków. W związku z tym niektóre nagłówki mogą nie używać CRLF do wskazania końca wiersza, co narusza specyfikację protokołu HTTP. Application Insights wymusza tę specyfikację protokołu HTTP i niepowodzenia odpowiedzi z nieprawidłowymi nagłówkami.| a. Skontaktuj się z dostawcą dostawcy usług sieci Web/dostawcy usługi CDN w celu rozwiązania uszkodzonych serwerów. <br> b. W przypadku nieudanych żądań są zasoby (np. pliki stylów, obrazy, skrypty), dlatego można rozważyć wyłączenie analizy zależnych żądań. Należy pamiętać, że w takim przypadku utracisz możliwość monitorowania dostępności tych plików.

> [!NOTE]
> Adres URL może zakończyć się niepowodzeniem w przeglądarkach, które mają swobodną weryfikację nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Często zadawane pytania dotyczące rozwiązywania problemów

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Witryna wygląda prawidłowo, ale występują niepowodzenia testów Dlaczego Application Insights alerty?

   * Czy test ma włączone **zależne żądania** ? Powoduje to ścisłą kontrolę nad zasobami, takimi jak skrypty, obrazy itp. Te typy błędów mogą nie być zauważalne w przeglądarce. Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli którekolwiek z nich nie powiedzie się, test zostanie zgłoszony jako zakończony niepowodzeniem, nawet jeśli główna strona HTML zostanie załadowana bez problemu. Aby desensitize test na takie błędy zasobów, po prostu usuń zaznaczenie opcji Analizuj zależne żądania z konfiguracji testu.

   * Aby zmniejszyć szanse szumu z przejściowej Blips sieci itp. Upewnij się, że jest zaznaczone pole wyboru Włącz ponowną próbę konfiguracji błędów testów. Możesz również testować z większej liczby lokalizacji i odpowiednio zarządzać progiem reguły alertu, aby zapobiec problemom związanym z lokalizacją powodującym niewłaściwe alerty.

   * Kliknij dowolną czerwoną kropkę ze względu na dostępność lub dowolny błąd dostępności z Eksploratora wyszukiwania, aby zobaczyć szczegóły przyczyny zgłoszenia błędu. Wynik testu wraz ze skorelowanej telemetrii po stronie serwera (jeśli jest włączony) powinien pomóc w zrozumieniu przyczyny niepowodzenia testu. Typowymi przyczynami problemów przejściowych są problemy z siecią lub połączeniem.

   * Czy Przekroczono limit czasu testu? Przerywamy testy po 2 minutach. Jeśli test polecenia ping lub wieloetapowego trwa dłużej niż 2 minuty, raport zostanie wysłany jako błąd. Rozważ przerwanie testu do wielu, które mogą być wykonane w krótszych okresach.

   * Czy wszystkie lokalizacje zgłaszają błąd lub tylko niektóre z nich? W przypadku niektórych zgłoszonych błędów może to być spowodowane problemami z siecią/usługą CDN. Ponownie klikając czerwoną kropkę, należy pomóc zrozumieć, dlaczego lokalizacja zgłasza błędy.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nie otrzymałem wiadomości e-mail po wyzwoleniu lub rozwiązaniu alertu?

Sprawdź konfigurację klasycznych alertów, aby potwierdzić, że poczta e-mail została wyświetlona bezpośrednio, lub listę dystrybucyjną, która jest skonfigurowana do odbierania powiadomień. Jeśli tak jest, sprawdź konfigurację listy dystrybucyjnej, aby potwierdzić, że może odbierać zewnętrzne wiadomości e-mail. Sprawdź również, czy administrator poczty mogą mieć skonfigurowane zasady, które mogą spowodować ten problem.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nie otrzymałem powiadomienia elementu webhook?

Upewnij się, że aplikacja otrzymująca powiadomienie elementu webhook jest dostępna i pomyślnie przetwarza żądania elementu webhook. Aby uzyskać więcej informacji, zobacz [ten](../platform/alerts-log-webhook.md) temat.

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

Ta sekcja ma zastosowanie tylko do klasycznych alertów i pomoże zoptymalizować swoje powiadomienia o alertach, aby upewnić się, że tylko żądani adresaci otrzymają powiadomienia. Aby dowiedzieć się więcej o różnicach między [klasycznymi alertami](../platform/alerts-classic.overview.md)i nowym działaniem alertów, zapoznaj się z [artykułem przegląd alertów](../platform/alerts-overview.md). Aby sterować powiadomieniami o alertach w nowych działaniach związanych z alertami, użyj [grup akcji](../platform/action-groups.md).

* Zalecamy użycie określonych odbiorców w przypadku klasycznych powiadomień o alertach.

* W przypadku alertów dotyczących błędów z lokalizacji X poza Y, opcja **Zbiorcza/Grupa** pole wyboru, jeśli jest włączone, wysyła do użytkowników z rolami administratora/współadministratora.  Zasadniczo _Wszyscy_ Administratorzy _subskrypcji_ otrzymają powiadomienia.

* W przypadku alertów dotyczących metryk dostępności opcja pola wyboru **Zbiorcza/Grupa** , jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w subskrypcji. W efekcie _Wszyscy_ użytkownicy z dostępem do subskrypcji, w której znajduje się zasób Application Insights, znajdują się w zakresie i będą otrzymywać powiadomienia. 

> [!NOTE]
> Jeśli obecnie używasz opcji **zbiorczych/grupowych** pól wyboru i go wyłączysz, nie będzie można przywrócić zmiany.

Jeśli musisz powiadomić użytkowników na podstawie ich ról, Użyj nowego środowiska alertu/alertów w czasie rzeczywistym. Za pomocą [grup akcji](../platform/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolnymi rolami współautor/właściciela/czytnika (nie razem ze sobą jako pojedynczą opcją).

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testowanie sieci Web](availability-multistep.md)
* [Testy ping adresu URL](monitor-web-app-availability.md)

