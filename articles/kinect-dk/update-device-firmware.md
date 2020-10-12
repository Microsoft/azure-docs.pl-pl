---
title: Aktualizowanie oprogramowania układowego usługi Azure urządzenia Kinect DK
description: Dowiedz się, jak zaktualizować oprogramowanie układowe usługi Azure urządzenia Kinect DK przy użyciu narzędzia oprogramowania układowego platformy Azure urządzenia Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, oprogramowanie układowe, aktualizacja, odzyskiwanie
ms.openlocfilehash: ecfa4a18592d3bc70e3b7cdd66ff6464a54e560d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030688"
---
# <a name="update-azure-kinect-dk-firmware"></a>Aktualizowanie oprogramowania układowego usługi Azure urządzenia Kinect DK

Ten dokument zawiera wskazówki dotyczące aktualizowania oprogramowania układowego urządzenia na platformie Azure urządzenia Kinect DK.

Usługa Azure urządzenia Kinect DK nie aktualizuje automatycznie oprogramowania układowego. Aby zaktualizować oprogramowanie układowe ręcznie do najnowszej dostępnej wersji, można użyć [narzędzia oprogramowania układowego platformy Azure urządzenia Kinect](azure-kinect-firmware-tool.md) .

## <a name="prepare-for-firmware-update"></a>Przygotowanie do aktualizacji oprogramowania układowego

1. [Pobierz zestaw SDK](sensor-sdk-download.md).
2. Zainstaluj zestaw SDK.
3. W lokalizacji instalacji zestawu SDK w obszarze (lokalizacja instalacji zestawu SDK) \tools\ należy znaleźć następujące informacje:

    - AzureKinectFirmwareTool.exe
    - Plik firmware. bin w folderze oprogramowania układowego, taki jak *AzureKinectDK_Fw_1.5.926614. bin*.

4. Podłącz urządzenie do komputera hosta i włącz go również.

> [!IMPORTANT]
> Podczas aktualizacji oprogramowania układowego należy zachować połączenie USB i zasilanie. Usunięcie jednego z połączeń podczas aktualizacji może spowodować uszkodzenie oprogramowania układowego.

## <a name="update-device-firmware"></a>Aktualizowanie oprogramowania układowego urządzenia

1. Otwórz wiersz polecenia w folderze (lokalizacja instalacji zestawu SDK) \tools\.
2. Aktualizowanie oprogramowania układowego przy użyciu narzędzia oprogramowania układowego Azure urządzenia Kinect

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Przykład:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Poczekaj na zakończenie aktualizacji oprogramowania układowego. Może to potrwać kilka minut w zależności od rozmiaru obrazu.

### <a name="verify-device-firmware-version"></a>Sprawdź wersję oprogramowania układowego urządzenia

1. Sprawdź, czy oprogramowanie układowe zostało zaktualizowane.

    `AzureKinectFirmwareTool.exe -q`

2. Zapoznaj się z poniższym przykładem.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Jeśli zobaczysz powyższe dane wyjściowe, oprogramowanie układowe zostanie zaktualizowane.

4. Po aktualizacji oprogramowania układowego można uruchomić [usługę Azure urządzenia Kinect Viewer](azure-kinect-viewer.md) , aby upewnić się, że wszystkie Czujniki działają zgodnie z oczekiwaniami.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aktualizacje oprogramowania układowego mogą się nie powieść z kilku powodów. Gdy aktualizacja oprogramowania układowego nie powiedzie się, spróbuj wykonać następujące czynności zaradcze:

1. Spróbuj uruchomić polecenie aktualizacji oprogramowania układowego po raz drugi.

2. Upewnij się, że urządzenie jest nadal połączone, badając wersję oprogramowania układowego.        AzureKinectFirmareTool.exe

3. Jeśli wszystkie inne opcje zakończą się niepowodzeniem, wykonaj kroki [odzyskiwania](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) , aby powrócić do oprogramowania układowego fabryki, i spróbuj ponownie.

Dodatkowe problemy można znaleźć na [stronie pomocy technicznej firmy Microsoft](https://aka.ms/kinectsupport) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Narzędzie oprogramowania układowego Azure urządzenia Kinect](azure-kinect-firmware-tool.md)
