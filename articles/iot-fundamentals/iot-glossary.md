---
title: Słownik terminów dotyczących usługi Azure IoT | Microsoft Docs
description: Przewodnik dla deweloperów — słownik objaśniający niektóre typowe terminy używane w artykułach IoT platformy Azure.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 3e8a2ac93e9fea6ad045030759be894617557658
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022118"
---
# <a name="glossary-of-iot-terms"></a>Słownik terminów IoT

W tym artykule wymieniono niektóre typowe terminy używane w artykułach IoT.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Protokół zaawansowanej kolejki komunikatów

[Protokół Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) to jeden z protokołów obsługi komunikatów, które [IoT Hub](#iot-hub) obsługiwać komunikację z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów obsługiwanych przez IoT Hub, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="automatic-device-management"></a>Automatyczne zarządzanie urządzeniami

Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń w całym cyklu ich życia. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestawem urządzeń na podstawie ich właściwości, definiować żądaną konfigurację i zezwalać IoT Hub na aktualizowanie urządzeń, gdy zostaną one objęte zakresem.  Składa się z [automatycznych konfiguracji urządzeń](../iot-hub/iot-hub-automatic-device-management.md) i [IoT Edge wdrożeń automatycznych](../iot-edge/how-to-deploy-at-scale.md).

### <a name="automatic-device-configuration"></a>Automatyczna konfiguracja urządzeń

Zaplecze rozwiązania może używać [automatycznych konfiguracji urządzeń](../iot-hub/iot-hub-automatic-device-management.md) do przypisywania odpowiednich właściwości do zestawu [bliźniaczych reprezentacji urządzeń](#device-twin) i stanu raportu przy użyciu metryk systemu i metryk niestandardowych. 

### <a name="azure-iot-device-sdks"></a>Zestawy SDK urządzeń usługi Azure IoT

Dostępne są _zestawy SDK urządzeń_ dla wielu języków, które umożliwiają tworzenie [aplikacji dla urządzeń](#device-app) , które współpracują z Centrum IoT. Samouczki IoT Hub pokazują, jak korzystać z tych zestawów SDK urządzeń. Kod źródłowy i dodatkowe informacje o zestawach SDK urządzeń można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub.

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

[Eksplorator usługi Azure IoT](https://github.com/Azure/azure-iot-explorer) służy do wyświetlania danych telemetrycznych wysyłanych przez urządzenie, pracy z właściwościami urządzenia i wywoływania poleceń. Można także użyć Eksploratora do współpracy z urządzeniami i ich testowania oraz do zarządzania [urządzeniami Plug and Play IoT](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>Zestawy SDK usługi Azure IoT

Istnieją _zestawy SDK usługi_ dostępne dla wielu języków, które umożliwiają tworzenie [aplikacji zaplecza](#back-end-app) , które współpracują z Centrum IoT. Samouczki IoT Hub pokazują, jak korzystać z tych zestawów SDK usługi. Kod źródłowy i dodatkowe informacje o zestawach SDK usług można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub.

### <a name="azure-iot-tools"></a>Narzędzia usługi Azure IoT

[Narzędzia Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) to międzyplatformowe rozszerzenie Visual Studio Code typu open-source, które ułatwia zarządzanie usługą Azure IoT Hub i urządzeniami w vs Code. Korzystając z narzędzi Azure IoT Tools, deweloperzy IoT mogą łatwo opracowywać projekt IoT w VS Code.

## <a name="b"></a>B

### <a name="back-end-app"></a>Aplikacja zaplecza

W kontekście [IoT Hub](#iot-hub)aplikacja zaplecza jest aplikacją, która łączy się z jednym z punktów końcowych mających dostęp do usługi w usłudze IoT Hub. Na przykład aplikacja zaplecza może pobrać komunikaty [z urządzenia do chmury](#device-to-cloud) lub zarządzać [rejestrem tożsamości](#identity-registry). Zazwyczaj aplikacja zaplecza działa w chmurze, ale w wielu samouczkach aplikacje zaplecza są aplikacjami konsolowymi uruchomionymi na lokalnym komputerze deweloperskim.

### <a name="built-in-endpoints"></a>Wbudowane punkty końcowe

Każde Centrum IoT Hub zawiera wbudowany [punkt końcowy](../iot-hub/iot-hub-devguide-endpoints.md) , który jest zgodny z centrum zdarzeń. Do odczytywania komunikatów z urządzenia do chmury z tego punktu końcowego można użyć dowolnego mechanizmu, który współpracuje z Event Hubs.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Brama chmury

Brama w chmurze umożliwia łączność urządzeń, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Brama w chmurze jest hostowana w chmurze w przeciwieństwie do [bramy pola](#field-gateway) działającej lokalnie na urządzeniach. Typowym przypadkiem użycia dla bramy w chmurze jest zaimplementowanie translacji protokołów dla urządzeń.

### <a name="cloud-to-device"></a>Z chmury do urządzenia

Odnosi się do komunikatów wysyłanych z usługi IoT Hub do podłączonego urządzenia. Często te komunikaty są poleceniami, które nakazują urządzeniu wykonanie akcji. Aby uzyskać więcej informacji, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Polecenia

W Plug and Play IoT polecenia zdefiniowane w [interfejsie](#interface) reprezentują metody, które mogą być wykonywane na [dwucyfrowej](#digital-twin)stawce. Na przykład polecenie umożliwiające ponowne uruchomienie urządzenia.

### <a name="component"></a>Składnik

W Plug and Play IoT składniki umożliwiają skompilowanie [interfejsu](#interface) modelu jako zestawu innych interfejsów. [Model urządzenia](#device-model) może łączyć wiele interfejsów jako składniki. Na przykład model może obejmować składnik przełącznika i składnik termostatu. Wiele składników w modelu może również korzystać z tego samego typu interfejsu. Na przykład model może zawierać dwa składniki termostatu.

### <a name="configuration"></a>Konfigurowanie

W kontekście [automatycznej konfiguracji urządzeń](../iot-hub/iot-hub-automatic-device-management.md)konfiguracja w ramach IoT Hub definiuje żądaną konfigurację zestawu urządzeń bliźniaczych reprezentacji i zawiera zestaw metryk do raportowania stanu i postępu.

### <a name="connection-string"></a>Parametry połączenia

Parametry połączenia w kodzie aplikacji są używane do hermetyzacji informacji wymaganych do nawiązania połączenia z punktem końcowym. Parametry połączenia zwykle obejmują adres punktu końcowego i informacje o zabezpieczeniach, ale formaty parametrów połączenia różnią się w zależności od usług. Istnieją dwa typy parametrów połączenia skojarzone z usługą IoT Hub:

- *Parametry połączenia urządzeń* umożliwiają urządzeniom łączenie się z punktami końcowymi dostępnymi dla urządzeń w centrum IoT.

- *IoT Hub parametry połączenia* umożliwiają aplikacjom zaplecza łączenie się z punktami końcowymi dostępnymi do usługi w usłudze IoT Hub.

### <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Można utworzyć niestandardowe [punkty końcowe](../iot-hub/iot-hub-devguide-endpoints.md) w usłudze IoT Hub w celu dostarczenia komunikatów wysłanych przez [regułę routingu](#routing-rules). Niestandardowe punkty końcowe łączą się bezpośrednio z centrum zdarzeń, kolejką Service Bus lub Service Bus tematem.

### <a name="custom-gateway"></a>Brama niestandardowa

Brama umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Za pomocą Azure IoT Edge można tworzyć bramy niestandardowe, które implementują logikę niestandardową w celu obsługi komunikatów, niestandardowych konwersji protokołów i innych operacji przetwarzania na krawędzi.

## <a name="d"></a>D

### <a name="data-point-message"></a>Komunikat punktu danych

Komunikat punktu danych to komunikat [z urządzenia do chmury](#device-to-cloud) , który zawiera dane [telemetryczne](#telemetry) , takie jak szybkość lub temperatura wiatru.

### <a name="default-component"></a>Składnik domyślny

W Plug and Play IoT wszystkie [modele urządzeń](#device-model) mają składnik domyślny. Prosty model urządzenia ma tylko domyślny składnik — taki model jest również znany jako urządzenie składnik. Bardziej skomplikowany model ma wiele składników zagnieżdżonych pod składnikiem domyślnym.

### <a name="device-certification"></a>Certyfikacja urządzenia

Program certyfikacji urządzenia Plug and Play IoT sprawdza, czy urządzenie spełnia wymagania certyfikacji Plug and Play IoT. Można dodać certyfikowane urządzenie do [wykazu urządzeń usługi Azure IoT z certyfikatem](https://aka.ms/devicecatalog)publicznym.

### <a name="device-model"></a>Model urządzenia

Model urządzenia używa [języka definicji Digital bliźniaczych reprezentacji](#digital-twins-definition-language) do opisywania możliwości urządzenia IoT Plug and Play. Prosty model urządzenia używa jednego interfejsu do opisywania możliwości urządzeń. Bardziej skomplikowany model urządzeń zawiera wiele składników, z których każdy opisuje zestaw funkcji. Aby dowiedzieć się więcej, zobacz [składniki Plug and Play IoT w modelach](../iot-pnp/concepts-components.md).

### <a name="device-builder"></a>Konstruktor urządzeń

Konstruktor urządzeń korzysta z [modelu](#device-model) i [interfejsów](#interface) urządzeń podczas implementowania kodu do uruchamiania na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device). Konstruktory urządzeń zazwyczaj korzystają z jednego z [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdks) w celu zaimplementowania klienta urządzenia.

### <a name="device-modeling"></a>Modelowanie urządzeń

[Konstruktor urządzeń](#device-builder) lub [Konstruktor modułów](#module-builder)używa [języka definicji Digital bliźniaczych reprezentacji](#digital-twins-definition-language) do modelowania możliwości [urządzenia Plug and Play IoT](#iot-plug-and-play-device). [Konstruktor rozwiązań](#solution-builder) może skonfigurować rozwiązanie IoT na podstawie modelu.

### <a name="desired-configuration"></a>Wymagana konfiguracja

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md), wymagana konfiguracja odnosi się do kompletnego zestawu właściwości i metadanych w bliźniaczych urządzeniach, które powinny być zsynchronizowane z urządzeniem.

### <a name="desired-properties"></a>Żądane właściwości

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)odpowiednie właściwości są podsekcją sznurka urządzenia, która jest używana z [zgłoszonymi właściwościami](#reported-properties) do synchronizowania konfiguracji lub warunku urządzenia. Żądane właściwości można ustawić tylko przez [aplikację zaplecza](#back-end-app) i są one obserwowane przez [aplikację urządzenia](#device-app).

### <a name="device-to-cloud"></a>Urządzenie-chmura

Odnosi się do komunikatów wysyłanych z podłączonego urządzenia do [IoT Hub](#iot-hub). Mogą to być komunikaty [typu punkt danych](#data-point-message) lub wiadomości [interaktywne](#interactive-message) . Aby uzyskać więcej informacji, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="device"></a>Urządzenie

W kontekście IoT urządzenie jest zazwyczaj niewielką skalowalnym urządzeniem komputerowym, które może zbierać dane lub sterować innymi urządzeniami. Na przykład urządzenie może być urządzeniem monitorującym środowisko lub kontrolerem dla systemów wodociągowych i wentylacyjnych w szklarni. [Katalog urządzeń](https://catalog.azureiotsolutions.com/) zawiera listę urządzeń sprzętowych certyfikowanych do pracy z [IoT Hub](#iot-hub).

### <a name="device-app"></a>Aplikacja urządzenia

Aplikacja urządzenia jest uruchamiana na [urządzeniu](#device) i obsługuje komunikację z [Centrum IoT Hub](#iot-hub). Zazwyczaj podczas implementowania aplikacji urządzenia używasz jednego z [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdks) . W wielu samouczkach IoT można użyć [symulowanego urządzenia](#simulated-device) dla wygody.

### <a name="device-condition"></a>Warunek urządzenia

Odnosi się do informacji o stanie urządzenia, takich jak aktualnie używana metoda łączności, zgłoszona przez [aplikację urządzenia](#device-app). [Aplikacje urządzenia](#device-app) mogą również zgłaszać swoje możliwości. Możesz wykonywać zapytania dotyczące warunków i informacji o możliwościach za pomocą bliźniaczych reprezentacji urządzeń.

### <a name="device-data"></a>Dane urządzenia

Dane urządzenia odnoszą się do danych na urządzeniu przechowywanych w [rejestrze tożsamości](#identity-registry)IoT Hub. Istnieje możliwość zaimportowania i wyeksportowania tych danych.

### <a name="device-identity"></a>Tożsamość urządzenia

Tożsamość urządzenia jest unikatowym identyfikatorem przypisanym do każdego urządzenia zarejestrowanego w [rejestrze tożsamości](#identity-registry).

### <a name="device-management"></a>Zarządzanie urządzeniami

Zarządzanie urządzeniami obejmuje pełny cykl życia związany z zarządzaniem urządzeniami w ramach rozwiązania IoT, w tym planowanie, Inicjowanie obsługi, konfigurowanie, monitorowanie i wycofywanie.

### <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami

[Centrum IoT Hub](#iot-hub) umożliwia korzystanie ze wspólnych wzorców zarządzania urządzeniami, w tym ponownego uruchamiania, resetowania fabryki i przeprowadzania aktualizacji oprogramowania układowego na urządzeniach.

### <a name="device-rest-api"></a>Interfejs API REST urządzenia

Za pomocą [interfejsu API REST urządzenia](/rest/api/iothub/device) można wysyłać komunikaty z urządzenia do chmury do usługi IoT Hub i odbierać komunikaty z [chmury do urządzenia](#cloud-to-device) z Centrum IoT. Zazwyczaj należy użyć jednego z [zestawów SDK urządzeń](#azure-iot-device-sdks) wyższego poziomu, jak pokazano w samouczkach IoT Hub.

### <a name="device-provisioning"></a>Inicjowanie obsługi urządzeń

Inicjowanie obsługi administracyjnej urządzeń polega na dodaniu początkowych [danych urządzenia](#device-data) do sklepów w rozwiązaniu. Aby umożliwić nowym urządzeniu łączenie się z centrum, należy dodać identyfikator i klucze urządzenia do [rejestru tożsamości](#identity-registry)IoT Hub. W ramach procesu aprowizacji może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań.

### <a name="device-twin"></a>Bliźniak urządzenia

Sznurki urządzenia to dokument JSON, który przechowuje informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które zainicjujesz w usłudze IoT Hub. Bliźniaczych reprezentacji urządzenia pozwala synchronizować warunki i konfiguracje urządzeń między urządzeniem a zapleczem rozwiązania. Można zbadać bliźniaczych reprezentacji urządzenia, aby zlokalizować określone urządzenia i dla stanu długotrwałych operacji.

### <a name="direct-method"></a>Metoda bezpośrednia

[Metoda bezpośrednia](../iot-hub/iot-hub-devguide-direct-methods.md) to sposób wyzwalania metody do wykonania na urządzeniu przez wywołanie interfejsu API w centrum IoT.

### <a name="digital-twin"></a>Cyfrowe sznurki

Cyfrowa sznurek jest kolekcją danych cyfrowych, które reprezentują obiekt fizyczny. Zmiany w obiekcie fizycznym są odzwierciedlone w cyfrach cyfrowych. W niektórych scenariuszach można użyć dwucyfrowego przędzy do manipulowania obiektem fizycznym. [Usługa Azure Digital bliźniaczych reprezentacji](../digital-twins/index.yml) korzysta z modeli wyrażonych w [języku definicji Digital bliźniaczych reprezentacji](#digital-twins-definition-language) w celu włączenia szerokiego zakresu rozwiązań opartych na chmurze, które używają Digital bliźniaczych reprezentacji. Urządzenie [Plug and Play IoT](../iot-pnp/index.yml) ma dwuosiową cyfrę, opisaną przez [model urządzenia](#device-model)DTDL.

### <a name="digital-twin-change-events"></a>Zdarzenia zmiany cyfrowej reprezentacji bliźniaczej

Gdy [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jest połączone z Centrum IoT, centrum może korzystać z funkcji routingu do wysyłania powiadomień o zmianach dwuosiowych. Na przykład za każdym razem, gdy wartość [Właściwości](#properties) zostanie zmieniona na urządzeniu, IoT Hub może wysłać powiadomienie do punktu końcowego, takiego jak centrum zdarzeń.

### <a name="digital-twins-definition-language"></a>Digital bliźniaczych reprezentacji Definition Language

Język służący do opisywania modeli i interfejsów dla [urządzeń Plug and Play IoT](#iot-plug-and-play-device). Użyj [języka Digital bliźniaczych reprezentacji Definition Language w wersji 2](https://github.com/Azure/opendigitaltwins-dtdl) do opisywania możliwości [cyfrowych przędzy](#digital-twin) i włączania platformy IoT i rozwiązań IoT do korzystania z semantyki jednostki.

### <a name="digital-twin-route"></a>Trasa Digital bliźniaczy

Trasa skonfigurowana w centrum IoT Hub w celu dostarczania [cyfrowych cyfrowych zdarzeń zmiany](#digital-twin-change-events) do i punktu końcowego, takiego jak centrum zdarzeń.

## <a name="e"></a>E

### <a name="endpoint"></a>Punkt końcowy

Centrum IoT udostępnia wiele [punktów końcowych](../iot-hub/iot-hub-devguide-endpoints.md) , które umożliwiają aplikacjom łączenie się z usługą IoT Hub. Istnieją punkty końcowe dostępne dla urządzeń, które umożliwiają urządzeniom wykonywanie operacji, takich jak wysyłanie komunikatów [z urządzenia do chmury](#device-to-cloud) i otrzymywanie komunikatów [z chmury do urządzenia](#cloud-to-device) . Istnieją punkty końcowe zarządzania dostępne w ramach usług, które umożliwiają [aplikacjom zaplecza](#back-end-app) wykonywanie operacji, takich jak zarządzanie [tożsamościami urządzeń](#device-identity) i zarządzanie firmowymi urządzeniami. Istnieją [wbudowane punkty końcowe](#built-in-endpoints) dostępne w ramach usług służące do odczytywania komunikatów z urządzenia do chmury. Można utworzyć [niestandardowe punkty końcowe](#custom-endpoints) , aby odbierać komunikaty z urządzenia do chmury wysyłane przez [regułę routingu](#routing-rules).

### <a name="event-hub-compatible-endpoint"></a>Punkt końcowy zgodny z centrum zdarzeń

Aby odczytać komunikaty [z urządzenia do chmury](#device-to-cloud) wysyłane do usługi IoT Hub, można nawiązać połączenie z punktem końcowym centrum i użyć dowolnej metody zgodnej z centrum zdarzeń w celu odczytania tych komunikatów. Metody zgodne z centrum zdarzeń obejmują używanie [zestawów sdk Event Hubs](../event-hubs/event-hubs-programming-guide.md) i [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="f"></a>F

### <a name="field-gateway"></a>Brama pola

Brama pola umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub) i są zwykle wdrażane lokalnie na urządzeniach. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Brama

Brama umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Zobacz też [brama pola](#field-gateway), [brama chmury](#cloud-gateway)i [brama niestandardowa](#custom-gateway).

## <a name="i"></a>I

### <a name="identity-registry"></a>Rejestr tożsamości

[Rejestr tożsamości](../iot-hub/iot-hub-devguide-identity-registry.md) to wbudowany składnik Centrum IoT, który przechowuje informacje o poszczególnych urządzeniach, które mogą nawiązywać połączenia z usługą IoT Hub.

### <a name="interactive-message"></a>Komunikat interaktywny

Komunikat interaktywny to komunikat z [chmury do urządzenia](#cloud-to-device) , który wyzwala natychmiastowe działanie w zapleczu rozwiązania. Na przykład urządzenie może wysłać alarm dotyczący błędu, który powinien zostać automatycznie zalogowany do systemu CRM.

### <a name="interface"></a>Interfejs

W Plug and Play IoT interfejs opisuje powiązane funkcje, które są implementowane przez [urządzenie IoT Plug and Play](#iot-plug-and-play-device) lub pojedyncze [sznurki cyfrowe](#digital-twin). Można ponownie użyć interfejsów dla różnych [modeli urządzeń](#device-model). Gdy interfejs jest używany w modelu urządzenia, definiuje [składnik](#component) urządzenia. Proste urządzenie zawiera tylko domyślny interfejs.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge umożliwia wdrożenie oparte na chmurze usług platformy Azure i kodu specyficznego dla rozwiązania na urządzeniach lokalnych. [IoT Edge urządzenia](#iot-edge-device) mogą agregować dane z innych urządzeń, aby wykonywać obliczenia i analizy przed wysłaniem danych do chmury. Aby dowiedzieć się więcej, zobacz [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agent IoT Edge

Część środowiska uruchomieniowego IoT Edge odpowiedzialna za wdrażanie i monitorowanie modułów.

### <a name="iot-edge-device"></a>Urządzenie usługi IoT Edge

Urządzenie IoT Edge używa kontenerów [IoT Edge](#iot-edge-module) w celu uruchomienia usług platformy Azure, usług innych firm lub własnego kodu. Na urządzeniu IoT Edge [środowisko uruchomieniowe IoT Edge](#iot-edge-runtime) zarządza modułami. Można zdalnie monitorować urządzenie IoT Edge i zarządzać nim z poziomu chmury.

### <a name="iot-edge-automatic-deployment"></a>IoT Edge Automatyczne wdrażanie

IoT Edge wdrożenie automatyczne konfiguruje docelowy zestaw IoT Edge urządzeń do uruchamiania zestawu modułów IoT Edge. Każde wdrożenie stale zapewnia, że wszystkie urządzenia, które pasują do warunku docelowego, uruchamiają określony zestaw modułów, nawet jeśli nowe urządzenia są tworzone lub są modyfikowane w celu dopasowania go do warunku docelowego. Każde urządzenie IoT Edge otrzymuje tylko wdrożenie o najwyższym priorytecie, którego warunek docelowy spełnia. Dowiedz się więcej o [IoT Edge wdrożeniu automatycznym](../iot-edge/module-deployment-monitoring.md).

### <a name="iot-edge-deployment-manifest"></a>IoT Edge manifest wdrożenia

Dokument JSON zawierający informacje, które mają być skopiowane do co najmniej jednego wieloosiowego modułu IoT Edge urządzeń, aby wdrożyć zestaw modułów, tras i skojarzonych z nimi właściwości.

### <a name="iot-edge-gateway-device"></a>Urządzenie bramy IoT Edge

Urządzenie IoT Edge z urządzeniem podrzędnym. Urządzenie podrzędne może być IoT Edge lub nie IoT Edge urządzeniu.

### <a name="iot-edge-hub"></a>IoT Edge Hub

Część środowiska uruchomieniowego IoT Edge odpowiedzialna za komunikację między modułami, nadrzędną (w kierunku IoT Hub) i przejściem do trybu podrzędnego (w odniesieniu do IoT Hub).

### <a name="iot-edge-leaf-device"></a>IoT Edge urządzenie liścia

Urządzenie IoT Edge bez urządzenia podrzędnego.

### <a name="iot-edge-module"></a>Moduł IoT Edge

Moduł IoT Edge jest kontenerem platformy Docker, który można wdrożyć na IoT Edge urządzeniach. Wykonuje określone zadanie, takie jak pozyskiwanie komunikatu z urządzenia, przekształcanie komunikatu lub wysyłanie komunikatu do centrum IoT Hub. Komunikuje się z innymi modułami i wysyła dane do środowiska uruchomieniowego IoT Edge. [Poznaj wymagania i narzędzia służące do opracowywania modułów IoT Edge](../iot-edge/module-development.md).

### <a name="iot-edge-module-identity"></a>IoT Edge tożsamość modułu

Rekord w rejestrze tożsamości modułu IoT Hub, w którym szczegółowo opisano istnienie i poświadczenia zabezpieczeń, które będą używane przez moduł do uwierzytelniania za pomocą koncentratora lub IoT Hub.

### <a name="iot-edge-module-image"></a>Obraz modułu IoT Edge

Obraz platformy Docker używany przez środowisko uruchomieniowe IoT Edge do tworzenia wystąpień modułów.

### <a name="iot-edge-module-twin"></a>IoT Edge dwuosiowy moduł

Dokument JSON utrwalony w IoT Hub, w którym przechowywane są informacje o stanie wystąpienia modułu.

### <a name="iot-edge-priority"></a>Priorytet IoT Edge

Gdy dwa IoT Edge wdrożenia są przeznaczone dla tego samego urządzenia, zostanie zastosowane wdrożenie z wyższym priorytetem. Jeśli dwa wdrożenia mają ten sam priorytet, zostanie zastosowane wdrożenie o późniejszej dacie utworzenia. Dowiedz się więcej o [priorytecie](../iot-edge/module-deployment-monitoring.md#priority).

### <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge

Środowisko uruchomieniowe IoT Edge obejmuje wszystkie elementy, które firma Microsoft dystrybuuje do zainstalowania na urządzeniu IoT Edge. Obejmuje ona agenta krawędzi, centrum brzegowe i demona zabezpieczeń IoT Edge.

### <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge ustawić moduły na jedno urządzenie

Operacja, która kopiuje zawartość manifestu IoT Edge na jednym sznurze modułu urządzenia. Podstawowy interfejs API to ogólna konfiguracja zastosowania, która po prostu przyjmuje manifest IoT Edge jako dane wejściowe.

### <a name="iot-edge-target-condition"></a>Warunek docelowy IoT Edge

W przypadku wdrożenia IoT Edge warunek docelowy jest dowolnym warunkiem logicznym w tagach urządzenia bliźniaczych reprezentacji ', aby wybrać docelowe urządzenia wdrożenia, na przykład **tag. Environment = prod**. Warunek docelowy jest stale oceniany w celu uwzględnienia nowych urządzeń spełniających wymagania lub usuwania urządzeń, które nie są już wykonywane. Dowiedz się więcej o [warunku docelowym](../iot-edge/module-deployment-monitoring.md#target-condition)

### <a name="iot-hub"></a>Usługa IoT Hub

IoT Hub to w pełni zarządzana usługa platformy Azure, która umożliwia niezawodne i bezpieczne komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](../iot-hub/about-iot-hub.md). Korzystając z subskrypcji platformy Azure, można utworzyć centra IoT do obsługi obciążeń związanych z wiadomościami IoT.

### <a name="iot-hub-metrics"></a>Metryki IoT Hub

Metryki IoT Hub zapewniają dane dotyczące stanu centrów IoT w ramach subskrypcji platformy Azure. Metryki IoT Hub umożliwiają ocenę ogólnej kondycji usługi i podłączonych do niej urządzeń. Metryki IoT Hub mogą pomóc zobaczyć, co się dzieje z Twoim centrum IoT Hub, i zbadać problemy związane z główną przyczyną, bez konieczności kontaktowania się z pomocą techniczną platformy Azure. Aby dowiedzieć się więcej, zobacz [Monitor IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Język zapytań IoT Hub

[Język zapytań IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) to język PRZYPOMINAjący SQL, który umożliwia wykonywanie zapytań dotyczących [zadań](#job), cyfrowych bliźniaczych reprezentacji i bliźniaczych reprezentacji urządzeń.

### <a name="iot-hub-resource-rest-api"></a>Interfejs API REST zasobów IoT Hub

Za pomocą [interfejsu API REST zasobów IoT Hub](/rest/api/iothub/iothubresource) można zarządzać centrami IoT w ramach subskrypcji platformy Azure wykonujących operacje, takie jak tworzenie, aktualizowanie i usuwanie centrów.

### <a name="iot-solution-accelerators"></a>Akceleratory rozwiązań IoT

Akceleratory rozwiązań usługi Azure IoT łączą wiele usług platformy Azure z rozwiązaniami. Te rozwiązania pozwalają szybko rozpocząć pracę z kompleksowymi implementacjami typowych scenariuszy IoT. Aby uzyskać więcej informacji, zobacz [co to są Akceleratory rozwiązań usługi Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

### <a name="the-iot-extension-for-azure-cli"></a>Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure 

[Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) to międzyplatformowe narzędzie wiersza poleceń. Narzędzie umożliwia zarządzanie urządzeniami w [rejestrze tożsamości](#identity-registry), wysyłanie i odbieranie komunikatów oraz plików z urządzeń oraz monitorowanie operacji usługi IoT Hub.

### <a name="iot-plug-and-play-bridge"></a>Mostek IoT Plug and Play

IoT Plug and Play Bridge to aplikacja Open Source, która umożliwia istniejące czujniki i urządzenia peryferyjne podłączone do bram systemu Windows lub Linux do łączenia się z [urządzeniami Plug and Play IoT](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-device"></a>Urządzenie Plug and Play IoT

Urządzenie IoT Plug and Play jest zazwyczaj niewielką skalowalnym urządzeniem komputerowym, które zbiera dane lub steruje innymi urządzeniami, a także uruchamia oprogramowanie lub oprogramowanie układowe implementujące [model urządzenia](#device-model).  Na przykład urządzenie IoT Plug and Play może być urządzeniem monitorującym środowisko lub kontrolerem systemu nawadniania inteligentnego. Urządzenie Plug and Play IoT może być implementowane bezpośrednio lub jako moduł IoT Edge. Możesz napisać rozwiązanie IoT hostowane w chmurze, aby móc polecenie, kontrolować i odbierać dane z urządzeń Plug and Play IoT.

### <a name="iot-plug-and-play-conventions"></a>Konwencje technologii IoT Plug and Play

Usługi IoT [Plug and Play są](#iot-plug-and-play-device) zgodne z zestawem konwencji podczas wymiany danych z rozwiązaniem.

## <a name="j"></a>J

### <a name="job"></a>Zadanie

Zaplecze rozwiązania może używać [zadań](../iot-hub/iot-hub-devguide-jobs.md) do planowania i śledzenia działań na zestawie urządzeń zarejestrowanych w usłudze IoT Hub. Działania obejmują aktualizowanie [odpowiednich właściwości](#desired-properties)sznurów urządzeń, aktualizowanie [znaczników](#tags)bliźniaczych urządzeń i wywoływanie [metod bezpośrednich](#direct-method). [IoT Hub](#iot-hub) używa również do [importowania i eksportowania](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [rejestru tożsamości](#identity-registry).

## <a name="m"></a>M

### <a name="model-id"></a>Identyfikator modelu

Gdy urządzenie IoT Plug and Play nawiązuje połączenie z IoT Hub, wysyła **Identyfikator modelu** modelu [DTDL](#digital-twins-definition-language) , który implementuje. Ten identyfikator umożliwia znalezienie modelu urządzenia przez rozwiązanie.

### <a name="model-repository"></a>Repozytorium modeli

Repozytorium modeli przechowuje [modele urządzeń](#device-model) i [interfejsy](#interface).

### <a name="model-repository-rest-api"></a>Interfejs API REST repozytorium modelu

Interfejs API służący do zarządzania repozytorium modelu i korzystania z niego. Można na przykład użyć interfejsu API do dodawania i wyszukiwania [modeli urządzeń](#device-model).

### <a name="module-builder"></a>Konstruktor modułów

Konstruktor modułów korzysta z [modelu](#device-model) i [interfejsów](#interface) urządzeń podczas implementowania kodu do uruchamiania na [urządzeniu Plug and Play IoT](#iot-plug-and-play-device). Konstruktory modułów implementują kod jako moduł lub moduł IoT Edge do wdrożenia w środowisku uruchomieniowym IoT Edge na urządzeniu.

### <a name="modules"></a>Moduły

Po stronie urządzenia zestawy SDK urządzeń IoT Hub umożliwiają tworzenie [modułów](../iot-hub/iot-hub-devguide-module-twins.md) , w których każdy z nich otwiera niezależne połączenie z IoT Hub. Ta funkcja umożliwia korzystanie z oddzielnych obszarów nazw dla różnych składników na urządzeniu.

Sznury i moduły modułu zapewniają takie same możliwości, jak [tożsamość urządzenia](#device-identity) i [sznurki urządzeń](#device-twin) , ale z większą szczegółowością. Ten bardziej szczegółowy stopień szczegółowości umożliwia urządzeniom opartym na systemie operacyjnym lub urządzeniom oprogramowania układowego zarządzanie wieloma składnikami, a także izolowanie konfiguracji i warunków dla każdego z tych składników.

### <a name="module-identity"></a>Tożsamość modułu

Tożsamość modułu jest unikatowym identyfikatorem przypisanym do każdego modułu, który należy do urządzenia. Tożsamość modułu jest również rejestrowana w [rejestrze tożsamości](#identity-registry).

### <a name="module-twin"></a>Sznurki modułu

Podobnie jak w przypadku sznurka urządzenia, sznurek modułu jest dokumentem JSON, który przechowuje informacje o stanie modułu, takie jak metadane, konfiguracje i warunki. IoT Hub utrzymuje sznurki modułu dla każdej tożsamości modułu, która jest dostarczana w ramach tożsamości urządzenia w centrum IoT. Moduł bliźniaczych reprezentacji pozwala synchronizować warunki modułu i konfiguracje między modułem a zapleczem rozwiązania. Można wysłać zapytanie do modułu bliźniaczych reprezentacji w celu zlokalizowania określonych modułów i zbadania stanu długotrwałych operacji.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) jest jednym z protokołów obsługi komunikatów, które [IoT Hub](#iot-hub) obsługiwać komunikację z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów obsługiwanych przez IoT Hub, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Monitorowanie operacji

[Monitorowanie operacji](../iot-hub/iot-hub-operations-monitoring.md) IoT Hub umożliwia monitorowanie stanu operacji w centrum IoT w czasie rzeczywistym. [IoT Hub](#iot-hub) śledzi zdarzenia w kilku kategoriach operacji. Można zrezygnować z wysyłania zdarzeń z jednej lub kilku kategorii do punktu końcowego IoT Hub w celu przetworzenia. Można monitorować dane pod kątem błędów lub konfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

## <a name="p"></a>P

### <a name="physical-device"></a>Urządzenie fizyczne

Urządzenie fizyczne to rzeczywiste urządzenie, takie jak Raspberry Pi, które nawiązuje połączenie z usługą IoT Hub. Dla wygody wiele samouczków IoT Hub korzysta z [symulowanych urządzeń](#simulated-device) , aby umożliwić uruchamianie przykładów na komputerze lokalnym.

### <a name="primary-and-secondary-keys"></a>Klucze podstawowe i pomocnicze

Po nawiązaniu połączenia z punktem końcowym podłączonym do urządzenia lub usługi w usłudze IoT Hub [Parametry połączenia](#connection-string) obejmują klucz umożliwiający udzielenie dostępu. Po dodaniu urządzenia do [rejestru tożsamości](#identity-registry) lub dodaniu [zasad dostępu współdzielonego](#shared-access-policy) do centrum Usługa generuje klucz podstawowy i pomocniczy. Posiadanie dwóch kluczy umożliwia przewinięcie jednego z kluczy do drugiego podczas aktualizowania klucza bez utraty dostępu do centrum IoT Hub.

### <a name="properties"></a>Właściwości

Właściwości są polami danych zdefiniowanymi w [interfejsie](#interface) , który reprezentuje jakiś stan dwucyfrowego sznurka. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Właściwości tylko do odczytu, takie jak numer seryjny, są ustawiane przez kod uruchomiony na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device) .  Właściwości z możliwością zapisu, takie jak próg alarmu, są zazwyczaj ustawiane na podstawie rozwiązania IoT opartego na chmurze.

### <a name="protocol-gateway"></a>Brama protokołu

Brama protokołu jest zwykle wdrażana w chmurze i udostępnia usługi translacji protokołów dla urządzeń łączących się z [IoT Hub](#iot-hub). Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="reported-configuration"></a>Raportowana konfiguracja

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md), raportowana konfiguracja odwołuje się do kompletnego zestawu właściwości i metadanych w bliźniaczych urządzeniach, które powinny być zgłaszane do zaplecza rozwiązania.

### <a name="reported-properties"></a>Raportowane właściwości

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md), raportowane właściwości jest podsekcją sznurka urządzenia używanego z [żądanymi właściwościami](#desired-properties) do synchronizowania konfiguracji lub warunku urządzenia. Raportowane właściwości mogą być ustawiane tylko przez [aplikację urządzenia](#device-app) i mogą być odczytywane i wysyłane przez [aplikację zaplecza](#back-end-app).

### <a name="retry-policy"></a>Zasady ponawiania

Zasady ponawiania służą do obsługi [błędów przejściowych](/azure/architecture/best-practices/transient-faults) podczas łączenia się z usługą w chmurze.

### <a name="routing-rules"></a>Reguły routingu

Można skonfigurować [reguły routingu](../iot-hub/iot-hub-devguide-messages-read-custom.md) w centrum IoT Hub, aby kierować komunikaty z urządzenia do chmury do [wbudowanego punktu końcowego](#built-in-endpoints) lub do [niestandardowych punktów końcowych](#custom-endpoints) w celu przetworzenia przez zaplecze rozwiązania.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN to protokół, który jest wykorzystywany przez protokół AMQP do transferowania tokenów zabezpieczających.

### <a name="service-rest-api"></a>Interfejs API REST usługi

Możesz użyć [interfejsu API REST usługi](/rest/api/iothub/service/configuration) z zaplecza rozwiązania do zarządzania urządzeniami. Interfejs API umożliwia pobieranie i aktualizowanie właściwości [bliźniaczych urządzeń](#device-twin) , wywoływanie [metod bezpośrednich](#direct-method)i planowanie [zadań](#job). Zazwyczaj należy użyć jednego z [zestawów SDK usług](#azure-iot-service-sdks) wyższego poziomu, jak pokazano w samouczkach IoT Hub.

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego (SAS) są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Uwierzytelnianie SAS ma dwa składniki: _zasady dostępu współdzielonego_ i _sygnatura dostępu współdzielonego_ (często nazywane tokenem). Urządzenie używa sygnatury dostępu współdzielonego do uwierzytelniania w usłudze IoT Hub. [Aplikacje zaplecza](#back-end-app) również używają sygnatury dostępu współdzielonego do uwierzytelniania za pomocą punktów końcowych mających dostęp do usługi w usłudze IoT Hub. Zazwyczaj należy uwzględnić token sygnatury dostępu współdzielonego w [parametrach połączenia](#connection-string) używanych przez aplikację w celu nawiązania połączenia z usługą IoT Hub.

### <a name="shared-access-policy"></a>Zasady dostępu współdzielonego

Zasady dostępu współdzielonego określają uprawnienia przyznane każdemu użytkownikowi, który ma prawidłowy [klucz podstawowy lub pomocniczy](#primary-and-secondary-keys) skojarzony z tymi zasadami. Możesz zarządzać zasadami dostępu współdzielonego i kluczami dla centrum w portalu.

### <a name="simulated-device"></a>Symulowane urządzenie

Dla wygody wiele samouczków IoT Hub korzysta z symulowanych urządzeń, aby umożliwić uruchamianie przykładów na komputerze lokalnym. Z kolei [urządzenie fizyczne](#physical-device) to rzeczywiste urządzenie, takie jak Raspberry Pi, które nawiązuje połączenie z usługą IoT Hub.

### <a name="solution"></a>Rozwiązanie

_Rozwiązanie_ może odwoływać się do rozwiązania programu Visual Studio, które zawiera co najmniej jeden projekt. _Rozwiązanie_ może również odnosić się do rozwiązania IoT, które obejmuje takie elementy, jak urządzenia, [aplikacje urządzenia](#device-app), centrum IoT, inne usługi platformy Azure i [Aplikacje zaplecza](#back-end-app).

### <a name="solution-builder"></a>Konstruktor rozwiązań

Konstruktor rozwiązań tworzy zaplecze rozwiązania. Konstruktor rozwiązań zwykle współpracuje z zasobami platformy Azure, takimi jak IoT Hub i [repozytoria modeli](#model-repository).

### <a name="system-properties"></a>Właściwości systemu

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)właściwości systemu są tylko do odczytu i zawierają informacje dotyczące użycia urządzenia, takie jak czas ostatniego działania i stan połączenia.

## <a name="t"></a>T

### <a name="tags"></a>Tagi

W kontekście [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)Tagi są metadane urządzenia przechowywane i pobierane przez zaplecze rozwiązania w postaci dokumentu JSON. Tagi nie są widoczne dla aplikacji na urządzeniu.

### <a name="telemetry"></a>Telemetria

Urządzenia zbierają dane telemetryczne, takie jak szybkość wiatru lub temperatura, i używają komunikatów punktów danych w celu wysyłania danych telemetrycznych do centrum IoT.

W Plug and Play IoT pola telemetrii zdefiniowane w [interfejsie](#interface) reprezentują pomiary. Te pomiary są zazwyczaj wartościami, takimi jak odczyty czujników, które są wysyłane przez [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jako strumień danych.

### <a name="token-service"></a>Usługa tokenu

Za pomocą usługi tokenu można zaimplementować mechanizm uwierzytelniania dla urządzeń. Używa [zasad dostępu współdzielonego](#shared-access-policy) IoT Hub z uprawnieniami **DeviceConnect** do tworzenia tokenów z *zakresem urządzeń* . Te tokeny umożliwiają urządzeniu łączenie się z Centrum IoT Hub. Urządzenie używa niestandardowego mechanizmu uwierzytelniania do uwierzytelniania za pomocą usługi tokenu. Jeśli urządzenie zostanie pomyślnie uwierzytelnione, usługa tokenów wystawia token sygnatury dostępu współdzielonego, aby urządzenie korzystało z Centrum IoT.

### <a name="twin-queries"></a>Zapytania bliźniaczye

[Zapytania wieloosiowe urządzenia i modułu](../iot-hub/iot-hub-devguide-query-language.md) używają języka zapytań IoT Hub, takich jak SQL, do pobierania informacji z urządzenia bliźniaczych reprezentacji lub modułu bliźniaczych reprezentacji. Możesz użyć tego samego IoT Hub języka zapytań, aby pobrać informacje o [zadaniu](#job) uruchomionym w centrum IoT.

### <a name="twin-synchronization"></a>Synchronizacja przędzy

Synchronizacja przędzy używa [żądanych właściwości](#desired-properties) w urządzeniu bliźniaczych reprezentacji lub module bliźniaczych reprezentacji w celu skonfigurowania urządzeń lub modułów i pobrania przez nich [raportowanych właściwości](#reported-properties) do sklepu w ramach sznurka.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certyfikat klienta X. 509

Urządzenie może używać certyfikatu X. 509 do uwierzytelniania za pomocą [IoT Hub](#iot-hub). Użycie certyfikatu X. 509 jest alternatywą dla korzystania z [tokenu SAS](#shared-access-signature).
