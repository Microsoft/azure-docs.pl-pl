---
title: Azure Automation zabezpieczeń danych
description: Ten artykuł pomaga dowiedzieć się, Azure Automation chroni twoją prywatność i zabezpiecza dane.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833530"
---
# <a name="management-of-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation

Ten artykuł zawiera kilka tematów wyjaśniających, jak dane są chronione i chronione w Azure Automation środowisku.

## <a name="tls-12-enforcement-for-azure-automation"></a>Wymuszanie TLS 1.2 dla Azure Automation

Aby zapewnić bezpieczeństwo danych podczas przesyłania do Azure Automation, zdecydowanie zachęcamy do skonfigurowania korzystania z Transport Layer Security (TLS) 1.2. Poniżej przedstawiono listę metod lub klientów, którzy komunikują się z usługą Automation za pośrednictwem protokołu HTTPS:

* Wywołania webhook

* Hybrydowe pracownicy runbook, które obejmują maszyny zarządzane przez Update Management i Śledzenie zmian i spis.

* Węzły DSC

Starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są podatne na zagrożenia i chociaż obecnie działają w celu zapewnienia zgodności z poprzednimi wersjami, nie **są zalecane.** Nie zaleca się jawnego ustawiania agenta do używania protokołu TLS 1.2 tylko wtedy, gdy jest to konieczne, ponieważ może ono przerwać funkcje zabezpieczeń na poziomie platformy, które umożliwiają automatyczne wykrywanie i korzystanie z bardziej bezpiecznych protokołów w miarę ich używania, takich jak TLS 1.3.

Aby uzyskać informacje na temat obsługi TLS 1.2 przy użyciu agenta usługi Log Analytics dla systemów Windows i Linux, które są zależnością roli hybrydowego procesu roboczego runbook, zobacz Omówienie agenta usługi Log Analytics — [TLS 1.2.](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)

### <a name="platform-specific-guidance"></a>Wskazówki dotyczące poszczególnych platform

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zwykle korzystają z [openssl](https://www.openssl.org) do obsługi TLS 1.2.  | Sprawdź [listę zmian OpenSSL,](https://www.openssl.org/news/changelog.html) aby potwierdzić, że twoja wersja programu OpenSSL jest obsługiwana.|
| Windows 8.0–10 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz ustawień [domyślnych.](/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2012–2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz ustawień [domyślnych](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale domyślnie niewłączona. | Szczegółowe informacje na temat włączania można znaleźć na stronie ustawień rejestru Transport Layer Security [(TLS).](/windows-server/security/tls/tls-registry-settings)  |

## <a name="data-retention"></a>Przechowywanie danych

Po usunięciu zasobu w Azure Automation jest on zachowywany przez wiele dni na potrzeby inspekcji przed trwałym usunięciem. W tym czasie nie można wyświetlić zasobu ani go używać. Te zasady dotyczą również zasobów należących do usuniętego konta usługi Automation. Zasady przechowywania dotyczą wszystkich użytkowników i obecnie nie można ich dostosowywać. Jeśli jednak musisz przechowywać dane przez dłuższy czas, możesz przesyłać dalej dane zadania Azure Automation do [dzienników Azure Monitor .](automation-manage-send-joblogs-log-analytics.md)

W poniższej tabeli podsumowano zasady przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Konto zostanie trwale usunięte 30 dni po jego usunięciu przez użytkownika. |
| Elementy zawartości |Zasób zostanie trwale usunięty 30 dni po jego usunięciu przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera zasób. Zasoby obejmują zmienne, harmonogramy, poświadczenia, certyfikaty, pakiety języka Python 2 i połączenia. |
| Węzły DSC |Węzeł DSC zostanie trwale usunięty po 30 dniach od wyrejestrowania z konta usługi Automation przy użyciu programu Azure Portal lub polecenia cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) w usłudze Windows PowerShell. Węzeł jest również trwale usuwany 30 dni po usunięciu przez użytkownika konta, które zawiera węzeł. |
| Stanowiska |Zadanie jest usuwane i trwale usuwane 30 dni po modyfikacji, na przykład po zakończeniu zadania, zatrzymaniu lub wstrzymaniu. |
| Moduły |Moduł jest trwale usuwany 30 dni po jego usunięciu przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera moduł. |
| Konfiguracje węzłów/pliki MOF |Stara konfiguracja węzła jest trwale usuwana 30 dni po wygenerowaniu nowej konfiguracji węzła. |
| Raporty dotyczące węzłów |Raport węzła jest trwale usuwany 90 dni po wygenerowaniu nowego raportu dla tego węzła. |
| Elementy Runbook |Element Runbook jest trwale usuwany 30 dni po usunięciu zasobu przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, które zawiera zasób<sup>1.</sup> |

<sup>1</sup> Element Runbook można odzyskać w 30-dniowym oknie, zgłoszenia zdarzenia pomocy technicznej platformy Azure do pomocy Microsoft Azure technicznej. Przejdź do witryny [pomocy technicznej platformy Azure i](https://azure.microsoft.com/support/options/) wybierz pozycję Prześlij **wniosek o pomoc techniczną.**

## <a name="data-backup"></a>Kopia zapasowa danych

Usunięcie konta usługi Automation na platformie Azure spowoduje usunięcie wszystkich obiektów na koncie. Obiekty obejmują runbook, moduły, konfiguracje, ustawienia, zadania i zasoby. Nie można ich odzyskać po usunięciu konta. Poniższe informacje mogą pomóc w kopii zapasowej zawartości konta usługi Automation przed jej usunięciem.

### <a name="runbooks"></a>Elementy Runbook

Możesz wyeksportować swoje runbook do plików skryptów przy użyciu polecenia Azure Portal lub polecenia cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) w Windows PowerShell. Te pliki skryptów można zaimportować do innego konta usługi Automation, zgodnie z omówieniem w artykule Manage runbook in Azure Automation (Zarządzanie plikami [Runbook w programie Azure Automation).](manage-runbooks.md)

### <a name="integration-modules"></a>Moduły integracji

Nie można eksportować modułów integracji z Azure Automation, muszą one zostać udostępnione poza kontem usługi Automation.

### <a name="assets"></a>Elementy zawartości

Nie można eksportować zasobów Azure Automation: certyfikatów, połączeń, poświadczeń, harmonogramów i zmiennych. Zamiast tego można użyć polecenia cmdlet Azure Portal azure, aby zanotować szczegóły tych zasobów. Następnie użyj tych szczegółów, aby utworzyć wszystkie zasoby, które są używane przez importowane do innego konta usługi Automation zasoby.

Nie można pobrać wartości zaszyfrowanych zmiennych ani pól haseł poświadczeń przy użyciu polecenia cmdlet. Jeśli nie znasz tych wartości, możesz je pobrać w programie Runbook. Aby uzyskać informacje na temat pobierania wartości zmiennych, zobacz [Zasoby zmiennych w Azure Automation](shared-resources/variables.md). Aby dowiedzieć się więcej na temat pobierania wartości poświadczeń, zobacz [Zasoby poświadczeń](shared-resources/credentials.md)w Azure Automation .

### <a name="dsc-configurations"></a>Konfiguracje DSC

Konfiguracje DSC można wyeksportować do plików skryptów przy użyciu polecenia Azure Portal lub polecenia cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) w Windows PowerShell. Te konfiguracje można importować i używać na innym koncie usługi Automation.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w Azure Automation

Replikacja geograficzna jest standardowa w Azure Automation kont. Podczas konfigurowania konta wybierasz region podstawowy. Wewnętrzna usługa replikacji geograficznej usługi Automation automatycznie przypisuje region pomocniczy do konta. Usługa następnie stale kopiuje kopię zapasową danych konta z regionu podstawowego do regionu pomocniczego. Pełną listę regionów podstawowych i pomocniczych można znaleźć na stronie [Business continuity and disaster recovery (BCDR): Azure Paired Regions (Ciągłość](../best-practices-availability-paired-regions.md)działalności biznesowej i odzyskiwanie po awarii ( BCDR): Regiony sparowane platformy Azure).

Kopia zapasowa utworzona przez usługę replikacji geograficznej usługi Automation jest pełną kopią zasobów usługi Automation, konfiguracji i podobnych. Tej kopii zapasowej można użyć, jeśli region podstawowy nie działa i utraci dane. W mało prawdopodobnym przypadku, gdy dane dla regionu podstawowego utracą dane, firma Microsoft spróbuje je odzyskać.

> [!NOTE]
> Azure Automation dane klientów są przechowywane w regionie wybranym przez klienta. Na potrzeby bcdr we wszystkich regionach z wyjątkiem Brazylii Południowej i Azji Południowo-Wschodniej dane Azure Automation są przechowywane w innym regionie (sparowany region platformy Azure). Tylko w przypadku regionu Brazylia Południowa (Stan Sao Paulo) w Brazylii i regionu Azja Południowo-Wschodnia (Singapur) obszaru geograficznego regionu Azja i Pacyfik dane z regionu Azure Automation są przechowywane w tym samym regionie, aby spełnić wymagania dotyczące rezydencji danych dla tych regionów.

Usługa replikacji geograficznej usługi Automation nie jest dostępna bezpośrednio dla klientów zewnętrznych w przypadku awarii regionalnej. Jeśli chcesz zachować konfigurację usługi Automation i jej runbook podczas awarii regionalnych:

1. Wybierz region pomocniczy do sparowania z regionem geograficznym podstawowego konta usługi Automation.

2. Utwórz konto usługi Automation w regionie pomocniczym.

3. Na koncie podstawowym wyeksportuj swoje runbook jako pliki skryptów.

4. Zaimportuj je na konto usługi Automation w regionie pomocniczym.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bezpiecznych zasobów w Azure Automation, zobacz Encryption of secure assets in Azure Automation (Szyfrowanie zabezpieczonych zasobów w [programie Azure Automation).](automation-secure-asset-encryption.md)

* Aby dowiedzieć się więcej na temat replikacji geograficznej, zobacz Creating and using active geo-replication (Tworzenie [i używanie aktywnej replikacji geograficznej).](../azure-sql/database/active-geo-replication-overview.md)
