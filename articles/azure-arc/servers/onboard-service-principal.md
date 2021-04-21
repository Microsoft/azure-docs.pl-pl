---
title: Łączenie maszyn hybrydowych z platformą Azure na dużą skalę
description: Z tego artykułu dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu Azure Arc serwerów z obsługą usługi przy użyciu jednostki usługi.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831604"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Łączenie maszyn hybrydowych z platformą Azure na dużą skalę

Możesz włączyć Azure Arc na wielu maszynach z systemem Windows lub Linux w swoim środowisku przy użyciu kilku elastycznych opcji w zależności od wymagań. Za pomocą skryptu szablonu, który zapewniamy, można zautomatyzować każdy krok instalacji, w tym nawiązywanie połączenia z Azure Arc. Musisz jednak interaktywnie wykonać ten skrypt przy użyciu konta z podwyższonym poziomem uprawnień na maszynie docelowej i na platformie Azure.

Aby połączyć maszyny z Azure Arc serwerami z włączoną obsługą usługi, możesz użyć jednostki usługi Azure Active Directory zamiast używać tożsamości uprzywilejowanej do interaktywnego łączenia [maszyny.](onboard-portal.md) [](../../active-directory/develop/app-objects-and-service-principals.md) Jednostką usługi jest specjalna ograniczona tożsamość zarządzania, która ma przyznane tylko minimalne uprawnienia niezbędne do łączenia maszyn z platformą Azure za pomocą `azcmagent` polecenia . Jest to bezpieczniejsze niż używanie konta o wyższym poziomie uprawnień, takiego jak Administrator dzierżawy, i jest zgodna z naszymi najlepszymi rozwiązaniami dotyczącymi zabezpieczeń kontroli dostępu. Jednostki usługi używa się tylko podczas dołączania. Nie jest ona używana do żadnego innego celu.  

Metody instalacji do instalowania i konfigurowania agenta Connected Machine wymagają, aby automatyczna metoda, która jest stosowana, mieć uprawnienia administratora na maszynach. W systemie Linux przy użyciu konta głównego i w systemie Windows jako członek grupy Administratorzy lokalni.

Przed rozpoczęciem zapoznaj się z [](agent-overview.md#prerequisites) wymaganiami wstępnymi i sprawdź, czy Twoja subskrypcja i zasoby spełniają wymagania. Aby uzyskać informacje o obsługiwanych regionach i innych powiązanych zagadnieniach, zobacz [obsługiwane regiony platformy Azure.](overview.md#supported-regions) Zapoznaj się również z [naszym przewodnikiem planowania na](plan-at-scale-deployment.md) dużą skalę, aby poznać kryteria projektowania i wdrażania, a także nasze zalecenia dotyczące zarządzania i monitorowania.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Tworzenie jednostki usługi do dołączania na dużą skalę

Możesz użyć [Azure PowerShell,](/powershell/azure/install-az-ps) aby utworzyć jednostkę usługi za pomocą polecenia cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Możesz też wykonać kroki wymienione w obszarze [Tworzenie jednostki](../../active-directory/develop/howto-create-service-principal-portal.md) usługi przy użyciu Azure Portal, aby wykonać to zadanie.

> [!NOTE]
> Przed utworzeniem jednostki usługi Twoje konto musi być członkiem roli **Właściciel** lub **Administrator** dostępu użytkowników w subskrypcji, której chcesz użyć do dołączania. Jeśli nie masz wystarczających uprawnień do konfigurowania przypisań ról, może zostać utworzona główna usługa, ale nie będzie ona mogła dołączać maszyn.
>

Aby utworzyć jednostkę usługi przy użyciu programu PowerShell, wykonaj następujące kroki.

1. Uruchom następujące polecenie. Dane wyjściowe polecenia cmdlet należy przechowywać w zmiennej lub nie będzie można pobrać hasła potrzebnego [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) w późniejszym kroku.

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

2. Aby pobrać hasło przechowywane w `$sp` zmiennej , uruchom następujące polecenie:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. W danych wyjściowych znajdź wartość password w polu **password i** skopiuj ją. Znajdź również wartość w polu **ApplicationId** i skopiuj ją również. Zapisz je później w bezpiecznym miejscu. Jeśli zapomnisz lub utracisz hasło jednostki usługi, możesz je zresetować przy użyciu [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) polecenia cmdlet .

Wartości z następujących właściwości są używane z parametrami przekazywanymi do `azcmagent` obiektu :

* Wartość właściwości **ApplicationId** jest używana dla wartości `--service-principal-id` parametru
* Wartość właściwości **password jest** używana dla  `--service-principal-secret` parametru używanego do nawiązywania połączenia z agentem.

> [!NOTE]
> Upewnij się, że używasz właściwości **ApplicationId jednostki** usługi, a nie **właściwości Id.**
>

Rola **Azure Connected Machine dołączania** zawiera tylko uprawnienia wymagane do do dołączania maszyny. Możesz przypisać uprawnienie jednostki usługi, aby zezwolić jej zakresowi na dołączoną grupę zasobów lub subskrypcję. Aby dodać przypisanie roli, zobacz [Assign Azure roles using the Azure Portal](../../role-based-access-control/role-assignments-portal.md) or Assign Azure roles using Azure CLI (Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy [Azure).](../../role-based-access-control/role-assignments-cli.md)

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Wygeneruj skrypt instalacji z Azure Portal

Skrypt do automatyzacji pobierania i instalacji oraz nawiązywania połączenia z Azure Arc jest dostępny w Azure Portal. Aby ukończyć ten proces, wykonaj następujące czynności:

1. W przeglądarce przejdź do strony [Azure Portal](https://portal.azure.com).

1. Na stronie **Serwery — Azure Arc** wybierz pozycję **Dodaj** w lewym górnym rogu.

1. Na stronie Wybierz metodę wybierz kafelek **Dodaj wiele serwerów,** **a** następnie wybierz pozycję **Generuj skrypt.**

1. Na **stronie Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszyny. Ta lokalizacja może być taka sama lub inna, co lokalizacja grupy zasobów.

1. Na stronie **Wymagania wstępne** przejrzyj informacje, a następnie wybierz pozycję **Dalej: Szczegóły zasobu.**

1. Na **stronie Szczegóły** zasobu podaj następujące informacje:

    1. Z **listy rozwijanej** Grupa zasobów wybierz grupę zasobów, z których będzie zarządzana maszyna.
    1. Z listy **rozwijanej Region** wybierz region platformy Azure, w których mają być przechowywane metadane serwerów.
    1. Z **listy rozwijanej System** operacyjny wybierz system operacyjny, w przypadku których skrypt jest skonfigurowany do uruchamiania.
    1. Jeśli maszyna komunikuje się za pośrednictwem serwera proxy w celu nawiązania połączenia z Internetem, określ adres IP serwera proxy lub nazwę i numer portu, których maszyna będzie używać do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>` .
    1. Wybierz **pozycję Dalej: Uwierzytelnianie.**

1. Na stronie **Uwierzytelnianie** z listy **rozwijanej jednostki** usługi wybierz pozycję **Arc-for-servers.**  Następnie wybierz pozycję **Dalej: Tagi**.

1. Na stronie **Tagi** przejrzyj  sugerowane domyślne tagi lokalizacji fizycznej i wprowadź wartość lub określ co najmniej jeden tag niestandardowy, **aby** obsługiwać standardy.

1. Wybierz **pozycję Dalej: Pobierz i uruchom skrypt.**

1. Na stronie **Pobieranie i uruchamianie skryptu** przejrzyj informacje podsumowania, a następnie wybierz pozycję **Pobierz.** Jeśli nadal musisz wprowadzić zmiany, wybierz pozycję **Poprzedni**.

W przypadku systemu Windows zostanie wyświetlony monit o zapisanie plików `OnboardingScript.ps1` i dla systemu Linux na `OnboardingScript.sh` komputerze.

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

Korzystając z utworzonego wcześniej szablonu skryptu, można zainstalować i skonfigurować agenta Connected Machine na wielu maszynach hybrydowych z systemami Linux i Windows przy użyciu preferowanego przez organizacje narzędzia do automatyzacji. Skrypt wykonuje podobne kroki opisane w artykule Connect hybrid machines to Azure (Łączenie maszyn hybrydowych z platformą [Azure) Azure Portal](onboard-portal.md) artykułu. Różnica polega na ostatnim kroku, w którym nawiązywane jest połączenie z usługą Azure Arc przy użyciu polecenia `azcmagent` przy użyciu jednostki usługi.

Poniżej przedstawiono ustawienia, które konfigurujesz do użycia dla `azcmagent` jednostki usługi.

* `service-principal-id` : unikatowy identyfikator (GUID), który reprezentuje identyfikator aplikacji jednostki usługi.
* `service-principal-secret` | Hasło jednostki usługi.
* `tenant-id` : unikatowy identyfikator (GUID) reprezentujący dedykowane wystąpienie usługi Azure AD.
* `subscription-id` : identyfikator subskrypcji (GUID) subskrypcji platformy Azure, w której mają być używane maszyny.
* `resource-group` : nazwa grupy zasobów, do której mają należeć połączone maszyny.
* `location`: zobacz [obsługiwane regiony platformy Azure.](overview.md#supported-regions) Ta lokalizacja może być taka sama lub inna, co lokalizacja grupy zasobów.
* `resource-name` :*(Opcjonalnie)* używany do reprezentacji zasobów platformy Azure maszyny lokalnej. Jeśli nie określisz tej wartości, zostanie użyta nazwa hosta maszyny.

Aby dowiedzieć się więcej na temat narzędzia `azcmagent` wiersza polecenia, zapoznaj się z tematem [Azcmagent Reference (Odwołanie azcmagent).](./manage-agent.md)

>[!NOTE]
>Skrypt Windows PowerShell obsługuje tylko uruchamianie z 64-bitowej wersji Windows PowerShell.
>

Po zainstalowaniu agenta i skonfigurowaniu go do nawiązywania połączenia z serwerami z włączoną obsługą Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetl maszyny w witrynie [Azure Portal](https://aka.ms/hybridmachineportal).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

- Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem połączonej maszyny.](troubleshoot-agent-onboard.md)

- Dowiedz się, jak zarządzać maszyną przy użyciu [](../../governance/policy/concepts/guest-configuration.md)usługi [Azure Policy,](../../governance/policy/overview.md)na przykład konfiguracji gościa maszyny wirtualnej, sprawdzić, czy maszyna raportuje do oczekiwanego obszaru roboczego usługi Log Analytics, włączyć monitorowanie za pomocą usługi [Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md)przy użyciu maszyn wirtualnych i wiele innych.

- Dowiedz się więcej o [agencie usługi Log Analytics.](../../azure-monitor/agents/log-analytics-agent.md) Agent usługi Log Analytics dla systemów Windows i Linux jest wymagany, jeśli chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia za pomocą programu Azure Monitor dla maszyn wirtualnych, zarządzać nimi przy użyciu elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich [jak Azure Security Center](../../security-center/security-center-introduction.md).
