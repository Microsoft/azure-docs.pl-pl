---
title: Certyfikaty zabezpieczeń urządzeń — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Usługa Azure IoT Edge używa certyfikatu do sprawdzania poprawności urządzeń, modułów i urządzeń typu liść i ustanawiania bezpiecznych połączeń między nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: d3e456d57d98b796fb1aea2e82de51f9fae40c68
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733160"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Dowiedz się, jak usługa Azure IoT Edge używa certyfikatów

Certyfikaty usługi IoT Edge są używane przez moduły i podrzędne urządzenia IoT do weryfikacji tożsamości i zasadności modułu środowiska wykonawczego [centrum usługi IoT Edge.](iot-edge-runtime.md#iot-edge-hub) Weryfikacje te umożliwiają bezpieczne połączenie TLS (transport layer security) między czasem wykonywania, modułami i urządzeniami IoT. Podobnie jak samo centrum IoT Hub, IoT Edge wymaga bezpiecznego i szyfrowanego połączenia z urządzeń podrzędnych (lub typu liść) i modułów usługi IoT Edge. Aby ustanowić bezpieczne połączenie TLS, moduł centrum usługi IoT Edge przedstawia łańcuch certyfikatów serwera do łączenia klientów w celu sprawdzenia jego tożsamości.

W tym artykule wyjaśniono, jak certyfikaty IoT Edge mogą działać w scenariuszach produkcji, programowania i testowania. Podczas gdy skrypty są różne (Powershell vs bash), pojęcia są takie same między Linuksem i Windows.

## <a name="iot-edge-certificates"></a>Certyfikaty usługi IoT Edge

Zazwyczaj producenci nie są końcowymi użytkownikami urządzenia IoT Edge. Czasami jedyną relacją między nimi jest, gdy użytkownik końcowy lub operator kupuje ogólne urządzenie wykonane przez producenta. Innym razem producent pracuje zgodnie z umową, aby zbudować niestandardowe urządzenie dla operatora. Projekt certyfikatu IoT Edge próbuje wziąć pod uwagę oba scenariusze.

Na poniższym rysunku przedstawiono użycie certyfikatów przez ioT Edge. Między certyfikatem głównego urzędu certyfikacji a certyfikatem urzędu certyfikacji urządzenia może istnieć zero, jeden lub wiele pośrednich certyfikatów podpisywania, w zależności od liczby zaangażowanych jednostek. Tutaj pokazujemy jeden przypadek.

![Diagram typowych relacji certyfikatów](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Urząd certyfikacji

Urząd certyfikacji lub "Urząd certyfikacji" w skrócie, jest jednostką, która wystawia certyfikaty cyfrowe. Urząd certyfikacji działa jako zaufana strona trzecia między właścicielem a odbiorcą certyfikatu. Certyfikat cyfrowy zaświadcza o posiadaniu klucza publicznego przez odbiorcę certyfikatu. Łańcuch zaufania certyfikatów polega na pierwszym wydaniu certyfikatu głównego, który jest podstawą zaufania do wszystkich certyfikatów wystawionych przez urząd. Następnie właściciel może użyć certyfikatu głównego do wystawiania dodatkowych certyfikatów pośrednich (certyfikatów typu "leaf").

### <a name="root-ca-certificate"></a>Główny certyfikat urzędu certyfikacji

Główny certyfikat urzędu certyfikacji jest katalogiem głównym zaufania całego procesu. W scenariuszach produkcyjnych ten certyfikat urzędu certyfikacji jest zwykle kupowany od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, Verisign lub DigiCert. Jeśli masz pełną kontrolę nad urządzeniami łączącymi się z urządzeniami IoT Edge, możesz użyć urzędu certyfikacji na poziomie firmy. W obu przypadkach cały łańcuch certyfikatów z centrum usługi IoT Edge jest do niego zwężany, więc urządzenia IoT typu liść muszą ufać certyfikatowi głównemu. Certyfikat głównego urzędu certyfikacji można przechowywać w magazynie zaufanych głównych urzędów certyfikacji lub podać szczegóły certyfikatu w kodzie aplikacji.

### <a name="intermediate-certificates"></a>Certyfikaty pośrednie

W typowym procesie produkcyjnym do tworzenia bezpiecznych urządzeń certyfikaty głównego urzędu certyfikacji są rzadko używane bezpośrednio, głównie ze względu na ryzyko wycieku lub narażenia. Główny certyfikat urzędu certyfikacji tworzy i podpisuje cyfrowo jeden lub więcej pośrednich certyfikatów urzędu certyfikacji. Może istnieć tylko jeden lub może istnieć łańcuch tych certyfikatów pośrednich. Scenariusze, które wymagałyby łańcucha certyfikatów pośrednich obejmują:

* Hierarchia działów w obrębie producenta.

* Wiele firm zaangażowanych seryjnie w produkcję urządzenia.

* Klient kupując główny urząd certyfikacji i uzyskujący certyfikat podpisywania dla producenta, aby podpisać urządzenia, które tworzą w imieniu tego klienta.

W każdym przypadku producent używa pośredniego certyfikatu urzędu certyfikacji na końcu tego łańcucha, aby podpisać certyfikat urzędu certyfikacji urządzenia umieszczony na urządzeniu końcowym. Ogólnie rzecz biorąc, te certyfikaty pośrednie są ściśle strzeżone w zakładzie produkcyjnym. Przechodzą rygorystyczne procesy, zarówno fizyczne, jak i elektroniczne do ich użytkowania.

### <a name="device-ca-certificate"></a>Certyfikat urzędu certyfikacji urządzenia

Certyfikat urzędu certyfikacji urządzenia jest generowany i podpisywane przez końcowy certyfikat pośredniego urzędu certyfikacji w procesie. Ten certyfikat jest zainstalowany na samym urządzeniu usługi IoT Edge, najlepiej w bezpiecznym magazynie, takim jak sprzętowy moduł zabezpieczeń (HSM). Ponadto certyfikat urzędu certyfikacji urządzenia jednoznacznie identyfikuje urządzenie ZoT Edge. Certyfikat urzędu certyfikacji urządzenia może podpisywać inne certyfikaty.

### <a name="iot-edge-workload-ca"></a>Urząd certyfikacji obciążenia ioT edge

[Menedżer zabezpieczeń ioT Edge](iot-edge-security-manager.md) generuje certyfikat urzędu certyfikacji obciążenia, pierwszy po stronie "operatora" procesu, gdy usługa IoT Edge jest uruchamiana po raz pierwszy. Ten certyfikat jest generowany i podpisywane przez "certyfikat urzędu certyfikacji urządzenia". Ten certyfikat, który jest tylko inny certyfikat podpisywania pośredniego, jest używany do generowania i podpisywania innych certyfikatów używanych przez środowisko uruchomieniowe IoT Edge. Obecnie jest to przede wszystkim certyfikat serwera usługi IoT Edge omówione w poniższej sekcji, ale w przyszłości może zawierać inne certyfikaty do uwierzytelniania składników usługi IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certyfikat serwera usługi IoT Edge

Certyfikat serwera koncentratora usługi IoT Edge to rzeczywisty certyfikat prezentowany urządzeniom i modułom typu liść do weryfikacji tożsamości podczas ustanawiania połączenia TLS wymaganego przez usługę IoT Edge. Ten certyfikat przedstawia pełny łańcuch certyfikatów podpisywania używanych do generowania go do głównego certyfikatu urzędu certyfikacji, któremu urządzenie IoT typu liść musi ufać. Po wygenerowaniu przez Menedżera zabezpieczeń usługi IoT Edge nazwa pospolita (CN) tego certyfikatu usługi IoT Edge jest ustawiona na właściwość "nazwa hosta" w pliku config.yaml po konwersji na małe litery. Ta konfiguracja jest częstym źródłem pomyłek z usługą IoT Edge.

## <a name="production-implications"></a>Implikacje produkcyjne

Rozsądne pytanie może być "dlaczego usługa IoT Edge potrzebuje dodatkowego certyfikatu 'workload CA'? Czy certyfikat urzędu certyfikacji urządzenia nie mógł używać certyfikatu urzędu certyfikacji urządzenia do bezpośredniego generowania certyfikatu serwera usługi IoT Edge?". Technicznie, to może. Jednak celem tego certyfikatu pośredniego "obciążenia" jest oddzielenie problemów między producentem urządzenia a operatorem urządzenia. Wyobraź sobie scenariusz, w którym urządzenie IoT Edge jest sprzedawane lub przenoszone z jednego klienta do drugiego. Prawdopodobnie certyfikat urzędu certyfikacji urządzenia dostarczony przez producenta będzie niezmienny. Jednak certyfikaty "obciążenia" specyficzne dla działania urządzenia powinny zostać wyczyszczone i odtworzone dla nowego wdrożenia.

Ponieważ procesy produkcyjne i operacyjne są oddzielone, należy wziąć pod uwagę następujące implikacje podczas przygotowywania urządzeń produkcyjnych:

* W przypadku dowolnego procesu opartego na certyfikatach główny certyfikat urzędu certyfikacji i wszystkie pośrednie certyfikaty urzędu certyfikacji powinny być zabezpieczone i monitorowane podczas całego procesu wprowadzania urządzenia usługi IoT Edge. Producent urządzeń IoT Edge powinien mieć silne procesy w celu prawidłowego przechowywania i wykorzystania ich certyfikatów pośrednich. Ponadto certyfikat urzędu certyfikacji urządzenia powinien być przechowywany w możliwie bezpiecznej pamięci masowej na samym urządzeniu, najlepiej w module zabezpieczeń sprzętowych.

* Certyfikat serwera usługi IoT Edge jest prezentowany przez centrum usługi IoT Edge do łączących się urządzeń klienckich i modułów. Nazwa pospolita (CN) certyfikatu urzędu certyfikacji urządzenia **nie może być** taka sama jak "nazwa hosta", która będzie używana w pliku config.yaml na urządzeniu usługi IoT Edge. Nazwa używana przez klientów do łączenia się z usługą IoT Edge (na przykład za pośrednictwem parametru GatewayHostName ciągu połączenia lub polecenia CONNECT w MQTT) **nie może być** taka sama jak nazwa pospolita używana w certyfikacie urzędu certyfikacji urządzenia. To ograniczenie jest, ponieważ centrum usługi IoT Edge przedstawia cały łańcuch certyfikatów do weryfikacji przez klientów. Jeśli certyfikat serwera usługi IoT Edge i certyfikat urzędu certyfikacji urządzenia mają ten sam CN, zostanie ono w ysuwce weryfikacyjnej, a certyfikat unieważnia.

* Ponieważ certyfikat urzędu certyfikacji urządzenia jest używany przez demon zabezpieczeń IoT Edge do generowania ostatecznych certyfikatów IoT Edge, sam musi być certyfikatem podpisywania, co oznacza, że ma możliwości podpisywania certyfikatów. Zastosowanie "V3 Basic constraints CA:True" do certyfikatu urzędu certyfikacji urządzenia automatycznie konfiguruje wymagane właściwości użycia klucza.

>[!Tip]
> Jeśli już przeszedł konfiguracji IoT Edge jako przejrzysty bramy w scenariuszu deweloper/test przy użyciu naszych "skryptów wygody" (patrz następna sekcja) i używane tej samej nazwy hosta podczas tworzenia certyfikatu urzędu certyfikacji urządzenia, jak to miało miejsce dla nazwy hosta w config.yaml, możesz się zastanawiać, dlaczego to działało. Aby uprościć środowisko dewelopera, skrypty wygody dołącza ".ca" na końcu nazwy, którą przekazujesz do skryptu. Tak więc, na przykład, jeśli użyto "mygateway" zarówno dla nazwy urządzenia w skryptach i nazwę hosta w config.yaml, pierwszy zostanie przekształcony w mygateway.ca przed użyciem jako CN dla urządzenia certyfikat CA.

## <a name="devtest-implications"></a>Implikacje deweloperów/testów

Aby ułatwić tworzenie scenariuszy i testowanie, firma Microsoft udostępnia zestaw [skryptów wygody](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) do generowania certyfikatów nieprodukcyjnych odpowiednich dla usługi IoT Edge w scenariuszu bramy przezroczystej. Aby zapoznać się z przykładami działania skryptów, zobacz [Tworzenie certyfikatów demonstracyjnych w celu przetestowania funkcji urządzenia IoT Edge](how-to-create-test-certificates.md).

>[!Tip]
> Aby podłączyć urządzenia IoT "liść" urządzeń i aplikacji, które używają naszego urządzenia IoT SDK za pośrednictwem usługi IoT Edge, należy dodać opcjonalny parametr GatewayHostName na końcu ciągu połączenia urządzenia. Po wygenerowaniu certyfikatu serwera koncentratora usługi Edge, jest on oparty na wersji małe litery nazwy hosta z config.yaml, w związku z tym dla nazw do dopasowania i weryfikacji certyfikatu TLS zakończyć się pomyślnie, należy wprowadzić GatewayHostName parametr w niższych przypadkach.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Przykład hierarchii certyfikatów ioT Edge

Aby zilustrować przykład tej ścieżki certyfikatu, poniższy zrzut ekranu pochodzi z działającego urządzenia usługi IoT Edge skonfigurowanego jako przezroczysta brama. OpenSSL służy do łączenia się z centrum usługi IoT Edge, sprawdzania poprawności i wyrzucania certyfikatów.

![Zrzut ekranu przedstawiający hierarchię certyfikatów na każdym poziomie](./media/iot-edge-certs/iotedge-cert-chain.png)

Hierarchia głębokości certyfikatu jest widoczna na zrzucie ekranu:

| Główny certyfikat urzędu certyfikacji         | Tylko test certyfikatu urzędu certyfikacji usługi Azure IoT Hub                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Pośredni certyfikat urzędu certyfikacji | Tylko test certyfikatu pośredniego usługi Azure IoT Hub                                                                 |
| Certyfikat urzędu certyfikacji urządzenia       | iotgateway.ca ("iotgateway" został przekazany jako nazwa hosta bramy < > do skryptów wygody)   |
| Certyfikat urzędu certyfikacji obciążenia     | obciążenie iotedge ca                                                                                       |
| Certyfikat serwera usługi IoT Edge Hub | iotedgegw.local (pasuje do 'hostname' z config.yaml)                                            |

## <a name="next-steps"></a>Następne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)

[Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
