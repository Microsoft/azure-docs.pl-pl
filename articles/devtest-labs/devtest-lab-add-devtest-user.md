---
title: Dodawanie właścicieli i użytkowników w Azure DevTest Labs | Microsoft Docs
description: Dodawanie właścicieli i użytkowników w Azure DevTest Labs przy użyciu Azure Portal lub programu PowerShell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 40173589c4798a8b00d940bca2a71f4d61f2c199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533329"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Dodawanie właścicieli i użytkowników w Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Dostęp w Azure DevTest Labs jest kontrolowany przez funkcję [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md). Korzystając z funkcji RBAC, można oddzielić obowiązki w zespole do *ról* , w których można uzyskać dostęp tylko do potrzeb użytkowników w celu wykonywania swoich zadań. Trzy z tych ról platformy Azure to *właściciel*, *użytkownik DevTest Labs*i *współautor*. W tym artykule dowiesz się, jakie akcje można wykonać w ramach każdej z trzech głównych ról platformy Azure. Z tego miejsca dowiesz się, jak dodawać użytkowników do laboratorium, za pośrednictwem portalu i za pośrednictwem skryptu programu PowerShell oraz jak dodawać użytkowników na poziomie subskrypcji.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akcje, które można wykonać w każdej roli
Istnieją trzy główne role, do których można przypisać użytkownika:

* Właściciel
* Użytkownik DevTest Labs
* Współautor

W poniższej tabeli przedstawiono akcje, które mogą być wykonywane przez użytkowników w każdej z tych ról:

| **Akcje, które użytkownicy w tej roli mogą wykonywać** | **Użytkownik DevTest Labs** | **Właściciel** | **Współautor** |
| --- | --- | --- | --- |
| **Zadania laboratoryjne** | | | |
| Dodawanie użytkowników do laboratorium |Nie |Tak |Nie |
| Aktualizowanie ustawień kosztów |Nie |Tak |Tak |
| **Zadania podstawowe maszyny wirtualnej** | | | |
| Dodawanie i usuwanie obrazów niestandardowych |Nie |Tak |Tak |
| Dodawanie, aktualizowanie i usuwanie formuł |Tak |Tak |Tak |
| Dozwolonych obrazów portalu Azure Marketplace |Nie |Tak |Tak |
| **Zadania maszyny wirtualnej** | | | |
| Tworzenie maszyn wirtualnych |Tak |Tak |Tak |
| Uruchamianie, zatrzymywanie i usuwanie maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Tak |Tak |
| Aktualizowanie zasad maszyny wirtualnej |Nie |Tak |Tak |
| Dodawanie/usuwanie dysków danych do/z maszyn wirtualnych |Tylko maszyny wirtualne utworzone przez użytkownika |Tak |Tak |
| **Zadania artefaktów** | | | |
| Dodawanie i usuwanie repozytoriów artefaktów |Nie |Tak |Tak |
| Zastosuj artefakty |Tak |Tak |Tak |

> [!NOTE]
> Gdy użytkownik tworzy maszynę wirtualną, ten użytkownik jest automatycznie przypisywany do roli **właściciela** utworzonej maszyny wirtualnej.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Dodawanie właściciela lub użytkownika na poziomie laboratorium
Właściciele i użytkownicy mogą zostać dodani do poziomu laboratorium za pośrednictwem Azure Portal. Użytkownik może być użytkownikiem zewnętrznym z prawidłowym [konto Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Poniższe kroki przeprowadzą Cię przez proces dodawania właściciela lub użytkownika do laboratorium w Azure DevTest Labs:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.
4. W bloku laboratorium wybierz pozycję **Konfiguracja i zasady**. 
5. Na stronie **Konfiguracja i zasady** wybierz pozycję **Kontrola dostępu (IAM)** z menu po lewej stronie. 
6. Wybierz pozycję **Dodaj przypisanie roli** na pasku narzędzi, aby dodać użytkownika do roli.
1. W oknie **Dodawanie uprawnień** wykonaj następujące czynności: 
    1. Wybierz rolę (na przykład: DevTest Labs User). W sekcji [akcje, które można wykonać w każdej roli](#actions-that-can-be-performed-in-each-role) , wymieniono różne akcje, które mogą być wykonywane przez użytkowników w rolach właściciel, DevTest użytkownika i współautor.
    2. Wybierz użytkownika, który ma zostać dodany do roli. 
    3. Wybierz pozycję **Zapisz**. 
11. Po powrocie do bloku **Użytkownicy** został dodany.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Dodawanie użytkownika zewnętrznego do laboratorium przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Oprócz dodawania użytkowników w Azure Portal można dodać użytkownika zewnętrznego do laboratorium przy użyciu skryptu programu PowerShell. W poniższym przykładzie Zmodyfikuj wartości parametrów w obszarze **wartości, aby zmienić** komentarz.
Możesz pobrać `subscriptionId` `labResourceGroup` wartości, i `labName` z bloku Lab w Azure Portal.

> [!NOTE]
> Przykładowy skrypt zakłada, że określony użytkownik został dodany jako gość do Active Directory i zakończy się niepowodzeniem, jeśli tak nie jest. Aby dodać użytkownika nieznajdującego się w Active Directory do laboratorium, użyj Azure Portal, aby przypisać użytkownika do roli, jak pokazano w sekcji, [Dodaj właściciela lub użytkownika na poziomie laboratorium](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Dodawanie właściciela lub użytkownika na poziomie subskrypcji
Uprawnienia platformy Azure są propagowane z zakresu nadrzędnego do zakresu podrzędnego na platformie Azure. W związku z tym właściciele subskrypcji platformy Azure, która zawiera laboratoria, są automatycznie właściciele tych laboratoriów. Są one również właścicielami maszyn wirtualnych i innych zasobów utworzonych przez użytkowników laboratorium i usługi Azure DevTest Labs. 

Można dodać kolejnych właścicieli do laboratorium za pośrednictwem bloku laboratorium w [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Jednak zakres administracji przez dodany właściciel jest bardziej wąski niż zakres właściciela subskrypcji. Na przykład dodani właściciele nie mają pełnego dostępu do niektórych zasobów utworzonych w ramach subskrypcji przez usługę DevTest Labs. 

Aby dodać właściciela do subskrypcji platformy Azure, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **subskrypcje** .
3. Wybierz żądaną subskrypcję.
4. Wybierz ikonę **dostępu** . 
   
    ![Dostęp do użytkowników](./media/devtest-lab-add-devtest-user/access-users.png)
5. W bloku **Użytkownicy** wybierz pozycję **Dodaj**.
   
    ![Dodaj użytkownika](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. W bloku **Wybierz rolę** wybierz pozycję **właściciel**.
7. W bloku **Dodaj użytkowników** wprowadź adres e-mail lub nazwę użytkownika, który ma zostać dodany jako właściciel. Jeśli nie można znaleźć użytkownika, zostanie wyświetlony komunikat o błędzie z wyjaśnieniem problemu. Jeśli użytkownik zostanie znaleziony, ten użytkownik zostanie wyświetlony w polu tekstowym **User (użytkownik** ).
8. Wybierz zlokalizowaną nazwę użytkownika.
9. Wybierz pozycję **Wybierz**.
10. Wybierz **przycisk OK** , aby zamknąć blok **Dodawanie dostępu** .
11. Po powrocie do bloku **Użytkownicy** użytkownik został dodany jako właściciel. Ten użytkownik jest teraz właścicielem wszystkich laboratoriów utworzonych w ramach tej subskrypcji, co umożliwia wykonywanie zadań będących właścicielami. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
