---
title: Dostęp do danych usługi Azure urządzenia Kinect DK w ramce treści
description: Dowiedz się więcej o danych w ramce treści i funkcjach umożliwiających dostęp do tych danych w usłudze Azure urządzenia Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: treść, ramka, Azure, urządzenia Kinect, treść, śledzenie, porady
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277564"
---
# <a name="access-data-in-body-frame"></a>Uzyskiwanie dostępu do danych w klatce ciała

W tym artykule opisano dane zawarte w ramce treści i funkcje umożliwiające dostęp do tych danych.

Omówione są następujące funkcje:

- [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>Najważniejsze składniki ramki treści

Każda ramka treści zawiera kolekcję struktur treści, mapę indeksu treści 2D i przechwytywanie danych wejściowych, które wygenerowały ten wynik.

![Składniki ramki treści](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>Dostęp do kolekcji struktur treści

W jednym przechwyceniu można wykryć wiele jednostek. Możesz badać liczbę treści, wywołując funkcję [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) .

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

Za pomocą funkcji [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) i [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) można wykonać iterację poszczególnych indeksów treści, aby znaleźć identyfikator treści i informacje o łącznym stanowisku/orientacji.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>Dostęp do mapy indeksu treści

Aby uzyskać dostęp do mapy indeksu treści, należy użyć funkcji [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) . Szczegółowe wyjaśnienie mapy indeksu treści można znaleźć na [mapie indeksu treści](body-index-map.md) . Pamiętaj, aby zwolnić mapę indeksu treści, gdy nie jest już potrzebne.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>Dostęp do przechwytywania danych wejściowych

Moduł śledzący treść jest asynchronicznym interfejsem API. Oryginalne przechwytywanie mogło już zostać wydane przez czas, gdy wynik jest zdjęte. Użyj funkcji [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) , aby wykonać zapytanie dotyczące przechwytywania wejściowego używanego do generowania wyniku śledzenia tego treści. Liczba odwołań dla k4a_capture_t jest zwiększana za każdym razem, gdy ta funkcja jest wywoływana. Użyj funkcji [k4a_capture_release ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) , gdy Przechwytywanie nie jest już potrzebne.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Azure Kinect Body Tracking SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
