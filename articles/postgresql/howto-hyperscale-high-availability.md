---
title: Konfigurowanie funkcji Citus (High Availability) — Azure Database for PostgreSQL
description: Jak włączyć lub wyłączyć wysoką dostępność
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 0c7702c8832e22d889a5d785dad845430bfb7d17
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116883"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurowanie wysokiej dostępności (Citus)

Azure Database for PostgreSQL-Citus) zapewnia wysoką dostępność (HA), aby uniknąć przestoju bazy danych. W przypadku włączenia wysokiej dostępności każdy węzeł w grupie serwerów będzie miał stan wstrzymania. Jeśli oryginalny węzeł ulegnie złej kondycji, jego stan wstrzymania zostanie podwyższony do jego zastąpienia.

> [!IMPORTANT]
> Ze względu na to, że HA podwaja liczbę serwerów w grupie, zostanie również podwojony koszt.

Włączenie wysokiej dostępności jest możliwe podczas tworzenia grupy serwerów lub później na karcie **Konfiguracja** dla grupy serwerów w Azure Portal. Interfejs użytkownika wygląda podobnie w każdym przypadku. Przeciągnij suwak, aby zapewnić **wysoką dostępność** na wartość tak:

![suwak ha](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kliknij przycisk **Zapisz** , aby zastosować wybór. Włączenie wysokiej dostępności może zająć trochę czasu, ponieważ Grupa serwerów inicjuje rezerwę i przesyła strumieniowo do nich dane.

Na karcie **Przegląd** dla grupy serwerów zostaną wystawione wszystkie węzły i ich wstrzymania wraz z kolumną **o wysokiej dostępności** , która wskazuje, czy ha została pomyślnie włączona dla każdego węzła.

![kolumna ha w grupie serwerów — Omówienie](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wysokiej dostępności](concepts-hyperscale-high-availability.md).
