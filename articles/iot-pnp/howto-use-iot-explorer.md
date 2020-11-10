---
title: Instalowanie i używanie programu Azure IoT Explorer | Microsoft Docs
description: Zainstaluj narzędzie Azure IoT Explorer i użyj go do współpracy z urządzeniami Plug and Play IoT podłączonymi do usługi IoT Hub. Mimo że ten artykuł koncentruje się na pracy z urządzeniami Plug and Play IoT, można użyć narzędzia z dowolnym urządzeniem podłączonym do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperfq2
ms.openlocfilehash: 8482ba608ee5fcefb006234b339cd9b711a38020
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445306"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalowanie i korzystanie z programu Azure IoT Explorer

Program Azure IoT Explorer jest graficznym narzędziem do współdziałania z usługami i urządzeniami podłączonymi do centrum IoT Hub. Ten artykuł koncentruje się na używaniu narzędzia do testowania urządzeń Plug and Play IoT. Po zainstalowaniu narzędzia na komputerze lokalnym można używać go do nawiązywania połączenia z koncentratorem. Można użyć narzędzia, aby wyświetlić dane telemetryczne wysyłane przez urządzenia, korzystać z właściwości urządzenia i wywoływać polecenia.

W tym artykule wyjaśniono, jak:

- Zainstaluj i skonfiguruj narzędzie Azure IoT Explorer.
- Użyj narzędzia, aby korzystać z i testować urządzenia Plug and Play IoT.

Aby uzyskać ogólne informacje na temat korzystania z narzędzia, zobacz [plik Readme](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)usługi GitHub.

Aby korzystać z narzędzia Azure IoT Explorer, potrzebne są:

- Usługa Azure IoT Hub. Istnieje wiele sposobów dodawania usługi IoT Hub do subskrypcji platformy Azure, takich jak [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-create-using-cli.md). Do uruchomienia narzędzia Azure IoT Explorer potrzebne są parametry połączenia z usługą IoT Hub. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Urządzenie zarejestrowane w usłudze IoT Hub. Za pomocą programu IoT Explorer można tworzyć rejestracje urządzeń i zarządzać nimi w IoT Hub.

## <a name="install-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Przejdź do [wersji programu Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) i rozwiń listę zasobów dla najnowszej wersji. Pobierz i zainstaluj najnowszą wersję aplikacji.

>[!Important]
> Aktualizacja do wersji 0.13. x w celu rozpoznania modeli z dowolnego repozytorium na podstawie [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Korzystanie z programu Azure IoT Explorer

W przypadku urządzenia można podłączyć własne urządzenie lub użyć jednego z przykładowych symulowanych urządzeń. W przypadku niektórych przykładowych symulowanych urządzeń zapisanych w różnych językach zapoznaj się z [aplikacją Connect a sample Plug and Play Device, aby IoT Hub](quickstart-connect-device-node.md) przewodników Szybki Start.

### <a name="connect-to-your-hub"></a>Nawiązywanie połączenia z centrum

Przy pierwszym uruchomieniu programu Azure IoT Explorer zostanie wyświetlony monit o podanie parametrów połączenia Centrum IoT. Po dodaniu parametrów połączenia wybierz pozycję **Połącz**. Możesz użyć ustawień narzędzia, aby przełączyć się do innego Centrum IoT Hub przez zaktualizowanie parametrów połączenia.

Definicje modeli dla urządzenia IoT Plug and Play są przechowywane w repozytorium publicznym, podłączonym urządzeniu lub w folderze lokalnym. Domyślnie narzędzie szuka definicji modelu w repozytorium publicznym i podłączonym urządzeniu. Możesz dodawać i usuwać źródła albo konfigurować priorytety źródeł w **ustawieniach** :

Aby dodać Źródło:

1. Przejdź do **ustawień Plug and Play domu/IoT**
2. Wybierz pozycję **Dodaj** , a następnie wybierz źródło, z repozytorium lub folderu lokalnego.

Aby usunąć Źródło:

1. Przejdź do **ustawień Plug and Play domu/IoT**
2. Znajdź źródło, które chcesz usunąć.
3. Wybierz pozycję **X** , aby ją usunąć.

Zmień priorytety źródłowe:

Możesz przeciągać i upuszczać jeden ze źródeł definicji modelu do innej klasyfikacji na liście.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Po połączeniu narzędzia z Centrum IoT zostanie wyświetlona strona lista **urządzeń** z listą tożsamości urządzeń zarejestrowanych w centrum IoT Hub. Możesz wybrać dowolny wpis na liście, aby wyświetlić więcej informacji.

Na stronie lista **urządzeń** można wykonać następujące instrukcje:

- Wybierz pozycję **Nowy** , aby zarejestrować nowe urządzenie w centrum. Następnie wprowadź identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i włączyć połączenie z centrum.
- Wybierz urządzenie, a następnie wybierz pozycję **Usuń** , aby usunąć tożsamość urządzenia. Przed ukończeniem tej akcji Przejrzyj szczegóły urządzenia, aby upewnić się, że usuwasz właściwą tożsamość urządzenia.

## <a name="interact-with-a-device"></a>Korzystanie z urządzenia

Na stronie lista **urządzeń** wybierz wartość w kolumnie **Identyfikator urządzenia** , aby wyświetlić stronę szczegółów dla zarejestrowanego urządzenia. Dla każdego urządzenia znajdują się dwie sekcje: **Device** i **Digital bliźniaczy**.

### <a name="device"></a>Urządzenie

Ta sekcja obejmuje **tożsamość urządzenia** ,  **sznurki urządzenia** , **dane telemetryczne** , **metodę bezpośrednią** , **komunikat z chmury do urządzenia** , karty **tożsamości modułów**  .

- Informacje o [tożsamości urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md) można wyświetlić i zaktualizować na karcie **tożsamość urządzenia** .
- Dostęp do informacji o [bliźniaczych urządzeniach](../iot-hub/iot-hub-devguide-device-twins.md) można uzyskać na karcie **sznurki urządzenia** .
- Jeśli urządzenie jest połączone i aktywnie wysyła dane, [można je wyświetlić na karcie](../iot-hub/iot-hub-devguide-messages-read-builtin.md) **telemetrii** .
- [Metodę bezpośrednią](../iot-hub/iot-hub-devguide-direct-methods.md) można wywołać na urządzeniu na karcie **Metoda bezpośrednia** .
- [Komunikat z chmury do urządzenia](../iot-hub/iot-hub-devguide-messages-c2d.md) można wysłać na karcie **komunikaty z chmury do urządzenia** .
- Możesz uzyskać dostęp do informacji o [bliźniaczych modułach](../iot-hub/iot-hub-devguide-module-twins.md) .

### <a name="iot-plug-and-play-components"></a>Składniki Plug and Play IoT

Jeśli urządzenie jest połączone z centrum przy użyciu **identyfikatora modelu** , narzędzie wyświetli kartę **składniki Plug and Play IoT** , w której można zobaczyć **Identyfikator modelu**.

Jeśli **Identyfikator modelu** jest dostępny w jednym ze skonfigurowanych źródeł — publicznego repozytorium lub folderu lokalnego, zostanie wyświetlona lista składników. Wybranie składnika powoduje wyświetlenie dostępnych właściwości, poleceń i danych telemetrycznych.

Na stronie **składnik** można wyświetlić właściwości tylko do odczytu, zaktualizować właściwości zapisywalne, wywołać polecenia i wyświetlić komunikaty telemetryczne wytwarzane przez ten składnik.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Wyświetlanie składników w programie Azure IoT Explorer":::

#### <a name="properties"></a>Właściwości

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Wyświetlanie właściwości w programie Azure IoT Explorer":::

Właściwości tylko do odczytu zdefiniowane w interfejsie można wyświetlić na karcie **właściwości (tylko do odczytu)** . Można zaktualizować zapisywalne właściwości zdefiniowane w interfejsie na karcie **właściwości (zapisywalne)** :

1. Przejdź do karty **właściwości (zapisywalne)** .
1. Kliknij właściwość, którą chcesz zaktualizować.
1. Wprowadź nową wartość właściwości.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij zmianę.

Po przesłaniu zmiany można śledzić stan aktualizacji: **Synchronizacja** , **sukces** lub **błąd**. Po zakończeniu synchronizacji zostanie wyświetlona nowa wartość właściwości w kolumnie **raportowana Właściwość** . Jeśli przejdziesz do innych stron przed ukończeniem synchronizacji, narzędzie będzie nadal powiadamiane po zakończeniu aktualizacji. Możesz również użyć Centrum powiadomień narzędzia, aby wyświetlić historię powiadomień.

#### <a name="commands"></a>Polecenia

Aby wysłać polecenie do urządzenia, przejdź do karty **polecenia** :

1. Na liście poleceń Rozwiń polecenie, które ma zostać wyzwolone.
1. Wprowadź wszelkie wymagane wartości dla polecenia.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij polecenie.

#### <a name="telemetry"></a>Telemetria

Aby wyświetlić dane telemetryczne dla wybranego interfejsu, przejdź do karty **telemetrii** .

#### <a name="known-issues"></a>Znane problemy

Aby zapoznać się z listą funkcji IoT obsługiwanych przez najnowszą wersję narzędzia, zobacz [Lista funkcji](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób instalowania i używania programu Azure IoT Explorer do współpracy z urządzeniami Plug and Play IoT. Sugerowanym następnym krokiem jest zapoznanie się z tematem [Instalowanie i używanie narzędzi autorskich DTDL](howto-use-dtdl-authoring-tools.md).
