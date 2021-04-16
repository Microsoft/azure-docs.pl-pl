---
title: Przywracanie — Hiperskala (Citus) — Azure Database for PostgreSQL — Azure Portal
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for PostgreSQL — Hiperskala (Citus) za pośrednictwem Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495077"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Przywracanie do punktu w czasie Hiperskala (Citus) grupy serwerów

Ten artykuł zawiera procedury krok po [](concepts-hyperscale-backup.md#point-in-time-restore-pitr) kroku dotyczące wykonywania odzyskiwania do punktu w czasie dla grupy Hiperskala (Citus) przy użyciu kopii zapasowych. Można przywrócić do najwcześniejszej kopii zapasowej lub do niestandardowego punktu przywracania w okresie przechowywania.

## <a name="restoring-to-the-earliest-restore-point"></a>Przywracanie do najwcześniejszego punktu przywracania

Wykonaj następujące kroki, aby przywrócić Hiperskala (Citus) grupy serwerów do jej najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz grupę serwerów, którą chcesz przywrócić.

2.  Kliknij **pozycję Przegląd** w lewym panelu, a następnie kliknij pozycję **Przywróć.**

    > [!IMPORTANT]
    > Jeśli przycisk **Przywróć** nie jest jeszcze obecny dla grupy serwerów, otwórz wniosek o pomoc techniczną platformy Azure.

3.  Na stronie przywracania pojawi się  prośba o wybranie między najwcześniejszym **a** niestandardowym punktem przywracania i wyświetleniem najwcześniejszej daty.

4.  Wybierz **pozycję Najwcześniejszy punkt przywracania.**

5.  Podaj nazwę nowej grupy serwerów w **polu Przywróć do nowego** serwera. Inne pola (subskrypcja, grupa zasobów i lokalizacja) są wyświetlane, ale nie można ich edytować.

6.  Kliknij przycisk **OK**.

7.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.

Na koniec wykonaj zadania [wykonywane po przywróceniu](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Przywracanie do niestandardowego punktu przywracania

Wykonaj następujące kroki, aby Hiperskala (Citus) grupy serwerów do zgodnie z potrzebami datą i godziną.

1.  W [Azure Portal](https://portal.azure.com/)wybierz grupę serwerów, którą chcesz przywrócić.

2.  Kliknij **pozycję Przegląd** w lewym panelu, a następnie kliknij pozycję **Przywróć.**

    > [!IMPORTANT]
    > Jeśli przycisk **Przywróć** nie jest jeszcze obecny dla grupy serwerów, otwórz wniosek o pomoc techniczną platformy Azure.

3.  Na stronie przywracania pojawi się  prośba o wybranie między najwcześniejszym **a** niestandardowym punktem przywracania. Zostanie również wyświetlana najwcześniejsza data.

4.  Wybierz **pozycję Niestandardowy punkt przywracania.**

5.  Wybierz datę i czas dla pola **Punkt przywracania (UTC)** i podaj nową nazwę grupy serwerów w polu Przywróć **do nowego** serwera. Inne pola (subskrypcja, grupa zasobów i lokalizacja) są wyświetlane, ale nie można ich edytować.
 
6.  Kliknij przycisk **OK**.

7.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.

Na koniec wykonaj zadania [wykonywane po przywróceniu](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Post-restore tasks

Po przywróceniu wykonaj następujące czynności, aby tworzyć i uruchamiać użytkowników i aplikacje:

* Jeśli nowy serwer ma zastąpić oryginalny serwer, przekieruj klientów i aplikacje klienckie na nowy serwer
* Upewnij się, że istnieje odpowiednia zapora na poziomie serwera, z którym użytkownicy mogą się łączyć. Te reguły nie są kopiowane z oryginalnej grupy serwerów.
* Dostosuj parametry serwera PostgreSQL zgodnie z potrzebami. Parametry nie są kopiowane z oryginalnej grupy serwerów.
* Upewnij się, że są dostępne odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych.
* Skonfiguruj alerty zgodnie z potrzebami.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na [temat tworzenia kopii zapasowych i przywracania](concepts-hyperscale-backup.md) w Hiperskala (Citus).
* Ustaw [sugerowane alerty](./howto-hyperscale-alert-on-metric.md#suggested-alerts) dla Hiperskala (Citus) serwera.
