---
title: Obsługa urządzeń w trybie offline — Azure IoT Edge | Microsoft Docs
description: Dowiedz się, IoT Edge i moduły mogą działać bez połączenia z Internetem przez dłuższy czas, oraz jak IoT Edge mogą umożliwiać regularne działanie urządzeń IoT również w trybie offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c9412e2adeb9b43b4c61437fb41e68bc96b86afd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481841"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Understand extended offline capabilities for IoT Edge devices, modules, and child devices (Opis rozszerzonych możliwości trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge obsługuje rozszerzone operacje offline na urządzeniach IoT Edge i umożliwia również operacje offline na urządzeniach IoT Edge innych niż urządzenia podrzędne. Jeśli urządzenie IoT Edge miało jedną okazję do nawiązania połączenia z usługą IoT Hub, to urządzenie i wszystkie urządzenia podrzędne mogą nadal działać przy sporadycznie lub bez połączenia internetowego.

## <a name="how-it-works"></a>Jak to działa

Gdy urządzenie IoT Edge przechodzi w tryb offline, centrum IoT Edge przyjmuje trzy role. Najpierw przechowuje wszystkie komunikaty, które byłyby nadrzędne, i zapisuje je do momentu ponownego nawiązania połączenia z urządzeniem. Pod drugie działa w imieniu usługi IoT Hub w celu uwierzytelniania modułów i urządzeń podrzędnych, aby mogły nadal działać. Po trzecie umożliwia komunikację między urządzeniami podrzędnymi, które zwykle przechodzą przez usługę IoT Hub.

Poniższy przykład pokazuje, jak scenariusz IoT Edge działa w trybie offline:

1. **Konfiguruj urządzenia**

   IoT Edge urządzenia mają automatycznie włączone możliwości trybu offline. Aby rozszerzyć tę możliwość na inne urządzenia IoT, należy zadeklarować relację nadrzędny-podrzędny między urządzeniami w IoT Hub. Następnie należy skonfigurować urządzenia podrzędne tak, aby ufały przypisanemu urządzeniu nadrzędnemu i przekierowyowały komunikację urządzenia do chmury za pośrednictwem urządzenia nadrzędnego jako bramy.

2. **Synchronizacja z IoT Hub**

   Co najmniej raz po zainstalowaniu IoT Edge uruchomieniowego urządzenie IoT Edge musi być w trybie online, aby można było zsynchronizować IoT Hub. Podczas tej synchronizacji urządzenie IoT Edge szczegółowe informacje o przypisanych do niego urządzeniach podrzędnych. Urządzenie IoT Edge również bezpiecznie aktualizuje swoją lokalną pamięć podręczną, aby umożliwić operacje offline i pobiera ustawienia lokalnego magazynu komunikatów telemetrycznych.

3. **Przejdź do trybu offline**

   Po odłączeniu IoT Hub urządzenie IoT Edge, jego wdrożone moduły i wszystkie urządzenia IoT dla dzieci mogą działać w sposób nieokreślony. Moduły i urządzenia podrzędne można uruchamiać i uruchamiać ponownie przez uwierzytelnianie za pomocą centrum IoT Edge w trybie offline. Dane telemetryczne powiązane z IoT Hub są przechowywane lokalnie. Komunikacja między modułami lub podrzędnym urządzeniami IoT jest utrzymywana za pośrednictwem metod bezpośrednich lub komunikatów.

4. **Ponowne nawiązywanie połączenia i ponowna synchronizacja z IoT Hub**

   Po przywróceniu IoT Hub urządzenie IoT Edge zostanie ponownie zsynchronizowane. Komunikaty przechowywane lokalnie są dostarczane do IoT Hub od razu, ale zależą od szybkości połączenia, IoT Hub opóźnienia i powiązanych czynników. Są one dostarczane w tej samej kolejności, w której były przechowywane.

   Wszelkie różnice między żądaną i zgłaszaną właściwością modułów i urządzeń są uzgadniane. Urządzenie IoT Edge aktualizuje wszelkie zmiany w zestawie przypisanych podrzędnych urządzeń IoT.

## <a name="restrictions-and-limits"></a>Ograniczenia i limity

Rozszerzone możliwości trybu offline opisane w tym artykule są dostępne w [IoT Edge wersji 1.0.7 lub wyższej.](https://github.com/Azure/azure-iotedge/releases) Wcześniejsze wersje mają podzestaw funkcji trybu offline. Istniejących urządzeń IoT Edge, które nie mają rozszerzonych możliwości trybu offline, nie można uaktualnić przez zmianę wersji środowiska uruchomieniowego, ale należy je ponownie skonfigurować przy użyciu nowej tożsamości urządzenia IoT Edge, aby uzyskać te funkcje.

Jako urządzenia podrzędne można dodawać tylko urządzenia inne niż IoT Edge.

IoT Edge urządzenia i ich przypisane urządzenia podrzędne mogą działać w trybie offline przez czas nieokreślony po początkowej synchronizacji. Jednak przechowywanie komunikatów zależy od ustawienia czasu wygaśnięcia (TTL) i dostępnego miejsca na dysku do przechowywania komunikatów.

## <a name="set-up-parent-and-child-devices"></a>Konfigurowanie urządzeń nadrzędnych i podrzędnych

Aby urządzenie IoT Edge rozszerzone możliwości trybu offline na podrzędne urządzenia IoT, należy wykonać dwa kroki. Najpierw zadeklaruj relacje nadrzędny-podrzędny w Azure Portal. Następnie utwórz relację zaufania między urządzeniem nadrzędnym i dowolnym urządzeniem podrzędnym, a następnie skonfiguruj komunikację między urządzeniem a chmurą, aby przechodziła przez urządzenie nadrzędne jako bramę.

### <a name="assign-child-devices"></a>Przypisywanie urządzeń podrzędnych

Urządzenia podrzędne mogą być dowolnymi urządzeniami IoT Edge zarejestrowanymi w tym samym IoT Hub. Urządzenia nadrzędne mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędne ma tylko jeden element nadrzędny. Istnieją trzy opcje ustawienia urządzenia podrzędnego na urządzenie brzegowe: za pośrednictwem interfejsu Azure Portal, przy użyciu interfejsu wiersza polecenia platformy Azure lub przy użyciu zestawu SDK IoT Hub service.

Poniższe sekcje zawierają przykłady sposobu deklarowania relacji nadrzędny/podrzędny w IoT Hub istniejących urządzeń IoT. Jeśli tworzysz nowe tożsamości urządzeń dla urządzeń podrzędnych, zobacz [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) (Uwierzytelnianie urządzenia podrzędnego Azure IoT Hub uzyskać więcej informacji.

#### <a name="option-1-iot-hub-portal"></a>Opcja 1. IoT Hub Portal

Relację nadrzędny-podrzędny można zadeklarować podczas tworzenia nowego urządzenia. Lub w przypadku istniejących urządzeń można zadeklarować relację na stronie szczegółów urządzenia nadrzędnego IoT Edge lub podrzędnego urządzenia IoT.

   ![Zarządzanie urządzeniami podrzędnym na stronie IoT Edge szczegóły urządzenia podrzędnego](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opcja 2. Korzystanie z `az` narzędzia wiersza polecenia

Za pomocą [interfejsu](/cli/azure/) wiersza polecenia platformy Azure z rozszerzeniem [IoT](https://github.com/azure/azure-iot-cli-extension) (wersja 0.7.0 lub nowsza) można zarządzać relacjami [nadrzędny-podrzędny](/cli/azure/iot/hub/device-identity/) za pomocą podpoleci tożsamości urządzenia. W poniższym przykładzie użyto zapytania w celu przypisania wszystkich urządzeń innych IoT Edge w centrum jako urządzeń IoT Edge urządzenia.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

Możesz zmodyfikować [zapytanie,](../iot-hub/iot-hub-devguide-query-language.md) aby wybrać inny podzestaw urządzeń. W przypadku określenia dużego zestawu urządzeń polecenie może potrwać kilka sekund.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opcja 3. Używanie zestawu SDK IoT Hub Service

Na koniec można programowo zarządzać relacjami nadrzędny-podrzędny przy użyciu języka C#, Java lub Node.js IoT Hub SERVICE SDK. Oto przykład [przypisywania urządzenia podrzędnego przy użyciu](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) zestawu SDK języka C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurowanie urządzenia nadrzędnego jako bramy

Relację nadrzędny/podrzędny można myśleć jak o przezroczystej bramie, w której urządzenie podrzędne ma własną tożsamość w usłudze IoT Hub ale komunikuje się za pośrednictwem chmury za pośrednictwem swojego elementu nadrzędnego. Aby zapewnić bezpieczną komunikację, urządzenie podrzędne musi mieć możliwość sprawdzenia, czy urządzenie nadrzędne pochodzi z zaufanego źródła. W przeciwnym razie inne firmy mogłyby skonfigurować złośliwe urządzenia podszywające się pod rodziców i przechwytywające komunikację.

Jeden ze sposobów tworzenia tej relacji zaufania został szczegółowo opisany w następujących artykułach:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Łączenie urządzenia podrzędnego z bramą Azure IoT Edge wirtualnej](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Określanie serwerów DNS

Aby zwiększyć niezawodność, zdecydowanie zaleca się określenie adresów serwerów DNS używanych w środowisku. Aby ustawić serwer DNS dla IoT Edge, zobacz rozwiązanie dla modułu agenta przeglądarki Edge stale zgłasza "pusty plik [konfiguracji"](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) i nie uruchamia żadnych modułów na urządzeniu w artykule rozwiązywania problemów.

## <a name="optional-offline-settings"></a>Opcjonalne ustawienia trybu offline

Jeśli urządzenia przejdą do trybu offline, IoT Edge nadrzędne przechowuje wszystkie komunikaty z urządzenia do chmury do momentu ponownego nawiązaniu połączenia. Moduł IoT Edge Hub zarządza magazynem i przekazywaniem komunikatów w trybie offline. W przypadku urządzeń, które mogą przejść w tryb offline przez dłuższy czas, zoptymalizuj wydajność, konfigurując dwa IoT Edge centrum danych.

Najpierw zwiększ czas transmisji na żywo, aby centrum danych IoT Edge przechowywać komunikaty wystarczająco długo, aby urządzenie ponownie nawiązyło połączenie. Następnie dodaj dodatkowe miejsce na dysku dla magazynu komunikatów.

### <a name="time-to-live"></a>Czas wygaśnięcia

Ustawienie czas wygaśnięcia to czas (w sekundach), po upływie których komunikat może czekać na jego wygaśnięcie. Wartość domyślna to 7200 sekund (dwie godziny). Wartość maksymalna jest ograniczona tylko przez maksymalną wartość zmiennej całkowitej, która wynosi około 2 miliardów.

To ustawienie jest żądaną właściwością centrum IoT Edge, które jest przechowywane w bliźniaczej reprezentacji modułu. Można go skonfigurować w Azure Portal lub bezpośrednio w manifeście wdrożenia.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Magazyn hosta dla modułów systemowych

Komunikaty i informacje o stanie modułu są domyślnie przechowywane IoT Edge lokalnym systemie plików kontenera centrum. W celu zwiększenia niezawodności, szczególnie podczas pracy w trybie offline, można również przeznaczyć magazyn na hoście IoT Edge urządzeniu. Aby uzyskać więcej informacji, zobacz [Zapewnianie modułom dostępu do magazynu lokalnego urządzenia](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat sposobu skonfigurowania przezroczystej bramy dla połączeń urządzenia nadrzędnego/podrzędnego:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)
