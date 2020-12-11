---
title: Zarządzanie agentem serwerów z obsługą usługi Azure Arc
description: W tym artykule opisano różne zadania zarządzania, które zwykle są wykonywane w cyklu życia serwerów z obsługą usługi Azure Arc połączonej z agentem.
ms.date: 10/30/2020
ms.topic: conceptual
ms.openlocfilehash: 9e17bf58d1e94b64d1cdc6ff0b57b1b6a81be180
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107196"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Zarządzanie agentem podłączonego komputera i ich obsługa

Po początkowym wdrożeniu serwera z włączoną obsługą usługi Azure Arc w systemie Windows lub Linux może być konieczne ponowne skonfigurowanie agenta, uaktualnienie go lub usunięcie z komputera. Można łatwo zarządzać tymi rutynowymi zadaniami konserwacji ręcznie lub przy użyciu automatyzacji, co zmniejsza zarówno błąd operacyjny, jak i wydatki.

## <a name="before-uninstalling-agent"></a>Przed odinstalowaniem agenta

Przed usunięciem agenta połączonej maszyny z serwera z obsługą łuku Rozważ następujące kwestie, aby uniknąć nieoczekiwanych problemów lub kosztów dodanych do rachunku na korzystanie z platformy Azure:

* Jeśli wdrożono rozszerzenia maszyny wirtualnej platformy Azure na włączonym serwerze i usuniesz agenta podłączonej maszyny lub usuniesz zasób reprezentujący serwer z włączonymi Łukiemmi w grupie zasobów, te rozszerzenia będą nadal działać i wykonywane były normalne operacje.

* Jeśli usuniesz zasób reprezentujący serwer z włączonym Łukem w grupie zasobów, ale nie odinstalujesz rozszerzeń maszyn wirtualnych, po ponownym zarejestrowaniu maszyny nie będziesz w stanie zarządzać zainstalowanymi rozszerzeniami maszyny wirtualnej.

W przypadku serwerów lub maszyn, których nie chcesz już zarządzać przy użyciu serwerów z obsługą usługi Azure ARC, należy wykonać następujące kroki, aby pomyślnie przerwać zarządzanie nim:

1. Usuń rozszerzenia maszyny wirtualnej z komputera lub serwera. Kroki są podane poniżej.

2. Odłącz maszynę od usługi Azure ARC przy użyciu jednej z następujących metod:

    * Uruchamianie `azcmagent disconnect` polecenia na komputerze lub serwerze.

    * Z wybranego zarejestrowanego serwera z włączonym łukiem łuku w Azure Portal wybierz pozycję **Usuń** z górnego paska.

    * Za pomocą [interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) lub [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). Dla `ResourceType` parametru USE `Microsoft.HybridCompute/machines` .

3. Odinstaluj agenta z komputera lub serwera. Wykonaj poniższe kroki.

## <a name="upgrading-agent"></a>Uaktualnianie agenta

Agent maszyny połączonej z platformą Azure jest regularnie aktualizowany w celu rozwiązywania poprawek usterek, ulepszeń stabilności i nowych funkcji. [Azure Advisor](../../advisor/advisor-overview.md) identyfikuje zasoby, które nie korzystają z najnowszej wersji programu Machine Agent i zaleca się uaktualnienie do najnowszej wersji. Po wybraniu serwera z włączonym Łukem zostanie wyświetlony transparent na stronie **Przegląd** lub podczas uzyskiwania dostępu do usługi Advisor za pomocą Azure Portal.

Agenta połączonego z platformą Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od wymagań.

W poniższej tabeli opisano metody, które są obsługiwane w celu przeprowadzenia uaktualnienia agenta.

| System operacyjny | Metoda uaktualniania |
|------------------|----------------|
| Windows | Ręczne<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [użyciu narzędzia zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agent systemu Windows

Pakiet aktualizacji dla agenta połączonej maszyny dla systemu Windows jest dostępny z:

* Microsoft Update

* [Katalog Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx)

* [Pakiet Instalator Windows agenta systemu Windows](https://aka.ms/AzureConnectedMachineAgent) z centrum pobierania Microsoft.

Agenta można uaktualnić, wykonując różne metody obsługi procesu zarządzania aktualizacjami oprogramowania. Poza uzyskaniem z Microsoft Update można pobrać i uruchomić ręcznie z wiersza polecenia, z skryptu lub innego rozwiązania do automatyzacji lub z Kreatora interfejsu użytkownika przez wykonanie `AzureConnectedMachine.msi` .

> [!NOTE]
> * Aby uaktualnić agenta, musisz mieć uprawnienia *administratora* .
> * Aby uaktualnić ręcznie, należy najpierw pobrać i skopiować pakiet Instalatora do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. 

Jeśli nie masz doświadczenia z opcjami wiersza polecenia dla Instalator Windows pakietów, przejrzyj [standardowe opcje wiersza polecenia](/windows/win32/msi/standard-installer-command-line-options) i [Opcje wiersza polecenia programu Msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby przeprowadzić uaktualnienie przy użyciu Kreatora instalacji

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **AzureConnectedMachineAgent.msi** , aby uruchomić Kreatora instalacji.

Kreator instalacji wykrywa, czy Poprzednia wersja istnieje, a następnie automatycznie wykonuje uaktualnienie agenta. Po zakończeniu uaktualniania Kreator instalacji zostanie automatycznie zamknięty.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby uaktualnić agenta w trybie dyskretnym i utworzyć plik dziennika instalacji w `C:\Support\Logs` folderze, uruchom następujące polecenie.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agent systemu Linux

Aby zaktualizować agenta na komputerze z systemem Linux do najnowszej wersji, obejmuje on dwa polecenia. Jedno polecenie aktualizowania indeksu pakietu lokalnego przy użyciu listy najnowszych dostępnych pakietów z repozytoriów i jednego polecenia w celu uaktualnienia pakietu lokalnego.

Najnowszy pakiet agenta można pobrać z [repozytorium pakietu](https://packages.microsoft.com/)firmy Microsoft.

> [!NOTE]
> Aby uaktualnić agenta, musisz mieć uprawnienia dostępu do zasobów *głównych* lub konto z podwyższonym poziomem uprawnień przy użyciu sudo.

#### <a name="upgrade-ubuntu"></a>Ubuntu uaktualnienia

1. Aby zaktualizować indeks pakietu lokalnego przy użyciu najnowszych zmian wprowadzonych w repozytoriach, uruchom następujące polecenie:

    ```bash
    apt update
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    apt upgrade
    ```

Akcje polecenia [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , takie jak instalacja i usuwanie pakietów, są rejestrowane w `/var/log/dpkg.log` pliku dziennika.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uaktualnienie Red Hat/CentOS/Amazon Linux

1. Aby zaktualizować indeks pakietu lokalnego przy użyciu najnowszych zmian wprowadzonych w repozytoriach, uruchom następujące polecenie:

    ```bash
    yum check-update
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    yum update
    ```

Akcje polecenia [yum](https://access.redhat.com/articles/yum-cheat-sheet) , takie jak instalacja i usuwanie pakietów, są rejestrowane w `/var/log/yum.log` pliku dziennika. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uaktualnianie systemu SUSE Linux Enterprise

1. Aby zaktualizować indeks pakietu lokalnego przy użyciu najnowszych zmian wprowadzonych w repozytoriach, uruchom następujące polecenie:

    ```bash
    zypper refresh
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    zypper update
    ```

Akcje polecenia [użyciu narzędzia zypper](https://en.opensuse.org/Portal:Zypper) , takie jak instalacja i usuwanie pakietów, są rejestrowane w `/var/log/zypper.log` pliku dziennika.

## <a name="about-the-azcmagent-tool"></a>Informacje o narzędziu Azcmagent

Narzędzie Azcmagent (Azcmagent.exe) służy do konfigurowania agenta maszyny połączonej z usługą Azure Arc podczas instalacji lub modyfikacji konfiguracji początkowej agenta po zakończeniu instalacji. Azcmagent.exe zawiera parametry wiersza polecenia umożliwiające dostosowanie agenta i wyświetlenie jego stanu:

* **Połącz** , aby połączyć maszynę z usługą Azure Arc

* **Odłącz** , aby rozłączyć maszynę z usługi Azure Arc

* **Pokaż** — Wyświetlanie stanu agenta i jego właściwości konfiguracji (nazwa grupy zasobów, Identyfikator subskrypcji, wersja itp.), która może pomóc w rozwiązaniu problemu z agentem. Dołącz `-j` parametr do danych wyjściowych w formacie JSON.

* **Logs** — tworzy plik zip w bieżącym katalogu zawierającym dzienniki, które ułatwiają rozwiązywanie problemów.

* **Wersja** — wyświetla wersję agenta połączonej maszyny.

* **-h lub--help** -wyświetla dostępne parametry wiersza polecenia

    Na przykład aby wyświetlić szczegółową pomoc dotyczącą parametru **Connect** , wpisz `azcmagent connect -h` . 

* **-v lub--verbose** -Włącz pełne rejestrowanie

Można **nawiązywać połączenie** i rozłączać ręcznie, podczas logowania interaktywnego, lub zautomatyzować przy użyciu tej samej jednostki usługi, która została użyta do **dołączenia** wielu agentów lub [tokenu dostępu](../../active-directory/develop/access-tokens.md)platformy tożsamości firmy Microsoft. Jeśli nie korzystasz z jednostki usługi w celu zarejestrowania maszyny przy użyciu serwerów z obsługą usługi Azure ARC, zapoznaj się z poniższym [artykułem](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) , aby utworzyć nazwę główną usługi.

>[!NOTE]
>Aby uruchamiać **azcmagent**, musisz mieć uprawnienia dostępu *głównego* na maszynach z systemem Linux.

### <a name="connect"></a>Połączenie

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę utworzoną na platformie Azure. Zasób należy do określonej subskrypcji i grupy zasobów, a dane dotyczące maszyny są przechowywane w regionie świadczenia usługi Azure określonym przez to `--location` ustawienie. Domyślna nazwa zasobu jest nazwą hosta maszyny, jeśli nie została określona.

Certyfikat odpowiadający tożsamości przypisanej do systemu komputera jest następnie pobierany i przechowywany lokalnie. Po zakończeniu tego kroku, Metadata Service i Agent konfiguracji gościa na platformie Azure rozpocznie synchronizację z serwerami z obsługą usługi Azure Arc.

Aby nawiązać połączenie przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Rozłącz

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę usuniętą z platformy Azure. Agent nie jest usuwany z komputera. należy to zrobić w osobnym kroku. Jeśli maszyna zostanie odłączona, jeśli chcesz ją ponownie zarejestrować przy użyciu serwerów z obsługą usługi Azure ARC, użyj, `azcmagent connect` Aby dla niej utworzyć nowy zasób na platformie Azure.

> [!NOTE]
> Po wdrożeniu co najmniej jednego rozszerzenia maszyny wirtualnej platformy Azure na serwerze z włączonym łukiem i usunięciu jego rejestracji na platformie Azure rozszerzenia są nadal zainstalowane. Ważne jest, aby zrozumieć, że w zależności od zainstalowanych rozszerzeń, aktywnie wykonuje swoją funkcję. Przed usunięciem rejestracji z platformy Azure należy najpierw usunąć te maszyny, które mają zostać wycofane lub już nie są zarządzane przez serwery z obsługą Arc.

Aby odłączyć się przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby rozłączyć się przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent disconnect --access-token <accessToken>`

Aby rozłączyć się z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Usuwanie agenta

Wykonaj jedną z następujących metod, aby odinstalować agenta połączonego komputera z systemem Windows lub Linux z komputera. Usunięcie agenta nie powoduje wyrejestrowania maszyny z serwerami z włączonymi Łukiemmi lub usunięciem zainstalowanych rozszerzeń maszyny wirtualnej platformy Azure. Te kroki należy wykonać oddzielnie, gdy nie trzeba już zarządzać maszyną na platformie Azure i należy je wykonać przed odinstalowaniem agenta.

### <a name="windows-agent"></a>Agent systemu Windows

Obie następujące metody usuwają agenta, ale nie usuwają folderu *C:\Program Files\AzureConnectedMachineAgent* na komputerze.

#### <a name="uninstall-from-control-panel"></a>Odinstaluj z panelu sterowania

1. Aby odinstalować agenta systemu Windows z komputera, wykonaj następujące czynności:

    a. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administratora.  
    b. W **Panelu sterowania** wybierz **aplet Programy i funkcje**.  
    c. W obszarze **programy i funkcje** wybierz pozycję **Agent połączonej maszyny Azure**, wybierz pozycję **Odinstaluj**, a następnie wybierz pozycję **tak**.  

    >[!NOTE]
    > Możesz również uruchomić Kreatora instalacji agenta, klikając dwukrotnie pakiet Instalatora **AzureConnectedMachineAgent.msi** .

#### <a name="uninstall-from-the-command-line"></a>Odinstalowywanie z wiersza polecenia

Aby odinstalować agenta ręcznie z wiersza polecenia lub użyć metody zautomatyzowanej, takiej jak skrypt, można użyć poniższego przykładu. Najpierw należy pobrać kod produktu, który jest identyfikatorem GUID, który jest identyfikatorem podmiotu zabezpieczeń pakietu aplikacji, z systemu operacyjnego. Odinstalowywanie odbywa się przy użyciu wiersza polecenia Msiexec.exe — `msiexec /x {Product Code}` .

1. Otwórz Edytor rejestru.

2. W obszarze klucz rejestru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` Znajdź i skopiuj identyfikator GUID kodu produktu.

3. Następnie można odinstalować agenta za pomocą polecenia msiexec przy użyciu następujących przykładów:

   * W wierszu polecenia wpisz:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Te same kroki można wykonać przy użyciu programu PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agent systemu Linux

> [!NOTE]
> Aby odinstalować agenta, musisz mieć uprawnienia dostępu do zasobów *głównych* lub konto z podwyższonym poziomem uprawnień przy użyciu sudo.

Do odinstalowania agenta systemu Linux polecenie, które ma być używane, zależy od system operacyjny Linux.

- W przypadku Ubuntu Uruchom następujące polecenie:

    ```bash
    sudo apt purge azcmagent
    ```

- W przypadku systemów RHEL, CentOS i Amazon Linux Uruchom następujące polecenie:

    ```bash
    sudo yum remove azcmagent
    ```

- W przypadku SLES Uruchom następujące polecenie:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Wyrejestruj maszynę

Jeśli planujesz zatrzymanie zarządzania komputerem za pomocą usług pomocniczych na platformie Azure, wykonaj następujące kroki, aby wyrejestrować maszynę z serwerami z włączonymi łukiem. Te kroki można wykonać wcześniej lub po usunięciu z komputera agenta podłączonego maszyny.

1. Otwórz serwery z obsługą usługi Azure ARC, przechodząc do [Azure Portal](https://aka.ms/hybridmachineportal).

2. Wybierz maszynę z listy, wybierz wielokropek (**...**), a następnie wybierz pozycję **Usuń**.

## <a name="update-or-remove-proxy-settings"></a>Aktualizowanie lub usuwanie ustawień serwera proxy

Aby skonfigurować agenta do komunikowania się z usługą za pomocą serwera proxy lub usunąć tę konfigurację po wdrożeniu, lub użyć jednej z następujących metod, aby wykonać to zadanie.

### <a name="windows"></a>Windows

Aby ustawić zmienną środowiskową serwera proxy, uruchom następujące polecenie:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Aby skonfigurować agenta do przetrzymywania komunikacji z serwerem proxy, uruchom następujące polecenie, aby usunąć zmienną środowiskową serwera proxy i ponownie uruchomić usługę agenta:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Aby ustawić serwer proxy, uruchom następujące polecenie z katalogu, do którego pobrano pakiet instalacyjny agenta:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Aby skonfigurować agenta do przetrzymywania komunikacji z serwerem proxy, uruchom następujące polecenie, aby usunąć konfigurację serwera proxy:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).