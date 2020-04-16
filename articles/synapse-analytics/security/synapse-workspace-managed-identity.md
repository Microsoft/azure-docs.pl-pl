---
title: Tożsamość zarządzana w obszarze roboczym usługi Azure Synapse
description: Artykuł wyjaśniający tożsamość zarządzaną w obszarze roboczym Usługi Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425119"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Tożsamość zarządzana obszaru roboczego Usługi Azure Synapse (wersja zapoznawcza)

W tym artykule dowiesz się o tożsamości zarządzanej w obszarze roboczym Usługi Azure Synapse.

## <a name="managed-identities"></a>Tożsamości zarządzane

Tożsamość zarządzana dla zasobów platformy Azure jest funkcją usługi Azure Active Directory. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą funkcji Tożsamość zarządzana można uwierzytelniać do dowolnej usługi obsługującej uwierzytelnianie usługi Azure AD.

Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi wcześniej znanej jako tożsamość usługi zarządzanej (MSI). Zobacz [Tożsamości zarządzane,](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby dowiedzieć się więcej.

## <a name="azure-synapse-workspace-managed-identity"></a>Tożsamość zarządzana obszarem roboczym usługi Azure Synapse

Tożsamość zarządzana przypisana do systemu jest tworzona dla obszaru roboczego usługi Azure Synapse podczas tworzenia obszaru roboczego.

>[!NOTE]
>Ta tożsamość zarządzana obszaru roboczego będzie określana jako tożsamość zarządzana za pośrednictwem pozostałej części tego dokumentu.

Usługa Azure Synapse używa tożsamości zarządzanej do organizowania potoków. Cykl życia tożsamości zarządzanej jest bezpośrednio powiązany z obszarem roboczym usługi Azure Synapse. Jeśli usuniesz obszar roboczy Usługi Azure Synapse, tożsamość zarządzana jest również czyszczona.

Tożsamość zarządzana obszaru roboczego wymaga uprawnień do wykonywania operacji w potokach. Identyfikator obiektu lub nazwę obszaru roboczego Usługi Azure Synapse można użyć, aby znaleźć tożsamość zarządzana podczas udzielania uprawnień.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Pobieranie tożsamości zarządzanej w witrynie Azure portal

Tożsamość zarządzana można pobrać w witrynie Azure portal. Otwórz obszar roboczy usługi Azure Synapse w witrynie Azure portal i wybierz **omówienie** z lewej strony nawigacji. Identyfikator obiektu tożsamości zarządzanej jest wyświetlany na ekranie głównym.

![Identyfikator obiektu tożsamości zarządzanej](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Informacje o tożsamości zarządzanej będą również wyświetlane podczas tworzenia połączonej usługi obsługującej uwierzytelnianie tożsamości zarządzanej z usługi Azure Synapse Studio.

Uruchom **usługę Azure Synapse Studio** i wybierz kartę **Zarządzaj** z lewej strony nawigacji. Następnie wybierz pozycję **Połączone usługi** i wybierz opcję **+ Nowy,** aby utworzyć nową usługę połączona.

![Tworzenie usługi połączonej 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

W oknie **Nowa usługa połączona** wpisz *Azure Data Lake Storage Gen2*. Wybierz typ zasobu **usługi Azure Data Lake Storage Gen2** z poniższej listy i wybierz pozycję **Kontynuuj**.

![Tworzenie usługi połączonej 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

W następnym oknie wybierz pozycję **Zarządzana tożsamość** dla **uwierzytelniania.** Zobaczysz **nazwę** tożsamości zarządzanej i **identyfikator obiektu**.

![Tworzenie usługi połączonej 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Następne kroki

[Udzielanie uprawnień do tożsamości zarządzanej obszaru roboczego Usługi Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
