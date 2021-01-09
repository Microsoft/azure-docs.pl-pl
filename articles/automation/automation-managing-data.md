---
title: Zabezpieczenia danych Azure Automation
description: Ten artykuł pomaga dowiedzieć się, jak Azure Automation chroni prywatność i zabezpiecza dane.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: 40405607e7f7198f190f621121022537ac3b3171
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046043"
---
# <a name="management-of-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation

Ten artykuł zawiera kilka tematów wyjaśniających, jak dane są chronione i zabezpieczane w środowisku Azure Automation.

## <a name="tls-12-enforcement-for-azure-automation"></a>Wymuszanie protokołu TLS 1,2 dla Azure Automation

Aby zapewnić bezpieczeństwo danych podczas przesyłania do Azure Automation, zdecydowanie zachęcamy do konfigurowania użycia Transport Layer Security (TLS) 1,2. Poniżej przedstawiono listę metod lub klientów komunikujących się za pośrednictwem protokołu HTTPS z usługą Automation:

* Wywołania elementu webhook

* Hybrydowe procesy robocze elementów Runbook, w tym maszyny zarządzane przez Update Management i Change Tracking i spisu.

* Węzły DSC

Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**. Nie zalecamy jawnego ustawienia agenta do używania protokołu TLS 1,2, chyba że jest to absolutnie konieczne, ponieważ może to spowodować przerwanie funkcji zabezpieczeń na poziomie platformy, które umożliwiają automatyczne wykrywanie i korzystanie z nowszych bezpieczniejszych protokołów, gdy staną się dostępne, takich jak TLS 1,3.

Aby uzyskać informacje o obsłudze protokołu TLS 1,2 z agentem Log Analytics dla systemów Windows i Linux, który jest zależnością roli hybrydowego procesu roboczego elementu Runbook, zobacz [Omówienie agenta log Analytics — TLS 1,2](..//azure-monitor/platform/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Wskazówki dotyczące platformy

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zależą od [OpenSSL](https://www.openssl.org) obsługi TLS 1,2.  | Sprawdź [Dziennik zmian OpenSSL](https://www.openssl.org/news/changelog.html) , aby potwierdzić, że wersja OpenSSL jest obsługiwana.|
| Windows 8,0 — 10 | Obsługiwane i domyślnie włączone. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 — 2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie włączone domyślnie. | Aby uzyskać szczegółowe informacje na temat włączania, zobacz stronę [Ustawienia rejestru Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-retention"></a>Przechowywanie danych

Po usunięciu zasobu w Azure Automation jest on przechowywany przez wiele dni do celów inspekcji przed trwałym usunięciem. Nie można zobaczyć lub użyć zasobu w tym czasie. Te zasady dotyczą także zasobów należących do usuniętego konta usługi Automation.

Poniższa tabela zawiera podsumowanie zasad przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Konto zostanie trwale usunięte 30 dni po usunięciu go przez użytkownika. |
| Elementy zawartości |Zasób zostanie trwale usunięty 30 dni po usunięciu go przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera element zawartości. Zasoby obejmują zmienne, harmonogramy, poświadczenia, certyfikaty, pakiety Python 2 i połączenia. |
| Węzły DSC |Węzeł DSC zostanie trwale usunięty 30 dni po wyrejestrowaniu z konta usługi Automation przy użyciu Azure Portal lub polecenia cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) w programie Windows PowerShell. Węzeł zostanie również trwale usunięty 30 dni po usunięciu konta, które zawiera węzeł. |
| Stanowiska |Zadanie jest usuwane i trwale usuwane po 30 dniach od modyfikacji, na przykład po zakończeniu zadania zostanie zatrzymane lub wstrzymane. |
| Moduły |Moduł zostanie trwale usunięty 30 dni po usunięciu go przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera moduł. |
| Konfiguracje węzłów/pliki MOF |Stara konfiguracja węzła zostanie trwale usunięta 30 dni po wygenerowaniu nowej konfiguracji węzła. |
| Raporty węzła |Raport węzła zostanie trwale usunięty 90 dni po wygenerowaniu nowego raportu dla tego węzła. |
| Elementy Runbook |Element Runbook zostanie trwale usunięty 30 dni od momentu usunięcia zasobu przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera zasób. |

Zasady przechowywania są stosowane dla wszystkich użytkowników i obecnie nie można ich dostosowywać. Jeśli jednak chcesz przechowywać dane przez dłuższy czas, możesz [przesłać Azure Automation dane zadania do dzienników Azure monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Kopia zapasowa danych

Po usunięciu konta usługi Automation na platformie Azure wszystkie obiekty na koncie zostaną usunięte. Obiekty obejmują elementy Runbook, moduły, konfiguracje, ustawienia, zadania i zasoby. Nie można ich odzyskać po usunięciu konta. Poniższe informacje służą do utworzenia kopii zapasowej zawartości konta usługi Automation przed jego usunięciem.

### <a name="runbooks"></a>Elementy Runbook

Elementy Runbook można eksportować do plików skryptów przy użyciu Azure Portal lub polecenia cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) w programie Windows PowerShell. Te pliki skryptów można zaimportować do innego konta usługi Automation, jak opisano w sekcji [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Moduły integracji

Nie można eksportować modułów integracji z Azure Automation. Musisz udostępnić je poza kontem usługi Automation.

### <a name="assets"></a>Elementy zawartości

Nie można eksportować Azure Automation zasobów: certyfikaty, połączenia, poświadczenia, harmonogramy i zmienne. Zamiast tego można użyć Azure Portal i poleceń cmdlet platformy Azure, aby zanotować szczegóły tych zasobów. Następnie te szczegóły umożliwiają utworzenie wszelkich zasobów używanych przez elementy Runbook, które zostały zaimportowane na inne konto usługi Automation.

Nie można pobrać wartości szyfrowanych zmiennych lub pól haseł poświadczeń przy użyciu poleceń cmdlet. Jeśli nie znasz tych wartości, możesz je pobrać w elemencie Runbook. Aby pobrać wartości zmiennych, zobacz [zmienne zasoby w Azure Automation](shared-resources/variables.md). Aby dowiedzieć się więcej o pobieraniu wartości poświadczeń, zobacz [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Konfiguracje DSC

Konfiguracje DSC można wyeksportować do plików skryptów przy użyciu Azure Portal lub polecenia cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) w programie Windows PowerShell. Możesz importować te konfiguracje i używać ich w innym koncie usługi Automation.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w Azure Automation

Replikacja geograficzna jest standardowa w ramach kont Azure Automation. Podczas konfigurowania konta możesz wybrać region podstawowy. Wewnętrzna usługa replikacji geograficznej automatyzacji automatycznie przypisuje region pomocniczy do konta. Następnie usługa ciągle tworzy kopię zapasową danych konta z regionu podstawowego do regionu pomocniczego. Pełną listę regionów podstawowych i pomocniczych można znaleźć w obszarze [ciągłość biznesowa i odzyskiwanie po awarii (BCDR): wielodostępne regiony platformy Azure](../best-practices-availability-paired-regions.md).

Kopia zapasowa utworzona przez usługę replikacji geograficznej automatyzacji to kompletna kopia zasobów usługi Automation, konfiguracji i podobne. Tej kopii zapasowej można użyć, jeśli region podstawowy ulegnie awarii i utraci dane. W prawdopodobnym zdarzeniu, gdy dane dla regionu podstawowego zostaną utracone, firma Microsoft podejmie próbę jego odzyskania. Jeśli firma nie może odzyskać danych podstawowych, używa automatycznej pracy awaryjnej i informuje Cię o sytuacji za pośrednictwem subskrypcji platformy Azure.

Usługa replikacji geograficznej usługi Automation nie jest dostępna bezpośrednio dla klientów zewnętrznych, jeśli wystąpi awaria regionalna. Jeśli chcesz zachować konfigurację automatyzacji i elementy Runbook w trakcie awarii regionalnych:

1. Wybierz region pomocniczy, który ma zostać powiązany z regionem geograficznym podstawowego konta usługi Automation.

2. Utwórz konto usługi Automation w regionie pomocniczym.

3. Na koncie podstawowym wyeksportuj elementy Runbook jako pliki skryptów.

4. Zaimportuj elementy Runbook do konta usługi Automation w regionie pomocniczym.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat zabezpieczania zasobów w Azure Automation, zobacz [szyfrowanie zabezpieczonych zasobów w programie Azure Automation](automation-secure-asset-encryption.md).

* Aby dowiedzieć się więcej na temat replikacji geograficznej, zobacz [Tworzenie i używanie aktywnej replikacji geograficznej](../azure-sql/database/active-geo-replication-overview.md).
