---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można ponownie uruchomić pojedynczy serwer Azure Database for PostgreSQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770088"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Uruchom ponownie Azure Database for PostgreSQL — pojedynczy serwer przy użyciu Azure Portal
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for PostgreSQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.
 
Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania PostgreSQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Wykonaj ponowne uruchomienie serwera

Wykonaj następujące kroki, aby ponownie uruchomić serwer PostgreSQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for PostgreSQL.

2. Na pasku narzędzi na stronie **Przegląd** serwera kliknij pozycję **Uruchom ponownie**.

   ![Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania](./media/howto-restart-server-portal/2-server.png)

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   ![Azure Database for PostgreSQL — Potwierdź ponowne uruchomienie](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   ![Azure Database for PostgreSQL — stan ponownego uruchomienia](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   ![Azure Database for PostgreSQL — pomyślne ponowne uruchomienie](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat sposobu ustawiania parametrów w Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)