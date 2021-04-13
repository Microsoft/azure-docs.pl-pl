---
title: Automatyczne powiększanie magazynu — Azure Portal — Azure Database for MariaDB
description: W tym artykule opisano, jak włączyć automatyczne powiększanie magazynu dla Azure Database for MariaDB użyciu Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366117"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatyczne powiększanie magazynu w Azure Database for MariaDB użyciu Azure Portal
W tym artykule opisano sposób konfigurowania magazynu Azure Database for MariaDB serwera w celu wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzielony limit magazynu, zostanie oznaczony jako tylko do odczytu. Jeśli jednak włączysz automatyczne zwiększenie magazynu, magazyn serwera zwiększy się w celu uwzględnienia rosnących danych. W przypadku serwerów z aprowizowany magazynem o rozmiarze mniejszym niż 100 GB aprowizowany rozmiar magazynu jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca jest mniejsza niż 1 GB lub 10% aprowizowanych magazynów. W przypadku serwerów z ponad 100 GB aprowizowanych magazynów aprowizowany rozmiar magazynu zwiększa się o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB rozmiaru aprowizowanych magazynów. Obowiązują maksymalne limity magazynu określone [w tym miejscu.](concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania instrukcji w tym przewodniku potrzebne są:
- Serwer [Azure Database for MariaDB serwera](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włączanie automatycznego wzrostu magazynu 

Wykonaj następujące kroki, aby ustawić automatyczne powiększanie magazynu serwera MariaDB:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for MariaDB serwer.

2. Na stronie serwera MariaDB w obszarze **Nagłówka** Ustawienia kliknij pozycję **Warstwa cenowa,** aby otworzyć stronę warstwy cenowej.

3. W sekcji Automatyczne powiększanie wybierz pozycję **Tak,** aby włączyć automatyczne powiększanie magazynu.

    ![Azure Database for MariaDB — Settings_Pricing_tier — Automatyczne wzrost](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK**, aby zapisać zmiany.

5. Powiadomienie potwierdzi, że automatyczne powiększanie zostało pomyślnie włączone.

    ![Azure Database for MariaDB — sukces automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące metryk.](howto-alert-metric.md)
