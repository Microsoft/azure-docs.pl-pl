---
title: Odmowa dostępu do sieci publicznej — Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej za pomocą Azure Portal dla Azure Database for PostgreSQL jednego serwera
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: bd777fe42dc8fa3ec4643fa9607f5ca8b7aac795
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240328"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odmowa dostępu do sieci publicznej w Azure Database for PostgreSQL pojedynczym serwerze przy użyciu Azure Portal

W tym artykule opisano sposób konfigurowania Azure Database for PostgreSQL pojedynczego serwera, aby odmówić wszystkich publicznych konfiguracji i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for PostgreSQL pojedynczy serwer](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>Ustaw Odmów dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić PostgreSQL jeden serwer Odmów dostępu do sieci publicznej:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for PostgreSQL pojedynczy serwer.

1. Na stronie PostgreSQL Single Server w obszarze **Ustawienia** kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **Odmów dostępu do sieci publicznej** wybierz pozycję **tak** , aby włączyć opcję Odmów dostępu publicznego dla pojedynczego serwera PostgreSQL.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL dostęp do sieci przy pojedynczym serwerze":::

1. Kliknij przycisk **Zapisz** , aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL dostęp do sieci przy pojedynczym serwerze":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
