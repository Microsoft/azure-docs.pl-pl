---
title: Azure Portal Azure Database for MySQL magazynu
description: W tym artykule opisano sposób włączania magazynu z możliwością autowzrostu dla Azure Database for MySQL przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: d4dc5c2690be7b9abbda685e78ea562878626b5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902852"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Powiększ magazyn w Azure Database for MySQL przy użyciu Azure Portal
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MySQL do wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzieloną liczbę magazynów, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz funkcję autozwiększania magazynu, magazyn serwera zwiększy się, aby uwzględnić rosnące dane. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie jest mniejsza niż 1 GB lub 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu 

Wykonaj następujące kroki, aby ustawić funkcję autowzrostu magazynu serwera MySQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL.

2. Na stronie serwer MySQL w polu Nagłówek **ustawień** kliknij pozycję **warstwa cenowa** , aby otworzyć stronę warstwa cenowa.

3. W sekcji autoprzyrost wybierz pozycję **tak** , aby włączyć funkcję automatycznego zwiększania rozmiaru magazynu.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL-Settings_Pricing_tier — autoprzyrost":::

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie zostanie potwierdzone, że funkcja autozwiększania została pomyślnie włączona.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL-Settings_Pricing_tier — autoprzyrost":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
