---
title: Konfigurowanie funkcji Citus (High Availability) — Azure Database for PostgreSQL
description: Jak włączyć lub wyłączyć wysoką dostępność
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907391"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurowanie wysokiej dostępności (Citus)

Azure Database for PostgreSQL-Citus) zapewnia wysoką dostępność (HA), aby uniknąć przestoju bazy danych. W przypadku włączenia wysokiej dostępności każdy węzeł w grupie serwerów będzie miał stan wstrzymania. Jeśli oryginalny węzeł ulegnie złej kondycji, jego stan wstrzymania zostanie podwyższony do jego zastąpienia.

> [!IMPORTANT]
> Ze względu na to, że HA podwaja liczbę serwerów w grupie, zostanie również podwojony koszt.

Włączenie wysokiej dostępności jest możliwe podczas tworzenia grupy serwerów, a następnie na karcie **obliczenia + magazyn** dla grupy serwerów w Azure Portal. Interfejs użytkownika wygląda podobnie w każdym przypadku. Przeciągnij suwak, aby uzyskać **wysoką dostępność** od nie do tak:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="suwak ha":::

Kliknij przycisk **Zapisz** , aby zastosować wybór. Włączenie wysokiej dostępności może zająć trochę czasu, ponieważ Grupa serwerów inicjuje rezerwę i przesyła strumieniowo do nich dane.

Na karcie **Przegląd** dla grupy serwerów zostaną wystawione wszystkie węzły i ich wstrzymania wraz z kolumną **o wysokiej dostępności** , która wskazuje, czy ha została pomyślnie włączona dla każdego węzła.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="suwak ha":::

### <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wysokiej dostępności](concepts-hyperscale-high-availability.md).
