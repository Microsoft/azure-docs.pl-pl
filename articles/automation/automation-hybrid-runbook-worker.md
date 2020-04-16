---
title: Azure Automation — hybrydowy proces roboczy elementu Runbook
description: Ten artykuł zawiera informacje dotyczące instalowania i używania hybrydowego procesu roboczego systemu runbook, który jest funkcją usługi Azure Automation, której można używać do uruchamiania elementów runbook na komputerach w lokalnym centrum danych lub dostawcy chmury.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1e4470ce5ac69390cf8d361577b9ebf0013e4e51
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405786"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatyzuj zasoby w centrum danych lub w chmurze przy użyciu hybrydowego środowiska roboczego żyli

Elementy runbook w usłudze Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ są uruchamiane na platformie chmurowej platformy Azure. Za pomocą funkcji Hybrydowy proces roboczy elementu runbook usługi Azure Automation można uruchamiać elementy runbook bezpośrednio na komputerze obsługującym rolę i przeciwko zasobom w środowisku do zarządzania tymi zasobami lokalnymi. Elementy runbook są przechowywane i zarządzane w usłudze Azure Automation, a następnie dostarczane do jednego lub więcej przypisanych komputerów.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Każdy hybrydowy proces roboczy elementu runbook jest członkiem grupy hybrydowego elementu roboczego elementu runbook, który określisz podczas instalowania agenta. Grupa może zawierać jednego agenta, ale można zainstalować wielu agentów w grupie dla wysokiej dostępności. Każdy komputer może obsługiwać jeden hybrydowy proces roboczy raportowania do jednego konta automatyzacji.

Po uruchomieniu śmiętu w hybrydowym uzyciu roboczym niuszka należy określić grupę, na której działa. Każdy pracownik w grupie sonduje usługę Azure Automation, aby sprawdzić, czy są dostępne zadania. Jeśli zadanie jest dostępne, pierwszy pracownik, który otrzymuje zadanie, przyjmuje go. Czas przetwarzania kolejki zadań zależy od profilu sprzętu i obciążenia sprzętu procesu hybrydowego. Nie można określić określonego pracownika. Hybrydowe procesy pracy y uruchomieniu nie mają wielu ograniczeń, które mają piaskownice platformy Azure. Nie mają tych samych limitów miejsca na dysku, pamięci lub gniazd sieciowych. Hybrydowe procesy robocze uruchomieniu są ograniczone tylko przez zasoby w samym udziale procesu roboczego hybrydowego. Ponadto hybrydowe procesy pracy uruchomieniu nie współużytkują 180-minutowy limit czasu [udziału w programie sprawiedliwym,](automation-runbook-execution.md#fair-share) który mają piaskownice platformy Azure. Aby dowiedzieć się więcej o limitach usług dla skrzynek izolowania platformy Azure i hybrydowych workers y uruchomieniu, zobacz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)zadań .

## <a name="install-a-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego uruchomieniu

Proces instalowania hybrydowego procesu roboczego systemu runbook zależy od systemu operacyjnego. Poniższa tabela definiuje typy wdrożeń.

|System operacyjny  |Typy wdrożeniów  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ręcznie](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

Zalecaną metodą instalacji jest użycie systemu runbook automatyzacji w celu całkowitego zautomatyzowania procesu konfigurowania komputera z systemem Windows. Drugą metodą jest wykonać procedurę krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku komputerów z systemem Linux uruchom skrypt Języka Python, aby zainstalować agenta na komputerze.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego systemu runbook z konfiguracją żądanego stanu (DSC), należy dodać serwery jako węzły DSC. Aby uzyskać więcej informacji na temat dołączania ich do zarządzania za pomocą dsc, zobacz [Maszyny dołączania do zarządzania przez usługę Azure Automation DSC](automation-dsc-onboarding.md).
>
>Jeśli [włączysz rozwiązanie do zarządzania aktualizacjami,](automation-update-management.md)każdy komputer połączony z obszarem roboczym usługi Azure Log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy do obsługi śmięty umnień zawartych w tym rozwiązaniu. Jednak komputer nie jest zarejestrowany w żadnych grupach hybrydowych procesów roboczych zdefiniowanych już na koncie automatyzacji. Komputer można dodać do grupy hybrydowego pracownika żyła na koncie automatyzacji, aby obsługiwać elementy runbook automatyzacji, o ile używasz tego samego konta zarówno dla rozwiązania, jak i członkostwa w grupie hybrydowego systemu runbook. Ta funkcja została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego uruchomieniu. .

Przejrzyj [informacje dotyczące planowania sieci](#network-planning) przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu runbook. Po pomyślnym wdrożeniu procesu roboczego przejrzyj [uruchom runbook na hybrydowym układzie roboczym żyli,](automation-hrw-run-runbooks.md) aby dowiedzieć się, jak skonfigurować programy runbook do automatyzacji procesów w lokalnym centrum danych lub innym środowisku chmury.

Komputer można dodać do grupy hybrydowego pracownika żyła na koncie automatyzacji, aby obsługiwać elementy runbook automatyzacji, o ile używasz tego samego konta zarówno dla rozwiązania, jak i członkostwa w grupie hybrydowego systemu runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Usuwanie hybrydowego procesu roboczego umniejszania z komputera lokalnego

Proces roboczy hybrydowego systemu runbook można usunąć z komputera lokalnego zgodnie z opisem w tej sekcji dla systemów Windows i Linux.

### <a name="remove-the-worker-on-windows"></a>Usuwanie pracownika w systemie Windows

1. W witrynie Azure portal przejdź do konta automatyzacji.
2. W obszarze **Ustawienia konta**wybierz pozycję **Klucze** i zanotuj wartości **adresu URL** i **podstawowego klucza dostępu**.

3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie z adresem URL i podstawowymi wartościami klucza dostępu. Użyj `Verbose` parametru do szczegółowego dziennika procesu usuwania. Aby usunąć przestarzałe maszyny z `machineName` grupy hybrydowy proces roboczy, należy użyć parametru opcjonalnego.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Usuń pracownika na Linuksie

Można użyć polecenia `ls /var/opt/microsoft/omsagent` w hybrydowym uzywce procesu roboczego żyli, aby uzyskać identyfikator obszaru roboczego. Tworzony jest folder o nazwie o identyfikatorze obszaru roboczego.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten kod nie usuwa agenta monitorowania firmy Microsoft z komputera. Usuwa tylko funkcje i konfigurację roli Hybrydowy proces roboczy podręcznika.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowego elementu roboczego elementu runbook, należy najpierw usunąć hybrydowy proces roboczy elementu runbook z każdego komputera, który jest członkiem grupy. Następnie usuń grupę, należy wykonać następujące czynności:

1. Otwórz konto automatyzacji w witrynie Azure portal.
2. Wybierz **hybrydowe grupy robocze** w obszarze **Automatyzacja procesów**. Zaznacz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stronie właściwości dla wybranej grupy wybierz pozycję **Usuń**. Komunikat z prośbą o potwierdzenie tej akcji. Wybierz **opcję Tak,** jeśli masz pewność, że chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ten proces może potrwać kilka sekund, aby zakończyć. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="configure-your-network"></a><a name="network-planning"></a>Konfigurowanie sieci

### <a name="hybrid-worker-role"></a>Rola hybrydowego pracownika

Aby proces roboczy hybrydowego systemu runbook mógł się połączyć z usługą Azure Automation i zarejestrować się w niej, musi mieć dostęp do numeru portu i adresów URL opisanych w tej sekcji. Ten dostęp jest na górze do [portów i adresów URL wymaganych dla programu Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) do łączenia się z dziennikami usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli używasz serwera proxy do komunikacji między agentem a usługą Azure Automation, upewnij się, że odpowiednie zasoby są dostępne. Limit czasu dla żądań z hybrydowego procesu roboczego systemu runbook i usług automatyzacji wynosi 30 sekund. Po 3 próbach żądanie zakończy się niepowodzeniem. Jeśli używasz zapory do ograniczania dostępu do Internetu, musisz skonfigurować zaporę, aby zezwoliła na dostęp. Jeśli używasz bramy usługi Log Analytics jako serwera proxy, upewnij się, że jest skonfigurowany dla pracowników hybrydowych. Aby uzyskać instrukcje dotyczące tego, jak to zrobić, zobacz [Konfigurowanie bramy usługi Log Analytics dla hybrydowych procesów pracy automatyzacji.](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)

Do komunikowania się z automatyzacją wymagany jest następujący port i adresy URL:

* Port: Tylko TCP 443 jest wymagany do wychodzącego dostępu do Internetu.
* Globalny adres URL: *.azure-automation.net
* Globalny adres URL w regionie US Gov Wirginia: *.azure-automation.us
* Usługa agenta: https://\<obszar\>roboczyId .agentsvc.azure-automation.net

Zaleca się używanie adresów wymienionych podczas definiowania wyjątków. W przypadku adresów IP można pobrać [zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto automatyzacji, które jest zdefiniowane dla określonego regionu, można ograniczyć komunikację do tego regionalnego centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Południowo-środkowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Zachodnie stany USA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Środkowa |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australia Wschodnia |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Aby uzyskać listę adresów IP regionu zamiast nazw regionów, pobierz plik XML [adresu IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) z Centrum pobierania Firmy Microsoft.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera listę zakresów adresów IP, które są używane w centrach danych platformy Microsoft Azure. Plik zawiera zakresy obliczeniowe, SQL i magazynowe.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień.
>
> Warto pobrać nowy plik XML co tydzień. Następnie zaktualizuj witrynę, aby poprawnie identyfikować usługi uruchomione na platformie Azure. Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizowania reklamy usługi Azure (Border Gateway Protocol) w pierwszym tygodniu każdego miesiąca.

### <a name="update-management"></a>Zarządzanie aktualizacjami

Oprócz standardowych adresów i portów wymaganych przez hybrydowy proces roboczy systemu runbook następujące adresy są wymagane specjalnie dla zarządzania aktualizacjami. Komunikacja z tymi adresami odbywa się za 443 portem.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować programy runbook do automatyzacji procesów w lokalnym centrum danych lub innym środowisku chmury, zobacz [Runbooks w hybrydowym usłudze Runbook Worker](automation-hrw-run-runbooks.md).
* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi pracownikami wiązki [uruchomieniu, zobacz Rozwiązywanie problemów z hybrydowymi pracownikami wiązki uruchomieniu](troubleshoot/hybrid-runbook-worker.md#general). .