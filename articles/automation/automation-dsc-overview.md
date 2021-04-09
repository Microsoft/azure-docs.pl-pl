---
title: Przegląd konfiguracji stanu Azure Automation
description: Ten artykuł zawiera omówienie konfiguracji stanu Azure Automation.
keywords: PowerShell DSC, Konfiguracja żądanego stanu, środowisko PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897024"
---
# <a name="azure-automation-state-configuration-overview"></a>Przegląd konfiguracji stanu Azure Automation

Konfiguracja stanu Azure Automation to usługa zarządzania konfiguracją platformy Azure, która umożliwia pisanie, zarządzanie i kompilowanie konfiguracyjnych [konfiguracji stanu (DSC) dla](/powershell/scripting/dsc/configurations/configurations) węzłów w chmurze lub lokalnych centrach danych. Usługa importuje również [zasoby DSC](/powershell/scripting/dsc/resources/resources)i przypisuje konfiguracje do węzłów docelowych, a wszystko to w chmurze. Aby uzyskać dostęp do konfiguracji stanu Azure Automation w Azure Portal, wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.

Azure Automation konfiguracji stanu można użyć do zarządzania różnymi maszynami:

- Maszyny wirtualne platformy Azure
- Azure Virtual Machines (klasyczny)
- Fizyczne/wirtualne maszyny z systemem Windows w środowisku lokalnym lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)
- Fizyczne/wirtualne maszyny z systemem Linux lokalnie, na platformie Azure lub w chmurze innej niż Azure

Jeśli nie masz gotowości do zarządzania konfiguracją maszyny z chmury, możesz użyć konfiguracji stanu Azure Automation jako punktu końcowego tylko do raportowania. Ta funkcja umożliwia ustawianie (wypychania) konfiguracji za poorednictwem DSC i wyświetlanie szczegółów raportowania w Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu Azure Automation jest bezpłatne, jeśli zainstalowana wersja rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest większa niż 2,70. Aby uzyskać więcej informacji, zobacz [**stronę z cennikiem usługi Automation**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto używać konfiguracji stanu Azure Automation

Azure Automation konfiguracja stanu zapewnia kilka korzyści w porównaniu z użyciem DSC poza platformą Azure. Ta usługa umożliwia szybkie i łatwe skalowanie w tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo włączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

Usługa konfiguracji stanu Azure Automation to DSC, co Azure Automation elementów Runbook do obsługi skryptów programu PowerShell. Innymi słowy, w taki sam sposób, jaki Azure Automation ułatwia zarządzanie skryptami programu PowerShell, ułatwia również zarządzanie konfiguracjami DSC.

### <a name="built-in-pull-server"></a>Wbudowany serwer ściągania

Azure Automation konfiguracja stanu udostępnia serwer ściągania DSC podobny do [usługi Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Węzły docelowe mogą automatycznie odbierać konfiguracje, odpowiadać na żądanym stanie i zgłaszać ich zgodność. Wbudowany serwer ściągania w programie Azure Automation eliminuje konieczność skonfigurowania i utrzymania własnego serwera ściągania. Azure Automation mogą kierować wirtualne lub fizyczne maszyny z systemem Windows lub Linux, w chmurze lub w środowisku lokalnym.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie wszystkimi artefaktami DSC

Azure Automation konfiguracja stanu umożliwia korzystanie z tej samej warstwy zarządzania do [konfiguracji żądanego stanu programu PowerShell](/powershell/scripting/dsc/overview/overview) , ponieważ oferuje ona obsługę skryptów programu PowerShell. Z poziomu Azure Portal lub z programu PowerShell można zarządzać wszystkimi konfiguracjami DSC, zasobami i węzłami docelowymi.

![Zrzut ekranu przedstawiający stronę Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importowanie danych raportowania do dzienników Azure Monitor

Węzły, które są zarządzane za pomocą Azure Automation stanu Configuration, wysyłają szczegółowe dane stanu raportowania do wbudowanego serwera ściągania. Konfigurację stanu Azure Automation można skonfigurować w celu wysłania tych danych do Log Analytics obszaru roboczego. Zapoznaj [się z raportem Azure Monitor Azure Automation przekazywania danych dotyczących konfiguracji stanu](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku korzystania z konfiguracji stanu Azure Automation należy wziąć pod uwagę wymagania zawarte w tej sekcji.

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego

W przypadku węzłów z systemem Windows obsługiwane są następujące wersje:

- Windows Server 2019
- Windows Server 2016
- 2012R2 systemu Windows Server
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Autonomiczna jednostka SKU produktu [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) nie zawiera implementacji DSC. Dlatego nie może być zarządzany przez program PowerShell DSC lub Azure Automation konfigurację stanu.

W przypadku węzłów z systemem Linux rozszerzenie DSC Linux obsługuje wszystkie dystrybucje systemu Linux wymienione w [dokumentacji DSC programu PowerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="dsc-requirements"></a>Wymagania DSC

W przypadku wszystkich węzłów systemu Windows działających na platformie Azure program [WMF 5,1](/powershell/scripting/wmf/setup/install-configure) jest instalowany po włączeniu maszyn. W przypadku węzłów z systemami Windows Server 2012 i Windows 7 [usługa WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) jest włączona.

W przypadku wszystkich węzłów systemu Linux działających na platformie Azure program [POWERSHELL DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest instalowany po włączeniu maszyn.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguracja sieci prywatnych

Sprawdź [konfigurację sieci Azure Automation](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) , aby uzyskać szczegółowe informacje dotyczące portów, adresów URL i innych szczegółów sieci wymaganych dla węzłów w sieci prywatnej.

#### <a name="proxy-support"></a>Obsługa serwera proxy

Obsługa serwera proxy dla agenta DSC jest dostępna w systemie Windows w wersji 1809 i nowszych. Ta opcja jest włączona przez ustawienie wartości `ProxyURL` i `ProxyCredential` właściwości w [skrypcie konfiguracji](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) , używanym do rejestrowania węzłów.

>[!NOTE]
>Konfiguracja stanu Azure Automation nie zapewnia obsługi serwera proxy DSC dla wcześniejszych wersji systemu Windows.

W przypadku węzłów systemu Linux Agent DSC obsługuje serwer proxy i używa `http_proxy` zmiennej do określenia adresu URL. Aby dowiedzieć się więcej na temat obsługi serwera proxy, zobacz [generowanie konfiguracji DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Rekordy DNS na region

Zaleca się używanie adresów wymienionych w tabeli [rekordy DNS na region](how-to/automation-region-dns-records.md) podczas definiowania wyjątków.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak włączyć węzły, zobacz [Włączanie konfiguracji stanu Azure Automation](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
