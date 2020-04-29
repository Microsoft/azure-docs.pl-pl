---
title: Azure Automation — hybrydowy proces roboczy elementu Runbook
description: Ten artykuł zawiera informacje na temat instalowania i używania hybrydowego procesu roboczego elementu Runbook, który jest funkcją Azure Automation, której można użyć do uruchamiania elementów Runbook na maszynach w lokalnym dostawcy centrum danych lub w chmurze.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 861c7ffa647c8d2f37b32c359253ca991eeb314f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457709"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook

Elementy Runbook w programie Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ działają na platformie Azure w chmurze. Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego przypisanego komputera.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowych procesów roboczych elementu Runbook, która została określona podczas instalacji agenta. Grupa może zawierać jednego agenta, ale w celu zapewnienia wysokiej dostępności można zainstalować wielu agentów w grupie. Każdy komputer może obsługiwać jedno zgłoszenie hybrydowego procesu roboczego na jedno konto usługi Automation.

Po uruchomieniu elementu Runbook w hybrydowym procesie roboczym elementu Runbook należy określić grupę, w której ma ona działać. Każdy proces roboczy w grupie sonduje Azure Automation, aby sprawdzić, czy jakieś zadania są dostępne. Jeśli zadanie jest dostępne, pierwszy proces roboczy, aby pobrać to zadanie. Czas przetwarzania kolejki zadań zależy od profilu sprzętu hybrydowego procesu roboczego i obciążenia. Nie można określić określonego pracownika. Hybrydowe procesy robocze elementów Runbook nie udostępniają wielu limitów, które są dostępne w piaskownicach platformy Azure. Nie mają one tych samych limitów dotyczących miejsca na dysku, pamięci lub gniazd sieciowych. Hybrydowe procesy robocze elementów Runbook są ograniczone przez zasoby samego hybrydowego procesu roboczego elementu Runbook. Ponadto hybrydowe procesy robocze elementów Runbook nie współdzielą 180-minutowym limitem czasu [udostępnienia](automation-runbook-execution.md#fair-share) w piaskownicach platformy Azure. Aby dowiedzieć się więcej na temat limitów usługi dla piaskownic platformy Azure i hybrydowych procesów roboczych elementów Runbook, zobacz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)zadań.

## <a name="install-a-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook

Proces instalacji hybrydowego procesu roboczego elementu Runbook zależy od systemu operacyjnego. W poniższej tabeli zdefiniowano typy wdrożeń.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ręcznie](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

Zalecaną metodą instalacji jest użycie elementu Runbook usługi Automation w celu całkowitego zautomatyzowania procesu konfigurowania komputera z systemem Windows. Druga metoda polega na przekroczeniu procedury krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku maszyn z systemem Linux należy uruchomić skrypt języka Python w celu zainstalowania agenta na komputerze.

> [!NOTE]
> Aby zarządzać konfiguracją serwerów obsługujących rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać serwery jako węzły DSC. Aby uzyskać więcej informacji na temat dołączania do zarządzania przy użyciu usługi DSC, zobacz [dołączanie maszyn w celu zarządzania przez Azure Automation DSC](automation-dsc-onboarding.md).
>
>Jeśli włączysz [Update Management rozwiązanie](automation-update-management.md), każdy komputer połączony z obszarem roboczym usługi Azure log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook uwzględnionych w tym rozwiązaniu. Jednak komputer nie jest zarejestrowany dla żadnych grup hybrydowych procesów roboczych, które są już zdefiniowane na koncie usługi Automation. Komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, o ile używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

Przed rozpoczęciem wdrażania hybrydowego procesu roboczego elementu Runbook zapoznaj się z [informacjami dotyczącymi planowania sieci](#network-planning) . Po pomyślnym wdrożeniu procesu roboczego zapoznaj się z tematem [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

Komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, o ile używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcjonalność została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Usuwanie hybrydowego procesu roboczego elementu Runbook z komputera lokalnego

Hybrydowy proces roboczy elementu Runbook można usunąć z komputera lokalnego, zgodnie z opisem w tej sekcji dla systemów Windows i Linux.

### <a name="remove-the-worker-on-windows"></a>Usuń proces roboczy w systemie Windows

1. W Azure Portal przejdź do konta usługi Automation.
2. W obszarze **Ustawienia konta**wybierz pozycję **klucze** i zanotuj wartości **adresu URL** i **podstawowego klucza dostępu**.

3. Otwórz sesję programu PowerShell w trybie administratora i uruchom następujące polecenie przy użyciu adresu URL i podstawowych wartości klucza dostępu. Użyj parametru `Verbose` , aby zapoznać się ze szczegółowym dziennikiem procesu usuwania. Aby usunąć przestarzałe maszyny z grupy hybrydowych procesów roboczych, użyj `machineName` opcjonalnego parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Usuwanie procesu roboczego w systemie Linux

Aby uzyskać identyfikator obszaru roboczego `ls /var/opt/microsoft/omsagent` , można użyć polecenia w hybrydowym procesie roboczym elementu Runbook. Zostanie utworzony folder o nazwie z IDENTYFIKATORem obszaru roboczego.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten kod nie usuwa z komputera agenta Log Analytics dla systemu Linux. Usuwa tylko funkcje i konfigurację roli hybrydowego procesu roboczego elementu Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook, należy najpierw usunąć hybrydowy proces roboczy elementu Runbook z każdego komputera, który jest członkiem grupy. Następnie wykonaj następujące kroki, aby usunąć grupę:

1. Otwórz konto usługi Automation w Azure Portal.
2. Wybierz **grupy hybrydowych procesów roboczych** w obszarze **Automatyzacja procesów**. Wybierz grupę, którą chcesz usunąć. Zostanie wyświetlona strona właściwości dla tej grupy.

   ![Strona właściwości](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stronie właściwości wybranej grupy wybierz pozycję **Usuń**. Zostanie wyświetlony komunikat z prośbą o potwierdzenie tej akcji. Wybierz opcję **tak** , jeśli na pewno chcesz kontynuować.

   ![Komunikat z potwierdzeniem](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ukończenie tego procesu może potrwać kilka sekund. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

## <a name="configure-your-network"></a><a name="network-planning"></a>Konfigurowanie sieci

### <a name="hybrid-worker-role"></a>Rola hybrydowego procesu roboczego

Aby hybrydowy proces roboczy elementu Runbook mógł nawiązać połączenie i zarejestrować się w Azure Automation, musi on mieć dostęp do numeru portu i adresów URL, które zostały opisane w tej sekcji. Ten dostęp znajduje się na górze [portów i adresów URL wymaganych przez program log Analytics Agent](../azure-monitor/platform/agent-windows.md) do nawiązywania połączenia z dziennikami Azure monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli serwer proxy jest używany do komunikacji między agentem a usługą Azure Automation, należy się upewnić, że dostępne są odpowiednie zasoby. Limit czasu dla żądań od hybrydowego procesu roboczego elementu Runbook i usług Automation wynosi 30 sekund. Po 3 próby żądanie zakończy się niepowodzeniem. W przypadku używania zapory w celu ograniczenia dostępu do Internetu należy skonfigurować zaporę tak, aby zezwalała na dostęp. Jeśli używasz bramy Log Analytics jako serwera proxy, upewnij się, że jest on skonfigurowany dla hybrydowych procesów roboczych. Aby uzyskać instrukcje, jak to zrobić, zobacz [Konfigurowanie bramy log Analytics dla hybrydowych procesów roboczych usługi Automation](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Następujący port i adresy URL są wymagane dla roli hybrydowego procesu roboczego elementu Runbook do komunikowania się z automatyzacją:

* Port: dla wychodzącego dostępu do Internetu wymagany jest tylko protokół TCP 443.
* Globalny adres URL: *. azure-automation.net
* Globalny adres URL w regionie US Gov Wirginia: *.azure-automation.us
* Usługa agenta: https://\<identyfikator obszaru roboczego\>. agentsvc.Azure-Automation.NET

Zaleca się użycie adresów wymienionych podczas definiowania wyjątków. W przypadku adresów IP można pobrać [zakresy adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację z tym regionalnym centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

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

Aby uzyskać listę adresów IP regionów zamiast nazw regionów, Pobierz plik XML [adresu IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) z centrum pobierania Microsoft.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera zakresy adresów IP, które są używane w centrach danych Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i magazynu.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień.
>
> Dobrym pomysłem jest pobranie nowego pliku XML co tydzień. Następnie zaktualizuj swoją witrynę, aby prawidłowo identyfikować usługi działające na platformie Azure. Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizacji anonsu usługi Border Gateway Protocol (BGP) w pierwszym tygodniu każdego miesiąca.

### <a name="update-management"></a>Zarządzanie aktualizacjami

Na podstawie standardowych adresów i portów wymaganych przez hybrydowy proces roboczy elementu Runbook wymagane są następujące adresy przeznaczone dla Update Management. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz temat [Rozwiązywanie problemów hybrydowych procesów roboczych](troubleshoot/hybrid-runbook-worker.md#general)