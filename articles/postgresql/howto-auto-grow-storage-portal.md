---
title: Automatyczne powiększanie magazynu — Azure Portal — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania automatycznego powiększania magazynu przy użyciu Azure Portal w Azure Database for PostgreSQL — pojedynczy serwer
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366134"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatyczne powiększanie magazynu przy użyciu Azure Portal w Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule opisano sposób konfigurowania magazynu Azure Database for PostgreSQL serwera w celu wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzielony limit magazynu, zostanie oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne zwiększenie magazynu, magazyn serwera zwiększy się w celu uwzględnienia rosnących danych. W przypadku serwerów z aprowizowany magazynem o rozmiarze mniejszym niż 100 GB aprowizowany rozmiar magazynu jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca jest mniejsza niż 1 GB lub 10% aprowizowanych magazynów. W przypadku serwerów z ponad 100 GB aprowizowanych magazynów aprowizowany rozmiar magazynu zwiększa się o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB rozmiaru aprowizowanych magazynów. Obowiązują maksymalne limity magazynu określone [w tym miejscu.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania instrukcji w tym przewodniku potrzebne są:
- Serwer [Azure Database for PostgreSQL serwera](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Włączanie automatycznego wzrostu magazynu 

Wykonaj następujące kroki, aby ustawić automatyczne powiększanie magazynu serwera PostgreSQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for PostgreSQL serwer.

2. Na stronie serwera PostgreSQL w obszarze **Ustawienia** kliknij pozycję **Warstwa cenowa,** aby otworzyć stronę warstwy cenowej.

3. W sekcji **Auto-growth (Automatyczne wzrost)** wybierz pozycję **Yes (Tak),** aby włączyć automatyczne powiększanie magazynu.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL — Settings_Pricing_tier — Automatyczne wzrost":::

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że automatyczne powiększanie zostało pomyślnie włączone.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL — sukces automatycznego wzrostu":::

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące metryk.](howto-alert-on-metric.md)
