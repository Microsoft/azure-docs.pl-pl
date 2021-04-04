---
title: Konfigurowanie przekazywania plików do IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Jak skonfigurować przekazywanie plików do usługi Azure IoT Hub przy użyciu międzyplatformowego interfejsu wiersza polecenia platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536066"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie przekazywania plików IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby [przekazać pliki z urządzenia](iot-hub-devguide-file-upload.md), musisz najpierw skojarzyć konto usługi Azure Storage z Centrum IoT Hub. Możesz użyć istniejącego konta magazynu lub utworzyć nowe.

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* Usługa Azure IoT Hub. Jeśli nie masz usługi IoT Hub, możesz użyć [ `az iot hub create` polecenia](/cli/azure/iot/hub#az-iot-hub-create) w celu utworzenia jednego lub [utworzenia Centrum IoT Hub przy użyciu portalu](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, utwórz je za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i Skonfiguruj swoje konto platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia uruchom [polecenie logowania](/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure](/cli/azure/account) dostępnych do użycia:

    ```azurecli
    az account list
    ```

    Użyj poniższego polecenia, aby wybrać subskrypcję, która ma być używana do uruchamiania poleceń w celu utworzenia Centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobierz szczegóły konta magazynu

W poniższych krokach przyjęto założenie, że konto magazynu zostało utworzone przy użyciu modelu wdrażania **Menedżer zasobów** , a nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, potrzebne są parametry połączenia dla konta usługi Azure Storage. Konto magazynu musi znajdować się w tej samej subskrypcji co centrum IoT. Potrzebna jest również nazwa kontenera obiektów BLOB na koncie magazynu. Użyj następującego polecenia, aby pobrać klucze konta magazynu:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Zanotuj wartość **ConnectionString** . Jest to konieczne w poniższych krokach.

Możesz użyć istniejącego kontenera obiektów BLOB do przekazywania plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów BLOB na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów BLOB na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie plików

Teraz można skonfigurować Centrum IoT, aby umożliwić [przekazywanie plików do usługi IoT Hub](iot-hub-devguide-file-upload.md) przy użyciu szczegółów konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu**: kontener obiektów BLOB na koncie usługi Azure Storage w bieżącej subskrypcji platformy Azure do skojarzenia z Twoim centrum IoT Hub. Pobrano niezbędne informacje o koncie magazynu w poprzedniej sekcji. IoT Hub automatycznie generuje identyfikatory URI SAS z uprawnieniami do zapisu w tym kontenerze obiektów BLOB dla urządzeń, które będą używane podczas przekazywania plików.

* **Odbieraj powiadomienia dla przekazanych plików**: Włącz lub Wyłącz powiadomienia dotyczące przekazywania plików.

* Czas **wygaśnięcia sygnatury dostępu współdzielonego**: to ustawienie jest czasem wygaśnięcia identyfikatorów URI sygnatury dostępu współdzielonego zwróconego do urządzenia przez IoT Hub. Domyślnie ustawiona na godzinę.

* **Ustawienia powiadomień o pliku domyślny czas wygaśnięcia**: godzina wygaśnięcia powiadomienia o przekazaniu pliku przed jego wygaśnięciem. Domyślnie ustawiona na jeden dzień.

* **Maksymalna liczba dostaw powiadomień o pliku**: liczba prób dostarczenia przez IoT Hub powiadomienia o przekazaniu pliku. Domyślnie ustawiona na 10.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby skonfigurować ustawienia przekazywania plików w centrum IoT:

<!--Robinsh this is out of date, add cloud powershell -->

W bash Shell, użyj:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfigurację przekazywania plików można przejrzeć w centrum IoT Hub przy użyciu następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat możliwości przekazywania plików IoT Hub, zobacz [przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md).

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorowanie Centrum IoT Hub](monitor-iot-hub.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpiecz swoje rozwiązanie IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)