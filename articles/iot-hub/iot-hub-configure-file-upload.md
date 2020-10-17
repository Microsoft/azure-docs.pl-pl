---
title: Użyj Azure Portal, aby skonfigurować przekazywanie plików | Microsoft Docs
description: Używanie Azure Portal do konfigurowania Centrum IoT Hub w celu umożliwienia przekazywania plików z połączonych urządzeń. Zawiera informacje o konfigurowaniu docelowego konta usługi Azure Storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: 8d374ff372e0bc3fb183d9210f663987e4804114
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142524"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurowanie przekazywania plików w usłudze IoT Hub przy użyciu witryny Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Przekazywanie plików

Aby skorzystać z [funkcji przekazywania plików w IoT Hub](iot-hub-devguide-file-upload.md), musisz najpierw skojarzyć konto usługi Azure Storage z centrum. Wybierz pozycję **Przekaż plik** , aby wyświetlić listę właściwości przekazywania plików dla Centrum IoT, które jest modyfikowane.

![Wyświetlanie ustawień przekazywania plików IoT Hub w portalu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontener magazynu**: Użyj Azure Portal, aby wybrać kontener obiektów BLOB na koncie usługi Azure Storage w bieżącej subskrypcji platformy Azure, aby skojarzyć go z IoT Hub. W razie potrzeby możesz utworzyć konto usługi Azure Storage w bloku **konta magazynu** i kontenera obiektów BLOB w bloku **kontenerów** . IoT Hub automatycznie generuje identyfikatory URI SAS z uprawnieniami do zapisu w tym kontenerze obiektów BLOB dla urządzeń, które będą używane podczas przekazywania plików.

   ![Wyświetlanie kontenerów magazynu na potrzeby przekazywania plików w portalu](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Odbieraj powiadomienia dla przekazanych plików**: Włącz lub Wyłącz powiadomienia przekazywania plików za pośrednictwem przełącznika.

* Czas **wygaśnięcia sygnatury dostępu współdzielonego**: to ustawienie jest czasem wygaśnięcia identyfikatorów URI sygnatury dostępu współdzielonego zwróconego do urządzenia przez IoT Hub. Domyślnie ustawione na jedną godzinę, ale można je dostosować do innych wartości przy użyciu suwaka.

* **Ustawienia powiadomień o pliku domyślny czas wygaśnięcia**: godzina wygaśnięcia powiadomienia o przekazaniu pliku przed jego wygaśnięciem. Ustaw na jeden dzień domyślnie, ale można go dostosować do innych wartości przy użyciu suwaka.

* **Maksymalna liczba dostaw powiadomień o pliku**: liczba prób dostarczenia przez IoT Hub powiadomienia o przekazaniu pliku. Domyślnie ustawione na 10, ale można je dostosować do innych wartości przy użyciu suwaka.

   ![Konfigurowanie przekazywania plików IoT Hub w portalu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o możliwościach przekazywania plików IoT Hub, zobacz [przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) w przewodniku dewelopera IoT Hub.

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpiecz swoje rozwiązanie IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)