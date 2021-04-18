---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: IoT Central to hostowana platforma aplikacji IoT, która jest bezpieczna, skalowana wraz ze wzrostem firmy i integrowana z istniejącymi aplikacjami biznesowymi. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600446"
---
# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

IoT Central to bezpieczna platforma aplikacji IoT, która jest bezpieczna, skalowana wraz ze wzrostem firmy i integrowana z istniejącymi aplikacjami biznesowymi. Wybranie opcji tworzenia za pomocą usługi IoT Central daje możliwość skoncentrowania czasu, pieniędzy i energii na przekształcaniu firmy przy użyciu danych IoT, a nie tylko na utrzymywaniu i aktualizowaniu złożonej i stale zmieniającej się infrastruktury IoT.

IoT Central umożliwia szybkie łączenie urządzeń, monitorowanie warunków urządzeń, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danymi w całym cyklu życia. Ponadto umożliwia ona korzystanie ze szczegółowych informacji o urządzeniach przez rozszerzanie analizy IoT na aplikacje biznesowe.

## <a name="create-your-iot-central-application"></a>Tworzenie aplikacji IoT Central aplikacji

Możesz szybko utworzyć nową aplikację IoT Central, a następnie dostosować ją do swoich unikatowych wymagań. Możesz rozpocząć od  ogólnego szablonu aplikacji lub jednego z branżowych szablonów aplikacji dla branży sprzedaży [detalicznej,](../retail/overview-iot-central-retail.md) [](../energy/overview-iot-central-energy.md)energetycznej, dla instytucji rządowych lub opieki [zdrowotnej.](../healthcare/overview-iot-central-healthcare.md) [](../government/overview-iot-central-government.md)

Zapoznaj się [z przewodnikem Szybki](quick-deploy-iot-central.md) start tworzenie nowej aplikacji, aby dowiedzieć się, jak utworzyć pierwszą aplikację.

## <a name="connect-your-devices"></a>Łączenie urządzeń
Po utworzeniu aplikacji pierwszym krokiem jest połączenie urządzeń. Zobacz omówienie [tworzenia urządzeń,](./overview-iot-central-developer.md) aby uzyskać wprowadzenie do łączenia urządzeń z IoT Central aplikacji.

### <a name="device-templates"></a>Szablony urządzeń

Urządzenia w IoT Central są skojarzone z _szablonem urządzenia._ Szablon urządzenia jest jak strategia: definiuje charakterystyki i zachowania urządzeń, takie jak:

- Telemetrie, które reprezentują pomiary z czujników, na przykład temperatury lub wilgotności.
- Właściwości, które reprezentują trwały stan urządzenia. Przykłady obejmują stan pompy chłodzcy lub temperaturę docelową urządzenia. Właściwości można zadeklarować jako tylko do odczytu lub zapisywalne. Tylko urządzenia mogą aktualizować wartość właściwości tylko do odczytu. Operator może ustawić wartość właściwości zapisywalnej do wysłania do urządzenia.
- Polecenia, operacje, które mogą być wyzwalane na urządzeniu, na przykład polecenie umożliwiające zdalne ponowne uruchomienie urządzenia.
- Właściwości chmury, które są metadanymi urządzenia do przechowywania w IoT Central, na przykład adres klienta lub data ostatniego serwisowania.

Zobacz artykuł [tworzenie szablonu urządzenia,](howto-set-up-template.md) aby dowiedzieć się więcej.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Możesz dostosować aplikację IoT Central dla operatorów, którzy są odpowiedzialni za korzystanie z aplikacji na co dzień, na przykład:

- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.


## <a name="manage-your-devices"></a>Zarządzanie urządzeniami


W przypadku każdego rozwiązania IoT zaprojektowanego do działania na dużą skalę ważne jest ustrukturyzowane podejście do zarządzania urządzeniami. Nie wystarczy tylko połączyć urządzenia z chmurą, ale też zachować ich związek i łączyć je w dobrej kondycji.

Urządzeniami można [zarządzać przy](howto-manage-devices.md) użyciu aplikacji IoT Central do wykonywania zadań, takich jak:

- Monitorowanie urządzeń.
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Przeprowadzanie aktualizacji zbiorczych na urządzeniach.

### <a name="dashboards"></a>Pulpity nawigacyjne

Wbudowane pulpity [nawigacyjne zapewniają](./howto-set-up-template.md#generate-default-views) dostosowywalny interfejs użytkownika do monitorowania kondycji i telemetrii urządzenia. Rozpocznij od wstępnie sbudowaną pulpitu nawigacyjnego w [szablonie](howto-use-app-templates.md) aplikacji lub utwórz własne pulpity nawigacyjne dostosowane do potrzeb operatorów. Pulpity nawigacyjne można udostępniać wszystkim użytkownikom w aplikacji lub zachować ich prywatnie.

### <a name="rules-and-actions"></a>Reguły i akcje

Twórz [niestandardowe reguły oparte](tutorial-create-telemetry-rules.md) na stanie urządzenia i telemetrii, aby identyfikować urządzenia, które wymagają uwagi. Skonfiguruj akcje w celu powiadamiania odpowiednich osób i zapewnienia terminowego działania naprawczego.

### <a name="jobs"></a>Stanowiska

[Zadania](howto-run-a-job.md) umożliwiają stosowanie aktualizacji pojedynczych lub zbiorczych na urządzeniach przez ustawienie właściwości lub wywołanie poleceń.

### <a name="analytics"></a>Analiza
[Analiza](howto-create-analytics.md) udostępnia zaawansowane możliwości analizowania trendów historycznych i korelowania różnych telemetrii z urządzeń.

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Platforma aplikacji umożliwia IoT Central danych IoT na szczegółowe informacje biznesowe, które wpływają na wyniki z możliwością działania. [Reguły,](./tutorial-create-telemetry-rules.md) [eksport danych](./howto-export-data.md)i publiczny [interfejs API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) to przykłady integracji IoT Central z aplikacjami biznesowymi:

![Jak IoT Central przekształcić dane IoT](media/overview-iot-central/transform.png)

Możesz generować szczegółowe informacje biznesowe, takie jak określanie trendów wydajności maszynowej lub przewidywanie przyszłego zużycia energii w fabryce, przez tworzenie niestandardowych potoków analitycznych do przetwarzania danych telemetrycznych z urządzeń i przechowywania wyników. Skonfiguruj eksporty danych w aplikacji IoT Central, aby eksportować dane do innych usług, w których można je analizować, przechowywać i wizualizować za pomocą preferowanych narzędzi.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Tworzenie niestandardowych rozwiązań IoT i integracji z interfejsami API REST

Tworzenie rozwiązań IoT, takich jak:

- Mobilne aplikacje towarzyszące, które mogą zdalnie skonfigurować i kontrolować urządzenia.
- Niestandardowe integracje, które umożliwiają istniejącym aplikacjom biznesowym interakcję z urządzeniami i danymi IoT.
- Aplikacje do zarządzania urządzeniami do modelowania urządzeń, dołączania, zarządzania i dostępu do danych.

## <a name="administer-your-application"></a>Administrowanie aplikacją

IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza obciążenie administracyjne związane z zarządzaniem aplikacjami. Administratorzy zarządzają dostępem do aplikacji przy [użyciu ról użytkownika i uprawnień.](howto-administer.md)

## <a name="pricing"></a>Ceny

Możesz utworzyć aplikację IoT Central korzystającą z 7-dniowej bezpłatnej wersji próbnej lub skorzystać ze standardowego planu cenowego.

- Aplikacje, które tworzysz przy użyciu *planu bezpłatnego,* są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Można je przekonwertować w celu korzystania ze standardowego planu cenowego w dowolnym momencie, zanim wygasną.
- Aplikacje, które tworzysz przy użyciu *planu standardowego,* są rozliczane według urządzeń. Możesz wybrać plan **cenowy Standard 0,** **Standard 1** lub **Standard 2** z dwoma pierwszymi urządzeniami, które są bezpłatne. Dowiedz się więcej o [IoT Central cen.](https://aka.ms/iotcentral-pricing)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już przegląd IoT Central, poniżej przedstawiono kilka sugerowanych następnych kroków:

- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w kod, utwórz aplikację kliencyjną i połącz ją z Azure IoT Central [aplikacją.](./tutorial-connect-device.md)
