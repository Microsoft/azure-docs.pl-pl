---
title: Słownik terminów — IoT Plug and Play Preview | Microsoft Docs
description: Pojęcia — słownik typowych terminów dotyczących programu IoT Plug and Play w wersji zapoznawczej.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767069"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Słownik terminów dotyczących usługi IoT Plug and Play w wersji zapoznawczej

Definicje typowych terminów używanych w artykułach Plug and Play IoT.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT — Portal

Witryna sieci Web z [certyfikatem platformy Azure dla IoT](https://aka.ms/ACFI) służy do:

- Ukończ [proces certyfikacji](#device-certification) [urządzenia Plug and Play IoT](#iot-plug-and-play-device).
- Znajdź [modele możliwości urządzeń](#device-capability-model).
- Opublikuj model możliwości urządzenia w [repozytorium modelu publicznego](#public-model-repository).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to międzyplatformowe narzędzie do zarządzania zasobami platformy Azure. Rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure to narzędzie wiersza poleceń umożliwiające współdziałanie z [urządzeniami Plug and Play IoT](#iot-plug-and-play-device)i testowanie ich. Możesz użyć rozszerzenia, aby:

- Połącz się z urządzeniem Plug and Play IoT.
- Wyświetl dane [telemetryczne](#telemetry) wysyłane przez urządzenie.
- Pracuj z [właściwościami](#properties)urządzeń.
- Wywoływanie [poleceń](#commands)dotyczących urządzeń.
- Zarządzaj [repozytoriami](#model-repository), [interfejsami](#interface)i modelami [możliwości urządzeń](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central to w pełni zarządzane rozwiązanie typu "oprogramowanie jako usługa", które ułatwia łączenie i monitorowanie [urządzeń Plug and Play IoT](#iot-plug-and-play-device)oraz zarządzanie nimi. Korzystając z [modeli możliwości urządzeń](#device-capability-model) , można automatycznie skonfigurować aplikację IoT Central do monitorowania urządzeń i zarządzania nimi.

## <a name="azure-iot-certification-service"></a>Usługa certyfikacji IoT Azure

Usługa certyfikacji IoT Azure uruchamia zestaw testów certyfikacji podczas przesyłania [urządzenia Plug and Play IoT](#iot-plug-and-play-device) na potrzeby certyfikacji za pomocą [portalu certyfikatu platformy Azure dla IoT](#azure-certified-for-iot-portal). Aby można było dodać urządzenie do [wykazu urządzeń z certyfikatem Certified for IoT](#certified-for-iot-device-catalog), urządzenie musi być certyfikowane.

## <a name="azure-iot-tools-extension"></a>Rozszerzenie narzędzi Azure IoT Tools

Azure IoT Tools to kolekcja rozszerzeń w programie [Visual Studio Code](#visual-studio-code) , która pomaga w współpracy z IoT Hub i opracowywania urządzeń IoT. Do tworzenia urządzeń Plug and Play IoT można:

- Tworzenie [modeli możliwości urządzeń](#device-capability-model) i [interfejsów](#interface).
- Publikuj w [repozytoriach modeli](#model-repository).
- Generuj kod szkieletowy w celu zaimplementowania aplikacji urządzenia.

## <a name="azure-iot-explorer-tool"></a>Narzędzie Azure IoT Explorer

Program Azure IoT Explorer jest graficznym narzędziem służącym do współdziałania z [urządzeniami Plug and Play IoT](#iot-plug-and-play-device)i ich testowania. Po zainstalowaniu narzędzia na komputerze lokalnym można go użyć do:

- Wyświetlanie urządzeń podłączonych do [Centrum IoT Hub](#azure-iot-hub).
- Połącz się z urządzeniem Plug and Play IoT.
- Wyświetl dane [telemetryczne](#telemetry) wysyłane przez urządzenie.
- Pracuj z [właściwościami](#properties)urządzeń.
- Wywoływanie [poleceń](#commands)dotyczących urządzeń.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Usługa IoT Hub jest usługą zarządzaną, hostowaną w chmurze, która działa jako centrum komunikatów w dwukierunkowej komunikacji między aplikacją IoT a zarządzanymi przez nią urządzeniami. [Urządzenia iot Plug and Play](#iot-plug-and-play-device) mogą łączyć się z usługą IoT Hub. Rozwiązanie IoT używa Centrum IoT, aby umożliwić:

- Urządzenia wysyłające dane telemetryczne do rozwiązania opartego na chmurze.
- Rozwiązanie oparte na chmurze służące do zarządzania podłączonymi urządzeniami.

## <a name="azure-iot-device-sdk"></a>Zestaw SDK urządzeń usługi Azure IoT

Istnieją zestawy SDK urządzeń dla wielu języków, których można użyć do tworzenia aplikacji klienckich urządzenia IoT Plug and Play. Jednym z wymagań dotyczących [certyfikacji urządzenia](#device-certification) jest użycie przez kod klienta urządzenia jednego z zestawów SDK urządzeń usługi Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Certyfikowany katalog urządzeń IoT

[Wykaz urządzeń z certyfikatem usługi IoT](https://catalog.azureiotsolutions.com/) zawiera listę [urządzeń Plug and Play IoT](#iot-plug-and-play-device) , które przekazały testy [certyfikacji urządzeń](#device-certification) . [Modele możliwości urządzeń](#device-capability-model) dla urządzeń Plug and Play IoT w wykazie i opublikowane w repozytorium modelu publicznego.

## <a name="commands"></a>Polecenia

Polecenia zdefiniowane w [interfejsie](#interface) reprezentują metody, które mogą być wykonywane na [dwucyfrowej](#digital-twin)stawce. Na przykład polecenie umożliwiające ponowne uruchomienie urządzenia.

## <a name="common-interface"></a>Wspólny interfejs

Wszystkie [urządzenia Plug and Play IoT](#iot-plug-and-play-device) powinny implementować niektóre typowe [interfejsy](#interface). Na przykład interfejs informacji o urządzeniu definiuje sprzęt i informacje o systemie operacyjnym na urządzeniu. [Certyfikacja urządzenia](#device-certification) wymaga, aby urządzenie zaimplementował kilka typowych interfejsów. Można pobrać typowe definicje interfejsów z repozytorium modelu publicznego.

## <a name="company-model-repository"></a>Repozytorium modelu firmy

Organizacja może używać [repozytorium modelu](#model-repository) firmy jako prywatnego magazynu dla [modeli możliwości urządzeń](#device-capability-model) i [interfejsów](#interface).

## <a name="connection-string"></a>Parametry połączenia

Parametry połączenia hermetyzują informacje wymagane do nawiązania połączenia z punktem końcowym. Parametry połączenia zwykle obejmują adres punktu końcowego i informacje o zabezpieczeniach, ale formaty parametrów połączenia różnią się w zależności od usług. Istnieją dwa typy parametrów połączenia skojarzone z usługą IoT Hub:

- Parametry połączenia urządzeń umożliwiają [urządzeniom Plug and Play IoT](#iot-plug-and-play-device) łączenie się z punktami końcowymi dostępnymi dla urządzeń w centrum IoT. Kod klienta na urządzeniu używa parametrów połączenia w celu nawiązania bezpiecznego połączenia z usługą IoT Hub.
- IoT Hub parametry połączenia umożliwiają bezpieczne łączenie rozwiązań i narzędzi zaplecza z punktami końcowymi dostępnymi do usługi w usłudze IoT Hub. Te rozwiązania i narzędzia umożliwiają zarządzanie usługą IoT Hub i podłączonymi do niej urządzeniami.
- Parametry połączenia repozytorium modelu firmy umożliwiają bezpieczne łączenie rozwiązań i narzędzi zaplecza z [repozytorium modelu firmy](#company-model-repository). Te rozwiązania i narzędzia zużywają [modele możliwości urządzeń](#device-capability-model) i [interfejsy](#interface) w repozytorium lub zarządzają nimi.

## <a name="device-capability-model"></a>Model możliwości urządzenia

Model możliwości urządzenia opisuje [urządzenie Plug and Play IoT](#iot-plug-and-play-device) i definiuje zestaw [interfejsów](#interface) implementowanych przez urządzenie. Model możliwości urządzenia zazwyczaj odpowiada urządzeniu fizycznemu, produktowi lub jednostce SKU. Do zdefiniowania modelu możliwości urządzenia służy [Język definicji Digital bliźniaczy](#digital-twin-definition-language) .

## <a name="device-certification"></a>Certyfikacja urządzenia

Certyfikat urządzenia to proces certyfikowania [urządzenia Plug and Play IoT](#iot-plug-and-play-device) , dzięki czemu można go dodać do [wykazu urządzeń z certyfikatem usługi IoT](#certified-for-iot-device-catalog) oraz [modelu możliwości urządzenia](#device-capability-model) i [interfejsów](#interface) dodanych do [repozytorium modelu publicznego](#public-model-repository).

## <a name="device-developer"></a>Deweloper urządzeń

Deweloper urządzenia używa [modelu możliwości urządzenia](#device-capability-model), [interfejsów](#interface)i [zestawu SDK urządzenia usługi Azure IoT](#azure-iot-device-sdk) w celu zaimplementowania kodu do uruchomienia na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelowanie urządzeń

[Deweloper urządzenia](#device-developer) używa [języka definicji Digital bliźniaczy](#digital-twin-definition-language) do modelowania możliwości [urządzenia Plug and Play IoT](#iot-plug-and-play-device). Model może być współużytkowany przy użyciu repozytorium modelu. Deweloper urządzenia może wygenerować kod urządzenia szkieletowego z modelu. [Deweloper rozwiązań](#solution-developer) może skonfigurować rozwiązanie IoT na podstawie modelu.

## <a name="device-provisioning-service"></a>Usługa Device Provisioning

Usługa [Azure IoT Central](#azure-iot-central) używa usługi Device Provisioning w celu zarządzania rejestracją i połączeniem urządzeń. Aby uzyskać więcej informacji, zobacz [łączność urządzenia w usłudze Azure IoT Central](../iot-central/core/concepts-get-connected.md). Możesz również użyć usługi Device Provisioning, aby zarządzać rejestracją i połączeniem urządzeń z opartym na IoT Hub rozwiązaniu IoT. Aby uzyskać więcej informacji, zobacz temat [aprowizacji urządzeń przy użyciu usługi Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Rejestracja urządzenia

Zanim [urządzenie iot Plug and Play](#iot-plug-and-play-device) może nawiązać połączenie z rozwiązaniem IoT, musi ono zostać zarejestrowane w rozwiązaniu. Usługa [Azure IoT Central](#azure-iot-central) używa [usługi Device Provisioning](#device-provisioning-service) do zarządzania rejestracją urządzeń. W niestandardowym rozwiązaniu IoT można rejestrować urządzenia z Centrum IoT w Azure Portal lub programowo.

## <a name="device-first"></a>Urządzenie — najpierw

[Usługa Azure IoT Central](#azure-iot-central) obsługuje pierwszy scenariusz rejestracji i połączenia urządzenia. W tym scenariuszu [urządzenie IoT Plug and Play](#iot-plug-and-play-device) może połączyć się z aplikacją IoT Central bez wcześniejszego zarejestrowania. Rejestracja odbywa się automatycznie, gdy urządzenie po raz pierwszy łączy się z aplikacją.

## <a name="digital-twin"></a>Cyfrowe sznurki

Cyfrowa przędza to model [urządzenia IoT Plug and Play](#iot-plug-and-play-device). Dwucyfrowy model jest modelowany przy użyciu [języka definicji cyfrowej sznurka](#digital-twin-definition-language). Za pomocą [zestawów SDK urządzeń usługi Azure IoT](#azure-iot-device-sdk) można korzystać z bliźniaczych reprezentacji cyfrowych w czasie wykonywania. Na przykład możesz ustawić wartość właściwości w postaci cyfrowej sznurka na urządzeniu, a zestaw SDK przekaże tę zmianę do rozwiązania IoT w chmurze.

## <a name="digital-twin-change-events"></a>Wielocyfrowe zdarzenia zmiany

Gdy [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jest połączone z [Centrum IoT](#azure-iot-hub), centrum może korzystać z funkcji routingu do wysyłania powiadomień o zmianach dwuosiowych. Na przykład za każdym razem, gdy wartość [Właściwości](#properties) zostanie zmieniona na urządzeniu, IoT Hub może wysłać powiadomienie do punktu końcowego, takiego jak kolejka Service Bus.

## <a name="digital-twin-definition-language"></a>Język definicji cyfrowej przędzy

Język służący do opisywania modeli i interfejsów dla [urządzeń Plug and Play IoT](#iot-plug-and-play-device). Użyj [języka definicji Digital bliźniaczy](https://aka.ms/DTDL) do opisywania możliwości [cyfrowych przędzy](#digital-twin) i Włącz platformę IoT i rozwiązania IoT, aby korzystać z semantyki jednostki.

## <a name="digital-twin-route"></a>Trasa Digital bliźniaczy

Trasa skonfigurowana w [Centrum IoT Hub](#azure-iot-hub) w celu dostarczania [cyfrowych sznurów wydarzeń](#digital-twin-change-events) do punktów końcowych, takich jak Kolejka Service Bus.

## <a name="interface"></a>Interface

Interfejs opisuje powiązane funkcje, które są implementowane przez [urządzenie IoT Plug and Play](#iot-plug-and-play-device) lub pojedyncze [sznurki cyfrowe](#digital-twin). Można ponownie użyć interfejsów w różnych [modelach możliwości urządzeń](#device-capability-model).

## <a name="iot-hub-query-language"></a>Język zapytań IoT Hub

Język zapytań IoT Hub jest używany w wielu celach. Można na przykład użyć języka do wyszukania [urządzeń zarejestrowanych](#device-registration) w centrum IoT Hub lub zawęzić zachowanie [routingu cyfrowego przędzy](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Urządzenie Plug and Play IoT

Urządzenie Plug and Play IoT to zwykle niezależne, autonomiczne urządzenie komputerowe, które zbiera dane lub steruje innymi urządzeniami oraz uruchamia oprogramowanie lub oprogramowanie układowe implementujące [model możliwości urządzenia](#device-capability-model).  Na przykład urządzenie IoT Plug and Play może być urządzeniem monitorującym środowisko lub kontrolerem systemu nawadniania inteligentnego. Możesz napisać rozwiązanie IoT hostowane w chmurze, aby móc polecenie, kontrolować i odbierać dane z urządzeń Plug and Play IoT. [Wykaz urządzeń z certyfikatem platformy Azure dla IoT](#certified-for-iot-device-catalog) zawiera listę dostępnych urządzeń Plug and Play IoT. Każde urządzenie Plug and Play IoT w wykazie zostało zweryfikowane i ma [model możliwości urządzenia](#device-capability-model).

## <a name="microsoft-partner-center"></a>Centrum Partnerskie Microsoft

[Centrum partnerskie firmy Microsoft](https://docs.microsoft.com/partner-center/) to miejsce, w którym organizacja zarządza kompleksową relacją z firmą Microsoft. Musisz mieć konto Centrum partnerskiego firmy Microsoft, aby móc certyfikowania [urządzenia IoT Plug and Play](#iot-plug-and-play-device) w [portalu Azure Certified for IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Odnajdywanie modelu

Gdy [urządzenie iot Plug and Play](#iot-plug-and-play-device) nawiązuje połączenie z rozwiązaniem IoT, rozwiązanie może odnaleźć możliwości urządzenia, wyszukując [model możliwości urządzeń](#device-capability-model). Urządzenie może wysłać swój model możliwości do rozwiązania, a rozwiązanie może znaleźć model możliwości urządzenia w [repozytorium modelu](#model-repository).

## <a name="model-repository"></a>Repozytorium modelu

Repozytorium modeli przechowuje [modele możliwości urządzeń](#device-capability-model) i [interfejsy](#interface). Istnieje jedno [repozytorium modelu publicznego](#public-model-repository). Organizacje mogą tworzyć własne repozytoria modeli organizacyjnych.

## <a name="model-repository-rest-api"></a>Interfejs API REST repozytorium modelu

Interfejs API służący do zarządzania repozytoriami modeli i korzystania z nich. Można na przykład użyć interfejsu API, aby dodać [modele możliwości urządzeń](#device-capability-model) i wyszukać modele możliwości.

## <a name="properties"></a>Właściwości

Właściwości są polami danych zdefiniowanymi w [interfejsie](#interface) , który reprezentuje jakiś stan dwucyfrowego sznurka. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Właściwości tylko do odczytu, takie jak numer seryjny, są ustawiane przez kod uruchomiony na [urządzeniu IoT Plug and Play](#iot-plug-and-play-device) .  Właściwości z możliwością zapisu, takie jak próg alarmu, są zazwyczaj ustawiane na podstawie rozwiązania IoT opartego na chmurze.

## <a name="public-model-repository"></a>Repozytorium modelu publicznego

Istnieje jedno repozytorium modelu publicznego, które przechowuje [modele możliwości urządzeń](#device-capability-model) i [interfejsy](#interface) dla [certyfikowanych urządzeń](#device-certification). W repozytorium modelu publicznego przechowywane są również [wspólne definicje interfejsów](#common-interface) .

## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji jednoznacznie identyfikuje urządzenie w [usłudze Device Provisioning](#device-provisioning-service). Ten identyfikator nie jest taki sam jak identyfikator urządzenia, który jest unikatowym identyfikatorem urządzenia w usłudze [IoT Hub](#azure-iot-hub).

## <a name="scope-id"></a>Identyfikator zakresu

Zakres identyfikatorów zakresu jednoznacznie identyfikuje wystąpienie [usługi Device Provisioning Service](#device-provisioning-service) .

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Uwierzytelnianie sygnatury dostępu współdzielonego ma dwa składniki: zasady dostępu współdzielonego i sygnaturę dostępu współdzielonego (często nazywane tokenem). [Urządzenie Plug and Play IoT](#iot-plug-and-play-device) używa sygnatury dostępu współdzielonego do uwierzytelniania w usłudze [IoT Hub](#azure-iot-hub).

## <a name="solution-developer"></a>Deweloper rozwiązań

Deweloper rozwiązania tworzy zaplecze rozwiązania. Deweloper rozwiązań zwykle współpracuje z zasobami platformy Azure, takimi jak [IoT Hub](#azure-iot-hub) i [repozytoria modeli](#model-repository), lub współdziała z [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

Pola telemetrii zdefiniowane w [interfejsie](#interface) reprezentują pomiary. Te pomiary są zazwyczaj wartościami, takimi jak odczyty czujników, które są wysyłane przez [urządzenie Plug and Play IoT](#iot-plug-and-play-device) jako strumień danych.

## <a name="visual-studio-code"></a>Visual Studio Code

Program Visual Studio Code jest nowoczesnym edytorem kodu dostępnym dla wielu platform. Rozszerzenia, takie jak te w pakiecie [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Pack, umożliwiają dostosowanie edytora do obsługi szerokiego zakresu scenariuszy programistycznych.
