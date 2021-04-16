---
title: Opisy i typy alertów
description: Przejrzyj opisy alertów usługi Defender dla IoT.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477999"
---
# <a name="alert-types-and-descriptions"></a>Opisy i typy alertów

W tym artykule opisano wszystkie typy alertów, które mogą być generowane przez aparaty usługi Defender dla IoT. Alerty są wyświetlane w oknie Alerty, co umożliwia zarządzanie zdarzeniem alertu. 

## <a name="policy-engine-alerts"></a>Alerty aparatu zasad

Alerty aparatu zasad opisują wykryte odchylenia od poznanych zachowań punktów odniesienia.

| Tytuł  | Opis | Ważność |
|--|--|--|
| Nietypowe użycie adresów MAC | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało autoryzowane. | Mały |
| Beckhoff Software Changed | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Logowanie do bazy danych nie powiodło się | Wykryto nieudaną próbę logowania z urządzenia źródłowego na serwer docelowy. Może to być wynikiem błędu ludzkiego, ale może również wskazywać na złośliwą próbę naruszenia bezpieczeństwa serwera lub danych na nim. | Duży |
| Zmieniono wersję oprogramowania układowego Emerson ROC | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Adres zewnętrzny w sieci komunikował się z Internetem | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Krytyczne |
| Urządzenie w terenie zostało nieoczekiwanie odnalezione | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało autoryzowane. | Duży |
| Wykryto zmianę oprogramowania układowego | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Zmieniono wersję oprogramowania układowego | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Operacja we/wy Foxboro nieautoryzowana | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Logowanie FTP nie powiodło się | Wykryto nieudaną próbę logowania z urządzenia źródłowego na serwer docelowy. Może to być wynikiem błędu ludzkiego, ale może również wskazywać na złośliwą próbę naruszenia bezpieczeństwa serwera lub danych na nim. | Duży |
| Kod funkcji został zwołyny jako wyjątek nieautoryzowany | Urządzenie źródłowe (podrzędne) zwróciło wyjątek do urządzenia docelowego (głównego). | Duży |
| Ustawienia typu komunikatu GOOSE | Ustawienia komunikatu (identyfikowane przez identyfikator protokołu) zostały zmienione na urządzeniu źródłowym. | Ostrzeżenie |
| Zmieniono wersję oprogramowania układowego Honeywell | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Niedozwolona komunikacja HTTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto dostęp do Internetu | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Duży |
| Zmieniono wersję oprogramowania układowego Firmy Testowej | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Naruszenie zakresu adresów Modbus | Urządzenie główne zażądało dostępu do nowego adresu pamięci podrzędnej. | Duży |
| Zmieniono wersję oprogramowania układowego Modbus | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Wykryto nowe działanie — klasa CIP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — usługa klasy CIP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — polecenie CIP PCCC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — symbol CIP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — połączenie We/Wy EtherNet/IP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — polecenie EtherNet/IP Protocol | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — kod komunikatu USŁUGI ZAOKI | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — kody poleceń LonTalk | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nowe odnajdywanie portów | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Ostrzeżenie |
| Wykryto nowe działanie — zmienna sieciowa LonTalk | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — żądanie danychovation | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — polecenie odczytu/zapisu (grupa indeksów ams) | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — polecenie odczytu/zapisu (przesunięcie indeksu ams) | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowany typ komunikatu DeltaV | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowana operacja deltaV ROC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowany typ komunikatu RPC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — nieautoryzowane wywoływanie procedury RPC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia protokołu AMS | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia SiCAM | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia Suitelink Protocol | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — używanie sesji protokołu Suitelink | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowe działanie — za pomocą polecenia Yokogawa VNetIP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Wykryto nowy zasób | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało autoryzowane. | Duży |
| Nowa konfiguracja urządzenia LLDP | W sieci wykryto nowe urządzenie źródłowe, ale nie zostało autoryzowane. | Duży |
| Nowe odnajdywanie portów | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Ostrzeżenie |
| Omron FINS Unauthorized, polecenie | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Zmieniono oprogramowanie układowe S7 plus SYSTEM SYSTEM | Oprogramowanie układowe zostało zaktualizowane na urządzeniu źródłowym. Może to być autoryzowane działanie, na przykład procedura planowanej konserwacji. | Duży |
| Ustawienia typu komunikatu przykładowych wartości | Ustawienia komunikatu (identyfikowane przez identyfikator protokołu) zostały zmienione na urządzeniu źródłowym. | Ostrzeżenie |
| Podejrzenie niedozwolonego skanowania integralności | Wykryto skanowanie na urządzeniu źródłowym DNP3 (zasypianiu). To skanowanie nie zostało autoryzowane jako nauczona ruch w sieci. | Duży |
| Polecenie Nieautoryzowany link komputera do komputera | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Mały |
| Nieautoryzowana operacja na pliku totalflow abb | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana operacja rejestrowania całkowitego przepływu ABB | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp do bloku danych Firmy S7 | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba uzyskania dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako nauczona ruch w sieci. | Ostrzeżenie |
| Nieautoryzowany dostęp do obiektu Marki S7 Plus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp do tagu Wonderware | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba uzyskania dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako nauczona ruch w sieci. | Duży |
| Nieautoryzowany dostęp do obiektów BACNet | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana trasa BACNet | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany identyfikator logowania do bazy danych | Wykryto próbę logowania między klientem źródłowym a serwerem docelowym. Komunikacja między tymi urządzeniami nie została autoryzowana jako nauczona komunikacja w sieci. | Duży |
| Nieautoryzowana operacja bazy danych | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana operacja Emerson ROC | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp do plików SRTP ge | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Polecenie nieautoryzowanego protokołu SRTP GE | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana operacja pamięci systemowej GE SRTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane działanie HTTP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany serwer HTTP | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako nauczona aplikacja w sieci. | Duży |
| Nieautoryzowana akcja protokołu HTTP SOAP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany agent użytkownika HTTP | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako nauczona aplikacja w sieci. | Duży |
| Wykryto nieautoryzowaną łączność z Internetem | Urządzenie źródłowe zdefiniowane jako część sieci komunikuje się z adresami internetowymi. Źródło nie ma autoryzacji do komunikowania się z adresami internetowymi. | Krytyczne |
| Nieautoryzowane polecenie Melsec | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp do programu MMS | Urządzenie źródłowe próbowało uzyskać dostęp do zasobu na innym urządzeniu. Próba uzyskania dostępu do tego zasobu między tymi dwoma urządzeniami nie została autoryzowana jako nauczona ruch w sieci. | Duży |
| Nieautoryzowana usługa MMS | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane połączenie multiemisji/emisji | Wykryto połączenie multiemisji/emisji między urządzeniem źródłowym a innymi urządzeniami. Komunikacja multiemisji/emisji nie jest autoryzowana. | Krytyczne |
| Zapytanie o nieautoryzowaną nazwę | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane działanie OPC UA | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane żądanie/odpowiedź OPC UA | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana operacja została wykryta przez regułę zdefiniowaną przez użytkownika | Wykryto ruch między dwoma urządzeniami. To działanie nie jest nieautoryzowane na podstawie niestandardowej reguły alertu zdefiniowanej przez użytkownika. | Duży |
| Odczyt nieautoryzowanych konfiguracji SYSTEMU ZARZĄDZANIA dostępem i dostępem | Urządzenie źródłowe nie jest zdefiniowane jako urządzenie programistyczne, ale wykonało operację odczytu/zapisu na kontrolerze docelowym. Zmiany programistyczne powinny być wykonywane tylko przez urządzenia programistyczne. Na tym urządzeniu może być zainstalowana aplikacja programistyka. | Ostrzeżenie |
| Nieautoryzowany zapis konfiguracji SYSTEMU DNS | Urządzenie źródłowe wysłało polecenie odczytu/zapisu programu kontrolera docelowego. To działanie nie było wcześniej widoczne. | Duży |
| Przekazywanie nieautoryzowanych programów ZEA | Urządzenie źródłowe wysłało polecenie odczytu/zapisu programu kontrolera docelowego. To działanie nie było wcześniej widoczne. | Duży |
| Nieautoryzowany programistyczne INTERFEJSY programowania | Urządzenie źródłowe nie jest zdefiniowane jako urządzenie programistyczne, ale wykonało operację odczytu/zapisu na kontrolerze docelowym. Zmiany programistyczne powinny być wykonywane tylko przez urządzenia programistyczne. Na tym urządzeniu może być zainstalowana aplikacja programistyka. | Krytyczne |
| Nieautoryzowany typ ramki Profinet | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane polecenie S-Bus ZEA | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane wykonanie funkcji sterowania przez S7 bez autoryzacji | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowane wykonanie funkcji zdefiniowanej przez użytkownika przez użytkownika | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp blokowy do sieci S7 Plus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowana operacja S7 Plus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany identyfikator logowania SMB | Wykryto próbę logowania między klientem źródłowym a serwerem docelowym. Komunikacja między tymi urządzeniami nie została autoryzowana jako nauczona komunikacja w sieci. | Duży |
| Nieautoryzowana operacja SNMP | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany dostęp SSH | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Nieautoryzowany proces systemu Windows | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako nauczona aplikacja w sieci. | Duży |
| Nieautoryzowana usługa systemu Windows | Na urządzeniu źródłowym wykryto nieautoryzowaną aplikację. Aplikacja nie została autoryzowana jako nauczona aplikacja w sieci. | Duży |
| Nieautoryzowana operacja została wykryta przez regułę zdefiniowaną przez użytkownika | Wykryto nowe parametry ruchu. Ta kombinacja parametrów narusza regułę zdefiniowaną przez użytkownika | Duży |
| Niezatwierdzone rozszerzenie Modbus z energii elektrycznej | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Niezatwierdzone użycie typów ASDU | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Niezatwierdzone użycie kodu funkcji DNP3 | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |
| Niezatwierdzone użycie wewnętrznego wskazania (IIN) | Urządzenie źródłowe (outstation) DNP3 zgłosiło wewnętrzne wskazanie (IIN), które nie ma autoryzacji jako nauczona ruch w sieci. | Duży |
| Niezatwierdzone użycie kodu funkcji Modbus | Wykryto nowe parametry ruchu. Ta kombinacja parametrów nie została autoryzowana jako nauczona ruch w sieci. Następująca kombinacja jest nieautoryzowana. | Duży |

## <a name="anomaly-engine-alerts"></a>Alerty aparatu anomalii

Alerty aparatu anomalii opisują wykryte anomalie w działaniu sieci.

| Tytuł | Opis | Ważność |
|--|--|--|
| Wzorzec wyjątku nietypowego w podrzędnym | Wykryto nadmierną liczbę błędów na urządzeniu źródłowym. Może to być wynikiem problemu operacyjnego. | Mały |
| Nietypowa długość nagłówka HTTP | Urządzenie źródłowe wysłało nietypowy komunikat. Może to oznaczać próbę ataku na urządzenie docelowe. | Krytyczne |
| Nietypowa liczba parametrów w nagłówku HTTP | Urządzenie źródłowe wysłało nietypowy komunikat. Może to oznaczać próbę ataku na urządzenie docelowe. | Krytyczne |
| Nietypowe okresowe zachowanie w kanale komunikacyjnym | Wykryto zmianę częstotliwości komunikacji między urządzeniami źródłowymi i docelowymi. | Mały |
| Nietypowe zakończenie działania aplikacji | Wykryto nadmierną liczbę poleceń zatrzymania na urządzeniu źródłowym. Może to być wynikiem problemu operacyjnego lub próby manipulowania urządzeniem. | Duży |
| Nietypowa przepustowość ruchu | Wykryto nietypową przepustowość w kanale. Przepustowość wydaje się być znacznie niższa/wyższa niż wcześniej wykryta. Aby uzyskać szczegółowe informacje, należy pracować z widżetem Łączna przepustowość. | Ostrzeżenie |
| Nietypowa przepustowość ruchu między urządzeniami | Wykryto nietypową przepustowość w kanale. Przepustowość wydaje się być znacznie niższa/wyższa niż wcześniej wykryta. Aby uzyskać szczegółowe informacje, należy pracować z widżetem Łączna przepustowość. | Ostrzeżenie |
| Wykryto skanowanie adresów | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe. To urządzenie nie zostało autoryzowane jako urządzenie skanujące sieć. | Krytyczne |
| Wykryto skanowanie adresów ARP | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe przy użyciu protokołu ARP (Address Resolution Protocol). Ten adres urządzenia nie został autoryzowany jako prawidłowy adres skanowania ARP. | Krytyczne |
| Wykryto skanowanie adresów ARP | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe przy użyciu protokołu ARP (Address Resolution Protocol). Ten adres urządzenia nie został autoryzowany jako prawidłowy adres skanowania ARP. | Krytyczne |
| Fałszowanie ARP | Wykryto w sieci nietypową liczbę pakietów. Może to oznaczać atak, na przykład fałszowanie ARP lub atak zalewania ICMP. | Ostrzeżenie |
| Nadmierne próby logowania | W urządzeniu źródłowym były widoczne nadmierne próby logowania do serwera docelowego. Może to być atak siłowy. Serwer może zostać naruszony przez złośliwego aktora. | Krytyczne |
| Nadmierna liczba sesji | Na urządzeniu źródłowym były widoczne nadmierne próby logowania do serwera docelowego. Może to być atak siłowy. Serwer może zostać naruszony przez złośliwego aktora. | Krytyczne |
| Nadmierna szybkość ponownego uruchamiania w przypadku outstation | Wykryto nadmierną liczbę poleceń ponownego uruchamiania na urządzeniu źródłowym. Może to być wynikiem problemu operacyjnego lub próby manipulowania urządzeniem. | Duży |
| Nadmierne próby logowania za pomocą SMB | Na urządzeniu źródłowym były widoczne nadmierne próby logowania do serwera docelowego. Może to być atak siłowy. Serwer może zostać naruszony przez złośliwego aktora. | Krytyczne |
| Powódź ICMP | Wykryto w sieci nietypową liczbę pakietów. Może to wskazywać na atak, na przykład fałszowanie ARP lub atak Zalążek ICMP. | Ostrzeżenie |
| Niedozwolona zawartość nagłówka HTTP | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Krytyczne |
| Nieaktywny kanał komunikacyjny | Kanał komunikacyjny między dwoma urządzeniami był nieaktywny w okresie, w którym zwykle występuje aktywność. Może to oznaczać, że program generujący ten ruch został zmieniony lub program może być niedostępny. Zaleca się przejrzenie konfiguracji zainstalowanego programu i sprawdzenie, czy jest on poprawnie skonfigurowany. | Ostrzeżenie |
| Wykryto skanowanie adresów o długim czasie trwania | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe. To urządzenie nie zostało autoryzowane jako urządzenie skanujące sieć. | Krytyczne |
| Wykryto próbę odgadnięcia hasła | W urządzeniu źródłowym były widoczne nadmierne próby logowania do serwera docelowego. Może to być atak siłowy. Serwer może zostać naruszony przez złośliwego aktora. | Krytyczne |
| Wykryto skanowanie w trybie SCAN | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe. To urządzenie nie zostało autoryzowane jako urządzenie skanujące sieć. | Krytyczne |
| Wykryto skanowanie portów | Wykryto urządzenie źródłowe skanujące urządzenia sieciowe. To urządzenie nie zostało autoryzowane jako urządzenie skanujące sieć. | Krytyczne |
| Nieoczekiwana długość komunikatu | Urządzenie źródłowe wysłało nietypowy komunikat. Może to oznaczać próbę ataku na urządzenie docelowe. | Krytyczne |
| Nieoczekiwany ruch dla portu standardowego | Wykryto ruch na urządzeniu przy użyciu portu zarezerwowanego dla innego protokołu. | Duży |

## <a name="protocol-violation-engine-alerts"></a>Alerty aparatu naruszenia protokołu

Alerty aparatu protokołu opisują wykryte odchylenia w strukturze pakietów lub wartości pól w porównaniu ze specyfikacjami protokołu.

| Tytuł | Opis | Ważność |
|--|--|--|
| Nadmierne źle sformułowane pakiety w jednej sesji | Nietypowa liczba źle sformułowanych pakietów wysyłanych z urządzenia źródłowego do urządzenia docelowego. Może to wskazywać na błędną komunikację lub próbę manipulowania urządzeniem docelowym. | Duży |
| Aktualizacja oprogramowania układowego | Urządzenie źródłowe wysłało polecenie aktualizacji oprogramowania układowego na urządzeniu docelowym. Sprawdź, czy najnowsze uaktualnienia programowania, konfiguracji i oprogramowania układowego wprowadzone na urządzeniu docelowym są prawidłowe. | Ostrzeżenie |
| Kod funkcji nie jest obsługiwany przez funkcję outstation | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Niedozwolony komunikat BACNet | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Niedozwolona próba połączenia na porcie 0 | Urządzenie źródłowe próbowało nawiązać połączenie z urządzeniem docelowym na porcie o numerze 0 (0). W przypadku protokołu TCP port 0 jest zarezerwowany i nie można go używać. W przypadku protokołu UDP port jest opcjonalny, a wartość 0 oznacza brak portu. W systemie zazwyczaj nie ma usługi, która nasłuchuje na porcie 0. To zdarzenie może wskazywać na próbę ataku na urządzenie docelowe lub wskazać, że aplikacja została zaprogramowana niepoprawnie. | Mały |
| Niedozwolona operacja DNP3 | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Niedozwolona operacja MODBUS (wyjątek wywoływany przez master) | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Niedozwolona operacja MODBUS (kod funkcji zero) | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Niedozwolona wersja protokołu | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Nieprawidłowy parametr wysłany do outstation | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Inicjowanie przestarzałego kodu funkcji (inicjowanie danych) | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Mały |
| Inicjacja przestarzałego kodu funkcji (Zapisz konfigurację) | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Mały |
| Wzorzec zażądał potwierdzenia warstwy aplikacji | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Ostrzeżenie |
| Wyjątek Modbus | Urządzenie źródłowe (podrzędne) zwróciło wyjątek do urządzenia docelowego (głównego). | Duży |
| Urządzenie podrzędne odebrano niedozwolony typ ASDU | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwoloną przyczynę polecenia transmisji | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony wspólny adres | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Parametr niedozwolonego adresu danych odebranego przez urządzenie podrzędne | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Parametr niedozwolonych wartości danych odebranych przez urządzenie podrzędne | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony kod funkcji | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Urządzenie podrzędne otrzymało niedozwolony adres obiektu informacyjnego | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Nieznany obiekt wysłany do outstation | Urządzenie docelowe odebrało nieprawidłowe żądanie. | Duży |
| Użycie kodu funkcji zarezerwowanej | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Duży |
| Użycie niewłaściwego formatowania przez usługę Outstation | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Ostrzeżenie |
| Użycie flag stanu zarezerwowanego (IIN) | Urządzenie źródłowe DNP3 (outstation) użyło zarezerwowanego wewnętrznego wskaźnika 2.6. Zaleca się sprawdzenie konfiguracji urządzenia. | Ostrzeżenie |

## <a name="malware-engine-alerts"></a>Alerty aparatu złośliwego oprogramowania

Alerty aparatu złośliwego oprogramowania opisują wykryte złośliwe działanie sieci.

| Tytuł | Opis| Ważność |
|--|--|--|
| Próba połączenia ze znanym złośliwym adresem IP | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Nieprawidłowy komunikat SMB (DoublePulsar Backdoor ), | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Żądanie złośliwej nazwy domeny | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto plik testowy złośliwego oprogramowania — powodzenie EICAR AV | Wykryto plik testowy EICAR AV w ruchu między dwoma urządzeniami. Plik nie jest złośliwym oprogramowaniem. Służy do potwierdzenia, że oprogramowanie antywirusowe jest zainstalowane poprawnie. zademonstrować, co się dzieje, gdy zostanie znaleziony wirus, oraz sprawdzić procedury wewnętrzne i reakcje po jego odnalezioniu. Oprogramowanie antywirusowe powinno wykrywać EICAR tak, jakby był prawdziwym wirusem. | Duży |
| Podejrzenie złośliwego oprogramowania conficker | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie ataku typu "odmowa usługi" | Urządzenie źródłowe próbowało zainicjować nadmierną liczbę nowych połączeń z urządzeniem docelowym. Może to być atak typu "odmowa usługi" (DOS, Denial Of Service) na urządzenie docelowe i może zakłócać działanie urządzenia, wpływać na wydajność i dostępność usługi lub powodować nieodwracalne błędy. | Krytyczne |
| Podejrzenie złośliwego działania | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwej aktywności (BlackEnergy) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (DarkComet) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Duqu) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Waze) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Havex) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Karagany) | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (LightsOut) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (nazywanie zapytań) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwego działania (zatruwa ivy) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Regin) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (Stuxnet) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego działania (WannaCry) | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie złośliwego oprogramowania NotPetya — wykryto niedozwolone parametry SMB | Wykryto podejrzane działanie sieciowe. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie złośliwego oprogramowania NotPetya — wykryto niedozwoloną transakcję SMB | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |
| Podejrzenie zdalnego wykonywania kodu za pomocą programu PsExec | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Podejrzenie zdalnego zarządzania usługami systemu Windows | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto podejrzany plik wykonywalny w punkcie końcowym | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Duży |
| Wykryto podejrzany ruch | Wykryto podejrzane działanie sieci. To działanie może być skojarzone z atakiem wykorzystującym metodę używaną przez znane złośliwe oprogramowanie. | Krytyczne |

## <a name="operational-engine-alerts"></a>Alerty aparatu operacyjnego

Alerty aparatu operacyjnego opisują wykryte zdarzenia operacyjne lub nieprawidłowo działający obiekt.

| Tytuł | Opis | Ważność |
|--|--|--|
| Wysłano polecenie S7 Stop SYSTEM SYSTEM | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać do momentu wysłania polecenia uruchamiania. | Ostrzeżenie |
| Operacja BACNet nie powiodła się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Zły stan urządzenia MMS | Wirtualne urządzenie produkcyjne (VMD) programu MMS wysłało komunikat o stanie. Komunikat wskazuje, że serwer może nie być poprawnie skonfigurowany, częściowo operacyjny lub w ogóle nie działa. | Duży |
| Zmiana konfiguracji urządzenia | Wykryto zmianę konfiguracji na urządzeniu źródłowym. | Mały |
| Ciągłe przepełnienie buforu zdarzeń podczas outstation | Wykryto zdarzenie przepełnienia buforu na urządzeniu źródłowym. Zdarzenie może spowodować uszkodzenie danych, awarię programu lub wykonanie złośliwego kodu. | Duży |
| Resetowanie kontrolera | Urządzenie źródłowe wysłało polecenie resetowania do kontrolera docelowego. Kontroler tymczasowo przestał działać i został uruchomiony ponownie automatycznie. | Ostrzeżenie |
| Zatrzymywanie kontrolera | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać do momentu wysłania polecenia uruchamiania. | Ostrzeżenie |
| Urządzenie nie może odebrać dynamicznego adresu IP | Urządzenie źródłowe jest skonfigurowane do odbierania dynamicznego adresu IP z serwera DHCP, ale nie otrzymało adresu. Oznacza to błąd konfiguracji na urządzeniu lub błąd operacyjny na serwerze DHCP. Zaleca się powiadomienie administratora sieci o zdarzeniu | Duży |
| Podejrzewa się, że urządzenie zostało odłączone (nie odpowiada) | Urządzenie źródłowe nie odpowiedziało na wysłane do niego polecenie. Być może został odłączony podczas wysłania polecenia. | Duży |
| Żądanie obsługi EtherNet/IP CIP nie powiodło się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Polecenie protokołu hermetyzacji EtherNet/IP nie powiodło się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Przepełnienie buforu zdarzeń w outstation | Wykryto zdarzenie przepełnienia buforu na urządzeniu źródłowym. Zdarzenie może spowodować uszkodzenie danych, awarię programu lub wykonanie złośliwego kodu. | Duży |
| Oczekiwano operacji tworzenia kopii zapasowej | Oczekiwano działania tworzenia kopii zapasowej/transferu plików między dwoma urządzeniami. Może to wskazywać błędy w procesie tworzenia kopii zapasowej/transferu plików. | Duży |
| Błąd polecenia GE SRTP | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Wysłano polecenie ZATRZYMANIA SRTP GE | Urządzenie źródłowe wysłało polecenie zatrzymania do kontrolera docelowego. Kontroler przestanie działać, dopóki nie zostanie wysłane polecenie uruchamiania. | Ostrzeżenie |
| Blok sterowania SYSTEMU GOOSE wymaga dalszej konfiguracji | Urządzenie źródłowe wysłało komunikat GOOSE informujący o tym, że urządzenie wymaga jego odkańczania. Oznacza to, że blok sterowania SYSTEMU GOOSE wymaga dalszej konfiguracji, a komunikaty GOOSE są częściowo lub całkowicie nieoperacyjne. | Duży |
| Konfiguracja zestawu danych SYSTEMU GOOSE została zmieniona | Zestaw danych komunikatu (identyfikowany przez identyfikator protokołu) został zmieniony na urządzeniu źródłowym. Oznacza to, że urządzenie będzie zgłaszać inny zestaw danych dla tego komunikatu. | Ostrzeżenie |
| Nieoczekiwany stan kontrolera Honeywell Controller | Kontroler Honeywell wysłał nieoczekiwany komunikat diagnostyczny wskazujący zmianę stanu. | Ostrzeżenie |
| Błąd klienta HTTP | Urządzenie źródłowe zainicjowało nieprawidłowe żądanie. | Ostrzeżenie |
| Niedozwolony adres IP | System wykrył ruch między urządzeniem źródłowym i adresem IP, który jest nieprawidłowym adresem. Może to oznaczać nieprawidłową konfigurację lub próbę wygenerowania niedozwolonego ruchu. | Mały |
| błąd Master-Slave uwierzytelniania | Proces uwierzytelniania między urządzeniem źródłowym DNP3 (głównym) a urządzeniem docelowym (outstation) nie powiódł się. | Mały |
| Żądanie usługi MMS nie powiodło się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Nie wykryto ruchu w interfejsie czujnika | Czujnik przestał wykrywać ruch sieciowy w interfejsie sieciowym. | Krytyczne |
| Serwer OPC UA zdjęł zdarzenie, które wymaga uwagi użytkownika | Serwer OPC UA wysłał powiadomienie o zdarzeniu do klienta. Ten typ zdarzenia wymaga uwagi użytkownika | Duży |
| Żądanie obsługi OPC UA nie powiodło się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Ponowne uruchomienie stacji wychodzącej | Wykryto zimne ponowne uruchomienie na urządzeniu źródłowym. Oznacza to, że urządzenie zostało fizycznie wyłączone i ponownie włączone. | Ostrzeżenie |
| Outstation jest często ponownie uruchamiany | Wykryto nadmierną liczbę zimnych uruchomień na urządzeniu źródłowym. Oznacza to, że urządzenie zostało fizycznie wyłączone i ponownie włączone zbyt wiele razy. | Mały |
| Zmieniono konfigurację usługi Outstation | Wykryto zmianę konfiguracji na urządzeniu źródłowym. | Duży |
| Wykryto uszkodzoną konfigurację usługi Outstation | To urządzenie źródłowe (outstation) DNP3 zgłosiło uszkodzoną konfigurację. | Duży |
| Polecenie DCP w sieci Profinet nie powiodło się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Profinet Device Factory Reset | Urządzenie źródłowe wysłało polecenie resetowania do ustawień fabrycznych do urządzenia docelowego w sieci Profinet. Polecenie reset powoduje wyczyszczenie konfiguracji urządzenia Profinet i zatrzymanie jego działania. | Ostrzeżenie |
| Operacja RPC nie powiodła się | Serwer zwrócił kod błędu. Oznacza to błąd serwera lub nieprawidłowe żądanie klienta. | Duży |
| Zmieniono konfigurację zestawu danych komunikatów przykładowych wartości | Zestaw danych komunikatu (identyfikowany przez identyfikator protokołu) został zmieniony na urządzeniu źródłowym. Oznacza to, że urządzenie będzie zgłaszać inny zestaw danych dla tego komunikatu. | Ostrzeżenie |
| Błąd nieodwracalnego urządzenia podrzędnego | Na urządzeniu źródłowym wykryto nieodwracalny błąd warunku. Ten rodzaj błędu zwykle wskazuje na awarię sprzętu lub niepowodzenie wykonania określonego polecenia. | Duży |
| Podejrzenie problemów sprzętowych w przypadku awarii | Na urządzeniu źródłowym wykryto nieodwracalny błąd warunku. Ten rodzaj błędu zwykle wskazuje na awarię sprzętu lub niepowodzenie wykonania określonego polecenia. | Duży |
| Podejrzenie, że urządzenie MODBUS nie odpowiada | Urządzenie źródłowe nie odpowiedziało na wysłane do niego polecenie. Być może został odłączony podczas wysłania polecenia. | Mały |
| Ruch wykryty w interfejsie czujnika | Czujnik wznowił wykrywanie ruchu sieciowego na interfejsie sieciowym. | Ostrzeżenie |

## <a name="next-steps"></a>Następne kroki

Możesz [zarządzać zdarzeniami alertów.](how-to-manage-the-alert-event.md)
Dowiedz się, jak [przesyłać dalej informacje o alertach.](how-to-forward-alert-information-to-partners.md)
