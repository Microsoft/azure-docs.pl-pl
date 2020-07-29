---
title: Słownik terminów — IoT Plug and Play Preview | Microsoft Docs
description: Pojęcia — słownik typowych terminów dotyczących programu IoT Plug and Play w wersji zapoznawczej.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d88ac62d9117df9d24c6d865e684a0972c87dae
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337317"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Słownik terminów dotyczących usługi IoT Plug and Play w wersji zapoznawczej

Definicje typowych terminów używanych w artykułach Plug and Play IoT.

## <a name="azure-iot-explorer-tool"></a>Narzędzie Azure IoT Explorer

Program Azure IoT Explorer jest graficznym narzędziem służącym do współdziałania z [urządzeniami Plug and Play IoT](#iot-plug-and-play-device)i ich testowania. Po zainstalowaniu narzędzia na komputerze lokalnym można go użyć do:

- Wyświetlanie urządzeń podłączonych do [Centrum IoT Hub](#azure-iot-hub).
- Połącz się z urządzeniem Plug and Play IoT.
- Wyświetl [składniki](#component)urządzenia.
- Wyświetl dane [telemetryczne](#telemetry) wysyłane przez urządzenie.
- Pracuj z [właściwościami](#properties)urządzeń.
- Wywoływanie [poleceń](#commands)dotyczących urządzeń.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Usługa IoT Hub jest usługą zarządzaną, hostowaną w chmurze, która działa jako centrum komunikatów w dwukierunkowej komunikacji między aplikacją IoT a zarządzanymi przez nią urządzeniami. [Urządzenia iot Plug and Play](#iot-plug-and-play-device) mogą łączyć się z usługą IoT Hub. Rozwiązanie IoT używa Centrum IoT, aby umożliwić:

- Urządzenia wysyłające dane telemetryczne do rozwiązania opartego na chmurze.
- Rozwiązanie oparte na chmurze służące do zarządzania podłączonymi urządzeniami.

## <a name="azure-iot-device-sdk"></a>Zestaw SDK urządzeń usługi Azure IoT

Istnieją zestawy SDK urządzeń dla wielu języków, których można użyć do tworzenia aplikacji klienckich urządzenia IoT Plug and Play.

## <a name="commands"></a>Polecenia

Polecenia zdefiniowane w [interfejsie](#interface) reprezentują metody, które mogą być wykonywane na [dwucyfrowej](#digital-twin)stawce. Na przykład polecenie umożliwiające ponowne uruchomienie urządzenia.

## <a name="component"></a>Składnik

Składniki umożliwiają skompilowanie [interfejsu](#interface) modelu jako zestawu innych interfejsów. [Model urządzenia](#device-model) może łączyć wiele interfejsów jako składniki. Na przykład model może obejmować składnik przełącznika i składnik termostatu. Wiele składników w modelu może również korzystać z tego samego typu interfejsu. Przykładowo model może zawierać dwa składniki termostatu.

## <a name="connection-string"></a>Parametry połączenia

Parametry połączenia hermetyzują informacje wymagane do nawiązania połączenia z punktem końcowym. Parametry połączenia zwykle obejmują adres punktu końcowego i informacje o zabezpieczeniach, ale formaty parametrów połączenia różnią się w zależności od usług. Istnieją dwa typy parametrów połączenia skojarzone z usługą IoT Hub:

- Parametry połączenia urządzeń umożliwiają [urządzeniom Plug and Play IoT](#iot-plug-and-play-device) łączenie się z punktami końcowymi dostępnymi dla urządzeń w centrum IoT. Kod klienta na urządzeniu używa parametrów połączenia w celu nawiązania bezpiecznego połączenia z usługą IoT Hub.
- IoT Hub parametry połączenia umożliwiają bezpieczne łączenie rozwiązań i narzędzi zaplecza z punktami końcowymi dostępnymi do usługi w usłudze IoT Hub. Te rozwiązania i narzędzia umożliwiają zarządzanie usługą IoT Hub i podłączonymi do niej urządzeniami.

## <a name="device-model"></a>Model urządzenia

Model urządzenia opisuje [urządzenie Plug and Play IoT](#iot-plug-and-play-device) i definiuje [składniki](#component) tworzące urządzenie. Prosty model urządzenia nie ma oddzielnych składników i zawiera definicję dla jednego interfejsu poziomu głównego. Bardziej skomplikowany model urządzeń zawiera wiele składników. Model urządzenia zazwyczaj odpowiada urządzeniu fizycznemu, produktowi lub jednostce SKU. Używasz [języka Digital bliźniaczych reprezentacji Definition Language w wersji 2](#digital-twins-definition-language) do definiowania modelu urządzenia.

## <a name="device-builder"></a>Konstruktor urządzeń

Konstruktor urządzeń korzysta z [modelu](#device-model) i [interfejsów](#interface) urządzeń podczas implementowania kodu do uruchamiania na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device). Konstruktory urządzeń zazwyczaj korzystają z jednego z [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdk) w celu zaimplementowania klienta urządzenia, ale nie jest to wymagane.

## <a name="device-modeling"></a>Modelowanie urządzeń

[Konstruktor urządzeń](#device-builder) używa [języka definicji Digital bliźniaczych reprezentacji](#digital-twins-definition-language) do modelowania możliwości [urządzenia Plug and Play IoT](#iot-plug-and-play-device). [Konstruktor rozwiązań](#solution-builder) może skonfigurować rozwiązanie IoT na podstawie modelu.

## <a name="digital-twin"></a>Cyfrowe sznurki

Cyfrowa przędza to model [urządzenia IoT Plug and Play](#iot-plug-and-play-device). Dwucyfrowy model jest modelowany przy użyciu [języka Digital bliźniaczych reprezentacji Definition Language](#digital-twins-definition-language). Za pomocą [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdk) można korzystać z bliźniaczych reprezentacji cyfrowych w czasie wykonywania. Na przykład możesz ustawić wartość właściwości w postaci cyfrowej sznurka na urządzeniu, a zestaw SDK przekaże tę zmianę do rozwiązania IoT w chmurze.

## <a name="digital-twin-change-events"></a>Wielocyfrowe zdarzenia zmiany

Gdy [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jest połączone z [Centrum IoT](#azure-iot-hub), centrum może korzystać z funkcji routingu do wysyłania powiadomień o zmianach dwuosiowych. Na przykład za każdym razem, gdy wartość [Właściwości](#properties) zostanie zmieniona na urządzeniu, IoT Hub może wysłać powiadomienie do punktu końcowego, takiego jak centrum zdarzeń.

## <a name="digital-twins-definition-language"></a>Digital bliźniaczych reprezentacji Definition Language

Język służący do opisywania modeli i interfejsów dla [urządzeń Plug and Play IoT](#iot-plug-and-play-device). Użyj [języka Digital bliźniaczych reprezentacji Definition Language w wersji 2](https://github.com/Azure/opendigitaltwins-dtdl) , aby opisać możliwości [cyfrowego przędzy](#digital-twin) i umożliwić platformie IoT i rozwiązań IoT wykorzystanie semantyki jednostki.

## <a name="digital-twin-route"></a>Trasa Digital bliźniaczy

Trasa skonfigurowana w [Centrum IoT Hub](#azure-iot-hub) w celu dostarczania [cyfrowych cyfrowych zdarzeń zmiany](#digital-twin-change-events) do i punktu końcowego, takiego jak centrum zdarzeń.

## <a name="interface"></a>Interfejs

Interfejs opisuje powiązane funkcje, które są implementowane przez [urządzenie IoT Plug and Play](#iot-plug-and-play-device) lub pojedyncze [sznurki cyfrowe](#digital-twin). Można ponownie użyć interfejsów dla różnych [modeli urządzeń](#device-model). Gdy interfejs jest używany w modelu urządzenia, definiuje [składnik](#component) urządzenia.

## <a name="iot-hub-query-language"></a>Język zapytań IoT Hub

Język zapytań IoT Hub jest używany w wielu celach. Można na przykład użyć języka do wyszukania urządzeń zarejestrowanych w centrum IoT Hub lub zawęzić zachowanie [routingu cyfrowego przędzy](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Urządzenie Plug and Play IoT

Urządzenie IoT Plug and Play jest zazwyczaj niewielką skalowalnym urządzeniem komputerowym, które zbiera dane lub steruje innymi urządzeniami, a także uruchamia oprogramowanie lub oprogramowanie układowe implementujące [model urządzenia](#device-model).  Na przykład urządzenie IoT Plug and Play może być urządzeniem monitorującym środowisko lub kontrolerem systemu nawadniania inteligentnego. Możesz napisać rozwiązanie IoT hostowane w chmurze, aby móc polecenie, kontrolować i odbierać dane z urządzeń Plug and Play IoT.

## <a name="iot-plug-and-play-conventions"></a>Konwencje Plug and Play IoT

Usługi IoT [Plug and Play są](#iot-plug-and-play-device) zgodne z zestawem [Konwencji](concepts-convention.md) podczas wymiany danych z rozwiązaniem.

## <a name="model-discovery"></a>Odnajdywanie modelu

Gdy [urządzenie iot Plug and Play](#iot-plug-and-play-device) nawiązuje połączenie z rozwiązaniem IoT, rozwiązanie może odnaleźć możliwości urządzenia przez znalezienie [modelu urządzenia](#device-model). Rozwiązanie może znaleźć model urządzenia w [repozytorium modelu](#model-repository) lub w innej lokalizacji.

## <a name="model-id"></a>Identyfikator modelu

Gdy urządzenie IoT Plug and Play nawiązuje połączenie z IoT Hub wysyła **Identyfikator modelu** modelu [DTDL](#digital-twins-definition-language) , który implementuje. Umożliwia to rozwiązanie znalezienie modelu urządzenia.

## <a name="model-repository"></a>Repozytorium modelu

[Repozytorium modeli](concepts-model-repository.md) przechowuje [modele urządzeń](#device-model) i [interfejsy](#interface).

## <a name="model-repository-rest-api"></a>Interfejs API REST repozytorium modelu

Interfejs API służący do zarządzania repozytorium modelu i korzystania z niego. Można na przykład użyć interfejsu API do dodawania i wyszukiwania [modeli urządzeń](#device-model).

## <a name="properties"></a>Właściwości

Właściwości są polami danych zdefiniowanymi w [interfejsie](#interface) , który reprezentuje jakiś stan dwucyfrowego sznurka. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Właściwości tylko do odczytu, takie jak numer seryjny, są ustawiane przez kod uruchomiony na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device) .  Właściwości z możliwością zapisu, takie jak próg alarmu, są zazwyczaj ustawiane na podstawie rozwiązania IoT opartego na chmurze.

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Uwierzytelnianie sygnatury dostępu współdzielonego ma dwa składniki: zasady dostępu współdzielonego i sygnaturę dostępu współdzielonego (często nazywane tokenem). [Urządzenie Plug and Play IoT](#iot-plug-and-play-device) używa sygnatury dostępu współdzielonego do uwierzytelniania w usłudze [IoT Hub](#azure-iot-hub).

## <a name="solution-builder"></a>Konstruktor rozwiązań

Konstruktor rozwiązań tworzy zaplecze rozwiązania. Konstruktor rozwiązań zwykle współpracuje z zasobami platformy Azure, takimi jak [IoT Hub](#azure-iot-hub) i [repozytoria modeli](#model-repository).

## <a name="telemetry"></a>Telemetry

Pola telemetrii zdefiniowane w [interfejsie](#interface) reprezentują pomiary. Te pomiary są zazwyczaj wartościami, takimi jak odczyty czujników, które są wysyłane przez [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jako strumień danych.
