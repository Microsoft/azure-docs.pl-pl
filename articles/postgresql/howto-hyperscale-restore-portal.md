---
title: Przywracanie — Hiperskala (Citus) — Azure Database for PostgreSQL — Azure Portal
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for PostgreSQL — Hiperskala (Citus) za pośrednictwem Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/14/2021
ms.openlocfilehash: 99e507fed35e5b74aa44bc2713550f7cf9d7b2e5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518885"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Przywracanie do punktu w czasie Hiperskala (Citus) grupy serwerów

Ten artykuł zawiera procedury krok po [](concepts-hyperscale-backup.md#restore) kroku dotyczące wykonywania odzyskiwania do punktu w czasie dla grupy Hiperskala (Citus) przy użyciu kopii zapasowych. W okresie przechowywania można przywrócić najwcześniejszą kopię zapasową lub niestandardowy punkt przywracania.

## <a name="restoring-to-the-earliest-restore-point"></a>Przywracanie do najwcześniejszego punktu przywracania

Wykonaj następujące kroki, aby przywrócić Hiperskala (Citus) grupy serwerów do jej najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz grupę serwerów, którą chcesz przywrócić.

2.  Kliknij **pozycję Przegląd** w lewym panelu, a następnie kliknij pozycję **Przywróć.**

    > [!IMPORTANT]
    > Jeśli przycisk **Przywróć** nie jest jeszcze obecny dla grupy serwerów, otwórz wniosek o pomoc techniczną platformy Azure.

3.  Na stronie przywracania pojawi się  prośba o wybranie między najwcześniejszym **a** niestandardowym punktem przywracania. Zostanie również wyświetlana najwcześniejsza data.

4.  Wybierz **pozycję Najwcześniejszy punkt przywracania.**

5.  Podaj nową nazwę grupy serwerów w **polu Przywróć do nowego** serwera. Inne pola (subskrypcja, grupa zasobów i lokalizacja) są wyświetlane, ale nie można ich edytować.

6.  Kliknij przycisk **OK**.

7.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.

Na koniec wykonaj zadania [wykonywane po przywróceniu](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Przywracanie do niestandardowego punktu przywracania

Wykonaj następujące kroki, aby przywrócić Hiperskala (Citus) grupy serwerów do daty i godziny.

1.  W [Azure Portal](https://portal.azure.com/)wybierz grupę serwerów, którą chcesz przywrócić.

2.  Kliknij **pozycję Przegląd** w lewym panelu, a następnie kliknij pozycję **Przywróć.**

    > [!IMPORTANT]
    > Jeśli przycisk **Przywróć** nie jest jeszcze obecny dla grupy serwerów, otwórz wniosek o pomoc techniczną platformy Azure.

3.  Na stronie przywracania pojawi się  prośba o wybranie między najwcześniejszym **a** niestandardowym punktem przywracania i wyświetleniem najwcześniejszej daty.

4.  Wybierz **pozycję Niestandardowy punkt przywracania.**

5.  Wybierz datę i godzina dla opcji **Punkt przywracania (UTC)** i podaj nową nazwę grupy serwerów w polu **Przywróć do nowego** serwera. Pozostałe pola (subskrypcja, grupa zasobów i lokalizacja) są wyświetlane, ale nie można ich edytować.
 
6.  Kliknij przycisk **OK**.

7.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.

Na koniec wykonaj zadania [wykonywane po przywróceniu](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Post-restore tasks

Po przywróceniu wykonaj następujące czynności, aby wykonać kopię zapasową użytkowników i aplikacji:

* Jeśli nowy serwer ma zastąpić oryginalny serwer, przekieruj klientów i aplikacje klienckie na nowy serwer
* Upewnij się, że użytkownicy mają dostęp do odpowiedniej zapory na poziomie serwera, aby użytkownicy się połączyli. Te reguły nie są kopiowane z oryginalnej grupy serwerów.
* Dostosuj parametry serwera PostgreSQL zgodnie z potrzebami. Parametry nie są kopiowane z oryginalnej grupy serwerów.
* Upewnij się, że są dostępne odpowiednie nazwy logowania i uprawnienia na poziomie bazy danych.
* Skonfiguruj alerty zgodnie z potrzebami.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na [temat tworzenia kopii zapasowych i przywracania](concepts-hyperscale-backup.md) w Hiperskala (Citus).
* Ustaw [sugerowane alerty](./howto-hyperscale-alert-on-metric.md#suggested-alerts) dla Hiperskala (Citus) serwera.
