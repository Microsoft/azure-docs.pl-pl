---
title: Jak zapobiec błędom konfiguracji za pomocą Azure Security Center
description: Dowiedz się, jak używać opcji "Wymuś" i "Odmów" Security Center na stronach szczegółów rekomendacji
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: fabbd255f64e5614ae052c9d7c8d65fc35d45856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600500"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Zapobieganie błędom konfiguracji za pomocą wymuszania/odmawiania — zalecenia

Błędy konfiguracji zabezpieczeń są główną przyczyną zdarzeń zabezpieczeń. Security Center teraz ma możliwość *zapobiegania* błędom konfiguracji nowych zasobów w odniesieniu do określonych zaleceń. 

Ta funkcja może pomóc w zabezpieczeniu obciążeń i stabilizacji bezpiecznego wyniku.

Wymuszanie bezpiecznej konfiguracji w oparciu o określone zalecenie jest oferowane w dwóch trybach:

- Przy użyciu efektu **odmowy** Azure Policy można zatrzymać tworzenie zasobów w złej kondycji
- Przy użyciu opcji **Wymuszaj** można korzystać z **DeployIfNotExist** i automatycznego korygowania niezgodnych zasobów podczas tworzenia

Można to znaleźć w górnej części strony Szczegóły zasobów dla wybranych zaleceń dotyczących zabezpieczeń (zobacz [zalecenia z opcjami Odmów/wymuszania](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Zapobiegaj tworzeniu zasobów

1. Otwórz zalecenie, które musi spełniać nowe zasoby, a następnie wybierz przycisk **Odmów** w górnej części strony.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Strona rekomendacja z wyróżnionym przyciskiem Odmów":::

    Zostanie otwarte okienko Konfiguracja z listami opcji zakresu. 

1. Ustaw zakres, wybierając odpowiednią subskrypcję lub grupę zarządzania.

    > [!TIP]
    > Możesz użyć trzech kropek na końcu wiersza, aby zmienić jedną subskrypcję, lub użyć pól wyboru, aby wybrać wiele subskrypcji lub grup, a następnie wybrać pozycję **Zmień na Odmów**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Ustawianie zakresu dla Azure Policy Odmów":::


## <a name="enforce-a-secure-configuration"></a>Wymuś bezpieczną konfigurację

1. Otwórz zalecenie wdrożenia szablonu, jeśli nowe zasoby nie spełnią go, a następnie wybierz przycisk **Wymuszaj** w górnej części strony.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Strona rekomendacja z wyróżnionym przyciskiem Wymuszaj":::

    Zostanie otwarte okienko Konfiguracja ze wszystkimi opcjami konfiguracji zasad. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Wymuszaj opcje konfiguracji":::

1. Ustaw zakres, nazwę przypisania i inne odpowiednie opcje.

1. Wybierz pozycję **Przejrzyj i utwórz**.

## <a name="recommendations-with-denyenforce-options"></a>Zalecenia z opcjami Odmów/wymuszania

Te zalecenia mogą być używane z opcją **Odmów** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Te zalecenia mogą być używane z opcją **Wymuszaj** :

- Inspekcja w programie SQL Server powinna być włączona
- Azure Backup powinna być włączona dla maszyn wirtualnych
- Usługa Azure Defender dla serwera SQL powinna być włączona na serwerach SQL
- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach
- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych
- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone
- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone
- Dzienniki diagnostyczne w Key Vault powinny być włączone
- Dzienniki diagnostyczne w Logic Apps powinny być włączone
- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone
- Dzienniki diagnostyczne w Service Bus powinny być włączone
