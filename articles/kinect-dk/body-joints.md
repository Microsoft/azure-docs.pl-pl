---
title: Złącza do śledzenia treści w usłudze Azure urządzenia Kinect
description: Zrozumienie ramki ciała, połączeń, wspólnych współrzędnych i wspólnych hierarchii w usłudze Azure urządzenia Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, transport, treść, śledzenie, wspólne, hierarchia, kość, połączenie
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277536"
---
# <a name="azure-kinect-body-tracking-joints"></a>Złącza do śledzenia treści w usłudze Azure urządzenia Kinect

Śledzenie treści w usłudze Azure urządzenia Kinect może jednocześnie śledzić wiele treści ludzkich. Każda treść zawiera identyfikator dla nieczasowej korelacji między ramkami a szkieletem Kinematic. Liczbę jednostek wykrytych w każdej ramce można uzyskać przy użyciu `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Staw

Wspólne położenie i orientacja to oszacowania względem globalnej ramki czujnika głębokości. Pozycja jest określona w milimetrach. Orientacja jest wyrażana jako znormalizowana quaternion.

## <a name="joint-coordinates"></a>Wspólne współrzędne

Pozycja i orientacja każdego wspólnego tworzą własny układ współrzędnych. Wszystkie wspólne systemy współrzędnych są bezwzględnymi systemami współrzędnych względem systemu współrzędnych 3W aparatu głębokości.

> [!NOTE]
> Wspólne współrzędne są w orientacji osi. Orientacja osi jest szeroko stosowana w przypadku komercyjnych awatarów, aparatów gier i oprogramowania renderującego. Użycie orientacji osi upraszcza ruchy lustrzane, np. podnoszenie obu ramion o 20 stopni.

![Wspólne współrzędne](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Wspólna hierarchia

Szkielet obejmuje 32 połączeń ze wspólną hierarchią przepływów z centrum treści do krańców. Każde połączenie (kość) łączy element nadrzędny ze wspólnym elementem podrzędnym. Rysunek ilustruje wspólne lokalizacje i połączenia względem treści ludzkiej.

![Wspólna hierarchia](./media/concepts/joint-hierarchy.png)

W poniższej tabeli wymieniono standardowe połączenia wspólne.

|Indeks |Nazwa wspólna     | Wspólny element nadrzędny   |
|------|---------------|----------------|
| 0    |MIEDNIC         | -              |
| 1    |SPINE_NAVAL    | MIEDNIC         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |SZYJKI           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | MIEDNIC         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | MIEDNIC         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | SZYJKI           |
| 27   |NOS           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Następne kroki

[Śledzenie ciała — mapa indeksu](body-index-map.md)
