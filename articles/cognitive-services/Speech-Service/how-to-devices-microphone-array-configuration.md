---
title: Jak skonfigurować tablicę mikrofonu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować tablicę mikrofonów, aby umożliwić jej korzystanie z zestawu Speech Devices SDK.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025065"
---
# <a name="how-to-configure-a-microphone-array"></a>Jak skonfigurować macierz mikrofonów

W tym artykule dowiesz się, jak skonfigurować [tablicę mikrofonu](./speech-devices-sdk-microphone.md). Obejmuje to ustawienie kąta pracy oraz sposób wybierania mikrofonu używanego dla zestawu Speech Devices SDK.

Zestaw SDK urządzeń mowy działa najlepiej z tablicą mikrofonu, która została zaprojektowana zgodnie z [naszymi wskazówkami](./speech-devices-sdk-microphone.md). Konfiguracja macierzy mikrofonu może być świadczona przez system operacyjny lub dostarczana za pomocą jednej z następujących metod.

Zestaw SDK usługi Speech Devices początkowo obsługiwał tablice mikrofonu, wybierając z ustalonego zestawu konfiguracji.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

W systemie Windows Konfiguracja macierzy mikrofonów jest dostarczana przez sterownik audio.

W programie v 1.11.0 zestaw SDK urządzeń mowy obsługuje również konfigurację z [pliku JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
W systemie Windows informacje o geometrii macierzy mikrofonu są automatycznie uzyskiwane ze sterownika audio. Dlatego właściwości `DeviceGeometry` ,  `SelectedGeometry` i `MicArrayGeometryConfigFile` są opcjonalne. Używamy [pliku JSON](https://aka.ms/sdsdk-micarray-json) , który jest używany `MicArrayGeometryConfigFile` do pobierania tylko zakresu Beamforming.

Jeśli jest określona tablica mikrofonu przy użyciu `AudioConfig::FromMicrophoneInput` , użyjemy określonego mikrofonu. Jeśli mikrofon nie został określony lub `AudioConfig::FromDefaultMicrophoneInput` jest wywoływany, użyjemy domyślnego mikrofonu, który jest określony w ustawieniach dźwięku w systemie Windows.
Stos audio firmy Microsoft w zestawie SDK urządzeń mowy obsługuje tylko próbkowanie dla częstotliwości próbkowania, które są całkowitymi wielokrotnośćmi 16 KHz.

## <a name="linux"></a>Linux
W systemie Linux należy podać informacje o geometrii mikrofonu. Korzystanie z `DeviceGeometry` i `SelectedGeometry` pozostaje obsługiwane. Można go również dostarczyć za pośrednictwem pliku JSON przy użyciu `MicArrayGeometryConfigFile` właściwości. Podobnie jak w przypadku systemu Windows, zakres Beamforming może być dostarczony przez plik JSON.

Jeśli jest określona tablica mikrofonu przy użyciu `AudioConfig::FromMicrophoneInput` , użyjemy określonego mikrofonu. Jeśli mikrofon nie został określony lub `AudioConfig::FromDefaultMicrophoneInput` jest wywoływany, zostanie zarejestrowany z urządzenia ALSA o nazwie *default*. Domyślnie *Domyślnie* jest zawsze wskazywana karta 0 Device 0, ale użytkownicy mogą ją zmienić w `asound.conf` pliku. 

Stos audio firmy Microsoft w zestawie SDK usługi Speech Devices obsługuje tylko próbkowanie dla częstotliwości próbkowania, które są całkowitymi wielokrotnośćmi 16 KHz. Dodatkowo obsługiwane są następujące formaty: 32-bitowe IEEE little endian float, 32-bit little endiand int, 24-bitowe little endian ze znakiem int, 16-bitowym, little endian ze znakiem int oraz 8-bitowe podpisane int.

## <a name="android"></a>Android
Obecnie tylko [roobo V1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) jest obsługiwany przez zestaw SDK urządzeń mowy. Zachowanie jest takie samo jak w poprzednich wersjach, z wyjątkiem `MicArrayGeometryConfigFile` Właściwości Now można użyć do określenia pliku JSON zawierającego zakres Beamforming.

## <a name="microphone-array-configuration-json"></a>Plik JSON konfiguracji macierzy mikrofonu

Plik JSON dla konfiguracji geometrii macierzy mikrofonu będzie zgodny ze [schematem JSON](https://aka.ms/sdsdk-micarray-json). Poniżej przedstawiono kilka przykładów, które są zgodne ze schematem.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Lub


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Lub

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)