---
title: Omówienie konfiguracji stanu automatyzacji platformy Azure
description: Ten artykuł jest omówienie konfiguracji stanu automatyzacji platformy Azure (DSC), jego warunki i jego znanych problemów.
keywords: powershell dsc, konfiguracja żądanego stanu, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406180"
---
# <a name="state-configuration-overview"></a>Konfiguracja stanu — omówienie

Konfiguracja stanu automatyzacji platformy Azure to usługa platformy Azure, która umożliwia [pisanie,](/powershell/scripting/dsc/configurations/configurations)zarządzanie i kompilowanie konfiguracji konfiguracji żądanego stanu programu PowerShell (DSC). Usługa importuje również [zasoby DSC](/powershell/scripting/dsc/resources/resources) i przypisuje konfiguracje do węzłów docelowych, wszystkie w chmurze.

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto używać usługi Azure Automation State Configuration?

Konfiguracja stanu automatyzacji platformy Azure zapewnia kilka zalet w stosunku do korzystania z dsc poza platformą Azure.

### <a name="built-in-pull-server"></a>Wbudowany serwer ściągania

Konfiguracja stanu automatyzacji platformy Azure udostępnia serwer ściągania DSC podobny do [usługi DSC funkcji systemu Windows.](/powershell/scripting/dsc/pull-server/pullserver) Węzły docelowe mogą automatycznie odbierać konfiguracje, być zgodne z żądanym stanem i raportować ich zgodność. Wbudowany serwer ściągania w usłudze Azure Automation eliminuje konieczność konfigurowania i utrzymywania własnego serwera ściągania. Usługa Azure Automation może być kierowana na maszyny z systemem Windows lub Linux, w chmurze lub lokalnie.

### <a name="manage-all-your-dsc-artifacts"></a>Zarządzanie wszystkimi artefaktami DSC

Konfiguracja stanu automatyzacji platformy Azure przynosi tę samą warstwę zarządzania do [konfiguracji żądanego stanu programu PowerShell,](/powershell/scripting/dsc/overview/overview) jak oferuje dla skryptów programu PowerShell. Z witryny Azure portal lub z programu PowerShell można zarządzać wszystkie konfiguracje DSC, zasoby i węzły docelowe.

![Zrzut ekranu przedstawiający stronę usługi Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importowanie danych raportowania do dzienników monitora platformy Azure

Węzły, które są zarządzane za pomocą konfiguracji stanu automatyzacji usługi Azure wysyłają szczegółowe dane o stanie raportowania do wbudowanego serwera ściągania. Można skonfigurować konfigurację stanu automatyzacji platformy Azure, aby wysłać te dane do obszaru roboczego usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Przesyłanie dalej danych raportowania konfiguracji stanu usługi Azure Automation do dzienników usługi Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania podczas korzystania z konfiguracji stanu automatyzacji platformy Azure dla DSC.

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego

W przypadku węzłów z systemem Windows obsługiwane są następujące wersje:

- Windows Server 2019
- Windows Server 2016
- System Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Ponieważ jednostka SKU autonomicznego produktu [programu Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) nie zawiera implementacji dsc, nie można zarządzać przez program PowerShell DSC lub konfigurację stanu automatyzacji platformy Azure.

W przypadku węzłów z systemem Linux rozszerzenie DSC Linux obsługuje wszystkie dystrybucje Linuksa wymienione w obszarze [Obsługiwane dystrybucje Linuksa](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Wymagania DSC

Dla wszystkich węzłów systemu Windows uruchomionych na platformie Azure program [Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) jest instalowany podczas dołączania. W przypadku węzłów z systemem Windows Server 2012 i Windows 7 usługa [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) jest włączona.

Dla wszystkich węzłów systemu Linux uruchomionych na platformie Azure [program PowerShell DSC dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowany podczas dołączania.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguracja sieci prywatnych

Jeśli węzły znajdują się w sieci prywatnej, wymagane są następujące porty i adresy URL. Te zasoby zapewniają łączność sieciową dla węzła zarządzanego i umożliwiają dsc do komunikowania się z usługą Azure Automation.

* Port: Tylko TCP 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL: ***.azure-automation.net**
* Globalny adres URL us Gov Virginia: ***.azure-automation.us**
* Usługa agenta: **https://\<obszar\>roboczyId .agentsvc.azure-automation.net**

Jeśli używasz zasobów DSC, które komunikują się między węzłami, takich jak [WaitFor* zasobów,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)należy również zezwolić na komunikację między węzłami. Aby zrozumieć te wymagania sieciowe, zobacz dokumentację dla każdego zasobu DSC.

#### <a name="proxy-support"></a>Obsługa serwera proxy

Obsługa serwera proxy dla agenta DSC jest dostępna w systemie Windows w wersji 1809 i nowszej. Tę opcję można włączyć, `ProxyURL` ustawiając wartości dla i `ProxyCredential` w [skrypcie metakonfiguracji,](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) który jest używany do rejestrowania węzłów.

>[!NOTE]
>Konfiguracja stanu automatyzacji platformy Azure nie zapewnia obsługi serwera proxy DSC dla wcześniejszych wersji systemu Windows.

W przypadku węzłów systemu Linux agent DSC obsługuje `http_proxy` serwer proxy i używa zmiennej do określenia adresu URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Zakresy sieci konfiguracji stanu automatyzacji platformy Azure i obszar nazw

Podczas definiowania wyjątków zaleca się używanie adresów IP wymienionych w poniższej tabeli. W przypadku adresów IP można pobrać plik XML [zakresów adresów IP centrów danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) z Centrum pobierania Microsoft. Ten plik zawiera aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Jest aktualizowany co tydzień.

Jeśli masz konto automatyzacji, które jest zdefiniowane dla określonego regionu, można ograniczyć komunikację do tego regionalnego centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Południowo-środkowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera listę zakresów adresów IP, które są używane w centrach danych platformy Microsoft Azure. Plik zawiera zakresy obliczeniowe, SQL i magazynowe.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Warto co tydzień pobierać nowy plik XML. Następnie można zaktualizować witrynę, aby poprawnie zidentyfikować usługi uruchomione na platformie Azure. 

Jeśli jesteś użytkownikiem usługi Azure ExpressRoute, należy pamiętać, że ten plik jest używany do aktualizowania reklamy protokołu Bramy granicznej (BGP) miejsca na platformie Azure w pierwszym tygodniu każdego miesiąca.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z usługi DSC w konfiguracji stanu automatyzacji platformy Azure, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak włączyć węzły, zobacz [Wbudowane maszyny do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Na przykład przy użyciu konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [ciągłe wdrażanie na maszynach wirtualnych przy użyciu konfiguracji stanu automatyzacji platformy Azure i Chocolatey](automation-dsc-cd-chocolatey.md).
