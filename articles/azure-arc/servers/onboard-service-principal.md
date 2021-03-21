---
title: Łączenie maszyn hybrydowych z platformą Azure na dużą skalę
description: W tym artykule dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu serwerów z obsługą usługi Azure ARC przy użyciu nazwy głównej.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175944"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Łączenie maszyn hybrydowych z platformą Azure na dużą skalę

Serwery z obsługą usługi Azure Arc można włączyć dla wielu maszyn z systemem Windows lub Linux w środowisku z kilkoma elastycznymi opcjami, w zależności od wymagań. Za pomocą szablonu skryptu udostępniamy, można zautomatyzować każdy krok instalacji, w tym ustanowienie połączenia z usługą Azure Arc. Jednak użytkownik musi interaktywnie wykonać ten skrypt przy użyciu konta, które ma podwyższone uprawnienia na maszynie docelowej i na platformie Azure.

Aby połączyć maszyny z serwerami z obsługą usługi Azure ARC, można użyć jednostki [usługi](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory zamiast korzystać z tożsamości uprzywilejowanej, aby [interaktywnie połączyć maszynę](onboard-portal.md). Nazwa główna usługi jest specjalną ograniczoną tożsamością zarządzania, która ma przyznane tylko minimalne uprawnienia wymagane do łączenia maszyn z platformą Azure przy użyciu `azcmagent` polecenia. Jest to bezpieczniejsze niż użycie wyższego konta uprzywilejowanego, takiego jak Administrator dzierżawy, i postępuj zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń kontroli dostępu. Nazwa główna usługi jest używana tylko podczas dołączania, nie jest używana do żadnego innego celu.  

Metody instalacji i konfigurowania agenta połączonego maszyny wymagają, aby stosowana Metoda zautomatyzowana miała uprawnienia administratora na maszynach. W systemie Linux przy użyciu konta głównego i w systemie Windows jako członek lokalnej grupy administratorów.

Przed rozpoczęciem należy zapoznać się z wymaganiami [wstępnymi](agent-overview.md#prerequisites) i upewnić się, że Twoja subskrypcja i zasoby spełniają wymagania. Aby uzyskać informacje o obsługiwanych regionach i innych powiązanych zagadnieniach, zobacz [Obsługiwane regiony platformy Azure](overview.md#supported-regions). Zapoznaj się również z naszym [przewodnikiem planowania w skali](plan-at-scale-deployment.md) , aby poznać kryteria projektowania i wdrażania, a także nasze zalecenia dotyczące zarządzania i monitorowania.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Tworzenie jednostki usługi na potrzeby dołączania na dużą skalę

Za pomocą [Azure PowerShell](/powershell/azure/install-az-ps) można utworzyć jednostkę usługi za pomocą polecenia cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . Możesz też wykonać kroki opisane w sekcji [Tworzenie nazwy głównej usługi przy użyciu Azure Portal,](../../active-directory/develop/howto-create-service-principal-portal.md) aby wykonać to zadanie.

> [!NOTE]
> Przed utworzeniem nazwy głównej usługi konto musi być członkiem roli **administratora dostępu** **właściciela** lub użytkownika w subskrypcji, która ma być używana do dołączania. Jeśli nie masz wystarczających uprawnień do konfigurowania przypisań ról, można utworzyć nazwę główną usługi, ale nie będzie ona mogła dołączyć maszyn.
>

Aby utworzyć jednostkę usługi przy użyciu programu PowerShell, wykonaj następujące czynności.

1. Uruchom następujące polecenie. Dane wyjściowe [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) polecenia cmdlet muszą być przechowywane w zmiennej lub nie będzie można pobrać hasła wymaganego w późniejszym kroku.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Aby pobrać hasło przechowywane w `$sp` zmiennej, uruchom następujące polecenie:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. W danych wyjściowych Znajdź wartość hasła w polu **hasło** pola i skopiuj ją. Znajdź również wartość w polu Identyfikator **aplikacji** i skopiuj ją. Zapisz je później w bezpiecznym miejscu. Jeśli zapomnisz lub utracisz hasło nazwy głównej usługi, możesz zresetować ją przy użyciu [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) polecenia cmdlet.

Wartości z następujących właściwości są używane z parametrami przekazaną do `azcmagent` :

* Wartość właściwości identyfikator **aplikacji** jest używana dla `--service-principal-id` wartości parametru
* Wartość właściwości **Password** służy  `--service-principal-secret` do łączenia się z agentem.

> [!NOTE]
> Upewnij się, że używasz **Właściwości głównej nazwy** usługi, a nie właściwości **ID** .
>

Rola **dołączania maszyny połączonej z platformą Azure** zawiera tylko uprawnienia wymagane do dołączenia maszyny. Można przypisać uprawnienia nazwy głównej usługi, aby umożliwić jej zakresowi uwzględnienie grupy zasobów lub subskrypcji. Aby dodać przypisanie roli, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md) lub [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Wygeneruj skrypt instalacji z Azure Portal

Skrypt służący do automatyzowania pobierania i instalacji oraz do nawiązywania połączenia z usługą Azure Arc jest dostępny w Azure Portal. Aby ukończyć ten proces, wykonaj następujące czynności:

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

1. Na stronie **serwery — usługa Azure Arc** wybierz pozycję **Dodaj** w lewym górnym rogu.

1. Na stronie **Wybierz metodę** wybierz kafelek **Dodaj wiele serwerów** , a następnie wybierz pozycję **Generuj skrypt**.

1. Na stronie **Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszyny. Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.

1. Na stronie **wymagania wstępne** Przejrzyj informacje, a następnie wybierz pozycję **Dalej: szczegóły zasobu**.

1. Na stronie **szczegóły zasobu** podaj następujące informacje:

    1. Z listy rozwijanej **Grupa zasobów** wybierz grupę zasobów, z której będzie zarządzana maszyna.
    1. Z listy rozwijanej **region** wybierz region platformy Azure, w którym mają być przechowywane metadane serwerów.
    1. Z listy rozwijanej **system operacyjny** wybierz system operacyjny, na którym ma być uruchomiony skrypt.
    1. Jeśli komputer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, określ adres IP serwera proxy lub nazwę i numer portu, który będzie używany przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>` .
    1. Wybierz pozycję **Dalej: uwierzytelnianie**.

1. Na stronie **uwierzytelnianie** na liście rozwijanej Nazwa **główna usługi** wybierz pozycję **łuk — dla serwerów**.  Następnie wybierz kolejno pozycje, **Dalej: Tagi**.

1. Na stronie **Tagi** Przejrzyj domyślne sugerowane **znaczniki lokalizacji fizycznej** i wprowadź wartość lub Określ jeden lub więcej **tagów niestandardowych** do obsługi Twoich standardów.

1. Wybierz pozycję **Dalej: Pobierz i uruchom skrypt**.

1. Na stronie **Pobierz i uruchom skrypt** Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. Jeśli nadal musisz wprowadzić zmiany, wybierz pozycję **Poprzednia**.

W przypadku systemu Windows zostanie wyświetlony monit o zapisanie `OnboardingScript.ps1` i dla `OnboardingScript.sh` komputera z systemem Linux.

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

Po utworzeniu utworzonego wcześniej szablonu skryptu można zainstalować i skonfigurować agenta podłączonego maszyny na wielu hybrydowych maszynach z systemem Linux i Windows za pomocą preferowanego narzędzia do automatyzacji organizacji. Skrypt wykonuje podobne kroki opisane w artykule [łączenie maszyn hybrydowych z platformą Azure z artykułu Azure Portal](onboard-portal.md) . Różnica jest w ostatnim kroku, w którym można nawiązać połączenie z usługą Azure ARC przy użyciu `azcmagent` polecenia przy użyciu nazwy głównej usługi.

Poniżej przedstawiono ustawienia, które należy skonfigurować dla jednostki `azcmagent` usługi.

* `service-principal-id` : Unikatowy identyfikator (GUID) reprezentujący identyfikator aplikacji nazwy głównej usługi.
* `service-principal-secret` | Hasło nazwy głównej usługi.
* `tenant-id` : Unikatowy identyfikator (GUID) reprezentujący dedykowane wystąpienie usługi Azure AD.
* `subscription-id` : Identyfikator subskrypcji (GUID) subskrypcji platformy Azure, w której mają być używane maszyny.
* `resource-group` : Nazwa grupy zasobów, do której mają należeć połączone maszyny.
* `location` : Zobacz [Obsługiwane regiony platformy Azure](overview.md#supported-regions). Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.
* `resource-name` : (*Opcjonalnie*) używany do reprezentacji zasobów platformy Azure na komputerze lokalnym. Jeśli ta wartość nie zostanie określona, zostanie użyta nazwa hosta maszyny.

Więcej informacji na temat `azcmagent` narzędzia wiersza polecenia można uzyskać, przeglądając [odwołanie Azcmagent](./manage-agent.md).

>[!NOTE]
>Skrypt programu Windows PowerShell obsługuje tylko uruchamianie z 64-bitowej wersji programu Windows PowerShell.
>

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z serwerami z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Wyświetl maszyny w witrynie [Azure Portal](https://aka.ms/hybridmachineportal).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

- Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

- Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzić, czy maszyna jest raportowana do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/vm/vminsights-enable-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia za pomocą Azure Monitor dla maszyn wirtualnych, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).
