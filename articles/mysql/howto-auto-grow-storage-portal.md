---
title: Automatyczne powiększanie magazynu — Azure Portal — Azure Database for MySQL
description: W tym artykule opisano, jak włączyć automatyczne powiększanie magazynu dla Azure Database for MySQL przy użyciu Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365029"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatyczne powiększanie magazynu w Azure Database for MySQL użyciu Azure Portal
W tym artykule opisano sposób konfigurowania magazynu Azure Database for MySQL serwera w celu wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzielony limit magazynu, zostanie oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne zwiększenie magazynu, magazyn serwera zwiększy się w celu uwzględnienia rosnących danych. W przypadku serwerów z aprowizowany magazynem o rozmiarze mniejszym niż 100 GB aprowizowany rozmiar magazynu jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca jest mniejsza niż 1 GB lub 10% aprowizowanych magazynów. W przypadku serwerów z ponad 100 GB aprowizowanych magazynów aprowizowany rozmiar magazynu zwiększa się o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB rozmiaru aprowizowanych magazynów. Obowiązują maksymalne limity magazynu określone [w tym miejscu.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania instrukcji w tym przewodniku potrzebne są:
- Serwer [Azure Database for MySQL serwera](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włączanie automatycznego wzrostu magazynu 

Wykonaj następujące kroki, aby ustawić automatyczne powiększanie magazynu serwera MySQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for MySQL serwera.

2. Na stronie serwera MySQL w obszarze **Nagłówek** Ustawienia kliknij pozycję **Warstwa** cenowa, aby otworzyć stronę Warstwa cenowa.

3. W sekcji Auto-growth (Automatyczne wzrost) wybierz pozycję **Yes (Tak),** aby włączyć automatyczne powiększanie magazynu.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL — Settings_Pricing_tier — Automatyczne wzrost":::

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że automatyczne powiększanie zostało pomyślnie włączone.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL — sukces automatycznego wzrostu":::

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące metryk.](howto-alert-on-metric.md)
