---
title: Uaktualnienie wersji głównej — Azure Portal-Azure Database for MySQL — pojedynczy serwer
description: W tym artykule opisano, jak uaktualnić wersję główną dla Azure Database for MySQL-pojedynczego serwera przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686957"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Uaktualnienie wersji głównej w Azure Database for MySQL pojedynczym serwerze przy użyciu Azure Portal

> [!IMPORTANT]
> Uaktualnienie wersji głównej dla pojedynczego serwera usługi Azure Database for MySQL jest w publicznej wersji zapoznawczej.

W tym artykule opisano, jak można uaktualnić wersję główną programu MySQL w miejscu Azure Database for MySQL jednym serwerze.

Ta funkcja umożliwi klientom wykonywanie uaktualnień w miejscu serwerów MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu kliknięcia przycisku bez przenoszenia danych lub konieczności zmiany parametrów połączenia aplikacji.

> [!Note]
> * Uaktualnienie wersji głównej jest dostępne tylko w przypadku uaktualnienia wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7<br>
> * Uaktualnienie wersji głównej nie jest jeszcze obsługiwane na serwerze repliki.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Azure Database for MySQL pojedynczy serwer](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Wykonaj uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7

Wykonaj następujące kroki, aby przeprowadzić uaktualnienie wersji głównej usługi Azure Database of MySQL 5,6

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL 5,6.

2. Na stronie **Przegląd** kliknij przycisk **Uaktualnij** na pasku narzędzi.

3. W sekcji **Uaktualnij** wybierz pozycję **OK** , aby uaktualnić serwer usługi Azure Database for MySQL 5,6 do wersji 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL — Omówienie — uaktualnienie":::

4. Powiadomienie zostanie potwierdzone, że uaktualnienie zakończyło się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zasadach dotyczących wersji Azure Database for MySQL](concepts-version-policy.md).