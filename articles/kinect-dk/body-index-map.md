---
title: Mapa indeksu treści usługi Azure urządzenia Kinect
description: Zapoznaj się z badaniem mapy indeksu śledzenia treści w usłudze Azure urządzenia Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, transport, treść, śledzenie, indeks, segmentacja, Mapa
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277552"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Mapa indeksu śledzenia treści usługi Azure urządzenia Kinect

Mapa indeksu treści zawiera mapę segmentacji wystąpień dla każdej treści w przechwyceniu aparatu głębokości. Każdy piksel jest mapowany na odpowiedni piksel na głębokości lub obrazie podczerwieni. Wartość każdego piksela reprezentuje treść, do której należy piksel. Może to być albo tło (wartość `K4ABT_BODY_INDEX_MAP_BACKGROUND` ), albo indeks wykrytego elementu `k4abt_body_t` .

![Przykład mapy indeksu treści](./media/concepts/body-index-map.png)

>[!NOTE]
> Indeks treści różni się od identyfikatora treści. Można wysłać zapytanie do identyfikatora treści z danego indeksu treści przez wywołanie interfejsu API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Korzystanie z mapy indeksu treści

Mapa indeksu treści jest przechowywana jako `k4a_image_t` a i ma takie samo rozwiązanie, jak głębokość lub obraz IR. Każdy piksel jest wartością 8-bitową. Zapytanie może być wykonywane `k4abt_frame_t` przez wywołanie `k4abt_frame_get_body_index_map` . Deweloper jest odpowiedzialny za zwalnianie pamięci dla mapowania indeksu treści przez wywołanie `k4a_image_release()` .

## <a name="next-steps"></a>Następne kroki

[Tworzenie pierwszej aplikacji śledzącej ciało](build-first-body-app.md)
