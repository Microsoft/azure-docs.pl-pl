---
title: Tworzenie punktu przywracania zdefiniowanego przez użytkownika dla dedykowanej puli SQL
description: Jak utworzyć punkt przywracania dla dedykowanej puli SQL.
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
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332133"
---
# <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

W tym artykule opisano tworzenie nowego punktu przywracania zdefiniowanego przez użytkownika dla dedykowanej puli SQL w usłudze Azure Synapse Analytics przy użyciu Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Utwórz punkty przywracania zdefiniowane przez użytkownika za pomocą Azure Portal

Punkty przywracania zdefiniowane przez użytkownika mogą być również tworzone za poorednictwem Azure Portal.

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .

2. Przejdź do dedykowanej puli SQL, dla której chcesz utworzyć punkt przywracania.

3. Wybierz pozycję **Przegląd** w okienku po lewej stronie, a następnie wybierz pozycję **+ nowy punkt przywracania**. Jeśli przycisk Nowy punkt przywracania nie jest włączony, upewnij się, że dedykowana Pula SQL nie jest wstrzymana.

    ![Nowy punkt przywracania](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Określ nazwę punktu przywracania zdefiniowanego przez użytkownika, a następnie kliknij przycisk **Zastosuj**. Punkty przywracania zdefiniowane przez użytkownika mają domyślny okres przechowywania wynoszący siedem dni.

    ![Nazwa punktu przywracania](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej dedykowanej puli SQL](restore-sql-pool.md)

