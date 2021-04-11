---
title: Technologie i rozwiązania Internetu rzeczy (IoT) na platformie Azure
description: Opisuje zbiór technologii i usług, za pomocą których można utworzyć rozwiązanie Azure IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 311f8164803dd12664aa193f08c8a1095ee07e31
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168709"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Jakie technologie i usługi platformy Azure mogą służyć do tworzenia rozwiązań IoT?

Technologie i usługi Azure IoT umożliwiają tworzenie różnorodnych rozwiązań IoT, które umożliwiają transformację cyfrową w organizacji. Możesz na przykład:

* Za pomocą [usługi Azure IoT Central](https://apps.azureiotcentral.com), zarządzanej platformy aplikacji IoT, można tworzyć i wdrażać bezpieczne rozwiązanie IoT klasy korporacyjnej. IoT Central funkcje kolekcji szablonów aplikacji specyficznych dla branż, takich jak handel detaliczny i opieka medyczna, w celu przyspieszenia procesu tworzenia rozwiązań.
* Użyj usług Azure IoT platform, takich jak [azure IoT Hub](../iot-hub/about-iot-hub.md) i [zestawy SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) , aby utworzyć niestandardowe rozwiązanie IoT od podstaw.

![Technologie, usługi i rozwiązania Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[Platforma aplikacji IoT Central](https://apps.azureiotcentral.com) zmniejsza obciążenie i koszty tworzenia rozwiązań IoT klasy korporacyjnej oraz zarządzania nimi. Dostosowywalny interfejs użytkownika sieci Web IoT Central w programie umożliwia monitorowanie warunków urządzenia, tworzenie reguł i zarządzanie milionami urządzeń oraz ich danych w całym cyklu życia. Powierzchnia interfejsu API w ramach IoT Central zapewnia programistyczny dostęp do konfiguracji i współpracy z rozwiązaniem IoT.

Azure IoT Central to w pełni zarządzana platforma aplikacji, za pomocą której można tworzyć niestandardowe rozwiązania IoT. IoT Central używa szablonów aplikacji do tworzenia rozwiązań. Istnieją szablony dla rozwiązań ogólnych i dla określonych branż, takich jak energia, opieka zdrowotna, instytucje rządowe i detaliczne. Szablony aplikacji IoT Central umożliwiają wdrożenie IoT Central aplikacji w ciągu kilku minut, które można dostosować za pomocą motywów, pulpitów nawigacyjnych i widoków.

Wybierz urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://devicecatalog.azure.com) , aby szybko nawiązać połączenie z rozwiązaniem. Użyj interfejsu użytkownika sieci Web IoT Central, aby monitorować urządzenia i zarządzać nimi, aby zapewnić ich dobrą kondycję i łączność. Użyj łączników i interfejsów API, aby zintegrować aplikację IoT Central z innymi aplikacjami biznesowymi.

Jako w pełni zarządzana platforma aplikacji IoT Central ma prosty, przewidywalny model cen.

## <a name="azure-iot-solution-accelerators"></a>Akceleratory rozwiązań Azure IoT

[Akceleratory rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com) to zbiór dostosowywalnych rozwiązań klasy korporacyjnej. Te rozwiązania można wdrażać w miarę ich tworzenia lub opracowywać niestandardowe rozwiązanie IoT przy użyciu kodu źródłowego środowiska Java lub .NET typu open source.

Akceleratory rozwiązań usługi Azure IoT zapewniają wysoki poziom kontroli nad rozwiązaniem IoT. Akceleratory rozwiązań obejmują wstępnie skompilowane rozwiązania dla typowych scenariuszy IoT, które można wdrożyć w ramach subskrypcji platformy Azure w ciągu kilku minut. Scenariusze obejmują:

* Połączona fabryka
* Symulacja urządzenia

Baza kodu open source dla wszystkich akceleratorów rozwiązań jest dostępna w witrynie GitHub. Pobierz kod, aby dostosować Akcelerator rozwiązania w celu spełnienia określonych wymagań IoT.

Akceleratory rozwiązań używają usług platformy Azure, takich jak Azure IoT Hub i Azure Storage, które muszą być zarządzane w ramach subskrypcji platformy Azure.

## <a name="custom-solutions"></a>Niestandardowe rozwiązania

Aby skompilować rozwiązanie IoT od podstaw lub zwiększyć rozwiązanie utworzone przy użyciu IoT Central lub akceleratora rozwiązań, Użyj co najmniej jednej z następujących technologii i usług Azure IoT:

### <a name="devices"></a>Urządzenia

Opracowuj swoje urządzenia IoT przy użyciu jednej z [zestawów startowych usługi Azure IoT](https://devicecatalog.azure.com/kits) lub wybierz urządzenie, które ma być używane z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://devicecatalog.azure.com). Zaimplementuj kod osadzony przy użyciu [zestawów SDK urządzeń](../iot-hub/iot-hub-devguide-sdks.md)Open Source. Zestawy SDK urządzeń obsługują wiele systemów operacyjnych, takich jak Linux, Windows i systemy operacyjne w czasie rzeczywistym. Istnieją zestawy SDK dla wielu języków programowania, takich jak [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)i [Python](https://github.com/Azure/azure-iot-sdk-python).

Możesz bardziej uprościć sposób tworzenia kodu osadzonego dla urządzeń za pomocą usługi [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) . Plug and Play IoT umożliwia deweloperom rozwiązań integrację urządzeń z ich rozwiązaniami bez konieczności pisania kodu osadzonego. Na początku Plug and Play IoT jest schemat _modelu możliwości urządzenia_ , który opisuje możliwości urządzeń. Użyj modelu możliwości urządzenia, aby wygenerować osadzony kod urządzenia i skonfigurować rozwiązanie oparte na chmurze, takie jak aplikacja IoT Central.

[Azure IoT Edge](../iot-edge/about-iot-edge.md) pozwala odciążać części obciążenia IoT z usług w chmurze platformy Azure na urządzeniach. IoT Edge może ograniczyć opóźnienia w rozwiązaniu, zmniejszyć ilość danych wymienianych przez urządzenia z chmurą i włączyć scenariusze międzywierszowe. Urządzeniami IoT Edge można zarządzać z poziomu IoT Central i niektórych akceleratorów rozwiązań.

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) to zabezpieczona platforma aplikacji wysokiego poziomu z wbudowanymi funkcjami komunikacji i zabezpieczeń dla urządzeń połączonych z Internetem. Obejmuje ona bezpieczną jednostkę mikrokontrolerów, niestandardowy system operacyjny oparty na systemie Linux oraz usługę zabezpieczeń opartą na chmurze, która zapewnia ciągły, odnawialny zabezpieczenia.

### <a name="cloud-connectivity"></a>Łączność z chmurą

Usługa [Azure IoT Hub](../iot-hub/about-iot-hub.md) umożliwia niezawodne i bezpieczne komunikację dwukierunkową między milionami urządzeń IoT i rozwiązaniami w chmurze. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) to usługa pomocnika dla IoT Hub. Usługa zapewnia nieprzerwane, samoobsługowe Inicjowanie obsługi urządzeń w odpowiednim Centrum IoT Hub bez konieczności interwencji człowieka. Te możliwości pozwalają klientom na udostępnianie milionów urządzeń w bezpieczny i skalowalny sposób.

IoT Hub jest głównym składnikiem akceleratorów rozwiązań i można go użyć do spełnienia wyzwań związanych z implementacją IoT, takich jak:

* Utrzymywanie łączności zapewniającej przepływ dużej ilości danych między urządzeniami i zarządzanie nimi.
* Pozyskiwanie dużej ilości danych telemetrycznych.
* Sterowanie urządzeniami i kontrola nad nimi.
* Wymuszanie zabezpieczeń urządzeń.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Mostkowanie przerwy między fizycznym i cyfrowym światem

[Azure Digital bliźniaczych reprezentacji](../digital-twins/overview.md) to usługa IoT, która umożliwia modelowanie środowiska fizycznego. Używa grafu analizy przestrzennej do modelowania relacji między osobami, miejscami i urządzeniami. Dzięki rozwiązaniom danych w ramach sieci cyfrowych i fizycznych można tworzyć rozwiązania z obsługą kontekstową.

IoT Central używa cyfrowego bliźniaczych reprezentacji do synchronizowania urządzeń i danych w świecie rzeczywistym z modelami cyfrowymi, które umożliwiają użytkownikom monitorowanie tych połączonych urządzeń i zarządzanie nimi.

### <a name="data-and-analytics"></a>Dane i analiza

Urządzenia IoT zazwyczaj generują duże ilości danych szeregów czasowych, takich jak odczyty temperatury z czujników. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) może nawiązać połączenie z usługą IoT Hub, odczytać strumień danych telemetrycznych z urządzeń, przechowywać te dane i umożliwić wykonywanie zapytań i wizualizację.

[Azure Maps](../azure-maps/index.yml) jest kolekcją usług geoprzestrzennych, które używają danych do odwzorowania w celu zapewnienia dokładnego kontekstu geograficznego w aplikacjach sieci Web i mobilnych. Możesz użyć interfejsu API REST, opartej na sieci Web kontrolki JavaScript lub Android SDK do kompilowania aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać praktyczne środowisko, wypróbuj jeden z przewodników szybki start:

- [Tworzenie aplikacji IoT Central platformy Azure](../iot-central/core/quick-deploy-iot-central.md)
- [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md)
