---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT i pomaga zmniejszyć obciążenie i koszty operacji zarządzania IoT i rozwoju. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 38152889ad43b41aeb345f59963cbfb869964b98
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025241"
---
# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty opracowywania, zarządzania i utrzymywania rozwiązań IoT klasy korporacyjnej. Wybór kompilacji za pomocą usługi IoT Central daje możliwość skoncentrowania czasu, pieniędzy i energii na przekształcaniu firmy za pomocą danych IoT, a nie tylko na utrzymywaniu i aktualizowaniu złożonej i stale rozwijającej się infrastruktury IoT.

Internetowy interfejs użytkownika umożliwia monitorowanie warunków urządzenia, tworzenie reguł i zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Ponadto umożliwia działanie w szczegółowych warunkach urządzenia, rozszerzając inteligencję IoT na aplikacje biznesowe.

W tym artykule opisano dla IoT Central:

- Typowe osoby skojarzone z projektem
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją
- Możliwości usługi Azure IoT Edge w Usłudze IoT Central.
- Jak połączyć urządzenia zasilane w czasie pracy usługi Azure IoT Edge z aplikacją.

## <a name="known-issues"></a>Znane problemy

- Ciągły eksport danych nie obsługuje formatu Avro (niezgodność).
- GeoJSON nie jest obecnie obsługiwany.
- Kafelek Mapy nie jest obecnie obsługiwany.
- Zadania nie obsługują typów złożonych.
- Typy schematów tablic nie są obsługiwane.
- Obsługiwane są tylko sdk urządzenia C oraz moduły SDK urządzenia i usługi Node.js.
- IoT Central jest obecnie dostępny w lokalizacjach w Stanach Zjednoczonych, Europie, Regionie Azji i Pacyfiku, Australii, Wielkiej Brytanii i Japonii.
- Nie można użyć szablonu aplikacji **niestandardowej (starszej wersji)** w lokalizacjach w Wielkiej Brytanii i Japonii.
- Modele możliwości urządzenia muszą mieć wszystkie interfejsy zdefiniowane w tym samym pliku.
- Obsługa [technologii IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) jest dostępna w wersji zapoznawczej i jest obsługiwana tylko w wybranych regionach.

## <a name="personas"></a>Osoby

Dokumentacja Centrum IoT odnosi się do czterech personas, które wchodzą w interakcje z aplikacją IoT Central:

- _Konstruktor rozwiązań_ jest odpowiedzialny za definiowanie typów urządzeń, które łączą się z aplikacją i dostosowywanie aplikacji dla operatora.
- _Operator_ zarządza urządzeniami połączonymi z aplikacją.
- _Administrator_ jest odpowiedzialny za zadania administracyjne, takie jak zarządzanie [rolami użytkowników i uprawnieniami](howto-administer.md) w aplikacji.
- _Deweloper urządzeń_ tworzy kod, który działa na urządzeniu lub modułu usługi IoT Edge podłączonego do aplikacji.

## <a name="create-your-iot-central-application"></a>Tworzenie aplikacji IoT Central

Jako konstruktor rozwiązań używasz usługi IoT Central do tworzenia niestandardowego rozwiązania IoT hostowanego w chmurze dla twojej organizacji. Niestandardowe rozwiązanie IoT zwykle obejmuje następujące elementy:

- Aplikacja oparta na chmurze, która odbiera dane telemetryczne z urządzeń i umożliwia zarządzanie tymi urządzeniami.
- Wiele urządzeń z uruchomionym niestandardowym kodem połączonych z aplikacją opartą na chmurze.

Możesz szybko wdrożyć nową aplikację IoT Central, a następnie dostosować ją do określonych wymagań w przeglądarce. Możesz zacząć od ogólnego _szablonu aplikacji_ lub jednego z szablonów aplikacji branżowych dla [handlu detalicznego,](../retail/overview-iot-central-retail.md) [energii,](../energy/overview-iot-central-energy.md) [instytucji rządowych](../government/overview-iot-central-government.md)lub [opieki zdrowotnej.](../healthcare/overview-iot-central-healthcare.md)

Jako konstruktor rozwiązań używasz narzędzi internetowych do tworzenia _szablonu urządzenia_ dla urządzeń, które łączą się z aplikacją. Szablon urządzenia to plan, który definiuje cechy i zachowanie typu urządzenia, takiego jak:

- Telemetria wysyła.
- Właściwości biznesowe, które może modyfikować operator.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji.
- Właściwości, które ustawia operator, które określają zachowanie urządzenia.

Ten szablon urządzenia zawiera:

- _Model możliwości urządzenia,_ który opisuje możliwości urządzenia należy zaimplementować, takie jak dane telemetryczne, które wysyła i właściwości, które zgłasza.
- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

### <a name="pricing"></a>Cennik

Aplikację IoT Central można utworzyć przy użyciu 7-dniowej bezpłatnej wersji próbnej lub użyć standardowego planu cenowego.

- Aplikacje utworzone za pomocą *bezpłatnego* planu są bezpłatne przez siedem dni i obsługują do pięciu urządzeń. Możesz przekonwertować je na standardowy plan cenowy w dowolnym momencie przed ich wygaśnięciem.
- Aplikacje utworzone przy użyciu *planu standardowego* są rozliczane na podstawie jednego urządzenia, można wybrać plan cenowy **Standard 1** lub **Standard 2,** przy czym dwa pierwsze urządzenia są bezpłatne. Dowiedz się więcej o bezpłatnych i standardowych planach cenowych na stronie cennik Azure [IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Tworzenie szablonów urządzeń

[IoT Plug and Play (wersja zapoznawcza)](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrowanie urządzeń bez pisania kodu osadzonego urządzenia. Sednem IoT Plug and Play (wersja zapoznawcza) jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzenia. W aplikacji IoT Central szablony urządzeń używają tych modeli urządzeń typu IoT Plug and Play (podgląd).

Jako konstruktor rozwiązań masz kilka opcji tworzenia szablonów urządzeń:

- Zaimportuj model możliwości urządzenia z [katalogu urządzeń certyfikatu platformy Azure dla IoT,](https://aka.ms/iotdevcat) a następnie dodaj wszystkie właściwości chmury, dostosowania i pulpity nawigacyjne, którego potrzebuje aplikacja IoT Central.
- Zaprojektuj szablon urządzenia w u administratorze IoT Central, a następnie zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Tworzenie modelu możliwości urządzenia przy użyciu kodu programu Visual Studio i publikowanie modelu w repozytorium. Zaimplementuj kod urządzenia z modelu i podłącz urządzenie do aplikacji IoT Central. IoT Central znajduje model możliwości urządzenia z repozytorium i tworzy prosty szablon urządzenia dla Ciebie.
- Tworzenie modelu możliwości urządzenia przy użyciu kodu programu Visual Studio. Zaimplementuj kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie dodaj wszystkie właściwości chmury, dostosowania i pulpity nawigacyjne, którego potrzebuje aplikacja IoT Central.

Jako konstruktor rozwiązań możesz użyć IoT Central do generowania kodu dla urządzeń testowych w celu sprawdzania poprawności szablonów urządzeń.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Jako konstruktor rozwiązań można również dostosować interfejs użytkownika aplikacji IoT Central dla operatorów, którzy są odpowiedzialni za codzienne korzystanie z aplikacji. Dostosowania, które może wprowadzić konstruktor rozwiązań, obejmują:

- Definiowanie układu właściwości i ustawień w szablonie urządzenia.
- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.

## <a name="pricing"></a>Cennik

Aplikację IoT Central można utworzyć przy użyciu 7-dniowej bezpłatnej wersji próbnej lub użyć standardowego planu cenowego.

- Aplikacje utworzone za pomocą *bezpłatnego* planu są bezpłatne przez siedem dni i obsługują do pięciu urządzeń. Możesz przekonwertować je na standardowy plan cenowy w dowolnym momencie przed ich wygaśnięciem.
- Aplikacje utworzone przy użyciu planu *standardowego* są rozliczane na podstawie jednego urządzenia, można wybrać plan cenowy **Standard 1** lub **Standard 2,** przy czym dwa pierwsze urządzenia są bezpłatne. Dowiedz się więcej o [cenach IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Łączenie urządzeń

Usługa Azure IoT Central używa [usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) do zarządzania całą rejestracją i połączeniem urządzeń.

Korzystanie z DPS umożliwia:

- IoT Central do obsługi urządzeń dołączających i łączących na dużą skalę.
- Aby wygenerować poświadczenia urządzenia i skonfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pośrednictwem interfejsu użytkownika ioT Central.
- Urządzenia do łączenia się przy użyciu podpisów dostępu współdzielonego.
- Urządzenia do łączenia się przy użyciu standardowych certyfikatów X.509.
- Aby używać własnych identyfikatorów urządzeń do rejestrowania urządzeń w centrum IoT. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden, spójny sposób łączenia urządzeń z IoT Central.

Aby dowiedzieć się więcej, zobacz [Nawiązywać połączenia z usługą Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Urządzenia usługi Azure IoT Edge

Oprócz urządzeń utworzonych przy użyciu [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks)można również połączyć [urządzenia usługi Azure IoT Edge](../../iot-edge/about-iot-edge.md) z aplikacją IoT Central. Usługa Azure IoT Edge umożliwia uruchamianie analizy chmury i niestandardowej logiki bezpośrednio na urządzeniach IoT zarządzanych przez usługę IoT Central. Środowisko wykonawcze IoT Edge umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

Aby uzyskać więcej informacji, zobacz [urządzenia usługi Azure IoT Edge i usługa IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Zachowanie łączności

Aplikacje IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza obciążenie administracyjne związane z zarządzaniem aplikacjami.

Jako operator używasz aplikacji IoT Central do zarządzania urządzeniami w rozwiązaniu IoT Central. Operatorzy wykonują zadania takie jak:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Jako konstruktor rozwiązań można zdefiniować niestandardowe reguły i akcje, które działają za pomocą przesyłania strumieniowego danych z podłączonych urządzeń. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

Administratorzy zarządzają dostępem do aplikacji za pomocą [ról i uprawnień użytkowników](howto-administer.md).

W przypadku każdego rozwiązania IoT zaprojektowanego do pracy na dużą skalę ważne jest ustrukturyzowane podejście do zarządzania urządzeniami. Nie wystarczy tylko podłączyć urządzenia do chmury, musisz utrzymać łączność i zdrowie urządzeń. Operator może korzystać z następujących funkcji IoT Central do zarządzania urządzeniami w całym cyklu życia aplikacji:

### <a name="dashboards"></a>Pulpity nawigacyjne 

Wbudowane [pulpity nawigacyjne](./howto-set-up-template.md#generate-default-views) zapewniają konfigurowalny interfejs użytkownika do monitorowania kondycji urządzenia i telemetrii. Zacznij od wstępnie utworzonego pulpitu nawigacyjnego w [szablonie aplikacji](howto-use-app-templates.md) lub utwórz własne pulpity nawigacyjne dostosowane do potrzeb operatorów. Pulpity nawigacyjne można udostępniać wszystkim użytkownikom w aplikacji lub zachować ich prywatność.

### <a name="rules-and-actions"></a>Reguły i akcje 

Twórz [reguły niestandardowe](tutorial-create-telemetry-rules.md) na podstawie stanu urządzenia i danych telemetrycznych w celu identyfikowania urządzeń wymagających uwagi. Skonfiguruj działania, aby powiadomić właściwe osoby i zapewnić podjęcie środków naprawczych w odpowiednim czasie.

### <a name="jobs"></a>Stanowiska 

[Zadania](howto-run-a-job.md) umożliwiają stosowanie aktualizacji pojedynczych lub zbiorczych do urządzeń przez ustawienie właściwości lub poleceń wywołujących. 

### <a name="user-roles-and-permissions"></a>Role i uprawnienia użytkowników

[Role i uprawnienia](howto-manage-users-roles.md) umożliwiają administratorowi dostosowanie środowiska każdego użytkownika. Administrator używa interfejsu użytkownika sieci Web do tworzenia ról i przypisywania uprawnień.

## <a name="transform-your-iot-data"></a>Przekształcanie danych IoT

Jako platforma aplikacji, IoT Central umożliwia przekształcenie danych IoT w informacje biznesowe, które napędzają zaskarżalne wyniki. [Reguły,](./tutorial-create-telemetry-rules.md) [eksport danych](./howto-export-data.md)i [publiczny interfejs API REST](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) to przykłady integracji usługi IoT Central z aplikacjami biznesowymi:

![Jak usługa IoT Central może przekształcać dane IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorowanie kondycji i operacji urządzenia przy użyciu reguł

Gdy urządzenia są podłączone i wysyłają dane, reguły mogą identyfikować urządzenia, na których występują problemy lub wysyłać komunikaty o błędach, dzięki czemu można je naprawić przy minimalnym przestoju. Tworzenie reguł w aplikacji IoT Central do monitorowania danych telemetrycznych z urządzeń i ostrzegania operatora, gdy metryka przekracza próg lub urządzenie wysyła określony komunikat. Akcje poczty e-mail i elementów webhook dla twoich zasad powiadamiają właściwe osoby i odpowiednie systemy podrzędne.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Uruchamianie analizy niestandardowej i przetwarzania eksportowanych danych

Można generować szczegółowe informacje biznesowe, takie jak określanie trendów wydajności maszyny lub przewidywanie przyszłego zużycia energii w hali produkcyjnej, tworząc niestandardowe potoki analizy w celu przetwarzania danych telemetrycznych z urządzeń i przechowywania wyników. Skonfiguruj eksport danych w aplikacji IoT Central, aby eksportować dane telemetryczne, zmiany właściwości urządzenia i zmiany szablonu urządzenia do innych usług, w których można analizować, przechowywać i wizualizować dane za pomocą preferowanych narzędzi.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Twórz niestandardowe rozwiązania i integracje IoT za pomocą interfejsów API REST

Twórz rozwiązania IoT, takie jak:

- Aplikacje towarzyszące dla urządzeń mobilnych, które mogą zdalnie ujednać i sterować urządzeniami.
- Niestandardowe integracje, które umożliwiają istniejącym aplikacjom biznesowym interakcję z urządzeniami IoT i danymi.
- Aplikacje do zarządzania urządzeniami do modelowania urządzeń, dołączania, zarządzania i dostępu do danych.

## <a name="quotas"></a>Przydziały

Każda subskrypcja platformy Azure ma domyślne przydziały, które mogą mieć wpływ na zakres rozwiązania IoT. Obecnie usługa IoT Central ogranicza liczbę aplikacji, które można wdrożyć w ramach subskrypcji, do 10. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd IoT Central, oto kilka sugerowanych następnych kroków:

- Poznaj dostępne [technologie i usługi platformy Azure do tworzenia rozwiązań IoT.](../../iot-fundamentals/iot-services-and-technologies.md)
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
- Dowiedz się więcej o [uszczypnieniu IoT (wersja zapoznawcza)](../../iot-pnp/overview-iot-plug-and-play.md).
- Dowiedz się, jak [połączyć urządzenie usługi Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Dowiedz się więcej o [technologiach i usługach IoT platformy Azure.](../../iot-fundamentals/iot-services-and-technologies.md)

Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w jakiś kod, sugerowanym następnym krokiem jest [utworzenie i połączenie aplikacji klienckiej z aplikacją Azure IoT Central.](./tutorial-connect-device-nodejs.md)
