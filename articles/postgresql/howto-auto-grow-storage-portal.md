---
title: Powiększ magazyn Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak skonfigurować Autorozszerzanie magazynu przy użyciu Azure Portal na pojedynczym serwerze Azure Database for PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: dd6effff2fa38276729e2609e115ef72c7af4066
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228082"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Powiększ magazyn przy użyciu Azure Portal w ramach Azure Database for PostgreSQL-pojedynczego serwera
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for PostgreSQL do wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzieloną liczbę magazynów, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz funkcję autozwiększania magazynu, magazyn serwera zwiększy się, aby uwzględnić rosnące dane. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie jest mniejsza niż 1 GB lub 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](./concepts-pricing-tiers.md#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu 

Wykonaj następujące kroki, aby ustawić funkcję autowzrostu magazynu serwera PostgreSQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for PostgreSQL.

2. Na stronie serwer PostgreSQL w obszarze **Ustawienia** kliknij pozycję **warstwa cenowa** , aby otworzyć stronę warstwa cenowa.

3. W sekcji **autoprzyrost** wybierz pozycję **tak** , aby włączyć funkcję automatycznego zwiększania rozmiaru magazynu.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL-Settings_Pricing_tier — autoprzyrost":::

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie zostanie potwierdzone, że funkcja autozwiększania została pomyślnie włączona.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL — powodzenie automatycznego wzrostu":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).