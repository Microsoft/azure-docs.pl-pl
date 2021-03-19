---
title: Systemy współrzędnych usługi Azure urządzenia Kinect
description: Opis systemu współrzędnych platformy Azure urządzenia Kinect
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, aparat głębokości, TOF, zasady, wydajność, unieważnienie
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277446"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Systemy współrzędnych usługi Azure urządzenia Kinect

W tym artykule opisano konwencje stosowane w systemach współrzędnych 2D i 3W.  Istnieją oddzielne systemy współrzędnych skojarzone z urządzeniem czujnika i [funkcje kalibracji](use-calibration-functions.md) umożliwiające przekształcanie punktów między nimi. [Funkcje transformacji](use-image-transformation.md) przekształćą całe obrazy między systemami współrzędnych.  

## <a name="2d-coordinate-systems"></a>Systemy współrzędnych 2D

 Zarówno w przypadku kamer głębi, jak i kolorów są skojarzone niezależny układ współrzędnych 2D. Współrzędne [x, y] są reprezentowane w jednostkach pikseli, w których zakresy *x* mają wartość od 0 do Width-1 i *y* z zakresu od 0 do Height-1. Szerokość i wysokość zależą od wybranego trybu, w którym są obsługiwane kamery głębi i kolorów. Współrzędna pikseli `[0,0]` odpowiada pikselowi lewego górnego rogu obrazu. Współrzędne pikseli mogą być ułamkowe reprezentujące współrzędne przekroczenia pikseli.

Układ współrzędnych 2D to 0 — wyśrodkowany, czyli Współrzędna `[0.0, 0.0]` przedziału dolnego reprezentuje środek i `[0.5,0.5]` prawego górnego rogu piksela, jak pokazano poniżej.

   ![układ współrzędnych 2D](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>Systemy współrzędnych 3W

Każdy aparat, przyspieszeniomierz i żyroskop są skojarzone z niezależnym systemem przestrzeni współrzędnych 3W.

Punkty w systemach współrzędnych 3W są reprezentowane jako metryki [X, Y, Z] — koordynuj Triplets z jednostkami w milimetrach.

### <a name="depth-and-color-camera"></a>Kamera głębi i kolorowa

Źródło znajduje `[0,0,0]` się w punkcie ogniskowym aparatu. Układ współrzędnych jest ukierunkowany na tak, aby dodatnie punkty osi X wskazywały prawo, dodatnia oś Y w dół, a dodatnie punkty Z osi Z przodu.

Kamera głębokości jest pochylona o 6 stopni w dół aparatu kolorów, jak pokazano poniżej. 

Aparat głębokości zawiera dwa kierunki. Lampka użyta w trybach wąskiego widoku (NFOV) jest wyrównana do wielkości obudowy z kamerą, więc nie jest to pochylony. W przypadku trybów wielopolowych (WFOV) jest pochylony dodatkowy 1,3 stopni w stosunku do aparatu głębokości.

![konwencje współrzędnych 3W](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Żyroskop i przyspieszeniomierz

Pochodzenie żyroskop `[0,0,0]` jest identyczne z początkiem aparatu głębokości. Początek przyspieszeniomierza pokrywa się z jego fizyczną lokalizacją. Systemy współrzędnych i żyroskop są odpowiednie. Dodatnia oś X systemu współrzędnych odwołuje się do tyłu, dodatnie punkty osi Y w lewo i dodatnie punkty Z osi Z w dół, jak pokazano poniżej.

![System współrzędnych IMU](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o zestawie SDK czujnika usługi Azure urządzenia Kinect](about-sensor-sdk.md)