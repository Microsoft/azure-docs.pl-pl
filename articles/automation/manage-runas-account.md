---
title: Zarządzanie kontem Azure Automation Uruchom jako
description: W tym artykule opisano sposób zarządzania kontem Uruchom jako przy użyciu programu PowerShell lub z Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e440a27c8f7778c800148feb5bec76ca5a48f4f5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833926"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Zarządzanie kontem Azure Automation Uruchom jako

Konta Uruchom jako w usłudze Azure Automation uwierzytelnianie do zarządzania zasobami w modelu wdrażania Azure Resource Manager lub klasycznym modelu wdrażania platformy Azure przy użyciu elementów Runbook usługi Automation i innych funkcji automatyzacji. Ten artykuł zawiera wskazówki dotyczące zarządzania kontem Uruchom jako lub klasycznym kontem Uruchom jako.

Aby dowiedzieć się więcej o Azure Automation uwierzytelniania konta i wskazówki dotyczące scenariuszy automatyzacji procesów, zobacz [Omówienie uwierzytelniania konta usługi Automation.](automation-security-overview.md)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Odnawianie certyfikatu z podpisem własnym

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa rok od daty utworzenia. W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Możesz go odnowić w dowolnym momencie, zanim wygaśnie.

W przypadku odnawiania certyfikatu z podpisem własnym bieżący prawidłowy certyfikat jest zachowywany, aby upewnić się, że nie ma to negatywnego wpływu na wszystkie przechowywane w kolejce lub aktywnie działające i uwierzytelnione przy użyciu konta Uruchom jako. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

>[!NOTE]
>Jeśli uważasz, że bezpieczeństwo konta Uruchom jako zostało naruszone, możesz usunąć i ponownie utworzyć certyfikat z podpisem własnym.

>[!NOTE]
>Jeśli konto Uruchom jako skonfigurowano do używania certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa i używasz opcji odnowienia certyfikatu z podpisem własnym, certyfikat przedsiębiorstwa zostanie zastąpiony certyfikatem z podpisem własnym.

Aby odnowić certyfikat z podpisem własnym, należy wykonać następujące czynności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do konta usługi Automation i wybierz **pozycję Konta Uruchom** jako w sekcji ustawień konta.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Okienko właściwości konta usługi Automation.":::

1. Na stronie **właściwości Konta Uruchom**  jako wybierz  pozycję Konto Uruchom jako lub Klasyczne konto Uruchom jako w zależności od konta, dla którego chcesz odnowić certyfikat.

1. Na stronie **Właściwości** dla wybranego konta wybierz pozycję **Odnów certyfikat**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Odnów certyfikat dla konta Uruchom jako.":::

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Udzielanie uprawnień konta Uruchom jako w innych subskrypcjach

Azure Automation obsługuje korzystanie z jednego konta usługi Automation z jednej subskrypcji i wykonywanie elementów Runbook Azure Resource Manager zasobów w wielu subskrypcjach. Ta konfiguracja nie obsługuje klasycznego modelu wdrażania platformy Azure.

Do jednostki usługi konta Uruchom jako należy przypisać [rolę Współautor](../role-based-access-control/built-in-roles.md#contributor) w innej subskrypcji lub bardziej restrykcyjne uprawnienia. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach](automation-role-based-access-control.md) w Azure Automation. Aby przypisać konto Uruchom jako do roli w innej subskrypcji, konto użytkownika wykonujące to zadanie musi być członkiem roli **Właściciel** w tej subskrypcji.

> [!NOTE]
> Ta konfiguracja obsługuje tylko wiele subskrypcji organizacji korzystających ze wspólnej dzierżawy usługi Azure AD.

Przed udzieleniem uprawnień konta Uruchom jako należy najpierw zanotować nazwę wyświetlaną jednostki usługi do przypisania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z konta usługi Automation wybierz pozycję **Konta Uruchom jako w** obszarze Ustawienia **konta.**
1. Wybierz **pozycję Konto Uruchom jako platformy Azure.**
1. Skopiuj lub zanotuj wartość **nazwa wyświetlana** na **stronie Konto Uruchom jako platformy Azure.**

Aby uzyskać szczegółowe instrukcje dotyczące dodawania przypisań ról, zapoznaj się z następującymi artykułami w zależności od metody, której chcesz użyć.

* [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
* [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](..//role-based-access-control/role-assignments-rest.md)

Po przypisaniu konta Uruchom jako do roli w swoim podręczniku Runbook określ, `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` aby ustawić kontekst subskrypcji do użycia. Aby uzyskać więcej informacji, zobacz [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Ograniczanie uprawnień konta Uruchom jako

Aby kontrolować kierowanie usługi Automation do zasobów na platformie Azure, możesz uruchomić [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) skrypt. Ten skrypt zmienia istniejącą jednostkę usługi konta Uruchom jako, aby utworzyć niestandardową definicję roli i używać jej. Rola ma uprawnienia do wszystkich zasobów z wyjątkiem [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Po uruchomieniu skryptu **Update-AutomationRunAsAccountRoleAssignments.ps1** runbook, które Key Vault dostęp za pośrednictwem kont Uruchom jako, nie będą już działać. Przed uruchomieniem skryptu zapoznaj się z tematem Runbook na swoim koncie, aby uzyskać wywołania Azure Key Vault. Aby włączyć dostęp do Key Vault z Azure Automation runbook, musisz dodać konto Uruchom jako do [Key Vault uprawnień użytkownika](#add-permissions-to-key-vault).

Jeśli musisz dodatkowo ograniczyć możliwości jednostki usługi Uruchom jako, możesz dodać inne typy zasobów do `NotActions` elementu definicji roli niestandardowej. Poniższy przykład ogranicza dostęp do `Microsoft.Compute/*` . Jeśli dodasz ten typ zasobu do definicji roli, rola nie będzie mogła uzyskać dostępu `NotActions` do żadnego zasobu obliczeniowego. Aby dowiedzieć się więcej na temat definicji ról, zobacz [Understand role definitions for Azure resources (Informacje o definicjach ról dla zasobów platformy Azure).](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Możesz określić, czy do jednostki usługi używanej przez konto Uruchom jako przypisano rolę **Współautor,** czy rolę niestandardową.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do konta usługi Automation i wybierz **pozycję Konta Uruchom** jako w sekcji ustawień konta.
1. Wybierz **pozycję Konto Uruchom jako platformy Azure.**
1. Wybierz **pozycję Rola,** aby zlokalizować używaną definicję roli.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Sprawdź rolę konta Uruchom jako." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont usługi Automation. W tym celu należy [ użyćCheck-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) skryptu w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Możesz zezwolić Azure Automation, czy Key Vault i twoja główna usługa konta Uruchom jako korzysta z niestandardowej definicji roli. Musisz:

* Przyznaj uprawnienia Key Vault.
* Ustaw zasady dostępu.

Możesz użyć skryptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby udzielić kontu Uruchom jako uprawnień do Key Vault. Zobacz [Przypisywanie Key Vault dostępu do aplikacji,](../key-vault/general/assign-access-policy-powershell.md) aby uzyskać więcej informacji na temat ustawiania uprawnień Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z błędną konfiguracją kont Uruchom jako

Niektóre elementy konfiguracji niezbędne dla konta Uruchom jako lub klasycznego konta Uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas początkowej konfiguracji. Możliwe wystąpienia błędnej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako usunięte z roli Współautor
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich wystąpień błędnej konfiguracji konto usługi Automation wykrywa  zmiany i wyświetla stan Niekompletne w okienku właściwości Konta Uruchom jako dla konta.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Niekompletna konfiguracja konta Uruchom jako.":::

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Te problemy z kontem Uruchom jako można szybko rozwiązać, [usuwając](delete-run-as-account.md) i [ponownie](create-run-as-account.md) tworząc konto Uruchom jako.

## <a name="next-steps"></a>Następne kroki

* [Obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* [Omówienie certyfikatów dla Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Aby utworzyć lub ponownie utworzyć konto Uruchom jako, zobacz [Tworzenie konta Uruchom jako.](create-run-as-account.md)
* Jeśli nie musisz już używać konta Uruchom jako, zobacz [Usuwanie konta Uruchom jako.](delete-run-as-account.md)
