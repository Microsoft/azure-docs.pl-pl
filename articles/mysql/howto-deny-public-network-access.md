---
title: Odmowa dostępu do sieci publicznej — Azure Portal-Azure Database for MySQL
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej za pomocą Azure Portal dla Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: eb5c4e4c4dfb73b2f7c9dc9f2629296790790885
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896143"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Odmowa dostępu do sieci publicznej w Azure Database for MySQL przy użyciu Azure Portal

W tym artykule opisano, jak można skonfigurować serwer Azure Database for MySQL, aby odmówić wszystkich publicznych konfiguracji i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ustaw Odmów dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić serwer MySQL Odmów dostępu do sieci publicznej:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL.

1. Na stronie serwer MySQL w obszarze **Ustawienia**kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **Odmów dostępu do sieci publicznej**wybierz pozycję **tak** , aby włączyć odmowa dostępu publicznego dla serwera MySQL.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database for MySQL odmowa dostępu do sieci":::

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL odmowa dostępu do sieci":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).