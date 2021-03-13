---
title: Łączność urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z łącznością urządzeń w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: dc0655aba424d29a4055f0d50a20057f22d084ed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015459"
---
# <a name="get-connected-to-azure-iot-central"></a>Nawiązywanie połączenia z usługą Azure IoT Central

*Ten artykuł ma zastosowanie do operatorów i deweloperów urządzeń.*

W tym artykule opisano sposób łączenia urządzeń z aplikacją IoT Central platformy Azure. Aby urządzenie mogły wymieniać dane z IoT Central, należy:

- *Uwierzytelnianie*. Uwierzytelnianie za pomocą aplikacji IoT Central używa _tokenu sygnatury dostępu współdzielonego (SAS)_ lub _certyfikatu X. 509_. Certyfikaty X. 509 są zalecane w środowiskach produkcyjnych.
- *Zarejestruj* się. Urządzenia muszą być zarejestrowane w aplikacji IoT Central. Zarejestrowane urządzenia można wyświetlić na stronie **urządzenia** w aplikacji.
- *Skojarz z szablonem urządzenia*. W aplikacji IoT Central szablony urządzeń definiują interfejs użytkownika używany przez operatorów do wyświetlania podłączonych urządzeń i zarządzania nimi.

IoT Central obsługuje następujące dwa scenariusze rejestracji urządzeń:

- *Automatyczna rejestracja*. Urządzenie jest automatycznie rejestrowane podczas pierwszego połączenia. Ten scenariusz umożliwia producentom OEM zbiorcze wyprodukowanie urządzeń, które mogą nawiązywać połączenia bez wcześniejszego zarejestrowania. Producent OEM generuje odpowiednie poświadczenia urządzenia i konfiguruje urządzenia w fabryce. Opcjonalnie można wymagać operatora, aby zatwierdzić urządzenie przed rozpoczęciem wysyłania danych. Ten scenariusz wymaga skonfigurowania _rejestracji grupy_ X. 509 lub SAS w aplikacji.
- *Rejestracja ręczna*. Operatory rejestrują poszczególne urządzenia na stronie **urządzenia** lub [zaimportują plik CSV](howto-manage-devices.md#import-devices) do rejestracji zbiorczej urządzeń. W tym scenariuszu można użyć rejestrowania X. 509 lub _rejestracji grup_ SAS lub _rejestracji pojedynczej_ x. 509 lub SAS.

Urządzenia łączące się z IoT Central powinny przestrzegać *konwencji Plug and Play IoT*. Jedną z tych konwencji jest to, że urządzenie ma wysyłać _Identyfikator modelu_ urządzenia, który implementuje podczas nawiązywania połączenia. Identyfikator modelu umożliwia aplikacji IoT Central kojarzenie urządzenia z prawidłowym szablonem urządzenia.

IoT Central używa [usługi Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania procesem połączenia. Urządzenie najpierw łączy się z punktem końcowym DPS, aby pobrać informacje potrzebne do nawiązania połączenia z aplikacją. Wewnętrznie aplikacja IoT Central używa Centrum IoT do obsługi łączności urządzeń. Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń w odpowiedniej skali.
- Można generować poświadczenia urządzenia i konfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pomocą interfejsu użytkownika IoT Central.
- Aby zarejestrować urządzenia w IoT Central, należy użyć własnych identyfikatorów urządzeń. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden spójny sposób łączenia urządzeń z IoT Central.

W tym artykule opisano następujące kroki połączenia urządzenia:

- [Rejestracja grupy X. 509](#x509-group-enrollment)
- [Rejestracja grupy SAS](#sas-group-enrollment)
- [Rejestracja indywidualna](#individual-enrollment)
- [Rejestracja urządzenia](#device-registration)
- [Kojarzenie urządzenia z szablonem urządzenia](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Rejestracja grupy X. 509

W środowisku produkcyjnym używanie certyfikatów X. 509 jest zalecanym mechanizmem uwierzytelniania urządzeń dla IoT Central. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](../../iot-hub/iot-hub-x509ca-overview.md).

Aby połączyć urządzenie z certyfikatem X. 509 z aplikacją:

1. Utwórz *grupę rejestracji* , która używa typu zaświadczania **certyfikatów (X. 509)** .
1. Dodawanie i weryfikowanie pośredniego lub głównego certyfikatu X. 509 w grupie rejestracji.
1. Wygeneruj certyfikat liścia z certyfikatu głównego lub pośredniego w grupie rejestracji. Wyślij certyfikat liścia z urządzenia, gdy nawiąże połączenie z aplikacją.

Aby dowiedzieć się więcej, zobacz [jak podłączyć urządzenia za pomocą certyfikatów X. 509](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Tylko do celów testowych

Do celów testowych można użyć następujących narzędzi do generowania certyfikatów głównych, pośrednich i urządzeń:

- [Narzędzia dla zestawu SDK urządzeń Azure IoT Device Provisioning](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): kolekcja narzędzi Node.js, których można użyć do generowania i weryfikowania certyfikatów X. 509.
- W przypadku korzystania z urządzenia DevKit to [Narzędzie wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) generuje certyfikat urzędu certyfikacji, który można dodać do aplikacji IoT Central w celu zweryfikowania certyfikatów.
- [Zarządzanie certyfikatami testów urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): Kolekcja skryptów programu PowerShell i bash do:
  - Utwórz łańcuch certyfikatów.
  - Zapisz certyfikaty jako pliki. cer, aby przekazać je do aplikacji IoT Central.
  - Użyj kodu weryfikacyjnego z aplikacji IoT Central w celu wygenerowania certyfikatu weryfikacji.
  - Utwórz certyfikaty liści dla urządzeń, korzystając z identyfikatorów urządzeń jako parametru do narzędzia.

## <a name="sas-group-enrollment"></a>Rejestracja grupy SAS

Aby połączyć urządzenie z kluczem sygnatury dostępu współdzielonego urządzenia z aplikacją:

1. Utwórz *grupę rejestracji* , która używa typu zaświadczania **sygnatury dostępu współdzielonego (SAS)** .
1. Skopiuj klucz podstawowy lub pomocniczy grupy z grupy rejestracji.
1. Użyj interfejsu wiersza polecenia platformy Azure do wygenerowania klucza urządzenia z poziomu klucza grupy:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Użyj wygenerowanego klucza urządzenia, gdy urządzenie łączy się z aplikacją IoT Central.

## <a name="individual-enrollment"></a>Rejestracja indywidualna

Klienci łączący urządzenia z własnymi poświadczeniami uwierzytelniania używają poszczególnych rejestracji. Rejestracja indywidualna to wpis dla pojedynczego urządzenia, które może nawiązać połączenie. Rejestracje indywidualne mogą używać certyfikatów liścia X. 509 lub tokenów SAS (z fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki. Aby uzyskać więcej informacji, zobacz temat Usługa [DPS — Rejestracja indywidualna](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Podczas tworzenia rejestracji indywidualnej dla urządzenia mają pierwszeństwo przed domyślnymi opcjami rejestracji grupy w aplikacji IoT Central.

### <a name="create-individual-enrollments"></a>Tworzenie rejestracji pojedynczych

IoT Central obsługuje następujące mechanizmy zaświadczania dotyczące poszczególnych rejestracji:

- **Zaświadczenie klucza symetrycznego:** Zaświadczenie klucza symetrycznego to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia DPS. Aby utworzyć rejestrację indywidualną, która używa kluczy symetrycznych, Otwórz stronę **połączenie urządzenia** dla urządzenia, wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia oraz **sygnatura dostępu współdzielonego (SAS)** jako mechanizm. Wprowadź klucze podstawowe i pomocnicze kodowane algorytmem Base64 i Zapisz zmiany. Aby połączyć urządzenie, użyj **zakresu identyfikatora**, **identyfikatora urządzenia** i klucza podstawowego lub pomocniczego.

    > [!TIP]
    > W celu przetestowania można użyć **OpenSSL** , aby generować zakodowane klucze Base64: `openssl rand -base64 64`

- **Certyfikaty X. 509:** Aby utworzyć rejestrację indywidualną za pomocą certyfikatów X. 509, Otwórz stronę **połączenie urządzenia** , wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia oraz **Certyfikaty (X. 509)** jako mechanizm. Certyfikaty urządzeń używane z indywidualnym wpisem rejestracji mają wymaganie, aby wystawca i CN podmiotu zostały ustawione na identyfikator urządzenia.

    > [!TIP]
    > Do testowania można użyć [narzędzi dla zestawu SDK urządzenia usługi Azure IoT Device Provisioning dla Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) w celu wygenerowania certyfikatu z podpisem własnym: `node create_test_cert.js device "mytestdevice"`

- **Zaświadczanie Trusted Platform Module (TPM):** [Moduł TPM](../../iot-dps/concepts-tpm-attestation.md) jest typem sprzętowego modułu zabezpieczeń. Korzystanie z modułu TPM jest jednym z najbardziej bezpiecznych sposobów łączenia urządzeń. W tym artykule przyjęto założenie, że używasz dyskretnego, oprogramowania układowego lub zintegrowanego modułu TPM. Emulowane moduły TPM oprogramowania są dobrze dopasowane do prototypowania lub testowania, ale nie zapewniają tego samego poziomu zabezpieczeń, takiego jak dyskretny, oprogramowanie układowe lub zintegrowane moduły TPM. Nie używaj moduły TPM oprogramowania w środowisku produkcyjnym. Aby utworzyć rejestrację indywidualną korzystającą z modułu TPM, Otwórz stronę **połączenie urządzenia** , wybierz pozycję **Rejestracja indywidualna** jako metoda połączenia i **moduł TPM** jako mechanizm. Wprowadź klucz poręczenia modułu TPM i Zapisz informacje o połączeniu z urządzeniem.

## <a name="device-registration"></a>Rejestracja urządzenia

Aby urządzenie mógł połączyć się z aplikacją IoT Central, musi być zarejestrowane w aplikacji:

- Urządzenia mogą być automatycznie rejestrowane po pierwszym nawiązaniu połączenia. Aby użyć tej opcji, należy użyć [rejestracji grupowej X. 509](#x509-group-enrollment) lub [rejestracji grup SAS](#sas-group-enrollment).
- Operator może zaimportować plik CSV, aby przeprowadzić zbiorczą rejestrację listy urządzeń w aplikacji.
- Operator może ręcznie zarejestrować pojedyncze urządzenie na stronie **urządzenia** w aplikacji.

IoT Central umożliwia producentom OEM zbiorcze wyprodukowanie urządzeń, które mogą zarejestrować się automatycznie. Producent OEM generuje odpowiednie poświadczenia urządzenia i konfiguruje urządzenia w fabryce. Gdy klient włącza urządzenie po raz pierwszy, nawiązuje połączenie z usługą DPS, która następnie automatycznie łączy urządzenie z poprawną aplikacją IoT Central. Opcjonalnie można wymagać, aby operator zatwierdzał urządzenie przed rozpoczęciem wysyłania danych do aplikacji.

> [!TIP]
> Na stronie **połączenie z urządzeniem > administracyjnym** opcja **autozatwierdzania** kontroluje, czy operator musi ręcznie zatwierdzić urządzenie, aby można było rozpocząć wysyłanie danych.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Automatyczne rejestrowanie urządzeń korzystających z certyfikatów X. 509

1. Wygeneruj certyfikaty liści dla urządzeń przy użyciu certyfikatu głównego lub pośredniego dodanego do [grupy rejestracji X. 509](#x509-group-enrollment). Użyj identyfikatorów urządzeń jako `CNAME` w certyfikatach liścia. Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki.

1. Jako producent OEM Flash każde urządzenie z IDENTYFIKATORem urządzenia, wygenerowanym certyfikatem liścia X. 509 i wartością **zakresu identyfikatora** aplikacji. Kod urządzenia powinien również wysyłać identyfikator modelu urządzenia, który implementuje.

1. Po przełączeniu na urządzenie najpierw nawiązuje połączenie z usługą DPS w celu pobrania IoT Central informacji o połączeniu.

1. Urządzenie używa informacji z usługi DPS do nawiązywania połączenia z aplikacją IoT Central i rejestrowania się w niej.

Aplikacja IoT Central używa identyfikatora modelu wysyłanego przez urządzenie w celu [skojarzenia zarejestrowanego urządzenia z szablonem urządzenia](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Automatyczne rejestrowanie urządzeń korzystających z tokenów SAS

1. Skopiuj klucz podstawowy grupy z grupy rejestracji **SAS-IoT-Devices** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Grupuj klucz podstawowy z grupy rejestracji SAS-IoT-Devices":::

1. Użyj `az iot central device compute-device-key` polecenia, aby wygenerować klucze SAS urządzenia. Użyj klucza podstawowego grupy z poprzedniego kroku. Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Jako producent OEM Flash każde urządzenie z IDENTYFIKATORem urządzenia, wygenerowanym kluczem SAS urządzenia i wartością **zakresu identyfikatora** aplikacji. Kod urządzenia powinien również wysyłać identyfikator modelu urządzenia, który implementuje.

1. Po przełączeniu na urządzenie najpierw nawiązuje połączenie z usługą DPS w celu pobrania informacji o rejestracji IoT Central.

1. Urządzenie używa informacji z usługi DPS do nawiązywania połączenia z aplikacją IoT Central i rejestrowania się w niej.

Aplikacja IoT Central używa identyfikatora modelu wysyłanego przez urządzenie w celu [skojarzenia zarejestrowanego urządzenia z szablonem urządzenia](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Rejestruj zbiorczo urządzenia z wyprzedzeniem

Aby zarejestrować dużą liczbę urządzeń w aplikacji IoT Central, należy [zaimportować identyfikatory urządzeń i nazwy](howto-manage-devices.md#import-devices)urządzeń przy użyciu pliku CSV.

Jeśli Twoje urządzenia używają tokenów SAS do uwierzytelniania, [wyeksportuj plik CSV z aplikacji IoT Central](howto-manage-devices.md#export-devices). Wyeksportowany plik CSV zawiera identyfikatory urządzeń i klucze SAS.

Jeśli urządzenia używają certyfikatów X. 509 do uwierzytelniania, Generuj certyfikaty liścia X. 509 dla urządzeń przy użyciu certyfikatu głównego lub pośredniego w przekazaniu do grupy rejestracji X. 509. Użyj identyfikatorów urządzeń zaimportowanych jako `CNAME` wartość w certyfikatach liścia.

Urządzenia muszą używać wartości **identyfikatora zakresu** dla aplikacji i wysyłać identyfikator modelu, gdy łączą się.

> [!TIP]
> Wartość **identyfikatora zakresu** można znaleźć w obszarze **Administracja > połączenie z urządzeniem**.

### <a name="register-a-single-device-in-advance"></a>Rejestrowanie pojedynczego urządzenia z wyprzedzeniem

Takie podejście jest przydatne w przypadku eksperymentowania z IoT Central lub testowaniem urządzeń. Wybierz pozycję **+ Nowy** na stronie **urządzenia** , aby zarejestrować pojedyncze urządzenie. Możesz użyć kluczy sygnatury dostępu współdzielonego połączenia urządzenia, aby podłączyć urządzenie do aplikacji IoT Central. Skopiuj _klucz sygnatury dostępu współdzielonego urządzenia_ z informacji o połączeniu dla zarejestrowanego urządzenia:

![Klucze SAS dla poszczególnych urządzeń](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Kojarzenie urządzenia z szablonem urządzenia

IoT Central automatycznie kojarzy urządzenie z szablonem urządzenia po nawiązaniu połączenia z urządzeniem. Urządzenie wysyła [Identyfikator modelu](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) podczas nawiązywania połączenia. IoT Central używa identyfikatora modelu do identyfikowania szablonu urządzenia dla tego konkretnego modelu urządzenia. Proces odnajdywania działa w następujący sposób:

1. Jeśli szablon urządzenia został już opublikowany w aplikacji IoT Central, urządzenie zostanie skojarzone z szablonem urządzenia.
1. Jeśli szablon urządzenia nie został jeszcze opublikowany w aplikacji IoT Central, IoT Central szuka modelu urządzenia w [repozytorium modelu publicznego](https://github.com/Azure/iot-plugandplay-models). Jeśli IoT Central odnajdzie model, używa go do wygenerowania podstawowego szablonu urządzenia.
1. Jeśli IoT Central nie znajdzie modelu w repozytorium modelu publicznego, urządzenie zostanie oznaczone jako **nieskojarzone**. Operator może utworzyć szablon urządzenia dla urządzenia, a następnie przeprowadzić migrację niepowiązanego urządzenia do nowego szablonu urządzenia.

Poniższy zrzut ekranu przedstawia sposób wyświetlania identyfikatora modelu dla szablonu urządzenia w IoT Central. W szablonie urządzenia wybierz składnik, a następnie wybierz pozycję **Wyświetl tożsamość**:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Zrzut ekranu przedstawiający Identyfikator modelu w szablonie urządzenia z termostatem.":::

[Model termostatu](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) można wyświetlić w repozytorium modelu publicznego. Definicja identyfikatora modelu wygląda następująco:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

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
- [Obsługa komunikatów z chmury do urządzenia](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Bliźniaczych reprezentacji urządzenia](../../iot-hub/iot-hub-devguide-device-twins.md)

Poniższa tabela zawiera podsumowanie sposobu mapowania funkcji usługi Azure IoT Central Device na funkcje IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Obsługa komunikatów przesyłanych z urządzeń do chmury |
| Polecenia w trybie offline | Obsługa komunikatów z chmury do urządzenia |
| Właściwość | Właściwości zgłoszone przez urządzenie |
| Właściwość (zapisywalny) | Wymagane i zgłoszone właściwości dotyczące sznurka urządzenia |
| Polecenie | Metody bezpośrednie |

### <a name="protocols"></a>Protokoły

Zestawy SDK urządzeń obsługują następujące protokoły sieciowe do nawiązywania połączenia z usługą IoT Hub:

- MQTT
- AMQP
- HTTPS

Aby uzyskać informacje o tych protokołach różnicowych i wskazówkach dotyczących wybierania tych protokołów, zobacz [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide-protocols.md).

Jeśli urządzenie nie może użyć żadnego z obsługiwanych protokołów, użyj Azure IoT Edge do konwersji protokołu. IoT Edge obsługuje inne scenariusze analizy w celu odciążenia przetwarzania z aplikacji IoT Central platformy Azure.

## <a name="security"></a>Zabezpieczenia

Wszystkie dane wymieniane między urządzeniami a IoT Central platformy Azure są szyfrowane. IoT Hub uwierzytelnia każde żądanie z urządzenia, które nawiązuje połączenie z dowolnymi punktami końcowymi IoT Hub z urządzeniem. Aby uniknąć wymiany poświadczeń za pośrednictwem sieci, urządzenie używa podpisanych tokenów do uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu do IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Zapoznaj się z [najlepszymi rozwiązaniami](concepts-best-practices.md) dotyczącymi tworzenia urządzeń.
- Zapoznaj się z przykładowym kodem, który pokazuje, jak używać tokenów SAS w [samouczku: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central](tutorial-connect-device.md)
- Dowiedz się, jak [łączyć urządzenia z certyfikatami X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Central aplikacji](how-to-connect-devices-x509.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
- Przeczytaj informacje o [Azure IoT Edge urządzeniach i platformie Azure IoT Central](./concepts-iot-edge.md)
