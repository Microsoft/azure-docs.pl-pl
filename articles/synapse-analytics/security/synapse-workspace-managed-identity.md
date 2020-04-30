---
title: Zarządzana tożsamość w obszarze roboczym usługi Azure Synapse
description: Artykuł objaśniający tożsamość zarządzaną w obszarze roboczym usługi Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425119"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Tożsamość zarządzana obszaru roboczego usługi Azure Synapse (wersja zapoznawcza)

W tym artykule omówiono tożsamość zarządzaną w obszarze roboczym usługi Azure Synapse.

## <a name="managed-identities"></a>Zarządzane tożsamości

Zarządzana tożsamość zasobów platformy Azure to funkcja Azure Active Directory. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Można użyć funkcji tożsamości zarządzanej do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie w usłudze Azure AD.

Zarządzane tożsamości dla zasobów platformy Azure to nowa nazwa usługi znana wcześniej jako tożsamość usługi zarządzanej (MSI). Zobacz [zarządzane tożsamości](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby dowiedzieć się więcej.

## <a name="azure-synapse-workspace-managed-identity"></a>Tożsamość zarządzana obszaru roboczego usługi Azure Synapse

Podczas tworzenia obszaru roboczego tworzona jest tożsamość zarządzana przypisana przez system dla obszaru roboczego usługi Azure Synapse.

>[!NOTE]
>Ta tożsamość zarządzana w obszarze roboczym będzie określana jako tożsamość zarządzana w pozostałej części tego dokumentu.

Usługa Azure Synapse używa zarządzanej tożsamości do organizowania potoków. Cykl życia zarządzanej tożsamości jest bezpośrednio powiązany z obszarem roboczym usługi Azure Synapse. Po usunięciu obszaru roboczego Azure Synapse, tożsamość zarządzana jest również czyszczona.

Tożsamość zarządzana w obszarze roboczym musi mieć uprawnienia do wykonywania operacji w potokach. Możesz użyć identyfikatora obiektu lub nazwy obszaru roboczego usługi Azure Synapse, aby znaleźć tożsamość zarządzaną podczas udzielania uprawnień.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Pobierz tożsamość zarządzaną w Azure Portal

Tożsamość zarządzaną można pobrać w Azure Portal. Otwórz obszar roboczy usługi Azure Synapse w Azure Portal i wybierz pozycję **Przegląd** na lewym pasku nawigacyjnym. Identyfikator obiektu tożsamości zarządzanej jest wyświetlany na ekranie głównym.

![Identyfikator obiektu tożsamości zarządzanej](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Informacje o tożsamości zarządzanej również zostaną wyświetlone podczas tworzenia połączonej usługi, która obsługuje uwierzytelnianie tożsamości zarządzanej z usługi Azure Synapse Studio.

Uruchom **usługę Azure Synapse Studio** i wybierz kartę **Zarządzanie** na pasku nawigacyjnym po lewej stronie. Następnie wybierz pozycję **połączone usługi** i wybierz opcję **+ Nowa** , aby utworzyć nową połączoną usługę.

![Tworzenie połączonej usługi 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

W oknie **Nowa połączona usługa** wpisz *Azure Data Lake Storage Gen2*. Wybierz z poniższej listy Typ zasobu **Azure Data Lake Storage Gen2** i wybierz pozycję **Kontynuuj**.

![Tworzenie połączonej usługi 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

W następnym oknie wybierz pozycję **zarządzana tożsamość** dla **metody uwierzytelniania**. Zobaczysz **nazwę** i **Identyfikator obiektu**tożsamości zarządzanej.

![Tworzenie połączonej usługi 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Następne kroki

[Przyznawanie uprawnień do tożsamości zarządzanej w obszarze roboczym usługi Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
