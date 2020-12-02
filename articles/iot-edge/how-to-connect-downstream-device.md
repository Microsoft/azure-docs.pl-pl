---
title: Połącz urządzenia podrzędne — Azure IoT Edge | Microsoft Docs
description: Jak skonfigurować urządzenia podrzędne lub liściowe w celu nawiązania połączenia z urządzeniami bramy Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 35bb17a5cdfcc6aff4a513a594a08283ab1f1305
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437035"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge

Ten artykuł zawiera instrukcje dotyczące ustanawiania zaufanego połączenia między urządzeniami podrzędnymi a IoT Edge niewidocznymi bramami. W przypadku niejawnego scenariusza z bramą co najmniej jedno urządzenie może przekazać swoje wiadomości za pomocą jednego urządzenia bramy, które utrzymuje połączenie z IoT Hub.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono trzeci krok:

1. Skonfiguruj urządzenie bramy jako serwer, aby urządzenia podrzędne mogły bezpiecznie się z nim połączyć. Skonfiguruj bramę do odbierania komunikatów z urządzeń podrzędnych i Roześlij je do odpowiednich miejsc docelowych. Aby zapoznać się z tymi krokami, zobacz [Konfigurowanie urządzenia IoT Edge jako nieprzezroczystej bramy](how-to-create-transparent-gateway.md).
2. Utwórz tożsamość urządzenia dla urządzenia podrzędnego, aby można było uwierzytelnić się za pomocą IoT Hub. Skonfiguruj urządzenie podrzędne do wysyłania komunikatów za pomocą urządzenia bramy. Aby zapoznać się z tymi krokami, zobacz temat [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Podłącz urządzenie podrzędne do urządzenia bramy i Rozpocznij wysyłanie komunikatów.**

W tym artykule omówiono podstawowe pojęcia związane z połączeniami z urządzeniami podrzędnymi oraz wskazówki dotyczące konfigurowania urządzeń podrzędnych w następujący sposób:

* Objaśnienie zabezpieczeń TLS (Transport Layer Security) i certyfikatów.
* Wyjaśnienie, jak biblioteki protokołu TLS działają w różnych systemach operacyjnych i jak każdy system operacyjny zajmuje się certyfikatami.
* Poznaj przykłady usługi Azure IoT w kilku językach, aby pomóc Ci rozpocząć pracę.

W tym artykule warunki bramy *Gateway* i *IoT Edge Gateway* odnoszą się do urządzenia IoT Edge skonfigurowanego jako nieprzezroczysta brama.

## <a name="prerequisites"></a>Wymagania wstępne

* Mieć plik certyfikatu głównego urzędu certyfikacji, który został użyty do wygenerowania certyfikatu urzędu certyfikacji urządzenia w obszarze [Konfigurowanie urządzenia IoT Edge do działania jako niewidoczna Brama](how-to-create-transparent-gateway.md) dostępna na urządzeniu podrzędnym. Urządzenie podrzędne używa tego certyfikatu do weryfikowania tożsamości urządzenia bramy. W przypadku korzystania z certyfikatów demonstracyjnych certyfikat głównego urzędu certyfikacji nosi nazwę **Azure-IoT-test-Only. root. ca. CERT. pem**.
* Mają zmodyfikowane parametry połączenia wskazujące na urządzenie bramy, zgodnie z opisem w temacie [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Przygotowywanie urządzenia podrzędnego

Urządzenie podrzędne może być dowolną aplikacją lub platformą, która ma tożsamość utworzoną za pomocą usługi Azure IoT Hub w chmurze. W wielu przypadkach te aplikacje używają [zestawu SDK urządzenia usługi Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Urządzenie podrzędne może nawet być aplikacją działającą na samym urządzeniu bramy IoT Edge. Jednak inne urządzenie IoT Edge nie może być niższe niż Brama IoT Edge.

>[!NOTE]
>Urządzenia IoT zarejestrowane w IoT Hub mogą używać [modułu bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md) do izolowania różnych procesów, sprzętu lub funkcji na jednym urządzeniu. Bramy IoT Edge obsługują połączenia modułu podrzędnego przy użyciu uwierzytelniania za pomocą klucza symetrycznego, ale nie uwierzytelniania certyfikatu X. 509.

Aby połączyć urządzenie podrzędne z bramą IoT Edge, potrzebne są dwie rzeczy:

* Urządzenie lub aplikacja, która jest skonfigurowana za pomocą parametrów połączenia urządzenia IoT Hub dołączone informacje, aby połączyć je z bramą.

    Ten krok został ukończony w poprzednim artykule, [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* Urządzenie lub aplikacja musi ufać **certyfikatowi głównego urzędu certyfikacji** bramy, aby zweryfikować połączenia protokołu TLS (Transport Layer Security) z urządzeniem bramy.

    Ten krok został szczegółowo opisany w dalszej części tego artykułu. Ten krok można wykonać jeden z dwóch sposobów: instalując certyfikat urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego lub (w przypadku niektórych języków), odwołując się do certyfikatu w aplikacjach przy użyciu zestawów SDK usługi Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Podstawowe informacje dotyczące protokołu TLS i certyfikatu

Wyzwanie bezpiecznego łączenia urządzeń podrzędnych z IoT Edge jest równie podobne jak w przypadku każdej innej bezpiecznej komunikacji klienta/serwera, która jest wykonywana przez Internet. Klient i serwer bezpiecznie komunikują się za pośrednictwem Internetu przy użyciu [protokołu TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest tworzony przy użyciu standardowych konstrukcji [infrastruktury kluczy publicznych (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) nazywanych certyfikatami. Protokół TLS to dość powiązana Specyfikacja i dotyczy szerokiego zakresu tematów związanych z zabezpieczaniem dwóch punktów końcowych. Ta sekcja zawiera podsumowanie pojęć związanych z bezpiecznym połączeniem urządzeń z bramą IoT Edge.

Gdy klient łączy się z serwerem, serwer przedstawia łańcuch certyfikatów nazywany *łańcuchem certyfikatów serwera*. Łańcuch certyfikatów zwykle składa się z certyfikatu głównego urzędu certyfikacji (CA), jednego lub większej liczby certyfikatów pośrednich i na koniec certyfikatu serwera. Klient ustanawia relację zaufania z serwerem przez kryptograficzną weryfikację całego łańcucha certyfikatów serwera. Ta weryfikacja klienta łańcucha certyfikatów serwera jest nazywana *walidacją łańcucha serwerów*. Klient wzywa serwer, aby udowodnić posiadanie klucza prywatnego skojarzonego z certyfikatem serwera w procesie nazywanym *potwierdzeniem posiadania*. Kombinacja weryfikacji łańcucha serwera i dowodu posiadania jest nazywana *uwierzytelnianiem serwera*. Aby sprawdzić poprawność łańcucha certyfikatów serwera, klient musi mieć kopię certyfikatu głównego urzędu certyfikacji, który został użyty do utworzenia (lub wystawienia) certyfikatu serwera. Zwykle w przypadku łączenia się z witrynami sieci Web przeglądarka udostępnia wstępnie skonfigurowane certyfikaty urzędu certyfikacji, aby klient miał bezproblemowe przetwarzanie.

Gdy urządzenie nawiązuje połączenie z usługą Azure IoT Hub, jest to klient, a IoT Hub usługa w chmurze to serwer. Usługa IoT Hub w chmurze jest obsługiwana przez certyfikat głównego urzędu certyfikacji o nazwie **Baltimore CyberTrust root**, który jest publicznie dostępny i szeroko używany. Ponieważ certyfikat urzędu certyfikacji IoT Hub jest już zainstalowany na większości urządzeń, wiele implementacji protokołu TLS (OpenSSL, Schannel, LibreSSL) jest automatycznie używane podczas weryfikacji certyfikatu serwera. Jednak urządzenie, które pomyślnie nawiązuje połączenie z IoT Hub, może mieć problemy z próbą nawiązania połączenia z bramą IoT Edge.

Gdy urządzenie łączy się z bramą IoT Edge, urządzenie podrzędne jest klientem, a urządzenie bramy jest serwerem. Azure IoT Edge umożliwia tworzenie łańcuchów certyfikatów bramy, ale są one dopasowane. Możesz wybrać użycie publicznego certyfikatu urzędu certyfikacji, takiego jak Baltimore, lub użyć certyfikatu głównego urzędu certyfikacji z podpisem własnym (lub w firmie). Certyfikaty publicznych urzędów certyfikacji często mają koszt związany z nimi, dlatego są zwykle używane w scenariuszach produkcyjnych. Certyfikaty urzędu certyfikacji z podpisem własnym są preferowane na potrzeby tworzenia i testowania. W przypadku korzystania z certyfikatów demonstracyjnych są to certyfikaty głównego urzędu certyfikacji z podpisem własnym.

W przypadku korzystania z certyfikatu głównego urzędu certyfikacji z podpisem własnym dla bramy IoT Edge należy zainstalować go na wszystkich urządzeniach podrzędnych próbujących nawiązać połączenie z bramą.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

Aby dowiedzieć się więcej na temat IoT Edge certyfikatów i niektórych konsekwencji związanych z produkcją, zobacz [IoT Edge szczegóły użycia certyfikatu](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Podaj certyfikat głównego urzędu certyfikacji

Aby sprawdzić certyfikaty urządzenia bramy, urządzenie podrzędne musi mieć własną kopię certyfikatu głównego urzędu certyfikacji. Jeśli do tworzenia certyfikatów testowych użyto skryptów udostępnionych w repozytorium IoT Edge git, certyfikat głównego urzędu certyfikacji nosi nazwę **Azure-IoT-test-Only. root. ca. CERT. pem**. Jeśli nie zostało to jeszcze zrobione w ramach innych kroków przygotowywania urządzenia podrzędnego, Przenieś ten plik certyfikatu do dowolnego katalogu na urządzeniu podrzędnym. Do przenoszenia pliku certyfikatu można użyć usługi, takiej jak [Azure Key Vault](../key-vault/index.yml) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

## <a name="install-certificates-in-the-os"></a>Instalowanie certyfikatów w systemie operacyjnym

Po utworzeniu certyfikatu głównego urzędu certyfikacji na urządzeniu podrzędnym należy upewnić się, że aplikacje łączące się z bramą mogą uzyskać dostęp do certyfikatu.

Zainstalowanie certyfikatu głównego urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego zwykle pozwala większości aplikacji używać certyfikatu głównego urzędu certyfikacji. Istnieją pewne wyjątki, takie jak aplikacje NodeJS, które nie korzystają z magazynu certyfikatów systemu operacyjnego, ale raczej używają wewnętrznego magazynu certyfikatów środowiska uruchomieniowego. Jeśli nie możesz zainstalować certyfikatu na poziomie systemu operacyjnego, przejdź z wyprzedzeniem, aby [użyć certyfikatów z zestawem SDK usługi Azure IoT](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Poniższe polecenia stanowią przykład sposobu instalowania certyfikatu urzędu certyfikacji na hoście Ubuntu. W tym przykładzie przyjęto założenie, że używasz certyfikatu **Azure-IoT-test-Only. root. ca. CERT. pem** z artykułów z wymaganiami wstępnymi i że certyfikat został skopiowany do lokalizacji na urządzeniu podrzędnym.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Powinien pojawić się komunikat "Aktualizowanie certyfikatów w programie/etc/SSL/certs... dodano 1, usunięto 0; Gotowe ".

### <a name="windows"></a>Windows

Poniższe kroki przedstawiają przykład sposobu instalowania certyfikatu urzędu certyfikacji na hoście z systemem Windows. W tym przykładzie przyjęto założenie, że używasz certyfikatu **Azure-IoT-test-Only. root. ca. CERT. pem** z artykułów z wymaganiami wstępnymi i że certyfikat został skopiowany do lokalizacji na urządzeniu podrzędnym.

Certyfikaty można instalować przy użyciu programu PowerShell [Import-Certificate](/powershell/module/pkiclient/import-certificate) jako administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Certyfikaty można także zainstalować za pomocą narzędzia **certlm** :

1. W menu Start Wyszukaj i wybierz pozycję **Zarządzaj certyfikatami komputerów**. Zostanie otwarte narzędzie o nazwie **certlm** .
2. Przejdź do folderu **Certificates —**  >  **Zaufane główne** urzędy certyfikacji komputera lokalnego.
3. Kliknij prawym przyciskiem myszy pozycję **Certyfikaty** i wybierz pozycję **wszystkie zadania**  >  **Importuj**. Powinien zostać uruchomiony Kreator importu certyfikatów.
4. Postępuj zgodnie z instrukcjami, które są kierowane i Importuj plik certyfikatu `<path>/azure-iot-test-only.root.ca.cert.pem` . Po zakończeniu powinien zostać wyświetlony komunikat "pomyślnie zaimportowano".

Możesz również programowo zainstalować certyfikaty przy użyciu interfejsów API platformy .NET, jak pokazano w przykładzie platformy .NET w dalszej części tego artykułu.

Zwykle aplikacje używają stosu TLS dostarczonego przez system Windows o nazwie [Schannel](/windows/desktop/com/schannel) do bezpiecznego łączenia się za pośrednictwem protokołu TLS. Dostawca Schannel *wymaga* , aby wszystkie certyfikaty zostały zainstalowane w magazynie certyfikatów systemu Windows przed podjęciem próby ustanowienia połączenia TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Używanie certyfikatów z zestawami SDK usługi Azure IoT

W tej sekcji opisano, jak zestawy SDK usługi Azure IoT łączą się z urządzeniem IoT Edge przy użyciu prostych przykładowych aplikacji. Celem wszystkich przykładów jest podłączenie klienta urządzenia i wysłanie komunikatów telemetrycznych do bramy, a następnie zamknięcie połączenia i zakończenie.

Przygotuj dwa rzeczy przed użyciem przykładów na poziomie aplikacji:

* Parametry połączenia IoT Hub urządzenia podrzędnego zostały zmodyfikowane w taki sposób, aby wskazywały na urządzenie bramy i wszystkie certyfikaty wymagane do uwierzytelnienia urządzenia podrzędnego w IoT Hub. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Pełna ścieżka do certyfikatu głównego urzędu certyfikacji skopiowanego i zapisanego w miejscu na urządzeniu podrzędnym.

    Na przykład `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Ta sekcja zawiera przykładową aplikację łączącą klienta urządzenia usługi Azure IoT NodeJS z bramą IoT Edge. W przypadku aplikacji NodeJS należy zainstalować certyfikat głównego urzędu certyfikacji na poziomie aplikacji, jak pokazano poniżej. Aplikacje NodeJS nie używają magazynu certyfikatów systemu.

1. Pobierz przykład dla **edge_downstream_device.js** z [zestawu SDK urządzeń Azure IoT dla Node.js repozytorium przykładów](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Upewnij się, że masz wszystkie wymagania wstępne, aby uruchomić próbkę, przeglądając plik **README.MD** .
3. W pliku edge_downstream_device.js zaktualizuj zmienne **ConnectionString** i **edge_ca_cert_path** .
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące uruchamiania przykładowego na urządzeniu.

Aby zrozumieć przykład, w którym pracujesz, Poniższy fragment kodu przedstawia sposób, w jaki zestaw SDK klienta odczytuje plik certyfikatu i używa go do ustanowienia bezpiecznego połączenia TLS:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

W tej sekcji przedstawiono przykładową aplikację łączącą klienta urządzenia z usługą Azure IoT .NET z bramą IoT Edge. Aplikacje .NET mogą jednak automatycznie używać wszelkich zainstalowanych certyfikatów w magazynie certyfikatów systemu zarówno na hostach z systemem Linux, jak i Windows.

1. Pobierz przykład dla **EdgeDownstreamDevice** z [folderu IoT Edge .NET Samples](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Upewnij się, że masz wszystkie wymagania wstępne, aby uruchomić próbkę, przeglądając plik **README.MD** .
3. W oknie **Właściwości/launchSettings.jsw** pliku zaktualizuj zmienne **DEVICE_CONNECTION_STRING** i **CA_CERTIFICATE_PATH** . Jeśli chcesz użyć certyfikatu zainstalowanego w zaufanym magazynie certyfikatów w systemie hosta, pozostaw tę zmienną pustą.
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące uruchamiania przykładowego na urządzeniu.

Aby programowo zainstalować zaufany certyfikat w magazynie certyfikatów za pośrednictwem aplikacji .NET, zapoznaj się z funkcją **InstallCACert ()** w pliku **EdgeDownstreamDevice/program.cs** . Ta operacja to idempotentne, więc można uruchamiać wiele razy z tymi samymi wartościami bez dodatkowych efektów.

### <a name="c"></a>C

W tej sekcji przedstawiono przykładową aplikację do łączenia klienta urządzenia usługi Azure IoT C z bramą IoT Edge. Zestaw C SDK może działać z wieloma bibliotekami TLS, w tym OpenSSL, WolfSSL i Schannel. Aby uzyskać więcej informacji, zobacz [zestaw SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c).

1. Pobierz aplikację **iotedge_downstream_device_sample** z [zestawu SDK urządzeń Azure IoT dla przykładów języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Upewnij się, że masz wszystkie wymagania wstępne, aby uruchomić próbkę, przeglądając plik **README.MD** .
3. W pliku iotedge_downstream_device_sample. c zaktualizuj zmienne **ConnectionString** i **edge_ca_cert_path** .
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące uruchamiania przykładowego na urządzeniu.


Zestaw SDK urządzeń Azure IoT dla języka C udostępnia opcję rejestrowania certyfikatu urzędu certyfikacji podczas konfigurowania klienta. Ta operacja nie instaluje certyfikatu w dowolnym miejscu, ale zamiast tego używa formatu ciągu certyfikatu w pamięci. Zapisany certyfikat jest dostarczany do bazowego stosu TLS podczas ustanawiania połączenia.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> Metoda rejestrowania certyfikatu urzędu certyfikacji podczas konfigurowania klienta można zmienić, jeśli używany jest pakiet lub biblioteka [zarządzana](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) . Na przykład [biblioteka oparta na środowisku IDE Arduino](https://github.com/azure/azure-iot-arduino) będzie wymagała dodania certyfikatu urzędu certyfikacji do tablicy certyfikatów zdefiniowanej w pliku Global Certificates [. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) zamiast przy użyciu `IoTHubDeviceClient_LL_SetOption` operacji.  

W przypadku hostów z systemem Windows, jeśli nie używasz usługi OpenSSL lub innej biblioteki TLS, zestaw SDK domyślnie używa kanału Schannel. Aby można było korzystać IoT Edge z kanału Schannel, należy zainstalować certyfikat głównego urzędu certyfikacji w magazynie certyfikatów systemu Windows, który nie jest ustawiany przy użyciu `IoTHubDeviceClient_SetOption` operacji.

### <a name="java"></a>Java

W tej sekcji przedstawiono przykładową aplikację łączącą klienta urządzenia z usługą Azure IoT Java z bramą IoT Edge.

1. Zapoznaj się z przykładami dotyczącymi **wysyłania zdarzeń** z [zestawu SDK urządzeń Azure IoT dla przykładów języka Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Upewnij się, że masz wszystkie wymagania wstępne, aby uruchomić próbkę, przeglądając plik **README.MD** .
3. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące uruchamiania przykładowego na urządzeniu.

### <a name="python"></a>Python

W tej sekcji przedstawiono przykładową aplikację łączącą klienta urządzenia usługi Azure IoT Python z bramą IoT Edge.

1. Pobierz przykład dla **send_message_downstream** z [zestawu SDK urządzeń Azure IoT dla przykładów języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Ustaw `IOTHUB_DEVICE_CONNECTION_STRING` `IOTEDGE_ROOT_CA_CERT_PATH` zmienne środowiskowe i określone w komentarzach skryptu języka Python.
3. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać dodatkowe instrukcje dotyczące uruchamiania przykładowego na urządzeniu.

## <a name="test-the-gateway-connection"></a>Testowanie połączenia bramy

Użyj tego przykładowego polecenia na urządzeniu podrzędnym, aby sprawdzić, czy może nawiązać połączenie z urządzeniem bramy:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

To polecenie testuje połączenia za pośrednictwem MQTTS (port 8883). Jeśli używasz innego protokołu, Dostosuj polecenie w razie potrzeby do AMQPS (5671) lub HTTPS (433)

Dane wyjściowe tego polecenia mogą być długie, w tym informacje o wszystkich certyfikatach w łańcuchu. Jeśli połączenie zakończy się pomyślnie, zobaczysz wiersz podobny do `Verification: OK` lub `Verify return code: 0 (ok)` .

![Weryfikowanie połączenia bramy](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Rozwiązywanie problemów z bramą

Jeśli urządzenie liścia ma sporadyczne połączenie z urządzeniem bramy, spróbuj wykonać poniższe czynności, aby rozwiązać ten problem.

1. Czy nazwa hosta bramy w parametrach połączenia jest taka sama jak wartość nazwy hosta w pliku IoT Edge config. YAML na urządzeniu bramy?
2. Czy nazwa hosta bramy jest rozpoznawana jako adres IP? Sporadyczne połączenia można rozwiązać przy użyciu systemu DNS lub przez dodanie wpisu do pliku hosta na urządzeniu liścia.
3. Czy w zaporze są otwarte porty komunikacyjne? Komunikacja oparta na używanym protokole (MQTTS: 8883/AMQPS: 5671/HTTPS: Port 433) musi być możliwa między urządzeniem podrzędnym i przezroczystą IoT Edge.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak IoT Edge można zwiększyć [możliwości trybu offline](offline-capabilities.md) na urządzeniach podrzędnych.