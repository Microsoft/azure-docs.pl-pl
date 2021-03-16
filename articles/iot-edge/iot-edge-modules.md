---
title: Dowiedz się, jak moduły uruchamiają logikę na urządzeniach — Azure IoT Edge | Microsoft Docs
description: Moduły Azure IoT Edge są kontenerami logiki, które mogą być wdrażane i zarządzane zdalnie w taki sposób, aby można było uruchamiać logikę biznesową na urządzeniach IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492374"
---
# <a name="understand-azure-iot-edge-modules"></a>Omówienie modułów usługi Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią na krawędzi w formie *modułów*. Moduły Azure IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure (takie jak Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, jak moduły są opracowywane, wdrażane i konserwowane, należy wziąć pod uwagę cztery elementy koncepcyjne modułu:

* **Obraz modułu** to pakiet zawierający oprogramowanie, które definiuje moduł.
* **Wystąpienie modułu** to określona jednostka obliczeniowa, która uruchamia obraz modułu na urządzeniu IoT Edge. Wystąpienie modułu jest uruchamiane przez środowisko uruchomieniowe IoT Edge.
* **Tożsamość modułu** jest częścią informacji (łącznie z poświadczeniami zabezpieczeń) przechowywaną w IoT Hub, która jest skojarzona z każdym wystąpieniem modułu.
* **Sznurek modułu** jest dokument JSON przechowywany w IoT Hub, który zawiera informacje o stanie wystąpienia modułu, w tym metadane, konfiguracje i warunki.

## <a name="module-images-and-instances"></a>Obrazy i wystąpienia modułu

Obrazy modułu IoT Edge zawierają aplikacje, które wykorzystują funkcje zarządzania, zabezpieczeń i komunikacji środowiska uruchomieniowego IoT Edge. Możesz tworzyć własne obrazy modułów lub wyeksportować je z obsługiwanej usługi platformy Azure, takiej jak Azure Stream Analytics.
Obrazy istnieją w chmurze i można je zaktualizować, zmienić i wdrożyć w różnych rozwiązaniach. Na przykład moduł korzystający z uczenia maszynowego do przewidywania danych wyjściowych w wierszu produkcji istnieje jako oddzielny obraz niż moduł, który używa wizji komputera do kontrolowania drona.

Za każdym razem, gdy obraz modułu jest wdrażany na urządzeniu i uruchamiany przez środowisko uruchomieniowe IoT Edge, tworzone jest nowe wystąpienie tego modułu. Dwa urządzenia w różnych częściach świata mogą korzystać z tego samego obrazu modułu. Jednak każde urządzenie będzie miało własne wystąpienie modułu, gdy moduł zostanie uruchomiony na urządzeniu.

![Diagramy — obrazy modułów w chmurze, wystąpienia modułów na urządzeniach](./media/iot-edge-modules/image_instance.png)

W implementacji, obrazy modułów istnieją jako obrazy kontenerów w repozytorium, a wystąpienia modułów są kontenerami na urządzeniach.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Tożsamości modułów

Gdy nowe wystąpienie modułu jest tworzone przez środowisko uruchomieniowe IoT Edge, pobiera odpowiednią tożsamość modułu. Tożsamość modułu jest przechowywana w IoT Hub i jest używana jako zakres adresów i zabezpieczeń dla całej komunikacji lokalnej i w chmurze dla tego wystąpienia modułu.

Tożsamość skojarzona z wystąpieniem modułu zależy od tożsamości urządzenia, na którym uruchomiono wystąpienie, oraz nazwy podanych dla tego modułu w rozwiązaniu. Na przykład, jeśli wywołasz `insight` moduł, który używa Azure Stream Analytics i zostanie on wdrożony na urządzeniu o nazwie `Hannover01` , środowisko uruchomieniowe IoT Edge tworzy odpowiednią tożsamość modułu o nazwie `/devices/Hannover01/modules/insight` .

Jasno, w sytuacjach, gdy konieczne jest wielokrotne wdrożenie jednego obrazu modułu na tym samym urządzeniu, można wdrożyć ten sam obraz wielokrotnie przy użyciu różnych nazw.

![Diagramy — tożsamości modułów są unikatowe w obrębie urządzeń i między urządzeniami](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Moduł bliźniaczych reprezentacji

Każde wystąpienie modułu ma również odpowiednią sznurek modułu, którego można użyć do skonfigurowania wystąpienia modułu. Wystąpienie i sznury są skojarzone ze sobą za pomocą tożsamości modułu.

Sznurki modułu jest dokumentem JSON, który przechowuje informacje o module i właściwości konfiguracji. Pojęcie to jest równoległe do koncepcji [przędzy urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) z IoT Hub. Struktura sznurka modułu jest taka sama jak sznurki urządzenia. Interfejsy API używane do współpracy z obydwoma typami bliźniaczych reprezentacji są również takie same. Jedyną różnicą między nimi jest tożsamość użyta do utworzenia wystąpienia zestawu SDK klienta.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Możliwości w trybie offline

Moduły Azure IoT Edge mogą działać w trybie offline w nieskończoność po synchronizacji z IoT Hub co najmniej raz. Urządzenia IoT Edge mogą również zwiększyć tę możliwość w trybie offline na innych urządzeniach IoT. Aby uzyskać więcej informacji, zobacz informacje o [rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych](offline-capabilities.md).

## <a name="next-steps"></a>Następne kroki

* [Informacje na temat wymagań i narzędzi do opracowywania modułów IoT Edge](module-development.md)
* [Poznaj środowisko uruchomieniowe Azure IoT Edge i jego architekturę](iot-edge-runtime.md)
