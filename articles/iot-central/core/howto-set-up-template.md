---
title: Definiowanie nowego typu urządzenia IoT w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jako konstruktor, jak utworzyć nowy szablon urządzenia Usługi Azure IoT w aplikacji Azure IoT Central. Definiujesz dane telemetryczne, stan, właściwości i polecenia dla typu.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 2a99f261e1a834705d081e8197e4ae627cf1cb9f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756650"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definiowanie nowego typu urządzenia IoT w aplikacji Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Szablon urządzenia to plan, który definiuje cechy i zachowania typu urządzenia, które łączy się z aplikacją Azure IoT Central.

Na przykład konstruktor może utworzyć szablon urządzenia dla połączonego wentylatora, który ma następujące cechy:

- Wysyła dane telemetryczne temperatury
- Wysyła właściwość lokalizacji
- Wysyła zdarzenia błędu silnika wentylatora
- Wysyła stan pracy wentylatora
- Zapewnia zapisowalne właściwości prędkości wentylatora
- Zawiera polecenie ponownego uruchomienia urządzenia
- Zapewnia ogólny widok urządzenia za pośrednictwem pulpitu nawigacyjnego

Z tego szablonu urządzenia operator może tworzyć i łączyć prawdziwe urządzenia wentylatora. Wszystkie te wentylatory mają pomiary, właściwości i polecenia używane przez operatorów do monitorowania i zarządzania nimi. Operatorzy używają pulpitów nawigacyjnych i formularzy urządzenia do interakcji z urządzeniami wentylatora.

> [!NOTE]
> Tylko konstruktorzy i administratorzy mogą tworzyć, edytować i usuwać szablony urządzeń. Każdy użytkownik może tworzyć urządzenia na stronie **Urządzenia** na podstawie istniejących szablonów urządzeń.

[IoT Plug and Play (wersja zapoznawcza)](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrowanie urządzeń bez pisania kodu osadzonego urządzenia. Sednem IoT Plug and Play (podgląd) jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzenia. W aplikacji IoT Central szablony urządzeń używają tych modeli urządzeń typu IoT Plug and Play (podgląd).

Jako konstruktor masz kilka opcji tworzenia szablonów urządzeń:

- Zaprojektuj szablon urządzenia w ustroju sieciowym IoT, a następnie zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Zaimportuj model możliwości urządzenia z [katalogu urządzeń certyfikatu platformy Azure dla IoT](https://aka.ms/iotdevcat). Następnie dodaj wszystkie właściwości chmury, dostosowania i pulpity nawigacyjne, które będą potrzebne aplikacji IoT Central.
- Tworzenie modelu możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie dodaj wszystkie właściwości chmury, dostosowania i pulpity nawigacyjne, które będą potrzebne aplikacji IoT Central.
- Tworzenie modelu możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj kod urządzenia z modelu i podłącz rzeczywiste urządzenie do aplikacji IoT Central przy użyciu połączenia z urządzeniem. IoT Central wyszukuje i importuje model możliwości urządzenia z publicznego repozytorium. Następnie można dodać wszystkie właściwości chmury, dostosowania i pulpity nawigacyjne aplikacji IoT Central musi do szablonu urządzenia.

## <a name="create-a-device-template-from-the-device-catalog"></a>Tworzenie szablonu urządzenia z katalogu urządzeń

Jako konstruktor możesz szybko rozpocząć tworzenie rozwiązania przy użyciu urządzenia z certyfikatem IoT Plug and Play (preview). Zobacz listę w wykazie [urządzeń Usługi Azure IoT](https://catalog.azureiotsolutions.com/alldevices). Funkcja IoT Central integruje się z katalogiem urządzeń, dzięki czemu można zaimportować model możliwości urządzenia z dowolnego z tych urządzeń z certyfikatem IoT Plug and Play (preview). Aby utworzyć szablon urządzenia z jednego z tych urządzeń w umiań IoT Central:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz **+ Nowy**, a następnie wybierz dowolne z certyfikowanych urządzeń IoT Plug and Play (wersja zapoznawcza) z katalogu. IoT Central tworzy szablon urządzenia na podstawie tego modelu możliwości urządzenia.
1. Dodaj do szablonu urządzenia wszystkie właściwości, dostosowania lub widoki w chmurze.
1. Wybierz **pozycję Publikuj,** aby udostępnić szablon operatorom do wyświetlania i łączenia urządzeń.

## <a name="create-a-device-template-from-scratch"></a>Tworzenie szablonu urządzenia od podstaw

Szablon urządzenia zawiera:

- _Model możliwości urządzenia,_ który określa dane telemetryczne, właściwości i polecenia, które implementuje urządzenie. Te możliwości są zorganizowane w jeden lub więcej interfejsów.
- _Właściwości chmury,_ które definiują informacje, które aplikacja IoT Central przechowuje o urządzeniach. Na przykład właściwość chmury może rejestrować datę ostatniego serwisu urządzenia. Te informacje nigdy nie są udostępniane urządzeniu.
- _Dostosowania_ umożliwiają konstruktorowi zastąpienie niektórych definicji w modelu możliwości urządzenia. Na przykład konstruktor może zastąpić nazwę właściwości urządzenia. Nazwy właściwości są wyświetlane w pulpitach nawigacyjnych i formularzach IoT Central.
- _Pulpity nawigacyjne i formularze_ umożliwiają konstruktorowi utworzenie interfejsu użytkownika, który umożliwia operatorom monitorowanie urządzeń podłączonych do aplikacji i zarządzanie nimi.

Aby utworzyć szablon urządzenia w centrum IoT:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz **+ Nowy** > **niestandardowy**.
1. Wprowadź nazwę szablonu, na przykład **Czujnik środowiska**.
1. Naciśnij **klawisz Enter**. Funkcja IoT Central tworzy pusty szablon urządzenia.

## <a name="manage-a-device-template"></a>Zarządzanie szablonem urządzenia

Możesz zmienić nazwę lub usunąć szablon ze strony głównej szablonu.

Po dodaniu modelu możliwości urządzenia do szablonu można go opublikować. Dopóki nie opublikujesz szablonu, nie możesz połączyć urządzenia opartego na tym szablonie, który operatorzy zobaczą na stronie **Urządzenia.**

## <a name="create-a-capability-model"></a>Tworzenie modelu możliwości

Aby utworzyć model możliwości urządzenia, można:

- Użyj usługi IoT Central, aby utworzyć model niestandardowy od podstaw.
- Importowanie modelu z pliku JSON. Konstruktor urządzeń mógł użyć programu Visual Studio Code do tworzenia modelu możliwości urządzenia dla aplikacji.
- Wybierz jedno z urządzeń z katalogu urządzeń. Ta opcja importuje model możliwości urządzenia, który producent opublikował dla tego urządzenia. Model możliwości urządzenia importowany w ten sposób jest automatycznie publikowany.

## <a name="manage-a-capability-model"></a>Zarządzanie modelem możliwości

Po utworzeniu modelu możliwości urządzenia można:

- Dodaj interfejsy do modelu. Model musi mieć co najmniej jeden interfejs.
- Edytuj metadane modelu, takie jak jego identyfikator, obszar nazw i nazwa.
- Usuń model.

## <a name="create-an-interface"></a>Tworzenie interfejsu

Funkcja urządzenia musi mieć co najmniej jeden interfejs. Interfejs jest wielokrotnego stosowania kolekcji możliwości.

Aby utworzyć interfejs:

1. Przejdź do modelu możliwości urządzenia i wybierz **+ Dodaj interfejs**.

1. Na stronie **Wybierz interfejs** można:

    - Tworzenie niestandardowego interfejsu od podstaw.
    - Importowanie istniejącego interfejsu z pliku. Konstruktor urządzeń mógł użyć programu Visual Studio Code do tworzenia interfejsu dla urządzenia.
    - Wybierz jeden ze standardowych interfejsów, takich jak interfejs **Informacji o urządzeniu.** Interfejsy standardowe określają możliwości wspólne dla wielu urządzeń. Te standardowe interfejsy są publikowane przez usługę Azure IoT i nie można ich wersjona lub edytować.

1. Po utworzeniu interfejsu wybierz pozycję **Edytuj tożsamość,** aby zmienić nazwę wyświetlaną interfejsu.

1. Jeśli zdecydujesz się utworzyć niestandardowy interfejs od podstaw, możesz dodać możliwości urządzenia. Możliwości urządzenia to dane telemetryczne, właściwości i polecenia.

### <a name="telemetry"></a>Telemetria

Telemetria to strumień wartości wysyłanych z urządzenia, zazwyczaj z czujnika. Na przykład czujnik może zgłaszać temperaturę otoczenia.

W poniższej tabeli przedstawiono ustawienia konfiguracji możliwości telemetrii:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Wyświetlana nazwa wartości telemetrycznometrii używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa pola w komunikacie telemetrycznym. Funkcja IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | Telemetrii. |
| Typ semantyczny | Typ semantyczny danych telemetrycznych, takich jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z następujących pól są dostępne. |
| Schemat | Typ danych telemetrycznych, takich jak double, string lub wektor. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantycznych zdarzeń i stanów. |
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. Ważność to **Błąd,** **Informacje**lub **Ostrzeżenie**. |
| Wartości stanu | Dostępne tylko dla typu semantycznego stanu. Zdefiniuj możliwe wartości stanu, z których każda ma wyświetlaną nazwę, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka dla wartości telemetrii, **%** takich jak **mph**, lub ** &deg;C**. |
| Wyświetlacz | Jednostka wyświetlająca do użytku na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze dotyczące możliwości telemetrii. |
| Opis | Opis możliwości telemetrii. |

### <a name="properties"></a>Właściwości

Właściwości reprezentują wartości punktu w czasie. Na przykład urządzenie może użyć właściwości do raportowania temperatury docelowej, do której próbuje osiągnąć. Można ustawić zapisywalne właściwości z IoT Central.

W poniższej tabeli przedstawiono ustawienia konfiguracji właściwości:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Wyświetlana nazwa właściwości używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości. Funkcja IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | Właściwość. |
| Typ semantyczny | Typ semantyczny właściwości, takich jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z następujących pól są dostępne. |
| Schemat | Typ danych właściwości, takich jak double, string lub wektor. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantycznych zdarzeń i stanów. |
| Z możliwością zapisu | Jeśli właściwość nie jest zapisywalna, urządzenie może zgłaszać wartości właściwości do usługi IoT Central. Jeśli właściwość jest zapisywalna, urządzenie może raportować wartości właściwości do IoT Central i IoT Central można wysyłać aktualizacje właściwości do urządzenia.
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. Ważność to **Błąd,** **Informacje**lub **Ostrzeżenie**. |
| Wartości stanu | Dostępne tylko dla typu semantycznego stanu. Zdefiniuj możliwe wartości stanu, z których każda ma wyświetlaną nazwę, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka dla wartości właściwości, takich **%** jak **mph**, lub ** &deg;C**. |
| Wyświetlacz | Jednostka wyświetlająca do użytku na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze na temat możliwości właściwości. |
| Opis | Opis właściwości. |

### <a name="commands"></a>Polecenia

Polecenia urządzenia można wywołać z centrum IoT. Polecenia opcjonalnie przekazują parametry do urządzenia i odbierają odpowiedź z urządzenia. Na przykład można wywołać polecenie, aby ponownie uruchomić urządzenie w ciągu 10 sekund.

W poniższej tabeli przedstawiono ustawienia konfiguracji funkcji polecenia:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Wyświetlana nazwa polecenia używanego na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa polecenia. Funkcja IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | Polecenia. |
| Polecenie | `SynchronousExecutionType`. |
| Komentarz | Wszelkie komentarze na temat możliwości polecenia. |
| Opis | Opis możliwości polecenia. |
| Żądanie | Jeśli ta opcja jest włączona, definicja parametru żądania, w tym: nazwa wyświetlana, schemat, jednostka i jednostka wyświetlająca. |
| Odpowiedź | Jeśli ta opcja jest włączona, definicja odpowiedzi polecenia, w tym: nazwa wyświetlana, schemat, jednostka i jednostka wyświetlająca. |

## <a name="manage-an-interface"></a>Zarządzanie interfejsem

Jeśli interfejs nie został opublikowany, można edytować możliwości zdefiniowane przez interfejs. Po opublikowaniu interfejsu, jeśli chcesz wprowadzić jakiekolwiek zmiany, musisz utworzyć nową wersję szablonu urządzenia i wersję interfejsu. W sekcji **Dostosuj** można wprowadzić zmiany, które nie wymagają przechowywania wersji, takie jak nazwy wyświetlane lub jednostki.

Można również wyeksportować interfejs jako plik JSON, jeśli chcesz ponownie użyć go w innym modelu możliwości.

## <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Właściwości chmury służy do przechowywania informacji o urządzeniach w centrum IoT. Właściwości chmury nigdy nie są wysyłane do urządzenia. Na przykład można użyć właściwości chmury do przechowywania nazwy klienta, który zainstalował urządzenie, lub ostatniej daty usługi urządzenia.

W poniższej tabeli przedstawiono ustawienia konfiguracji właściwości chmury:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Wyświetlana nazwa wartości właściwości w chmurze używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości chmury. Funkcja IoT Central generuje wartość tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ semantyczny | Typ semantyczny właściwości, takich jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z następujących pól są dostępne. |
| Schemat | Typ danych właściwości chmury, takich jak double, string lub wektora. Dostępne opcje są określane przez typ semantyczny. |

## <a name="add-customizations"></a>Dodawanie dostosowań

Dostosowania należy używać, gdy trzeba zmodyfikować importowany interfejs lub dodać funkcje specyficzne dla usługi IoT Central do możliwości. Można dostosować tylko pola, które nie łamią zgodności interfejsu. Można na przykład:

- Dostosuj nazwę wyświetlaną i jednostki tej funkcji.
- Dodaj domyślny kolor, który będzie używany, gdy wartość pojawia się na wykresie.
- Określ wartości początkowe, minimalne i maksymalne dla właściwości.

Nie można dostosować nazwę możliwości lub typ możliwości. Jeśli w sekcji **Dostosuj** nie można wprowadzić zmian, musisz dostosować szablon i interfejs urządzenia do wersji.

### <a name="generate-default-views"></a>Generowanie widoków domyślnych

Generowanie widoków domyślnych to szybki sposób wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia wygenerowano maksymalnie trzy widoki domyślne:

- **Polecenia** zapewniają widok z poleceniami urządzenia i umożliwia operatorowi wysyłanie ich do urządzenia.
- **Omówienie** zapewnia widok z telemetrią urządzenia, wyświetlanie wykresów i metryk.
- **Informacje** zapewnia widok z informacjami o urządzeniu, wyświetlanie właściwości urządzenia.

Po wybraniu opcji **Generuj widoki domyślne**zobaczysz, że zostały one automatycznie dodane w sekcji **Widoki** szablonu urządzenia.

## <a name="add-dashboards"></a>Dodawanie pulpitów nawigacyjnych

Dodaj pulpity nawigacyjne do szablonu urządzenia, aby umożliwić operatorom wizualizację urządzenia przy użyciu wykresów i metryk. Możesz mieć wiele pulpitów nawigacyjnych dla szablonu urządzenia.

Aby dodać pulpit nawigacyjny do szablonu urządzenia:

1. Przejdź do szablonu urządzenia i wybierz pozycję **Widoki**.
1. Wybierz **wizualizację urządzenia**.
1. Wprowadź nazwę pulpitu nawigacyjnego w **aplikacji Nazwa pulpitu nawigacyjnego**.
1. Dodaj kafelki do pulpitu nawigacyjnego z listy statycznych, właściwości, właściwości chmury, danych telemetrycznych i kafelków poleceń. Przeciągnij i upuść kafelki, które chcesz dodać do pulpitu nawigacyjnego.
1. Aby wykreślić wiele wartości telemetrycznych na jednym kafelku wykresu, zaznacz wartości telemetrii, a następnie wybierz pozycję **Połącz**.
1. Skonfiguruj każdy dodawany kafelek, aby dostosować sposób wyświetlania danych. Można to zrobić, wybierając ikonę koła zębatego lub wybierając **pozycję Zmień konfigurację** na kafelku wykresu.
1. Rozmieść i zmienić rozmiar kafelków na pulpicie nawigacyjnym.
1. Zapisz zmiany.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurowanie urządzenia podglądu do wyświetlania pulpitu nawigacyjnego

Aby wyświetlić i przetestować pulpit nawigacyjny, wybierz pozycję **Konfiguruj urządzenie podglądu**. Dzięki temu można zobaczyć pulpit nawigacyjny, jak operator widzi go po jego opublikowaniu. Ta opcja służy do sprawdzania, czy widoki są wyświetlane poprawne dane. Możesz wybrać następujące opcje:

- Brak urządzenia podglądu.
- Prawdziwe urządzenie testowe skonfigurowane dla szablonu urządzenia.
- Istniejące urządzenie w aplikacji przy użyciu identyfikatora urządzenia.

## <a name="add-forms"></a>Dodawanie formularzy

Dodawanie formularzy do szablonu urządzenia w celu umożliwienia operatorom zarządzania urządzeniem przez wyświetlanie i ustawianie właściwości. Operatorzy mogą edytować tylko właściwości chmury i zapisywalne właściwości urządzenia. Dla szablonu urządzenia może być dostępnych wiele formularzy.

Aby dodać formularz do szablonu urządzenia:

1. Przejdź do szablonu urządzenia i wybierz pozycję **Widoki**.
1. Wybierz **pozycję Edycja urządzenia i danych w chmurze**.
1. Wprowadź nazwę formularza w **aplikacji Nazwa formularza**.
1. Wybierz liczbę kolumn, które mają być używane do rozłąki formularza.
1. Dodaj właściwości do istniejącej sekcji formularza lub wybierz właściwości i wybierz pozycję **Dodaj sekcję**. Sekcje służy do grupowania właściwości formularza. Tytuł można dodać do sekcji.
1. Skonfiguruj każdą właściwość w formularzu, aby dostosować jej zachowanie.
1. Rozmieść właściwości formularza.
1. Zapisz zmiany.

## <a name="publish-a-device-template"></a>Publikowanie szablonu urządzenia

Przed podłączeniem urządzenia, które implementuje model możliwości urządzenia, należy opublikować szablon urządzenia.

Po opublikowaniu szablonu urządzenia można wprowadzać tylko ograniczone zmiany w modelu możliwości urządzenia. Aby zmodyfikować interfejs, należy [utworzyć i opublikować nową wersję](./howto-version-device-template.md).

Aby opublikować szablon urządzenia, przejdź do szablonu urządzenia i wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia operator może przejść do strony **Urządzenia** i dodać rzeczywiste lub symulowane urządzenia korzystające z szablonu urządzenia. Podczas wprowadzania zmian możesz nadal modyfikować i zapisywać szablon urządzenia. Jeśli chcesz wypchnąć te zmiany do operatora, aby wyświetlić w obszarze **urządzenia** strony, należy wybrać **Publikuj** za każdym razem.

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest przeczytanie o [wersjonacie szablonów urządzeń](./howto-version-device-template.md).
