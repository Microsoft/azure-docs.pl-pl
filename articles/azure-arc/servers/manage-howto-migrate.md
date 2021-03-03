---
title: Jak migrować serwery z obsługą usługi Azure Arc w różnych regionach
description: Dowiedz się, jak migrować serwer z obsługą usługi Azure ARC z jednego regionu do innego.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650179"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Jak migrować serwery z obsługą usługi Azure Arc w różnych regionach

Istnieją scenariusze, w których należy przenieść istniejący serwer z obsługą usługi Azure ARC z jednego regionu do innego. Na przykład zarejestrowano maszynę w niewłaściwym regionie, aby zwiększyć możliwości zarządzania lub przenieść się z powodów związanych z zarządzaniem.

Aby przeprowadzić migrację serwera z obsługą usługi Azure ARC z jednego regionu platformy Azure do innego, należy odinstalować rozszerzenia maszyn wirtualnych, usunąć zasób na platformie Azure i utworzyć go ponownie w innym regionie. Przed wykonaniem tych kroków należy przeprowadzić inspekcję maszyny w celu sprawdzenia, które rozszerzenia maszyny wirtualnej są zainstalowane.

> [!NOTE]
> Gdy zainstalowane rozszerzenia nadal działają i wykonują ich normalną operację po zakończeniu tej procedury, nie będziesz w stanie zarządzać nimi. W przypadku próby ponownego wdrożenia rozszerzeń na komputerze może wystąpić nieprzewidywalne zachowanie.

## <a name="move-machine-to-other-region"></a>Przenieś maszynę do innego regionu

> [!NOTE]
> Podczas tej operacji powstaje przestój podczas migracji.

1. Usuń rozszerzenia maszyny wirtualnej zainstalowane z [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension)przy użyciu [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)lub [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Użyj narzędzia **azcmagent** z parametrem [Disconnect](manage-agent.md#disconnect) , aby rozłączyć maszynę z usługi Azure Arc i usunąć zasób maszyny z platformy Azure. Odłączanie maszyny od serwerów z obsługą łuku nie powoduje usunięcia agenta połączonej maszyny i nie ma potrzeby usuwania agenta w ramach tego procesu. Można uruchomić to ręcznie podczas logowania interakcyjnego lub zautomatyzować przy użyciu tej samej jednostki usługi, która została użyta do dołączenia wielu agentów lub [tokenu dostępu](../../active-directory/develop/access-tokens.md)platformy tożsamości firmy Microsoft. Jeśli nie korzystasz z jednostki usługi w celu zarejestrowania maszyny przy użyciu serwerów z obsługą usługi Azure ARC, zapoznaj się z poniższym [artykułem](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) , aby utworzyć nazwę główną usługi.

3. Zarejestruj ponownie agenta połączonej maszyny z serwerami z obsługą łuku w innym regionie. Uruchom `azcmagent` Narzędzie z parametrem [Connect](manage-agent.md#connect) , aby wykonać ten krok.

4. Wdróż ponownie rozszerzenia maszyn wirtualnych, które zostały początkowo wdrożone na maszynie z serwerów z włączonym łukiem. Po wdrożeniu agenta Azure Monitor dla maszyn wirtualnych (Insights) lub agenta Log Analytics przy użyciu zasad platformy Azure agenci są wdrażani ponownie po kolejnym [cyklu oceny](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, Włącz monitorowanie za Azure monitor pomocą zasad dotyczących [maszyn wirtualnych](../../azure-monitor/vm/vminsights-enable-policy.md) i wiele innych elementów.