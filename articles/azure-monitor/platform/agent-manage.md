---
title: Zarządzanie agentem usługi Azure Log Analytics
description: W tym artykule opisano różne zadania zarządzania, które zwykle są wykonywane podczas cyklu życia Log Analytics agenta systemu Windows lub Linux wdrożonego na komputerze.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011884"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Managing and maintaining the Log Analytics agent for Windows and Linux (Konserwacja agenta usługi Log Analytics dla systemów Windows i Linux oraz zarządzanie nim)

Po początkowym wdrożeniu Log Analytics agenta systemu Windows lub Linux w programie Azure Monitor może być konieczne ponowne skonfigurowanie agenta, uaktualnienie go lub usunięcie z komputera, jeśli osiągnął etap wycofania w jego cyklu życia. Można łatwo zarządzać tymi rutynowymi zadaniami konserwacji ręcznie lub przy użyciu automatyzacji, co zmniejsza zarówno błąd operacyjny, jak i wydatki.

## <a name="upgrading-agent"></a>Uaktualnianie agenta

Log Analytics agenta dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od scenariusza wdrażania i środowiska, w którym jest uruchomiona maszyna wirtualna. Do uaktualnienia agenta można użyć poniższych metod.

| Środowisko | Metoda instalacji | Metoda uaktualniania |
|--------|----------|-------------|
| Maszyna wirtualna platformy Azure | Rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Windows/Linux | Agent jest automatycznie uaktualniany, chyba że skonfigurowano szablon Azure Resource Manager, aby zrezygnował z ustawienia właściwości *włączoną flagą autoupgrademinorversion* na **wartość false**. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux.|
| Maszyny wirtualne spoza platformy Azure | Ręczna instalacja agenta Log Analytics dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux. |

### <a name="upgrade-windows-agent"></a>Uaktualnij agenta systemu Windows 

Aby zaktualizować agenta na maszynie wirtualnej z systemem Windows do najnowszej wersji, która nie jest zainstalowana przy użyciu rozszerzenia maszyny wirtualnej Log Analytics, można uruchomić polecenie z wiersza polecenia, skryptu lub innego rozwiązania do automatyzacji lub za pomocą \<platform\> Kreatora instalacji MMASetup-. msi.  

Możesz pobrać najnowszą wersję agenta systemu Windows z obszaru roboczego Log Analytics, wykonując poniższe kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.

3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy.

4. W obszarze roboczym Log Analytics wybierz pozycję **Ustawienia zaawansowane**, a następnie wybierz pozycję **połączone źródła**, a na koniec **serwery z systemem Windows**.

5. Na stronie **serwery z systemem Windows** wybierz odpowiednią wersję **agenta pobierania systemu Windows** do pobrania w zależności od architektury procesora systemu operacyjnego Windows.

>[!NOTE]
>Podczas uaktualniania agenta Log Analytics dla systemu Windows program nie obsługuje konfigurowania lub ponownego konfigurowania obszaru roboczego do raportowania. Aby skonfigurować agenta, należy wykonać jedną z obsługiwanych metod wymienionych w obszarze [Dodawanie lub usuwanie obszaru roboczego](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby przeprowadzić uaktualnienie przy użyciu Kreatora instalacji

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **MMASetup- \<platform\> . exe** , aby uruchomić Kreatora instalacji.

3. Na pierwszej stronie Kreatora instalacji kliknij przycisk **dalej**.

4. W oknie dialogowym **konfiguracja Microsoft Monitoring Agent** kliknij przycisk **zgadzam** się, aby zaakceptować umowę licencyjną.

5. W oknie dialogowym **Instalator programu Microsoft Monitoring Agent** kliknij przycisk **Uaktualnij**. Na stronie stanu wyświetlany będzie postęp uaktualnienia.

6. Po **pomyślnym ukończeniu konfiguracji Microsoft Monitoring Agent.** Kliknij przycisk **Zakończ**.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Do wyodrębnienia plików instalacyjnych agenta z wiersza polecenia z podwyższonym poziomem uprawnień `MMASetup-<platform>.exe /c` i pojawi się monit o ścieżkę, do której mają zostać wyodrębnione pliki. Alternatywnie możesz określić ścieżkę, przekazując argumenty `MMASetup-<platform>.exe /c /t:<Full Path>` .

3. Uruchom następujące polecenie (w którym D:\ określa lokalizację pliku dziennika uaktualnienia).

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Uaktualnij agenta systemu Linux 

Aktualizacja z wcześniejszych wersji (>1.0.0-47) jest obsługiwana. Wykonanie instalacji przy użyciu `--upgrade` polecenia spowoduje uaktualnienie wszystkich składników agenta do najnowszej wersji.

Uruchom następujące polecenie, aby uaktualnić agenta.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Dodawanie lub usuwanie obszaru roboczego

### <a name="windows-agent"></a>Agent systemu Windows
Kroki opisane w tej sekcji są niezbędne, aby nie tylko ponownie konfigurować agenta systemu Windows w celu raportowania do innego obszaru roboczego lub usuwania obszaru roboczego z jego konfiguracji, ale również wtedy, gdy chcesz skonfigurować agenta do raportowania do więcej niż jednego obszaru roboczego (często określanego jako wiele multihostingu). Konfigurowanie agenta systemu Windows w celu raportowania do wielu obszarów roboczych można wykonać tylko po wstępnej instalacji agenta i korzystając z metod opisanych poniżej.    

#### <a name="update-settings-from-control-panel"></a>Aktualizowanie ustawień z panelu sterowania

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz **Microsoft Monitoring Agent** a następnie kliknij kartę **Azure log Analytics** .

4. W przypadku usuwania obszaru roboczego zaznacz go, a następnie kliknij przycisk **Usuń**. Powtórz ten krok dla każdego obszaru roboczego, do którego Agent ma przestać zgłaszać.

5. W przypadku dodawania obszaru roboczego kliknij przycisk **Dodaj** , a następnie w oknie dialogowym **Dodawanie log Analytics obszaru roboczego** wklej identyfikator obszaru roboczego i klucz obszaru roboczego (klucz podstawowy). Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz Wersja platformy Azure dla administracji USA z listy rozwijanej Azure Cloud.

6. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="remove-a-workspace-using-powershell"></a>Usuwanie obszaru roboczego przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Dodawanie obszaru roboczego w komercyjnym środowisku platformy Azure przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Dodawanie obszaru roboczego na platformie Azure dla instytucji rządowych USA przy użyciu programu PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Jeśli używasz wiersza polecenia lub skryptu wcześniej do zainstalowania lub skonfigurowania agenta, `EnableAzureOperationalInsights` został on zastąpiony przez `AddCloudWorkspace` i `RemoveCloudWorkspace` .
>

### <a name="linux-agent"></a>Agent systemu Linux
Poniższe kroki pokazują, jak ponownie skonfigurować agenta systemu Linux, jeśli zdecydujesz się zarejestrować go w innym obszarze roboczym lub usunąć obszar roboczy z jego konfiguracji.

1. Aby sprawdzić, czy jest ona zarejestrowana w obszarze roboczym, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinna zostać zwrócona wartość podobna do poniższego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Należy pamiętać, że stan pokazuje również, że Agent jest uruchomiony. w przeciwnym razie następujące kroki w celu zmiany konfiguracji agenta nie zostaną zakończone pomyślnie.

2. Jeśli jest już zarejestrowany w obszarze roboczym, Usuń zarejestrowany obszar roboczy, uruchamiając następujące polecenie. W przeciwnym razie, jeśli nie jest zarejestrowany, przejdź do następnego kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Aby zarejestrować się w innym obszarze roboczym, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Aby sprawdzić, czy zmiany zostały wprowadzone, uruchom następujące polecenie:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Powinna zostać zwrócona wartość podobna do poniższego przykładu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Nie trzeba ponownie uruchamiać usługi agenta, aby zmiany zaczęły obowiązywać.

## <a name="update-proxy-settings"></a>Aktualizowanie ustawień serwera proxy
Aby skonfigurować agenta do komunikowania się z usługą za pomocą serwera proxy lub [bramy log Analytics](gateway.md) po wdrożeniu, należy użyć jednej z następujących metod, aby wykonać to zadanie.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="update-settings-using-control-panel"></a>Aktualizowanie ustawień przy użyciu panelu sterowania

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Wybierz **Microsoft Monitoring Agent** a następnie kliknij kartę **Ustawienia serwera proxy** .

4. Kliknij pozycję **Użyj serwera proxy** i podaj adres URL oraz numer portu serwera proxy lub bramy. Jeśli Twój serwer proxy lub brama usługi Log Analytics wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby się uwierzytelnić, a następnie kliknij przycisk **OK**.

#### <a name="update-settings-using-powershell"></a>Aktualizowanie ustawień przy użyciu programu PowerShell

Skopiuj następujący przykładowy kod programu PowerShell, zaktualizuj go o informacje specyficzne dla danego środowiska i Zapisz go z rozszerzeniem nazwy pliku PS1. Uruchom skrypt na każdym komputerze, który łączy się bezpośrednio z obszarem roboczym Log Analytics w Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Agent systemu Linux
Jeśli komputery z systemem Linux muszą komunikować się za pomocą serwera proxy lub bramy Log Analytics, wykonaj następujące czynności. Wartość konfiguracji serwera proxy ma następującą składnię `[protocol://][user:password@]proxyhost[:port]`. Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy.

1. Edytuj plik `/etc/opt/microsoft/omsagent/proxy.conf`, uruchamiając następujące polecenia, i zmień wartości na odpowiednie dla siebie.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Uruchom ponownie agenta, uruchamiając następujące polecenie:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Odinstaluj agenta
Aby odinstalować agenta systemu Windows lub Linux przy użyciu wiersza polecenia lub Kreatora instalacji, należy użyć jednej z poniższych procedur.

### <a name="windows-agent"></a>Agent systemu Windows

#### <a name="uninstall-from-control-panel"></a>Odinstaluj z panelu sterowania
1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. W **Panelu sterowania** kliknij pozycję **programy i funkcje**.

3. W obszarze **programy i funkcje** kliknij pozycję **Microsoft Monitoring Agent**, kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **tak**.

>[!NOTE]
>Kreatora instalacji agenta można również uruchomić, klikając dwukrotnie plik **MMASetup- \<platform\> . exe**, który jest dostępny do pobrania z obszaru roboczego w Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstalowywanie z wiersza polecenia
Pobrany plik dla agenta to samodzielny pakiet instalacyjny utworzony przy użyciu programu IExpress. Program instalacyjny agenta i plików pomocniczych są zawarte w pakiecie i muszą zostać wyodrębnione w celu poprawnego odinstalowania przy użyciu wiersza polecenia pokazanego w poniższym przykładzie.

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Do wyodrębnienia plików instalacyjnych agenta z wiersza polecenia z podwyższonym poziomem uprawnień `extract MMASetup-<platform>.exe` i pojawi się monit o ścieżkę, do której mają zostać wyodrębnione pliki. Alternatywnie możesz określić ścieżkę, przekazując argumenty `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` . Aby uzyskać więcej informacji na temat przełączników wiersza polecenia obsługiwanych przez program IExpress, zobacz [przełączniki wiersza polecenia dla programu IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) , a następnie zaktualizujmy ten przykład do własnych potrzeb.

3. W wierszu polecenia wpisz polecenie `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` .

### <a name="linux-agent"></a>Agent systemu Linux
Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux. Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Skonfiguruj agenta do raportowania do Operations Manager grupy zarządzania

### <a name="windows-agent"></a>Agent systemu Windows
Wykonaj następujące kroki, aby skonfigurować Log Analytics agenta dla systemu Windows do raportowania do System Center Operations Manager grupy zarządzania.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Otwórz **Panel sterowania**.

3. Kliknij **Microsoft Monitoring Agent** a następnie kliknij kartę **Operations Manager** .

4. Jeśli serwery Operations Manager mają integrację z usługą Active Directory, kliknij pozycję **automatycznie Aktualizuj przypisania grupy zarządzania z AD DS**.

5. Kliknij przycisk **Dodaj** , aby otworzyć okno dialogowe **Dodawanie grupy zarządzania** .

6. W polu **Nazwa grupy zarządzania** wpisz nazwę grupy zarządzania.

7. W polu **podstawowy serwer zarządzania** wpisz nazwę komputera podstawowego serwera zarządzania.

8. W polu **port serwera zarządzania** wpisz numer portu TCP.

9. W obszarze **konto działania agenta** wybierz konto systemu lokalnego lub lokalne konto domeny.

10. Kliknij przycisk **OK** , aby zamknąć okno dialogowe **Dodawanie grupy zarządzania** , a następnie kliknij przycisk **OK** , aby zamknąć okno dialogowe **właściwości Microsoft Monitoring Agent** .

### <a name="linux-agent"></a>Agent systemu Linux
Wykonaj następujące kroki, aby skonfigurować agenta Log Analytics dla systemu Linux w celu raportowania do System Center Operations Manager grupy zarządzania.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edytuj plik `/etc/opt/omi/conf/omiserver.conf`

2. Upewnij się, że wiersz zaczynający się od `httpsport=` definiuje port 1270. Takie jak: `httpsport=1270`

3. Uruchom ponownie serwer OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Następne kroki

- Sprawdź [Rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta systemu Linux lub zarządzania nim.

- Sprawdź [Rozwiązywanie problemów z agentem systemu Windows,](agent-windows-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta systemu Windows lub zarządzania nim.
