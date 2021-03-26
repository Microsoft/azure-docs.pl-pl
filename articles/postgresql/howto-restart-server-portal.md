---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można ponownie uruchomić pojedynczy serwer Azure Database for PostgreSQL przy użyciu Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: 1a1afabd606df70ec60cf4fa7c8530ff95a0564f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604859"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Uruchom ponownie Azure Database for PostgreSQL — pojedynczy serwer przy użyciu Azure Portal
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for PostgreSQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.
 
> [!NOTE] 
> Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania PostgreSQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem. Możesz również zwiększyć częstotliwość punktów kontrolnych. Można również dostrajać wartości parametrów powiązane z punktem kontrolnym, w tym `max_wal_size` . Zalecane jest również uruchomienie `CHECKPOINT` polecenia przed ponownym uruchomieniem serwera.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Wykonaj ponowne uruchomienie serwera

Wykonaj następujące kroki, aby ponownie uruchomić serwer PostgreSQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for PostgreSQL.

2. Na pasku narzędzi na stronie **Przegląd** serwera kliknij pozycję **Uruchom ponownie**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania":::

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL — Potwierdź ponowne uruchomienie":::

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL — stan ponownego uruchomienia":::

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL — pomyślne ponowne uruchomienie":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat sposobu ustawiania parametrów w Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
