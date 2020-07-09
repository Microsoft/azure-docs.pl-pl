---
title: Instalowanie i używanie programu Azure IoT Explorer | Microsoft Docs
description: Zainstaluj narzędzie Azure IoT Explorer i użyj go do współpracy z urządzeniami z programem IoT Plug and Play w wersji zapoznawczej podłączonym do centrum IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159187"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalowanie i korzystanie z programu Azure IoT Explorer

Program Azure IoT Explorer jest graficznym narzędziem do współdziałania z urządzeniami w wersji zapoznawczej IoT Plug and Play i testowania. Po zainstalowaniu narzędzia na komputerze lokalnym można używać go do nawiązywania połączenia z urządzeniem. Możesz użyć narzędzia, aby wyświetlić dane telemetryczne wysyłane przez urządzenie, współpracować z właściwościami urządzenia i wywoływać polecenia.

W tym artykule wyjaśniono, jak:

- Zainstaluj i skonfiguruj narzędzie Azure IoT Explorer.
- Użyj narzędzia, aby korzystać z i testować urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z narzędzia Azure IoT Explorer, potrzebne są:

- Usługa Azure IoT Hub. Istnieje wiele sposobów dodawania usługi IoT Hub do subskrypcji platformy Azure, takich jak [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-create-using-cli.md). Do uruchomienia narzędzia Azure IoT Explorer potrzebne są parametry połączenia z usługą IoT Hub. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Urządzenie zarejestrowane w usłudze IoT Hub. Aby zarejestrować urządzenie, można użyć poniższego polecenia interfejsu CLI platformy Azure. Pamiętaj, aby zastąpić `{YourIoTHubName}` `{YourDeviceID}` symbole zastępcze i wartościami:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Przejdź do [wersji programu Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) i rozwiń listę zasobów dla najnowszej wersji. Pobierz i zainstaluj najnowszą wersję aplikacji.

## <a name="use-azure-iot-explorer"></a>Korzystanie z programu Azure IoT Explorer

W przypadku urządzenia można podłączyć własne urządzenie lub użyć jednego z przykładowych symulowanych urządzeń. Wykonaj [te instrukcje](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) , aby uruchomić przykład symulowanego urządzenia.

### <a name="connect-to-your-hub"></a>Nawiązywanie połączenia z centrum

Przy pierwszym uruchomieniu programu Azure IoT Explorer zostanie wyświetlony monit o podanie parametrów połączenia Centrum IoT. Po dodaniu parametrów połączenia wybierz pozycję **Połącz**. Możesz użyć ustawień narzędzia, aby przełączyć się do innego Centrum IoT Hub przez zaktualizowanie parametrów połączenia.

Definicje modeli dla urządzenia IoT Plug and Play są przechowywane w repozytorium publicznym, w repozytorium firmy lub na podłączonym urządzeniu. Domyślnie narzędzie szuka definicji modelu w repozytorium modelu publicznego i podłączonym urządzeniu. Możesz dodawać i usuwać źródła albo konfigurować priorytety źródeł w **ustawieniach**:

Aby dodać Źródło:

1. Przejdź do obszaru **Settings** (Ustawienia).
1. Wybierz pozycję **Nowy** i wybierz źródło.
1. W przypadku dodawania repozytorium modelu firmy podaj parametry połączenia.

Aby usunąć Źródło:

1. Przejdź do obszaru **Settings** (Ustawienia).
1. Znajdź źródło, które chcesz usunąć.
1. Wybierz pozycję **X** , aby ją usunąć. Nie można usunąć repozytorium modelu publicznego, ponieważ wspólne definicje interfejsu pochodzą z tego repozytorium.

Zmień priorytety źródłowe:

Możesz przeciągać i upuszczać jeden ze źródeł definicji modelu do innej klasyfikacji na liście. Jeśli występuje konflikt, źródła definicji o wyższych klasyfikacjach przesłaniają źródła z niższą klasyfikacją.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Po połączeniu narzędzia z Centrum IoT zostanie wyświetlona strona lista **urządzeń** z listą tożsamości urządzeń zarejestrowanych w centrum IoT Hub. Można rozwinąć każdy wpis na liście, aby wyświetlić więcej informacji.

Na stronie lista **urządzeń** można wykonać następujące instrukcje:

- Wybierz pozycję **Dodaj** , aby zarejestrować nowe urządzenie w centrum. Następnie wprowadź identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i włączyć połączenie z centrum.
- Wybierz urządzenie, a następnie wybierz pozycję **Usuń** , aby usunąć tożsamość urządzenia. Przed ukończeniem tej akcji Przejrzyj szczegóły urządzenia, aby upewnić się, że usuwasz właściwą tożsamość urządzenia.
- Zapytanie według `capabilityID` i `interfaceID` . Dodaj swój `capabilityID` lub `interfaceID` jako parametr, aby wykonać zapytanie dotyczące urządzeń.

## <a name="interact-with-a-device"></a>Korzystanie z urządzenia

Na stronie lista **urządzeń** wybierz wartość w kolumnie **Identyfikator urządzenia** , aby wyświetlić stronę szczegółów dla zarejestrowanego urządzenia. Dla każdego urządzenia znajdują się dwie sekcje: **Device** i **Digital bliźniaczy**.

### <a name="device"></a>Urządzenie

Ta sekcja zawiera karty **tożsamość urządzenia**, **dane** **telemetrii, dane telemetryczne**, **metody bezpośrednie** i **komunikaty z chmury do urządzenia** .

- Informacje o [tożsamości urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md) można wyświetlić i zaktualizować na karcie **tożsamość urządzenia** .
- Dostęp do informacji o [bliźniaczych urządzeniach](../iot-hub/iot-hub-devguide-device-twins.md) można uzyskać na karcie **sznurki urządzenia** .
- Jeśli urządzenie jest połączone i aktywnie wysyła dane, [można je wyświetlić na karcie](../iot-hub/iot-hub-devguide-messages-read-builtin.md) **telemetrii** .
- [Metodę bezpośrednią](../iot-hub/iot-hub-devguide-direct-methods.md) można wywołać na urządzeniu na karcie **Metoda bezpośrednia** .
- [Komunikat z chmury do urządzenia](../iot-hub/iot-hub-devguide-messages-c2d.md) można wysłać na karcie **komunikaty z chmury do urządzenia** .

### <a name="digital-twin"></a>Cyfrowe sznurki

Możesz użyć narzędzia, aby wyświetlić wystąpienie cyfrowego przędzenia urządzenia. W przypadku urządzenia Plug and Play IoT wszystkie interfejsy skojarzone z modelem możliwości urządzenia są wyświetlane w tej sekcji Narzędzia. Wybierz interfejs, aby rozwinąć odpowiednie elementy podstawowe [Plug and Play IoT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Interfejs

Na stronie **interfejs** można wyświetlić definicję JSON interfejsu.

#### <a name="properties"></a>Właściwości

Właściwości tylko do odczytu zdefiniowane w interfejsie można wyświetlić na stronie **właściwości bez możliwości zapisu** . Można aktualizować właściwości do zapisu zdefiniowane w interfejsie na stronie właściwości do **zapisu** :

1. Przejdź do strony **właściwości z możliwością zapisu** .
1. Kliknij właściwość, którą chcesz zaktualizować.
1. Wprowadź nową wartość właściwości.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij zmianę.

Po przesłaniu zmiany można śledzić stan aktualizacji: **Synchronizacja**, **sukces**lub **błąd**. Po zakończeniu synchronizacji zostanie wyświetlona nowa wartość właściwości w kolumnie **raportowana Właściwość** . Jeśli przejdziesz do innych stron przed ukończeniem synchronizacji, narzędzie będzie nadal powiadamiane po zakończeniu aktualizacji. Możesz również użyć Centrum powiadomień narzędzia, aby wyświetlić historię powiadomień.

#### <a name="commands"></a>Polecenia

Aby wysłać polecenie do urządzenia, przejdź do strony **poleceń** :

1. Na liście poleceń Rozwiń polecenie, które ma zostać wyzwolone.
1. Wprowadź wszelkie wymagane wartości dla polecenia.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij polecenie.

#### <a name="telemetry"></a>Telemetria

Aby wyświetlić dane telemetryczne dla wybranego interfejsu, przejdź do strony **telemetrii** .

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób instalowania i używania programu Azure IoT Explorer do współpracy z urządzeniami Plug and Play IoT. Sugerowanym następnym krokiem jest zapoznanie się z tematem [Instalowanie i używanie rozszerzenia interfejsu wiersza polecenia platformy Azure](./howto-install-pnp-cli.md).
