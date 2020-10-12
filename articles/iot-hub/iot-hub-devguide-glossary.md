---
title: Azure IoT Hub słownik terminów | Microsoft Docs
description: Przewodnik dla deweloperów — słownik objaśniający niektóre typowe terminy używane w artykułach IoT Hub platformy Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b7a81357cc6ee2608d2acd3051dcac3e5d0e07e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87023470"
---
# <a name="glossary-of-iot-hub-terms"></a>Słownik warunków IoT Hub

W tym artykule wymieniono niektóre typowe terminy używane w artykułach IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protokół zaawansowanej kolejki komunikatów

[Protokół Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) to jeden z protokołów obsługi komunikatów, które [IoT Hub](#iot-hub) obsługiwać komunikację z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów obsługiwanych przez IoT Hub, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatyczne zarządzanie urządzeniami

Automatyczne zarządzanie urządzeniami w systemie Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem dużymi flotami urządzeń w całym cyklu ich życia. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestawem urządzeń na podstawie ich właściwości, definiować żądaną konfigurację i zezwalać IoT Hub na aktualizowanie urządzeń, gdy zostaną one objęte zakresem.  Składa się z [automatycznych konfiguracji urządzeń](iot-hub-auto-device-config.md) i [IoT Edge wdrożeń automatycznych](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Automatyczna konfiguracja urządzeń

Zaplecze rozwiązania może używać [automatycznych konfiguracji urządzeń](iot-hub-auto-device-config.md) do przypisywania odpowiednich właściwości do zestawu [bliźniaczych reprezentacji urządzeń](#device-twin) i stanu raportu przy użyciu metryk systemu i metryk niestandardowych. 

## <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

[Klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md) to międzyplatformowe narzędzie do tworzenia i zarządzania zasobami w Microsoft Azure. Ta wersja interfejsu wiersza polecenia powinna być używana tylko w przypadku wdrożeń klasycznych.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) to międzyplatformowe narzędzie do tworzenia i zarządzania zasobami w Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>Zestawy SDK urządzeń usługi Azure IoT

Dostępne są _zestawy SDK urządzeń_ dla wielu języków, które umożliwiają tworzenie [aplikacji dla urządzeń](#device-app) , które współpracują z Centrum IoT. Samouczki IoT Hub pokazują, jak korzystać z tych zestawów SDK urządzeń. Kod źródłowy i dodatkowe informacje o zestawach SDK urządzeń można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub.

## <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

[Eksplorator usługi Azure IoT](https://github.com/Azure/azure-iot-explorer) służy do wyświetlania danych telemetrycznych wysyłanych przez urządzenie, pracy z właściwościami urządzenia i wywoływania poleceń. Można również użyć Eksploratora do współpracy z urządzeniami i ich testowania oraz do zarządzania urządzeniami typu Plug and Play.

## <a name="azure-iot-service-sdks"></a>Zestawy SDK usługi Azure IoT

Istnieją _zestawy SDK usługi_ dostępne dla wielu języków, które umożliwiają tworzenie [aplikacji zaplecza](#back-end-app) , które współpracują z Centrum IoT. Samouczki IoT Hub pokazują, jak korzystać z tych zestawów SDK usługi. Kod źródłowy i dodatkowe informacje o zestawach SDK usług można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub.

## <a name="azure-iot-tools"></a>Narzędzia usługi Azure IoT

[Narzędzia Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) to międzyplatformowe rozszerzenie Visual Studio Code typu open-source, które ułatwia zarządzanie usługą Azure IoT Hub i urządzeniami w vs Code. Korzystając z narzędzi Azure IoT Tools, deweloperzy IoT mogą łatwo opracowywać projekt IoT w VS Code.

## <a name="azure-portal"></a>Azure Portal

[Microsoft Azure Portal](https://portal.azure.com) to centralne miejsce, w którym można zainicjować obsługę zasobów platformy Azure i zarządzać nimi. Organizuje swoją _zawartość przy użyciu_bloków.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/) to zbiór poleceń cmdlet służących do zarządzania platformą Azure przy użyciu programu Windows PowerShell. Za pomocą poleceń cmdlet można tworzyć, testować i wdrażać rozwiązania i usługi dostarczane za pomocą platformy Azure oraz zarządzać nimi.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) umożliwia współpracę z zasobami w rozwiązaniu jako Grupa. W jednej, skoordynowanej operacji można wdrożyć, zaktualizować lub usunąć zasoby dla rozwiązania.

## <a name="azure-service-bus"></a>Azure Service Bus

[Service Bus](../service-bus/index.md) zapewnia komunikację w chmurze z obsługą komunikatów w przedsiębiorstwie i przekazywaniem komunikacji, która ułatwia łączenie rozwiązań lokalnych z chmurą. Niektóre samouczki IoT Hub korzystają z [kolejek](../service-bus-messaging/service-bus-messaging-overview.md)Service Busych.

## <a name="azure-storage"></a>Azure Storage

[Usługa Azure Storage](../storage/common/storage-introduction.md) to rozwiązanie magazynu w chmurze. Obejmuje ona usługę Blob Storage, której można użyć do przechowywania danych obiektów bez struktury. Niektóre samouczki IoT Hub korzystają z magazynu obiektów BLOB.

## <a name="back-end-app"></a>Aplikacja zaplecza

W kontekście [IoT Hub](#iot-hub)aplikacja zaplecza jest aplikacją, która łączy się z jednym z punktów końcowych mających dostęp do usługi w usłudze IoT Hub. Na przykład aplikacja zaplecza może pobrać komunikaty [z urządzenia do chmury](#device-to-cloud) lub zarządzać [rejestrem tożsamości](#identity-registry). Zazwyczaj aplikacja zaplecza działa w chmurze, ale w wielu samouczkach aplikacje zaplecza są aplikacjami konsolowymi uruchomionymi na lokalnym komputerze deweloperskim.

## <a name="built-in-endpoints"></a>Wbudowane punkty końcowe

Każde Centrum IoT Hub zawiera wbudowany [punkt końcowy](iot-hub-devguide-endpoints.md) , który jest zgodny z centrum zdarzeń. Do odczytywania komunikatów z urządzenia do chmury z tego punktu końcowego można użyć dowolnego mechanizmu, który współpracuje z Event Hubs.

## <a name="cloud-gateway"></a>Brama chmury

Brama w chmurze umożliwia łączność urządzeń, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Brama w chmurze jest hostowana w chmurze w przeciwieństwie do [bramy pola](#field-gateway) działającej lokalnie na urządzeniach. Typowym przypadkiem użycia dla bramy w chmurze jest zaimplementowanie translacji protokołów dla urządzeń.

## <a name="cloud-to-device"></a>Z chmury do urządzenia

Odnosi się do komunikatów wysyłanych z usługi IoT Hub do podłączonego urządzenia. Często te komunikaty są poleceniami, które nakazują urządzeniu wykonanie akcji. Aby uzyskać więcej informacji, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguracja

W kontekście [automatycznej konfiguracji urządzeń](iot-hub-auto-device-config.md)konfiguracja w ramach IoT Hub definiuje żądaną konfigurację zestawu urządzeń bliźniaczych reprezentacji i zawiera zestaw metryk do raportowania stanu i postępu.

## <a name="connection-string"></a>Parametry połączenia

Parametry połączenia w kodzie aplikacji są używane do hermetyzacji informacji wymaganych do nawiązania połączenia z punktem końcowym. Parametry połączenia zwykle obejmują adres punktu końcowego i informacje o zabezpieczeniach, ale formaty parametrów połączenia różnią się w zależności od usług. Istnieją dwa typy parametrów połączenia skojarzone z usługą IoT Hub:

- *Parametry połączenia urządzeń* umożliwiają urządzeniom łączenie się z punktami końcowymi dostępnymi dla urządzeń w centrum IoT.

- *IoT Hub parametry połączenia* umożliwiają aplikacjom zaplecza łączenie się z punktami końcowymi dostępnymi do usługi w usłudze IoT Hub.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Można utworzyć niestandardowe [punkty końcowe](iot-hub-devguide-endpoints.md) w usłudze IoT Hub w celu dostarczenia komunikatów wysłanych przez [regułę routingu](#routing-rules). Niestandardowe punkty końcowe łączą się bezpośrednio z centrum zdarzeń, kolejką Service Bus lub Service Bus tematem.

## <a name="custom-gateway"></a>Brama niestandardowa

Brama umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Za pomocą Azure IoT Edge można tworzyć bramy niestandardowe, które implementują logikę niestandardową w celu obsługi komunikatów, niestandardowych konwersji protokołów i innych operacji przetwarzania na krawędzi.

## <a name="data-point-message"></a>Komunikat punktu danych

Komunikat punktu danych to komunikat [z urządzenia do chmury](#device-to-cloud) , który zawiera dane [telemetryczne](#telemetry) , takie jak szybkość lub temperatura wiatru.

## <a name="desired-configuration"></a>Wymagana konfiguracja

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md), wymagana konfiguracja odnosi się do kompletnego zestawu właściwości i metadanych w bliźniaczych urządzeniach, które powinny być zsynchronizowane z urządzeniem.

## <a name="desired-properties"></a>Żądane właściwości

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md)odpowiednie właściwości są podsekcją sznurka urządzenia, która jest używana z [zgłoszonymi właściwościami](#reported-properties) do synchronizowania konfiguracji lub warunku urządzenia. Żądane właściwości można ustawić tylko przez [aplikację zaplecza](#back-end-app) i są one obserwowane przez [aplikację urządzenia](#device-app).

## <a name="device-to-cloud"></a>Urządzenie-chmura

Odnosi się do komunikatów wysyłanych z podłączonego urządzenia do [IoT Hub](#iot-hub). Mogą to być komunikaty [typu punkt danych](#data-point-message) lub wiadomości [interaktywne](#interactive-message) . Aby uzyskać więcej informacji, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Urządzenie

W kontekście IoT urządzenie jest zazwyczaj niewielką skalowalnym urządzeniem komputerowym, które może zbierać dane lub sterować innymi urządzeniami. Na przykład urządzenie może być urządzeniem monitorującym środowisko lub kontrolerem dla systemów wodociągowych i wentylacyjnych w szklarni. [Katalog urządzeń](https://catalog.azureiotsolutions.com/) zawiera listę urządzeń sprzętowych certyfikowanych do pracy z [IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikacja urządzenia

Aplikacja urządzenia jest uruchamiana na [urządzeniu](#device) i obsługuje komunikację z [Centrum IoT Hub](#iot-hub). Zazwyczaj podczas implementowania aplikacji urządzenia używasz jednego z [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdks) . W wielu samouczkach IoT można użyć [symulowanego urządzenia](#simulated-device) dla wygody.

## <a name="device-condition"></a>Warunek urządzenia

Odnosi się do informacji o stanie urządzenia, takich jak aktualnie używana metoda łączności, zgłoszona przez [aplikację urządzenia](#device-app). [Aplikacje urządzenia](#device-app) mogą również zgłaszać swoje możliwości. Możesz wykonywać zapytania dotyczące warunków i informacji o możliwościach za pomocą bliźniaczych reprezentacji urządzeń.

## <a name="device-data"></a>Dane urządzenia

Dane urządzenia odnoszą się do danych na urządzeniu przechowywanych w [rejestrze tożsamości](#identity-registry)IoT Hub. Istnieje możliwość zaimportowania i wyeksportowania tych danych.

## <a name="device-explorer"></a>Device Explorer

Eksplorator urządzeń został zastąpiony [Eksploratorem usługi Azure IoT](https://github.com/Azure/azure-iot-explorer), który służy do wyświetlania danych telemetrycznych wysyłanych przez urządzenie, pracy z właściwościami urządzenia i wywoływania poleceń. Można również użyć Eksploratora do współpracy z urządzeniami i ich testowania oraz do zarządzania urządzeniami typu Plug and Play.

## <a name="device-identity"></a>Tożsamość urządzenia

Tożsamość urządzenia jest unikatowym identyfikatorem przypisanym do każdego urządzenia zarejestrowanego w [rejestrze tożsamości](#identity-registry).

## <a name="device-management"></a>Zarządzanie urządzeniami

Zarządzanie urządzeniami obejmuje pełny cykl życia związany z zarządzaniem urządzeniami w ramach rozwiązania IoT, w tym planowanie, Inicjowanie obsługi, konfigurowanie, monitorowanie i wycofywanie.

## <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami

[Centrum IoT Hub](#iot-hub) umożliwia korzystanie ze wspólnych wzorców zarządzania urządzeniami, w tym ponownego uruchamiania, resetowania fabryki i przeprowadzania aktualizacji oprogramowania układowego na urządzeniach.

## <a name="device-rest-api"></a>Interfejs API REST urządzenia

Za pomocą [interfejsu API REST urządzenia](https://docs.microsoft.com/rest/api/iothub/device) można wysyłać komunikaty z urządzenia do chmury do usługi IoT Hub i odbierać komunikaty z [chmury do urządzenia](#cloud-to-device) z Centrum IoT. Zazwyczaj należy użyć jednego z [zestawów SDK urządzeń](#azure-iot-device-sdks) wyższego poziomu, jak pokazano w samouczkach IoT Hub.

## <a name="device-provisioning"></a>Inicjowanie obsługi urządzeń

Inicjowanie obsługi administracyjnej urządzeń polega na dodaniu początkowych [danych urządzenia](#device-data) do sklepów w rozwiązaniu. Aby umożliwić nowym urządzeniu łączenie się z centrum, należy dodać identyfikator i klucze urządzenia do [rejestru tożsamości](#identity-registry)IoT Hub. W ramach procesu aprowizacji może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań.

## <a name="device-twin"></a>Bliźniak urządzenia

[Sznurki urządzenia](iot-hub-devguide-device-twins.md) to dokument JSON, który przechowuje informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. [IoT Hub](#iot-hub) utrzymuje sznurki urządzenia dla każdego urządzenia, które zainicjujesz w usłudze IoT Hub. Bliźniaczych reprezentacji urządzenia pozwala synchronizować warunki i konfiguracje [urządzeń](#device-condition) między urządzeniem a zapleczem rozwiązania. Można zbadać bliźniaczych reprezentacji urządzenia, aby zlokalizować określone urządzenia i zbadać stan długotrwałych operacji.

## <a name="direct-method"></a>Metoda bezpośrednia

[Metoda bezpośrednia](iot-hub-devguide-direct-methods.md) to sposób wyzwalania metody do wykonania na urządzeniu przez wywołanie interfejsu API w centrum IoT.

## <a name="endpoint"></a>Punkt końcowy

Centrum IoT udostępnia wiele [punktów końcowych](iot-hub-devguide-endpoints.md) , które umożliwiają aplikacjom łączenie się z usługą IoT Hub. Istnieją punkty końcowe dostępne dla urządzeń, które umożliwiają urządzeniom wykonywanie operacji, takich jak wysyłanie komunikatów [z urządzenia do chmury](#device-to-cloud) i otrzymywanie komunikatów [z chmury do urządzenia](#cloud-to-device) . Istnieją punkty końcowe zarządzania dostępne w ramach usług, które umożliwiają [aplikacjom zaplecza](#back-end-app) wykonywanie operacji, takich jak zarządzanie [tożsamościami urządzeń](#device-identity) i zarządzanie firmowymi urządzeniami. Istnieją [wbudowane punkty końcowe](#built-in-endpoints) dostępne w ramach usług służące do odczytywania komunikatów z urządzenia do chmury. Można utworzyć [niestandardowe punkty końcowe](#custom-endpoints) , aby odbierać komunikaty z urządzenia do chmury wysyłane przez [regułę routingu](#routing-rules).

## <a name="event-hubs-service"></a>Usługa Event Hubs

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) to wysoce skalowalna usługa transferu danych przychodzących, która pozwala na pozyskiwanie milionów zdarzeń na sekundę. Usługa umożliwia przetwarzanie i analizowanie ogromnych ilości danych generowanych przez połączone urządzenia i aplikacje. Porównanie z usługą IoT Hub można znaleźć w temacie [porównanie IoT Hub platformy Azure i usługi azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Punkt końcowy zgodny z centrum zdarzeń

Aby odczytać komunikaty [z urządzenia do chmury](#device-to-cloud) wysyłane do usługi IoT Hub, można nawiązać połączenie z punktem końcowym centrum i użyć dowolnej metody zgodnej z centrum zdarzeń w celu odczytania tych komunikatów. Metody zgodne z centrum zdarzeń obejmują używanie [zestawów sdk Event Hubs](../event-hubs/event-hubs-programming-guide.md) i [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Brama pola

Brama pola umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub) i są zwykle wdrażane lokalnie na urządzeniach. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Bezpłatne konto

Możesz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) , aby dokończyć samouczki IoT Hub i eksperymentować z usługą IoT Hub (i innymi usługami platformy Azure).

## <a name="gateway"></a>Brama

Brama umożliwia łączność z urządzeniami, które nie mogą łączyć się bezpośrednio z [IoT Hub](#iot-hub). Zobacz też [brama pola](#field-gateway), [brama chmury](#cloud-gateway)i [brama niestandardowa](#custom-gateway).

## <a name="identity-registry"></a>Rejestr tożsamości

[Rejestr tożsamości](iot-hub-devguide-identity-registry.md) to wbudowany składnik Centrum IoT, który przechowuje informacje o poszczególnych urządzeniach, które mogą nawiązywać połączenia z usługą IoT Hub.

## <a name="interactive-message"></a>Komunikat interaktywny

Komunikat interaktywny to komunikat z [chmury do urządzenia](#cloud-to-device) , który wyzwala natychmiastowe działanie w zapleczu rozwiązania. Na przykład urządzenie może wysłać alarm dotyczący błędu, który powinien zostać automatycznie zalogowany do systemu CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Usługa IoT Hub

IoT Hub to w pełni zarządzana usługa platformy Azure, która umożliwia niezawodne i bezpieczne komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](about-iot-hub.md) Korzystając z [subskrypcji platformy Azure](#subscription), można utworzyć centra IoT do obsługi obciążeń związanych z wiadomościami IoT.

## <a name="iot-hub-metrics"></a>Metryki IoT Hub

[Metryki IoT Hub](iot-hub-metrics.md) zapewniają dane dotyczące stanu centrów IoT w [ramach subskrypcji platformy Azure](#subscription). Metryki IoT Hub umożliwiają ocenę ogólnej kondycji usługi i podłączonych do niej urządzeń. Metryki IoT Hub mogą pomóc zobaczyć, co się dzieje z Twoim centrum IoT Hub, i zbadać problemy związane z główną przyczyną, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

## <a name="iot-hub-query-language"></a>Język zapytań IoT Hub

[Język zapytań IoT Hub](iot-hub-devguide-query-language.md) to język przypominający SQL, który umożliwia wykonywanie zapytań dotyczących [zadań](#job) i bliźniaczych reprezentacji urządzeń.

## <a name="iot-hub-resource-rest-api"></a>Interfejs API REST zasobów IoT Hub

Za pomocą [interfejsu API REST zasobów IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) można zarządzać centrami IoT w [ramach subskrypcji platformy Azure](#subscription) wykonujących operacje, takie jak tworzenie, aktualizowanie i usuwanie centrów.

## <a name="iot-solution-accelerators"></a>Akceleratory rozwiązań IoT

Akceleratory rozwiązań usługi Azure IoT łączą wiele usług platformy Azure z rozwiązaniami. Te rozwiązania pozwalają szybko rozpocząć pracę z kompleksowymi implementacjami typowych scenariuszy IoT. Aby uzyskać więcej informacji, zobacz [co to są Akceleratory rozwiązań usługi Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure 

[Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) to międzyplatformowe narzędzie wiersza poleceń. Narzędzie umożliwia zarządzanie urządzeniami w [rejestrze tożsamości](#identity-registry), wysyłanie i odbieranie komunikatów oraz plików z urządzeń oraz monitorowanie operacji usługi IoT Hub.

## <a name="job"></a>Zadanie

Zaplecze rozwiązania może używać [zadań](iot-hub-devguide-jobs.md) do planowania i śledzenia działań na zestawie urządzeń zarejestrowanych w usłudze IoT Hub. Działania obejmują aktualizowanie [odpowiednich właściwości](#desired-properties)sznurów urządzeń, aktualizowanie [znaczników](#tags)bliźniaczych urządzeń i wywoływanie [metod bezpośrednich](#direct-method). [IoT Hub](#iot-hub) używa również do [importowania i eksportowania](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [rejestru tożsamości](#identity-registry).

## <a name="modules"></a>Moduły

Po stronie urządzenia zestawy SDK urządzeń IoT Hub umożliwiają tworzenie [modułów](iot-hub-devguide-module-twins.md) , w których każdy z nich otwiera niezależne połączenie z IoT Hub. Ta funkcja umożliwia korzystanie z oddzielnych obszarów nazw dla różnych składników na urządzeniu.

Sznury i moduły modułu zapewniają takie same możliwości, jak [tożsamość urządzenia](#device-identity) i [sznurki urządzeń](#device-twin) , ale z większą szczegółowością. Ten bardziej szczegółowy stopień szczegółowości umożliwia urządzeniom opartym na systemie operacyjnym lub urządzeniom oprogramowania układowego zarządzanie wieloma składnikami, a także izolowanie konfiguracji i warunków dla każdego z tych składników.

## <a name="module-identity"></a>Tożsamość modułu

Tożsamość modułu jest unikatowym identyfikatorem przypisanym do każdego modułu, który należy do urządzenia. Tożsamość modułu jest również rejestrowana w [rejestrze tożsamości](#identity-registry).

## <a name="module-twin"></a>Sznurki modułu

Podobnie jak w przypadku sznurka urządzenia, sznurek modułu jest dokumentem JSON, który przechowuje informacje o stanie modułu, takie jak metadane, konfiguracje i warunki. IoT Hub utrzymuje sznurki modułu dla każdej tożsamości modułu, która jest dostarczana w ramach tożsamości urządzenia w centrum IoT. Moduł bliźniaczych reprezentacji pozwala synchronizować warunki modułu i konfiguracje między modułem a zapleczem rozwiązania. Można wysłać zapytanie do modułu bliźniaczych reprezentacji w celu zlokalizowania określonych modułów i zbadania stanu długotrwałych operacji.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) jest jednym z protokołów obsługi komunikatów, które [IoT Hub](#iot-hub) obsługiwać komunikację z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów obsługiwanych przez IoT Hub, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorowanie operacji

[Monitorowanie operacji](iot-hub-operations-monitoring.md) IoT Hub umożliwia monitorowanie stanu operacji w centrum IoT w czasie rzeczywistym. [IoT Hub](#iot-hub) śledzi zdarzenia w kilku kategoriach operacji. Można zrezygnować z wysyłania zdarzeń z jednej lub kilku kategorii do punktu końcowego IoT Hub w celu przetworzenia. Można monitorować dane pod kątem błędów lub konfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

## <a name="physical-device"></a>Urządzenie fizyczne

Urządzenie fizyczne to rzeczywiste urządzenie, takie jak Raspberry Pi, które nawiązuje połączenie z usługą IoT Hub. Dla wygody wiele samouczków IoT Hub korzysta z [symulowanych urządzeń](#simulated-device) , aby umożliwić uruchamianie przykładów na komputerze lokalnym.

## <a name="primary-and-secondary-keys"></a>Klucze podstawowe i pomocnicze

Po nawiązaniu połączenia z punktem końcowym podłączonym do urządzenia lub usługi w usłudze IoT Hub [Parametry połączenia](#connection-string) obejmują klucz umożliwiający udzielenie dostępu. Po dodaniu urządzenia do [rejestru tożsamości](#identity-registry) lub dodaniu [zasad dostępu współdzielonego](#shared-access-policy) do centrum Usługa generuje klucz podstawowy i pomocniczy. Posiadanie dwóch kluczy umożliwia przewinięcie jednego z kluczy do drugiego podczas aktualizowania klucza bez utraty dostępu do centrum IoT Hub.

## <a name="protocol-gateway"></a>Brama protokołu

Brama protokołu jest zwykle wdrażana w chmurze i udostępnia usługi translacji protokołów dla urządzeń łączących się z [IoT Hub](#iot-hub). Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Istnieją różne [przydziały](iot-hub-devguide-quotas-throttling.md) , które dotyczą korzystania z [IoT Hub](#iot-hub), wiele z przydziałów zależy od warstwy Centrum IoT. [IoT Hub](#iot-hub) stosuje także [ograniczenia](iot-hub-devguide-quotas-throttling.md) dotyczące korzystania z usługi w czasie wykonywania.

## <a name="reported-configuration"></a>Raportowana konfiguracja

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md), raportowana konfiguracja odwołuje się do kompletnego zestawu właściwości i metadanych w bliźniaczych urządzeniach, które powinny być zgłaszane do zaplecza rozwiązania.

## <a name="reported-properties"></a>Raportowane właściwości

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md), raportowane właściwości jest podsekcją sznurka urządzenia używanego z [żądanymi właściwościami](#desired-properties) do synchronizowania konfiguracji lub warunku urządzenia. Raportowane właściwości mogą być ustawiane tylko przez [aplikację urządzenia](#device-app) i mogą być odczytywane i wysyłane przez [aplikację zaplecza](#back-end-app).

## <a name="resource-group"></a>Grupa zasobów

[Azure Resource Manager](#azure-resource-manager) używa grup zasobów w celu pogrupowania powiązanych zasobów ze sobą. Grupy zasobów można używać do wykonywania operacji na wszystkich zasobach grupy jednocześnie.

## <a name="retry-policy"></a>Zasady ponawiania

Zasady ponawiania służą do obsługi [błędów przejściowych](/azure/architecture/best-practices/transient-faults) podczas łączenia się z usługą w chmurze.

## <a name="routing-rules"></a>Reguły routingu

Można skonfigurować [reguły routingu](iot-hub-devguide-messages-read-custom.md) w centrum IoT Hub, aby kierować komunikaty z urządzenia do chmury do [wbudowanego punktu końcowego](#built-in-endpoints) lub do [niestandardowych punktów końcowych](#custom-endpoints) w celu przetworzenia przez zaplecze rozwiązania.

## <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN to protokół, który jest wykorzystywany przez protokół AMQP do transferowania tokenów zabezpieczających.

## <a name="service-rest-api"></a>Interfejs API REST usługi

Możesz użyć [interfejsu API REST usługi](https://docs.microsoft.com/rest/api/iothub/service/configuration) z zaplecza rozwiązania do zarządzania urządzeniami. Interfejs API umożliwia pobieranie i aktualizowanie właściwości [bliźniaczych urządzeń](#device-twin) , wywoływanie [metod bezpośrednich](#direct-method)i planowanie [zadań](#job). Zazwyczaj należy użyć jednego z [zestawów SDK usług](#azure-iot-service-sdks) wyższego poziomu, jak pokazano w samouczkach IoT Hub.

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego (SAS) są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Uwierzytelnianie SAS ma dwa składniki: _zasady dostępu współdzielonego_ i _sygnatura dostępu współdzielonego_ (często nazywane tokenem). Urządzenie używa sygnatury dostępu współdzielonego do uwierzytelniania w usłudze IoT Hub. [Aplikacje zaplecza](#back-end-app) również używają sygnatury dostępu współdzielonego do uwierzytelniania za pomocą punktów końcowych mających dostęp do usługi w usłudze IoT Hub. Zazwyczaj należy uwzględnić token sygnatury dostępu współdzielonego w [parametrach połączenia](#connection-string) używanych przez aplikację w celu nawiązania połączenia z usługą IoT Hub.

## <a name="shared-access-policy"></a>Zasady dostępu współdzielonego

Zasady dostępu współdzielonego określają uprawnienia przyznane każdemu użytkownikowi, który ma prawidłowy [klucz podstawowy lub pomocniczy](#primary-and-secondary-keys) skojarzony z tymi zasadami. Możesz zarządzać zasadami dostępu współdzielonego i kluczami dla centrum w [portalu](#azure-portal).

## <a name="simulated-device"></a>Symulowane urządzenie

Dla wygody wiele samouczków IoT Hub korzysta z symulowanych urządzeń, aby umożliwić uruchamianie przykładów na komputerze lokalnym. Z kolei [urządzenie fizyczne](#physical-device) to rzeczywiste urządzenie, takie jak Raspberry Pi, które nawiązuje połączenie z usługą IoT Hub.

## <a name="solution"></a>Rozwiązanie
_Rozwiązanie_ może odwoływać się do rozwiązania programu Visual Studio, które zawiera co najmniej jeden projekt. _Rozwiązanie_ może również odnosić się do rozwiązania IoT, które obejmuje takie elementy, jak urządzenia, [aplikacje urządzenia](#device-app), centrum IoT, inne usługi platformy Azure i [Aplikacje zaplecza](#back-end-app).

## <a name="subscription"></a>Subskrypcja

Na subskrypcję platformy Azure odbywa się rozliczanie. Każdy tworzony zasób platformy Azure lub usługa platformy Azure jest skojarzona z jedną subskrypcją. Wiele przydziałów stosuje się również na poziomie subskrypcji.

## <a name="system-properties"></a>Właściwości systemu

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md)właściwości systemu są tylko do odczytu i zawierają informacje dotyczące użycia urządzenia, takie jak czas ostatniego działania i stan połączenia.

## <a name="tags"></a>Tagi

W kontekście [sznurka urządzenia](iot-hub-devguide-device-twins.md)Tagi są metadane urządzenia przechowywane i pobierane przez zaplecze rozwiązania w postaci dokumentu JSON. Tagi nie są widoczne dla aplikacji na urządzeniu.

## <a name="telemetry"></a>Telemetria

Urządzenia zbierają dane telemetryczne, takie jak szybkość wiatru lub temperatura, i używają komunikatów punktów danych w celu wysyłania danych telemetrycznych do centrum IoT.

## <a name="token-service"></a>Usługa tokenu

Za pomocą usługi tokenu można zaimplementować mechanizm uwierzytelniania dla urządzeń. Używa [zasad dostępu współdzielonego](#shared-access-policy) IoT Hub z uprawnieniami **DeviceConnect** do tworzenia tokenów z *zakresem urządzeń* . Te tokeny umożliwiają urządzeniu łączenie się z Centrum IoT Hub. Urządzenie używa niestandardowego mechanizmu uwierzytelniania do uwierzytelniania za pomocą usługi tokenu. Jeśli urządzenie zostanie pomyślnie uwierzytelnione, usługa tokenów wystawia token sygnatury dostępu współdzielonego, aby urządzenie korzystało z Centrum IoT.

## <a name="twin-queries"></a>Zapytania bliźniaczye

[Zapytania wieloosiowe urządzenia i modułu](iot-hub-devguide-query-language.md) używają języka zapytań IoT Hub, takich jak SQL, do pobierania informacji z urządzenia bliźniaczych reprezentacji lub modułu bliźniaczych reprezentacji. Możesz użyć tego samego IoT Hub języka zapytań, aby pobrać informacje o [zadaniu](#job) uruchomionym w centrum IoT.

## <a name="twin-synchronization"></a>Synchronizacja przędzy

Synchronizacja przędzy używa [żądanych właściwości](#desired-properties) w urządzeniu bliźniaczych reprezentacji lub module bliźniaczych reprezentacji w celu skonfigurowania urządzeń lub modułów i pobrania przez nich [raportowanych właściwości](#reported-properties) do sklepu w ramach sznurka.

## <a name="x509-client-certificate"></a>Certyfikat klienta X. 509

Urządzenie może używać certyfikatu X. 509 do uwierzytelniania za pomocą [IoT Hub](#iot-hub). Użycie certyfikatu X. 509 jest alternatywą dla korzystania z [tokenu SAS](#shared-access-signature).
