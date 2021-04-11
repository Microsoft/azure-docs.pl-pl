---
title: Typy alertów i opisy
description: Przejrzyj opisy alertów usługi Defender for IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 4/8/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9ef7aa388d0f25adcafec1cb4a5b38dcfb8597a1
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210469"
---
# <a name="alert-types-and-descriptions"></a>Typy alertów i opisy

W tym artykule opisano wszystkie typy alertów, które mogą być generowane przez aparaty usługi Defender for IoT. W oknie alerty są wyświetlane alerty, które umożliwiają zarządzanie zdarzeniem alertu. 

## <a name="policy-engine-alerts"></a>Alerty aparatu zasad

Alerty aparatu zasad opisują odchylenia od zachowań sieci linii bazowej.

| Tytuł  | Opis | Ważność |
|--|--|--|
| Nieprawidłowe użycie adresów MAC | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało ono autoryzowane. | Mały |
| Beckhoff zmieniono oprogramowanie | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Logowanie do bazy danych nie powiodło się | Na serwerze docelowym wykryto nieudaną próbę zalogowania z urządzenia źródłowego. Może to być spowodowane błędem ludzkim, ale może również oznaczać złośliwą próbę złamania zabezpieczeń serwera lub danych. | Duży |
| Zmieniono wersję oprogramowania układowego firmy Emerson ROC | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Adres zewnętrzny w sieci, który komunikuje się z Internetem | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Krytyczne |
| Nieoczekiwane wykrycie urządzenia Field | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało ono autoryzowane. | Duży |
| Wykryto zmianę oprogramowania układowego | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Zmieniono wersję oprogramowania układowego | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Nieautoryzowana operacja Foxboro I/A | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Logowanie do usługi FTP nie powiodło się | Na serwerze docelowym wykryto nieudaną próbę zalogowania z urządzenia źródłowego. Może to być spowodowane błędem ludzkim, ale może również oznaczać złośliwą próbę złamania zabezpieczeń serwera lub danych. | Duży |
| Kod funkcji zgłosił wyjątek nieautoryzowany | Urządzenie źródłowe (podrzędne) zwróciło wyjątek z urządzeniem docelowym (głównym). | Duży |
| Ustawienia typu komunikatu GĘSI | Ustawienia komunikatu (identyfikowane przez identyfikator protokołu) zostały zmienione na urządzeniu źródłowym. | Ostrzeżenie |
| Zmieniono wersję oprogramowania układowego Honeywell | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Niedozwolona komunikacja HTTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto dostęp do Internetu | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Duży |
| Zmieniono wersję oprogramowania układowego Mitsubishi | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Naruszenie zakresu adresów Modbus | Urządzenie główne zażądało dostępu do nowego adresu pamięci podrzędnej. | Duży |
| Zmieniono wersję oprogramowania układowego Modbus | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Wykryto nowe działanie — Klasa PRZELEWu | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — usługa klasy przelew | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — polecenie przelew PCCC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — symbol PRZELEWu | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — połączenie sieci EtherNet/protokołu IP/O | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — protokół EtherNet/IP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — kod komunikatu GSM | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — kody poleceń LonTalk | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nowe odnajdywanie portów | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Ostrzeżenie |
| Wykryto nowe działanie — zmienna sieciowa LonTalk | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — żądanie danych Ovation | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — polecenie odczytu/zapisu (Grupa indeksów AMS) | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — polecenie odczytu/zapisu (przesunięcie indeksu AMS) | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowany typ komunikatu DeltaV | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowana operacja DeltaV ROC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowany typ komunikatu RPC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowane wywołanie procedury RPC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia usługi AMS | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia Siemens SICAM | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia protokołu Suitelink | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — używanie sesji protokołu Suitelink | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia Yokogawa VNetIP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Wykryto nowy element zawartości | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało ono autoryzowane. | Duży |
| Nowa konfiguracja urządzenia LLDP | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało ono autoryzowane. | Duży |
| Nowe odnajdywanie portów | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Ostrzeżenie |
| Omron żeberka — polecenie | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| S7 i oprogramowanie układowe PLC zmienione | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład zaplanowaną procedurę konserwacji. | Duży |
| Ustawienia typu komunikatu wartości próbkowane | Ustawienia komunikatu (identyfikowane przez identyfikator protokołu) zostały zmienione na urządzeniu źródłowym. | Ostrzeżenie |
| Podejrzenie nielegalnego skanowania integralności | Wykryto skanowanie na urządzeniu źródłowym DNP3 (na stacji). Ten skan nie został autoryzowany jako nauczyny ruch w sieci. | Duży |
| Łącze do komputera Toshiba — nieautoryzowane polecenie | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Mały |
| Nieautoryzowana operacja pliku ABB Totalflow | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana operacja Register ABB Totalflow | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp do bloku danych Siemens S7 | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako ruch sieciowy w sieci. | Ostrzeżenie |
| Nieautoryzowany dostęp do obiektu Siemens S7 Plus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp do tagu "zastanawianie się" | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako ruch sieciowy w sieci. | Duży |
| Nieautoryzowany dostęp do obiektów BACNet | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana trasa BACNet | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane logowanie do bazy danych | Wykryto próbę zalogowania między klientem źródłowym a serwerem docelowym. Komunikacja między tymi urządzeniami nie została autoryzowana jako nauczyny ruch w sieci. | Duży |
| Nieautoryzowana operacja bazy danych | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana operacja firmy Emerson ROC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp do plików GE SRTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane polecenie protokołu SRTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana operacja pamięci systemowej GE SRTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane działanie HTTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany serwer HTTP | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako aplikacja poznania w Twojej sieci. | Duży |
| Nieautoryzowana akcja SOAP protokołu HTTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany agent użytkownika HTTP | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako aplikacja poznania w Twojej sieci. | Duży |
| Wykryto nieautoryzowaną łączność z Internetem | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Krytyczne |
| Nieautoryzowane polecenie Mitsubishi MELSEC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp do programu MMS | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako ruch sieciowy w sieci. | Duży |
| Nieautoryzowana usługa MMS | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane połączenie multiemisji/emisji | Wykryto połączenie multiemisji/emisji między urządzeniem źródłowym a innymi urządzeniami. Komunikacja multiemisji/emisji nie jest autoryzowana. | Krytyczne |
| Nieautoryzowana nazwa zapytania | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane działanie OPC UA | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane żądanie OPC UA/odpowiedź | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana operacja została wykryta przez regułę zdefiniowaną przez użytkownika | Wykryto ruch między dwoma urządzeniami. To działanie nie jest autoryzowane na podstawie niestandardowej reguły alertu zdefiniowanej przez użytkownika. | Duży |
| Odczytanie nieautoryzowanej konfiguracji PLC | Urządzenie źródłowe nie jest zdefiniowane jako urządzenie programistyczne, ale wykonuje operację odczytu/zapisu na kontrolerze docelowym. Zmiany programistyczne powinny być wykonywane tylko przez urządzenia programistyczne. Aplikacja programistyczna mogła zostać zainstalowana na tym urządzeniu. | Ostrzeżenie |
| Zapis konfiguracji nieautoryzowanego PLC | Urządzenie źródłowe wysłało polecenie odczytu/zapisu programu na kontrolerze docelowym. To działanie nie było wcześniej widoczne. | Duży |
| Nieautoryzowane przekazywanie programu PLC | Urządzenie źródłowe wysłało polecenie odczytu/zapisu programu na kontrolerze docelowym. To działanie nie było wcześniej widoczne. | Duży |
| Nieautoryzowane Programowanie PLC | Urządzenie źródłowe nie jest zdefiniowane jako urządzenie programistyczne, ale wykonuje operację odczytu/zapisu na kontrolerze docelowym. Zmiany programistyczne powinny być wykonywane tylko przez urządzenia programistyczne. Aplikacja programistyczna mogła zostać zainstalowana na tym urządzeniu. | Krytyczne |
| Nieautoryzowany typ ramki PROFINET | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane SAIA S-Bus polecenie | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane wykonywanie funkcji sterowania przez Siemens S7 | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane wykonywanie funkcji zdefiniowanej przez użytkownika przez Siemens S7 | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp do bloku Siemens S7 Plus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowana operacja plusa Siemens S7 | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowane logowanie do protokołu SMB | Wykryto próbę zalogowania między klientem źródłowym a serwerem docelowym. Komunikacja między tymi urządzeniami nie została autoryzowana jako nauczyny ruch w sieci. | Duży |
| Nieautoryzowana operacja SNMP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany dostęp SSH | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Nieautoryzowany proces systemu Windows | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako aplikacja poznania w Twojej sieci. | Duży |
| Nieautoryzowana usługa systemu Windows | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako aplikacja poznania w Twojej sieci. | Duży |
| Nieautoryzowana operacja została wykryta przez regułę zdefiniowaną przez użytkownika | Wykryto nowe parametry ruchu. Ta kombinacja parametrów narusza regułę zdefiniowaną przez użytkownika | Duży |
| Niedozwolone rozszerzenie elektryczne Modbus Schneider | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Niedozwolone użycie typów ASDU | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Niedozwolone użycie kodu funkcji DNP3 | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |
| Niedozwolone użycie wskaźnika wewnętrznego (PROFILOWANIE) | Urządzenie źródłowe DNP3 (Substation) zgłosiło wewnętrzne oznaczenie (PROFILOWANIE), które nie jest autoryzowane jako ruch sieciowy w sieci. | Duży |
| Niedozwolone użycie kodu funkcji Modbus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczyny ruch sieciowy. Następująca kombinacja nie jest autoryzowana. | Duży |

## <a name="anomaly-engine-alerts"></a>Alerty aparatu anomalii

| Tytuł | Opis | Ważność |
|--|--|--|
| Nietypowy wzorzec wyjątku w elemencie podrzędnym | Na urządzeniu źródłowym wykryto nadmierną liczbę błędów. Może to być wynikiem problemu operacyjnego. | Mały |
| Nietypowe Długość nagłówka HTTP | Urządzenie źródłowe wysłało nietypowy komunikat. Może to wskazywać na próbę ataku na urządzenie docelowe. | Krytyczne |
| Nietypowa liczba parametrów w nagłówku HTTP | Urządzenie źródłowe wysłało nietypowy komunikat. Może to wskazywać na próbę ataku na urządzenie docelowe. | Krytyczne |
| Nietypowe zachowanie okresowe w kanale komunikacji | Wykryto zmianę częstotliwości komunikacji między urządzeniem źródłowym a docelowym. | Mały |
| Nietypowe zakończenie działania aplikacji | Na urządzeniu źródłowym wykryto nadmierną liczbę poleceń zatrzymania. Może to być wynikiem problemu operacyjnego lub próby manipulowania urządzeniem. | Duży |
| Nietypowa przepustowość ruchu sieciowego | W kanale wykryto nieprawidłową przepustowość. Przepustowość wydaje się znacznie niższa/wyższa od wcześniej wykrytych. Aby uzyskać szczegółowe informacje, Pracuj z widżetem łącznej przepustowości. | Ostrzeżenie |
| Nietypowa przepustowość ruchu między urządzeniami | W kanale wykryto nieprawidłową przepustowość. Przepustowość wydaje się znacznie niższa/wyższa od wcześniej wykrytych. Aby uzyskać szczegółowe informacje, Pracuj z widżetem łącznej przepustowości. | Ostrzeżenie |
| Wykryto skanowanie adresów | Podczas skanowania urządzeń sieciowych wykryto urządzenie źródłowe. To urządzenie nie zostało autoryzowane jako urządzenie do skanowania w sieci. | Krytyczne |
| Wykryto skanowanie adresów ARP | Urządzenie źródłowe wykryło skanowanie urządzeń sieciowych przy użyciu protokołu ARP (Address Resolution Protocol). Ten adres urządzenia nie został autoryzowany jako prawidłowy adres skanowania ARP. | Krytyczne |
| Wykryto skanowanie adresów ARP | Urządzenie źródłowe wykryło skanowanie urządzeń sieciowych przy użyciu protokołu ARP (Address Resolution Protocol). Ten adres urządzenia nie został autoryzowany jako prawidłowy adres skanowania ARP. | Krytyczne |
| Fałszowanie protokołu ARP | W sieci wykryto nieprawidłową liczbę pakietów. Może to wskazywać na atak, na przykład fałszowanie protokołu ARP lub atak na zalanie protokołu ICMP. | Ostrzeżenie |
| Nadmierne próby logowania | Na serwerze docelowym pojawiło się urządzenie źródłowe z nadmiernym logowaniem. Może to być atak z odżyciem. Złośliwy aktor może naruszyć bezpieczeństwo serwera. | Krytyczne |
| Zbyt wiele sesji | Na serwerze docelowym pojawiło się urządzenie źródłowe z nadmiernym logowaniem. Może to być atak z odżyciem. Złośliwy aktor może naruszyć bezpieczeństwo serwera. | Krytyczne |
| Nadmierna częstotliwość ponownego uruchamiania stacji | Na urządzeniu źródłowym wykryto nadmierną liczbę poleceń ponownego uruchomienia. Może to być wynikiem problemu operacyjnego lub próby manipulowania urządzeniem. | Duży |
| Nadmierne próby zalogowania do protokołu SMB | Na serwerze docelowym pojawiło się urządzenie źródłowe z nadmiernym logowaniem. Może to być atak z odżyciem. Złośliwy aktor może naruszyć bezpieczeństwo serwera. | Krytyczne |
| Zalanie protokołu ICMP | W sieci wykryto nieprawidłową liczbę pakietów. Może to wskazywać na atak, na przykład fałszowanie protokołu ARP lub atak na zalanie protokołu ICMP. | Ostrzeżenie |
| Niedozwolona zawartość nagłówka HTTP | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Krytyczne |
| Nieaktywny kanał komunikacyjny | Kanał komunikacyjny między dwoma urządzeniami był nieaktywny w okresie, w którym działanie jest zwykle widoczne. Może to wskazywać, że program generujący ten ruch został zmieniony lub program może być niedostępny. Zaleca się przejrzenie konfiguracji zainstalowanego programu i sprawdzenie, czy jest on prawidłowo skonfigurowany. | Ostrzeżenie |
| Wykryto skanowanie adresu długotrwałego czasu trwania | Podczas skanowania urządzeń sieciowych wykryto urządzenie źródłowe. To urządzenie nie zostało autoryzowane jako urządzenie do skanowania w sieci. | Krytyczne |
| Wykryto próbę odgadnięcia hasła | Na serwerze docelowym pojawiło się urządzenie źródłowe z nadmiernym logowaniem. Może to być atak z odżyciem. Złośliwy aktor może naruszyć bezpieczeństwo serwera. | Krytyczne |
| Wykryto skanowanie PLC | Podczas skanowania urządzeń sieciowych wykryto urządzenie źródłowe. To urządzenie nie zostało autoryzowane jako urządzenie do skanowania w sieci. | Krytyczne |
| Wykryto skanowanie portu | Podczas skanowania urządzeń sieciowych wykryto urządzenie źródłowe. To urządzenie nie zostało autoryzowane jako urządzenie do skanowania w sieci. | Krytyczne |
| Nieoczekiwana długość komunikatu | Urządzenie źródłowe wysłało nietypowy komunikat. Może to wskazywać na próbę ataku na urządzenie docelowe. | Krytyczne |
| Nieoczekiwany ruch dla portu standardowego | Wykryto ruch na urządzeniu przy użyciu portu zastrzeżonego dla innego protokołu. | Duży |

## <a name="protocol-violation-engine-alerts"></a>Alerty aparatu naruszenia protokołu

| Tytuł | Opis | Ważność |
|--|--|--|
| Nadmiernie wadliwe pakiety w pojedynczej sesji | Nietypowa liczba źle sformułowanych pakietów wysłanych z urządzenia źródłowego do urządzenia docelowego. Może to wskazywać na błędną komunikację lub próbę manipulowania urządzeniem określonym przez program. | Duży |
| Aktualizacja oprogramowania układowego | Urządzenie źródłowe wysłało polecenie w celu zaktualizowania oprogramowania układowego na urządzeniu docelowym. Upewnij się, że aktualizacje ostatnio używane do programowania, konfiguracji i oprogramowania układowego są prawidłowe. | Ostrzeżenie |
| Kod funkcji nie jest obsługiwany przez funkcję unstation | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Niedozwolony komunikat BACNet | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Niedozwolona próba połączenia z portem 0 | Urządzenie źródłowe podjęło próbę nawiązania połączenia z urządzeniem docelowym na numer portu zero (0). W przypadku protokołu TCP port 0 jest zarezerwowany i nie można go używać. W przypadku protokołu UDP port jest opcjonalny, a wartość 0 oznacza brak portu. W systemie zwykle nie ma usługi, która nasłuchuje na porcie 0. To zdarzenie może wskazywać na próbę ataku na urządzenie docelowe lub wskazać, że aplikacja została niepoprawnie zaprogramowana. | Mały |
| Niedozwolona operacja DNP3 | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Niedozwolona operacja MODBUS (wyjątek zgłoszony przez serwer główny) | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Niedozwolona operacja MODBUS (kod funkcji zero) | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Niedozwolona wersja protokołu | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Nieprawidłowy parametr wysłany do niestacji | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Inicjacja nieaktualnego kodu funkcji (zainicjuj dane) | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Mały |
| Inicjowanie przestarzałego kodu funkcji (Zapisz konfigurację) | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Mały |
| Główna prośba o potwierdzenie warstwy aplikacji | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Ostrzeżenie |
| Wyjątek Modbus | Urządzenie źródłowe (podrzędne) zwróciło wyjątek z urządzeniem docelowym (głównym). | Duży |
| Urządzenie podrzędne otrzymało niedozwolony typ ASDU | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwoloną przyczynę przesłania polecenia | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony wspólny adres | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony parametr adresu danych | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony parametr wartości danych | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony kod funkcji | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony adres obiektu informacji | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Nieznany obiekt wysłany do niestacji | Urządzenie docelowe otrzymało Nieprawidłowe żądanie. | Duży |
| Użycie zarezerwowanego kodu funkcji | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Duży |
| Użycie nieprawidłowego formatowania według stacji | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Ostrzeżenie |
| Użycie flag stanu zarezerwowanych (PROFILOWANIE) | Urządzenie źródłowe DNP3 (Substation) użyło zastrzeżonego wewnętrznego wskaźnika 2,6. Zalecane jest sprawdzenie konfiguracji urządzenia. | Ostrzeżenie |

## <a name="malware-engine-alerts"></a>Alerty aparatu złośliwego oprogramowania

| Tytuł | Opis| Ważność |
|--|--|--|
| Próba połączenia z znanym złośliwym adresem IP | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Nieprawidłowy komunikat SMB (DoublePulsar tylne wejście przydzielania) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Żądanie złośliwej nazwy domeny | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto plik testu złośliwego oprogramowania — powodzenie EICAR AV | Wykryto plik testu AV EICAR w ruchu między dwoma urządzeniami. Ten plik nie jest złośliwym oprogramowaniem. Służy do upewnienia się, że oprogramowanie antywirusowe zostało poprawnie zainstalowane; Zapoznaj się z informacjami o tym, co się stanie w przypadku znalezienia wirusa, i sprawdź wewnętrzne procedury i reakcje po znalezieniu wirusa. Oprogramowanie antywirusowe powinno wykryć EICAR tak, jakby był prawdziwym wirusem. | Duży |
| Podejrzenie złośliwego oprogramowania Conficker | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie ataku typu "odmowa usługi" | Urządzenie źródłowe podjęło próbę zainicjowania nadmiernej liczby nowych połączeń z urządzeniem docelowym. Może to być atak typu "odmowa usługi" (DOS) na urządzenie docelowe i może zakłócać funkcjonalność urządzeń, wpływ na wydajność i dostępność usługi albo spowodować nieodwracalne błędy. | Krytyczne |
| Podejrzenie złośliwego działania | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwego działania (BlackEnergy) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (DarkComet) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Duqu) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (płomień) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Havex) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Karagany) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (LightsOut) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (zapytania o nazwy) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwego działania (Trująca Ivy) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Regin) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Stuxnet) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (atak wannacry) | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwego oprogramowania NotPetya — wykryto niedozwolone parametry protokołu SMB | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego oprogramowania NotPetya — wykryto niedozwoloną transakcję SMB | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie zdalnego wykonania kodu z PsExec | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie zdalnego zarządzania usługami systemu Windows | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto podejrzany plik wykonywalny w punkcie końcowym | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto podejrzany ruch | Wykryto podejrzaną aktywność sieciową. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |

## <a name="operational-engine-alerts"></a>Alerty aparatu operacyjnego

| Tytuł | Opis | Ważność |
|--|--|--|
| Wysłano polecenie S7 zatrzymania PLC | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać do momentu wysłania polecenia uruchomienia. | Ostrzeżenie |
| Operacja BACNet nie powiodła się | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Nieprawidłowy stan urządzenia MMS | Wirtualne urządzenie produkcyjne MMS (VMD) wysłało komunikat o stanie. Komunikat wskazuje, że serwer może nie być prawidłowo skonfigurowany, częściowo działał lub nie działa. | Duży |
| Zmiana konfiguracji urządzenia | Wykryto zmianę konfiguracji na urządzeniu źródłowym. | Mały |
| Przepełnienie buforu zdarzeń ciągłego na niestacji | Na urządzeniu źródłowym wykryto zdarzenie przepełnienia buforu. Zdarzenie może spowodować uszkodzenie danych, awarię programu lub wykonanie złośliwego kodu. | Duży |
| Resetowanie kontrolera | Urządzenie źródłowe wysłało polecenie resetowania do kontrolera docelowego. Kontroler tymczasowo przestanie działać i rozpocznie się automatycznie. | Ostrzeżenie |
| Zatrzymanie kontrolera | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać do momentu wysłania polecenia uruchomienia. | Ostrzeżenie |
| Urządzenie nie może odebrać dynamicznego adresu IP | Urządzenie źródłowe jest skonfigurowane do odbierania dynamicznego adresu IP z serwera DHCP, ale nie otrzymało adresu. Wskazuje to na błąd konfiguracji na urządzeniu lub błąd operacyjny na serwerze DHCP. Zaleca się powiadomienie administratora sieci o zdarzeniu | Duży |
| Urządzenie prawdopodobnie zostało rozłączone (nie odpowiada) | Urządzenie źródłowe nie odpowiada na wysłane polecenie. Być może został on odłączony, gdy polecenie zostało wysłane. | Duży |
| Żądanie obsługi usługi PRZELEWania w sieci EtherNet/IP nie powiodło się | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Nie można wykonać polecenia protokołu hermetyzacji sieci EtherNet/IP | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Przepełnienie buforu zdarzeń na platformie | Na urządzeniu źródłowym wykryto zdarzenie przepełnienia buforu. Zdarzenie może spowodować uszkodzenie danych, awarię programu lub wykonanie złośliwego kodu. | Duży |
| Oczekiwana operacja tworzenia kopii zapasowej nie została wykonana | Nie nastąpiło oczekiwane działanie tworzenia kopii zapasowej/transferu plików między dwoma urządzeniami. Może to wskazywać na błędy procesu tworzenia kopii zapasowej/transferu plików. | Duży |
| Błąd polecenia GE SRTP | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Wysłano polecenie GE SRTP zatrzymania PLC | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać do momentu wysłania polecenia uruchomienia. | Ostrzeżenie |
| Blok kontroli GĘSI wymaga dalszej konfiguracji | Urządzenie źródłowe wysłało komunikat z informacją o tym, że urządzenie wymaga prowizji. Oznacza to, że blok kontroli GĘSI wymaga częściowej konfiguracji, a komunikaty GĘSI są częściowo lub całkowicie niefunkcjonalne. | Duży |
| Konfiguracja wygęsi zestawu danych została zmieniona | Komunikat (identyfikowany przez identyfikator protokołu) został zmieniony na urządzeniu źródłowym. Oznacza to, że urządzenie będzie zgłaszać inny zestaw danych dla tego komunikatu. | Ostrzeżenie |
| Nieoczekiwany stan kontrolera Honeywell | Kontroler Honeywell wysłał nieoczekiwany komunikat diagnostyczny wskazujący zmianę stanu. | Ostrzeżenie |
| Błąd klienta HTTP | Urządzenie źródłowe zainicjowało Nieprawidłowe żądanie. | Ostrzeżenie |
| Niedozwolony adres IP | System wykrył ruch między urządzeniem źródłowym a adresem IP, który jest nieprawidłowym adresem. Może to wskazywać na nieprawidłową konfigurację lub próbę wygenerowania niedozwolonego ruchu. | Mały |
| Błąd uwierzytelniania Master-Slave | Proces uwierzytelniania między urządzeniem źródłowym DNP3 (głównym) i urządzeniem docelowym (niestacji) nie powiódł się. | Mały |
| Żądanie usługi MMS nie powiodło się | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Nie wykryto ruchu w interfejsie czujnika | Czujnik przestał wykrywać ruch sieciowy w interfejsie sieciowym. | Krytyczne |
| Serwer OPC UA zgłosił zdarzenie, które wymaga uwagi użytkownika | Serwer OPC UA wysłał powiadomienie o zdarzeniu do klienta. Ten typ zdarzenia wymaga uwagi użytkownika | Duży |
| Żądanie usługi OPC UA nie powiodło się | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Stacja dokująca została ponownie uruchomiona | Na urządzeniu źródłowym wykryto zimne ponowne uruchomienie. Oznacza to, że urządzenie zostało fizycznie wyłączone i ponownie włączone. | Ostrzeżenie |
| Częste ponowne uruchamianie stacji | Na urządzeniu źródłowym wykryto nadmierną liczbę ponownych uruchomień. Oznacza to, że urządzenie zostało fizycznie wyłączone i ponownie wystąpiło zbyt wiele razy. | Mały |
| Zmieniono konfigurację stacji dokującej | Wykryto zmianę konfiguracji na urządzeniu źródłowym. | Duży |
| Wykryto uszkodzoną konfigurację stacji dokującej | To urządzenie źródłowe DNP3 (Substation) zgłosiło uszkodzoną konfigurację. | Duży |
| Polecenie PROFINET DCP nie powiodło się | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Resetowanie do ustawień fabrycznych urządzenia PROFINET | Urządzenie źródłowe wysłało polecenie resetowania do ustawień fabrycznych do urządzenia docelowego PROFINET. Zresetuj polecenie czyści konfiguracje urządzeń PROFINET i kończy jego działanie. | Ostrzeżenie |
| Nie można wykonać operacji RPC | Serwer zwrócił kod błędu. Wskazuje to na błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Konfiguracja zestawu danych komunikatów z wartościami przykładowymi została zmieniona | Komunikat (identyfikowany przez identyfikator protokołu) został zmieniony na urządzeniu źródłowym. Oznacza to, że urządzenie będzie zgłaszać inny zestaw danych dla tego komunikatu. | Ostrzeżenie |
| Niemożliwy nieodwracalny błąd urządzenia podrzędnego | Na urządzeniu źródłowym wykryto błąd niemożliwy do odzyskania warunku. Ten rodzaj błędu zwykle wskazuje na awarię sprzętu lub niepowodzenie wykonania konkretnego polecenia. | Duży |
| Podejrzenie problemów sprzętowych na urządzeniach stacjonarnych | Na urządzeniu źródłowym wykryto błąd niemożliwy do odzyskania warunku. Ten rodzaj błędu zwykle wskazuje na awarię sprzętu lub niepowodzenie wykonania konkretnego polecenia. | Duży |
| Podejrzenie, że urządzenie MODBUS nie odpowiada | Urządzenie źródłowe nie odpowiada na wysłane polecenie. Być może został on odłączony, gdy polecenie zostało wysłane. | Mały |
| Wykryto ruch w interfejsie czujnika | Czujnik wznowił wykrywanie ruchu sieciowego w interfejsie sieciowym. | Ostrzeżenie |

## <a name="next-steps"></a>Następne kroki

Można [zarządzać zdarzeniami alertów](how-to-manage-the-alert-event.md).
Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
