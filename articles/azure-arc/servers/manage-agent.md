---
title: Zarządzanie usługą Azure ARC dla serwerów (wersja zapoznawcza) Agent
description: W tym artykule opisano różne zadania zarządzania, które zwykle są wykonywane w cyklu życia usługi Azure ARC dla serwerów podłączonych do agenta.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81308968"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Zarządzanie agentem podłączonego komputera i ich obsługa

Po początkowym wdrożeniu usługi Azure ARC dla serwerów (wersja zapoznawcza) Agent połączonej maszyny dla systemu Windows lub Linux może zajść potrzeba ponownego skonfigurowania agenta, uaktualnienia go lub usunięcia z komputera, jeśli osiągnął etap wycofania w jego cyklu życia. Można łatwo zarządzać tymi rutynowymi zadaniami konserwacji ręcznie lub przy użyciu automatyzacji, co zmniejsza zarówno błąd operacyjny, jak i wydatki.

## <a name="upgrading-agent"></a>Uaktualnianie agenta

Agenta połączonego z platformą Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od wymagań. W poniższej tabeli opisano metody, które są obsługiwane w celu przeprowadzenia uaktualnienia agenta.

| System operacyjny | Metoda uaktualniania |
|------------------|----------------|
| Windows | Ręczne<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [użyciu narzędzia zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agent systemu Windows

Aby zaktualizować agenta na komputerze z systemem Windows do najnowszej wersji, Agent jest dostępny w Microsoft Update i można go wdrożyć przy użyciu istniejącego procesu zarządzania aktualizacjami oprogramowania. Może być również uruchamiany ręcznie z wiersza polecenia, z skryptu lub innego rozwiązania do automatyzacji lub z Kreatora interfejsu użytkownika przez wykonanie `AzureConnectedMachine.msi`. 

> [!NOTE]
> * Aby uaktualnić agenta, musisz mieć uprawnienia *administratora* .
> * Aby uaktualnić ręcznie, należy najpierw pobrać i skopiować pakiet Instalatora do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. 

Jeśli nie masz doświadczenia z opcjami wiersza polecenia dla Instalator Windows pakietów, przejrzyj [standardowe opcje wiersza polecenia](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) i [Opcje wiersza polecenia programu Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby przeprowadzić uaktualnienie przy użyciu Kreatora instalacji

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **AzureConnectedMachineAgent. msi** , aby uruchomić Kreatora instalacji.

Kreator instalacji wykrywa, czy Poprzednia wersja istnieje, a następnie automatycznie wykonuje uaktualnienie agenta. Po zakończeniu uaktualniania Kreator instalacji zostanie automatycznie zamknięty.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby uaktualnić agenta w trybie dyskretnym i utworzyć plik dziennika instalacji w `C:\Support\Logs` folderze, uruchom następujące polecenie.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agent systemu Linux

Aby zaktualizować agenta na komputerze z systemem Linux do najnowszej wersji, obejmuje on dwa polecenia. Jedno polecenie aktualizowania indeksu pakietu lokalnego przy użyciu listy najnowszych dostępnych pakietów z repozytoriów i jednego polecenia w celu uaktualnienia pakietu lokalnego. 

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

Akcje polecenia [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/dpkg.log` dziennika.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uaktualnienie Red Hat/CentOS/Amazon Linux

1. Aby zaktualizować indeks pakietu lokalnego przy użyciu najnowszych zmian wprowadzonych w repozytoriach, uruchom następujące polecenie:

    ```bash
    yum check-update
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    yum update
    ```

Akcje polecenia [yum](https://access.redhat.com/articles/yum-cheat-sheet) , takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/yum.log` dziennika. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uaktualnianie systemu SUSE Linux Enterprise

1. Aby zaktualizować indeks pakietu lokalnego przy użyciu najnowszych zmian wprowadzonych w repozytoriach, uruchom następujące polecenie:

    ```bash
    zypper refresh
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    zypper update
    ```

Akcje polecenia [użyciu narzędzia zypper](https://en.opensuse.org/Portal:Zypper) , takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/zypper.log` dziennika. 

## <a name="about-the-azcmagent-tool"></a>Informacje o narzędziu Azcmagent

Narzędzie Azcmagent (Azcmagent. exe) służy do konfigurowania usługi Azure ARC dla serwerów (wersja zapoznawcza) podczas instalacji lub modyfikacji konfiguracji początkowej agenta po zakończeniu instalacji. Azcmagent. exe dostarcza parametrów wiersza polecenia, aby dostosować agenta i wyświetlić jego stan:

* **Połącz** , aby połączyć maszynę z usługą Azure Arc

* **Odłącz** , aby rozłączyć maszynę z usługi Azure Arc

* **Połącz ponownie** — aby ponownie połączyć rozłączoną maszynę z usługą Azure Arc

* **Pokaż** — Wyświetlanie stanu agenta i jego właściwości konfiguracji (nazwa grupy zasobów, Identyfikator subskrypcji, wersja itp.), która może pomóc w rozwiązaniu problemu z agentem.

* **-h lub--help** -wyświetla dostępne parametry wiersza polecenia

    Na przykład aby wyświetlić szczegółową pomoc dotyczącą parametru **reconnect** , wpisz `azcmagent reconnect -h`polecenie. 

* **-v lub--verbose** -Włącz pełne rejestrowanie

Można ręcznie wykonać **połączenie**, **rozłączyć**i **ponownie nawiązać połączenie** , a jednocześnie zalogować się przy użyciu tej samej jednostki usługi, która została użyta w celu dołączenia wielu agentów lub [tokenu dostępu](../../active-directory/develop/access-tokens.md)platformy tożsamości firmy Microsoft. Jeśli nie korzystasz z jednostki usługi w celu zarejestrowania maszyny przy użyciu usługi Azure ARC dla serwerów (wersja zapoznawcza), zapoznaj się z poniższym [artykułem](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) , aby utworzyć nazwę główną usługi.

### <a name="connect"></a>Połącz

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę utworzoną na platformie Azure. Zasób należy do określonej subskrypcji i grupy zasobów, a dane dotyczące maszyny są przechowywane w regionie świadczenia usługi Azure określonym przez to `--location` ustawienie. Domyślna nazwa zasobu jest nazwą hosta tej maszyny, jeśli nie została określona.

Certyfikat odpowiadający tożsamości przypisanej do systemu komputera jest następnie pobierany i przechowywany lokalnie. Po zakończeniu tego kroku, Metadata Service i Agent konfiguracji gościa platformy Azure rozpocznie synchronizację z usługą Azure ARC dla serwerów (wersja zapoznawcza).

Aby nawiązać połączenie przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby nawiązać połączenie z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Rozłącz

Ten parametr określa zasób w Azure Resource Manager reprezentujący maszynę usuniętą z platformy Azure. Agent nie jest usuwany z komputera. należy to zrobić w osobnym kroku. Jeśli komputer zostanie odłączony, jeśli chcesz go ponownie zarejestrować za pomocą usługi Azure ARC dla serwerów (wersja zapoznawcza) `azcmagent connect` , użyj tego elementu, aby utworzyć nowy zasób na platformie Azure.

Aby odłączyć się przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby rozłączyć się przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent disconnect --access-token <accessToken>`

Aby rozłączyć się z poświadczeniami logowania z podniesionymi uprawnieniami (Interactive), uruchom następujące polecenie:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Ponowne łączenie

Ten parametr służy do łączenia już zarejestrowanej lub podłączonej maszyny z usługą Azure ARC dla serwerów (wersja zapoznawcza). Może to być konieczne, jeśli maszyna została wyłączona, co najmniej 45 dni, aby jej certyfikat wygaśnie. Ten parametr używa podanych opcji uwierzytelniania do pobrania nowych poświadczeń odpowiadających zasobowi Azure Resource Manager reprezentującemu ten komputer.

To polecenie wymaga wyższych uprawnień niż rola [dołączania maszyny połączonej z platformą Azure](overview.md#required-permissions) .

Aby ponownie nawiązać połączenie przy użyciu nazwy głównej usługi, uruchom następujące polecenie:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby ponownie nawiązać połączenie przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent reconnect --access-token <accessToken>`

Aby ponownie nawiązać połączenie z poświadczeniami logowania z podwyższonym poziomem uprawnień (Interactive), uruchom następujące polecenie:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Usuwanie agenta

Wykonaj jedną z następujących metod, aby odinstalować agenta połączonego komputera z systemem Windows lub Linux z komputera. Usunięcie agenta nie spowoduje wyrejestrowania maszyny z opcją ARC dla serwerów (wersja zapoznawcza). jest to oddzielny proces wykonywany, gdy nie jest już konieczne zarządzanie maszyną na platformie Azure.

### <a name="windows-agent"></a>Agent systemu Windows

Obie następujące metody usuwają agenta, ale nie usuwają folderu *C:\Program Files\AzureConnectedMachineAgent* na komputerze.

#### <a name="uninstall-from-control-panel"></a>Odinstaluj z panelu sterowania

1. Aby odinstalować agenta systemu Windows z komputera, wykonaj następujące czynności:

    a. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administratora.  
    b. W **Panelu sterowania**wybierz **aplet Programy i funkcje**.  
    c. W obszarze **programy i funkcje**wybierz pozycję **Agent połączonej maszyny Azure**, wybierz pozycję **Odinstaluj**, a następnie wybierz pozycję **tak**.  

    >[!NOTE]
    > Możesz również uruchomić Kreatora instalacji agenta, klikając dwukrotnie pakiet Instalatora **AzureConnectedMachineAgent. msi** .

#### <a name="uninstall-from-the-command-line"></a>Odinstalowywanie z wiersza polecenia

Aby odinstalować agenta ręcznie z wiersza polecenia lub użyć metody zautomatyzowanej, takiej jak skrypt, można użyć poniższego przykładu. Najpierw należy pobrać kod produktu, który jest identyfikatorem GUID, który jest identyfikatorem podmiotu zabezpieczeń pakietu aplikacji, z systemu operacyjnego. Odinstalowywanie odbywa się przy użyciu wiersza polecenia msiexec. exe- `msiexec /x {Product Code}`.
    
1. Otwórz Edytor rejestru.

2. W obszarze klucz `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`rejestru znajdź i skopiuj identyfikator GUID kodu produktu.

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

Jeśli planujesz zatrzymać zarządzanie maszyną za pomocą usług pomocniczych na platformie Azure, wykonaj następujące kroki, aby wyrejestrować maszynę z użyciem usługi ARC dla serwerów (wersja zapoznawcza). Te kroki można wykonać wcześniej lub po usunięciu z komputera agenta podłączonego maszyny.

1. Otwórz usługę Azure ARC dla serwerów (wersja zapoznawcza), przechodząc do [Azure Portal](https://aka.ms/hybridmachineportal).

2. Wybierz maszynę z listy, wybierz wielokropek (**...**), a następnie wybierz pozycję **Usuń**.
