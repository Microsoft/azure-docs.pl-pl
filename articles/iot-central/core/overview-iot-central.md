---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT i pomaga w zmniejszeniu obciążenia i kosztów operacji zarządzania IoT oraz rozwoju. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 409ba6cd192f65c73aba6bd6eb11a26ec1f820fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790885"
---
# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty tworzenia i utrzymywania rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Wybranie opcji kompilowania przy użyciu IoT Central umożliwia skoncentrowanie się na czasie, pieniędzy i energii na transformacje swojej firmy z danymi IoT, a nie tylko utrzymaniem i aktualizacją złożonej i ciągle rosnącej infrastruktury IoT.

Interfejs użytkownika sieci Web umożliwia monitorowanie warunków urządzenia, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Ponadto umożliwia ona działanie w usłudze Device Insights poprzez rozszerzenie usługi IoT Intelligence na aplikacje biznesowe.

Ten artykuł zawiera opis IoT Central:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją
- Możliwości Azure IoT Edge w IoT Central.
- Jak podłączyć urządzenia obsługiwane przez środowisko uruchomieniowe Azure IoT Edge do aplikacji.

## <a name="personas"></a>Osoby

Dokumentacja IoT Central obejmuje cztery osób, którzy współpracują z aplikacją IoT Central:

- _Konstruktor rozwiązań_ jest odpowiedzialny za Definiowanie typów urządzeń łączących się z aplikacją i dostosowywania aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie [rolami i uprawnieniami użytkownika](howto-administer.md) w aplikacji.
- _Deweloper urządzenia_ tworzy kod, który jest uruchamiany na urządzeniu lub IoT Edge module podłączonym do aplikacji.

## <a name="create-your-iot-central-application"></a>Tworzenie aplikacji IoT Central

Jako Konstruktor rozwiązań możesz utworzyć niestandardowe rozwiązanie IoT hostowane w chmurze dla swojej organizacji za pomocą IoT Central. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację IoT Central, a następnie dostosować ją do określonych wymagań w przeglądarce. Możesz zacząć od _szablonu aplikacji_ ogólnej lub z jednym z branżowych szablonów aplikacji dla [handlu detalicznego](../retail/overview-iot-central-retail.md), [energetyki](../energy/overview-iot-central-energy.md), [administracji publicznej](../government/overview-iot-central-government.md)lub [opieki zdrowotnej](../healthcare/overview-iot-central-healthcare.md).

Jako Konstruktor rozwiązań można utworzyć _szablon urządzenia_ dla urządzeń, które łączą się z aplikacją za pomocą narzędzi sieci Web. Szablon urządzenia to plan, który definiuje charakterystykę i zachowanie typu urządzenia, takiego jak:

- Dane telemetryczne wysyłane przez nią.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Właściwości, które są ustawiane przez operator, które określają zachowanie urządzenia.

Ten szablon urządzenia obejmuje następujące:

- _Model możliwości urządzenia_ , który opisuje możliwości implementowane przez urządzenie, takie jak dane telemetryczne, które wysyła, oraz właściwości, które zgłasza.
- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

### <a name="pricing"></a>Cennik

Możesz utworzyć aplikację IoT Central przy użyciu 7-dniowej bezpłatnej wersji próbnej lub użyć planu cenowego w warstwie Standardowa.

- Aplikacje tworzone za pomocą planu *bezpłatnego* są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Możesz przekonwertować je tak, aby używały standardowego planu cenowego w dowolnym momencie przed wygaśnięciem.
- W przypadku aplikacji tworzonych przy użyciu planu *standardowego* są naliczane opłaty za poszczególne urządzenia. możesz wybrać plan cenowy w **warstwie Standardowa 1** lub **standardowa 2** z dwoma urządzeniami, które są bezpłatne. Dowiedz się więcej o planach cen bezpłatnych i standardowych na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Tworzenie szablonów urządzeń

Program [IoT Plug and Play (wersja zapoznawcza)](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrację urządzeń bez konieczności pisania kodu urządzenia osadzonego. Podstawą Plug and Play IoT (wersja zapoznawcza) jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzeń. W aplikacji IoT Central szablony urządzeń używają następujących modeli możliwości urządzenia IoT Plug and Play (wersja zapoznawcza).

Jako Konstruktor rozwiązań masz kilka opcji tworzenia szablonów urządzeń:

- Zaimportuj model możliwości urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat) , a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje IoT Central aplikacji.
- Zaprojektuj szablon urządzenia w IoT Central a następnie Zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code i Opublikuj model w repozytorium. Zaimplementuj swój kod urządzenia z modelu i Połącz urządzenie z aplikacją IoT Central. IoT Central odnajduje model możliwości urządzenia z repozytorium i tworzy prosty szablon urządzenia.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje aplikacja IoT Central.

Jako Konstruktor rozwiązań możesz użyć IoT Central do wygenerowania kodu dla urządzeń testowych, aby sprawdzić poprawność szablonów urządzeń.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Jako Konstruktor rozwiązań można także dostosować interfejs użytkownika aplikacji IoT Central dla operatorów, którzy są odpowiedzialni za codzienne korzystanie z aplikacji. Dostępne są dostosowania dostępne w konstruktorze rozwiązań:

- Definiowanie układu właściwości i ustawień w szablonie urządzenia.
- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.

## <a name="pricing"></a>Cennik

Możesz utworzyć aplikację IoT Central przy użyciu 7-dniowej bezpłatnej wersji próbnej lub użyć planu cenowego w warstwie Standardowa.

- Aplikacje tworzone za pomocą planu *bezpłatnego* są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Możesz przekonwertować je tak, aby używały standardowego planu cenowego w dowolnym momencie przed wygaśnięciem.
- W przypadku aplikacji tworzonych przy użyciu planu w *warstwie Standardowa* są naliczane opłaty za poszczególne urządzenia. możesz wybrać plan cenowy w **warstwie Standardowa 1** lub **standardowa 2** z dwoma bezpłatnymi urządzeniami. Dowiedz się więcej o [cenach IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Łączenie urządzeń

Usługa Azure IoT Central używa [usługi azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania wszystkimi rejestracjami i połączeniem urządzeń.

Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń w odpowiedniej skali.
- Można generować poświadczenia urządzenia i konfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pomocą interfejsu użytkownika IoT Central.
- Urządzenia, które mają być połączone za pomocą sygnatur dostępu współdzielonego.
- Urządzenia, które mają być połączone przy użyciu standardowych certyfikatów X. 509.
- Aby zarejestrować urządzenia w IoT Central, należy użyć własnych identyfikatorów urządzeń. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden spójny sposób łączenia urządzeń z IoT Central.

Aby dowiedzieć się więcej, zobacz temat [połączono z usługą Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Urządzenia usługi Azure IoT Edge

A także urządzeń utworzonych przy użyciu [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks), można również łączyć [Azure IoT Edge urządzeń](../../iot-edge/about-iot-edge.md) z aplikacją IoT Central. Azure IoT Edge umożliwia uruchamianie analizy chmurowej i logiki niestandardowej bezpośrednio na urządzeniach IoT zarządzanych przez IoT Central. Środowisko uruchomieniowe IoT Edge umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge urządzeń i IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Zachowanie łączności

Aplikacje IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza koszty administracyjne związane z zarządzaniem aplikacjami.

Jako operator korzystasz z aplikacji IoT Central do zarządzania urządzeniami w rozwiązaniu IoT Central. Operatory wykonywania zadań, takich jak:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Jako Konstruktor rozwiązań można definiować niestandardowe reguły i akcje, które działają w ramach przesyłania strumieniowego danych z połączonych urządzeń. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

Administratorzy zarządzają dostępem do aplikacji przy użyciu [ról i uprawnień użytkownika](howto-administer.md).

W przypadku wszystkich rozwiązań IoT zaprojektowanych do działania w odpowiedniej skali, podejście strukturalne do zarządzania urządzeniami jest ważne. Połączenie urządzeń z chmurą jest za mało, aby urządzenia były połączone i działać prawidłowo. Operator może korzystać z następujących funkcji IoT Central, aby zarządzać urządzeniami w całym cyklu życia aplikacji:

### <a name="dashboards"></a>Pulpity nawigacyjne

Wbudowane [pulpity nawigacyjne](./howto-set-up-template.md#generate-default-views) udostępniają DOSTOSOWYWALNY interfejs użytkownika do monitorowania kondycji i danych telemetrycznych urządzenia. Zacznij od wstępnie skompilowanego pulpitu nawigacyjnego w [szablonie aplikacji](howto-use-app-templates.md) lub Utwórz własne pulpity nawigacyjne dostosowane do potrzeb operatorów. Pulpity nawigacyjne można udostępniać wszystkim użytkownikom w aplikacji lub utrzymywać je prywatnie.

### <a name="rules-and-actions"></a>Reguły i akcje

Twórz [niestandardowe reguły](tutorial-create-telemetry-rules.md) na podstawie stanu urządzenia i telemetrii, aby identyfikować urządzenia z koniecznością uwagi. Skonfiguruj akcje powiadamiania odpowiednich osób i zapewnić podejmowanie środków naprawczych w odpowiednim czasie.

### <a name="jobs"></a>Stanowiska

[Zadania](howto-run-a-job.md) umożliwiają stosowanie pojedynczych lub zbiorczych aktualizacji do urządzeń przez ustawienie właściwości lub Wywoływanie poleceń. 

### <a name="user-roles-and-permissions"></a>Role i uprawnienia użytkownika

[Role i uprawnienia](howto-manage-users-roles.md) umożliwiają administratorowi dostosowanie środowiska użytkownika. Administrator używa interfejsu użytkownika sieci Web do tworzenia ról i przypisywania uprawnień.

## <a name="transform-your-iot-data"></a>Przekształcanie danych IoT

Jako platforma aplikacji IoT Central umożliwia przekształcanie danych IoT w szczegółowe informacje biznesowe, które umożliwiają wykonanie akcji. [Zasady](./tutorial-create-telemetry-rules.md), [eksport danych](./howto-export-data.md)i [publiczny interfejs API REST](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) to przykłady umożliwiające integrację IoT Central z aplikacjami biznesowymi:

![Jak IoT Central może przekształcić dane IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorowanie kondycji i operacji urządzenia przy użyciu reguł

Gdy urządzenia są połączone i wysyłające dane, zasady mogą identyfikować urządzenia z problemami lub wysyłać komunikaty o błędach, aby można było je rozwiązać przy minimalnym przestoju. Kompiluj reguły w aplikacji IoT Central, aby monitorować dane telemetryczne z urządzeń i wysyłać alerty do operatora, gdy Metryka przekroczy próg, lub urządzenie wysyła określoną wiadomość. Akcje poczty e-mail i elementy webhook dla reguł powiadamiają odpowiednie osoby i właściwe systemy podrzędne.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Uruchamianie analiz niestandardowych i przetwarzanie na wyeksportowanych danych

Możesz generować szczegółowe informacje biznesowe, takie jak określanie trendów wydajności maszyn lub przewidywanie przyszłego użycia energii w fabryce, tworząc niestandardowe potoki analizy, aby przetwarzać dane telemetryczne z urządzeń i przechowywać wyniki. Skonfiguruj eksportowanie danych w aplikacji IoT Central, aby eksportować dane telemetryczne, zmiany właściwości urządzeń i zmiany szablonu urządzenia do innych usług, w których można analizować, przechowywać i wizualizować danych przy użyciu preferowanych narzędzi.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Tworzenie niestandardowych rozwiązań IoT i integracji za pomocą interfejsów API REST

Twórz rozwiązania IoT, takie jak:

- Aplikacje towarzyszące urządzeniom przenośnym, które mogą zdalnie konfigurować i kontrolować urządzenia.
- Integracji niestandardowe umożliwiające korzystanie z istniejących aplikacji biznesowych w celu współdziałania z danymi i urządzeniami IoT.
- Aplikacje do zarządzania urządzeniami do modelowania urządzeń, dołączania, zarządzania i dostępu do danych.

## <a name="quotas"></a>Przydziały

Każda subskrypcja platformy Azure ma domyślne przydziały, które mogą mieć wpływ na zakres rozwiązania IoT. Obecnie IoT Central ogranicza liczbę aplikacji, które można wdrożyć w subskrypcji do 10. Jeśli musisz zwiększyć ten limit, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Znane problemy

- Ciągły eksport danych nie obsługuje formatu Avro (niezgodność).
- GEOJSON nie jest obecnie obsługiwany.
- Kafelek mapy nie jest obecnie obsługiwany.
- Typy schematów tablicy nie są obsługiwane.
- Obsługiwane są tylko zestawy SDK dla urządzeń C i Node. js.
- IoT Central jest obecnie dostępna w lokalizacjach Stany Zjednoczone, Europy, Azja i Pacyfik, Australii, Wielkiej Brytanii i Japonii.
- Nie można użyć szablonu aplikacji **aplikacja niestandardowa (starsza wersja)** w lokalizacjach w Zjednoczonym Królestwie i w Japonii.
- Modele możliwości urządzeń muszą mieć wszystkie interfejsy zdefiniowane wewnętrznie w tym samym pliku.
- Obsługa [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) jest dostępna w wersji zapoznawczej i jest obsługiwana tylko w wybranych regionach.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd IoT Central, Oto kilka sugerowanych następnych kroków:

- Zapoznaj się z dostępnymi [technologiami i usługami platformy Azure służącymi do tworzenia rozwiązań IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
- Dowiedz się więcej o usłudze [IoT Plug and Play (wersja zapoznawcza)](../../iot-pnp/overview-iot-plug-and-play.md).
- Dowiedz się, jak [połączyć urządzenie Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Dowiedz się więcej na temat [technologii i usług Azure IoT](../../iot-fundamentals/iot-services-and-technologies.md).

Jeśli jesteś deweloperem urządzenia i chcesz szczegółowe do pewnego kodu, sugerowanym następnym krokiem jest [utworzenie i podłączenie aplikacji klienckiej do aplikacji IoT Central platformy Azure](./tutorial-connect-device-nodejs.md).
