---
title: Przywróć Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal.
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for MySQL elastycznym serwerze za pośrednictwem Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502049"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Przywracanie do punktu w czasie Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza) przy użyciu Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie dostępny w publicznej wersji zapoznawczej.

Ten artykuł zawiera procedurę krok po kroku w celu wykonania odzyskiwania do punktu w czasie na serwerze elastycznym przy użyciu kopii zapasowych.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania instrukcji w tym przewodniku potrzebne są:

-   Musisz mieć serwer Azure Database for MySQL elastyczny.

## <a name="restore-to-the-latest-restore-point"></a>Przywracanie do najnowszego punktu przywracania

Wykonaj następujące kroki, aby przywrócić serwer elastyczny przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Kliknij **pozycję Przegląd** w lewym panelu.

3.  Na stronie przeglądu kliknij pozycję **Przywróć.**

4.  Zostanie pokazana strona przywracania z opcją wyboru między najnowszym **punktem przywracania** a niestandardowym punktem przywracania.

5.  Wybierz **pozycję Najnowszy punkt przywracania.**

6.  Podaj nową nazwę serwera w **polu Przywróć do nowego** serwera.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Najwcześniejsza godzina przywracania":::

8.  Kliknij przycisk **OK**.

9.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.

## <a name="restoring-to-a-custom-restore-point"></a>Przywracanie do niestandardowego punktu przywracania

Wykonaj następujące kroki, aby przywrócić serwer elastyczny przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Na stronie przeglądu kliknij pozycję **Przywróć.**

3.  Zostanie pokazana strona przywracania z opcją wyboru między najwcześniejszym punktem przywracania a niestandardowym punktem przywracania.

4.  Wybierz **pozycję Niestandardowy punkt przywracania.**

5.  Wybierz datę i godzina.

6.  Podaj nową nazwę serwera w **polu Przywróć do nowego** serwera.

6.  Podaj nową nazwę serwera w **polu Przywróć do nowego** serwera.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="przegląd widoku":::

7.  Kliknij przycisk **OK**.

8.  Zostanie wyświetlone powiadomienie o zainicjowaniu operacji przywracania.


## <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu
Po zakończeniu przywracania należy wykonać następujące zadania, aby przywrócić użytkowników i aplikacje do pracy:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekieruj klientów i aplikacje klienckie na nowy serwer.
- Upewnij się, że istnieją odpowiednie reguły sieci wirtualnej, aby użytkownicy nawiązyli połączenie. Te reguły nie są kopiowane z oryginalnego serwera.
- Upewnij się, że są dostępne odpowiednie nazwy logowania i uprawnienia na poziomie bazy danych.
- Skonfiguruj alerty odpowiednio dla nowo przywróconego serwera.


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [ciągłości działania](concepts-business-continuity.md)
