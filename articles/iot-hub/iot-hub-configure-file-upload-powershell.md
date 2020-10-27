---
title: Użyj Azure PowerShell, aby skonfigurować przekazywanie plików | Microsoft Docs
description: Jak skonfigurować Centrum IoT w celu umożliwienia przekazywania plików z połączonych urządzeń za pomocą poleceń cmdlet Azure PowerShell. Zawiera informacje o konfigurowaniu docelowego konta usługi Azure Storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536015"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurowanie przekazywania plików IoT Hub przy użyciu programu PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby skorzystać z [funkcji przekazywania plików w IoT Hub](iot-hub-devguide-file-upload.md), musisz najpierw skojarzyć konto usługi Azure Storage z Twoim centrum IoT. Możesz użyć istniejącego konta magazynu lub utworzyć nowe.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Polecenia cmdlet Azure PowerShell](/powershell/azure/install-Az-ps).

* Usługa Azure IoT Hub. Jeśli nie masz Centrum IoT Hub, możesz użyć [polecenia cmdlet New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) , aby utworzyć jeden lub użyć portalu do [utworzenia Centrum IoT](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz użyć [poleceń cmdlet programu PowerShell usługi Azure Storage](/powershell/module/az.storage/) , aby utworzyć [konto magazynu](../storage/common/storage-account-create.md) lub użyć tego portalu

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i Skonfiguruj swoje konto platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia programu PowerShell uruchom polecenie cmdlet **Connect-AzAccount** :

    ```powershell
    Connect-AzAccount
    ```

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się na platformie Azure spowoduje przyznanie dostępu do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Użyj następującego polecenia, aby wyświetlić listę dostępnych subskrypcji platformy Azure do użycia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj poniższego polecenia, aby wybrać subskrypcję, która ma być używana do uruchamiania poleceń zarządzania centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobierz szczegóły konta magazynu

W poniższych krokach przyjęto założenie, że konto magazynu zostało utworzone przy użyciu modelu wdrażania **Menedżer zasobów** , a nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, potrzebne są parametry połączenia dla konta usługi Azure Storage. Konto magazynu musi znajdować się w tej samej subskrypcji co centrum IoT. Potrzebna jest również nazwa kontenera obiektów BLOB na koncie magazynu. Użyj następującego polecenia, aby pobrać klucze konta magazynu:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Zanotuj wartość klucza konta magazynu **Klucz1** . Jest to konieczne w poniższych krokach.

Możesz użyć istniejącego kontenera obiektów BLOB do przekazywania plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów BLOB na koncie magazynu, użyj następujących poleceń:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Aby utworzyć kontener obiektów BLOB na koncie magazynu, użyj następujących poleceń:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurowanie Centrum IoT Hub

Teraz możesz skonfigurować Centrum IoT w celu [przekazywania plików do usługi IoT Hub,](iot-hub-devguide-file-upload.md) korzystając ze szczegółowych informacji o koncie magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu** : kontener obiektów BLOB na koncie usługi Azure Storage w bieżącej subskrypcji platformy Azure do skojarzenia z Twoim centrum IoT Hub. Pobrano niezbędne informacje o koncie magazynu w poprzedniej sekcji. IoT Hub automatycznie generuje identyfikatory URI SAS z uprawnieniami do zapisu w tym kontenerze obiektów BLOB dla urządzeń, które będą używane podczas przekazywania plików.

* **Odbieraj powiadomienia dla przekazanych plików** : Włącz lub Wyłącz powiadomienia dotyczące przekazywania plików.

* Czas **wygaśnięcia sygnatury dostępu współdzielonego** : to ustawienie jest czasem wygaśnięcia identyfikatorów URI sygnatury dostępu współdzielonego zwróconego do urządzenia przez IoT Hub. Domyślnie ustawiona na godzinę.

* **Ustawienia powiadomień o pliku domyślny czas wygaśnięcia** : godzina wygaśnięcia powiadomienia o przekazaniu pliku przed jego wygaśnięciem. Domyślnie ustawiona na jeden dzień.

* **Maksymalna liczba dostaw powiadomień o pliku** : liczba prób dostarczenia przez IoT Hub powiadomienia o przekazaniu pliku. Domyślnie ustawiona na 10.

Użyj następującego polecenia cmdlet programu PowerShell, aby skonfigurować ustawienia przekazywania plików w centrum IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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