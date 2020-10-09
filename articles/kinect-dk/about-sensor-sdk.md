---
title: Informacje o zestawie SDK czujnika usługi Azure urządzenia Kinect
description: Omówienie zestawu Azure urządzenia Kinect sensor Software Development Kit (SDK), jego funkcji i narzędzi.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, urządzenia Kinect, RGB, IR, nagrywanie, czujnik, zestaw SDK, dostęp, Głębokość, wideo, kamera, IMU, ruch, czujnik, dźwięk, mikrofon, Matroska, zestaw SDK czujnika, pobieranie
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277531"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Informacje o zestawie SDK czujnika usługi Azure urządzenia Kinect

Ten artykuł zawiera omówienie zestawu Azure urządzenia Kinect sensor Software Development Kit (SDK), jego funkcji i narzędzi.

## <a name="features"></a>Funkcje

Zestaw SDK czujnika usługi Azure urządzenia Kinect zapewnia Międzyplatformowy dostęp do usługi Azure urządzenia Kinect i strumieni czujników sprzętu, w tym:

- Głębokość dostępu do aparatu i Sterowanie trybem (tryb pasywnego IR oraz szeroki i wąski tryb głębokości widoku) 
- Dostęp i kontrola w aparacie RGB (na przykład ekspozycja i białe saldo) 
- Dostęp do czujnika ruchu (żyroskop i przyspieszeniomierz) 
- Zsynchronizowana głębokość — przesyłanie strumieniowe aparatu RGB z konfigurowalnymi opóźnieniami między kamerami 
- Kontrola synchronizacji urządzeń zewnętrznych z konfigurowalnym przesunięciem opóźnienia między urządzeniami 
- Ramka aparatu — dostęp do metadanych dla rozdzielczości obrazu, sygnatury czasowej itp. 
- Dostęp do danych kalibracji urządzenia 

## <a name="tools"></a>Narzędzia

- [Usługa Azure urządzenia Kinect Viewer](azure-kinect-viewer.md) do monitorowania strumieni danych urządzenia i konfigurowania różnych trybów.
- Interfejs API [usługi Azure urządzenia Kinect Recorder](azure-kinect-recorder.md) i czytnika odtwarzania, który używa [formatu kontenera Matroska](record-file-format.md).
- [Narzędzie do aktualizacji oprogramowania układowego](azure-kinect-firmware-tool.md)w usłudze Azure urządzenia Kinect DK.

## <a name="sensor-sdk"></a>Zestaw SDK czujnika

- [Pobierz zestaw SDK czujnika](sensor-sdk-download.md).
- Zestaw SDK czujnika jest dostępny w programie [Open Source w serwisie GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Aby uzyskać więcej informacji o użyciu, zobacz [Dokumentacja interfejsu API zestawu czujników SDK](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Następne kroki

Teraz już wiesz o zestawie SDK usługi Azure urządzenia Kinect, można również:
>[!div class="nextstepaction"]
>[Pobierz kod zestawu SDK czujnika](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Znajdowanie i otwieranie urządzenia](find-then-open-device.md)
