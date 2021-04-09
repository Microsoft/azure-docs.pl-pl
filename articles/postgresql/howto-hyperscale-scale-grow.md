---
title: Skalowanie grupy serwerów — Citus — Azure Database for PostgreSQL
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026424"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie rdzeni wirtualnych istniejących węzłów. Dodawanie węzłów nie powoduje przestoju, a nawet przeniesienie fragmentów do nowych węzłów (o nazwie [fragmentu Rebalancing](howto-hyperscale-scale-rebalance.md)) odbywa się bez przerywania zapytań.

## <a name="add-worker-nodes"></a>Dodaj węzły procesu roboczego

Aby dodać węzły, przejdź do karty **obliczenia + magazyn** w grupie serwerów Citus.  Przeciągnięcie suwaka dla **liczby węzłów roboczych** powoduje zmianę wartości.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Suwaki zasobów":::

Kliknij przycisk **Zapisz** , aby zmiana wartości zaczęła obowiązywać.

> [!NOTE]
> Po zwiększeniu i zapisaniu liczby węzłów roboczych nie można jej zmniejszyć przy użyciu suwaka.

> [!NOTE]
> Aby skorzystać z nowo dodanych węzłów, należy ponownie [zrównoważyć rozproszoną tabelę fragmentów](howto-hyperscale-scale-rebalance.md), co oznacza przeniesienie niektórych [fragmentów](concepts-hyperscale-distributed-data.md#shards) z istniejących węzłów do nowych.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zwiększanie lub zmniejszanie liczby rdzeni wirtualnych w węzłach

Oprócz dodawania nowych węzłów można zwiększać możliwości istniejących węzłów. Dostosowanie pojemności obliczeniowej w górę i w dół może być przydatne w przypadku eksperymentów dotyczących wydajności, a także krótko-lub długoterminowych zmian w zakresie ruchu.

Aby zmienić rdzeni wirtualnych dla wszystkich węzłów procesu roboczego, Dostosuj suwak **rdzeni wirtualnych** w obszarze **Konfiguracja (na węzeł procesu roboczego)**. Rdzeni wirtualnych węzła koordynatora można dostosowywać niezależnie. Dostosuj suwak **rdzeni wirtualnych** w obszarze  **Konfiguracja (węzeł koordynatora)**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
- Ponowne [równoważenie rozproszonej tabeli fragmentów](howto-hyperscale-scale-rebalance.md) tak, aby wszystkie węzły procesów roboczych mogły uczestniczyć w zapytaniach równoległych
