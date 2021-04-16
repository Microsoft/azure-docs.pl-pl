---
title: Tworzenie punktu przywracania zdefiniowanego przez użytkownika dla dedykowanej puli SQL
description: Dowiedz się, jak za pomocą Azure Portal utworzyć zdefiniowany przez użytkownika punkt przywracania dla dedykowanej puli SQL w Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567931"
---
# <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

W tym artykule dowiesz się, jak utworzyć nowy punkt przywracania zdefiniowany przez użytkownika dla dedykowanej puli SQL w programie Azure Synapse Analytics przy użyciu Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Tworzenie punktów przywracania zdefiniowanych przez użytkownika za pośrednictwem Azure Portal

Punkty przywracania zdefiniowane przez użytkownika można również tworzyć za pośrednictwem Azure Portal.

1. Zaloguj się do [swojego Azure Portal](https://portal.azure.com/) konta.

2. Przejdź do dedykowanej puli SQL, dla której chcesz utworzyć punkt przywracania.

3. Wybierz **pozycję Przegląd** w okienku po lewej stronie, a następnie wybierz pozycję + Nowy punkt **przywracania.** Jeśli przycisk Nowy punkt przywracania nie jest włączony, upewnij się, że dedykowana pula SQL nie jest wstrzymana.

    ![Nowy punkt przywracania](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Określ nazwę punktu przywracania zdefiniowanego przez użytkownika i kliknij przycisk **Zastosuj.** Punkty przywracania zdefiniowane przez użytkownika mają domyślny okres przechowywania siedmiu dni.

    ![Nazwa punktu przywracania](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej dedykowanej puli SQL](restore-sql-pool.md)

