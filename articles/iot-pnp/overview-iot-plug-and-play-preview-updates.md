---
title: Co nowego? Odświeżanie w usłudze IoT Plug and Play wersja zapoznawcza | Microsoft Docs
description: Dowiedz się, co nowego w wersji zapoznawczej programu IoT Plug and Play Preview.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: 94cec26d883c29eb4d43d2f244a9a2b8f7aea9dd
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183177"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Odświeżanie podglądu Plug and Play IoT

W tym artykule opisano kluczowe zmiany w zestawach SDK, bibliotekach, narzędziach i usługach w usłudze IoT Plug and Play wersja zapoznawcza w wersji zapoznawczej w lipcu 2020. Wcześniejsza wersja zapoznawcza Plug and Play IoT była w sierpniu 2019.

## <a name="digital-twins-definition-language-dtdl"></a>Digital bliźniaczych reprezentacji Definition Language (DTDL)

W tej wersji dodano obsługę [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) i przestarzałe [DTDL V1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

Na poniższej liście przedstawiono kluczowe różnice między DTDL V1 i DTDL v2. W DTDL v2:

- Identyfikatory modeli mają prefiks `dtmi` zamiast `urn` . Identyfikatory modelu przędzy cyfrowej (DTMI) zastępują `urn` prefiksy cyfrowe z podpisem cyfrowym używane w DTDL v1. Wersja jest teraz poprzedzona znakiem `;` . Na przykład poprzednio używane `urn:CompanyName:Model:1` polecenie `dtmi:CompanyName:Model;1` .
- Ustaw wartość `@context` na `dtmi:dtdl:context;2` zamiast `http://azureiot.com/v1/contexts/IoTModel.json` .
- Aby modelować urządzenie, użyj typu **interfejsu** zamiast typów **CapabilityModel** .
- **Składniki** zastępują wystąpienia **interfejsu** . Możesz definiować **relacje** i **składniki** w ramach zawartości **interfejsu**.
- **Interfejs** może dziedziczyć z innego **interfejsu**.
- Można rozszerzyć DTDL za pomocą _rozszerzalnych typów semantycznych_. Ten rozszerzalny system typów zapewnia większą elastyczność niż zakodowane typy semantyczne, takie jak temperatura i wilgotność w DTDL v1.
- Właściwość **displayUnit** została usunięta.
- Nie można używać znaków podkreślenia wiodących lub końcowych w nazwie. Wiodące znaki podkreślenia w nazwie są zarezerwowane do użycia przez system.

Aby można było korzystać z DTDL V1, należy użyć poprzedniej wersji zestawu SDK i programu Azure IoT Explorer 0.10. x. Aby można było korzystać z DTDL v2, potrzebna jest Najnowsza wersja zestawu SDK i Azure IoT Explorer 0,11. x.

## <a name="no-component-and-multiple-component-implementations"></a>Brak składników i wielu implementacji składników

Proste urządzenia korzystające z kilku danych telemetrycznych, poleceń lub właściwości można opisać w jednym interfejsie bez używania składników. Każde istniejące urządzenie może zostać Plug and Play IoT, ogłaszając **Identyfikator modelu** bez wprowadzania zmian w istniejącej implementacji urządzenia.

Bardziej złożone urządzenia mogą grupować dane telemetryczne, polecenia i właściwości w różnych interfejsach w celu zarządzania złożonością i ponownego użycia między urządzeniami. Te urządzenia muszą zostać zaktualizowane, aby były zgodne z zestawem prostych reguł zdefiniowanych w [konwencjach komunikatów Plug and Play IoT](concepts-convention.md).

## <a name="registration-and-discovery"></a>Rejestracja i odnajdywanie

W tej wersji urządzenia anonsują swój **Identyfikator modelu** przy użyciu IoT Hub przy każdym połączeniu. IoT Hub buforuje **Identyfikator modelu** , włączając rozwiązania zaplecza, aby pobrać **Identyfikator modelu** przy użyciu właściwości przędzy urządzenia `modelId` . **Identyfikator modelu** można także pobrać z w postaci `$metadata.$model` dwuosiowej.

## <a name="microsoft-defined-interfaces"></a>Interfejsy zdefiniowane przez firmę Microsoft

Następujące interfejsy zdefiniowane przez firmę Microsoft są przestarzałe i nie są publikowane w nowym repozytorium modelu:

- **urn: azureiot: ModelDiscovery: DigitalTwin: 1**
- **urn: azureiot: ModelDiscovery: ModelInformation: 1**

Następujący interfejs jest publikowany w nowym repozytorium modelu: `dtmi:azure:DeviceManagement:DeviceInformation;1` dostępne w adresie URL [ https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ; 1? API-Version = 2020-05 -01-Preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

Struktura zdarzeń [źródła zdarzeń](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) **DigitalTwinChangeEvents** została zmieniona w celu użycia formatu **poprawki JSON** . Ta zmiana jest istotną zmianą bez obsługi zgodności z poprzednimi wersjami.

## <a name="message-routing"></a>Routing komunikatów

Komunikaty telemetryczne zawierają następujące zmiany w ramach kolekcji [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) .

Zawiera teraz właściwość typu **DT-DataSchema** , która jest **identyfikatorem modelu** zarejestrowanym przez urządzenie.

Właściwość **"DT-subject"** reprezentuje składnik wysyłający komunikat telemetrii.

Właściwość **iothub-Interface-Name** jest przestarzała.

## <a name="device-and-service-sdks"></a>Zestawy SDK urządzeń i usługi

Nie istnieje zgodność z poprzednimi wersjami zestawów SDK. Jeśli przejdziesz do najnowszej wersji zapoznawczej zestawu SDK, musisz zmienić swój kod.

W przypadku podejścia opartego na Konwencji nie ma potrzeby stosowania oddzielnych zestawów SDK klienta dla urządzeń. W tej wersji zapoznawczej istniejące biblioteki **DigitalTwinClient** są przestarzałe we wszystkich językach. Zamiast tego zostały zaktualizowane zestawy SDK klienta urządzenia IoT Hub, aby uwzględnić opcję anonsowania **identyfikatora modelu**.

Urządzenia, które nie korzystają ze składników wymagają minimalnych zmian w kodzie — po prostu zapowiadają **Identyfikator modelu**. Bardziej złożone urządzenia korzystające z wielu składników mogą wymagać niektórych funkcji wielokrotnego użytku w celu zaimplementowania [Konwencji](concepts-convention.md). Przykłady urządzeń obejmują zestaw funkcji, których można użyć ponownie w implementacji urządzenia.

### <a name="service-sdks"></a>Zestawy SDK usług

Zestaw SDK usługi jest dostępny w [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/pnp-preview-refresh/digitaltwins/service/readme.md) i [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>VS Code rozszerzenie

Rozszerzenie [Workbench urządzenia Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) oferuje obsługę autorstwa DTDL V1, integrację z poprzednią wersją repozytorium modeli i generowanie kodu.

Jeśli wymagana jest obsługa autorstwa DTDL v2 w VS Code, zainstaluj nowe [rozszerzenie DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) w vs Code. Rozszerzenie nie zapewnia integracji z repozytorium modelu lub generowaniem kodu. Zarządzanie modelami w repozytorium jest teraz realizowane przy użyciu [interfejsu użytkownika sieci Web](https://aka.ms/iotmodelrepo) lub interfejsu [wiersza polecenia](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest).

## <a name="digital-twin-service-side-rest-apis"></a>Interfejsy API REST usługi Digital bliźniaczy

[Interfejsy API REST usługi Digital bliźniaczy](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) i ładunki uległy zmianie. Obsługiwane są następujące interfejsy API:

- Pobierz wieloosiową cyfrę.
- Wywołaj polecenie.
- Aktualizowanie sieci cyfrowych przy użyciu ładunku **z poprawkami JSON** .

Istniejące interfejsy API REST są nadal obsługiwane w tej wersji.

## <a name="model-repository"></a>Repozytorium modeli

Teraz istnieje jedno repozytorium modelu zawierające zarówno opublikowane modele publiczne, jak i prywatne modele firm chronionych przez funkcję RBAC. Wszystkie modele mają unikatowy identyfikator i są niezmienne po utworzeniu.

Istniejące repozytoria modelu firmy z poprzedniej wersji nie są obsługiwane w tej wersji. Aby zarządzać starymi modelami DTDL V1, można nadal korzystać z witryny sieci Web z [certyfikatem platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com/products) . Nie można już używać tej witryny sieci Web do rejestrowania, testowania i certyfikowania urządzeń.

## <a name="azure-iot-central"></a>Azure IoT Central

Usługa Azure IoT Central jest obecnie aktualizowana w celu obsługi wersji zapoznawczej usługi IoT Plug and Play Preview.
