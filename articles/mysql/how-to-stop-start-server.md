---
title: Zatrzymaj/Start-Azure Portal-Azure Database for MySQL Server
description: W tym artykule opisano sposób zatrzymania/uruchamiania operacji w Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: f09b6d48e8a98b0995c882769d6c978996324dad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346115"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Zatrzymywanie/uruchamianie Azure Database for MySQL

> [!IMPORTANT]
> Funkcje Stop/Start dla Azure Database for MySQL są obecnie dostępne w publicznej wersji zapoznawczej.

Ten artykuł zawiera procedury krok po kroku dotyczące przetrzymywania i uruchamiania elastycznego serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for MySQL elastyczny serwer.

> [!NOTE]
> Zapoznaj się z ograniczeniem korzystania z funkcji [Zatrzymaj/Uruchom](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Jak zatrzymać/uruchomić Azure Database for MySQL przy użyciu Azure Portal

### <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer MySQL, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL Zatrzymaj serwer":::

    > [!NOTE]
    > Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla serwera elastycznego.

### <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL uruchomić serwer":::

    > [!NOTE]
    > Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla serwera elastycznego.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Jak zatrzymać/uruchomić Azure Database for MySQL przy użyciu interfejsu wiersza polecenia

### <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer MySQL, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla serwera elastycznego.

### <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla serwera elastycznego.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
