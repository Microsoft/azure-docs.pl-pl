---
title: Tworzenie punktu przywracania zdefiniowanego przez użytkownika dla dedykowanej puli SQL
description: Dowiedz się, jak za pomocą Azure Portal utworzyć punkt przywracania zdefiniowany przez użytkownika dla dedykowanej puli SQL w usłudze Azure Synapse Analytics.
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
ms.openlocfilehash: 21fd20100095040fda9f72b00e17147ff560fbca
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579542"
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

