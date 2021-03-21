---
title: Rozwiązywanie problemów ze wspólną aktualizacją urządzeń dla platformy Azure IoT Hub | Microsoft Docs
description: Ten dokument zawiera listę wskazówek i wskazówek ułatwiających zaradzenie wielu ewentualnych problemów z aktualizacją urządzeń dla IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030634"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Aktualizacja urządzenia dla IoT Hub Przewodnik rozwiązywania problemów

Ten dokument zawiera listę typowych pytań i problemów zgłoszonych przez użytkowników aktualizacji urządzeń. Gdy aktualizacja urządzenia odbywa się w publicznej wersji zapoznawczej, ten przewodnik rozwiązywania problemów będzie okresowo aktualizowany przy użyciu nowych pytań i rozwiązań. Jeśli wystąpi problem, którego nie ma w tym przewodniku rozwiązywania problemów, zapoznaj się z sekcją [kontaktowanie się pomoc techniczna firmy Microsoft](#contact) , aby udokumentować swoją sytuację.

## <a name="importing-updates"></a><a name="import"></a>Importowanie aktualizacji

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>P: mam problem z połączeniem mojego wystąpienia aktualizacji urządzenia z wystąpieniem IoT Hub.
_Upewnij się, że trasy komunikatów IoT Hub są prawidłowo skonfigurowane, zgodnie z dokumentacją [aktualizacji urządzenia](./device-update-resources.md) ._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Pytanie: Wystąpił błąd związany z rolą (komunikat o błędzie w Azure Portal lub błąd interfejsu API 403).
_Być może nie masz prawidłowo skonfigurowanych uprawnień dostępu. Upewnij się, że skonfigurowano uprawnienia dostępu poprawnie zgodnie z dokumentacją [kontroli dostępu do aktualizacji urządzenia](./device-update-control-access.md) ._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Pytanie: Wystąpił błąd 500 typu podczas importowania zawartości do usługi aktualizacji urządzenia.
_Kod błędu w zakresie 500 może wskazywać na problem z usługą aktualizacji urządzenia. Zaczekaj 5 minut, a następnie spróbuj ponownie. Jeśli ten sam błąd będzie się powtarzać, postępuj zgodnie z instrukcjami w sekcji [nawiązywanie kontaktu pomoc techniczna firmy Microsoft](#contact) , aby wysłać żądanie pomocy technicznej do firmy Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Pytanie: Wystąpił kod błędu podczas importowania zawartości i chcemy ją przeanalizować.
_Zapoznaj się z dokumentacją dotyczącą [kodów błędów aktualizacji urządzeń](./device-update-error-codes.md) , aby uzyskać informacje na temat analizowania kodów błędów._

## <a name="device-failures"></a><a name="device-failure"></a>Awarie urządzeń

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>P: Jak upewnić się, że urządzenie jest połączone z aktualizacją urządzenia dla IoT Hub?
_Możesz sprawdzić, czy urządzenie jest połączone z aktualizacją urządzenia, sprawdzając, czy jest ono widoczne w sekcji "urządzenia niezgrupowane" w widoku zgodność Azure Portal._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>P: nie można zaktualizować co najmniej jednego z moich urządzeń.
_Istnieje wiele możliwych głównych przyczyn niepowodzenia aktualizacji urządzenia. Sprawdź, czy urządzenie jest: 1) połączone z wystąpieniem IoT Hub, 2) połączone z wystąpieniem aktualizacji urządzenia i 3) Usługa optymalizacji dostarczania (DO) jest uruchomiona. Jeśli na urządzeniu są spełnione wszystkie trzy warunki, postępuj zgodnie z instrukcjami w sekcji [nawiązywanie kontaktu pomoc techniczna firmy Microsoft](#contact) , aby wysłać żądanie pomocy technicznej do firmy Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Wdrażanie aktualizacji

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>P: wdrożono aktualizację na urządzeniach, ale stan zgodności wskazuje, że nie jest ona Najnowsza. Co mam zrobić?
_Odświeżanie stanu zgodności urządzenia może potrwać do 5 minut. Zaczekaj, a następnie sprawdź ponownie._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>P: stan wdrożenia urządzenia wyświetla niezgodność, co należy zrobić?
_Właściwości producenta i modelu urządzenia wskazanego mogły zostać zmienione po podłączeniu urządzenia do IoT Hub, co spowoduje, że urządzenie zostanie uznane za niezgodne z aktualizacją zawartości bieżącego wdrożenia._

_Sprawdź [interfejs adu Core](./device-update-plug-and-play.md) , aby zobaczyć, co producent i model wysyła urządzenie do usługi aktualizacji urządzeń, i upewnij się, że jest on zgodny z producentem i modelem określonym w [manifeście importu](./import-concepts.md) wdrożonej zawartości aktualizacji. Te właściwości dla danego urządzenia można zmienić przy użyciu [pliku konfiguracji aktualizacji urządzenia](./device-update-configuration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>P: widzę, że moje wdrożenie znajduje się w fazie "aktywny", ale żadne z moich urządzeń nie jest "w toku" z aktualizacją. Co mam zrobić?
_Upewnij się, że data rozpoczęcia wdrożenia nie jest ustawiona w przyszłości. Podczas tworzenia nowego wdrożenia Data rozpoczęcia wdrożenia jest domyślnie przyrastana do następnego dnia jako zabezpieczenie, chyba że zostanie on jawnie zmieniony. Możesz poczekać na nadejście daty rozpoczęcia wdrożenia lub anulować bieżące wdrożenie i utworzyć nowe wdrożenie z wymaganą datą rozpoczęcia._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>P: próbuję zgrupować moje urządzenia, ale nie widzę znacznika na liście rozwijanej podczas tworzenia grupy.
_Upewnij się, że trasy komunikatów zostały poprawnie skonfigurowane w IoT Hub zgodnie z dokumentacją dotyczącą [zasobów aktualizacji urządzenia](./device-update-resources.md) . Po skonfigurowaniu trasy należy ponownie oznaczyć urządzenie._

_Innym główną przyczyną może być zastosowany tag przed połączeniem urządzenia z aktualizacją urządzenia dla IoT Hub. Upewnij się, że urządzenie zostało już połączone z aktualizacją urządzenia. Możesz sprawdzić, czy urządzenie jest połączone z aktualizacją urządzenia dla IoT Hub, sprawdzając, czy jest ono wyświetlane w obszarze "urządzenia niezgrupowane" w widoku zgodność. Tymczasowo Dodaj tag innej wartości, a następnie Dodaj swój zamierzony tag ponownie po nawiązaniu połączenia z urządzeniem._

_W przypadku korzystania z usługi Device Provisioning (DPS) Upewnij się, że znaczniki urządzeń są oznaczane po zainicjowaniu obsługi administracyjnej, a nie w trakcie procesu tworzenia urządzenia. Jeśli urządzenie zostało już oznaczone jako wybrane podczas kroku tworzenia urządzenia, należy tymczasowo oznaczyć urządzenie przy użyciu innej wartości po jej udostępnieniu, a następnie ponownie dodać zamierzony tag._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Pytanie: moje wdrożenie zostało ukończone pomyślnie, ale aktualizacja niektórych urządzeń nie powiodła się.
_Przyczyną może być błąd po stronie klienta na urządzeniach zakończonych niepowodzeniem. Zapoznaj się z sekcją błędy urządzeń w tym przewodniku rozwiązywania problemów._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>P: podczas próby zainicjowania wdrożenia Wystąpił błąd w interfejsie użytkownika.
_Może to być spowodowane przez usterkę usługi/środowiska użytkownika lub z powodu problemu z uprawnieniami interfejsu API. Postępuj zgodnie z instrukcjami w sekcji [kontaktowanie się pomoc techniczna firmy Microsoft](#contact) , aby wysłać żądanie pomocy technicznej do firmy Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>P: uruchomiono wdrożenie, ale nie osiągnie ono stanu końcowego.
_Przyczyną może być problem z wydajnością usługi, usterka usługi lub usterka klienta. Spróbuj ponownie wykonać wdrożenie po 10 minutach. W przypadku wystąpienia tego samego problemu należy ściągnąć dzienniki urządzeń i zapoznać się z sekcją błędy urządzeń w tym przewodniku rozwiązywania problemów. Jeśli ten sam problem będzie się powtarzać, postępuj zgodnie z instrukcjami w sekcji [nawiązywanie kontaktu pomoc techniczna firmy Microsoft](#contact) , aby wysłać żądanie pomocy technicznej do firmy Microsoft._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Pobieranie aktualizacji na urządzeniach

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>P: Jak mogę wznowić pobieranie, gdy urządzenie zostanie ponownie połączone po upływie okresu odłączenia?
_Pobieranie zostanie wznowione po przywróceniu łączności w okresie 24 godzin. Po 24 godzinach pobieranie musi zostać ponownie zainicjowane przez użytkownika._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Korzystanie z połączonej pamięci podręcznej firmy Microsoft (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>P: Wystąpił problem podczas próby wdrożenia modułu MCC na urządzeniu IoT Edge.
_Zapoznaj się z [dokumentacją IoT Edge]() dotyczącą wdrażania modułów brzegowych w IoT Edge urządzeniach. Aby sprawdzić, czy moduł MCC został uruchomiony pomyślnie na urządzeniu IoT Edge, przejdź do http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>P: jedno z moich urządzeń IoT próbuje pobrać aktualizację za poorednictwem MCC, ale kończy się niepowodzeniem.
_Istnieje kilka problemów, które mogą powodować niepowodzenie urządzenia IoT w połączeniu z usługą MCC. Aby zdiagnozować problem, Zbierz dzienniki klienta i Nginx na urządzeniu z niepowodzeniem (Aby uzyskać instrukcje dotyczące zbierania dzienników klientów), należy zapoznać się z sekcją [kontaktowanie się z usługą pomoc techniczna firmy Microsoft](#contact) ._

_Urządzenie może nie być w trakcie ściągania zawartości z Internetu w celu przekazania go do modułu MCC, ponieważ używany przez niego adres URL nie jest dozwolony. Aby określić, czy tak jest, należy sprawdzić zmienne środowiskowe IoT Edge w Azure Portal._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Kontaktowanie się z pomoc techniczna firmy Microsoft

Jeśli występują problemy, których nie można rozwiązać przy użyciu powyższych często zadawanych pytań, można wysłać żądanie pomocy technicznej za pomocą pomoc techniczna firmy Microsoft za pośrednictwem interfejsu Azure Portal. W zależności od kategorii, do której należy wskazać swój problem, może zostać wyświetlony monit o zebranie i udostępnienie dodatkowych danych w celu pomoc techniczna firmy Microsoft zbadania problemu. 

Aby uzyskać instrukcje dotyczące sposobu zbierania poszczególnych typów danych, zobacz poniżej. Można użyć [GetDevices]() , aby wyszukać dodatkowe informacje w odpowiedzi na ładunek interfejsu API.

Ponadto poniższe informacje mogą być przydatne w celu zawężenia głównej przyczyny problemu:
* Typ urządzenia, które próbujesz zaktualizować (Azure Percept, Brama IoT Edge, inne)
* Jakiego typu klienta aktualizacji urządzenia używasz (oparty na obrazach, pakiet, symulator)
* System operacyjny, na którym działa urządzenie
* Szczegóły dotyczące architektury urządzenia
* Czy aktualizacja urządzenia została pomyślnie użyta do zaktualizowania urządzenia przed

Jeśli masz jakiekolwiek z powyższych informacji, Dołącz je w opisie problemu.

### <a name="collecting-client-logs"></a>Zbieranie dzienników klienta

* Na urządzeniu Raspberry Pi można znaleźć dwa zestawy dzienników:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* W przypadku spakowanego klienta dzienniki są dostępne tutaj:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* W przypadku symulatora dzienniki są dostępne tutaj:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Kody błędów
Może zostać wyświetlony monit o podanie kodów błędów podczas zgłaszania problemu związanego z importowaniem aktualizacji, awarią urządzenia lub wdrożeniem aktualizacji.

Kody błędów można uzyskać, przeglądając Interfejs [ADUCoreInterface](./device-update-plug-and-play.md) . Zapoznaj się z dokumentacją dotyczącą [kodów błędów aktualizacji urządzeń](./device-update-error-codes.md) , aby uzyskać informacje na temat analizowania kodów błędów w celu samodzielnej diagnostyki i rozwiązywania problemów.

### <a name="trace-id"></a>Identyfikator śledzenia
Może zostać wyświetlony monit o podanie identyfikatora śledzenia podczas zgłaszania problemu związanego z importowaniem lub wdrażaniem aktualizacji.

Identyfikator śledzenia dla danego działania użytkownika można znaleźć w odpowiedzi interfejsu API lub w sekcji Historia importu w interfejsie użytkownika Azure Portal. 

Obecnie identyfikatory śledzenia dla akcji wdrożenia są dostępne tylko za pomocą odpowiedzi interfejsu API.

### <a name="deployment-id"></a>Identyfikator wdrożenia
Może zostać wyświetlony monit o podanie identyfikatora wdrożenia podczas zgłaszania problemu związanego z wdrażaniem aktualizacji.

Identyfikator wdrożenia jest tworzony przez użytkownika podczas wywoływania interfejsu API w celu zainicjowania wdrożenia.

Obecnie identyfikatory wdrożeń dla wdrożeń inicjowanych z interfejsu użytkownika Azure Portal są generowane automatycznie i nie są przeznaczone do użytkownika.

### <a name="iot-hub-instance-name"></a>Nazwa wystąpienia IoT Hub
Może zostać wyświetlony monit o podanie nazwy wystąpienia IoT Hub podczas zgłaszania problemu związanego z awariami urządzeń lub wdrożeniem aktualizacji.

Nazwa IoT Hub jest wybierana przez użytkownika podczas pierwszego aprowizacji.

### <a name="device-update-account-name"></a>Nazwa konta aktualizacji urządzenia
Może zostać wyświetlony monit o podanie nazwy konta aktualizacji urządzenia podczas zgłaszania problemu związanego z importowaniem aktualizacji, awariami urządzeń lub wdrożeniem aktualizacji.

Nazwa konta aktualizacji urządzenia jest wybierana przez użytkownika podczas pierwszego rejestracji w usłudze. Więcej informacji można znaleźć w dokumentacji dotyczącej [zasobów aktualizacji urządzenia](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Nazwa wystąpienia aktualizacji urządzenia
Może zostać wyświetlony monit o podanie nazwy wystąpienia aktualizacji urządzenia podczas zgłaszania problemu związanego z importowaniem aktualizacji, awariami urządzeń lub wdrożeniem aktualizacji.

Nazwa wystąpienia aktualizacji urządzenia jest wybierana przez użytkownika podczas pierwszego aprowizacji. Więcej informacji można znaleźć w dokumentacji dotyczącej [zasobów aktualizacji urządzenia](./device-update-resources.md) .

### <a name="device-id"></a>Identyfikator urządzenia
Może zostać wyświetlony monit o podanie identyfikatora urządzenia podczas zgłaszania problemu związanego z awariami urządzeń lub wdrożeniem aktualizacji.

Identyfikator urządzenia jest definiowany przez klienta, gdy urządzenie jest inicjowane po raz pierwszy. Można go również pobrać z sznurka urządzenia urządzenia.

### <a name="update-id"></a>Identyfikator aktualizacji
Może zostać wyświetlony monit o podanie identyfikatora aktualizacji podczas zgłaszania problemu związanego z wdrażaniem aktualizacji.

Identyfikator aktualizacji jest definiowany przez klienta podczas inicjowania wdrożenia.

### <a name="nginx-logs"></a>Dzienniki Nginx
Może pojawić się prośba o podanie dzienników Nginx podczas zgłaszania problemu związanego z podłączoną przez firmę Microsoft pamięcią podręczną.

### <a name="adu-conftxt"></a>ADU-conf.txt
Może zostać wyświetlony monit o podanie pliku konfiguracji aktualizacji urządzenia ("adu-conf.txt") w przypadku zgłaszania problemu związanego z wdrażaniem aktualizacji.

Plik konfiguracji jest opcjonalny i tworzony przez użytkownika zgodnie z instrukcjami w dokumentacji dotyczącej [konfiguracji aktualizacji urządzenia](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Importuj manifest
Może zostać wyświetlony monit o podanie pliku manifestu importowania podczas zgłaszania problemu związanego z importowaniem lub wdrażaniem aktualizacji.

Manifest importowania to plik utworzony przez klienta podczas importowania zawartości aktualizacji do usługi aktualizacji urządzenia.

**[Następny krok: Dowiedz się więcej o kodach błędów aktualizacji urządzeń](.\device-update-error-codes.md)**