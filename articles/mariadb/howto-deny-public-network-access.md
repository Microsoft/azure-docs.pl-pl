---
title: Odmowa dostępu do sieci publicznej — Azure Portal-Azure Database for MariaDB
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej za pomocą Azure Portal dla Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 7925107f4334df7a844b3f3e029f3769eef51a9c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665076"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Odmowa dostępu do sieci publicznej w Azure Database for MariaDB przy użyciu Azure Portal

W tym artykule opisano, jak można skonfigurować serwer Azure Database for MariaDB, aby odmówić wszystkich publicznych konfiguracji i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ustaw Odmów dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić MariaDB serwera Odmów dostępu do sieci publicznej:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MariaDB.

1. Na stronie serwer MariaDB w obszarze **Ustawienia** kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu Odmów dostępu do sieci publicznej wybierz pozycję **tak** , aby włączyć opcję Odmów dostępu publicznego dla serwera MariaDB.

    ![Azure Database for MariaDB odmowa dostępu do sieci](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Azure Database for MariaDB odmowa dostępu do sieci](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-metric.md).