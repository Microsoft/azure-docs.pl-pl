---
title: Narzędzie do zarządzania oprogramowaniem układowym urządzenia Azure Kinect
description: Dowiedz się, jak wysyłać zapytania i aktualizować oprogramowanie układowe urządzenia przy użyciu narzędzia oprogramowania układowego Azure urządzenia Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, oprogramowanie układowe, aktualizacja
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277523"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Narzędzie oprogramowania układowego w usłudze Azure urządzenia Kinect DK

Za pomocą narzędzia układowego platformy Azure urządzenia Kinect można wysyłać zapytania i aktualizować oprogramowanie układowe urządzenia w usłudze Azure urządzenia Kinect DK.

## <a name="list-connected-devices"></a>Wyświetl listę połączonych urządzeń

Listę połączonych urządzeń można uzyskać przy użyciu opcji-l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Sprawdź wersję oprogramowania układowego urządzenia

Bieżące wersje oprogramowania układowego pierwszego dołączonego urządzenia można sprawdzić za pomocą-q opcji, na przykład `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Jeśli istnieje więcej niż jedno urządzenie, możesz określić, które urządzenie ma być zapytania, dodając pełny numer seryjny do polecenia, na przykład:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Aktualizowanie oprogramowania układowego urządzenia

Najbardziej typowym zastosowaniem tego narzędzia jest aktualizowanie oprogramowania układowego urządzenia. Wykonaj aktualizację, wywołując narzędzie przy użyciu `-u` opcji. Aktualizacja oprogramowania układowego może potrwać kilka minut, w zależności od tego, które pliki oprogramowania układowego muszą zostać zaktualizowane.

Aby uzyskać instrukcje krok po kroku dotyczące aktualizacji oprogramowania układowego, zobacz [Aktualizacja oprogramowania układowego platformy Azure urządzenia Kinect](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Jeśli istnieje więcej niż jedno urządzenie, możesz określić, które urządzenie ma być zapytania, dodając pełny numer seryjny do polecenia.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Resetowanie urządzenia

Dołączonego do usługi Azure urządzenia Kinect DK można zresetować przy użyciu opcji-r, jeśli urządzenie musi zostać podłączone do znanego stanu.

Jeśli istnieje więcej niż jedno urządzenie, możesz określić, które urządzenie ma być zapytania, dodając pełny numer seryjny do polecenia.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Sprawdzanie oprogramowania układowego

Sprawdzenie oprogramowania układowego umożliwia uzyskanie informacji o wersji z pliku bin oprogramowania układowego przed aktualizacją rzeczywistego urządzenia.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Opcje narzędzia aktualizacji oprogramowania układowego

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Instrukcje krok po kroku dotyczące aktualizowania oprogramowania układowego urządzenia](update-device-firmware.md)
