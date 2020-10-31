---
title: Łączenie maszyn hybrydowych z platformą Azure z poziomu Centrum administracyjnego systemu Windows
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszyny z platformą Azure przy użyciu serwerów z obsługą usługi Azure ARC z poziomu Centrum administracyjnego systemu Windows.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: d47e3dd4434d719f890b64e4cdfb12a189c0632a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133708"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Łączenie maszyn hybrydowych z platformą Azure z poziomu Centrum administracyjnego systemu Windows

Możesz włączyć serwery z obsługą usługi Azure ARC dla co najmniej jednej maszyny z systemem Windows w środowisku, wykonując ręcznie zestaw kroków. Można też użyć [Centrum administracyjnego systemu Windows](/windows-server/manage/windows-admin-center/understand/what-is) do wdrożenia agenta połączonej maszyny i zarejestrować serwery lokalne bez konieczności wykonywania jakichkolwiek kroków poza tym narzędziem.

## <a name="prerequisites"></a>Wymagania wstępne

* Serwery z włączonymi środkami Arc — Sprawdź wymagania [wstępne](agent-overview.md#prerequisites) i sprawdź, czy Twoja subskrypcja, Twoje konto platformy Azure i zasoby spełniają wymagania.

* Centrum administracyjne systemu Windows — Zapoznaj się z wymaganiami dotyczącymi [przygotowania środowiska](/windows-server/manage/windows-admin-center/deploy/prepare-environment) do wdrożenia i [skonfigurowania integracji z platformą Azure ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Docelowe serwery z systemem Windows, którymi chcesz zarządzać, muszą mieć łączność z Internetem, aby uzyskać dostęp do platformy Azure.

### <a name="security"></a>Zabezpieczenia

Ta metoda wdrażania wymaga uprawnień administratora na docelowym komputerze lub serwerze z systemem Windows w celu zainstalowania i skonfigurowania agenta. Musisz być również członkiem roli [**Użytkownicy bramy**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Wdróż

Wykonaj następujące kroki, aby skonfigurować serwer z systemem Windows przy użyciu serwerów z włączonym łukiem.

1. Zaloguj się do centrum administracyjnego systemu Windows.

1. Z listy połączenie na stronie **Przegląd** na liście podłączonych serwerów z systemem Windows wybierz serwer z listy, aby nawiązać z nim połączenie.

1. W okienku po lewej stronie wybierz pozycję **usługi hybrydowe platformy Azure** .

1. Na stronie **usługi hybrydowe platformy Azure** wybierz pozycję **odkryj usługi platformy Azure** .

1. Na stronie **odnajdywanie usług platformy Azure** w obszarze wykorzystywanie **zasad i rozwiązań platformy Azure do zarządzania serwerami za pomocą usługi Azure Arc** wybierz pozycję **Konfiguruj** .

1. Na stronie **Settings\Azure ARC dla serwerów** , jeśli zostanie wyświetlony monit uwierzytelnianie na platformie Azure, a następnie wybierz pozycję **Rozpocznij pracę** .

1. Na stronie **Połącz serwer z platformą Azure** podaj następujące elementy:

    1. Z listy rozwijanej **subskrypcja platformy Azure** wybierz subskrypcję platformy Azure.
    1. W obszarze **Grupa zasobów** wybierz pozycję **Nowy** , aby utworzyć nową grupę zasobów, lub na liście rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów, z której chcesz zarejestrować maszynę i zarządzać nią.
    1. Z listy rozwijanej **region** wybierz region platformy Azure, w którym mają być przechowywane metadane serwerów.
    1. Jeśli komputer lub serwer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, wybierz opcję **Użyj serwera proxy** . Określ adres IP lub nazwę serwera proxy oraz numer portu, który będzie używany przez maszynę do komunikacji z serwerem proxy.

1. Wybierz pozycję **Konfiguruj** , aby kontynuować konfigurowanie serwera z systemem Windows przy użyciu usługi Azure ARC z obsługą serwerów.

System Windows Server będzie łączył się z platformą Azure, pobierz agenta podłączonej maszyny, zainstaluj go i zarejestruj na serwerach z obsługą usługi Azure Arc. Aby śledzić postęp, wybierz pozycję **powiadomienia** w menu.

Aby potwierdzić instalację agenta podłączonego komputera, w centrum administracyjnym systemu Windows wybierz pozycję [**zdarzenia**](/windows-server/manage/windows-admin-center/use/manage-servers#events) z okienka po lewej stronie, aby przejrzeć zdarzenia *MsiInstaller* w dzienniku zdarzeń aplikacji.

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z serwerami z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Zapoznaj się z maszyną w [Azure Portal](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Pomyślne połączenie z maszyną" border="false":::

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-intro.md).