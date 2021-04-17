---
title: Przywracanie istniejącej dedykowanej puli SQL
description: Przewodnik z instrukcje dotyczące przywracania istniejącej dedykowanej puli SQL.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567982"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Przywracanie istniejącej dedykowanej puli SQL

Z tego artykułu dowiesz się, jak przywrócić istniejącą dedykowaną pulę SQL w programie Azure Synapse Analytics użyciu Azure Portal i Synapse Studio. Ten artykuł dotyczy zarówno przywracania, jak i przywracania geograficznego. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Przywracanie istniejącej dedykowanej puli SQL za pośrednictwem Synapse Studio

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do obszaru roboczego synapse. 
3. W Wprowadzenie -> Otwórz Synapse Studio wybierz pozycję **Otwórz**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. W okienku nawigacji po lewej stronie wybierz pozycję **Dane.**
5. Wybierz pozycję **Zarządzaj pulami.** 
6. Wybierz **pozycję + Nowa,** aby utworzyć nową dedykowaną pulę SQL. 
7. Na karcie Ustawienia dodatkowe wybierz punkt przywracania, z którym chcesz przywrócić. 

    Jeśli chcesz wykonać przywracanie geograficzne, wybierz obszar roboczy i dedykowaną pulę SQL, którą chcesz odzyskać. 

8. Wybierz pozycję **Automatyczne punkty przywracania** **lub Zdefiniowane przez użytkownika punkty przywracania.** 

    ![Punkty przywracania](../media/sql-pools/restore-point.PNG)

    Jeśli dedykowana pula SQL nie ma żadnych automatycznych punktów przywracania, zaczekaj kilka godzin lub utwórz punkt przywracania zdefiniowany przez użytkownika przed przywróceniem. Aby User-Defined punkty przywracania, wybierz istniejącą lub utwórz nowy.

    Jeśli przywracasz geograficzną kopię zapasową, po prostu wybierz obszar roboczy znajdujący się w regionie źródłowym i dedykowaną pulę SQL, którą chcesz przywrócić. 

9. Wybierz pozycję **Przejrzyj i utwórz**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Przywracanie istniejącej dedykowanej puli SQL za pośrednictwem Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do dedykowanej puli SQL, z której chcesz przywrócić.
3. W górnej części bloku Przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](../media/sql-pools/restore-sqlpool-01.png)

4. Wybierz pozycję **Automatyczne punkty przywracania** **lub Punkty przywracania zdefiniowane przez użytkownika.** 

    Jeśli dedykowana pula SQL nie ma żadnych punktów automatycznego przywracania, zaczekaj kilka godzin lub utwórz punkt przywracania zdefiniowany przez użytkownika przed przywróceniem. 

    Jeśli chcesz wykonać przywracanie geograficzne, wybierz obszar roboczy i dedykowaną pulę SQL, którą chcesz odzyskać. 

5. Wybierz pozycję **Przejrzyj i utwórz**.

## <a name="next-steps"></a>Następne kroki

- [Utwórz punkt przywracania](sqlpool-create-restore-point.md)
