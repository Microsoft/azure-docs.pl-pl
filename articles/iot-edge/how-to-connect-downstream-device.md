---
title: Łączenie urządzeń podrzędnych — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Jak skonfigurować urządzenia podrzędne lub typu liść, aby połączyć się z urządzeniami bramy usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3113f01341d2a1ec6160cfea3eb9d12d18b8495c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687182"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge

Ten artykuł zawiera instrukcje dotyczące ustanawiania zaufanego połączenia między urządzeniami podrzędnymi i przezroczystymi bramami usługi IoT Edge. W scenariuszu bramy przezroczystej co najmniej jedno urządzenie może przekazywać swoje wiadomości za pośrednictwem jednego urządzenia bramy, które utrzymuje połączenie z centrum IoT Hub. Urządzeniem podrzędnym może być dowolna aplikacja lub platforma, która ma tożsamość utworzoną za pomocą usługi w chmurze [usługi Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) W wielu przypadkach te aplikacje używają [SDK urządzenia Usługi Azure IoT.](../iot-hub/iot-hub-devguide-sdks.md) Urządzenie podrzędne może być nawet aplikacją działającą na samym urządzeniu bramy usługi IoT Edge.

Istnieją trzy ogólne kroki konfigurowania pomyślnego połączenia bramy przezroczystej. Ten artykuł obejmuje trzeci krok:

1. Urządzenie bramy musi bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikację z urządzeń podrzędnych i kierować wiadomości do właściwego miejsca docelowego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do działania jako przezroczysta brama](how-to-create-transparent-gateway.md).
2. Urządzenie podrzędne wymaga tożsamości urządzenia, aby móc uwierzytelniać się za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie urządzenia podrzędnego w centrum Usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Urządzenie podrzędne musi bezpiecznie połączyć się z urządzeniem bramy.**

W tym artykule zidentyfikowano typowe problemy z połączeniami urządzeń podrzędnych i przewodnik po konfigurowaniu urządzeń podrzędnych przez:

* Objaśnienie zabezpieczeń warstwy transportu (TLS) i podstaw certyfikatów.
* Wyjaśnienie, jak biblioteki TLS działają w różnych systemach operacyjnych i jak każdy system operacyjny radzi sobie z certyfikatami.
* Przechodzenie przez przykłady IoT platformy Azure w kilku językach, aby ułatwić rozpoczęcie pracy.

W tym artykule *terminy bramy* i *bramy usługi IoT Edge* odnoszą się do urządzenia usługi IoT Edge skonfigurowanego jako brama przezroczysta.

## <a name="prerequisites"></a>Wymagania wstępne

* Plik certyfikatu **azure-iot-test-only.root.ca.cert.pem,** który został wygenerowany w [obszarze Konfigurowanie urządzenia usługi IoT Edge, aby działał jako przezroczysta brama](how-to-create-transparent-gateway.md) dostępna na urządzeniu podrzędnym. Urządzenie podrzędne używa tego certyfikatu do sprawdzania tożsamości urządzenia bramy.
* Mieć zmodyfikowany ciąg połączenia, który wskazuje na urządzenie bramy, jak wyjaśniono w [Uwierzytelnij urządzenie podrzędne do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Przygotowanie urządzenia podrzędnego

Urządzeniem podrzędnym może być dowolna aplikacja lub platforma, która ma tożsamość utworzoną za pomocą usługi w chmurze [usługi Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) W wielu przypadkach te aplikacje używają [SDK urządzenia Usługi Azure IoT.](../iot-hub/iot-hub-devguide-sdks.md) Urządzenie podrzędne może być nawet aplikacją działającą na samym urządzeniu bramy usługi IoT Edge. Jednak inne urządzenie Usługi IoT Edge nie może znajdować się za bramą usługi IoT Edge.

>[!NOTE]
>Urządzenia IoT, które mają tożsamości zarejestrowane w U usług IoT Hub, mogą używać [bliźniaczych modułów](../iot-hub/iot-hub-devguide-module-twins.md) do izolowania różnych procesów, sprzętu lub funkcji na jednym urządzeniu. Bramy usługi IoT Edge obsługują połączenia modułów podrzędnych przy użyciu uwierzytelniania klucza symetrycznego, ale nie uwierzytelniania certyfikatów X.509.

Aby podłączyć urządzenie podrzędne do bramy usługi IoT Edge, potrzebne są dwie rzeczy:

* Urządzenie lub aplikacja, która jest skonfigurowana z ciągiem połączenia urządzenia Usługi IoT Hub dołączone z informacjami, aby połączyć go z bramą.

    Ten krok jest wyjaśniony w [uwierzytelnij urządzenie podrzędne do usługi Azure IoT Hub.](how-to-authenticate-downstream-device.md)

* Urządzenie lub aplikacja musi ufać **głównej** certyfikacie urzędu certyfikacji bramy, aby sprawdzić poprawność połączeń TLS z urządzeniem bramy.

    Ten krok jest szczegółowo wyjaśniony w dalszej części tego artykułu. Ten krok można wykonać na jeden z dwóch sposobów: instalując certyfikat urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego lub (w przypadku niektórych języków) przez odwoływanie się do certyfikatu w aplikacjach przy użyciu zestawów SDK usługi Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>TLS i podstawy certyfikatów

Wyzwaniem bezpiecznego łączenia urządzeń podrzędnych z usługą IoT Edge jest jak każda inna bezpieczna komunikacja klient/serwer, która odbywa się za pośrednictwem Internetu. Klient i serwer bezpiecznie komunikują się przez Internet za pomocą [zabezpieczeń warstwy transportowej (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS jest zbudowany przy użyciu standardowych [konstrukcji infrastruktury klucza publicznego (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) o nazwie certyfikaty. TLS jest dość zaangażowanych specyfikacji i dotyczy szeroki zakres tematów związanych z zabezpieczaniem dwóch punktów końcowych. W tej sekcji podsumowano pojęcia istotne dla bezpiecznego łączenia urządzeń z bramą usługi IoT Edge.

Gdy klient łączy się z serwerem, serwer przedstawia łańcuch certyfikatów, nazywany *łańcuchem certyfikatów serwera*. Łańcuch certyfikatów zazwyczaj składa się z głównego certyfikatu urzędu certyfikacji, jednego lub kilku pośrednich certyfikatów urzędu certyfikacji i wreszcie samego certyfikatu serwera. Klient ustanawia zaufanie z serwerem, sprawdzając kryptograficznie cały łańcuch certyfikatów serwera. To sprawdzanie poprawności klienta łańcucha certyfikatów serwera jest nazywane *sprawdzanie poprawności łańcucha serwera*. Klient kryptograficznie wyzywa usługę do udowodnienia posiadania klucza prywatnego skojarzonego z certyfikatem serwera w procesie zwanym *dowodem posiadania*. Połączenie sprawdzania poprawności łańcucha serwerów i potwierdzenia posiadania nazywa się *uwierzytelnianiem serwera.* Aby sprawdzić poprawność łańcucha certyfikatów serwera, klient potrzebuje kopii certyfikatu głównego urzędu certyfikacji, który został użyty do utworzenia (lub wydania) certyfikatu serwera. Zwykle podczas łączenia się z witrynami sieci Web przeglądarka jest wstępnie skonfigurowana z powszechnie używanymi certyfikatami urzędu certyfikacji, dzięki czemu klient ma bezproblemowy proces.

Gdy urządzenie łączy się z usługą Azure IoT Hub, urządzenie jest klientem, a usługa w chmurze usługi Usługi IoT Hub jest serwerem. Usługa w chmurze usługi IoT Hub jest obsługiwana przez główny certyfikat urzędu certyfikacji o nazwie **Baltimore CyberTrust Root**, który jest publicznie dostępny i szeroko stosowany. Ponieważ certyfikat urzędu certyfikacji usługi IoT Hub jest już zainstalowany na większości urządzeń, wiele implementacji TLS (OpenSSL, Schannel, LibreSSL) automatycznie używa go podczas sprawdzania poprawności certyfikatu serwera. Urządzenie, które może pomyślnie połączyć się z Centrum IoT może mieć problemy z próbą połączenia się z bramą usługi IoT Edge.

Gdy urządzenie łączy się z bramą usługi IoT Edge, urządzeniem podrzędnym jest klient, a urządzenie bramy jest serwerem. Usługa Azure IoT Edge umożliwia operatorom (lub użytkownikom) tworzenie łańcuchów certyfikatów bramy, niezależnie od ich zgodności. Operator może użyć publicznego certyfikatu urzędu certyfikacji, takiego jak Baltimore, lub użyć certyfikatu głównego urzędu certyfikacji z podpisem własnym (lub w domu). Publiczne certyfikaty urzędu certyfikacji często mają z nimi związane koszty, więc są zwykle używane w scenariuszach produkcyjnych. Certyfikaty urzędu certyfikacji z podpisem własnym są preferowane do programowania i testowania. Artykuły konfiguracji bramy przezroczystej wymienione we wstępie używają certyfikatów głównego urzędu certyfikacji z podpisem własnym.

W przypadku korzystania z certyfikatu głównego urzędu certyfikacji z podpisem własnym dla bramy usługi IoT Edge musi on zostać zainstalowany lub dostarczony wszystkim urządzeniom podrzędnym, które próbują połączyć się z bramą.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

Aby dowiedzieć się więcej o certyfikatach IoT Edge i niektórych implikacjach produkcyjnych, zobacz [Szczegóły użycia certyfikatu IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Podaj główny certyfikat urzędu certyfikacji

Aby zweryfikować certyfikaty urządzenia bramy, urządzenie podrzędne wymaga własnej kopii certyfikatu głównego urzędu certyfikacji. Jeśli do tworzenia certyfikatów testowych użyto skryptów podanych w repozytorium git usługi IoT Edge, certyfikat głównego urzędu certyfikacji nosi nazwę **azure-iot-test-only.root.ca.cert.pem**. Jeśli nie masz jeszcze w ramach innych kroków przygotowania urządzenia podrzędnego, przenieś ten plik certyfikatu do dowolnego katalogu na urządzeniu podrzędnym. Aby przenieść plik certyfikatu, można użyć usługi takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takiej jak secure copy [protocol.](https://www.ssh.com/ssh/scp/)

## <a name="install-certificates-in-the-os"></a>Instalowanie certyfikatów w os

Instalowanie certyfikatu głównego urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego zazwyczaj umożliwia większości aplikacji korzystanie z głównego certyfikatu urzędu certyfikacji. Istnieją pewne wyjątki, takie jak aplikacje NodeJS, które nie używają magazynu certyfikatów systemu operacyjnego, ale raczej używają wewnętrznego magazynu certyfikatów środowiska wykonawczego węzła. Jeśli nie możesz zainstalować certyfikatu na poziomie systemu operacyjnego, przejdź do [witryny Użyj certyfikatów z zestawami SDK usługi Azure IoT](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Poniższe polecenia są przykładem instalacji certyfikatu urzędu certyfikacji na hoście Ubuntu. W tym przykładzie przyjęto założenie, że używasz certyfikatu **azure-iot-test-only.root.ca.cert.pem** z artykułów wymagań wstępnych i że certyfikat został skopiowany do lokalizacji na urządzeniu podrzędnym.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Powinien zostać wyświetlony komunikat z informacją: "Aktualizowanie certyfikatów w /etc/ssl/certs... 1 dodany, 0 usunięty; zrobić."

### <a name="windows"></a>Windows

Poniższe kroki są przykładem instalowania certyfikatu urzędu certyfikacji na hoście systemu Windows. W tym przykładzie przyjęto założenie, że używasz certyfikatu **azure-iot-test-only.root.ca.cert.pem** z artykułów wymagań wstępnych i że certyfikat został skopiowany do lokalizacji na urządzeniu podrzędnym.

Certyfikaty można instalować przy użyciu [certyfikatu importu](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) programu PowerShell jako administratora:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Certyfikaty można również instalować za pomocą narzędzia **certlm:**

1. W menu Start wyszukaj i wybierz pozycję **Zarządzaj certyfikatami komputerów**. Zostanie otwarte narzędzie o nazwie **certlm.**
2. Przejdź do **pozycji Certyfikaty — zaufane** > **główne urzędy certyfikacji**komputera lokalnego .
3. Kliknij prawym przyciskiem myszy **pozycję Certyfikaty** i wybierz polecenie**Importuj** **wszystkie zadania** > . Kreator importu certyfikatów powinien zostać uruchomiony.
4. Wykonaj kroki zgodnie z zaleceniami i zaimportuj plik `<path>/azure-iot-test-only.root.ca.cert.pem`certyfikatu . Po zakończeniu powinien zostać wyświetlony komunikat "Pomyślnie zaimportowany".

Certyfikaty można również instalować programowo przy użyciu interfejsów API platformy .NET, jak pokazano w przykładzie .NET w dalszej części tego artykułu.

Zazwyczaj aplikacje używają stosu TLS dostarczonego przez system Windows o nazwie [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) do bezpiecznego łączenia się za pomocą protokołu TLS. Schannel *wymaga* zainstalowania wszystkich certyfikatów w magazynie certyfikatów systemu Windows przed podjęciem próby nawiązania połączenia TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Używanie certyfikatów z zestawami SDK usługi Azure IoT

W tej sekcji opisano, jak zestawY SDK usługi Azure IoT łączą się z urządzeniem usługi IoT Edge przy użyciu prostych przykładowych aplikacji. Celem wszystkich przykładów jest połączenie klienta urządzenia i wysyłanie wiadomości telemetrycznych do bramy, a następnie zamknięcie połączenia i zakończenia.

Przygotuj dwie rzeczy przed użyciem próbek na poziomie aplikacji:

* Parametry połączenia usługi IoT Hub urządzenia podrzędnego zmodyfikowane w celu wskazanie urządzenia bramy oraz wszystkie certyfikaty wymagane do uwierzytelnienia urządzenia podrzędnego w centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie urządzenia podrzędnego w centrum Usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Pełna ścieżka do głównego certyfikatu urzędu certyfikacji, który został skopiowany i zapisany gdzieś na urządzeniu podrzędnym.

    Na przykład `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Ta sekcja zawiera przykładową aplikację do łączenia klienta urządzenia Usługi Azure IoT NodeJS z bramą usługi IoT Edge. W przypadku aplikacji NodeJS należy zainstalować główny certyfikat urzędu certyfikacji na poziomie aplikacji, jak pokazano poniżej. Aplikacje NodeJS nie korzystają z magazynu certyfikatów systemu.

1. Pobierz przykład dla **edge_downstream_device.js** z [zestawem SDK urządzenia Usługi Azure IoT dla repozytorium próbek Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Upewnij się, że masz wszystkie wymagania wstępne do uruchomienia próbki, przeglądając plik **readme.md.**
3. W pliku edge_downstream_device.js zaktualizuj **zmienne connectionString** i **edge_ca_cert_path.**
4. Zapoznaj się z dokumentacją SDK, aby uzyskać instrukcje dotyczące uruchamiania próbki na urządzeniu.

Aby zrozumieć przykład, który jest uruchomiony, poniższy fragment kodu jest jak klient SDK odczytuje plik certyfikatu i używa go do ustanowienia bezpiecznego połączenia TLS:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

W tej sekcji przedstawiono przykładową aplikację do łączenia klienta urządzenia Usługi Azure IoT .NET z bramą usługi IoT Edge. Jednak aplikacje .NET mogą automatycznie używać zainstalowanych certyfikatów w magazynie certyfikatów systemu zarówno na hostach systemu Linux, jak i Windows.

1. Pobierz przykład dla **EdgeDownstreamDevice** z [folderu próbki IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Upewnij się, że masz wszystkie wymagania wstępne do uruchomienia próbki, przeglądając plik **readme.md.**
3. W pliku **Właściwości / launchSettings.json** zaktualizuj zmienne **DEVICE_CONNECTION_STRING** i **CA_CERTIFICATE_PATH.** Jeśli chcesz użyć certyfikatu zainstalowanego w magazynie certyfikatów zaufanych w systemie hosta, pozostaw tę zmienną pustą.
4. Zapoznaj się z dokumentacją SDK, aby uzyskać instrukcje dotyczące uruchamiania próbki na urządzeniu.

Aby programowo zainstalować zaufany certyfikat w magazynie certyfikatów za pomocą aplikacji .NET, należy zapoznać się z funkcją **InstallCACert()** w pliku **EdgeDownstreamDevice / Program.cs.** Ta operacja jest idempotentna, więc można uruchomić wiele razy z tymi samymi wartościami bez dodatkowego efektu.

### <a name="c"></a>C

W tej sekcji przedstawiono przykładową aplikację do łączenia klienta urządzenia Usługi Azure IoT C z bramą usługi IoT Edge. C SDK może współpracować z wieloma bibliotekami TLS, w tym OpenSSL, WolfSSL i Schannel. Aby uzyskać więcej informacji, zobacz [zestaw SDK języka Azure IoT C](https://github.com/Azure/azure-iot-sdk-c).

1. Pobierz **aplikację iotedge_downstream_device_sample** z [sdk urządzenia Usługi Azure IoT dla przykładów języka C.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
2. Upewnij się, że masz wszystkie wymagania wstępne do uruchomienia próbki, przeglądając plik **readme.md.**
3. W pliku iotedge_downstream_device_sample.c zaktualizuj **zmienne connectionString** i **edge_ca_cert_path.**
4. Zapoznaj się z dokumentacją SDK, aby uzyskać instrukcje dotyczące uruchamiania próbki na urządzeniu.

Zestaw SDK urządzenia Usługi Azure IoT dla języka C umożliwia zarejestrowanie certyfikatu urzędu certyfikacji podczas konfigurowania klienta. Ta operacja nie instaluje certyfikatu w dowolnym miejscu, ale raczej używa formatu ciągu certyfikatu w pamięci. Zapisany certyfikat jest dostarczany do podstawowego stosu TLS podczas ustanawiania połączenia.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Jeśli na hostach systemu Windows nie jest używany protokół OpenSSL lub innej biblioteki TLS, przy użyciu schannel domyślnie jest używany plik Schannel. Aby schannel działał, certyfikat głównego urzędu certyfikacji usługi IoT Edge powinien `IoTHubDeviceClient_SetOption` być zainstalowany w magazynie certyfikatów systemu Windows, a nie ustawiony przy użyciu tej operacji.

### <a name="java"></a>Java

W tej sekcji przedstawiono przykładową aplikację do łączenia klienta urządzenia Java usługi Azure z bramą usługi IoT Edge.

1. Pobierz przykład **zdarzenia wysyłania** z [sdk urządzenia Usługi Azure IoT dla próbek języka Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Upewnij się, że masz wszystkie wymagania wstępne do uruchomienia próbki, przeglądając plik **readme.md.**
3. Zapoznaj się z dokumentacją SDK, aby uzyskać instrukcje dotyczące uruchamiania próbki na urządzeniu.

### <a name="python"></a>Python

W tej sekcji przedstawiono przykładową aplikację do łączenia klienta urządzenia Usługi Azure IoT Python z bramą usługi IoT Edge.

1. Pobierz przykład dla **send_message** z [zestawu SDK urządzenia Usługi Azure IoT dla przykładów języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Upewnij się, że są uruchomione w kontenerze usługi IoT Edge `EdgeHubConnectionString` lub `EdgeModuleCACertificateFile` w scenariuszu debugowania, mają i środowiska zmienne ustawione.
3. Zapoznaj się z dokumentacją SDK, aby uzyskać instrukcje dotyczące uruchamiania próbki na urządzeniu.

## <a name="test-the-gateway-connection"></a>Testowanie połączenia bramy

Użyj tego przykładowego polecenia, aby sprawdzić, czy urządzenie podrzędne może łączyć się z urządzeniem bramy:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

To polecenie testuje połączenia za pomocą MQTTS (port 8883). Jeśli używasz innego protokołu, dostosuj to polecenie w razie potrzeby dla protokołu AMQPS (5671) lub HTTPS (433)

Dane wyjściowe tego polecenia mogą być długie, w tym informacje o wszystkich certyfikatach w łańcuchu. Jeśli połączenie zakończy się pomyślnie, zobaczysz `Verification: OK` linię `Verify return code: 0 (ok)`podobną do lub .

![Weryfikowanie połączenia bramy](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Rozwiązywanie problemów z połączeniem bramy

Jeśli urządzenie typu liść ma sporadyczne połączenie z urządzeniem bramy, spróbuj wykonać następujące kroki, aby uzyskać rozdzielczość.

1. Czy nazwa hosta bramy w ciągu połączenia jest taka sama jak wartość nazwy hosta w pliku config.yaml usługi IoT Edge na urządzeniu bramy?
2. Czy nazwa hosta bramy można rozwiązać z adresem IP? Sporadyczne połączenia można rozwiązać za pomocą systemu DNS lub dodając wpis pliku hosta na urządzeniu typu liść.
3. Czy porty komunikacyjne są otwarte w zaporze? Komunikacja oparta na używanym protokole (MQTTS:8883/AMQPS:5671/HTTPS:433) musi być możliwa między urządzeniem podrzędnym a przezroczystą krawędzią IoT.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa IoT Edge może rozszerzać [możliwości trybu offline](offline-capabilities.md) na urządzenia podrzędne.
