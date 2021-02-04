---
title: Jak zapobiec błędom konfiguracji za pomocą Azure Security Center
description: Dowiedz się, jak używać opcji "Wymuś" i "Odmów" Security Center na stronach szczegółów rekomendacji
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558188"
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

- Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć
- Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej
- Konta Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych
- Obszary robocze Azure Machine Learning powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)
- Chmura ze sprężyną na platformie Azure powinna używać iniekcji sieci
- Konta Cognitive Services powinny włączać szyfrowanie danych za pomocą klucza zarządzanego przez klienta (CMK)
- Należy wymusić limity procesora CPU i pamięci kontenera
- Obrazy kontenerów powinny być wdrażane tylko z zaufanych rejestrów
- Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)
- Należy unikać kontenera z eskalacją uprawnień
- Należy unikać obsługi kontenerów chronionych przestrzeni nazw hosta
- Kontenery powinny nasłuchiwać tylko na dozwolonych portach
- Niezmienny (tylko do odczytu) główny system plików powinien zostać wymuszony dla kontenerów
- Klucze Key Vault powinny mieć datę wygaśnięcia
- Wpisy tajne Key Vault powinny mieć datę wygaśnięcia
- W magazynach kluczy powinna być włączona ochrona przed czyszczeniem
- Magazyny kluczy powinny mieć włączone usuwanie nietrwałe
- W przypadku kontenerów powinny być wymuszane najmniej uprzywilejowane funkcje systemu Linux.
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Zastępowanie lub wyłączanie profilowania AppArmor profil powinien być ograniczony
- Należy unikać używania kontenerów uprzywilejowanych
- Należy unikać uruchamiania kontenerów jako użytkownik główny
- Należy włączyć bezpieczny transfer na konta magazynu
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- Usługi powinny nasłuchiwać tylko na dozwolonych portach
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej
- Użycie sieci i portów hosta powinno być ograniczone
- Użycie instalacji woluminów HostPath na początku musi być ograniczone do znanej listy, aby ograniczyć dostęp do węzła ze złamanych kontenerów
- Okres ważności certyfikatów przechowywanych w Azure Key Vault nie powinien przekraczać 12 miesięcy
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi

Te zalecenia mogą być używane z opcją **Wymuszaj** :

- Inspekcja w programie SQL Server powinna być włączona
- Azure Backup powinna być włączona dla maszyn wirtualnych
- Usługa Azure Defender dla serwera SQL powinna być włączona na serwerach SQL
- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych
- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone
- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone
- Dzienniki diagnostyczne w Key Vault powinny być włączone
- Dzienniki diagnostyczne w Logic Apps powinny być włączone
- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone
- Dzienniki diagnostyczne w Service Bus powinny być włączone