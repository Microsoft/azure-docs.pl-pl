---
title: Uruchom ponownie serwer-Azure Portal-Azure Database for MySQL-elastyczny serwer
description: W tym artykule opisano, jak można ponownie uruchomić Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542664"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Uruchom ponownie Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal
W tym temacie opisano, jak można ponownie uruchomić serwer elastyczny Azure Database for MySQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MySQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Azure Database for MySQL elastyczny serwer](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Wykonaj ponowne uruchomienie serwera

Wykonaj następujące kroki, aby ponownie uruchomić serwer MySQL:

1. W Azure Portal wybierz swój Azure Database for MySQL elastyczny serwer.

2. Na pasku narzędzi na stronie **Przegląd** serwera kliknij pozycję **Uruchom ponownie**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL — przegląd — przycisk ponownego uruchamiania":::

3. Kliknij przycisk **tak** , aby potwierdzić ponowne uruchomienie serwera.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL — Potwierdź ponowne uruchomienie":::

4. Zwróć uwagę, że stan serwera zmieni się na "ponowne uruchomienie".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL — stan ponownego uruchomienia":::

5. Potwierdzenie ponownego uruchomienia serwera zakończyło się pomyślnie.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL — pomyślne ponowne uruchomienie":::

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Tworzenie Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal](quickstart-create-server-portal.md)
