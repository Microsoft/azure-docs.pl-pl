---
title: Przywracanie istniejącej dedykowanej puli SQL
description: Przewodnik dotyczący przywracania istniejącej dedykowanej puli SQL.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94332099"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Przywracanie istniejącej dedykowanej puli SQL

Ten artykuł zawiera informacje na temat przywracania istniejącej dedykowanej puli SQL w usłudze Azure Synapse Analytics przy użyciu Azure Portal i Synapse Studio. Ten artykuł ma zastosowanie do przywracania i przywracania geograficznego. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Przywracanie istniejącej dedykowanej puli SQL za pomocą programu Synapse Studio

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do obszaru roboczego Synapse. 
3. W obszarze Wprowadzenie-> Otwórz Synapse Studio, wybierz pozycję **Otwórz**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. W okienku nawigacji po lewej stronie wybierz pozycję **dane**.
5. Wybierz pozycję **Zarządzaj pulami**. 
6. Wybierz pozycję **+ Nowy** , aby utworzyć nową dedykowaną pulę SQL. 
7. Na karcie Ustawienia dodatkowe wybierz punkt przywracania, z którego chcesz wykonać przywracanie. 

    Jeśli chcesz przeprowadzić przywracanie geograficzne, wybierz obszar roboczy i dedykowaną pulę SQL, które chcesz odzyskać. 

8. Wybierz **punkty przywracania automatycznego** lub **punkty przywracania zdefiniowane przez użytkownika**. 

    ![Punkty przywracania](../media/sql-pools/restore-point.PNG)

    Jeśli dedykowana Pula SQL nie zawiera żadnych automatycznych punktów przywracania, poczekaj kilka godzin lub przed przywróceniem Utwórz punkt przywracania zdefiniowany przez użytkownika. W przypadku punktów przywracania User-Defined wybierz istniejącą lub Utwórz nową.

    Jeśli przywracasz geograficzną kopię zapasową, po prostu wybierz obszar roboczy znajdujący się w regionie źródłowym i dedykowaną pulę SQL, które chcesz przywrócić. 

9. Wybierz pozycję **Przejrzyj i utwórz**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Przywracanie istniejącej dedykowanej puli SQL za pomocą Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do dedykowanej puli SQL, z której chcesz wykonać przywracanie.
3. W górnej części bloku przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](../media/sql-pools/restore-sqlpool-01.png)

4. Wybierz **punkty przywracania automatycznego** lub **punkty przywracania zdefiniowane przez użytkownika**. 

    Jeśli dedykowana Pula SQL nie zawiera żadnych automatycznych punktów przywracania, poczekaj kilka godzin lub przed przywróceniem Utwórz punkt przywracania zdefiniowany przez użytkownika. 

    Jeśli chcesz przeprowadzić przywracanie geograficzne, wybierz obszar roboczy i dedykowaną pulę SQL, które chcesz odzyskać. 

5. Wybierz pozycję **Przejrzyj i utwórz**.

## <a name="next-steps"></a>Następne kroki

- [Utwórz punkt przywracania](sqlpool-create-restore-point.md)
