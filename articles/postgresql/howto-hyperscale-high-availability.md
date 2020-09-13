---
title: Konfigurowanie funkcji Citus (High Availability) — Azure Database for PostgreSQL
description: Jak włączyć lub wyłączyć wysoką dostępność
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f08fb01cf26ddf73d31b575242b27d7d8b4017d9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033283"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurowanie wysokiej dostępności (Citus)

Azure Database for PostgreSQL-Citus) zapewnia wysoką dostępność (HA), aby uniknąć przestoju bazy danych. W przypadku włączenia wysokiej dostępności każdy węzeł w grupie serwerów będzie miał stan wstrzymania. Jeśli oryginalny węzeł ulegnie złej kondycji, jego stan wstrzymania zostanie podwyższony do jego zastąpienia.

> [!IMPORTANT]
> Ze względu na to, że HA podwaja liczbę serwerów w grupie, zostanie również podwojony koszt.

Włączenie wysokiej dostępności jest możliwe podczas tworzenia grupy serwerów, a następnie na karcie **obliczenia + magazyn** dla grupy serwerów w Azure Portal. Interfejs użytkownika wygląda podobnie w każdym przypadku. Przeciągnij suwak, aby uzyskać **wysoką dostępność** od nie do tak:

![suwak ha](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kliknij przycisk **Zapisz** , aby zastosować wybór. Włączenie wysokiej dostępności może zająć trochę czasu, ponieważ Grupa serwerów inicjuje rezerwę i przesyła strumieniowo do nich dane.

Na karcie **Przegląd** dla grupy serwerów zostaną wystawione wszystkie węzły i ich wstrzymania wraz z kolumną **o wysokiej dostępności** , która wskazuje, czy ha została pomyślnie włączona dla każdego węzła.

![kolumna ha w grupie serwerów — Omówienie](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wysokiej dostępności](concepts-hyperscale-high-availability.md).
