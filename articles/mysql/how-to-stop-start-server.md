---
title: Zatrzymaj/Start-Azure Portal-Azure Database for MySQL Server
description: W tym artykule opisano sposób zatrzymania/uruchamiania operacji w Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516876"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Zatrzymywanie/uruchamianie Azure Database for MySQL

> [!IMPORTANT]
>  **Zatrzymanie** serwera pozostanie w tym stanie w ciągu następnych 7 dni w rozciągnięciu. Jeśli nie **uruchomisz** go ręcznie w tym czasie, serwer zostanie automatycznie uruchomiony po upływie 7 dni. Jeśli serwer nie jest używany, można go **zatrzymać** ponownie.

Ten artykuł zawiera procedury krok po kroku umożliwiające przeprowadzenie zatrzymania i uruchomienia jednego serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for MySQL jeden serwer.

> [!NOTE]
> Zapoznaj się z ograniczeniem korzystania z funkcji [Zatrzymaj/Uruchom](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Jak zatrzymać/uruchomić Azure Database for MySQL przy użyciu Azure Portal

### <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer MySQL, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL Zatrzymaj serwer":::

    > [!NOTE]
    > Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla jednego serwera.

### <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz jeden serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL uruchomić serwer":::

    > [!NOTE]
    > Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla jednego serwera.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Jak zatrzymać/uruchomić Azure Database for MySQL przy użyciu interfejsu wiersza polecenia

### <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer MySQL, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla jednego serwera.

### <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz jeden serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla jednego serwera.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
