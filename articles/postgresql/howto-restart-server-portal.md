---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można ponownie uruchomić pojedynczy serwer Azure Database for PostgreSQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907370"
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

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania":::

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania":::

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania":::

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL — przegląd — przycisk ponownego uruchamiania":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat sposobu ustawiania parametrów w Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)