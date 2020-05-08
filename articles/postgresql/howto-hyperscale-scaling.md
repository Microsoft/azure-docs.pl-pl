---
title: Skalowanie grupy serwerów — Citus — Azure Database for PostgreSQL
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584004"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie rdzeni wirtualnych istniejących węzłów.

## <a name="add-worker-nodes"></a>Dodaj węzły procesu roboczego

Aby dodać węzły, przejdź na kartę **Konfiguracja** w grupie serwerów moja skala (Citus).  Przeciągnięcie suwaka dla **liczby węzłów roboczych** powoduje zmianę wartości.

![Suwaki zasobów](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kliknij przycisk **Zapisz** , aby zmiana wartości zaczęła obowiązywać.

> [!NOTE]
> Po zwiększeniu i zapisaniu liczba węzłów procesu roboczego nie może być obniżona przy użyciu suwaka.

### <a name="rebalance-shards"></a>Ponowne równoważenie fragmentów

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Najpierw sprawdź, czy nowi pracownicy pomyślnie ukończyli Inicjowanie obsługi administracyjnej. Następnie należy uruchomić moduł równoważenia fragmentu, łącząc się z węzłem koordynatora klastra z PSQL i uruchamiając następujące polecenie:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` Funkcja ponownie równoważy wszystkie tabele w grupie wspólnej [lokalizacji](concepts-hyperscale-colocation.md) tabeli o nazwie w jej argumencie. W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zwiększ lub Zmniejsz rdzeni wirtualnych na węzłach

> [!NOTE]
> Ta funkcja jest obecnie w wersji zapoznawczej. Aby zażądać zmiany w rdzeni wirtualnych dla węzłów w grupie serwerów, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Oprócz dodawania nowych węzłów można zwiększyć możliwości istniejących węzłów. Dostosowanie pojemności obliczeniowej w górę i w dół może być przydatne w przypadku eksperymentów dotyczących wydajności, a także krótko-lub długoterminowych zmian w zakresie ruchu.

Aby zmienić rdzeni wirtualnych dla wszystkich węzłów procesu roboczego, Dostosuj suwak **rdzeni wirtualnych** w obszarze **Konfiguracja (na węzeł procesu roboczego)**. Rdzeni wirtualnych węzła koordynatora można dostosowywać niezależnie. Kliknij link **Zmień konfigurację** w **węźle koordynator**. Zostanie wyświetlone okno dialogowe z suwakami dla pojemności rdzeni wirtualnych i magazynu koordynatora. Zmień suwaki zgodnie z potrzebami, a następnie wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
