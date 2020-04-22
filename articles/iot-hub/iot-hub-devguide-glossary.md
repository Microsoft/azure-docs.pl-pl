---
title: Glosariusz terminów usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — glosariusz wyjaśniający niektóre typowe terminy używane w artykułach usługi Azure IoT Hub.
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
ms.openlocfilehash: 5c59ddf046e7ffe936b097878d5d049b43ea8aec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729026"
---
# <a name="glossary-of-iot-hub-terms"></a>Słowniczek terminów IoT Hub

W tym artykule wymieniono niektóre typowe terminy używane w artykułach usługi IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Zaawansowany protokół kolejkowania wiadomości

[Protokół AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) jest jednym z protokołów obsługi wiadomości, które [usługa IoT Hub](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi protokołów obsługi usługi IoT Hub, zobacz [Wysyłanie i odbieranie wiadomości za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatyczne zarządzanie urządzeniami

Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem flotami dużych urządzeń w całym cyklu ich życia. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestaw urządzeń na podstawie ich właściwości, definiować żądaną konfigurację i pozwolić centrum IoT hub na aktualizowanie urządzeń za każdym razem, gdy wchodzą w zakres.  Składa się z [automatycznych konfiguracji urządzeń](iot-hub-auto-device-config.md) i [automatycznych wdrożeń usługi IoT Edge.](../iot-edge/how-to-deploy-monitor.md)

## <a name="automatic-device-configuration"></a>Automatyczna konfiguracja urządzenia

Zaplecza rozwiązania można użyć [automatycznych konfiguracji urządzeń,](iot-hub-auto-device-config.md) aby przypisać żądane właściwości do zestawu [bliźniaczych urządzeń](#device-twin) i stan raportu przy użyciu metryk systemowych i metryk niestandardowych. 

## <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

Klasyczna [interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md) to wieloplatformowe narzędzie do poleceń oparte na otwartym, opartym na powłoce do tworzenia zasobów na platformie Microsoft Azure i zarządzania nimi. Ta wersja interfejsu wiersza polecenia powinna być używana tylko dla klasycznych wdrożeń.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[Narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) to wieloplatformowe narzędzie do poleceń oparte na otwartym, opartym na powłoce do tworzenia zasobów na platformie Microsoft Azure i zarządzania nimi.

## <a name="azure-iot-device-sdks"></a>Zestaw SDK urządzeń IoT platformy Azure

Dostępne są _zestawy SDK urządzeń_ dla wielu języków, które umożliwiają tworzenie [aplikacji urządzeń,](#device-app) które współdziałają z centrum IoT hub. Samouczki Centrum IoT pokazują, jak używać tych zestawu SDK urządzeń. Kod źródłowy i więcej informacji na temat sdk urządzeń można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-explorer"></a>Eksplorator IoT platformy Azure

[Eksplorator IoT platformy Azure](https://github.com/Azure/azure-iot-explorer) służy do wyświetlania danych telemetrycznych wysyłanych przez urządzenie, pracy z właściwościami urządzenia i poleceń wywołania. Explorer może również używać do interakcji z urządzeniami i testowania ich oraz do zarządzania urządzeniami typu plug and play.

## <a name="azure-iot-service-sdks"></a>Zestaw SDK usług IoT platformy Azure

Dostępne są _zestawy SDK usług_ dla wielu języków, które umożliwiają tworzenie [aplikacji zaplecza,](#back-end-app) które współdziałają z centrum IoT hub. Samouczki Centrum IoT pokazują, jak korzystać z tych zestawu SDK usług. Kod źródłowy i więcej informacji na temat sdk usług można znaleźć w tym [repozytorium](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-tools"></a>Narzędzia usługi Azure IoT

[Narzędzia Azure IoT to](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) wieloplatformowe rozszerzenie kodu programu Visual Studio typu open source, które ułatwia zarządzanie usługą Azure IoT Hub i urządzeniami w programie VS Code. Dzięki narzędziom Usługi Azure IoT deweloperzy IoT mogą z łatwością tworzyć projekt IoT w programie VS Code.

## <a name="azure-portal"></a>Azure Portal

Witryna [microsoft azure portal](https://portal.azure.com) to centralne miejsce, w którym można aprowizować zasoby platformy Azure i zarządzać nimi. Organizuje swoją zawartość za pomocą _ostrzy._

## <a name="azure-powershell"></a>Azure PowerShell

[Program Azure PowerShell](/powershell/azure/overview) to zbiór poleceń cmdlet, których można używać do zarządzania platformą Azure za pomocą programu Windows PowerShell. Polecenia cmdlet służy do tworzenia, testowania, wdrażania i zarządzania rozwiązaniami i usługami dostarczanymi za pośrednictwem platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Usługa Azure Resource Manager](../azure-resource-manager/management/overview.md) umożliwia pracę z zasobami w rozwiązaniu jako grupa. Zasoby rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej, skoordynowanej operacji.

## <a name="azure-service-bus"></a>Azure Service Bus

[Usługa Service Bus](../service-bus/index.md) zapewnia komunikację w chmurze z wiadomościami dla przedsiębiorstw i komunikacją przekazywania, która ułatwia łączenie rozwiązań lokalnych z chmurą. Niektóre samouczki Usługi IoT Hub korzystają z [kolejek](../service-bus-messaging/service-bus-messaging-overview.md)usługi Service Bus .

## <a name="azure-storage"></a>Azure Storage

[Usługa Azure Storage](../storage/common/storage-introduction.md) to rozwiązanie magazynu w chmurze. Zawiera usługę magazynu obiektów blob, której można używać do przechowywania danych obiektów nieustrukturyzowanych. Niektóre samouczki Usługi IoT Hub używają magazynu obiektów blob.

## <a name="back-end-app"></a>Aplikacja zaplecza

W kontekście [Usługi IoT Hub](#iot-hub)aplikacja zaplecza to aplikacja, która łączy się z jednym z punktów końcowych skierowanych do usługi w centrum IoT hub. Na przykład aplikacja zaplecza może pobierać wiadomości [z urządzenia do chmury](#device-to-cloud) lub zarządzać [rejestrem tożsamości.](#identity-registry) Zazwyczaj aplikacja zaplecza działa w chmurze, ale w wielu samouczkach aplikacje zaplecza są aplikacje konsoli uruchomione na lokalnym komputerze deweloperskim.

## <a name="built-in-endpoints"></a>Wbudowane punkty końcowe

Każdy centrum IoT zawiera wbudowany [punkt końcowy,](iot-hub-devguide-endpoints.md) który jest zgodny z Centrum zdarzeń. Można użyć dowolnego mechanizmu, który współpracuje z Centrum zdarzeń do odczytu komunikatów z urządzenia do chmury z tego punktu końcowego.

## <a name="cloud-gateway"></a>Brama w chmurze

Brama w chmurze umożliwia łączność dla urządzeń, które nie mogą łączyć się bezpośrednio z [Centrum IoT Hub.](#iot-hub) Brama w chmurze jest hostowana w chmurze w przeciwieństwie do [bramy polowej,](#field-gateway) która działa lokalnie na urządzeniach. Typowym przypadkiem użycia bramy w chmurze jest zaimplementowanie tłumaczenia protokołu dla urządzeń.

## <a name="cloud-to-device"></a>Chmura na urządzenie

Odnosi się do wiadomości wysyłanych z centrum IoT hub do podłączonego urządzenia. Często te komunikaty są poleceniami, które instruują urządzenie do podjęcia akcji. Aby uzyskać więcej informacji, zobacz [Wysyłanie i odbieranie wiadomości za pomocą Usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfigurowanie

W kontekście [automatycznej konfiguracji urządzenia](iot-hub-auto-device-config.md)konfiguracja w centrum IoT Hub definiuje żądaną konfigurację dla zestawu bliźniaczych urządzeń i udostępnia zestaw metryk do raportowania stanu i postępu.

## <a name="connection-string"></a>Parametry połączenia

Ciągi połączeń w kodzie aplikacji służy do hermetyzacji informacji wymaganych do łączenia się z punktem końcowym. Parametry połączenia zazwyczaj zawiera adres punktu końcowego i informacje o zabezpieczeniach, ale formaty ciągu połączenia różnią się w zależności od usługi. Istnieją dwa typy ciągu połączenia skojarzone z usługą Centrum IoT:

- *Parametry połączenia urządzenia* umożliwiają urządzeniom łączenie się z punktami końcowymi skierowanymi do urządzenia w centrum IoT hub.

- *Parametry połączenia usługi IoT Hub* umożliwiają aplikacjom zaplecza łączenie się z punktami końcowymi skierowanymi do usługi w centrum IoT hub.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Można utworzyć [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md) w centrum IoT hub, aby dostarczać wiadomości wywoływane przez [regułę routingu](#routing-rules). Niestandardowe punkty końcowe łączą się bezpośrednio z Centrum zdarzeń, kolejką usługi Service Bus lub tematem usługi Service Bus.

## <a name="custom-gateway"></a>Brama niestandardowa

Brama umożliwia łączność dla urządzeń, które nie mogą łączyć się bezpośrednio z [Centrum IoT](#iot-hub)Hub . Za pomocą usługi Azure IoT Edge można tworzyć bramy niestandardowe, które implementują niestandardową logikę do obsługi wiadomości, konwersji protokołów niestandardowych i innych przetwarzania na krawędzi.

## <a name="data-point-message"></a>Komunikat o punkcie danych

Komunikat punktu danych to komunikat [między urządzeniami do chmury,](#device-to-cloud) który zawiera dane [telemetryczne,](#telemetry) takie jak prędkość wiatru lub temperatura.

## <a name="desired-configuration"></a>Żądana konfiguracja

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)żądana konfiguracja odnosi się do pełnego zestawu właściwości i metadanych w bliźniaczej reprezentacji urządzenia, które powinny być synchronizowane z urządzeniem.

## <a name="desired-properties"></a>Żądane właściwości

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)żądane właściwości to podsekcja bliźniaczej reprezentacji urządzenia, która jest używana ze [zgłoszonymi właściwościami](#reported-properties) do synchronizowania konfiguracji lub warunku urządzenia. Żądane właściwości mogą być ustawiane tylko przez [aplikację zaplecza](#back-end-app) i są obserwowane przez [aplikację urządzenia](#device-app).

## <a name="device-to-cloud"></a>Urządzenie do chmury

Odnosi się do wiadomości wysyłanych z podłączonego urządzenia do [usługi IoT Hub](#iot-hub). Te wiadomości mogą być [wiadomościami punktu danych](#data-point-message) lub [interaktywnymi.](#interactive-message) Aby uzyskać więcej informacji, zobacz [Wysyłanie i odbieranie wiadomości za pomocą Usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Urządzenie

W kontekście IoT urządzenie jest zazwyczaj na małą skalę, autonomiczne urządzenie obliczeniowe, które mogą zbierać dane lub kontrolować inne urządzenia. Na przykład urządzenie może być urządzeniem monitorującym środowisko lub kontrolerem systemów nawadniania i wentylacji w szklarni. [Katalog urządzeń](https://catalog.azureiotsolutions.com/) zawiera listę urządzeń sprzętowych certyfikowanych do pracy z [Centrum IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikacja urządzenia

Aplikacja urządzenia działa na [urządzeniu](#device) i obsługuje komunikację z [centrum IoT hub](#iot-hub). Zazwyczaj podczas implementowania aplikacji urządzenia używane są jedno z [zestawów SDK urządzenia Usługi Azure IoT.](#azure-iot-device-sdks) W wielu samouczkach IoT używasz [symulowanego urządzenia](#simulated-device) dla wygody.

## <a name="device-condition"></a>Stan urządzenia

Odnosi się do informacji o stanie urządzenia, takich jak aktualnie używana metoda łączności, zgłoszona przez [aplikację urządzenia.](#device-app) [Aplikacje na urządzenia](#device-app) mogą również zgłaszać swoje możliwości. Można zbadać informacje o stanie i możliwości przy użyciu bliźniacze urządzenia.

## <a name="device-data"></a>Dane urządzenia

Dane urządzenia odnoszą się do danych dotyczących urządzeń przechowywanych w [rejestrze tożsamości](#identity-registry)usługi IoT Hub . Istnieje możliwość importowania i eksportowania tych danych.

## <a name="device-explorer"></a>Device Explorer

Eksplorator urządzeń został zastąpiony [eksploratorem IoT usługi Azure](https://github.com/Azure/azure-iot-explorer), który służy do wyświetlania danych telemetrycznych wysyłanych przez urządzenie, pracy z właściwościami urządzenia i poleceń wywołania. Explorer może również używać do interakcji z urządzeniami i testowania ich oraz do zarządzania urządzeniami typu plug and play.

## <a name="device-identity"></a>Tożsamość urządzenia

Tożsamość urządzenia jest unikatowym identyfikatorem przypisanym do każdego urządzenia zarejestrowanego w [rejestrze tożsamości.](#identity-registry)

## <a name="device-management"></a>Zarządzanie urządzeniami

Zarządzanie urządzeniami obejmuje pełny cykl życia związany z zarządzaniem urządzeniami w rozwiązaniu IoT, w tym planowaniem, inicjowaniu obsługi administracyjnej, konfigurowaniem, monitorowaniem i wycofywaniem.

## <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami

[Centrum IoT hub](#iot-hub) umożliwia typowe wzorce zarządzania urządzeniami, w tym ponowne uruchamianie, resetowanie ustawień fabrycznych i wykonywanie aktualizacji oprogramowania układowego na urządzeniach.

## <a name="device-rest-api"></a>Interfejs API REST urządzenia

Za pomocą [interfejsu API interfejsu REST urządzenia](https://docs.microsoft.com/rest/api/iothub/device) z urządzenia można wysyłać wiadomości z urządzenia do chmury do centrum IoT hub i [odbierać komunikaty](#cloud-to-device) z chmury do urządzenia z centrum IoT hub. Zazwyczaj należy użyć jednego z zestawu [SDK urządzeń](#azure-iot-device-sdks) wyższego poziomu, jak pokazano w samouczkach Usługi IoT Hub.

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń

Inicjowanie obsługi administracyjnej urządzenia to proces dodawania początkowych [danych urządzenia](#device-data) do magazynów w rozwiązaniu. Aby włączyć połączenie nowego urządzenia z koncentratorem, należy dodać identyfikator urządzenia i klucze do [rejestru tożsamości](#identity-registry)usługi IoT Hub . W ramach procesu inicjowania obsługi administracyjnej może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań.

## <a name="device-twin"></a>Bliźniak urządzenia

[Bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md) jest dokument JSON, który przechowuje informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. [Usługi IoT Hub](#iot-hub) utrzymuje bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które aprowizować w centrum IoT hub. Bliźniacze urządzenia umożliwiają synchronizację warunków i konfiguracji [urządzenia](#device-condition) między urządzeniem a zapleczem rozwiązania. Można wysyłać zapytania do bliźniąt bliźniąt urządzeń, aby zlokalizować określone urządzenia i zbadać stan długotrwałych operacji.

## <a name="direct-method"></a>Metoda bezpośrednia

[Metoda bezpośrednia](iot-hub-devguide-direct-methods.md) jest sposobem wyzwalania metody do wykonania na urządzeniu przez wywołanie interfejsu API w centrum IoT hub.

## <a name="endpoint"></a>Endpoint

Centrum IoT udostępnia wiele [punktów końcowych,](iot-hub-devguide-endpoints.md) które umożliwiają aplikacjom łączenie się z centrum IoT hub. Istnieją punkty końcowe skierowane do urządzenia, które umożliwiają urządzeniom wykonywanie operacji, takich jak wysyłanie komunikatów [z urządzenia do chmury](#device-to-cloud) i odbieranie komunikatów z chmury do [urządzenia.](#cloud-to-device) Istnieją punkty końcowe zarządzania oparte na usługach, które umożliwiają [aplikacjom zaplecza](#back-end-app) wykonywanie operacji, takich jak zarządzanie [tożsamościami urządzeń](#device-identity) i zarządzanie bliźniaczymi urządzeniami. Istnieją [wbudowane punkty końcowe](#built-in-endpoints) przeznaczone do obsługi usług do odczytywania komunikatów z urządzenia do chmury. Można tworzyć [niestandardowe punkty końcowe](#custom-endpoints) do odbierania wiadomości z urządzenia do chmury wysyłanych przez [regułę routingu](#routing-rules).

## <a name="event-hubs-service"></a>Usługa Centrum zdarzeń

[Usługi Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) to wysoce skalowalna usługa transferu danych przychodzących, która może pozyskiwania milionów zdarzeń na sekundę. Usługa umożliwia przetwarzanie i analizowanie ogromnych ilości danych wytwarzanych przez podłączone urządzenia i aplikacje. Aby porównać z usługą Centrum IoT, zobacz [Porównanie usługi Azure IoT Hub i usługi Azure Event Hubs.](iot-hub-compare-event-hubs.md)

## <a name="event-hub-compatible-endpoint"></a>Punkt końcowy zgodny z Centrum zdarzeń

Aby odczytać [komunikaty z urządzenia do chmury](#device-to-cloud) wysyłane do centrum IoT hub, można połączyć się z punktem końcowym w centrum i użyć dowolnej metody zgodnej z Centrum zdarzeń do odczytu tych wiadomości. Metody zgodne z Centrum zdarzeń obejmują korzystanie z [zestawów SDK centrów zdarzeń](../event-hubs/event-hubs-programming-guide.md) i [usługi Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md)

## <a name="field-gateway"></a>Brama polowa

Brama polowa umożliwia łączność dla urządzeń, które nie mogą łączyć się bezpośrednio z [centrum IoT Hub](#iot-hub) i jest zazwyczaj wdrażany lokalnie z urządzeniami. Aby uzyskać więcej informacji, zobacz [Co to jest Usługa Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Bezpłatne konto

Możesz utworzyć [bezpłatne konto platformy Azure,](https://azure.microsoft.com/pricing/free-trial/) aby ukończyć samouczki usługi IoT Hub i eksperymentować z usługą Centrum IoT (i innymi usługami platformy Azure).

## <a name="gateway"></a>Brama

Brama umożliwia łączność dla urządzeń, które nie mogą łączyć się bezpośrednio z [Centrum IoT](#iot-hub)Hub . Zobacz [też: Brama polowa,](#field-gateway) [Brama w chmurze](#cloud-gateway)i [Brama niestandardowa](#custom-gateway).

## <a name="identity-registry"></a>Rejestr tożsamości

[Rejestr tożsamości](iot-hub-devguide-identity-registry.md) jest wbudowanym składnikiem centrum IoT hub, który przechowuje informacje o poszczególnych urządzeniach, które mogą łączyć się z centrum IoT hub.

## <a name="interactive-message"></a>Interaktywna wiadomość

Interaktywna wiadomość to komunikat [chmury do urządzenia,](#cloud-to-device) który wyzwala natychmiastową akcję w zapleczu rozwiązania. Na przykład urządzenie może wysłać alarm o awarii, która powinna być automatycznie zalogowana do systemu CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Usługa IoT Hub

Usługa IoT Hub to w pełni zarządzana usługa platformy Azure, która umożliwia niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Aby uzyskać więcej informacji, zobacz [Co to jest Usługa Azure IoT Hub?](about-iot-hub.md) Korzystając z [subskrypcji platformy Azure,](#subscription)można utworzyć centra IoT do obsługi obciążeń obsługi wiadomości IoT.

## <a name="iot-hub-metrics"></a>Metryki usługi IoT Hub

[Metryki usługi IoT Hub](iot-hub-metrics.md) zapewniają dane o stanie centrów IoT w [subskrypcji platformy Azure.](#subscription) Metryki usługi IoT Hub umożliwiają ocenę ogólnej kondycji usługi i podłączonych do niej urządzeń. Metryki usługi IoT Hub mogą pomóc ci zobaczyć, co dzieje się z centrum IoT hub i zbadać problemy z przyczynami bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

## <a name="iot-hub-query-language"></a>Język kwerend centrum IoT

[Język zapytań Centrum IoT](iot-hub-devguide-query-language.md) jest językiem sql-like, który umożliwia wykonywanie zapytań bliźniaczych [](#job) i urządzeń.

## <a name="iot-hub-resource-rest-api"></a>Interfejs API REST zasobów usługi IoT Hub

Za pomocą [interfejsu API REST zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) można zarządzać centrami IoT w [ramach subskrypcji platformy Azure,](#subscription) wykonując operacje, takie jak tworzenie, aktualizowanie i usuwanie koncentratorów.

## <a name="iot-solution-accelerators"></a>Akceleratory rozwiązań IoT

Akceleratory rozwiązań Azure IoT łączą wiele usług platformy Azure w rozwiązania. Te rozwiązania umożliwiają szybkie rozpoczęcie pracy dzięki kompleksowym implementacjom typowych scenariuszy IoT. Aby uzyskać więcej informacji, zobacz [Co to są akceleratory rozwiązań Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure 

[Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) to narzędzie wiersza polecenia między platformami. Narzędzie umożliwia zarządzanie urządzeniami w [rejestrze tożsamości,](#identity-registry)wysyłanie i odbieranie wiadomości i plików z urządzeń oraz monitorowanie operacji centrum IoT.

## <a name="job"></a>Zadanie

Zaplecze rozwiązania można używać [zadań](iot-hub-devguide-jobs.md) do planowania i śledzenia działań na zestaw urządzeń zarejestrowanych w centrum IoT hub. Działania obejmują aktualizowanie bliźniaczej reprezentacji urządzenia [żądanych właściwości,](#desired-properties)aktualizowanie [bliźniaczych tagów](#tags)urządzenia i wywoływanie [metod bezpośrednich.](#direct-method) [Usługa IoT Hub](#iot-hub) służy również do [importowania i eksportowania](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [rejestru tożsamości](#identity-registry).

## <a name="modules"></a>Moduły

Po stronie urządzenia moduły SDK urządzenia usługi IoT Hub umożliwiają tworzenie [modułów,](iot-hub-devguide-module-twins.md) w których każdy z nich otwiera niezależne połączenie z centrum IoT Hub. Ta funkcja umożliwia używanie oddzielnych obszarów nazw dla różnych składników na urządzeniu.

Tożsamość modułu i bliźniaczej reprezentacji modułu zapewniają takie same możliwości jak [tożsamość urządzenia](#device-identity) i [bliźniaczej reprezentacji urządzenia,](#device-twin) ale z większą szczegółowością. Ta drobna szczegółowość umożliwia urządzeniom, takim jak urządzenia oparte na systemie operacyjnym lub urządzenia układowe zarządzające wieloma składnikami, izolowanie konfiguracji i warunków dla każdego z tych składników.

## <a name="module-identity"></a>Tożsamość modułu

Tożsamość modułu jest unikatowym identyfikatorem przypisanym do każdego modułu, który należy do urządzenia. Tożsamość modułu jest również zarejestrowana w [rejestrze tożsamości](#identity-registry).

## <a name="module-twin"></a>Bliźniaczy moduł

Podobnie jak bliźniacza bliźniaczej reprezentacji urządzenia, bliźniaczej reprezentacji modułu jest dokument JSON, który przechowuje informacje o stanie modułu, takie jak metadane, konfiguracje i warunki. Usługa IoT Hub utrzymuje bliźniacząt modułu dla każdej tożsamości modułu, który aprowizować w ramach tożsamości urządzenia w centrum IoT hub. Bliźniacze moduły umożliwiają synchronizację warunków i konfiguracji modułu między modułem a zapleczem rozwiązania. Można wysyłać zapytania do bliźniąt bliźniąt modułów, aby zlokalizować określone moduły i zbadać stan długotrwałych operacji.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) jest jednym z protokołów obsługi wiadomości, które [usługa IoT Hub](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi protokołów obsługi usługi IoT Hub, zobacz [Wysyłanie i odbieranie wiadomości za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorowanie operacji

[Monitorowanie operacji](iot-hub-operations-monitoring.md) usługi IoT Hub umożliwia monitorowanie stanu operacji w centrum IoT hub w czasie rzeczywistym. [Centrum IoT hub](#iot-hub) śledzi zdarzenia w kilku kategoriach operacji. Można zdecydować się na wysyłanie zdarzeń z jednej lub więcej kategorii do punktu końcowego usługi IoT Hub do przetwarzania. Można monitorować dane pod kątem błędów lub skonfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

## <a name="physical-device"></a>Urządzenie fizyczne

Urządzenie fizyczne to prawdziwe urządzenie, takie jak Raspberry Pi, które łączy się z centrum IoT Hub. Dla wygody wiele samouczków Usługi IoT Hub używa [symulowanych urządzeń,](#simulated-device) aby umożliwić uruchamianie próbek na komputerze lokalnym.

## <a name="primary-and-secondary-keys"></a>Klucze podstawowe i pomocnicze

Po połączeniu się z punktem końcowym skierowanym do urządzenia lub usługi w centrum IoT hub, [parametry połączenia](#connection-string) zawiera klucz, aby udzielić dostępu. Po dodaniu urządzenia do [rejestru tożsamości](#identity-registry) lub dodaniu zasad [dostępu współużytkującego](#shared-access-policy) do centrum usługa generuje klucz podstawowy i pomocniczy. Posiadanie dwóch kluczy umożliwia przewracanie z jednego klucza do drugiego podczas aktualizowania klucza bez utraty dostępu do centrum IoT hub.

## <a name="protocol-gateway"></a>Brama protokołu

Brama protokołu jest zazwyczaj wdrażana w chmurze i zapewnia usługi tłumaczenia protokołów dla urządzeń łączących się z [Centrum IoT](#iot-hub)Hub . Aby uzyskać więcej informacji, zobacz [Co to jest Usługa Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności

Istnieją różne [przydziały,](iot-hub-devguide-quotas-throttling.md) które mają zastosowanie do korzystania z [Usługi IoT Hub,](#iot-hub)wiele przydziałów różnią się w zależności od warstwy centrum IoT hub. [Usługa IoT Hub](#iot-hub) dotyczy również [ograniczania](iot-hub-devguide-quotas-throttling.md) przepustowości do korzystania z usługi w czasie wykonywania.

## <a name="reported-configuration"></a>Zgłoszona konfiguracja

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)zgłoszona konfiguracja odnosi się do pełnego zestawu właściwości i metadanych w bliźniaczej reprezentacji urządzenia, które powinny być zgłaszane do zaplecza rozwiązania.

## <a name="reported-properties"></a>Zgłoszone właściwości

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)zgłaszane właściwości są podsekcja bliźniaczej reprezentacji urządzenia używana z [żądanymi właściwościami](#desired-properties) do synchronizacji konfiguracji lub stanu urządzenia. Zgłoszone właściwości mogą być ustawiane tylko przez [aplikację urządzenia](#device-app) i mogą być odczytywane i wyszukiwane przez [aplikację zaplecza](#back-end-app).

## <a name="resource-group"></a>Grupa zasobów

[Usługa Azure Resource Manager](#azure-resource-manager) używa grup zasobów do grupowanie powiązanych zasobów razem. Za pomocą grupy zasobów można wykonywać operacje na wszystkich zasobach w grupie jednocześnie.

## <a name="retry-policy"></a>Zasady ponawiania

Zasady ponawiania próby są używane do obsługi [błędów przejściowych](/azure/architecture/best-practices/transient-faults) podczas łączenia się z usługą w chmurze.

## <a name="routing-rules"></a>Reguły routingu

Reguły [routingu](iot-hub-devguide-messages-read-custom.md) w centrum IoT hub można skonfigurować do kierowania komunikatów z urządzenia do chmury do [wbudowanego punktu końcowego](#built-in-endpoints) lub do [niestandardowych punktów końcowych](#custom-endpoints) do przetwarzania przez zaplecze rozwiązania.

## <a name="sasl-plain"></a>SASL ZWYKŁY

SASL PLAIN to protokół używany przez protokół AMQP do przesyłania tokenów zabezpieczających.

## <a name="service-rest-api"></a>Interfejs API REST usługi

Aby zarządzać urządzeniami, można użyć [interfejsu API rest](https://docs.microsoft.com/rest/api/iothub/service/configuration) usługi z zaplecza rozwiązania. Interfejs API umożliwia pobieranie i aktualizowanie właściwości [bliźniaczej reprezentacji urządzenia,](#device-twin) wywoływanie [metod bezpośrednich](#direct-method)i planowanie [zadań.](#job) Zazwyczaj należy użyć jednego z zestawu [SDK usług](#azure-iot-service-sdks) wyższego poziomu, jak pokazano w samouczkach Usługi IoT Hub.

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego (SAS) to mechanizm uwierzytelniania oparty na bezpiecznych skrótach lub identyfikatorach URI SHA-256. Uwierzytelnianie sygnatury dostępu współdzielonego ma dwa składniki: _zasady dostępu współdzielonego_ i _sygnaturę dostępu współdzielonego_ (często nazywane tokenem). Urządzenie używa sygnatury dostępu Współdzielonego do uwierzytelniania za pomocą centrum IoT hub. [Aplikacje zaplecza](#back-end-app) również używać Sygnatury dostępu Współdzielonego do uwierzytelniania z punktami końcowymi skierowanymi do usługi w centrum IoT hub. Zazwyczaj dodajesz token sygnatury dostępu Współdzielonego w [ciągu połączenia,](#connection-string) który aplikacja używa do ustanowienia połączenia z centrum IoT hub.

## <a name="shared-access-policy"></a>Zasady dostępu współdzielonego

Zasady dostępu współdzielonego definiują uprawnienia przyznane każdemu, kto ma prawidłowy [klucz podstawowy lub pomocniczy](#primary-and-secondary-keys) skojarzony z tą zasadą. Zasady dostępu współdzielonego i klucze centrum można zarządzać w [portalu](#azure-portal).

## <a name="simulated-device"></a>Symulowane urządzenie

Dla wygody wiele samouczków Usługi IoT Hub używa symulowanych urządzeń, aby umożliwić uruchamianie próbek na komputerze lokalnym. Natomiast urządzenie [fizyczne](#physical-device) jest prawdziwym urządzeniem, takim jak Raspberry Pi, które łączy się z centrum IoT Hub.

## <a name="solution"></a>Rozwiązanie
_Rozwiązanie_ można odwołać się do rozwiązania programu Visual Studio, który zawiera jeden lub więcej projektów. _Rozwiązanie_ może również odnosić się do rozwiązania IoT, które zawiera elementy, takie jak urządzenia, [aplikacje urządzeń,](#device-app)centrum IoT, inne usługi platformy Azure i [aplikacje zaplecza.](#back-end-app)

## <a name="subscription"></a>Subskrypcja

Subskrypcja platformy Azure to miejsce, w którym odbywa się rozliczanie. Każdy utworzony zasób platformy Azure lub używana usługa platformy Azure jest skojarzona z pojedynczą subskrypcją. Wiele przydziałów ma również zastosowanie na poziomie subskrypcji.

## <a name="system-properties"></a>Właściwości systemu

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)właściwości systemu są tylko do odczytu i zawierają informacje dotyczące użycia urządzenia, takie jak czas ostatniej aktywności i stan połączenia.

## <a name="tags"></a>Tagi

W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)tagi są metadanymi urządzenia przechowywanymi i pobieranymi przez zaplecze rozwiązania w postaci dokumentu JSON. Tagi nie są widoczne dla aplikacji na urządzeniu.

## <a name="telemetry"></a>Telemetria

Urządzenia zbierają dane telemetryczne, takie jak prędkość wiatru lub temperatura, i używają komunikatów punktów danych do wysyłania danych telemetrycznych do centrum IoT hub.

## <a name="token-service"></a>Usługa tokenów

Usługi tokenu można użyć do zaimplementowania mechanizmu uwierzytelniania dla urządzeń. Używa [zasad dostępu współdzielonego](#shared-access-policy) usługi IoT Hub z uprawnieniami **DeviceConnect** do tworzenia tokenów *o zakresie urządzenia.* Tokeny te umożliwiają urządzeniu łączenie się z centrum IoT Hub. Urządzenie używa mechanizmu uwierzytelniania niestandardowego do uwierzytelniania za pomocą usługi tokenu. Jeśli urządzenie uwierzytelnia się pomyślnie, usługa tokenu wystawia token Sygnatury dostępu współdzielonego dla urządzenia, aby uzyskać dostęp do centrum IoT hub.

## <a name="twin-queries"></a>Zapytania bliźniacze

[Zapytania bliźniaczej reprezentacji urządzeń i modułów](iot-hub-devguide-query-language.md) używają języka zapytań usługi IoT hub podobnej do języka SQL do pobierania informacji z bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacji modułów. Aby pobrać informacje o [](#job) uruchomieniu w centrum IoT hub, można użyć tego samego języka zapytań usługi IoT Hub.

## <a name="twin-synchronization"></a>Synchronizacja bliźniaczych

Synchronizacja bliźniaczych numerów podwójnych używa [żądanych właściwości](#desired-properties) w bliźniaczych bliźniaczych reprezentacji urządzenia lub bliźniaczych reprezentacji modułów, aby skonfigurować urządzenia lub moduły i pobrać [zgłoszone właściwości](#reported-properties) z nich do przechowywania w bliźniaczej reprezentacji.

## <a name="x509-client-certificate"></a>Certyfikat klienta X.509

Urządzenie może używać certyfikatu X.509 do uwierzytelniania za pomocą [usługi IoT Hub](#iot-hub). Używanie certyfikatu X.509 jest alternatywą dla używania [tokenu Sygnatury dostępu Współdzielonego.](#shared-access-signature)
