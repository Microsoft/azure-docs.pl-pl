---
title: Zarządzanie agentem serwerów z obsługą usługi Azure Arc
description: W tym artykule opisano różne zadania zarządzania, które zwykle są wykonywane w cyklu życia serwerów z obsługą usługi Azure Arc połączonej z agentem.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: cc42830fc73612e744942bdd8b353832e0ccbf2a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368459"
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

3. [Odinstaluj agenta](#remove-the-agent) z komputera lub serwera, wykonując poniższe kroki.

## <a name="renaming-a-machine"></a>Zmiana nazwy maszyny

Po zmianie nazwy maszyny z systemem Linux lub Windows połączonej z serwerami z obsługą usługi Azure Arc Nowa nazwa nie zostanie rozpoznana automatycznie, ponieważ nazwa zasobu na platformie Azure jest niezmienna. Podobnie jak w przypadku innych zasobów platformy Azure, należy usunąć zasób i utworzyć go ponownie w celu użycia nowej nazwy.

W przypadku serwerów z obsługą łuku przed zmianą nazwy maszyny należy usunąć rozszerzenia maszyn wirtualnych przed kontynuowaniem.

> [!NOTE]
> Gdy zainstalowane rozszerzenia nadal działają i wykonują ich normalną operację po zakończeniu tej procedury, nie będziesz w stanie zarządzać nimi. W przypadku próby ponownego wdrożenia rozszerzeń na komputerze może wystąpić nieprzewidywalne zachowanie.

> [!WARNING]
> Zalecamy uniknięcie zmiany nazwy komputera komputera i wykonanie tej procedury tylko w razie potrzeby.

1. Inspekcja rozszerzeń maszyn wirtualnych zainstalowanych na maszynie i zanotuj ich konfigurację przy użyciu [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md#list-extensions-installed) lub przy użyciu [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed).

2. Usuń rozszerzenia maszyny wirtualnej zainstalowane z [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension)przy użyciu [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)lub [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

3. Użyj narzędzia **azcmagent** z parametrem [Disconnect](manage-agent.md#disconnect) , aby rozłączyć maszynę z usługi Azure Arc i usunąć zasób maszyny z platformy Azure. Odłączanie maszyny od serwerów z obsługą łuku nie powoduje usunięcia agenta połączonej maszyny i nie ma potrzeby usuwania agenta w ramach tego procesu. Można uruchomić to ręcznie podczas logowania interakcyjnego lub zautomatyzować przy użyciu tej samej jednostki usługi, która została użyta do dołączenia wielu agentów lub [tokenu dostępu](../../active-directory/develop/access-tokens.md)platformy tożsamości firmy Microsoft. Jeśli nie korzystasz z jednostki usługi w celu zarejestrowania maszyny przy użyciu serwerów z obsługą usługi Azure ARC, zapoznaj się z poniższym [artykułem](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) , aby utworzyć nazwę główną usługi.

4. Zmień nazwę komputera.

5. Zarejestruj ponownie agenta połączonej maszyny z włączonymi serwerami z funkcją Arc. Uruchom `azcmagent` Narzędzie z parametrem [Connect](manage-agent.md#connect) , aby wykonać ten krok.

6. Wdróż ponownie rozszerzenia maszyn wirtualnych, które zostały początkowo wdrożone na maszynie z serwerów z włączonym łukiem. Po wdrożeniu agenta Azure Monitor dla maszyn wirtualnych (Insights) lub agenta Log Analytics przy użyciu zasad platformy Azure agenci są wdrażani ponownie po kolejnym [cyklu oceny](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

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

Agenta można uaktualnić przy użyciu różnych metod, aby obsługiwać proces zarządzania aktualizacjami oprogramowania. Poza uzyskaniem z Microsoft Update można pobrać i uruchomić ręcznie z wiersza polecenia, z skryptu lub innego rozwiązania do automatyzacji lub z Kreatora interfejsu użytkownika przez wykonanie `AzureConnectedMachine.msi` .

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

### <a name="connect"></a>Connect

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę utworzoną na platformie Azure. Zasób należy do określonej subskrypcji i grupy zasobów, a dane dotyczące maszyny są przechowywane w regionie świadczenia usługi Azure określonym przez to `--location` ustawienie. Domyślna nazwa zasobu jest nazwą hosta maszyny, jeśli nie została określona.

Certyfikat odpowiadający tożsamości przypisanej do systemu komputera jest następnie pobierany i przechowywany lokalnie. Po zakończeniu tego kroku, Metadata Service i Agent konfiguracji gościa na platformie Azure rozpocznie synchronizację z serwerami z obsługą usługi Azure Arc.

Aby nawiązać połączenie przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Rozłącz

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę usuniętą z platformy Azure. Agent nie jest usuwany z komputera, odinstalowany Agent osobno. Jeśli maszyna zostanie odłączona, jeśli chcesz ją ponownie zarejestrować przy użyciu serwerów z obsługą usługi Azure ARC, użyj, `azcmagent connect` Aby dla niej utworzyć nowy zasób na platformie Azure.

> [!NOTE]
> Po wdrożeniu co najmniej jednego rozszerzenia maszyny wirtualnej platformy Azure na serwerze z włączonym łukiem i usunięciu jego rejestracji na platformie Azure rozszerzenia są nadal zainstalowane. Ważne jest, aby zrozumieć, że w zależności od zainstalowanych rozszerzeń, aktywnie wykonuje swoją funkcję. Przed usunięciem rejestracji z platformy Azure należy najpierw usunąć te maszyny, które mają zostać wycofane lub już nie są zarządzane przez serwery z obsługą Arc.

Aby odłączyć się przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby rozłączyć się przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent disconnect --access-token <accessToken>`

Aby rozłączyć się z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Usuwanie agenta

Wykonaj jedną z następujących metod, aby odinstalować agenta połączonego komputera z systemem Windows lub Linux z komputera. Usunięcie agenta nie powoduje wyrejestrowania maszyny z serwerami z włączonymi Łukiemmi lub usunięciem zainstalowanych rozszerzeń maszyny wirtualnej platformy Azure. Wyrejestruj maszynę i Usuń zainstalowane rozszerzenia maszyn wirtualnych oddzielnie, gdy nie potrzebujesz już zarządzać maszyną na platformie Azure, a następnie te kroki należy wykonać przed odinstalowaniem agenta.

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

> [!NOTE]
> Serwery z włączonymi łukiemmi nie obsługują używania [bramy log Analytics](../../azure-monitor/platform/gateway.md) jako serwera proxy dla agenta połączonego maszyny.
>

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