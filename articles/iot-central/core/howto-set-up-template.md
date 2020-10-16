---
title: Definiowanie nowego typu urządzenia IoT na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono jako konstruktora, jak utworzyć nowy szablon urządzenia usługi Azure IoT w aplikacji IoT Central platformy Azure. Zdefiniuj dane telemetryczne, stan, właściwości i polecenia dla danego typu.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: c8fb60a4b549a7203057dd60298d2ae0540450d6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122657"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definiowanie nowego typu urządzenia IoT w aplikacji usługi Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Szablon urządzenia jest planem, który definiuje cechy i zachowania typu urządzenia, które nawiązuje połączenie z [aplikacją IoT Central platformy Azure](concepts-app-templates.md).

Na przykład Konstruktor może utworzyć szablon urządzenia dla połączonego wentylatora o następujących cechach:

- Wysyła dane telemetryczne dotyczące temperatury
- Wysyła Właściwość Location
- Wysyła zdarzenia błędów silnika wentylatorowego
- Wysyła stan operacyjny wentylatora
- Zapewnia Właściwość szybkość wentylatorów zapisywalnych
- Udostępnia polecenie ponownego uruchomienia urządzenia
- Udostępnia ogólny widok urządzenia za pośrednictwem pulpitu nawigacyjnego

Z tego szablonu urządzenia operator może tworzyć i łączyć urządzenia wentylatorów w rzeczywistości. Wszystkie te wentylatory mają pomiary, właściwości i polecenia używane przez operatorów do monitorowania ich i zarządzania nimi. Operatory korzystają z [pulpitów nawigacyjnych](#add-dashboards) i formularzy urządzeń w celu współdziałania z urządzeniami wentylatorów. Deweloper urządzenia używa szablonu, aby zrozumieć, jak urządzenie współdziała z aplikacją. Aby dowiedzieć się więcej, zobacz dane [telemetryczne, właściwości i poleceń](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Tylko konstruktory i Administratorzy mogą tworzyć, edytować i usuwać szablony urządzeń. Każdy użytkownik może tworzyć urządzenia na stronie **urządzenia** z istniejących szablonów urządzeń.

W aplikacji IoT Central szablon urządzenia używa modelu możliwości urządzenia do opisywania możliwości urządzenia. Jako Konstruktor można utworzyć szablony urządzeń za pomocą kilku opcji:

- Zaprojektuj szablon urządzenia w IoT Central, a następnie [Zaimplementuj jego model możliwości urządzenia w kodzie urządzenia](concepts-telemetry-properties-commands.md).
- Zaimportuj model możliwości urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat). Następnie Dodaj wszystkie właściwości i dostosowania chmury oraz pulpity nawigacyjne, których potrzebujesz IoT Central aplikacji.
- Utwórz model możliwości urządzenia przy użyciu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje aplikacja IoT Central.
- Utwórz model możliwości urządzenia przy użyciu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu, a następnie połącz rzeczywiste urządzenie z aplikacją IoT Central przy użyciu połączenia urządzenia po raz pierwszy. IoT Central odnajduje i importuje model możliwości urządzenia z repozytorium publicznego. Następnie można dodać wszystkie właściwości, dostosowania i pulpity nawigacyjne, których aplikacja IoT Central musi być szablonem urządzenia.

Możesz również dodać szablony urządzeń do aplikacji IoT Central przy użyciu [interfejsu API REST lub interfejs](/learn/modules/manage-iot-central-apps-with-rest-api/) [wiersza polecenia](howto-manage-iot-central-from-cli.md).

Niektóre [Szablony aplikacji](concepts-app-templates.md) zawierają już szablony urządzeń, które są przydatne w scenariuszu obsługiwanym przez szablon aplikacji. Na przykład zobacz temat [Architektura analizy w sklepie](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Tworzenie szablonu urządzenia z poziomu katalogu urządzeń

Jako Konstruktor możesz szybko rozpocząć tworzenie rozwiązania przy użyciu certyfikatu usługi IoT Plug and Play (wersja zapoznawcza). Zapoznaj się z listą w [wykazie urządzeń Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central integrują się z katalogiem urządzeń, aby można było zaimportować model możliwości urządzenia z dowolnego z tych urządzeń z certyfikatem certyfikowanym Plug and Play IoT (wersja zapoznawcza). Aby utworzyć szablon urządzenia na podstawie jednego z tych urządzeń w IoT Central:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz pozycję **+ Nowy**, a następnie wybierz dowolne z certyfikowanych urządzeń Plug and Play IoT (wersja zapoznawcza) z wykazu. IoT Central tworzy szablon urządzenia w oparciu o ten model możliwości urządzenia.
1. Dodaj do szablonu urządzenia wszystkie właściwości, dostosowania lub widoki w chmurze.
1. Wybierz pozycję **Publikuj** , aby udostępnić szablon dla operatorów do wyświetlania i łączenia urządzeń.

## <a name="create-a-device-template-from-scratch"></a>Tworzenie szablonu urządzenia od podstaw

Szablon urządzenia zawiera:

- _Model możliwości urządzenia_ określający dane telemetryczne, właściwości i polecenia implementowane przez urządzenie. Te możliwości są zorganizowane w jeden lub więcej interfejsów.
- _Właściwości chmury_ definiujące informacje o urządzeniach, które IoT Central aplikacji. Na przykład właściwość chmury może rejestrować datę ostatniej obsługi urządzenia. Te informacje nigdy nie są udostępniane na urządzeniu.
- _Dostosowania_ pozwalają konstruktorowi zastępować niektóre definicje w modelu możliwości urządzenia. Na przykład Konstruktor może zastąpić nazwę właściwości urządzenia. Nazwy właściwości są wyświetlane na IoT Central pulpitów nawigacyjnych i formularzach.
- _Pulpity nawigacyjne i formularze_ umożliwiają konstruktorowi Tworzenie interfejsu użytkownika, który umożliwia operatorom monitorowanie i zarządzanie urządzeniami podłączonymi do aplikacji.

Aby utworzyć szablon urządzenia w IoT Central:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz pozycję **+ Nowy**  >  **niestandardowy**.
1. Wprowadź nazwę szablonu, na przykład **czujnik środowiska**.
1.  Naciśnij klawisz **Enter**. IoT Central tworzy pusty szablon urządzenia.

## <a name="manage-a-device-template"></a>Zarządzanie szablonem urządzenia

Można zmienić nazwę szablonu lub usunąć go ze strony głównej szablonu.

Po dodaniu modelu możliwości urządzenia do szablonu można go opublikować. Dopóki szablon nie zostanie opublikowany, nie można połączyć urządzenia na podstawie tego szablonu, aby operatory były widoczne na stronie **urządzenia** .

## <a name="create-a-capability-model"></a>Tworzenie modelu możliwości

Aby utworzyć model możliwości urządzenia, możesz:

- Użyj IoT Central, aby utworzyć niestandardowy model od podstaw.
- Importuj model z pliku JSON. Konstruktor urządzeń mógł używać Visual Studio Code do tworzenia modelu możliwości urządzenia dla aplikacji.
- Wybierz jedno z urządzeń z katalogu urządzeń. Ta opcja Importuje model możliwości urządzenia Opublikowany przez producenta dla tego urządzenia. Model możliwości urządzenia zaimportowany jak ten jest automatycznie publikowany.

## <a name="manage-a-capability-model"></a>Zarządzanie modelem możliwości

Po utworzeniu modelu możliwości urządzenia można:

- Dodaj interfejsy do modelu. Model musi mieć co najmniej jeden interfejs.
- Edytuj metadane modelu, takie jak jego identyfikator, przestrzeń nazw i nazwa.
- Usuń model.

## <a name="create-an-interface"></a>Tworzenie interfejsu

Możliwość urządzenia musi mieć co najmniej jeden interfejs. Interfejs jest kolekcją możliwości wielokrotnego użytku.

Aby utworzyć interfejs:

1. Przejdź do modelu możliwości urządzenia i wybierz pozycję **+ Dodaj interfejs**.

1. Na stronie **Wybieranie interfejsu** można:

    - Utwórz niestandardowy interfejs od podstaw.
    - Importuj istniejący interfejs z pliku. Konstruktor urządzeń mógł używać Visual Studio Code do tworzenia interfejsu dla urządzenia.
    - Wybierz jeden z standardowych interfejsów, takich jak interfejs **informacji o urządzeniu** . Interfejsy standardowe określają możliwości typowe dla wielu urządzeń. Te standardowe interfejsy są publikowane przez usługę Azure IoT i nie mogą być w wersji ani edytowane.

1. Po utworzeniu interfejsu wybierz opcję **Edytuj tożsamość** , aby zmienić nazwę wyświetlaną interfejsu.

1. Jeśli zdecydujesz się utworzyć interfejs niestandardowy od podstaw, możesz dodać możliwości urządzenia. Możliwości urządzenia to dane telemetryczne, właściwości i polecenia.

### <a name="telemetry"></a>Telemetria

Telemetrię jest strumieniem wartości wysyłanych z urządzenia, zazwyczaj z czujnika. Na przykład czujnik może zgłosić temperaturę otoczenia.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji telemetrii:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości telemetrii używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa pola w komunikacie telemetrii. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi być alfanumeryczne. |
| Typ możliwości | Telemetrii. |
| Typ semantyczny | Typ semantyczny telemetrii, taki jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych telemetrii, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu. |
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. Te informacje dotyczą **błędu**, **informacji**lub **ostrzeżenia**. |
| Wartości stanu | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka wartości telemetrii, na przykład **mph**, **%** lub ** &deg; C**. |
| Jednostka wyświetlania | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze dotyczące funkcji telemetrii. |
| Opis | Opis możliwości telemetrii. |

### <a name="properties"></a>Właściwości

Właściwości reprezentują wartości punktu w czasie. Na przykład urządzenie może użyć właściwości do raportowania temperatury docelowej, do której próbujesz uzyskać dostęp. Można ustawić właściwości do zapisu z IoT Central.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji właściwości:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości właściwości używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi być alfanumeryczne. |
| Typ możliwości | Wartość. |
| Typ semantyczny | Typ semantyczny właściwości, taki jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych właściwości, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu. |
| Z możliwością zapisu | Jeśli właściwość nie jest zapisywalna, urządzenie może raportować wartości właściwości do IoT Central. Jeśli właściwość jest zapisywalna, urządzenie może raportować wartości właściwości do IoT Central i IoT Central może wysyłać do urządzenia aktualizacje właściwości.
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. Te informacje dotyczą **błędu**, **informacji**lub **ostrzeżenia**. |
| Wartości stanu | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka wartości właściwości, takiej jak **mph**, **%** lub ** &deg; C**. |
| Jednostka wyświetlania | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze dotyczące możliwości właściwości. |
| Opis | Opis możliwości właściwości. |

### <a name="commands"></a>Polecenia

Polecenia urządzenia można wywołać z IoT Central. Polecenia opcjonalne umożliwiają przekazywanie parametrów do urządzenia i odbieranie odpowiedzi z urządzenia. Na przykład można wywołać polecenie, aby ponownie uruchomić urządzenie w ciągu 10 sekund.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji polecenia:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana polecenia użyta na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa polecenia. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi być alfanumeryczne. |
| Typ możliwości | Dotyczące. |
| Polecenie | `SynchronousExecutionType`. |
| Komentarz | Wszelkie komentarze dotyczące funkcji polecenia. |
| Opis | Opis możliwości polecenia. |
| Żądanie | Jeśli ta funkcja jest włączona, definicja parametru żądania, w tym: nazwa, nazwa wyświetlana, schemat, jednostka i jednostka wyświetlania. |
| Reakcja | Jeśli ta funkcja jest włączona, definicja odpowiedzi polecenia, w tym: nazwa, nazwa wyświetlana, schemat, jednostka i jednostka wyświetlania. |

#### <a name="offline-commands"></a>Polecenia w trybie offline

Możesz wybrać polecenia kolejki, jeśli urządzenie jest obecnie w trybie offline, włączając opcję **kolejki w trybie offline** dla polecenia w szablonie urządzenia.

Ta opcja służy IoT Hub komunikatów z chmury do urządzeń w celu wysyłania powiadomień do urządzeń. Aby dowiedzieć się więcej, zapoznaj się z artykułem IoT Hub [wysyłanie komunikatów z chmury do urządzenia](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Komunikaty z chmury do urządzenia:

- Są jednokierunkowymi powiadomieniami na urządzeniu z rozwiązania.
- Gwarancja dostarczania wiadomości co najmniej raz. IoT Hub utrzymuje komunikaty z chmury do urządzenia w kolejkach poszczególnych urządzeń, co gwarantuje odporność na łączność i awarie urządzeń.
- Wymagaj, aby urządzenie zaimplementował procedurę obsługi komunikatów w celu przetworzenia komunikatu z chmury do urządzenia.

> [!NOTE]
> Ta opcja jest dostępna tylko w interfejsie użytkownika sieci Web IoT Central. To ustawienie nie jest uwzględniane w przypadku eksportowania modelu lub interfejsu z szablonu urządzenia.

## <a name="manage-an-interface"></a>Zarządzanie interfejsem

Jeśli interfejs nie został opublikowany, można edytować możliwości zdefiniowane przez interfejs. Po opublikowaniu interfejsu, jeśli chcesz wprowadzić zmiany, musisz utworzyć nową wersję szablonu urządzenia i wersję interfejsu. W sekcji **Dostosowywanie** można wprowadzać zmiany, które nie wymagają przechowywania wersji, takie jak nazwy wyświetlane lub jednostki.

Możesz również wyeksportować interfejs jako plik JSON, jeśli chcesz użyć go ponownie w innym modelu możliwości.

## <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Właściwości chmury służą do przechowywania informacji o urządzeniach w IoT Central. Właściwości chmury nigdy nie są wysyłane do urządzenia. Na przykład można użyć właściwości chmury do przechowywania nazwy klienta, który zainstalował urządzenie, lub daty jego ostatniej usługi.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla właściwości chmury:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości właściwości chmury używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości chmury. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ semantyczny | Typ semantyczny właściwości, taki jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych właściwości chmury, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. |

## <a name="add-customizations"></a>Dodawanie dostosowań

Dostosowań należy używać w przypadku konieczności modyfikacji zaimportowanego interfejsu lub dodania funkcji specyficznych dla IoT Central do funkcji. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Możesz na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości. Jeśli istnieją zmiany, których nie można wprowadzić w sekcji **Dostosowywanie** , musisz zmienić wersję szablonu i interfejsu urządzenia, aby zmodyfikować tę możliwość.

### <a name="generate-default-views"></a>Generuj widoki domyślne

Generowanie widoków domyślnych to szybki sposób wizualizacji ważnych informacji o urządzeniu. Istnieje maksymalnie trzy widoki domyślne wygenerowane dla szablonu urządzenia:

- **Polecenia** udostępniają widok z poleceniami urządzenia i umożliwiają operatorowi wysyłanie ich do urządzenia.
- **Przegląd** zawiera widok z danymi telemetrycznymi urządzeń, wyświetlanie wykresów i metryk.
- Informacje **o** programie zawierają informacje o urządzeniu, wyświetlając właściwości urządzenia.

Po wybraniu opcji **Generuj domyślne widoki**zobaczysz, że zostały one automatycznie dodane w sekcji **widoki** szablonu urządzenia.

## <a name="add-dashboards"></a>Dodaj pulpity nawigacyjne

Dodaj pulpity nawigacyjne do szablonu urządzenia, aby umożliwić operatorom wizualizację urządzenia za pomocą wykresów i metryk. Dla szablonu urządzenia można mieć wiele pulpitów nawigacyjnych.

Aby dodać pulpit nawigacyjny do szablonu urządzenia:

1. Przejdź do szablonu urządzenia i wybierz pozycję **widoki**.
1. Wybierz **wizualizację urządzenia**.
1. Wprowadź nazwę pulpitu nawigacyjnego w polu **Nazwa pulpitu nawigacyjnego**.
1. Dodaj kafelki do pulpitu nawigacyjnego z listy kafelków statycznej, właściwości, chmury, telemetrii i poleceń. Przeciągnij i upuść kafelki, które chcesz dodać do pulpitu nawigacyjnego.
1. Aby wykreolić wiele wartości telemetrii na pojedynczym kafelku wykresu, wybierz wartości telemetryczne, a następnie wybierz pozycję **Połącz**.
1. Skonfiguruj wszystkie dodawane kafelki, aby dostosować sposób wyświetlania danych. Możesz to zrobić, wybierając ikonę koła zębatego lub wybierając pozycję **Zmień konfigurację** na kafelku wykresu.
1. Rozmieść kafelki na pulpicie nawigacyjnym i zmień ich rozmiar.
1. Zapisz zmiany.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurowanie urządzenia w wersji zapoznawczej do wyświetlania pulpitu nawigacyjnego

Aby wyświetlić i przetestować pulpit nawigacyjny, wybierz pozycję **Konfiguruj urządzenie w wersji zapoznawczej**. Dzięki temu pulpit nawigacyjny będzie widoczny po opublikowaniu. Użyj tej opcji, aby sprawdzić, czy w widokach są wyświetlane poprawne dane. Możesz wybrać następujące opcje:

- Brak urządzenia w wersji zapoznawczej.
- Rzeczywiste urządzenie testowe skonfigurowane dla szablonu urządzenia.
- Istniejące urządzenie w aplikacji, używając identyfikatora urządzenia.

## <a name="add-forms"></a>Dodawanie formularzy

Dodaj formularze do szablonu urządzenia, aby umożliwić operatorom zarządzanie urządzeniem przez wyświetlanie i Ustawianie właściwości. Operatory mogą edytować tylko właściwości chmury i zapisywalne właściwości urządzeń. Dla szablonu urządzenia można mieć wiele formularzy.

Aby dodać formularz do szablonu urządzenia:

1. Przejdź do szablonu urządzenia i wybierz pozycję **widoki**.
1. Wybierz pozycję **Edytowanie urządzenia i dane w chmurze**.
1. Wprowadź nazwę formularza w polu **nazwa formularza**.
1. Wybierz liczbę kolumn, które mają być używane do układania formularza.
1. Dodaj właściwości do istniejącej sekcji w formularzu lub wybierz pozycję Właściwości, a następnie wybierz pozycję **Dodaj sekcję**. Użyj sekcji, aby grupować właściwości w formularzu. Możesz dodać tytuł do sekcji.
1. Skonfiguruj każdą właściwość w formularzu, aby dostosować jej zachowanie.
1. Rozmieść właściwości w formularzu.
1. Zapisz zmiany.

## <a name="publish-a-device-template"></a>Publikowanie szablonu urządzenia

Aby można było połączyć urządzenie, które implementuje model możliwości urządzenia, należy opublikować szablon urządzenia.

Po opublikowaniu szablonu urządzenia można wprowadzać tylko ograniczone zmiany w modelu możliwości urządzenia. Aby zmodyfikować interfejs, należy [utworzyć i opublikować nową wersję](./howto-version-device-template.md).

Aby opublikować szablon urządzenia, przejdź do szablonu urządzenia i wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia operator może przejść do strony **urządzenia** i dodać rzeczywiste lub symulowane urządzenia, które używają szablonu urządzenia. Możesz nadal modyfikować i zapisywać szablon urządzenia w miarę wprowadzania zmian. Aby wypchnąć te zmiany do operatora w celu wyświetlenia na stronie **urządzenia** , należy wybrać opcję **Publikuj** za każdym razem.

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, zalecanym następnym krokiem jest zapoznanie się z informacjami na temat [przechowywania szablonów urządzeń](./howto-version-device-template.md).