---
title: Konfigurowanie przekazywania plików do usługi IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Jak skonfigurować przekazywanie plików do usługi Azure IoT Hub pomocą międzyplatformowego interfejsu wiersza polecenia platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: ea6ec30ad5f3b1cdbc906cc94cb211295b84e802
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761731"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie IoT Hub przekazywania plików przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby [przekazać pliki z urządzenia,](iot-hub-devguide-file-upload.md)należy najpierw skojarzyć konto usługi Azure Storage z centrum IoT. Możesz użyć istniejącego konta magazynu lub utworzyć nowe.

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* Centrum Azure IoT Hub. Jeśli nie masz centrum IoT Hub, możesz [ `az iot hub create` ](/cli/azure/iot/hub#az_iot_hub_create) je utworzyć za pomocą polecenia lub utworzyć centrum [IoT hub przy użyciu portalu](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz je utworzyć za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Logowanie i ustawianie konta platformy Azure

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

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz użyć do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W poniższych krokach założono, że konto magazynu utworzono przy użyciu **modelu Resource Manager,** a nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, potrzebujesz parametrów połączenia dla konta usługi Azure Storage. Konto magazynu musi znajdować się w tej samej subskrypcji co centrum IoT. Potrzebna jest również nazwa kontenera obiektów blob na koncie magazynu. Użyj następującego polecenia, aby pobrać klucze konta magazynu:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Zanotuj **wartość connectionString.** Będzie on potrzebny w następujących krokach.

Do przekazywania plików możesz użyć istniejącego kontenera obiektów blob lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie plików

Teraz możesz skonfigurować centrum IoT, aby umożliwić przekazywanie plików do centrum [IoT przy](iot-hub-devguide-file-upload.md) użyciu szczegółów konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu:** kontener obiektów blob na koncie usługi Azure Storage w bieżącej subskrypcji platformy Azure do skojarzenia z centrum IoT. W poprzedniej sekcji pobrano niezbędne informacje o koncie magazynu. IoT Hub automatycznie generuje sygnatury dostępu współdzielonego z uprawnieniami do zapisu w tym kontenerze obiektów blob dla urządzeń do użycia podczas przekazywania plików.

* **Otrzymywanie powiadomień dotyczących przekazanych plików:** włącza lub wyłącza powiadomienia o przekazywaniu plików.

* **Czas wygaśnięcia sygnatury** dostępu współdzielonego: to ustawienie określa czas wygaśnięcia adresów URI sygnatur dostępu współdzielonego zwracanych do urządzenia przez IoT Hub. Ustaw wartość domyślną na jedną godzinę.

* **Domyślny czas wygaśnięcia ustawień** powiadomienia o pliku: czas wygaśnięcia powiadomienia o przekazywaniu pliku przed jego wygaśnięciem. Ustaw wartość domyślną na jeden dzień.

* **Maksymalna liczba dostarczania powiadomień o plikach:** liczba prób IoT Hub dostarczenia powiadomienia o przekazywaniu pliku. Domyślnie ustaw wartość 10.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby skonfigurować ustawienia przekazywania plików w centrum IoT:

<!--Robinsh this is out of date, add cloud powershell -->

W powłoce bash użyj:

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

Konfigurację przekazywania plików w centrum IoT Hub można przejrzeć za pomocą następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat możliwości przekazywania plików IoT Hub, zobacz [Przekazywanie plików z urządzenia.](iot-hub-devguide-file-upload.md)

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorowanie centrum IoT](monitor-iot-hub.md)

Aby bardziej eksplorować możliwości IoT Hub, zobacz:

* [IoT Hub dla deweloperów](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpieczanie rozwiązania IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)