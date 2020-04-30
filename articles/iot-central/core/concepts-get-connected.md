---
title: Łączność urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z łącznością urządzeń w usłudze Azure IoT Central
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
ms.openlocfilehash: b66f5a7d85eb91970d5f551b010dd512b216b9c6
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509520"
---
# <a name="get-connected-to-azure-iot-central"></a>Połącz z usługą Azure IoT Central

*Ten artykuł ma zastosowanie do operatorów i deweloperów urządzeń.*

W tym artykule opisano opcje łączenia urządzeń z aplikacją IoT Central platformy Azure.

Zazwyczaj musisz zarejestrować urządzenie w swojej aplikacji, aby można było nawiązać połączenie. Jednak IoT Central obsługuje scenariusze, w których [urządzenia mogą się łączyć bez wcześniejszego zarejestrowania](#connect-without-registering-devices).

IoT Central używa [usługi Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania procesem połączenia. Urządzenie najpierw łączy się z punktem końcowym DPS, aby pobrać informacje potrzebne do nawiązania połączenia z aplikacją. Wewnętrznie aplikacja IoT Central używa Centrum IoT do obsługi łączności urządzeń. Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń w odpowiedniej skali.
- Można generować poświadczenia urządzenia i konfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pomocą interfejsu użytkownika IoT Central.
- Aby zarejestrować urządzenia w IoT Central, należy użyć własnych identyfikatorów urządzeń. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden spójny sposób łączenia urządzeń z IoT Central.

Aby zabezpieczyć komunikację między urządzeniem a aplikacją, IoT Central obsługuje zarówno sygnatury dostępu współdzielonego, jak i certyfikat X. 509. Certyfikaty X. 509 są zalecane w środowiskach produkcyjnych.

W tym artykule opisano następujące przypadki użycia:

- [Łączenie pojedynczego urządzenia przy użyciu sygnatury dostępu współdzielonego](#connect-a-single-device)
- [Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego](#connect-devices-at-scale-using-sas)
- [Łączenie urządzeń na dużą skalę za pomocą certyfikatów X. 509](#connect-devices-using-x509-certificates) — zalecane podejście dla środowisk produkcyjnych.
- [Łączenie urządzeń bez wcześniejszego rejestrowania](#connect-without-registering-devices)
- [Łączenie urządzeń korzystających z poszczególnych rejestracji w usłudze DPS](#individual-enrollment-based-device-connectivity)
- [Automatyczne kojarzenie urządzenia z szablonem urządzenia](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Podłącz pojedyncze urządzenie

Takie podejście jest przydatne w przypadku eksperymentowania z IoT Central lub testowaniem urządzeń. Możesz użyć kluczy SAS połączenia urządzenia z aplikacji IoT Central, aby podłączyć urządzenie do aplikacji IoT Central. Skopiuj _klucz sygnatury dostępu współdzielonego urządzenia_ z informacji o połączeniu dla zarejestrowanego urządzenia:

![Klucze SAS dla poszczególnych urządzeń](./media/concepts-get-connected/single-device-sas.png)

Aby dowiedzieć się więcej, zobacz samouczek [Tworzenie i łączenie aplikacji klienckiej Node. js z samouczkiem aplikacji platformy Azure IoT Central](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego

Aby podłączyć urządzenia do IoT Central na dużą skalę przy użyciu kluczy SAS, należy zarejestrować i skonfigurować urządzenia:

### <a name="register-devices-in-bulk"></a>Rejestruj urządzenia luzem

Aby zarejestrować dużą liczbę urządzeń w aplikacji IoT Central, należy [zaimportować identyfikatory urządzeń i nazwy](howto-manage-devices.md#import-devices)urządzeń przy użyciu pliku CSV.

Aby pobrać informacje o połączeniu dla zaimportowanych urządzeń, [wyeksportuj plik CSV z aplikacji IoT Central](howto-manage-devices.md#export-devices). Wyeksportowany plik CSV zawiera identyfikatory urządzeń i klucze SAS.

### <a name="set-up-your-devices"></a>Konfigurowanie urządzeń

Użyj informacji o połączeniu z pliku eksportu w kodzie urządzenia, aby umożliwić urządzeniom łączenie i wysyłanie danych do usługi IoT do aplikacji IoT Central. Wymagany jest również **zakres** usługi DPS dla Twojej aplikacji. Tę wartość można znaleźć w obszarze **administracja > połączenie z urządzeniem**.

> [!NOTE]
> Aby dowiedzieć się, jak można podłączyć urządzenia bez wcześniejszego rejestrowania ich w IoT Central, zobacz [Connecting bez wcześniejszego rejestrowania urządzeń](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Łączenie urządzeń za pomocą certyfikatów X. 509

W środowisku produkcyjnym używanie certyfikatów X. 509 jest zalecanym mechanizmem uwierzytelniania urządzeń dla IoT Central. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](../../iot-hub/iot-hub-x509ca-overview.md).

Przed nawiązaniem połączenia z urządzeniem za pomocą certyfikatu X. 509 Dodaj i sprawdź pośredni lub główny certyfikat X. 509 w aplikacji. Urządzenia muszą używać certyfikatów liścia X. 509 wygenerowanych na podstawie certyfikatu głównego lub pośredniego.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Dodawanie i weryfikowanie certyfikatu głównego lub pośredniego

Przejdź do opcji **administracja > połączenie z urządzeniem > zarządzanie certyfikatem podstawowym** i Dodawanie głównego lub pośredniego certyfikatu X. 509 używanego do generowania certyfikatów urządzeń.

![Ustawienia połączenia](media/concepts-get-connected/manage-x509-certificate.png)

Weryfikowanie własności certyfikatu gwarantuje, że osoba przekazująca certyfikat ma klucz prywatny certyfikatu. Aby zweryfikować certyfikat:

  1. Wybierz przycisk obok **kodu weryfikacyjnego** , aby wygenerować kod.
  1. Utwórz certyfikat weryfikacji X. 509 z kodem weryfikacyjnym wygenerowanym w poprzednim kroku. Zapisz certyfikat jako plik CER.
  1. Przekaż podpisany certyfikat weryfikacyjny i wybierz pozycję **Weryfikuj**. Certyfikat jest oznaczony jako **zweryfikowany** , gdy weryfikacja zakończy się pomyślnie.

W przypadku naruszenia zabezpieczeń lub wymuszenia wygaśnięcia certyfikatu podstawowego Użyj certyfikatu pomocniczego, aby zmniejszyć przestoje. Podczas aktualizowania certyfikatu podstawowego można nadal inicjować obsługę administracyjną urządzeń przy użyciu certyfikatu pomocniczego.

### <a name="register-and-connect-devices"></a>Rejestrowanie i łączenie urządzeń

Aby połączyć zbiorczo urządzenia za pomocą certyfikatów X. 509, należy najpierw zarejestrować urządzenia w aplikacji przy użyciu pliku CSV w celu [zaimportowania identyfikatorów urządzeń i nazw urządzeń](howto-manage-devices.md#import-devices). Wszystkie identyfikatory urządzeń powinny być małymi literami.

Generuj certyfikaty liścia X. 509 dla urządzeń przy użyciu przekazanego certyfikatu głównego lub pośredniego. Użyj **identyfikatora urządzenia** jako `CNAME` wartości w certyfikatach liścia. Kod urządzenia wymaga wartości **identyfikatora zakresu** dla aplikacji, **identyfikatora urządzenia**i odpowiedniego certyfikatu urządzenia.

### <a name="for-testing-purposes-only"></a>Tylko do celów testowych

Do celów testowych można użyć następujących narzędzi do generowania certyfikatów głównych, pośrednich i urządzeń:

- [Narzędzia dla zestawu SDK urządzenia usługi Azure IoT Device Provisioning](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): kolekcja narzędzi Node. js, których można użyć do generowania i weryfikowania certyfikatów i kluczy X. 509.
- W przypadku korzystania z urządzenia DevKit to [Narzędzie wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) generuje certyfikat urzędu certyfikacji, który można dodać do aplikacji IoT Central w celu zweryfikowania certyfikatów.
- [Zarządzanie certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): Kolekcja skryptów programu PowerShell i bash do:
  - Utwórz łańcuch certyfikatów.
  - Zapisz certyfikaty jako pliki. cer, aby przekazać je do aplikacji IoT Central.
  - Użyj kodu weryfikacyjnego z aplikacji IoT Central w celu wygenerowania certyfikatu weryfikacji.
  - Utwórz certyfikaty liści dla urządzeń, korzystając z identyfikatorów urządzeń jako parametru do narzędzia.

### <a name="further-reference"></a>Dalsze informacje

- [Przykładowa implementacja dla RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Przykładowy klient urządzenia w języku C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Łączenie bez rejestrowania urządzeń

Opisane wcześniej scenariusze wymagają zarejestrowania urządzeń w aplikacji przed ich połączeniem. IoT Central również umożliwia producentom OEM rozgrupowanie urządzeń wytwarzających, które mogą nawiązywać połączenia bez wcześniejszego zarejestrowania. Producent OEM generuje odpowiednie poświadczenia urządzenia i konfiguruje urządzenia w fabryce. Gdy klient włącza urządzenie po raz pierwszy, nawiązuje połączenie z usługą DPS, która następnie automatycznie łączy urządzenie z poprawną aplikacją IoT Central. Operator IoT Central musi zatwierdzić urządzenie przed rozpoczęciem wysyłania danych do aplikacji.

Przepływ jest nieco różny w zależności od tego, czy urządzenia używają tokenów SAS czy certyfikatów X. 509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Łączenie urządzeń korzystających z tokenów SAS bez rejestrowania

1. Skopiuj klucz podstawowy grupy aplikacji IoT Central:

    ![Podstawowy klucz SAS grupy aplikacji](media/concepts-get-connected/group-sas-keys.png)

1. Użyj narzędzia [DPS-Keygen](https://www.npmjs.com/package/dps-keygen) do wygenerowania kluczy SAS urządzeń. Użyj klucza podstawowego grupy z poprzedniego kroku. Identyfikatory urządzeń muszą mieć wielkie litery:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. Producent OEM błyskuje każde urządzenie przy użyciu identyfikatora urządzenia, wygenerowanego klucza sygnatury dostępu współdzielonego urządzenia oraz wartości **zakresu identyfikatora** aplikacji.

1. Po przełączeniu na urządzenie najpierw nawiązuje połączenie z usługą DPS w celu pobrania informacji o rejestracji IoT Central.

    Urządzenie ma początkowo stan urządzenia **nieskojarzone** na stronie **urządzenia** i nie jest przypisane do szablonu urządzenia. Na stronie **urządzenia** **dokonaj migracji** urządzenia do odpowiedniego szablonu urządzenia. Inicjowanie obsługi urządzeń zostało zakończone, stan urządzenia jest teraz **zainicjowany**i urządzenie może rozpocząć wysyłanie danych.

    Na stronie **połączenie z urządzeniem >m administracyjnym** opcja **autozatwierdzania** kontroluje, czy należy ręcznie zatwierdzić urządzenie przed rozpoczęciem wysyłania danych.

    > [!NOTE]
    > Aby dowiedzieć się, jak automatycznie skojarzyć urządzenie z szablonem urządzenia, zobacz sekcję [Automatyczne kojarzenie urządzenia z szablonem urządzenia](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Łączenie urządzeń korzystających z certyfikatów X. 509 bez rejestrowania

1. [Dodaj i Sprawdź główny lub pośredni certyfikat X. 509](#connect-devices-using-x509-certificates) do aplikacji IoT Central. (#connect-Devices-using-x509-Certificates)

1. Wygeneruj certyfikaty liści dla urządzeń przy użyciu certyfikatu głównego lub pośredniego dodanego do aplikacji IoT Central. `CNAME` W przypadku certyfikatów liścia należy używać identyfikatorów urządzeń z małymi literami.

1. Producent OEM przebłyskuje każde urządzenie przy użyciu identyfikatora urządzenia, a następnie wygenerowała pozostały certyfikat X. 509 i wartość **zakresu identyfikatora** aplikacji.

1. Po przełączeniu na urządzenie najpierw nawiązuje połączenie z usługą DPS w celu pobrania informacji o rejestracji IoT Central.

    Urządzenie ma początkowo stan urządzenia **nieskojarzone** na stronie **urządzenia** i nie jest przypisane do szablonu urządzenia. Na stronie **urządzenia** **dokonaj migracji** urządzenia do odpowiedniego szablonu urządzenia. Inicjowanie obsługi urządzeń zostało zakończone, stan urządzenia jest teraz **zainicjowany**i urządzenie może rozpocząć wysyłanie danych.

    Na stronie **połączenie z urządzeniem >m administracyjnym** opcja **autozatwierdzania** kontroluje, czy należy ręcznie zatwierdzić urządzenie przed rozpoczęciem wysyłania danych.

    > [!NOTE]
    > Aby dowiedzieć się, jak automatycznie skojarzyć urządzenie z szablonem urządzenia, zobacz sekcję [Automatyczne kojarzenie urządzenia z szablonem urządzenia](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Indywidualne połączenie z urządzeniem opartym na rejestracji

W przypadku klientów łączących urządzenia z własnymi poświadczeniami uwierzytelniania należy użyć indywidualnych rejestracji. Rejestracja indywidualna to wpis dla pojedynczego urządzenia, które może nawiązać połączenie. Rejestracje indywidualne mogą używać certyfikatów liścia X. 509 lub tokenów SAS (z fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Identyfikator urządzenia (znany również jako identyfikator rejestracji) w rejestracji indywidualnej jest alfanumeryczny, pisany małymi literami i może zawierać łączniki. Aby uzyskać więcej informacji, zobacz temat Usługa [DPS — Rejestracja indywidualna](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Podczas tworzenia rejestracji indywidualnej dla urządzenia mają pierwszeństwo przed domyślnymi opcjami rejestracji grupy w aplikacji IoT Central.

### <a name="create-individual-enrollments"></a>Tworzenie rejestracji pojedynczych

IoT Central obsługuje następujące mechanizmy zaświadczania dotyczące poszczególnych rejestracji:

- **Zaświadczenie klucza symetrycznego:** Zaświadczenie klucza symetrycznego to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia DPS. Aby utworzyć rejestrację indywidualną, która używa kluczy symetrycznych, Otwórz stronę **połączenie urządzenia** , wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia i **sygnatura dostępu współdzielonego (SAS)** jako mechanizm. Wprowadź klucze podstawowe i pomocnicze kodowane algorytmem Base64 i Zapisz zmiany. Aby połączyć urządzenie, użyj **zakresu identyfikatora**, **identyfikatora urządzenia**i klucza podstawowego lub pomocniczego.

    > [!TIP]
    > W celu przetestowania można użyć **OpenSSL** , aby generować zakodowane klucze Base64:`openssl rand -base64 64`

- **Certyfikaty X. 509:** Aby utworzyć rejestrację indywidualną za pomocą certyfikatów X. 509, Otwórz stronę **połączenie urządzenia** , wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia oraz **Certyfikaty (X. 509)** jako mechanizm. Certyfikaty urządzeń używane z indywidualnym wpisem rejestracji mają wymaganie, aby wystawca i CN podmiotu zostały ustawione na identyfikator urządzenia.

    > [!TIP]
    > W celu przetestowania można użyć [narzędzi dla zestawu SDK urządzenia usługi Azure IoT Device Provisioning dla środowiska Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) w celu wygenerowania certyfikatu z podpisem własnym:`node create_test_cert.js device "mytestdevice"`

- **Zaświadczanie moduł TPM (TPM):** [Moduł TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) jest typem sprzętowego modułu zabezpieczeń. Korzystanie z modułu TPM jest jednym z najbardziej bezpiecznych sposobów łączenia urządzeń. W tym artykule przyjęto założenie, że używasz dyskretnego, oprogramowania układowego lub zintegrowanego modułu TPM. Emulowane moduły TPM oprogramowania są dobrze dopasowane do prototypowania lub testowania, ale nie zapewniają tego samego poziomu zabezpieczeń, takiego jak dyskretny, oprogramowanie układowe lub zintegrowane moduły TPM. Nie używaj moduły TPM oprogramowania w środowisku produkcyjnym. Aby utworzyć rejestrację indywidualną korzystającą z modułu TPM, Otwórz stronę **połączenie urządzenia** , wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia i **moduł TPM** jako mechanizm. Wprowadź klucz poręczenia modułu TPM i Zapisz informacje o połączeniu z urządzeniem.

## <a name="automatically-associate-with-a-device-template"></a>Automatycznie Skojarz z szablonem urządzenia

Jedną z najważniejszych funkcji IoT Central jest możliwość automatycznego kojarzenia szablonów urządzeń z urządzeniem. Wraz z poświadczeniami urządzeń urządzenia mogą wysyłać **CapabilityModelId** w ramach wywołania rejestracji urządzenia. **CapabilityModelID** to nazwa urn identyfikująca model możliwości implementowany przez urządzenie. Aplikacja IoT Central może użyć **CapabilityModelID** do zidentyfikowania szablonu urządzenia do użycia, a następnie automatycznie skojarzyć urządzenie z szablonem urządzenia. Proces odnajdywania działa w następujący sposób:

1. Jeśli szablon urządzenia został już opublikowany w aplikacji IoT Central, urządzenie zostanie skojarzone z szablonem urządzenia.
1. W przypadku wstępnie certyfikowanych urządzeń Plug and Play IoT, jeśli szablon urządzenia nie został jeszcze opublikowany w aplikacji IoT Central, szablon urządzenia zostanie pobrany z repozytorium publicznego.

Poniższe fragmenty kodu pokazują format dodatkowego ładunku, który urządzenie musi wysłać podczas wywołania rejestracji usługi DPS, aby można było automatycznie skojarzyć skojarzenie.

Jest to format urządzeń korzystających z ogólnie dostępnego zestawu SDK urządzeń, który nie obsługuje Plug and Play IoT:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Jest to format urządzeń korzystających z zestawu SDK urządzeń w wersji zapoznawczej, który obsługuje Plug and Play IoT:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Opcja **automatycznego zatwierdzania** w przypadku **administrowania > połączenie z urządzeniem** musi być włączone, aby urządzenia mogły automatycznie nawiązywać połączenia, wykrywać szablon urządzenia i rozpoczynać wysyłanie danych.

## <a name="device-status-values"></a>Wartości stanu urządzenia

Gdy rzeczywiste urządzenie nawiązuje połączenie z aplikacją IoT Central, stan urządzenia zmienia się w następujący sposób:

1. Stan urządzenia jest pierwszy **zarejestrowany**. Ten stan oznacza, że urządzenie jest tworzone w IoT Central i ma identyfikator urządzenia. Urządzenie jest rejestrowane, gdy:
    - Na stronie **urządzenia** zostanie dodane nowe rzeczywiste urządzenie.
    - Zestaw urządzeń jest dodawany przy użyciu opcji **Importuj** na stronie **urządzenia** .

1. Stan urządzenia zostanie zmieniony na **zainicjowany** , gdy urządzenie połączone z aplikacją IoT Central z prawidłowymi poświadczeniami ukończy krok aprowizacji. W tym kroku urządzenie używa usługi DPS do automatycznego pobierania parametrów połączenia z IoT Hub używane przez aplikację IoT Central. Urządzenie może teraz połączyć się z IoT Central i rozpocząć wysyłanie danych.

1. Operator może blokować urządzenie. Gdy urządzenie jest zablokowane, nie może wysyłać danych do aplikacji IoT Central. Zablokowane urządzenia mają stan **zablokowany**. Operator musi zresetować urządzenie, aby można było wznowić wysyłanie danych. Gdy operator odblokowuje urządzenie, stan powraca do poprzedniej wartości, **zarejestrowane** lub **obsługiwane**.

1. Jeśli stan urządzenia **oczekuje na zatwierdzenie**, oznacza to, że opcja **autozatwierdzania** jest wyłączona. Operator musi jawnie zatwierdzić urządzenie przed rozpoczęciem wysyłania danych. Urządzenia, które nie zostały zarejestrowane ręcznie na stronie **urządzeń** , ale połączono z prawidłowymi poświadczeniami, będą mieć stan urządzenia **oczekujący na zatwierdzenie**. Operatory mogą zatwierdzać te urządzenia ze strony **urządzenia** za pomocą przycisku **Zatwierdź** .

1. Jeśli stan urządzenia jest **nieskojarzony**, oznacza to, że urządzenie łączące się z IoT Central nie ma skojarzonego szablonu urządzenia. Ta sytuacja zwykle odbywa się w następujących scenariuszach:

    - Zestaw urządzeń jest dodawany przy użyciu opcji **Importuj** na stronie **urządzenia** bez określania szablonu urządzenia.
    - Urządzenie zostało zarejestrowane ręcznie na stronie **urządzenia** bez określania szablonu urządzenia. Następnie urządzenie jest połączone z prawidłowymi poświadczeniami.  

    Operator może skojarzyć urządzenie z szablonem urządzenia ze strony **urządzenia** za pomocą przycisku **Migrowanie** .

## <a name="best-practices"></a>Najlepsze rozwiązania

Nie Utrwalaj ani nie Buforuj parametrów połączenia urządzenia, które usługa DPS zwraca przy pierwszym połączeniu z urządzeniem. Aby ponownie połączyć urządzenie, przejdź do standardowego przepływu rejestracji urządzeń w celu uzyskania prawidłowych parametrów połączenia z urządzeniem. Jeśli urządzenie buforuje parametry połączenia, oprogramowanie urządzenia zostanie uruchomione w celu ryzyka posiadania starych parametrów połączenia w przypadku, gdy IoT Central aktualizuje bazowe Centrum IoT Azure, którego używa.

## <a name="sdk-support"></a>Obsługa zestawu SDK

Zestawy SDK urządzeń platformy Azure oferują najprostszy sposób implementacji kodu urządzenia. Dostępne są następujące zestawy SDK urządzeń:

- [Zestaw Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Zestaw Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Zestaw Azure IoT SDK dla platformy Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Zestaw Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Zestaw Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i IoT Hub łączności

Cała komunikacja urządzeń z IoT Hub używa następujących opcji łączności IoT Hub:

- [Obsługa komunikatów przesyłanych z urządzeń do chmury](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Bliźniaczych reprezentacji urządzenia](../../iot-hub/iot-hub-devguide-device-twins.md)

Poniższa tabela zawiera podsumowanie sposobu mapowania funkcji usługi Azure IoT Central Device na funkcje IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Obsługa komunikatów przesyłanych z urządzeń do chmury |
| Właściwość | Właściwości zgłoszone przez urządzenie |
| Właściwość (zapisywalny) | Wymagane i zgłoszone właściwości dotyczące sznurka urządzenia |
| Polecenie | Metody bezpośrednie |

Aby dowiedzieć się więcej o korzystaniu z zestawów SDK urządzeń, zobacz temat [łączenie urządzenia z systemem DevDiv Kit z aplikacją IoT Central platformy Azure](howto-connect-devkit.md) na przykład Code.

### <a name="protocols"></a>Protokoły

Zestawy SDK urządzeń obsługują następujące protokoły sieciowe do nawiązywania połączenia z usługą IoT Hub:

- MQTT
- AMQP
- HTTPS

Aby uzyskać informacje o tych protokołach różnicowych i wskazówkach dotyczących wybierania tych protokołów, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

Jeśli urządzenie nie może użyć żadnego z obsługiwanych protokołów, można użyć Azure IoT Edge do konwersji protokołu. IoT Edge obsługuje inne scenariusze analizy w celu odciążenia przetwarzania do krawędzi z aplikacji IoT Central platformy Azure.

## <a name="security"></a>Zabezpieczenia

Wszystkie dane wymieniane między urządzeniami a IoT Central platformy Azure są szyfrowane. IoT Hub uwierzytelnia każde żądanie z urządzenia, które nawiązuje połączenie z dowolnymi punktami końcowymi IoT Hub z urządzeniem. Aby uniknąć wymiany poświadczeń za pośrednictwem sieci, urządzenie używa podpisanych tokenów do uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu do IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Dowiedz się, jak [zdefiniować nowy typ urządzenia IoT w aplikacji usługi Azure IoT Central](./howto-set-up-template.md)
- Przeczytaj informacje o [Azure IoT Edge urządzeniach i platformie Azure IoT Central](./concepts-iot-edge.md)
