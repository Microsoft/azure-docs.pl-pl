---
title: Zarządzanie Azure Automation kontem Uruchom jako
description: W tym artykule opisano, jak zarządzać kontem Uruchom jako przy użyciu programu PowerShell lub z poziomu Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f170fc948f136f4f46634e7ae2645ed2eb357afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096467"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Zarządzanie Azure Automation kontem Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami w ramach Azure Resource Manager lub klasycznego modelu wdrażania platformy Azure przy użyciu elementów Runbook usługi Automation i innych funkcji automatyzacji. Ten artykuł zawiera wskazówki dotyczące zarządzania kontem Uruchom jako lub klasycznego.

Aby dowiedzieć się więcej na temat Azure Automation uwierzytelniania i wskazówek dotyczących procesu automatyzacji procesów, zobacz [Omówienie uwierzytelniania konta usługi Automation](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Odnów certyfikat z podpisem własnym

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa w jednym roku od daty utworzenia. W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Można je odnowić w dowolnym momencie przed wygaśnięciem.

W przypadku odnowienia certyfikatu z podpisem własnym bieżący prawidłowy certyfikat jest zachowywany, aby upewnić się, że wszystkie elementy Runbook, które zostały dodane do kolejki lub aktywnie uruchomione, i które uwierzytelniają się przy użyciu konta Uruchom jako, nie mają negatywnego wpływu. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

>[!NOTE]
>Jeśli uważasz, że zabezpieczenia konta Uruchom jako zostały naruszone, możesz usunąć i ponownie utworzyć certyfikat z podpisem własnym.

>[!NOTE]
>Jeśli konto Uruchom jako zostało skonfigurowane do użycia certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa i używasz opcji odnawiania certyfikatu z podpisem własnym, certyfikat przedsiębiorstwa zostanie zastąpiony certyfikatem z podpisem własnym.

Aby odnowić certyfikat z podpisem własnym, wykonaj następujące czynności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do konta usługi Automation i wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Okienko właściwości konta usługi Automation.":::

1. Na stronie właściwości **konta Uruchom** jako wybierz opcję **konto Uruchom jako** lub **klasyczne konto Uruchom jako** , w zależności od tego, którego konta chcesz odnowić certyfikat.

1. Na stronie **Właściwości** wybranego konta wybierz pozycję **odnów certyfikat**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Odnów certyfikat dla konta Uruchom jako.":::

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Przyznawanie uprawnień konta Uruchom jako w innych subskrypcjach

Azure Automation obsługuje użycie jednego konta usługi Automation z jednej subskrypcji i wykonywanie elementów Runbook wobec zasobów Azure Resource Manager w wielu subskrypcjach. Ta konfiguracja nie obsługuje klasycznego modelu wdrażania platformy Azure.

Należy przypisać jednostkę usługi konta Uruchom jako jako rolę [współautor](../role-based-access-control/built-in-roles.md#contributor) w drugiej subskrypcji lub bardziej restrykcyjne uprawnienia. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach](automation-role-based-access-control.md) w Azure Automation. Aby przypisać konto Uruchom jako do roli w innej subskrypcji, konto użytkownika wykonujące to zadanie musi być członkiem roli **właściciela** w tej subskrypcji.

> [!NOTE]
> Ta konfiguracja obsługuje tylko wiele subskrypcji organizacji przy użyciu wspólnej dzierżawy usługi Azure AD.

Przed udzieleniem uprawnień konta Uruchom jako należy najpierw zanotować nazwę wyświetlaną jednostki usługi, która ma zostać przypisana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z poziomu konta usługi Automation wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**.
1. Wybierz pozycję **konto Uruchom jako platformy Azure**.
1. Skopiuj lub Zanotuj wartość w polu **Nazwa wyświetlana** na stronie **konta Uruchom jako platformy Azure** .

Aby uzyskać szczegółowe instrukcje dotyczące dodawania przypisań ról, zapoznaj się z następującymi artykułami w zależności od metody, której chcesz użyć.

* [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
* [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](..//role-based-access-control/role-assignments-rest.md)

Po przypisaniu konta Uruchom jako do roli, w elemencie Runbook Określ, `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` Aby ustawić kontekst subskrypcji do użycia. Aby uzyskać więcej informacji, zobacz [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Ogranicz uprawnienia konta Uruchom jako

Aby kontrolować Określanie wartości docelowej automatyzacji względem zasobów na platformie Azure, można uruchomić skrypt [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) . Ten skrypt zmienia istniejącą nazwę główną usługi konta Uruchom jako, aby utworzyć niestandardową definicję roli i korzystać z niej. Rola ma uprawnienia do wszystkich zasobów, z wyjątkiem [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Po uruchomieniu skryptu **Update-AutomationRunAsAccountRoleAssignments.ps1** elementy Runbook, które uzyskują dostęp do Key Vault za pomocą kont Uruchom jako, nie będą już działać. Przed uruchomieniem skryptu należy przejrzeć elementy Runbook na koncie w celu wywołania Azure Key Vault. Aby umożliwić dostęp do Key Vault z Azure Automation elementów Runbook, należy [dodać konto Uruchom jako do uprawnień Key Vault](#add-permissions-to-key-vault).

Jeśli musisz dodatkowo ograniczyć działanie jednostki usługi Uruchom jako, możesz dodać inne typy zasobów do `NotActions` elementu definicji roli niestandardowej. Poniższy przykład ogranicza dostęp do programu `Microsoft.Compute/*` . W przypadku dodania tego typu zasobu do `NotActions` definicji roli nie będzie można uzyskać dostępu do żadnego zasobu obliczeniowego. Aby dowiedzieć się więcej na temat definicji ról, zobacz [Omówienie definicji ról dla zasobów platformy Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Możesz określić, czy nazwa główna usługi używana przez konto Uruchom jako ma przypisaną rolę **współautor** , czy niestandardową.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do konta usługi Automation i wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.
1. Wybierz pozycję **konto Uruchom jako platformy Azure**.
1. Wybierz **rolę** , aby zlokalizować używaną definicję roli.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Sprawdź rolę konta Uruchom jako." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont usługi Automation. W tym celu należy użyć skryptu [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Możesz zezwolić Azure Automation, aby sprawdzić, czy Key Vault i nazwa główna usługi konta Uruchom jako używają niestandardowej definicji roli. Należy:

* Udziel uprawnień do Key Vault.
* Ustaw zasady dostępu.

Możesz użyć skryptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby udzielić uprawnień konta Uruchom jako do Key Vault. Aby uzyskać więcej informacji na temat ustawiania uprawnień Key Vault, zobacz temat [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z błędami konfiguracji dla kont Uruchom jako

Niektóre elementy konfiguracji niezbędne do uruchomienia jako lub klasycznego konta Uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas konfiguracji początkowej. Możliwe wystąpienia niepozostałej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako zostało usunięte z roli współautor
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich błędnych wystąpień konfiguracji konto usługi Automation wykrywa zmiany i wyświetla stan *niekompletne* w okienku właściwości konta Uruchom jako dla konta.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Niepełna Konfiguracja konta Uruchom jako.":::

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Możesz szybko rozwiązać te problemy dotyczące konta Uruchom jako, [usuwając](delete-run-as-account.md) i [tworząc](create-run-as-account.md) je.

## <a name="next-steps"></a>Następne kroki

* [Obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* [Omówienie certyfikatów dla Cloud Services platformy Azure](../cloud-services/cloud-services-certs-create.md).
* Aby utworzyć lub ponownie utworzyć konto Uruchom jako, zobacz [Tworzenie konta Uruchom jako](create-run-as-account.md).
* Jeśli nie musisz już używać konta Uruchom jako, zobacz [usuwanie konta Uruchom jako](delete-run-as-account.md).