---
title: Łączność z urządzeniem w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kluczowe pojęcia dotyczące łączności z urządzeniami w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f37b070c74abd8511fc597f9b159312d91281083
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759117"
---
# <a name="get-connected-to-azure-iot-central"></a>Nawiązywać połączenia z usługą Azure IoT Central

*Ten artykuł dotyczy operatorów i deweloperów urządzeń.*

W tym artykule opisano opcje łączenia urządzeń z aplikacją Azure IoT Central.

Zazwyczaj należy zarejestrować urządzenie w aplikacji, zanim będzie można połączyć. Jednak IoT Central obsługuje scenariusze, w których [urządzenia mogą łączyć się bez uprzedniego zarejestrowania](#connect-without-registering-devices).

Usługa IoT Central używa [usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania procesem połączenia. Urządzenie najpierw łączy się z punktem końcowym DPS, aby pobrać informacje potrzebne do połączenia z aplikacją. Wewnętrznie aplikacja IoT Central używa centrum IoT hub do obsługi łączności z urządzeniem. Korzystanie z DPS umożliwia:

- IoT Central do obsługi urządzeń dołączających i łączących na dużą skalę.
- Aby wygenerować poświadczenia urządzenia i skonfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pośrednictwem interfejsu użytkownika ioT Central.
- Aby używać własnych identyfikatorów urządzeń do rejestrowania urządzeń w centrum IoT. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden, spójny sposób łączenia urządzeń z IoT Central.

Aby zabezpieczyć komunikację między urządzeniem a aplikacją, usługa IoT Central obsługuje zarówno certyfikaty shared access signatures (SAS), jak i X.509. Certyfikaty X.509 są zalecane w środowiskach produkcyjnych.

W tym artykule opisano następujące przypadki użycia:

- [Podłączanie pojedynczego urządzenia za pomocą usługi SAS](#connect-a-single-device)
- [Łączenie urządzeń na dużą skalę za pomocą sas](#connect-devices-at-scale-using-sas)
- [Podłącz urządzenia na dużą skalę przy użyciu certyfikatów X.509](#connect-devices-using-x509-certificates) — zalecane podejście dla środowisk produkcyjnych.
- [Podłączanie urządzeń bez uprzedniej rejestracji](#connect-without-registering-devices)
- [Łączenie urządzeń korzystających z pojedynczych rejestracji DPS](#individual-enrollment-based-device-connectivity)
- [Łączenie urządzeń przy użyciu funkcji IoT Plug and Play (podgląd)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Podłączanie jednego urządzenia

Takie podejście jest przydatne podczas eksperymentowania z IoT Central lub urządzeń testowych. Za pomocą kluczy SAS połączenia urządzenia z aplikacji IoT Central można podłączyć urządzenie do aplikacji IoT Central. Skopiuj _klucz SYGNATURY DOSTĘPU WSPÓŁDZIELONEGO urządzenia_ z informacji o połączeniu zarejestrowanego urządzenia:

![Klawisze SAS dla pojedynczego urządzenia](./media/concepts-get-connected/single-device-sas.png)

Aby dowiedzieć się więcej, zobacz [Tworzenie i łączenie aplikacji klienckiej Node.js z samouczkiem aplikacji Azure IoT Central.](./tutorial-connect-device.md)

## <a name="connect-devices-at-scale-using-sas"></a>Łączenie urządzeń na dużą skalę za pomocą sas

Aby połączyć urządzenia z IoT Central na dużą skalę przy użyciu kluczy SAS, należy zarejestrować, a następnie skonfigurować urządzenia:

### <a name="register-devices-in-bulk"></a>Rejestrowanie urządzeń zbiorczo

Aby zarejestrować dużą liczbę urządzeń w aplikacji IoT Central, użyj pliku CSV, aby [zaimportować identyfikatory urządzeń i nazwy urządzeń.](howto-manage-devices.md#import-devices)

Aby pobrać informacje o połączeniu dla importowanych urządzeń, [wyeksportuj plik CSV z aplikacji IoT Central](howto-manage-devices.md#export-devices). Wyeksportowany plik CSV zawiera identyfikatory urządzeń i klucze SAS.

### <a name="set-up-your-devices"></a>Konfigurowanie urządzeń

Użyj informacji o połączeniu z pliku eksportu w kodzie urządzenia, aby umożliwić urządzeniom łączenie i wysyłanie danych do aplikacji IoT Central. Potrzebujesz również zakresu **identyfikatora** DPS dla aplikacji. Tę wartość można znaleźć w **administracji > połączenie urządzenia**.

> [!NOTE]
> Aby dowiedzieć się, jak łączyć urządzenia bez uprzedniej rejestracji w Urzędzie IoT, zobacz [Łączenie bez uprzedniej rejestracji urządzeń](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Łączenie urządzeń przy użyciu certyfikatów X.509

W środowisku produkcyjnym używanie certyfikatów X.509 jest zalecanym mechanizmem uwierzytelniania urządzeń dla usługi IoT Central. Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie urządzeń przy użyciu certyfikatów urzędu certyfikacji X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Przed podłączeniem urządzenia do certyfikatu X.509 należy dodać i zweryfikować certyfikat pośredni lub główny certyfikat X.509 w aplikacji. Urządzenia muszą używać certyfikatów typu leaf X.509 wygenerowanych na podstawie certyfikatu głównego lub pośredniego.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Dodawanie i weryfikowanie certyfikatu głównego lub pośredniego

Przejdź do **pozycji Połączenie > administracji urządzenia > zarządzać certyfikatem podstawowym** i dodać certyfikat główny lub pośredni X.509 używany do generowania certyfikatów urządzeń.

![Ustawienia połączenia](media/concepts-get-connected/manage-x509-certificate.png)

Weryfikacja własności certyfikatu gwarantuje, że osoba przekazująca certyfikat ma klucz prywatny certyfikatu. Aby zweryfikować certyfikat:

  1. Wybierz przycisk obok **pozycji Kod weryfikacyjny,** aby wygenerować kod.
  1. Utwórz certyfikat weryfikacji X.509 z kodem weryfikacyjnym wygenerowanym w poprzednim kroku. Zapisz certyfikat jako plik cer.
  1. Prześlij podpisany certyfikat weryfikacji i wybierz pozycję **Sprawdź**. Certyfikat jest oznaczony jako **Zweryfikowany** po pomyślnym zakończeniu weryfikacji.

Jeśli masz naruszenie zabezpieczeń lub certyfikat podstawowy jest ustawiony na wygaśnięcie, użyj certyfikatu pomocniczego, aby skrócić czas przestoju. Podczas aktualizowania certyfikatu podstawowego można kontynuować aprowizyję urządzeń przy użyciu certyfikatu pomocniczego.

### <a name="register-and-connect-devices"></a>Rejestrowanie i podłączanie urządzeń

Aby połączyć urządzenia zbiorcze przy użyciu certyfikatów X.509, należy najpierw zarejestrować urządzenia w aplikacji, za pomocą pliku CSV, aby [zaimportować identyfikatory urządzeń i nazwy urządzeń](howto-manage-devices.md#import-devices). Identyfikatory urządzeń powinny być małe.

Generowanie certyfikatów X.509 dla urządzeń przy użyciu przekazanego certyfikatu głównego lub pośredniego. Użyj **identyfikatora urządzenia** `CNAME` jako wartości w certyfikatach liścia. Kod urządzenia wymaga wartości **zakresu identyfikatora** dla aplikacji, **identyfikatora urządzenia**i odpowiedniego certyfikatu urządzenia.

### <a name="for-testing-purposes-only"></a>Tylko do celów testowych

Tylko do testowania można użyć następujących narzędzi do generowania certyfikatów głównych, pośrednich i urządzeń:

- [Narzędzia dla zestawu SDK urządzenia inicjowania obsługi administracyjnej urządzeń azure IoT:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)kolekcja narzędzi Node.js, których można używać do generowania i weryfikowania certyfikatów i kluczy X.509.
- Jeśli używasz urządzenia DevKit, to [narzędzie wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) generuje certyfikat urzędu certyfikacji, który można dodać do aplikacji IoT Central w celu zweryfikowania certyfikatów.
- [Zarządzanie testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)kolekcja skryptów programu PowerShell i Bash w celu:
  - Tworzenie łańcucha certyfikatów.
  - Zapisz certyfikaty jako pliki cer do przekazania do aplikacji IoT Central.
  - Użyj kodu weryfikacyjnego z aplikacji IoT Central, aby wygenerować certyfikat weryfikacji.
  - Tworzenie certyfikatów liścia dla urządzeń przy użyciu identyfikatorów urządzeń jako parametru narzędzia.

### <a name="further-reference"></a>Dalsze informacje

- [Przykładowa implementacja dla RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Przykładowy klient urządzenia w C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Łączenie bez rejestracji urządzeń

Wcześniej opisane scenariusze wymagają zarejestrowania urządzeń w aplikacji przed nawiązaniem połączenia. IoT Central umożliwia również producentom OEM masową produkcję urządzeń, które można łączyć bez uprzedniego zarejestrowania. Producent OEM generuje odpowiednie poświadczenia urządzenia i konfiguruje urządzenia fabrycznie. Gdy klient włącza urządzenie po raz pierwszy, łączy się z dps, który następnie automatycznie łączy urządzenie do poprawnej aplikacji IoT Central. Operator centrum IoT musi zatwierdzić urządzenie przed rozpoczęciem wysyłania danych do aplikacji.

Przepływ różni się nieco w zależności od tego, czy urządzenia używają tokenów Sygnatury dostępu Współdzielonego, czy certyfikatów X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Łączenie urządzeń korzystających z tokenów Sygnatury dostępu Współdzielonego bez rejestracji

1. Skopiuj klucz podstawowy grupy aplikacji IoT Central:

    ![Podstawowy klucz SAS grupy aplikacji](media/concepts-get-connected/group-sas-keys.png)

1. Użyj narzędzia [dps-keygen](https://www.npmjs.com/package/dps-keygen) do generowania kluczy SAS urządzenia. Użyj klucza podstawowego grupy z poprzedniego kroku. Identyfikatory urządzeń muszą być małe:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. Producent OEM miga każde urządzenie z identyfikatorem urządzenia, wygenerowanym kluczem SAS urządzenia i **wartością zakresu identyfikatora** aplikacji.

1. Po włączeniu urządzenia najpierw łączy się z dps, aby pobrać jego informacje rejestracyjne IoT Central.

    Urządzenie początkowo ma stan urządzenia **bez związku** na stronie **Urządzenia** i nie jest przypisane do szablonu urządzenia. Na stronie **Urządzenia** **przenieś** urządzenie do odpowiedniego szablonu urządzenia. Inicjowanie obsługi administracyjnej urządzenia jest już zakończone, stan urządzenia jest teraz **aprowizowana,** a urządzenie może rozpocząć wysyłanie danych.

    Na stronie **Połączenie > Urządzenia administracja** opcja Automatyczne **zatwierdzanie** określa, czy należy ręcznie zatwierdzić urządzenie, zanim będzie można rozpocząć wysyłanie danych.

    > [!NOTE]
    > Aby dowiedzieć się, jak automatycznie kojarzyć urządzenie z szablonem urządzenia, zobacz [Łączenie urządzeń z ioT Plug and Play (wersja zapoznawcza)](#connect-devices-with-iot-plug-and-play-preview).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Podłączanie urządzeń korzystających z certyfikatów X.509 bez rejestracji

1. [Dodaj i zweryfikuj główny lub pośredni certyfikat X.509](#connect-devices-using-x509-certificates) do aplikacji IoT Central. (#connect-devices-using-x509-certificates)

1. Generuj certyfikaty liściowe dla swoich urządzeń przy użyciu certyfikatu głównego lub pośredniego dodanego do aplikacji IoT Central. Użyj identyfikatorów urządzeń o niższych literach jako `CNAME` certyfikatów typu liść.

1. Producent OEM miga każde urządzenie z identyfikatorem urządzenia, wygenerowanym lewym certyfikatem X.509 i **wartością zakresu identyfikatora** aplikacji.

1. Po włączeniu urządzenia najpierw łączy się z dps, aby pobrać jego informacje rejestracyjne IoT Central.

    Urządzenie początkowo ma stan urządzenia **bez związku** na stronie **Urządzenia** i nie jest przypisane do szablonu urządzenia. Na stronie **Urządzenia** **przenieś** urządzenie do odpowiedniego szablonu urządzenia. Inicjowanie obsługi administracyjnej urządzenia jest już zakończone, stan urządzenia jest teraz **aprowizowana,** a urządzenie może rozpocząć wysyłanie danych.

    Na stronie **Połączenie > Urządzenia administracja** opcja Automatyczne **zatwierdzanie** określa, czy należy ręcznie zatwierdzić urządzenie, zanim będzie można rozpocząć wysyłanie danych.

    > [!NOTE]
    > Aby dowiedzieć się, jak automatycznie kojarzyć urządzenie z szablonem urządzenia, zobacz [Łączenie urządzeń z ioT Plug and Play (wersja zapoznawcza)](#connect-devices-with-iot-plug-and-play-preview).

## <a name="individual-enrollment-based-device-connectivity"></a>Indywidualna łączność z urządzeniami opartymi na rejestracji

W przypadku klientów łączących urządzenia, z których każdy ma własne poświadczenia uwierzytelniania, należy używać rejestracji indywidualnych. Rejestracja indywidualna to wpis dla jednego urządzenia, które może się połączyć. Poszczególne rejestracje mogą używać certyfikatów listowych X.509 lub tokenów Sygnatury dostępu Współdzielonego (z fizycznego lub wirtualnego zaufanego modułu platformy) jako mechanizmów zaświadczania. Identyfikator urządzenia (znany również jako identyfikator rejestracji) w indywidualnej rejestracji jest alfanumeryczny, małe litery i może zawierać łączniki. Aby uzyskać więcej informacji, zobacz [DPS rejestracji indywidualnej](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Podczas tworzenia rejestracji indywidualnej dla urządzenia, ma pierwszeństwo przed domyślnymi opcjami rejestracji grupy w aplikacji IoT Central.

### <a name="creating-individual-enrollments"></a>Tworzenie rejestracji indywidualnych

IoT Central obsługuje następujące mechanizmy zaświadczania dla poszczególnych rejestracji:

- **Zaświadczenie klucza symetrycznego:** Zaświadczanie klucza symetrycznego jest proste podejście do uwierzytelniania urządzenia z wystąpieniem DPS. Aby utworzyć indywidualną rejestrację, która używa kluczy symetrycznych, otwórz stronę **Połączenie urządzenia,** wybierz opcję **Rejestracja indywidualna** jako metodę połączenia i **Podpis dostępu współdzielonego (SAS)** jako mechanizm. Wprowadź zakodowane klucze podstawowe i pomocnicze base64 i zapisz zmiany. Użyj **zakresu identyfikatora**, **identyfikatora urządzenia**i klucza podstawowego lub pomocniczego, aby podłączyć urządzenie.

    > [!TIP]
    > Do testowania można użyć **OpenSSL** do generowania kluczy zakodowanych base64:`openssl rand -base64 64`

- **Certyfikaty X.509:** Aby utworzyć indywidualną rejestrację z certyfikatami X.509, otwórz stronę **Połączenie urządzenia,** wybierz opcję **Rejestracja indywidualna** jako metoda połączenia i **Certyfikaty (X.509)** jako mechanizm. Certyfikaty urządzeń używane z indywidualnym wpisem rejestracji mają wymóg, aby wystawca i podmiot CN były ustawione na identyfikator urządzenia.

    > [!TIP]
    > Do testowania można użyć [narzędzia dla zestawu SDK urządzenia inicjowania obsługi administracyjnej urządzeń azure dla node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) do generowania certyfikatu z podpisem własnym:`node create_test_cert.js device "mytestdevice"`

- **Zaświadczenie modułu TPM (Trusted Platform Module):** Moduł [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) jest rodzajem sprzętowego modułu zabezpieczeń. Korzystanie z modułu TPM jest jednym z najbezpieczniejszych sposobów podłączenia urządzenia. W tym artykule założono, że używasz dyskretnego, firmware lub zintegrowanego modułu TPM. Emulowane programowo moduły TPM doskonale nadają się do prototypowania lub testowania, ale nie zapewniają takiego samego poziomu bezpieczeństwa, jak dyskretne, firmware lub zintegrowane moduły TPM. Nie używaj programów TPM w produkcji. Aby utworzyć rejestrację indywidualną, która używa modułu TPM, otwórz stronę **Połączenie urządzenia,** wybierz opcję **Rejestracja indywidualna** jako metodę połączenia, a moduł **TPM** jako mechanizm. Wprowadź klucz poręczenia modułu TPM i zapisz informacje o połączeniu urządzenia.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Łączenie urządzeń za pomocą ioT Plug and Play (wersja zapoznawcza)

Jedną z kluczowych funkcji IoT Plug and Play (podgląd) z IoT Central jest możliwość automatycznego kojarzenia szablonów urządzeń na połączenie urządzenia. Wraz z poświadczeniami urządzenia urządzenia mogą teraz wysyłać **CapabilityModelId** jako część wywołania rejestracji urządzenia. Ta funkcja umożliwia IoT Central odnajdywać i kojarzyć szablon urządzenia z urządzeniem. Proces odnajdywania działa w następujący sposób:

1. Kojarzy się z szablonem urządzenia, jeśli jest on już opublikowany w aplikacji IoT Central.
1. Pobiera z publicznego repozytorium opublikowanych i certyfikowanych modeli możliwości.

Poniżej znajduje się format dodatkowego ładunku, który urządzenie wysłałoby podczas wywołania rejestracji DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Należy zauważyć, że opcja **Automatyczne zatwierdzanie** w **administracji > połączenie urządzenia** musi być włączona, aby urządzenia automatycznie łączyły się, odnajdywały szablon urządzenia i rozpoczynały wysyłanie danych.

## <a name="device-status-values"></a>Wartości stanu urządzenia

Gdy rzeczywiste urządzenie łączy się z aplikacją IoT Central, jego stan urządzenia zmienia się w następujący sposób:

1. Stan urządzenia jest najpierw **zarejestrowany**. Ten stan oznacza, że urządzenie jest tworzone w UIOT Central i ma identyfikator urządzenia. Urządzenie jest rejestrowane, gdy:
    - Nowe rzeczywiste urządzenie zostanie dodane na stronie **Urządzenia.**
    - Zestaw urządzeń jest dodawany przy użyciu **importu** na stronie **Urządzenia.**

1. Stan urządzenia zmienia się na **Aprowizowana,** gdy urządzenie podłączone do aplikacji IoT Central z prawidłowymi poświadczeniami zakończy krok inicjowania obsługi administracyjnej. W tym kroku urządzenie używa dps do automatycznego pobierania ciągu połączenia z Usługi IoT Hub używane przez aplikację IoT Central. Urządzenie może teraz łączyć się z centrum IoT i rozpoczynać wysyłanie danych.

1. Operator może zablokować urządzenie. Gdy urządzenie jest zablokowane, nie może wysyłać danych do aplikacji IoT Central. Zablokowane urządzenia mają stan **Zablokowane**. Operator musi zresetować urządzenie, zanim będzie można wznowić wysyłanie danych. Gdy operator odblokowuje urządzenie, stan powraca do poprzedniej wartości, **zarejestrowanej** lub **Aprowizowana**.

1. Jeśli stan urządzenia to **Oczekiwanie na zatwierdzenie,** oznacza to, że opcja **Auto zatwierdzania** jest wyłączona. Operator musi jawnie zatwierdzić urządzenie przed rozpoczęciem wysyłania danych. Urządzenia niezarejestrowane ręcznie na stronie **Urządzenia,** ale połączone z prawidłowymi poświadczeniami będą miały stan urządzenia **Oczekiwanie na zatwierdzenie**. Operatorzy mogą zatwierdzać te urządzenia na stronie **Urządzenia** za pomocą przycisku **Zatwierdź.**

1. Jeśli stan urządzenia jest **nieskołączeniu,** oznacza to, że urządzenie łączące się z centrum IoT nie ma skojarzonego szablonu urządzenia. Taka sytuacja zazwyczaj dzieje się w następujących scenariuszach:

    - Zestaw urządzeń jest dodawany przy użyciu **importu** na stronie **Urządzenia** bez określania szablonu urządzenia.
    - Urządzenie zostało zarejestrowane ręcznie na stronie **Urządzenia** bez określania szablonu urządzenia. Urządzenie następnie połączone z prawidłowymi poświadczeniami.  

    Operator może skojarzyć urządzenie z szablonem urządzenia na stronie **Urządzenia** za pomocą przycisku **Migruj.**

## <a name="best-practices"></a>Najlepsze rozwiązania

Nie należy zachowywać ani buforować ciągu połączenia urządzenia, który dps zwraca po pierwszym podłączeniu urządzenia. Aby ponownie podłączyć urządzenie, przejdź przez standardowy przepływ rejestracji urządzenia, aby uzyskać prawidłowy ciąg połączenia urządzenia. Jeśli urządzenie buforuje parametry połączenia, oprogramowanie urządzenia naraży na ryzyko wystąpienia nieaktualnego ciągu połączenia, jeśli usługa IoT Central aktualizuje bazowy koncentrator Azure IoT hub, którego używa.

## <a name="sdk-support"></a>Obsługa zestawu SDK

Zestaw SDK urządzeń platformy Azure oferuje najprostszy sposób implementowania kodu urządzenia. Dostępne są następujące sdk urządzeń:

- [Zestaw Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Zestaw Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Zestaw Azure IoT SDK dla platformy Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Zestaw Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Zestaw Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje SDK i łączność z centrum IoT

Cała komunikacja urządzenia z centrum IoT Hub korzysta z następujących opcji łączności Usługi IoT Hub:

- [Wiadomości między urządzeniami a chmurą](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Bliźnięta urządzeń](../../iot-hub/iot-hub-devguide-device-twins.md)

W poniższej tabeli podsumowano sposób mapowania funkcji urządzenia usługi Azure IoT Central na funkcje usługi IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Wiadomości między urządzeniami a chmurą |
| Właściwość | Właściwości zgłaszane bliźniaczej reprezentacji urządzenia |
| Właściwość (zapisywalna) | Dwaj bliźniacze właściwości urządzenia pożądane i zgłaszane właściwości |
| Polecenie | Metody bezpośrednie |

Aby dowiedzieć się więcej na temat korzystania z zestawu SDK urządzeń, zobacz [Łączenie urządzenia zestawu DevDiv z aplikacją Usługi Azure IoT Central,](howto-connect-devkit.md) na przykład kod.

### <a name="protocols"></a>Protokoły

SDK urządzeń obsługują następujące protokoły sieciowe do łączenia się z centrum IoT hub:

- MQTT
- AMQP
- HTTPS

Aby uzyskać informacje na temat tych protokołów różnic i wskazówek dotyczących wybierania jednego z nich, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

Jeśli urządzenie nie może używać żadnego z obsługiwanych protokołów, możesz użyć usługi Azure IoT Edge do konwersji protokołu. Usługa IoT Edge obsługuje inne scenariusze analizy na podstawie krawędzi, aby odciążyć przetwarzanie do krawędzi z aplikacji Azure IoT Central.

## <a name="security"></a>Zabezpieczenia

Wszystkie dane wymieniane między urządzeniami a centrum Usługi Azure IoT Central są szyfrowane. Centrum IoT Hub uwierzytelnia każde żądanie z urządzenia, które łączy się z dowolnymi punktami końcowymi usługi IoT Hub skierowanego na urządzenie. Aby uniknąć wymiany poświadczeń za pomocą sieci owego, urządzenie używa podpisanych tokenów do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Kontrolowanie dostępu do Usługi IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, niektóre z sugerowanych następnych kroków to:

- Dowiedz się, jak [monitorować łączność urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Dowiedz się, jak [zdefiniować nowy typ urządzenia IoT w aplikacji Azure IoT Central](./howto-set-up-template.md)
- Przeczytaj o [urządzeniach usługi Azure IoT Edge i usłudze Azure IoT Central](./concepts-iot-edge.md)
