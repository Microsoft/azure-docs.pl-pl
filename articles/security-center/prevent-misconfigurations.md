---
title: Jak zapobiec błędom konfiguracji za pomocą Azure Security Center
description: Dowiedz się, jak używać opcji "Wymuś" i "Odmów" Security Center na stronach szczegółów rekomendacji
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906387"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Zapobiegaj błędom konfiguracji z zaleceń Wymuszaj/Odmów

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

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Strona rekomendacja z wyróżnionym przyciskiem Odmów":::


## <a name="enforce-a-secure-configuration"></a>Wymuś bezpieczną konfigurację

1. Otwórz zalecenie wdrożenia szablonu, jeśli nowe zasoby nie spełnią go, a następnie wybierz przycisk **Wymuszaj** w górnej części strony.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Strona rekomendacja z wyróżnionym przyciskiem Odmów":::

    Zostanie otwarte okienko Konfiguracja ze wszystkimi opcjami konfiguracji zasad. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Strona rekomendacja z wyróżnionym przyciskiem Odmów":::

1. Ustaw zakres, nazwę przypisania i inne odpowiednie opcje.

1. Wybierz pozycję **Przeglądanie + tworzenie**.

## <a name="recommendations-with-denyenforce-options"></a>Zalecenia z opcjami Odmów/wymuszania

Te zalecenia mogą być używane z opcją **Odmów** :

- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń
- Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw Service Bus
- Należy włączyć bezpieczny transfer na konta magazynu
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Zmienne konta usługi Automation powinny być szyfrowane
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu


Te zalecenia mogą być używane z opcją **Wymuszaj** :

- Dzienniki diagnostyczne w Logic Apps powinny być włączone
- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone
- Dzienniki diagnostyczne w IoT Hub powinny być włączone
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych
- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone
- Dzienniki diagnostyczne w Service Bus powinny być włączone
- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone
- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone
- Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone
- Dzienniki diagnostyczne w Key Vault powinny być włączone
- Inspekcja w programie SQL Server powinna być włączona
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL



