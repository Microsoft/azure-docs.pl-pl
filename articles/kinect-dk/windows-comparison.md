---
title: Porównanie systemu Windows w urządzenia Kinect DK
description: Różnice w zakresie sprzętu i oprogramowania między usługą Azure urządzenia Kinect DK i urządzenia Kinect dla systemu Windows w wersji 2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Urządzenia Kinect, Windows, v2, Azure urządzenia Kinect, porównanie, zestaw SDK, różnice, sprzęt, oprogramowanie
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078302"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Porównanie usług Azure urządzenia Kinect i urządzenia Kinect systemu Windows v2

Zestawy SDK sprzętu i oprogramowania platformy Azure urządzenia Kinect w wersji 2 mają różnice od urządzenia Kinect dla systemu Windows v2. Wszystkie istniejące aplikacje urządzenia Kinect dla systemu Windows w wersji 2 nie będą działały bezpośrednio z platformą Azure urządzenia Kinect DK i będą wymagały przenoszenia nowego zestawu SDK.  

## <a name="hardware"></a>Sprzęt

W poniższej tabeli wymieniono ogólne różnice między zestawem Azure urządzenia Kinect Development Kit a urządzenia Kinect dla systemu Windows v2.

| Cecha | Typ | Azure Kinect DK | Urządzenia Kinect dla systemu Windows w wersji 2 |
| ------- | ---- | --------------- | --------------------- |
| **Dźwięk** | Szczegóły  | 7 — macierz cykliczna z użyciem mikrofonu | 4-MIC liniowy — macierz fazowy |
| **Czujnik ruchu** | Szczegóły | 3-osiowy przyspieszeniomierz 3-osiowy Gyro | przyspieszeniomierz 3-osiowy |
| **Aparat RGB**    | Szczegóły | 3840 x 2160 piks. @30 fps | 1920 x 1080 piks. @30 fps |
| **Aparat głębokości**  | Metoda   | Czas trwania lotu | Czas trwania lotu |
|                   | Rozwiązanie | 640 x 576 piks. @30 fps | 512 x 424 piks. 30 fps |
|                   |            | 512 x 512 piks. @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **Połączenia** | Dane | USB 3.1 Gen 1 i Type USB-C  | USB 3,1 Gen 1|
|  | Zasilanie | Zewnętrzna PSU lub USB-C | Zewnętrzna PSU |
|  | Synchronizacja | Tryb RGB & wewnętrzne, zewnętrzne urządzenie do urządzenia| Tylko & RGB głębokości |
| **Układ** | Wymiary | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Masowa | 440 g | 970 g |
| | Pokazują | Jeden 1/4-20 UNC. Cztery wewnętrzne punkty złączenia | Jeden 1/4-20 UNC |

Dodatkowe szczegóły znajdziesz w dokumencie dotyczącym [sprzętu platformy Azure urządzenia Kinect DK](hardware-specification.md) .

## <a name="sensor-access"></a>Dostęp do czujnika

W poniższej tabeli przedstawiono porównanie możliwości dostępu czujnika urządzenia niskiego poziomu.

| **Funkcjonalność**| **Azure Kinect** | **Urządzenia Kinect dla systemu Windows** | **Uwagi** |
|---------|---------|------------|---------|
| **Ścisł** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **Kolor** | ✔️ | ✔️ | Format koloru obsługuje różnice, usługa Azure urządzenia Kinect DK obsługuje następujące kontrolki aparatu: ekspozycja, biały balans, jasność, kontrast, nasycenie, ostrość i kontrola wzmocnienia |
| **Dźwięk** | ✔️ | ✔️ | Dostęp do usługi Azure urządzenia Kinect DK MICS odbywa się za pośrednictwem zestawu Speech SDK lub natywnego interfejsu API systemu Windows |
| **Moduł IMU** | ✔️ |  | Usługa Azure urządzenia Kinect DK ma pełną 6-osiową IMU i urządzenia Kinect dla systemu Windows, która zawiera tylko 1 oś |
| **Dane kalibracji** | ✔️ | ✔️ | Kalibracja zgodna z modelem aparatu fotograficznego OpenCV |
| **Głębokość — wewnętrzna synchronizacja RGB** | ✔️ | ✔️ |  |
| **Synchronizacja zewnętrzna**| ✔️|  | Usługa Azure urządzenia Kinect DK umożliwia programowalne opóźnienie synchronizacji zewnętrznej |
| **Udostępnianie dostępu wielu klientom** | | ✔️ | Zestaw SDK czujnika usługi Azure urządzenia Kinect korzysta z WinUSB/libUSB w celu uzyskania dostępu do urządzenia i nie ma zaimplementowanej usługi, aby umożliwić udostępnianie dostępu urządzeń z wieloma procesami |
| **Narzędzie do nagrywania/odtwarzania strumieniowego** | ✔️ | ✔️ | Usługa Azure urządzenia Kinect DK korzysta z implementacji opartej na kontenerach Open Source Matroska |

## <a name="features"></a>Funkcje

Zestaw funkcji zestawu Azure urządzenia Kinect SDK różni się od urządzenia Kinect dla systemu Windows v2, jak opisano poniżej:

| **Funkcja urządzenia Kinect V2** | **Urządzenia Kinect V2 — typ danych** | **Azure urządzenia Kinect SDK/usługa** |
|--------|--------|------|
| Dostęp do danych z czujnika |DepthFrame| [Zestaw SDK czujnika — pobieranie obrazów](retrieve-images.md) 
| |InfraredFrame | [Zestaw SDK czujnika — pobieranie obrazów](retrieve-images.md) 
| | ColorFrame | [Zestaw SDK czujnika — pobieranie obrazów](retrieve-images.md) | 
| | AudioBeamFrame |Nie jest obecnie obsługiwana. 
| Śledzenie treści | BodyFrame | Zestaw SDK śledzenia treści |
| | BodyIndexFrame | Zestaw SDK śledzenia treści  |
| Mapowanie współrzędnych|CoordinateMapper| [Zestaw SDK czujnika — przekształcenia obrazów](use-image-transformation.md) |
|Śledzenie kroju | FaceFrame | [Cognitive Services: kroju](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Rozpoznawanie mowy    |    Nie dotyczy                      |    [Cognitive Services: Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Następne kroki

[Urządzenia Kinect dla deweloperów systemu Windows](https://developer.microsoft.com/windows/kinect)
