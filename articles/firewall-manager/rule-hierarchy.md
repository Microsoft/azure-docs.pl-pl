---
title: Definiowanie hierarchii reguł przy użyciu zasad zapory platformy Azure
description: Dowiedz się, jak definiować hierarchię reguł i wymuszać zgodność za pomocą zasad zapory platformy Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: c290904c9f4bc7dba70dad9351dc45b676e0c236
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893721"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Definiowanie hierarchii reguł przy użyciu zasad zapory platformy Azure

Administratorzy zabezpieczeń muszą zarządzać zaporami i zapewnić zgodność między wdrożeniami lokalnymi i w chmurze. Kluczowy składnik to możliwość zapewnienia zespołom aplikacji elastyczności w celu wdrożenia potoków ciągłej integracji/ciągłego tworzenia reguł zapory w zautomatyzowany sposób.

Zasady zapory platformy Azure umożliwiają zdefiniowanie hierarchii reguł i Wymuszanie zgodności:

- Udostępnia strukturę hierarchiczną do nakładania centralnych zasad podstawowych na nadrzędne zasady zespołu aplikacji. Zasady podstawowe mają wyższy priorytet i są uruchamiane przed zasadami podrzędnymi.
- Użyj niestandardowej definicji kontroli dostępu opartej na rolach (RBAC), aby zapobiec przypadkowemu usunięciu zasad podstawowych i zapewnić selektywny dostęp do grup kolekcji reguł w ramach subskrypcji lub grupy zasobów. 

## <a name="solution-overview"></a>Omówienie rozwiązania

Poniżej przedstawiono ogólne kroki, które są następujące:

1. Utwórz podstawowe zasady zapory w grupie zasobów zespołu zabezpieczeń. 
3. Zdefiniuj reguły specyficzne dla zabezpieczeń IT w zasadach podstawowych. Spowoduje to dodanie wspólnego zestawu reguł, aby zezwalać na ruch/odmawiać go.
4. Utwórz zasady zespołu aplikacji, które dziedziczą zasady podstawowe. 
5. Zdefiniuj reguły specyficzne dla zespołu aplikacji w zasadach. Można również migrować reguły z istniejących wcześniej zapór.
6. Utwórz niestandardowe role Azure Active Directory, aby zapewnić szczegółowy dostęp do grupy kolekcji reguł i dodać role w zakresie zasad zapory. W poniższym przykładzie członkowie zespołu ds. sprzedaży mogą edytować grupy kolekcji reguł dla zasad zapory dla zespołów ds. sprzedaży. To samo dotyczy zespołów baz danych i inżynierów.
7. Skojarz zasady z odpowiednią zaporą. Zapora platformy Azure może mieć tylko jedną przypisaną zasadę. Wymaga to, aby każdy zespół aplikacji miał własną zaporę.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Zespoły i wymagania" border="false":::

### <a name="create-the-firewall-policies"></a>Tworzenie zasad zapory

- Podstawowe zasady zapory.

Utwórz zasady dla każdego zespołu aplikacji:

- Zasady dotyczące zapory sprzedaży. Zasady zapory sprzedaży dziedziczą podstawowe zasady zapory.
- Zasady zapory bazy danych. Zasady zapory bazy danych dziedziczą podstawowe zasady zapory.
- Zasady zapory inżynieryjnej. Zasady zapory inżynieryjnej również dziedziczą podstawowe zasady zapory.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Zespoły i wymagania" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Tworzenie ról niestandardowych w celu uzyskania dostępu do grup kolekcji reguł 

Role niestandardowe są definiowane dla każdego zespołu aplikacji. Rola definiuje operacje i zakres. Zespoły aplikacji mogą edytować grupy kolekcji reguł dla odpowiednich aplikacji.

Użyj poniższej procedury wysokiego poziomu, aby zdefiniować role niestandardowe:

1. Pobierz subskrypcję:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Uruchom następujące polecenie:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Użyj polecenia Get-AzRoleDefinition, aby wyprowadzić rolę czytnika w formacie JSON. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Otwórz ReaderSupportRole.jsw pliku w edytorze.

   Poniżej przedstawiono dane wyjściowe w formacie JSON. Aby uzyskać informacje o różnych właściwościach, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Edytuj plik JSON, aby dodać 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   Operacja do właściwości **Actions**   . Pamiętaj o dodaniu przecinka po operacji odczytu. Ta akcja umożliwia użytkownikowi tworzenie i aktualizowanie grup kolekcji reguł.
6. W programie **AssignableScopes**Dodaj swój identyfikator subskrypcji o następującym formacie: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Musisz jawnie dodać identyfikatory subskrypcji, ponieważ w przeciwnym razie nie będzie można zaimportować roli do subskrypcji.
7. Usuń **Id**   wiersz właściwości ID i zmień właściwość **IsCustom**   na wartość true.
8. Zmień właściwości **nazwy**   i **opisu**   na *autora grupy kolekcji reguł AZFM* , a *Użytkownicy w tej roli mogą edytować grupy kolekcji reguł zasad zapory*

Plik JSON powinien wyglądać podobnie do poniższego przykładu:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Aby utworzyć nową rolę niestandardową, użyj polecenia New-AzRoleDefinition i określ plik definicji roli JSON. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę wszystkich ról niestandardowych, można użyć Get-AzRoleDefinition polecenie:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Role niestandardowe można także wyświetlić w Azure Portal. Przejdź do subskrypcji, wybierz pozycję **Kontrola dostępu (IAM)**, **role**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="Zespoły i wymagania":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Zespoły i wymagania":::

Aby uzyskać więcej informacji, zobacz [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Dodawanie użytkowników do roli niestandardowej

W portalu możesz dodać użytkowników do roli autorów grup kolekcji reguł AZFM i zapewnić dostęp do zasad zapory.

1. W portalu wybierz zasady zapory zespołu aplikacji (na przykład SalesAppPolicy).
2. Wybierz **Access Control**.
3. Wybierz pozycję **Dodaj przypisanie roli**.
4. Dodaj użytkowników/grupy użytkowników (na przykład zespół sprzedaży) do roli.

Powtórz tę procedurę dla innych zasad zapory.

### <a name="summary"></a>Podsumowanie

Zasady zapory z niestandardową RBAC umożliwiają teraz dostęp do grup kolekcji reguł zasad zapory.

Użytkownicy nie mają uprawnień do:
- Usuń zaporę lub zasady zapory platformy Azure.
- Aktualizowanie hierarchii zasad zapory lub ustawień DNS lub analizy zagrożeń.
- Zaktualizuj zasady zapory, gdy nie są członkami grupy autora kolekcji reguł AZFM.

Administratorzy zabezpieczeń mogą używać zasad podstawowych w celu wymuszania guardrails i blokowania niektórych typów ruchu (na przykład ICMP) zgodnie z wymaganiami przedsiębiorstwa. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zasadach zapory platformy Azure](policy-overview.md).

