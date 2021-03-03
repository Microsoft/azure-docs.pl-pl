---
title: Tożsamość zarządzana w obszarze roboczym Synapse
description: Artykuł objaśniający tożsamość zarządzaną w obszarze roboczym usługi Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7790bc2895449e8ab21cbd30d7da0e5529eb0562
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670674"
---
# <a name="azure-synapse-workspace-managed-identity"></a>Tożsamość zarządzana obszaru roboczego usługi Azure Synapse

W tym artykule omówiono tożsamość zarządzaną w obszarze roboczym usługi Azure Synapse.

## <a name="managed-identities"></a>Tożsamości zarządzane

Zarządzana tożsamość zasobów platformy Azure to funkcja Azure Active Directory. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Można użyć funkcji tożsamości zarządzanej do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie w usłudze Azure AD.

Zarządzane tożsamości dla zasobów platformy Azure to nowa nazwa usługi znana wcześniej jako tożsamość usługi zarządzanej (MSI). Zobacz [zarządzane tożsamości](../../active-directory/managed-identities-azure-resources/overview.md) , aby dowiedzieć się więcej.

## <a name="azure-synapse-workspace-managed-identity"></a>Tożsamość zarządzana obszaru roboczego usługi Azure Synapse

Tożsamość zarządzana przypisana przez system jest tworzona dla obszaru roboczego usługi Azure Synapse podczas jego tworzenia.

>[!NOTE]
>Ta tożsamość zarządzana w obszarze roboczym będzie określana jako tożsamość zarządzana w pozostałej części tego dokumentu.

Usługa Azure Synapse używa zarządzanej tożsamości do integrowania potoków. Cykl życia zarządzanej tożsamości jest bezpośrednio powiązany z obszarem roboczym usługi Azure Synapse. Po usunięciu obszaru roboczego Azure Synapse, tożsamość zarządzana jest również czyszczona.

Tożsamość zarządzana w obszarze roboczym musi mieć uprawnienia do wykonywania operacji w potokach. Możesz użyć identyfikatora obiektu lub nazwy obszaru roboczego usługi Azure Synapse, aby znaleźć tożsamość zarządzaną podczas udzielania uprawnień.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Pobierz tożsamość zarządzaną w Azure Portal

Tożsamość zarządzaną można pobrać w Azure Portal. Otwórz obszar roboczy usługi Azure Synapse w Azure Portal i wybierz pozycję **Przegląd** na lewym pasku nawigacyjnym. Identyfikator obiektu tożsamości zarządzanej jest wyświetlany na ekranie głównym.

![Identyfikator obiektu tożsamości zarządzanej](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Informacje o tożsamości zarządzanej również zostaną wyświetlone podczas tworzenia połączonej usługi, która obsługuje uwierzytelnianie tożsamości zarządzanej z usługi Azure Synapse Studio.

Uruchom **usługę Azure Synapse Studio** i wybierz kartę **Zarządzanie** na pasku nawigacyjnym po lewej stronie. Następnie wybierz pozycję **połączone usługi** i wybierz opcję **+ Nowa** , aby utworzyć nową połączoną usługę.

![Tworzenie połączonej usługi 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

W oknie **Nowa połączona usługa** wpisz *Azure Data Lake Storage Gen2*. Wybierz z poniższej listy Typ zasobu **Azure Data Lake Storage Gen2** i wybierz pozycję **Kontynuuj**.

![Tworzenie połączonej usługi 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

W następnym oknie wybierz pozycję **zarządzana tożsamość** dla **metody uwierzytelniania**. Zobaczysz **nazwę** i **Identyfikator obiektu** tożsamości zarządzanej.

![Tworzenie połączonej usługi 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat przyznawania uprawnień do tożsamości zarządzanej obszaru roboczego usługi Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
