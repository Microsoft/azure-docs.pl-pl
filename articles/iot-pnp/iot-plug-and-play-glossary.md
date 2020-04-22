---
title: Słowniczek terminów - IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Pojęcia - glosariusz wspólnych terminów odnoszących się do podglądu iod iod IoT.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767069"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Słowniczek terminów dla podglądu iod iod IoT

Definicje typowych terminów używanych w artykułach IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Portal z certyfikatem platformy Azure dla IoT

Witryny sieci Web [portalu Certyfikat platformy Azure dla IoT](https://aka.ms/ACFI) umożliwiają:

- Zakończ [proces certyfikacji](#device-certification) [urządzenia IoT Plug and Play](#iot-plug-and-play-device).
- Znajdź [modele możliwości urządzenia](#device-capability-model).
- Opublikuj model możliwości urządzenia w [publicznym repozytorium modelu](#public-model-repository).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Narzędzie interfejsu wiersza polecenia platformy Azure to wieloplatformowe narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. Rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure to narzędzie wiersza polecenia do interakcji z urządzeniami Typu Plug and Play i [testowania urządzeń IoT.](#iot-plug-and-play-device) Można użyć rozszerzenia do:

- Połącz się z urządzeniem Typu Plug and Play IoT.
- Wyświetl [dane telemetryczne,](#telemetry) które wysyła urządzenie.
- Praca z [właściwościami](#properties)urządzenia .
- Wywołaj [polecenia](#commands)urządzenia .
- Zarządzanie [repozytoriami modeli,](#model-repository) [interfejsami](#interface)i [modelami możliwości urządzeń.](#device-capability-model)

## <a name="azure-iot-central"></a>Azure IoT Central

Usługa Azure IoT Central to w pełni zarządzane rozwiązanie programowe jako usługa, które ułatwia łączenie, monitorowanie i zarządzanie [urządzeniami IoT Plug and Play.](#iot-plug-and-play-device) Za pomocą [modeli funkcji urządzenia](#device-capability-model) można automatycznie skonfigurować aplikację IoT Central do monitorowania urządzeń i zarządzania nimi.

## <a name="azure-iot-certification-service"></a>Usługa certyfikacji Usługi Azure IoT

Usługa certyfikacji Usługi Azure IoT uruchamia zestaw testów certyfikacyjnych podczas przesyłania [urządzenia Typu Plug and Play IoT](#iot-plug-and-play-device) do certyfikacji za pośrednictwem portalu Certyfikat azure dla [IoT.](#azure-certified-for-iot-portal) Przed dodaniem urządzenia do [katalogu urządzeń Certyfikowane dla IoT](#certified-for-iot-device-catalog)urządzenie musi być certyfikowane.

## <a name="azure-iot-tools-extension"></a>Rozszerzenie narzędzia IoT platformy Azure

Narzędzia Azure IoT Tools to zbiór rozszerzeń w [kodzie programu Visual Studio,](#visual-studio-code) które ułatwiają interakcję z Centrum IoT i tworzenie urządzeń IoT. W przypadku tworzenia urządzeń IoT Plug and Play pomaga:

- Tworzenie [modeli możliwości urządzenia](#device-capability-model) i [interfejsów](#interface).
- Publikowanie w [repozytoriach modelu](#model-repository).
- Generowanie kodu szkieletu w celu zaimplementowania aplikacji urządzenia.

## <a name="azure-iot-explorer-tool"></a>Narzędzie do eksplorowania Usługi Azure IoT

Eksplorator IoT platformy Azure to narzędzie graficzne, za pomocą którego można wchodzić w interakcje i testować [urządzenia Typu Plug and Play IoT.](#iot-plug-and-play-device) Po zainstalowaniu narzędzia na komputerze lokalnym można go użyć do:

- Wyświetlanie urządzeń podłączonych do [centrum IoT](#azure-iot-hub)hub .
- Połącz się z urządzeniem Typu Plug and Play IoT.
- Wyświetl [dane telemetryczne,](#telemetry) które wysyła urządzenie.
- Praca z [właściwościami](#properties)urządzenia .
- Wywołaj [polecenia](#commands)urządzenia .

## <a name="azure-iot-hub"></a>Azure IoT Hub

Usługa IoT Hub jest usługą zarządzaną, hostowaną w chmurze, która działa jako centrum komunikatów w dwukierunkowej komunikacji między aplikacją IoT a zarządzanymi przez nią urządzeniami. [Urządzenia IoT Plug and Play](#iot-plug-and-play-device) mogą łączyć się z centrum IoT Hub. Rozwiązanie IoT używa centrum IoT hub, aby włączyć:

- Urządzenia do wysyłania danych telemetrycznych do rozwiązania opartego na chmurze.
- Rozwiązanie oparte na chmurze do zarządzania podłączonymi urządzeniami.

## <a name="azure-iot-device-sdk"></a>Zestaw SDK urządzenia Usługi Azure IoT

Istnieją zestawy SDK urządzeń dla wielu języków, których można używać do tworzenia aplikacji klienckich urządzeń IoT Plug and Play. Jednym z wymagań [dotyczących certyfikacji urządzeń](#device-certification) jest to, że kod klienta urządzenia używa jednego z zestawów SDK urządzenia IoT platformy Azure.

## <a name="certified-for-iot-device-catalog"></a>Certyfikowany katalog urządzeń IoT

[Katalog urządzeń Certified for IoT](https://catalog.azureiotsolutions.com/) zawiera listę urządzeń Typu Plug and Play usługi [IoT,](#iot-plug-and-play-device) które przeszły testy [certyfikacji urządzeń.](#device-certification) [Modele możliwości urządzeń](#device-capability-model) dla urządzeń IoT Plug and Play w katalogu i opublikowane w publicznym repozytorium modelu.

## <a name="commands"></a>Polecenia

Polecenia zdefiniowane w [interfejsie](#interface) reprezentują metody, które mogą być wykonywane na [bliźniaczej reprezentacji cyfrowej](#digital-twin). Na przykład polecenie ponownego uruchomienia urządzenia.

## <a name="common-interface"></a>Wspólny interfejs

Oczekuje się, że wszystkie [urządzenia IoT Plug and Play](#iot-plug-and-play-device) zaimplementują niektóre typowe [interfejsy.](#interface) Na przykład interfejs informacji o urządzeniu definiuje informacje o sprzęcie i systemie operacyjnym o urządzeniu. [Certyfikacja urządzenia](#device-certification) wymaga, aby urządzenie zaimplementować kilka wspólnych interfejsów. Typowe definicje interfejsu można pobrać z repozytorium modelu publicznego.

## <a name="company-model-repository"></a>Repozytorium modeli firmy

Organizacja może używać [repozytorium modelu](#model-repository) firmy jako magazynu prywatnego dla [modeli możliwości urządzeń](#device-capability-model) i [interfejsów](#interface).

## <a name="connection-string"></a>Parametry połączenia

Ciąg połączenia hermetyzuje informacje wymagane do nawiązania połączenia z punktem końcowym. Parametry połączenia zazwyczaj zawiera adres punktu końcowego i informacje o zabezpieczeniach, ale formaty ciągu połączenia różnią się w zależności od usługi. Istnieją dwa typy ciągu połączenia skojarzone z usługą Centrum IoT:

- Parametry połączenia urządzenia umożliwiają [urządzeniom Typu Plug and Play IoT](#iot-plug-and-play-device) łączenie się z punktami końcowymi skierowanymi do urządzenia w centrum IoT hub. Kod klienta na urządzeniu używa ciągu połączenia do ustanowienia bezpiecznego połączenia z centrum IoT hub.
- Parametry połączeń usługi IoT Hub umożliwiają rozwiązania zaplecza i narzędzia do bezpiecznego łączenia się z punktami końcowymi skierowanymi do usługi w centrum IoT hub. Te rozwiązania i narzędzia zarządzają centrum IoT i podłączonymi do niego urządzeniami.
- Parametry połączenia repozytorium repozytorium modelu firmy umożliwiają rozwiązania zaplecza i narzędzia do bezpiecznego łączenia się z [repozytorium modelu firmy.](#company-model-repository) Te rozwiązania i narzędzia zużywają lub zarządzają [modelami i](#device-capability-model) [interfejsami](#interface) możliwości urządzenia w repozytorium.

## <a name="device-capability-model"></a>Model możliwości urządzenia

Model możliwości urządzenia opisuje [urządzenie Typu Plug and Play IoT](#iot-plug-and-play-device) i definiuje zestaw [interfejsów](#interface) zaimplementowanych przez urządzenie. Model możliwości urządzenia zazwyczaj odpowiada urządzeniu fizycznemu, produktowi lub jednostce SKU. Do zdefiniowania modelu możliwości urządzenia użyj [języka cyfrowej podwójnej definicji.](#digital-twin-definition-language)

## <a name="device-certification"></a>Certyfikacja urządzeń

Certyfikacja urządzenia to proces certyfikacji [urządzenia Typu Plug and Play IoT,](#iot-plug-and-play-device) dzięki czemu można je dodać do katalogu urządzeń Certified for [IoT](#certified-for-iot-device-catalog) oraz [do modelu i](#device-capability-model) interfejsów funkcji urządzenia [dodanych](#interface) do [publicznego repozytorium modelu](#public-model-repository).

## <a name="device-developer"></a>Deweloper urządzeń

Deweloper urządzeń używa [modelu możliwości urządzenia,](#device-capability-model) [interfejsów](#interface)i [zestawu SDK urządzenia Usługi Azure IoT](#azure-iot-device-sdk) do zaimplementowania kodu do uruchomienia na [urządzeniu Typu Plug and Play usługi IoT.](#iot-plug-and-play-device)

## <a name="device-modeling"></a>Modelowanie urządzeń

[Deweloper urządzenia](#device-developer) używa [języka cyfrowej podwójnej definicji](#digital-twin-definition-language) do modelowania możliwości [urządzenia IoT Plug and Play](#iot-plug-and-play-device). Model można udostępniać przy użyciu repozytorium modelu. Deweloper urządzenia można wygenerować kod urządzenia szkielet z modelu. [Deweloper rozwiązania](#solution-developer) można skonfigurować rozwiązanie IoT z modelu.

## <a name="device-provisioning-service"></a>Usługa inicjowania obsługi administracyjnej urządzeń

[Usługa Azure IoT Central](#azure-iot-central) używa usługi inicjowania obsługi administracyjnej urządzeń do zarządzania całą rejestracją i połączeniem urządzenia. Aby uzyskać więcej informacji, zobacz [Łączność urządzenia w usłudze Azure IoT Central](../iot-central/core/concepts-get-connected.md). Za pomocą usługi inicjowania obsługi administracyjnej urządzeń można również zarządzać rejestracją urządzeń i połączeniem z rozwiązaniem IoT opartym na centrum IoT opartym na Usłudze IoT. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej urządzeń za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Rejestracja urządzenia

Zanim [urządzenie IoT Plug and Play](#iot-plug-and-play-device) może połączyć się z rozwiązaniem IoT, musi być zarejestrowane w rozwiązaniu. [Usługa Azure IoT Central](#azure-iot-central) używa [usługi inicjowania obsługi administracyjnej urządzeń](#device-provisioning-service) do zarządzania rejestracją urządzenia. W niestandardowym rozwiązaniu IoT można zarejestrować urządzenia za pomocą centrum IoT hub w witrynie Azure portal lub programowo.

## <a name="device-first"></a>Urządzenie po raz pierwszy

[Usługa Azure IoT Central](#azure-iot-central) obsługuje scenariusz rejestracji i połączenia pierwszego urządzenia. W tym scenariuszu [urządzenie Typu Plug and Play usługi IoT](#iot-plug-and-play-device) może łączyć się z aplikacją IoT Central bez wcześniejszego zarejestrowania. Rejestracja odbywa się automatycznie, gdy urządzenie po raz pierwszy łączy się z aplikacją.

## <a name="digital-twin"></a>Cyfrowy bliźniak

Bliźniak cyfrowy to model [urządzenia IoT Plug and Play.](#iot-plug-and-play-device) Cyfrowy bliźniak jest modelowany przy użyciu [języka Digital Twin Definition .](#digital-twin-definition-language) Zestawów [SDK urządzeń IoT platformy Azure](#azure-iot-device-sdk) można używać do interakcji z cyfrowymi bliźniaczymi w czasie wykonywania. Na przykład można ustawić wartość właściwości w cyfrowej bliźniaczej reprezentacji na urządzeniu i zestaw SDK komunikuje tę zmianę do rozwiązania IoT w chmurze.

## <a name="digital-twin-change-events"></a>Cyfrowe podwójne wydarzenia zmian

Gdy [urządzenie IoT Plug and Play](#iot-plug-and-play-device) jest podłączone do [centrum IoT](#azure-iot-hub)hub, centrum może używać funkcji routingu do wysyłania powiadomień o cyfrowych zmianach bliźniaczych. Na przykład za każdym razem, gdy wartość [właściwości](#properties) zmienia się na urządzeniu, Usługa IoT Hub może wysyłać powiadomienie do punktu końcowego, takiego jak kolejka usługi Service Bus.

## <a name="digital-twin-definition-language"></a>Język cyfrowej podwójnej definicji

Język opisujący modele i interfejsy dla [urządzeń IoT Plug and Play](#iot-plug-and-play-device). Użyj [języka cyfrowej podwójnej definicji,](https://aka.ms/DTDL) aby opisać możliwości [bliźniaka cyfrowego](#digital-twin) i włączyć platformę IoT i rozwiązania IoT, aby wykorzystać semantykę jednostki.

## <a name="digital-twin-route"></a>Cyfrowa trasa bliźniaczka

Trasa skonfigurowana w [centrum IoT hub](#azure-iot-hub) do dostarczania zdarzeń zmiany [podwójnej cyfrowej](#digital-twin-change-events) i punktu końcowego, takiego jak kolejka usługi Service Bus.

## <a name="interface"></a>Interface

Interfejs opisuje powiązane funkcje, które są implementowane przez [urządzenie IoT Plug and Play](#iot-plug-and-play-device) lub cyfrowy [bliźniak.](#digital-twin) Interfejsy można ponownie wykorzystać w różnych [modelach możliwości urządzenia](#device-capability-model).

## <a name="iot-hub-query-language"></a>Język kwerend centrum IoT

Język zapytań Usługi IoT Hub jest używany do wielu celów. Na przykład można użyć języka do wyszukiwania [urządzeń zarejestrowanych](#device-registration) w centrum IoT hub lub zawęzić [zachowanie routingu bliźniaczej cyfrowej.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>Urządzenie Typu Plug and Play IoT

Urządzenie Typu Plug and Play IoT jest zazwyczaj urządzeniem do samodzielnego przetwarzania na małą skalę, które zbiera dane lub steruje innymi urządzeniami, a także uruchamia oprogramowanie lub oprogramowanie układowe implementujące [model możliwości urządzenia.](#device-capability-model)  Na przykład urządzenie IoT Plug and Play może być urządzeniem monitorującym środowisko lub kontrolerem dla inteligentnego systemu nawadniania rolnictwa. Możesz napisać rozwiązanie IoT hostowane w chmurze do sterowania, kontrolowania i odbierania danych z urządzeń IoT Plug and Play. [Wykaz urządzeń z certyfikatem platformy Azure dla IoT](#certified-for-iot-device-catalog) zawiera listę dostępnych urządzeń typu Plug and Play usługi IoT. Każde urządzenie Typu Plug and Play IoT w katalogu zostało zweryfikowane i ma [model możliwości urządzenia.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Centrum Partnerskie Microsoft

[Centrum partnerów firmy Microsoft](https://docs.microsoft.com/partner-center/) to miejsce, w którym organizacja zarządza relacjami end-to-end z firmą Microsoft. Aby można było certyfikować [urządzenie IoT Plug and Play](#iot-plug-and-play-device) w portalu Azure Certified for [IoT,](#azure-certified-for-iot-portal)musisz mieć konto Centrum partnerów firmy Microsoft.

## <a name="model-discovery"></a>Odnajdywanie modelu

Gdy [urządzenie IoT Plug and Play](#iot-plug-and-play-device) łączy się z rozwiązaniem IoT, rozwiązanie może odkryć możliwości urządzenia, znajdując [model możliwości urządzenia.](#device-capability-model) Urządzenie może wysłać swój model możliwości do rozwiązania lub rozwiązanie może znaleźć model możliwości urządzenia w [repozytorium modelu](#model-repository).

## <a name="model-repository"></a>Repozytorium modelu

Repozytorium modelu [przechowuje modele możliwości urządzenia](#device-capability-model) i [interfejsy](#interface). Istnieje jedno [publiczne repozytorium modeli.](#public-model-repository) Organizacje mogą tworzyć własne repozytoria modeli organizacyjnych.

## <a name="model-repository-rest-api"></a>Interfejs API REST repozytorium modelu

Interfejs API do zarządzania repozytoriami modelu i interakcji z nimi. Na przykład można użyć interfejsu API, aby dodać modele możliwości urządzenia i [wyszukać](#device-capability-model) modele możliwości.

## <a name="properties"></a>Właściwości

Właściwości są pola danych zdefiniowane w [interfejsie,](#interface) które reprezentują pewien stan cyfrowej bliźniaczej reprezentacji. Można zadeklarować właściwości jako tylko do odczytu lub zapisywalne. Właściwości tylko do odczytu, takie jak numer seryjny, są ustawiane przez kod uruchomiony na samym [urządzeniu IoT Plug and Play.](#iot-plug-and-play-device)  Zapisywalne właściwości, takie jak próg alarmu, są zazwyczaj ustawiane z rozwiązania IoT oparte na chmurze.

## <a name="public-model-repository"></a>Publiczne repozytorium modeli

Istnieje jedno publiczne repozytorium modeli, które [przechowuje modele możliwości urządzenia](#device-capability-model) i [interfejsy](#interface) dla [certyfikowanych urządzeń.](#device-certification) Repozytorium modelu publicznego przechowuje również typowe definicje [interfejsu.](#common-interface)

## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji jednoznacznie identyfikuje urządzenie w [usłudze aprowizacji urządzeń.](#device-provisioning-service) Ten identyfikator nie jest taki sam jak identyfikator urządzenia, który jest unikatowym identyfikatorem urządzenia w [centrum IoT hub.](#azure-iot-hub)

## <a name="scope-id"></a>Identyfikator zakresu

Zakres identyfikatora zakresu jednoznacznie identyfikuje [wystąpienie usługi inicjowania obsługi administracyjnej urządzeń.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Sygnatury dostępu współdzielonego są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach lub identyfikatorach URI SHA-256. Uwierzytelnianie podpisu dostępu współdzielonego ma dwa składniki: zasady dostępu współdzielonego i podpis dostępu współdzielonego (często nazywany tokenem). [Urządzenie Typu Plug and Play usługi IoT](#iot-plug-and-play-device) używa podpisu dostępu współdzielonego do uwierzytelniania za pomocą centrum [IoT hub](#azure-iot-hub).

## <a name="solution-developer"></a>Deweloper rozwiązań

Deweloper rozwiązania tworzy zaplecze rozwiązania. Deweloper rozwiązania zazwyczaj współpracuje z zasobami platformy Azure, takimi jak [Centrum IoT](#azure-iot-hub) i [repozytoriami modeli,](#model-repository)lub współpracuje z [usługą IoT Central.](#azure-iot-central)

## <a name="telemetry"></a>Telemetria

Pola telemetryczne zdefiniowane w [interfejsie](#interface) reprezentują pomiary. Te pomiary są zazwyczaj wartościami, takimi jak odczyty czujników wysyłane przez [urządzenie IoT Plug and Play](#iot-plug-and-play-device) jako strumień danych.

## <a name="visual-studio-code"></a>Kod programu Visual Studio

Kod programu Visual Studio to nowoczesny edytor kodu dostępny dla wielu platform. Rozszerzenia, takie jak te w pakiecie [Narzędzia Usługi Azure IoT,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) umożliwiają dostosowanie edytora do obsługi szerokiej gamy scenariuszy rozwoju.
