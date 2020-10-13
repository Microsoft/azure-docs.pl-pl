---
title: Wdróż Azure File Sync | Microsoft Docs
description: Dowiedz się, jak wdrażać Azure File Sync od początku do końca przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: eda6e6b5ef2b68c55bf1f7f6ceb30bb6aea21d67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856351"
---
# <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync
Użyj Azure File Sync, aby scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

Zdecydowanie zalecamy zapoznanie się z [planowaniem wdrożenia Azure Files](storage-files-planning.md) i [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) przed wykonaniem kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Udział plików platformy Azure w tym samym regionie, w którym chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików](storage-how-to-create-file-share.md) , aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub systemu Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu Windows Server i zalecanych zasobów systemowych, zobacz [zagadnienia dotyczące serwera plików systemu Windows](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Udział plików platformy Azure w tym samym regionie, w którym chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików](storage-how-to-create-file-share.md) , aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub systemu Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu Windows Server i zalecanych zasobów systemowych, zobacz [zagadnienia dotyczące serwera plików systemu Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. Moduł AZ PowerShell może być używany z programem PowerShell 5,1 lub PowerShell 6 +. Możesz użyć polecenia AZ PowerShell module dla Azure File Sync w dowolnym obsługiwanym systemie, w tym w systemach innych niż Windows, jednak polecenie cmdlet rejestracji serwera musi być zawsze uruchamiane w zarejestrowanym wystąpieniu systemu Windows Server (można to zrobić bezpośrednio lub za pośrednictwem komunikacji zdalnej programu PowerShell). W systemie Windows Server 2012 R2 można sprawdzić, czy jest uruchomiony program PowerShell 5,1 lub nowszy. \* Przeglądając wartość właściwości **PSVersion** obiektu **$PSVersionTable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Jeśli wartość **PSVersion** jest mniejsza niż 5,1. \* , podobnie jak w przypadku większości świeżych instalacji systemu Windows Server 2012 R2, można łatwo przeprowadzić uaktualnienie, pobierając i instalując program [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616). Odpowiedni pakiet do pobrania i zainstalowania dla systemu Windows Server 2012 R2 to **Win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64. msu**. 

    Program PowerShell 6 może być używany z dowolnym obsługiwanym systemem i można go pobrać za pośrednictwem [strony usługi GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Jeśli planujesz korzystać z interfejsu użytkownika rejestracji serwera, zamiast rejestrować się bezpośrednio w programie PowerShell, musisz użyć programu PowerShell 5,1.

1. Jeśli wybrano opcję użycia programu PowerShell 5,1, należy się upewnić, że jest zainstalowany co najmniej program .NET 4.7.2. Dowiedz się więcej na temat [.NET Framework wersji i zależności](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) w systemie.

    > [!Important]  
    > W przypadku instalowania programu .NET 4.7.2 + w systemie Windows Server Core należy zainstalować z `quiet` `norestart` flagami i lub instalacja nie powiedzie się. Na przykład w przypadku instalowania programu .NET 4,8 polecenie będzie wyglądać następująco:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Moduł AZ PowerShell, który można zainstalować, postępując zgodnie z instrukcjami znajdującymi się tutaj: [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Moduł AZ. StorageSync jest teraz instalowany automatycznie podczas instalacji modułu AZ PowerShell.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Udział plików platformy Azure w tym samym regionie, w którym chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików](storage-how-to-create-file-share.md) , aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub systemu Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu Windows Server i zalecanych zasobów systemowych, zobacz [zagadnienia dotyczące serwera plików systemu Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

   Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym samouczku.  Azure Cloud Shell to interaktywne środowisko powłoki, które jest używane w przeglądarce.  Rozpocznij Cloud Shell przy użyciu jednej z następujących metod:

   - Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. **Spróbuje** otworzyć Azure Cloud Shell, ale nie kopiuje automatycznie kodu do Cloud Shell.

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu [Azure Portal](https://portal.azure.com)

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az-login).

   ```azurecli
   az login
   ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie [AZ FileSync](/cli/azure/ext/storagesync/storagesync) Azure CLI.

   ```azurecli
   az extension add --name storagesync
   ```

   Po zainstalowaniu odwołania do rozszerzenia **storagesync** zostanie wyświetlone następujące ostrzeżenie:

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
Dla każdego serwera, który ma być używany z Azure File Sync, włącznie z każdym węzłem serwera w klastrze trybu failover, wyłącz **konfigurację zwiększonych zabezpieczeń programu Internet Explorer**. Jest to wymagane tylko w przypadku początkowej rejestracji serwera. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Ten krok można pominąć, Jeśli wdrażasz Azure File Sync w systemie Windows Server Core.

1. Otwórz Menedżera serwera.
2. Kliknij pozycję **serwer lokalny**:  
    ![Pozycja „Serwer lokalny” po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. W okienku podrzędnym **Właściwości** wybierz link do funkcji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  
    ![Okienko „Konfiguracja zwiększonych zabezpieczeń” w interfejsie użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. W oknie dialogowym **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** wybierz pozycję **wyłączone** dla **administratorów** i **użytkowników**:  
    ![Okno podręczne „Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer” z wybraną pozycją „Wyłączono”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyłączyć konfigurację zwiększonych zabezpieczeń programu Internet Explorer, wykonaj następujące czynności z poziomu sesji programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Postępuj zgodnie z instrukcjami dla Azure Portal lub programu PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Wdrażanie usługi synchronizacji magazynu 
Wdrożenie Azure File Sync rozpoczyna się od umieszczenia zasobu **usługi synchronizacji magazynu** w grupie zasobów wybranej subskrypcji. Zalecamy obsługę administracyjną w razie konieczności. Zostanie utworzona relacja zaufania między serwerami a tym zasobem, a serwer może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu. W związku z tym zaleca się wdrożenie programu jako wielu usług synchronizacji magazynu, co jest potrzebne do oddzielenia grup serwerów. Należy pamiętać, że serwery z różnych usług synchronizacji magazynu nie mogą się ze sobą synchronizować.

> [!Note]
> Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu od subskrypcji i grupy zasobów, w której została wdrożona. Zalecamy dokładne sprawdzenie, kto ma do niego dostęp. Jednostki z dostępem do zapisu mogą rozpocząć Synchronizowanie nowych zestawów plików z serwerów zarejestrowanych w tej usłudze synchronizacji magazynu i spowodować, że dane będą przepływać do usługi Azure Storage, która jest dla nich dostępna.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby wdrożyć usługę synchronizacji magazynu, przejdź do [Azure Portal](https://portal.azure.com/), kliknij pozycję *Utwórz zasób* , a następnie wyszukaj Azure File Sync. W wynikach wyszukiwania wybierz pozycję **Azure File Sync**, a następnie wybierz pozycję **Utwórz** , aby otworzyć kartę **Wdróż magazyn synchronizacji** .

W otwartym okienku wprowadź następujące informacje:

- **Nazwa**: unikatowa nazwa (na region) usługi synchronizacji magazynu.
- **Subskrypcja**: subskrypcja, w której chcesz utworzyć usługę synchronizacji magazynu. W zależności od strategii konfiguracji w organizacji może być możliwe uzyskanie dostępu do co najmniej jednej subskrypcji. Subskrypcja platformy Azure to najbardziej podstawowy kontener dotyczący rozliczeń dla każdej usługi w chmurze (na przykład Azure Files).
- **Grupa zasobów**: Grupa zasobów to logiczna Grupa zasobów platformy Azure, na przykład konto magazynu lub usługa synchronizacji magazynu. Można utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów dla Azure File Sync. (Zalecamy używanie grup zasobów jako kontenerów do izolowania zasobów logicznie dla organizacji, takich jak grupowanie zasobów kadr lub zasobów dla określonego projektu).
- **Lokalizacja**: region, w którym ma zostać wdrożony Azure File Sync. Na tej liście są dostępne tylko Obsługiwane regiony.

Po zakończeniu wybierz pozycję **Utwórz** , aby wdrożyć usługę synchronizacji magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Zastąp `<Az_Region>` `<RG_Name>` wartości, i `<my_storage_sync_service>` własnymi wartościami, a następnie użyj następujących poleceń, aby utworzyć i wdrożyć usługę synchronizacji magazynu:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Postępuj zgodnie z instrukcjami dla Azure Portal lub programu PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Agenta można pobrać z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Po zakończeniu pobierania kliknij dwukrotnie pakiet MSI, aby uruchomić instalację agenta Azure File Sync.

> [!Important]  
> Jeśli zamierzasz używać Azure File Sync z klastrem trybu failover, Agent Azure File Sync musi być zainstalowany na każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowany do pracy z Azure File Sync.

Zalecamy wykonanie następujących czynności:
- Pozostaw domyślną ścieżkę instalacji (C:\Program Files\Azure\StorageSyncAgent), aby uprościć Rozwiązywanie problemów i konserwację serwera.
- Włącz Microsoft Update, aby zachować Azure File Sync aktualne. Wszystkie aktualizacje, do Azure File Sync agenta, w tym aktualizacje funkcji i poprawki, występują z Microsoft Update. Zalecamy zainstalowanie najnowszej aktualizacji do Azure File Sync. Aby uzyskać więcej informacji, zobacz [Azure File Sync Policy Update](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po zakończeniu instalacji agenta Azure File Sync zostanie automatycznie otwarty interfejs użytkownika rejestracji serwera. Musisz mieć usługę synchronizacji magazynu przed rejestracją; Zobacz następną sekcję, jak utworzyć usługę synchronizacji magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujący kod programu PowerShell, aby pobrać odpowiednią wersję agenta Azure File Sync dla systemu operacyjnego i zainstalować ją w systemie.

> [!Important]  
> Jeśli zamierzasz używać Azure File Sync z klastrem trybu failover, Agent Azure File Sync musi być zainstalowany na każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowany do pracy z Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Postępuj zgodnie z instrukcjami dla Azure Portal lub programu PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Rejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu
Zarejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu powoduje ustanowienie relacji zaufania między serwerem (lub klastrem) oraz usługą synchronizacji magazynu. Serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu i można go synchronizować z innymi serwerami i udziałami plików platformy Azure skojarzonymi z tą samą usługą synchronizacji magazynu.

> [!Note]
> Rejestracja serwera korzysta z poświadczeń platformy Azure, aby utworzyć relację zaufania między usługą synchronizacji magazynu i systemem Windows Server, jednak następnie serwer tworzy i używa własnej tożsamości, która jest ważna, dopóki serwer pozostaje zarejestrowany, a bieżący token sygnatury dostępu współdzielonego (SAS Storage) jest prawidłowy. Nie można wystawić nowego tokenu sygnatury dostępu współdzielonego na serwerze po wyrejestrowaniu serwera, co eliminuje możliwość uzyskiwania dostępu do udziałów plików platformy Azure w celu zatrzymywania synchronizacji.

Administrator, który zarejestruje serwer, musi być członkiem roli **zarządzania** lub **współautorem** dla danej usługi synchronizacji magazynu. Można to skonfigurować w obszarze **Access Control (IAM)** w Azure Portal dla usługi synchronizacji magazynu.

Istnieje również możliwość odróżnienia administratorów, którzy mogą rejestrować serwery z tych, które umożliwiają również Konfigurowanie synchronizacji w usłudze synchronizacji magazynu. W tym celu należy utworzyć rolę niestandardową, w której można wyświetlić listę administratorów, którzy mogą rejestrować tylko serwery i nadać roli niestandardowej następujące uprawnienia:

* "Microsoft. StorageSync/storageSyncServices/registeredServers/Write"
* "Microsoft. StorageSync/storageSyncServices/Read"
* "Microsoft. StorageSync/storageSyncServices/przepływy pracy/Odczyt"
* "Microsoft. StorageSync/storageSyncServices/przepływy pracy/operacje/Odczyt"

# <a name="portal"></a>[Portal](#tab/azure-portal)
Interfejs użytkownika rejestracji serwera powinien zostać otwarty automatycznie po zainstalowaniu agenta Azure File Sync. W przeciwnym razie można otworzyć go ręcznie w lokalizacji jego pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Gdy zostanie otwarty interfejs użytkownika rejestracji serwera, wybierz pozycję **Zaloguj** się, aby rozpocząć.

Po zalogowaniu zostanie wyświetlony monit o podanie następujących informacji:

![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subskrypcja platformy Azure**: subskrypcja zawierająca usługę synchronizacji magazynu (zobacz [wdrażanie usługi synchronizacji magazynu](#deploy-the-storage-sync-service)). 
- **Grupa zasobów**: Grupa zasobów zawierająca usługę synchronizacji magazynu.
- **Usługa synchronizacji magazynu**: nazwa usługi synchronizacji magazynu, która ma zostać zarejestrowana.

Po wybraniu odpowiednich informacji wybierz pozycję **zarejestruj** , aby zakończyć rejestrację serwera. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Postępuj zgodnie z instrukcjami dla Azure Portal lub programu PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać jeden punkt końcowy chmury, który reprezentuje udział plików platformy Azure, i co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanym serwerze. Serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji. Aby odpowiednio opisać żądaną topologię synchronizacji, można utworzyć dowolną liczbę grup synchronizacji.

Punkt końcowy w chmurze jest wskaźnikiem do udziału plików platformy Azure. Wszystkie punkty końcowe serwera będą synchronizowane z punktem końcowym w chmurze, co sprawia, że punkt końcowy w chmurze jest centrum. Konto magazynu dla udziału plików platformy Azure musi znajdować się w tym samym regionie co usługa synchronizacji magazynu. Zostanie zsynchronizowany pełny udział plików platformy Azure z jednym wyjątkiem: folder specjalny, porównywalny z ukrytym folderem "informacje o woluminie systemowym" na woluminie NTFS. Ten katalog jest nazywany ". SystemShareInformation". Zawiera ważne metadane synchronizacji, które nie będą synchronizowane z innymi punktami końcowymi. Nie używaj ani nie usuwaj!

> [!Important]  
> Można wprowadzać zmiany w dowolnym punkcie końcowym chmury lub w punkcie końcowym serwera w grupie synchronizacji i zsynchronizować pliki z innymi punktami końcowymi w grupie synchronizacji. Jeśli zmienisz bezpośrednio punkt końcowy w chmurze (udział plików platformy Azure), najpierw muszą zostać odnalezione zmiany za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, zobacz [Azure Files często zadawanych pytań](storage-files-faq.md#afs-change-detection).

Administrator tworzący punkt **końcowy w chmurze** musi być członkiem roli zarządzania konta magazynu zawierającego udział plików platformy Azure wskazywanym przez punkt końcowy w chmurze. Tę konfigurację można skonfigurować w obszarze **Access Control (IAM)** w Azure Portal dla konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć grupę synchronizacji, w [Azure Portal](https://portal.azure.com/)przejdź do usługi synchronizacji magazynu, a następnie wybierz pozycję **+ Grupa synchronizacji**:

![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

- **Nazwa grupy synchronizacji**: Nazwa grupy synchronizacji, która ma zostać utworzona. Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia.
- **Subskrypcja**: subskrypcja, w ramach której została wdrożona usługa synchronizacji magazynu w ramach [wdrażania usługi synchronizacji magazynu](#deploy-the-storage-sync-service).
- **Konto magazynu**: w przypadku wybrania **opcji wybierz konto magazynu**zostanie wyświetlone inne okienko, w którym można wybrać konto magazynu, które ma udział plików platformy Azure, z którym chcesz przeprowadzić synchronizację.
- **Udział plików platformy Azure**: nazwa udziału plików platformy Azure, z którym chcesz przeprowadzić synchronizację.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć grupę synchronizacji, wykonaj następujące polecenie programu PowerShell. Pamiętaj, aby zamienić na `<my-sync-group>` żądaną nazwę grupy synchronizacji.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po pomyślnym utworzeniu grupy synchronizacji można utworzyć punkt końcowy w chmurze. Pamiętaj, aby zamienić `<my-storage-account>` i uzyskać `<my-file-share>` oczekiwane wartości.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ storagesync Sync-Group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) , aby utworzyć nową grupę synchronizacji.  Aby określić domyślną grupę zasobów dla wszystkich poleceń interfejsu wiersza polecenia, użyj polecenie [AZ Configure](/cli/azure/reference-index#az-configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Użyj polecenia [AZ storagesync Sync-Group Cloud-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) , aby utworzyć nowy punkt końcowy w chmurze.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Tworzenie punktu końcowego serwera
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Punkt końcowy serwera musi być ścieżką na zarejestrowanym serwerze (a nie zainstalowanym udziałem) i korzystać z warstw w chmurze, ponieważ ścieżka musi znajdować się na woluminie niesystemowym. Magazyn dołączony do sieci (NAS) nie jest obsługiwany.

> [!NOTE]
> Zmiana ścieżki lub litery dysku po ustanowieniu punktu końcowego serwera na woluminie nie jest obsługiwana. Upewnij się, że na zarejestrowanym serwerze jest używana ścieżka końcowa.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby dodać punkt końcowy serwera, przejdź do nowo utworzonej grupy synchronizacji, a następnie wybierz pozycję **Dodaj punkt końcowy serwera**.

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

- **Zarejestrowano serwer**: Nazwa serwera lub klastra, w którym chcesz utworzyć punkt końcowy serwera.
- **Ścieżka**: ścieżka do systemu Windows Server, która ma zostać zsynchronizowana w ramach grupy synchronizacji.
- Obsługa **warstw w chmurze**: przełącznik umożliwiający włączenie lub wyłączenie obsługi warstw w chmurze. W przypadku obsługi warstw w chmurze nierzadko używane lub dostępne pliki można przystąpić do Azure Files.
- **Wolne miejsce w woluminie**: ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli ilość wolnego miejsca na woluminie jest ustawiona na 50% na woluminie z jednym punktem końcowym serwera, około połowy ilości danych jest warstwą Azure Files. Bez względu na to, czy włączono obsługę warstw w chmurze, udział plików platformy Azure zawsze ma kompletną kopię danych w grupie synchronizacji.

Aby dodać punkt końcowy serwera, wybierz pozycję **Utwórz**. Twoje pliki są teraz zsynchronizowane w ramach udziału plików platformy Azure i systemu Windows Server. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujące polecenia programu PowerShell, aby utworzyć punkt końcowy serwera, i pamiętaj o zastąpieniu `<your-server-endpoint-path>` i `<your-volume-free-space>` z żądanymi wartościami i sprawdź opcjonalne ustawienie opcjonalnych początkowych zasad pobierania.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath
}
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ storagesync Sync-Group Server-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) , aby utworzyć nowy punkt końcowy serwera.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurowanie ustawień zapory i sieci wirtualnej

### <a name="portal"></a>Portal
Jeśli chcesz skonfigurować usługę Azure File Sync do pracy z ustawieniami zapory i sieci wirtualnej, wykonaj następujące czynności:

1. W Azure Portal przejdź do konta magazynu, które chcesz zabezpieczyć.
1. Wybierz przycisk **zapory i sieci wirtualne** w menu po lewej stronie.
1. Wybierz opcję **wybrane sieci** w obszarze **Zezwalaj na dostęp z**.
1. Upewnij się, że serwery IP lub Sieć wirtualna są wymienione w odpowiedniej sekcji.
1. Upewnij się, że jest zaznaczone pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** .
1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

![Konfigurowanie ustawień zapory i sieci wirtualnej do pracy z usługą Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Dołączanie za pomocą Azure File Sync
Zalecane kroki do dołączenia na Azure File Sync w pierwszej kolejności bez przestojów podczas zachowywania pełnej wierności plików i listy kontroli dostępu (ACL) są następujące:
 
1. Wdróż usługę synchronizacji magazynu.
1. Utwórz grupę synchronizacji.
1. Zainstaluj agenta Azure File Sync na serwerze z pełnym zestawem danych.
1. Zarejestruj ten serwer i Utwórz punkt końcowy serwera w udziale. 
1. Pozwól na synchronizację wykonaj pełne przekazywanie do udziału plików platformy Azure (punkt końcowy w chmurze).  
1. Po zakończeniu wstępnego przekazywania Zainstaluj agenta Azure File Sync na każdym z pozostałych serwerów.
1. Utwórz nowe udziały plików na każdym z pozostałych serwerów.
1. W razie potrzeby utwórz punkty końcowe serwera w nowych udziałach plików przy użyciu zasad obsługi warstw w chmurze. (Ten krok wymaga dostępności dodatkowego magazynu dla konfiguracji początkowej).
1. Zezwól Azure File Sync agentowi na szybkie przywrócenie pełnej przestrzeni nazw bez rzeczywistego transferu danych. Po przeprowadzeniu pełnej synchronizacji przestrzeni nazw aparat synchronizacji wypełni miejsce na dysku lokalnym na podstawie zasad obsługi warstw w chmurze dla punktu końcowego serwera. 
1. Upewnij się, że synchronizacja została zakończona i przetestuj topologię zgodnie z potrzebami. 
1. Przekieruj użytkowników i aplikacje do tego nowego udziału.
1. Opcjonalnie można usunąć wszystkie zduplikowane udziały na serwerach.
 
Jeśli nie masz dodatkowego magazynu do wstępnego dołączenia i chcesz dołączyć do istniejących udziałów, możesz wstępnie wypełniać dane w udziałach plików platformy Azure. To podejście jest sugerowane, jeśli i tylko wtedy, gdy można zaakceptować przestoje i bezwzględnie zagwarantować brak zmian danych w udziałach serwera podczas początkowego procesu dołączania. 
 
1. Upewnij się, że dane na żadnym z serwerów nie mogą ulec zmianie podczas procesu dołączania.
1. Wstępne udziały plików platformy Azure z danymi serwera przy użyciu dowolnego narzędzia do transferu danych za pośrednictwem protokołu SMB. Robocopy, na przykład. Możesz również użyć AzCopy przez REST. Upewnij się, że używasz AzCopy z odpowiednimi przełącznikami, aby zachować sygnatury czasowe i atrybuty dla list ACL.
1. Utwórz topologię Azure File Sync z żądanymi punktami końcowymi serwera wskazującymi istniejące udziały.
1. Pozwól, aby synchronizacja zakończyła się na wszystkich punktach końcowych. 
1. Po zakończeniu uzgadniania można otworzyć udziały dla zmian.
 
Obecnie podejście poprzedzające Określanie wartości zawiera kilka ograniczeń — 
- Zmiany danych na serwerze przed pełnym uruchomieniem topologii synchronizacji mogą spowodować konflikty w punktach końcowych serwera.  
- Po utworzeniu punktu końcowego w chmurze Program Azure File Sync uruchamia proces wykrywania plików w chmurze przed rozpoczęciem synchronizacji początkowej. Czas potrzebny do ukończenia tego procesu zależy od różnych czynników, takich jak szybkość sieci, dostępna przepustowość i liczba plików i folderów. W przypadku przybliżonej oceny w wersji zapoznawczej proces wykrywania jest uruchamiany około 10 plików na sekundę.  W związku z tym nawet jeśli wstępne umieszczanie jest uruchamiane szybko, całkowity czas, w którym w pełni uruchomiony system, może być znacznie dłuższy, gdy dane są wstępnie umieszczane w chmurze.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Samoobsługowe Przywracanie za poorednictwem poprzednich wersji i usługi VSS (Usługa kopiowania woluminów w tle)

> [!IMPORTANT]
> Poniższe informacje mogą być używane tylko w programie w wersji 9 (lub nowszej) agenta synchronizacji magazynu. Wersje starsze niż 9 nie będą miały poleceń cmdlet StorageSyncSelfService.

Poprzednie wersje to funkcja systemu Windows, która umożliwia korzystanie z migawek VSS po stronie serwera, aby przedstawić dostępnych wersje pliku na kliencie SMB.
Zapewnia to zaawansowany scenariusz, często nazywany samoobsługowym przywracaniem, bezpośrednio dla pracowników przetwarzających informacje, a nie w zależności od przywracania od administratora IT.

Migawki i wcześniejsze wersje usługi VSS działają niezależnie od Azure File Sync. Jednak Obsługa warstw w chmurze musi być ustawiona na tryb zgodny. Wiele punktów końcowych serwera Azure File Sync może istnieć na tym samym woluminie. Należy wykonać następujące wywołania programu PowerShell dla każdego woluminu, który ma nawet jeden punkt końcowy serwera, na którym planujesz lub korzystasz z obsługi warstw w chmurze.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Migawki usługi VSS są pobierane z całego woluminu. Domyślnie do 64 migawek może istnieć dla danego woluminu, na którym jest wystarczająca ilość miejsca do przechowywania migawek. Usługa VSS obsługuje to automatycznie. Domyślny harmonogram migawek obejmuje dwie migawki dziennie, od poniedziałku do piątku. Ten harmonogram można skonfigurować za pomocą zaplanowanego zadania systemu Windows. Powyższe polecenie cmdlet programu PowerShell wykonuje dwie czynności:
1. Konfiguruje ona usługę Azure File Sync na określonym woluminie pod kątem zgodności z poprzednimi wersjami i gwarantuje, że plik można przywrócić z poprzedniej wersji, nawet jeśli został warstwowy w chmurze na serwerze. 
1. Włącza domyślny harmonogram usługi VSS. Później możesz zdecydować się na jego modyfikację. 

> [!Note]  
> Istnieją dwie ważne kwestie, na które należy zwrócić uwagę:
>- Jeśli zostanie użyty parametr-Force, a usługa VSS jest obecnie włączona, spowoduje to zastąpienie bieżącego harmonogramu migawek VSS i zamienienie go zgodnie z harmonogramem domyślnym. Przed uruchomieniem polecenia cmdlet upewnij się, że została zapisana Konfiguracja niestandardowa.
> - Jeśli używasz tego polecenia cmdlet w węźle klastra, musisz również uruchomić go na wszystkich innych węzłach w klastrze. 

Aby sprawdzić, czy jest włączona zgodność funkcji samoobsługowego przywracania, można uruchomić następujące polecenie cmdlet.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Będzie ona wyświetlać wszystkie woluminy na serwerze, a także liczbę dni zgodne z obsługą warstw w chmurze dla każdego z nich. Ta liczba jest obliczana automatycznie na podstawie maksymalnej liczby możliwych migawek na wolumin i domyślnego harmonogramu migawek. Domyślnie wszystkie poprzednie wersje prezentowane pracownikowi przetwarzającym informacje mogą służyć do przywracania danych z programu. To samo jest prawdziwe w przypadku zmiany domyślnego harmonogramu w celu wykonania większej liczby migawek.
Jeśli jednak zmienisz harmonogram w taki sposób, że będzie to możliwe, na woluminie, który jest starszy niż wartość zgodne dni, użytkownicy nie będą mogli używać tej starszej migawki (poprzedniej wersji) do przywracania z programu.

> [!Note]
> Włączenie przywracania samoobsługowego może mieć wpływ na użycie usługi Azure Storage i rozliczenia. Ten wpływ jest ograniczony do plików aktualnie warstwowych na serwerze. Włączenie tej funkcji gwarantuje, że w chmurze jest dostępna wersja pliku, do której można odwoływać się za pośrednictwem wpisów poprzednich wersji (migawek VSS).
>
> Jeśli wyłączysz tę funkcję, użycie usługi Azure Storage będzie wolno odrzucać do momentu przełączenia okna zgodnej liczby dni. Nie ma możliwości przyspieszenia tego działania. 

Domyślna maksymalna liczba migawek VSS na wolumin (64), a także harmonogram domyślny, który ma zostać uwzględniony, spowoduje to, że w ciągu maksymalnie 45 dni poprzednich wersji może zostać przywrócony pracownik przetwarzający informacje, w zależności od liczby migawek VSS, które można przechowywać na woluminie.

Jeśli maks. 64 migawek VSS na wolumin nie jest prawidłowym ustawieniem, można [zmienić tę wartość za pomocą klucza rejestru](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Aby nowe ograniczenie zaczęły obowiązywać, należy ponownie uruchomić polecenie cmdlet, aby włączyć zgodność poprzedniej wersji na każdym woluminie, który był wcześniej włączony, z flagą-Force, aby pobrać nową maksymalną liczbę migawek VSS na wolumin. Spowoduje to powstanie nowo obliczonej liczby zgodnych dni. Należy pamiętać, że ta zmiana zacznie obowiązywać tylko w przypadku nowych plików warstwowych i nadpisać wszelkie dostosowania w harmonogramie usługi VSS, który miał być wykonany.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrowanie wdrożenia Replikacja systemu plików DFS (DFS-R) do Azure File Sync
Aby przeprowadzić migrację wdrożenia systemu plików DFS-R do Azure File Sync:

1. Utwórz grupę synchronizacji, która będzie reprezentować zamienionej topologii systemu plików DFS-R.
2. Uruchom na serwerze, który ma pełen zestaw danych w topologii systemu plików DFS-R, aby przeprowadzić migrację. Zainstaluj Azure File Sync na tym serwerze.
3. Zarejestruj ten serwer i Utwórz punkt końcowy serwera dla pierwszego migrowanego serwera. Nie należy włączać obsługi warstw w chmurze.
4. Zezwól na synchronizację danych z udziałem plików platformy Azure (punkt końcowy w chmurze).
5. Zainstaluj i zarejestruj agenta Azure File Sync na każdym z pozostałych serwerów DFS-R.
6. Wyłącz system plików DFS-R. 
7. Utwórz punkt końcowy serwera na każdym serwerze z systemem plików DFS-R. Nie należy włączać obsługi warstw w chmurze.
8. Upewnij się, że synchronizacja została zakończona i przetestuj topologię zgodnie z potrzebami.
9. Wycofaj system plików DFS-R.
10. W razie potrzeby można teraz włączyć obsługę warstw w chmurze na dowolnym punkcie końcowym serwera.

Aby uzyskać więcej informacji, zobacz [Azure File Sync Interop with rozproszony system plików (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie punktu końcowego serwera Azure File Sync](storage-sync-files-server-endpoint.md)
- [Zarejestruj lub wyrejestruj serwer z Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
