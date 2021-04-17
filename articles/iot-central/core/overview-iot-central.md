---
title: Co to jest usługa Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT i pomaga zmniejszyć obciążenie i koszty związane z operacjami i opracowywaniem rozwiązań IoT. Ten artykuł zawiera omówienie funkcji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: abf5f601bb98ab7e03b25f6ab9bd06cb051a1112
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589072"
---
# <a name="what-is-azure-iot-central"></a>Co to jest usługa Azure IoT Central?

IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty tworzenia i utrzymywania rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Wybranie opcji tworzenia za pomocą usługi IoT Central daje możliwość skoncentrowania czasu, pieniędzy i energii na przekształcaniu firmy przy użyciu danych IoT, a nie tylko na konserwacji i aktualizowaniu złożonej i stale zmieniającej się infrastruktury IoT.

Internetowy interfejs użytkownika umożliwia szybkie łączenie urządzeń, monitorowanie warunków urządzeń, tworzenie reguł oraz zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Ponadto umożliwia ona korzystanie ze szczegółowych informacji o urządzeniach przez rozszerzanie analizy IoT na aplikacje biznesowe.

Ten artykuł zawiera opis IoT Central:

- Typowe role użytkownika skojarzone z projektem.
- Tworzenie aplikacji
- Łączenie urządzeń z aplikacją
- Zarządzanie aplikacją
- Azure IoT Edge możliwości w IoT Central.
- Jak połączyć urządzenia Azure IoT Edge środowiska uruchomieniowego z aplikacją.

## <a name="user-roles"></a>Role użytkownika

Dokumentacja IoT Central odnosi się do czterech ról użytkownika, które wchodzą w interakcję z IoT Central aplikacji:

- Konstruktor _rozwiązań jest_ odpowiedzialny za tworzenie aplikacji, konfigurowanie reguł i akcji, definiowanie integracji z innymi usługami oraz dalsze dostosowywanie aplikacji dla operatorów i deweloperów urządzeń. [](quick-deploy-iot-central.md) [](quick-configure-rules.md) [](howto-export-data.md)
- Operator _zarządza_ [urządzeniami połączonymi](howto-manage-devices.md) z aplikacją.
- Administrator _jest_ odpowiedzialny za zadania administracyjne, takie jak zarządzanie [rolami użytkowników i uprawnieniami](howto-administer.md) w aplikacji.
- Deweloper _urządzenia tworzy_ [kod uruchamiany na](concepts-telemetry-properties-commands.md) urządzeniu lub w module [IoT Edge połączonym](concepts-iot-edge.md) z aplikacją.

## <a name="create-your-iot-central-application"></a>Tworzenie aplikacji IoT Central aplikacji

Możesz szybko wdrożyć nową aplikację IoT Central, a następnie dostosować ją do określonych wymagań. Rozpocznij od ogólnego szablonu _aplikacji lub_ jednego z branżowych szablonów aplikacji dla branży sprzedaży [detalicznej,](../retail/overview-iot-central-retail.md)energetycznej, rządowej lub opieki [zdrowotnej.](../healthcare/overview-iot-central-healthcare.md) [](../energy/overview-iot-central-energy.md) [](../government/overview-iot-central-government.md)

Zobacz przewodnik Szybki start Tworzenie [nowej aplikacji,](quick-deploy-iot-central.md) aby dowiedzieć się, jak utworzyć pierwszą aplikację.

## <a name="connect-devices"></a>Łączenie urządzeń

Po utworzeniu aplikacji pierwszym krokiem jest utworzenie połączenia urządzeń. Każde urządzenie połączone z IoT Central korzysta z _szablonu urządzenia_. Szablon urządzenia to strategia definiująca charakterystykę i zachowanie typu urządzenia, takiego jak:

- Dane telemetryczne, które wysyła. Przykłady obejmują temperaturę i wilgotność. Telemetria to dane przesyłane strumieniowo.
- Właściwości biznesowe, które może modyfikować operator. Przykłady obejmują adres klienta i datę ostatniego serwisowania.
- Właściwości urządzenia ustawiane przez urządzenie, które są tylko do odczytu w aplikacji. Na przykład stan komputera jako otwartego lub zamkniętego.
- Właściwości ustawiane przez operatora, które określają zachowanie urządzenia. Na przykład temperatura docelowa urządzenia.
- Polecenia, które operator może wywołać, które są uruchamiane na urządzeniu. Na przykład polecenie zdalnego ponownego uruchomienia urządzenia.

Każdy [szablon urządzenia zawiera:](howto-set-up-template.md)

- Model _urządzenia opisujący_ możliwości, które urządzenie powinno zaimplementować. Możliwości urządzenia obejmują:

  - Dane telemetryczne są przesyłane strumieniowo do IoT Central.
  - Właściwości tylko do odczytu używane do zgłaszania stanu do IoT Central.
  - Właściwości z zapisywalnym dostępem, które otrzymuje od IoT Central, aby ustawić stan urządzenia.
  - Polecenia wywoływane z IoT Central.

- Właściwości chmury, które nie są przechowywane na urządzeniu.
- Dostosowania, pulpity nawigacyjne i formularze, które są częścią IoT Central aplikacji.

Dostępnych jest kilka opcji tworzenia szablonów urządzeń:

- Zaprojektuj szablon urządzenia w IoT Central a następnie zaim implementuj jego model urządzenia w kodzie urządzenia.
- Utwórz model urządzenia przy użyciu Visual Studio i opublikuj model w repozytorium. Zaimplikuj kod urządzenia z modelu i połącz urządzenie z IoT Central aplikacją. IoT Central model urządzenia z repozytorium i tworzy dla Ciebie prosty szablon urządzenia.
- Utwórz model urządzenia przy użyciu Visual Studio kodu. Zaim implementuj kod urządzenia z modelu. Ręcznie zaimportuj model urządzenia do aplikacji IoT Central, a następnie dodaj wszelkie właściwości chmury, dostosowania i pulpity nawigacyjne, IoT Central potrzeb aplikacji.

Zobacz przewodnik Szybki start Dodawanie [symulowanego urządzenia,](quick-create-simulated-device.md) aby uzyskać szczegółowe informacje na temat tworzenia i łączenia pierwszego urządzenia.

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Możesz również dostosować interfejs IoT Central użytkownika aplikacji dla operatorów, którzy są odpowiedzialni za korzystanie z aplikacji na co dzień. Dostosowania, które można wprowadzić, obejmują:

- Konfigurowanie niestandardowych pulpitów nawigacyjnych ułatwiających operatorom odnajdywanie szczegółowych informacji i szybsze rozwiązywanie problemów.
- Konfigurowanie niestandardowych analiz w celu eksplorowania danych serii czasu na połączonych urządzeniach.
- Definiowanie układu właściwości i ustawień w szablonie urządzenia.

## <a name="manage-your-devices"></a>Zarządzanie urządzeniami

Operator używa aplikacji IoT Central do zarządzania urządzeniami [w](howto-manage-devices.md) IoT Central rozwiązania. Operatorzy wykonywać zadania, takie jak:

- Monitorowanie urządzeń połączonych z aplikacją
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizowanie nowych urządzeń

Można zdefiniować [niestandardowe reguły i akcje, które działają na](howto-configure-rules.md) strumieniu danych z połączonych urządzeń. Operator może włączyć lub wyłączyć te reguły na poziomie urządzenia, aby kontrolować i automatyzować zadania w aplikacji.

W przypadku każdego rozwiązania IoT zaprojektowanego do działania na dużą skalę ważne jest ustrukturyzowane podejście do zarządzania urządzeniami. Nie wystarczy tylko połączyć urządzenia z chmurą, ale też zachować ich połączenie i łączyć je w dobrej kondycji. Użyj następujących IoT Central, aby zarządzać urządzeniami w całym cyklu życia aplikacji:

### <a name="dashboards"></a>Pulpity nawigacyjne

Wbudowane pulpity [nawigacyjne zapewniają](./howto-set-up-template.md#generate-default-views) dostosowywalny interfejs użytkownika do monitorowania kondycji i telemetrii urządzenia. Rozpocznij od wstępnie sbudowaną pulpitu nawigacyjnego w [szablonie](howto-use-app-templates.md) aplikacji lub utwórz własne pulpity nawigacyjne dostosowane do potrzeb operatorów. Pulpity nawigacyjne można udostępniać wszystkim użytkownikom w aplikacji lub zachować ich prywatnie.

### <a name="rules-and-actions"></a>Reguły i akcje

Twórz [niestandardowe reguły oparte](tutorial-create-telemetry-rules.md) na stanie urządzenia i telemetrii, aby identyfikować urządzenia, które wymagają uwagi. Skonfiguruj akcje w celu powiadamiania odpowiednich osób i zapewnienia terminowego działania naprawczego.

### <a name="jobs"></a>Stanowiska

[Zadania](howto-run-a-job.md) umożliwiają stosowanie aktualizacji pojedynczych lub zbiorczych na urządzeniach przez ustawienie właściwości lub wywołanie poleceń.

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Platforma aplikacji umożliwia IoT Central danych IoT na szczegółowe informacje biznesowe, które wpływają na wyniki z możliwością działania. [Reguły,](./tutorial-create-telemetry-rules.md) [eksport danych](./howto-export-data.md)i publiczny [interfejs API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) to przykłady integracji IoT Central z aplikacjami biznesowymi:

![Jak IoT Central przekształcić dane IoT](media/overview-iot-central/transform.png)

Możesz generować szczegółowe informacje biznesowe, takie jak określanie trendów wydajności maszynowej lub przewidywanie przyszłego zużycia energii w fabryce, przez tworzenie niestandardowych potoków analitycznych do przetwarzania danych telemetrycznych z urządzeń i przechowywania wyników. Skonfiguruj eksporty danych w aplikacji IoT Central, aby eksportować dane telemetryczne, zmiany właściwości urządzenia i zmiany szablonów urządzeń do innych usług, w których można analizować, przechowywać i wizualizować dane za pomocą preferowanych narzędzi.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Tworzenie niestandardowych rozwiązań IoT i integracji z interfejsami API REST

Tworzenie rozwiązań IoT, takich jak:

- Mobilne aplikacje towarzyszące, które mogą zdalnie skonfigurować i kontrolować urządzenia.
- Niestandardowe integracje, które umożliwiają istniejącym aplikacjom biznesowym interakcję z urządzeniami i danymi IoT.
- Aplikacje do zarządzania urządzeniami do modelowania urządzeń, dołączania, zarządzania i dostępu do danych.

## <a name="administer-your-application"></a>Administrowanie aplikacją

IoT Central są w pełni hostowane przez firmę Microsoft, co zmniejsza obciążenie administracyjne związane z zarządzaniem aplikacjami. Administratorzy zarządzają dostępem do aplikacji przy [użyciu ról użytkownika i uprawnień](howto-administer.md).

## <a name="pricing"></a>Ceny

Możesz utworzyć aplikację IoT Central korzystającą z 7-dniowej bezpłatnej wersji próbnej lub użyć standardowego planu cenowego.

- Aplikacje, które tworzysz przy użyciu *planu bezpłatnego,* są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. Można je przekonwertować w celu korzystania ze standardowego planu cenowego w dowolnym momencie, zanim wygasną.
- Aplikacje, które  tworzysz przy użyciu planu standardowego, są rozliczane według urządzeń. Możesz wybrać plan cenowy **Standard 0,** **Standard 1** lub **Standard 2** z dwoma pierwszymi urządzeniami, które są bezpłatne. Dowiedz się więcej o [IoT Central cen.](https://aka.ms/iotcentral-pricing)

## <a name="quotas"></a>Przydziały

Każda subskrypcja platformy Azure ma domyślne limity przydziału, które mogą mieć wpływ na zakres rozwiązania IoT. Obecnie IoT Central ogranicza liczbę aplikacji, które można wdrożyć w ramach subskrypcji, do 10. Jeśli chcesz zwiększyć ten limit, skontaktuj się z pomocą [techniczną firmy Microsoft.](https://azure.microsoft.com/support/options/)

## <a name="known-issues"></a>Znane problemy

- Ciągły eksport danych nie obsługuje formatu Avro (niezgodność).
- Dane GeoJSON nie są obecnie obsługiwane.
- Kafelek mapy nie jest obecnie obsługiwany.
- Typy schematów tablic nie są obsługiwane.
- Obsługiwane są tylko zestawy SDK urządzenia Node.js C i zestawy SDK usługi.
- IoT Central jest obecnie dostępna w lokalizacjach w Stany Zjednoczone, Europie, Azja i Pacyfik, Australii, Zjednoczonym Królestwie i Japonii.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już przegląd IoT Central, poniżej przedstawiono kilka sugerowanych następnych kroków:

- Jeśli jesteś deweloperem urządzeń i chcesz zagłębić się w kod, sugerowanym następnym krokiem jest utworzenie aplikacji klienckiej i połączenie jej z aplikacją Azure IoT Central [aplikacji.](./tutorial-connect-device.md)
- Zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md).
- Rozpoczęcie pracy przez [utworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md).
