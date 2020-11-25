---
title: Duże relokalizacja w języku C++/WinRT
description: Szczegółowe informacje na temat sposobu tworzenia i lokalizowania kotwic przy użyciu grubej relokalizacji w języku C++/WinRT.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7068ecb7fcfe888118396523e0e3d997e5a59e9a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005014"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-cwinrt"></a>Jak utworzyć i zlokalizować kotwice przy użyciu grubej relokalizacji w języku C++/WinRT

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [System Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Kotwice przestrzenne platformy Azure mogą kojarzyć dane z czujników na urządzeniu, umieszczając w nich kotwice. Te dane mogą również służyć do szybkiego ustalenia, czy w pobliżu urządzenia znajdują się jakieś kotwice. Aby uzyskać więcej informacji, zobacz artykuł o [niedużej lokalizacji](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, upewnij się, że masz:

- Podstawowa wiedza na temat języka C++ i <a href="/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">interfejsów api środowisko wykonawcze systemu Windows</a>.
- Zapoznaj się z [omówieniem zakotwiczenia przestrzennego platformy Azure](../overview.md).
- Ukończono jedno z [5-minutowych przewodników szybki start](../index.yml).
- Zapoznaj się z [tematem tworzenie i lokalizowanie kotwic](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]