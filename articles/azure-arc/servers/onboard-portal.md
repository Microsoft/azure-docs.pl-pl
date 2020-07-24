---
title: Łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszyny z platformą Azure przy użyciu usługi Azure ARC dla serwerów (wersja zapoznawcza) z Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: fe26731a7f217d0f441a3dea1650f2652677a57f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133014"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal

Można włączyć usługę Azure ARC dla serwerów (wersja zapoznawcza) dla jednej lub małej liczby maszyn z systemem Windows lub Linux w środowisku, wykonując ręcznie zestaw kroków. Możesz też użyć metody zautomatyzowanej, uruchamiając skrypt szablonu, który udostępniamy. Ten skrypt automatyzuje pobieranie i Instalowanie obu agentów.

Ta metoda wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do lokalnej grupy administratorów.

Przed rozpoczęciem należy zapoznać się z wymaganiami [wstępnymi](agent-overview.md#prerequisites) i upewnić się, że Twoja subskrypcja i zasoby spełniają wymagania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Wygeneruj skrypt instalacji z Azure Portal

Skrypt służący do automatyzowania pobierania i instalacji oraz do nawiązywania połączenia z usługą Azure Arc jest dostępny w Azure Portal. Aby ukończyć ten proces, wykonaj następujące czynności:

1. W przeglądarce przejdź do [Azure Portal](https://aka.ms/hybridmachineportal).

1. Na stronie **automaty na platformie Azure** wybierz pozycję **Dodaj**, w lewym górnym rogu lub opcję **Utwórz Machine-Azure Arc** w dolnej części środkowego okienka.

1. Na stronie **Wybierz metodę** wybierz kafelek **Dodawanie maszyn przy użyciu interakcyjnego skryptu** , a następnie wybierz pozycję **Generuj skrypt**.

1. Na stronie **Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszyny.

    >[!NOTE]
    >Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje tylko następujące regiony:
    >- EastUS
    >- WestUS2
    >- WestEurope
    >- SoutheastAsia
    >
    >Zapoznaj się z dodatkowymi zagadnieniami dotyczącymi [wyboru regionu w](overview.md#supported-regions) artykule przegląd.

1. Na stronie **Generuj skrypt** na liście rozwijanej **system operacyjny** wybierz system operacyjny, na którym będzie uruchamiany skrypt.

1. Jeśli komputer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, wybierz pozycję **Dalej: serwer proxy**.
1. Na karcie **serwer proxy** Określ adres IP serwera proxy lub nazwę i numer portu, który będzie używany przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>` .
1. Wybierz pozycję **Recenzja + generowanie**.

1. Na karcie **Recenzja + generowanie** Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. Jeśli nadal musisz wprowadzić zmiany, wybierz pozycję **Poprzednia**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalowanie i weryfikowanie agenta w systemie Windows

### <a name="install-manually"></a>Instalowanie ręczne

Agenta połączonego maszyny można zainstalować ręcznie, uruchamiając *AzureConnectedMachineAgent.msi*pakietu Instalator Windows. Najnowszą wersję [pakietu Windows agent Instalator Windows](https://aka.ms/AzureConnectedMachineAgent) można pobrać z centrum pobierania Microsoft.

>[!NOTE]
>* Aby zainstalować lub odinstalować agenta, musisz mieć uprawnienia *administratora* .
>* Należy najpierw pobrać i skopiować pakiet Instalatora do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. Jeśli zostanie uruchomiony pakiet Instalatora bez żadnych opcji, zostanie uruchomiony Kreator instalacji, który można wykonać, aby zainstalować agenta interaktywnie.

Jeśli komputer musi komunikować się z usługą za pomocą serwera proxy, po zainstalowaniu agenta należy uruchomić polecenie opisane w dalszej części artykułu. Powoduje to ustawienie zmiennej środowiskowej serwera proxy `https_proxy` .

Jeśli nie masz doświadczenia z opcjami wiersza polecenia dla Instalator Windows pakietów, przejrzyj [standardowe opcje wiersza polecenia](/windows/win32/msi/standard-installer-command-line-options) i [Opcje wiersza polecenia programu Msiexec](/windows/win32/msi/command-line-options).

Na przykład Uruchom program instalacyjny z `/?` parametrem, aby przejrzeć opcję Pomoc i krótka dokumentacja.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Aby zainstalować agenta w trybie dyskretnym i utworzyć plik dziennika instalacji w `C:\Support\Logs` folderze, który istnieje, uruchom następujące polecenie.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Zainstaluj przy użyciu metody skryptowej

1. Zaloguj się na serwerze.

1. Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień.

    >[!NOTE]
    >Skrypt obsługuje tylko uruchamianie z 64-bitowej wersji programu Windows PowerShell.
    >

1. Przejdź do folderu lub udziału, do którego skopiowano skrypt, i wykonaj go na serwerze, uruchamiając `./OnboardingScript.ps1` skrypt.

Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="configure-the-agent-proxy-setting"></a>Skonfiguruj ustawienie serwera proxy agenta

Aby ustawić zmienną środowiskową serwera proxy, uruchom następujące polecenie:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nie obsługuje ustawiania uwierzytelniania serwera proxy w tej wersji zapoznawczej.
>

### <a name="configure-agent-communication"></a>Konfigurowanie komunikacji agenta

Po zainstalowaniu agenta należy skonfigurować agenta do komunikowania się z usługą Azure ARC, uruchamiając następujące polecenie:

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalowanie i weryfikowanie agenta w systemie Linux

Agent połączonej maszyny dla systemu Linux jest dostępny w preferowanym formacie pakietu dla dystrybucji (. RPM lub. DEB), które są hostowane w [repozytorium pakietu](https://packages.microsoft.com/)Microsoft. [ `Install_linux_azcmagent.sh` Pakiet skryptu powłoki](https://aka.ms/azcmagent) wykonuje następujące akcje:

- Konfiguruje maszynę hosta do pobrania pakietu agenta z packages.microsoft.com.
- Instaluje pakiet hybrydowego dostawcy zasobów.

Opcjonalnie można skonfigurować agenta przy użyciu informacji o serwerze proxy, dołączając `--proxy "{proxy-url}:{proxy-port}"` parametr.

Skrypt zawiera również logikę identyfikującą obsługiwane i nieobsługiwane dystrybucje oraz weryfikuje uprawnienia wymagane do przeprowadzenia instalacji.

Poniższy przykład pobiera agenta i instaluje go:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

Aby pobrać i zainstalować agenta, w tym `--proxy` parametr służący do konfigurowania agenta do komunikowania się za pomocą serwera proxy, uruchom następujące polecenia:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurowanie komunikacji agenta

Po zainstalowaniu agenta skonfiguruj go do komunikacji z usługą Azure ARC, uruchamiając następujące polecenie:

`azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

>[!NOTE]
>Aby uruchamiać **azcmagent**, musisz mieć uprawnienia dostępu *głównego* na maszynach z systemem Linux.

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z usługą Azure ARC dla serwerów (wersja zapoznawcza) przejdź do Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetl maszyny w witrynie [Azure Portal](https://aka.ms/hybridmachineportal).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy użytkownik chce aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim za pomocą elementów Runbook usługi Automation lub rozwiązań, takich jak Update Management, lub używać innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-intro.md).
