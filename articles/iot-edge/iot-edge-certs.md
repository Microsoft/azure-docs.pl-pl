---
title: Certyfikaty dla zabezpieczeń urządzeń — Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge używa certyfikatu do weryfikowania urządzeń, modułów i urządzeń liściowych oraz ustanawiania bezpiecznych połączeń między nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: ffe2f2b7f94d546cdfe393170da2fd2ca6ac0149
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490997"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Dowiedz się, jak Azure IoT Edge używa certyfikatów

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge certyfikaty są używane przez moduły i podrzędne urządzenia IoT do weryfikacji tożsamości i poprawności modułu [IoT Edge centrum](iot-edge-runtime.md#iot-edge-hub) środowiska uruchomieniowego. Te weryfikacje umożliwiają bezpieczne połączenie TLS (Transport Layer Security) między środowiskiem uruchomieniowym, modułami i urządzeniami IoT. Podobnie jak IoT Hub, IoT Edge wymaga bezpiecznego i szyfrowanego połączenia z urządzeń podrzędnych (lub liściowych IoT) i modułów IoT Edge. Aby nawiązać bezpieczne połączenie TLS, moduł IoT Edge Hub przedstawia łańcuch certyfikatów serwera do łączenia klientów, aby zweryfikować swoją tożsamość.

>[!NOTE]
>W tym artykule omówiono certyfikaty, które są używane do zabezpieczania połączeń między różnymi składnikami na urządzeniu IoT Edge lub między urządzeniem IoT Edge i wszystkimi urządzeniami typu liść. Możesz również użyć certyfikatów do uwierzytelnienia urządzenia IoT Edge, aby IoT Hub. Te certyfikaty uwierzytelniania są różne i nie zostały omówione w tym artykule. Aby uzyskać więcej informacji na temat uwierzytelniania urządzenia za pomocą certyfikatów, zobacz [Tworzenie i udostępnianie urządzenia IoT Edge przy użyciu certyfikatów X. 509](how-to-auto-provision-x509-certs.md).

W tym artykule wyjaśniono, jak IoT Edge certyfikaty mogą funkcjonować w scenariuszach produkcyjnych, deweloperskich i testowych. Chociaż skrypty są różne (program PowerShell a bash), pojęcia są takie same w systemach Linux i Windows.

## <a name="iot-edge-certificates"></a>Certyfikaty usługi IoT Edge

Istnieją dwa typowe scenariusze dotyczące konfigurowania certyfikatów na urządzeniu IoT Edge. Czasami użytkownik końcowy lub operator, który dokonał zakupu urządzenia ogólnego przez producenta, następnie zarządza certyfikatami. W innych przypadkach producent działa w ramach kontraktu, aby zbudować niestandardowe urządzenie dla operatora i wykonał wstępne podpisywanie certyfikatu przed przekazaniem urządzenia. Projektowanie certyfikatów IoT Edge próbuje wykonać oba scenariusze.

Poniższy rysunek ilustruje użycie certyfikatów przez IoT Edge. Między certyfikatem głównego urzędu certyfikacji a certyfikatem urzędu certyfikacji urządzenia może istnieć zero, jeden lub wiele pośrednich certyfikatów podpisywania, w zależności od liczby powiązanych jednostek. W tym miejscu pokazujemy jeden przypadek.

![Diagram typowych relacji certyfikatów](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038. To ograniczenie ma zastosowanie do certyfikatu urzędu certyfikacji urządzenia, wszystkich certyfikatów w pakiecie zaufania oraz certyfikatów identyfikatorów urządzeń używanych do metod aprowizacji X. 509.

:::moniker-end

### <a name="certificate-authority"></a>Urząd certyfikacji

Urząd certyfikacji (CA) jest w krótkim przypadku jednostką, która wystawia certyfikaty cyfrowe. Urząd certyfikacji działa jako zaufana osoba trzecia między właścicielem i odbiorcą certyfikatu. Certyfikat cyfrowy poświadcza własność klucza publicznego przez odbiorcę certyfikatu. Łańcuch certyfikatów zaufania działa początkowo przez wystawienie certyfikatu głównego, który stanowi podstawę do zaufania we wszystkich certyfikatach wystawionych przez urząd certyfikacji. Następnie właściciel może użyć certyfikatu głównego w celu wystawienia dodatkowych certyfikatów pośrednich ("liści").

### <a name="root-ca-certificate"></a>Certyfikat głównego urzędu certyfikacji

Certyfikat głównego urzędu certyfikacji jest głównym elementem zaufania całego procesu. W scenariuszach produkcyjnych ten certyfikat urzędu certyfikacji jest zwykle kupowany z zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, VeriSign lub DigiCert. Jeśli masz pełną kontrolę nad urządzeniami łączącymi się z IoT Edge urządzeniami, możliwe jest korzystanie z urzędu certyfikacji na poziomie firmy. W każdym z tych zdarzeń cały łańcuch certyfikatów z Centrum IoT Edge do niego, dlatego urządzenia IoT typu liść muszą ufać certyfikatowi głównemu. Certyfikat głównego urzędu certyfikacji można przechowywać w magazynie zaufanych głównych urzędów certyfikacji lub podawać szczegóły certyfikatu w kodzie aplikacji.

### <a name="intermediate-certificates"></a>Certyfikaty pośrednie

W typowym procesie produkcji do tworzenia bezpiecznych urządzeń certyfikaty głównego urzędu certyfikacji są rzadko używane bezpośrednio, głównie ze względu na ryzyko wycieku lub ekspozycji. Certyfikat głównego urzędu certyfikacji tworzy i podpisuje cyfrowo jeden lub więcej certyfikatów pośrednich urzędów certyfikacji. Może istnieć tylko jeden lub istnieje łańcuch tych certyfikatów pośrednich. Scenariusze, które wymagają łańcucha certyfikatów pośrednich, obejmują:

* Hierarchia działów należących do producenta.

* Wiele firm jest wykonywanych w szeregowej fazie produkcyjnej urządzenia.

* Klient dokonujący zakupu głównego urzędu certyfikacji i wystawiającego certyfikat podpisywania producentowi w celu podpisania urządzeń w imieniu tego klienta.

W każdym przypadku producent korzysta z certyfikatu pośredniego urzędu certyfikacji na końcu tego łańcucha, aby podpisać certyfikat urzędu certyfikacji urządzenia umieszczony na urządzeniu końcowym. Ogólnie rzecz biorąc, te certyfikaty pośrednie są ściśle chronione w zakładzie produkcyjnym. Są one poddawane ścisłym procesom, zarówno fizycznym, jak i elektronicznym do ich użycia.

### <a name="device-ca-certificate"></a>Certyfikat urzędu certyfikacji urządzenia

Certyfikat urzędu certyfikacji urządzenia jest generowany z i podpisany przez końcowy certyfikat pośredniego urzędu certyfikacji w procesie. Ten certyfikat jest instalowany na urządzeniu IoT Edge, najlepiej w bezpiecznym magazynie, takim jak sprzętowy moduł zabezpieczeń (HSM). Ponadto certyfikat urzędu certyfikacji urządzenia jednoznacznie identyfikuje urządzenie IoT Edge. Certyfikat urzędu certyfikacji urządzenia może podpisać inne certyfikaty.

### <a name="iot-edge-workload-ca"></a>IoT Edge urząd certyfikacji obciążeń

Program [IoT Edge Security Manager](iot-edge-security-manager.md) generuje certyfikat urzędu certyfikacji obciążenia, pierwszy na stronie "operator" procesu, IoT Edge po pierwszym uruchomieniu. Ten certyfikat jest generowany z i podpisany przez certyfikat urzędu certyfikacji urządzenia. Ten certyfikat, który jest tylko innym pośrednim certyfikatem podpisywania, służy do generowania i podpisywania innych certyfikatów używanych przez środowisko uruchomieniowe IoT Edge. Obecnie jest to przede wszystkim certyfikat serwera IoT Edge Hub omówiony w poniższej sekcji, ale w przyszłości może on obejmować inne certyfikaty służące do uwierzytelniania składników IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certyfikat serwera IoT Edge Hub

Certyfikat serwera Centrum IoT Edge jest rzeczywistym certyfikatem przedstawianym urządzeniom liściowym i modułom w celu weryfikacji tożsamości podczas ustanawiania połączenia TLS wymaganego przez IoT Edge. Ten certyfikat przedstawia pełny łańcuch certyfikatów podpisywania użytych do wygenerowania certyfikatu do głównego urzędu certyfikacji, który musi być zaufany dla liści IoT. Gdy jest generowany przez IoT Edge, nazwa pospolita (CN) tego certyfikatu Centrum IoT Edge jest ustawiona na Właściwość "hostname" w pliku konfiguracji po konwersji na niższą literę. Ta konfiguracja jest wspólnym źródłem pomyłek w IoT Edge.

## <a name="production-implications"></a>Implikacje produkcji

Uzasadnione pytaniem może być "Dlaczego IoT Edge potrzebujesz dodatkowego certyfikatu" obciążenie urzędu certyfikacji "? Nie można użyć certyfikatu urzędu certyfikacji urządzenia do bezpośredniego wygenerowania certyfikatu serwera IoT Edge Hub? ". Technicznie może. Jednak celem tego certyfikatu pośredniego "obciążenie" jest oddzielenie problemów między producentem urządzenia i operatorem urządzenia. Wyobraź sobie scenariusz, w którym urządzenie IoT Edge jest sprzedawane lub przesyłane z jednego klienta do innego. Prawdopodobnie certyfikat urzędu certyfikacji urządzenia dostarczony przez producenta powinien być niezmienny. Jednak certyfikaty "obciążenie" specyficzne dla działania urządzenia powinny być wyczyszczone i ponownie utworzone dla nowego wdrożenia.

Ponieważ procesy produkcyjne i operacyjne są rozdzielone, podczas przygotowywania urządzeń produkcyjnych należy wziąć pod uwagę następujące konsekwencje:

* W przypadku dowolnego procesu opartego na certyfikatach certyfikat głównego urzędu certyfikacji i wszystkie certyfikaty pośredniego urzędu certyfikacji powinny być zabezpieczone i monitorowane w trakcie całego procesu wdrażania IoT Edgego urządzenia. Producent urządzenia IoT Edge powinien dysponować silnymi procesami w celu zapewnienia prawidłowego magazynu i użycia ich certyfikatów pośrednich. Ponadto certyfikat urzędu certyfikacji urządzenia powinien być przechowywany jako bezpieczny magazyn, jak to możliwe na urządzeniu, najlepiej do sprzętowego modułu zabezpieczeń.

* Certyfikat serwera Centrum IoT Edge jest przedstawiany przez Centrum IoT Edge do łączenia urządzeń i modułów klienckich. Nazwa pospolita (CN) certyfikatu urzędu certyfikacji urządzenia **nie może być** taka sama jak wartość "hostname", która będzie używana w pliku konfiguracyjnym na urządzeniu IoT Edge. Nazwa używana przez klientów do łączenia się z IoT Edge (na przykład za pośrednictwem parametru GatewayHostName parametrów połączenia lub polecenia CONNECT w MQTT) **nie może być** taka sama jak nazwa pospolita użyta w certyfikacie urzędu certyfikacji. To ograniczenie wynika z faktu, że Centrum IoT Edge prezentuje cały łańcuch certyfikatów do weryfikacji przez klientów. Jeśli certyfikat serwera Centrum IoT Edge i certyfikat urzędu certyfikacji urządzenia mają ten sam CN, uzyskasz pętlę weryfikacyjną i unieważnia certyfikat.

* Ponieważ certyfikat urzędu certyfikacji urządzenia jest używany przez demona zabezpieczeń IoT Edge w celu wygenerowania końcowych certyfikatów IoT Edge, musi być to certyfikat podpisywania, co oznacza, że ma ona możliwości podpisywania certyfikatu. Zastosowanie do certyfikatu urzędu certyfikacji "v3 Basic Constraints CA: true" powoduje automatyczne skonfigurowanie wymaganych właściwości użycia klucza.

>[!Tip]
> Jeśli w scenariuszu deweloperskiego/testowego za pomocą naszych "wygodnych skryptów" została już wykonana konfiguracja IoT Edge jako transparentowa Brama (zobacz następną sekcję) i użyto tej samej nazwy hosta podczas tworzenia certyfikatu urzędu certyfikacji jako nazwy hosta w pliku konfiguracji, może być zastanawiasz się, dlaczego działa. W celu uproszczenia środowiska programistycznego, wygodne skrypty dołącza ". ca" na końcu nazwy przekazanej do skryptu. Tak więc, na przykład, jeśli w pliku konfiguracyjnym użyto elementu "Moja Gateway" dla nazwy urządzenia w skryptach i nazwie hosta, dawniej zostanie on włączony do mygateway.ca przed użyciem jako CN dla certyfikatu urzędu certyfikacji.

## <a name="devtest-implications"></a>Implikacje tworzenia i testowania

Aby ułatwić tworzenie i testowanie scenariuszy, firma Microsoft udostępnia zestaw [wygodnych skryptów](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) służących do generowania certyfikatów nieprodukcyjnych odpowiednich dla IoT Edge w scenariuszu dla bramy przezroczystej. Przykłady działania skryptów znajdują się w temacie [Create demonstracyjne Certificates to test IoT Edge Features](how-to-create-test-certificates.md).

>[!Tip]
> Aby połączyć urządzenia "liścia" IoT "i aplikacje korzystające z naszego zestawu SDK urządzenia IoT za pomocą IoT Edge, należy dodać opcjonalny parametr GatewayHostName na końcu parametrów połączenia urządzenia. Po wygenerowaniu certyfikatu serwera centrum brzegowego jest on oparty na niższej wersji nazwy hosta z pliku konfiguracji, dlatego w celu pomyślnego nadania nazw i weryfikacji certyfikatu TLS należy wprowadzić parametr GatewayHostName w małych przypadkach.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Przykład hierarchii certyfikatów IoT Edge

Aby zilustrować przykład tej ścieżki certyfikatu, Poniższy zrzut ekranu pochodzi z działającego urządzenia IoT Edge skonfigurowany jako nieprzezroczysta brama. OpenSSL jest używany do nawiązywania połączenia z Centrum IoT Edge, weryfikowania i zrzucania certyfikatów.

![Zrzut ekranu hierarchii certyfikatów na każdym poziomie](./media/iot-edge-certs/iotedge-cert-chain.png)

Hierarchię głębokości certyfikatów można zobaczyć na zrzucie ekranu:

| Certyfikat głównego urzędu certyfikacji         | Tylko test certyfikatu urzędu certyfikacji IoT Hub platformy Azure                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Pośredni certyfikat urzędu certyfikacji | Tylko test certyfikatu pośredniego IoT Hub platformy Azure                                                                 |
| Certyfikat urzędu certyfikacji urządzenia       | iotgateway.ca ("iotgateway" został przekazano jako nazwę hosta bramy < > do wygodnych skryptów)   |
| Certyfikat urzędu certyfikacji obciążenia     | Urząd certyfikacji obciążeń iotedge                                                                                       |
| Certyfikat serwera IoT Edge Hub | iotedgegw. Local (dopasowuje nazwę "hostname" z pliku konfiguracji)                                            |

## <a name="next-steps"></a>Następne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)

[Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
