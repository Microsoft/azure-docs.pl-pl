---
title: Wdrażanie Azure File Sync | Microsoft Docs
description: Dowiedz się, jak wdrażać Azure File Sync od początku do końca przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 284ef8de1c672fdc0a5bb1a996a3446010253f57
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816795"
---
# <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync
Użyj Azure File Sync, aby scentralizować udziały plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

Zdecydowanie zalecamy przeczytanie artykułów Planowanie [wdrożenia](../files/storage-files-planning.md) Azure Files [](file-sync-planning.md) wdrożenia i Planowanie wdrożenia Azure File Sync przed ukończeniem kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Udział plików platformy Azure w tym samym regionie, który chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionów](file-sync-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików,](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji o obsługiwanych wersjach systemu Windows Server i zalecanych zasobach systemowych, zobacz Zagadnienia dotyczące serwera plików [systemu Windows.](file-sync-planning.md#windows-file-server-considerations)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Udział plików platformy Azure w tym samym regionie, który chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionów](file-sync-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików,](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji o obsługiwanych wersjach systemu Windows Server i zalecanych zasobach systemowych, zobacz Zagadnienia dotyczące serwera plików [systemu Windows.](file-sync-planning.md#windows-file-server-considerations)

1. Modułu Az programu PowerShell można używać z programem PowerShell 5.1 lub PowerShell 6+. Modułu Az programu PowerShell dla programu Azure File Sync można używać w dowolnym obsługiwanym systemie, w tym w systemach innych niż Windows, jednak polecenie cmdlet rejestracji serwera zawsze musi być uruchamiane w zarejestrowanym wystąpieniu systemu Windows Server (można to zrobić bezpośrednio lub za pośrednictwem komunikacji zdalnej programu PowerShell). W systemie Windows Server 2012 R2 możesz sprawdzić, czy używasz programu PowerShell w wersji co najmniej 5.1. \* patrząc na wartość właściwości **PSVersion** obiektu **$PSVersionTable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Jeśli wartość **PSVersion** jest mniejsza niż 5.1. , tak jak w przypadku najbardziej nowych instalacji systemu Windows Server 2012 R2, możesz łatwo uaktualnić, pobierając i \* instalując program Windows Management Framework [(WMF) 5.1.](https://www.microsoft.com/download/details.aspx?id=54616) Odpowiedni pakiet do pobrania i zainstalowania dla systemu Windows Server 2012 R2 to **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu.** 

    Programu PowerShell 6+ można używać z dowolnym obsługiwanym systemem i można go pobrać za pośrednictwem strony [usługi GitHub.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Jeśli planujesz używać interfejsu użytkownika rejestracji serwera, a nie rejestracji bezpośrednio z programu PowerShell, musisz użyć programu PowerShell 5.1.

1. Jeśli zdecydujesz się na korzystanie z programu PowerShell 5.1, upewnij się, że zainstalowano co najmniej program .NET 4.7.2. Dowiedz się [więcej .NET Framework o różnych wersjach i](/dotnet/framework/migration-guide/versions-and-dependencies) zależnościach systemu.

    > [!Important]  
    > Jeśli instalujesz program .NET 4.7.2+ w systemie Windows Server Core, musisz zainstalować go z flagami i , w przypadku gdyby instalacja zakończyła się `quiet` `norestart` niepowodzeniem. Na przykład w przypadku instalowania programu .NET 4.8 polecenie będzie wyglądać następująco:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Moduł Az programu PowerShell, który można zainstalować, zgodnie z instrukcjami podanymi tutaj: Instalowanie i [konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Moduł Az.StorageSync jest teraz instalowany automatycznie podczas instalowania modułu Az programu PowerShell.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Udział plików platformy Azure w tym samym regionie, w którym chcesz wdrożyć Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionów](file-sync-planning.md#azure-file-sync-region-availability) dla Azure File Sync.
    - [Utwórz udział plików,](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) aby uzyskać szczegółowy opis sposobu tworzenia udziału plików.
1. Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub Windows Server do synchronizacji z Azure File Sync. Aby uzyskać więcej informacji o obsługiwanych wersjach systemu Windows Server i zalecanych zasobach systemowych, zobacz Zagadnienia dotyczące serwera plików [systemu Windows.](file-sync-planning.md#windows-file-server-considerations)

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

   Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym samouczku.  Azure Cloud Shell to interakcyjne środowisko powłoki, które można używać za pośrednictwem przeglądarki.  Uruchom Cloud Shell przy użyciu jednej z tych metod:

   - Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. **Próba spowoduje** otwarcie Azure Cloud Shell, ale nie spowoduje automatycznego skopiowania kodu do Cloud Shell.

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu okna [Azure Portal](https://portal.azure.com)

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az_login).

   ```azurecli
   az login
   ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie [az filesync interfejsu](/cli/azure/ext/storagesync/storagesync) wiersza polecenia platformy Azure.

   ```azurecli
   az extension add --name storagesync
   ```

   Po zainstalowaniu **odwołania do rozszerzenia storagesync** zostanie wyświetlony następujące ostrzeżenie.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
Dla każdego serwera, który ma być Azure File Sync, łącznie z każdym węzłem serwera w klastrze trybu failover, Internet Explorer **konfiguracja zwiększonych zabezpieczeń.** Jest to wymagane tylko w przypadku początkowej rejestracji serwera. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Możesz pominąć ten krok, jeśli wdrażasz program Azure File Sync w systemie Windows Server Core.

1. Otwórz Menedżera serwera.
2. Kliknij **pozycję Serwer lokalny:**  
    ![Pozycja „Serwer lokalny” po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. W okienku podrzędnym **Właściwości** wybierz link do funkcji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  
    ![Okienko „Konfiguracja zwiększonych zabezpieczeń” w interfejsie użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. W **oknie Internet Explorer oknie dialogowym Konfiguracja zwiększonych** zabezpieczeń wybierz pozycję **Wyłączone dla** **opcji Administratorzy** i **użytkownicy:**  
    ![Okno podręczne „Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer” z wybraną pozycją „Wyłączono”](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyłączyć konfigurację Internet Explorer zabezpieczeń rozszerzonych, wykonaj następujące czynności w sesji programu PowerShell z podwyższonym poziomem uprawnień:

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

Postępuj zgodnie z instrukcjami dotyczącymi Azure Portal programu PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Wdrażanie usługi synchronizacji magazynu 
Wdrażanie usługi Azure File Sync rozpoczyna się od umieszczenia zasobu usługi **synchronizacji** magazynu w grupie zasobów wybranej subskrypcji. Zalecamy aprowizowanie ich w razie potrzeby. Utworzysz relację zaufania między serwerami i tym zasobem, a serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu. W związku z tym zaleca się wdrożenie tylu usług synchronizacji magazynu, ile potrzeba do oddzielenia grup serwerów. Należy pamiętać, że serwery z różnych usług synchronizacji magazynu nie mogą być synchronizowane ze sobą.

> [!Note]
> Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu z subskrypcji i grupy zasobów, w których została wdrożona. Zalecamy dokładne sprawdzenie, kto ma do niego dostęp. Jednostki z dostępem do zapisu mogą rozpocząć synchronizowanie nowych zestawów plików z serwerów zarejestrowanych w tej usłudze synchronizacji magazynu i spowodować przepływ danych do dostępnej dla nich usługi Azure Storage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby wdrożyć usługę synchronizacji magazynu, przejdź do Azure Portal [,](https://portal.azure.com/)kliknij pozycję Utwórz zasób, *a* następnie wyszukaj Azure File Sync. W wynikach wyszukiwania wybierz pozycję Azure File Sync , **a** następnie wybierz pozycję **Utwórz,** aby otworzyć **kartę Wdrażanie synchronizacji** magazynu.

W otwartym okienku wprowadź następujące informacje:

- **Nazwa:** unikatowa nazwa (na region) dla usługi synchronizacji magazynu.
- **Subskrypcja:** subskrypcja, w której chcesz utworzyć usługę synchronizacji magazynu. W zależności od strategii konfiguracji organizacji możesz mieć dostęp do co najmniej jednej subskrypcji. Subskrypcja platformy Azure to najbardziej podstawowy kontener do rozliczeń dla każdej usługi w chmurze (na przykład Azure Files).
- **Grupa zasobów:** Grupa zasobów to logiczna grupa zasobów platformy Azure, taka jak konto magazynu lub usługa synchronizacji magazynu. Możesz utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów na Azure File Sync. (Zalecamy używanie grup zasobów jako kontenerów w celu logicznego izolowania zasobów dla organizacji, takich jak grupowanie zasobów kadrowych lub zasobów dla określonego projektu).
- **Lokalizacja:** region, w którym chcesz wdrożyć Azure File Sync. Na tej liście są dostępne tylko obsługiwane regiony.

Po zakończeniu wybierz pozycję Utwórz, **aby** wdrożyć usługę synchronizacji magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Zastąp wartości , i własnymi wartościami, a następnie użyj następujących poleceń, aby utworzyć `<Az_Region>` `<RG_Name>` i `<my_storage_sync_service>` wdrożyć usługę synchronizacji magazynu:

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

Postępuj zgodnie z instrukcjami dotyczącymi Azure Portal programu PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Agenta można pobrać z Centrum [pobierania Microsoft.](https://go.microsoft.com/fwlink/?linkid=858257) Po zakończeniu pobierania kliknij dwukrotnie pakiet MSI, aby rozpocząć instalację Azure File Sync agenta.

> [!Important]  
> Jeśli zamierzasz używać usługi Azure File Sync z klastrem trybu failover, agent Azure File Sync musi być zainstalowany w każdym węźle klastra. Każdy węzeł w klastrze musi być zarejestrowany do pracy z Azure File Sync.

Zalecamy wykonanie następujących czynności:
- Pozostaw domyślną ścieżkę instalacji (C:\Program Files\Azure\StorageSyncAgent), aby uprościć rozwiązywanie problemów i konserwację serwera.
- Włącz Microsoft Update, aby Azure File Sync aktualne. Wszystkie aktualizacje agenta Azure File Sync, w tym aktualizacje funkcji i poprawki, są Microsoft Update. Zalecamy zainstalowanie najnowszej aktualizacji w celu Azure File Sync. Aby uzyskać więcej informacji, [zobacz Azure File Sync zasad aktualizacji.](file-sync-planning.md#azure-file-sync-agent-update-policy)

Po zakończeniu Azure File Sync agenta rejestracji serwera zostanie automatycznie otwarty interfejs użytkownika rejestracji serwera. Przed zarejestrowaniem musisz mieć usługę synchronizacji magazynu; Zobacz następną sekcję, aby dowiedzieć się, jak utworzyć usługę synchronizacji magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujący kod programu PowerShell, aby pobrać odpowiednią wersję agenta Azure File Sync dla systemu operacyjnego i zainstalować go w systemie.

> [!Important]  
> Jeśli zamierzasz używać usługi Azure File Sync z klastrem trybu failover, agent Azure File Sync musi być zainstalowany w każdym węźle klastra. Każdy węzeł w klastrze musi być zarejestrowany do pracy z Azure File Sync.

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

Postępuj zgodnie z instrukcjami dotyczącymi Azure Portal programu PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Rejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu
Zarejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu powoduje ustanowienie relacji zaufania między serwerem (lub klastrem) oraz usługą synchronizacji magazynu. Serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu i można go synchronizować z innymi serwerami i udziałami plików platformy Azure skojarzonymi z tą samą usługą synchronizacji magazynu.

> [!Note]
> Rejestracja serwera używa poświadczeń platformy Azure do utworzenia relacji zaufania między usługą synchronizacji magazynu i systemem Windows Server, jednak następnie serwer tworzy i używa własnej tożsamości, która jest ważna, o ile serwer pozostaje zarejestrowany, a bieżący token sygnatury dostępu współdzielonego (SAS magazynu) jest prawidłowy. Po wyrejestrowyniu serwera nie można wystawiać nowego tokenu SYGNATURY dostępu współdzielonego, co pozwala serwerowi na dostęp do udziałów plików platformy Azure i zatrzymanie synchronizacji.

Administrator rejestrujący serwer musi być członkiem ról zarządzania **Właściciel** lub **Współautor** dla danej usługi synchronizacji magazynu. Można to skonfigurować w obszarze **Access Control (IAM)** w Azure Portal dla usługi synchronizacji magazynu.

Istnieje również możliwość rozróżnienia administratorów, którzy mogą rejestrować serwery, od tych, które mogą również konfigurować synchronizację w usłudze synchronizacji magazynu. W tym celu należy utworzyć rolę niestandardową, w której należy wyświetlić listę administratorów, którzy mogą rejestrować tylko serwery, i nadać roli niestandardowej następujące uprawnienia:

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[Portal](#tab/azure-portal)
Interfejs użytkownika rejestracji serwera powinien zostać otwarty automatycznie po Azure File Sync agenta. W przeciwnym razie można otworzyć go ręcznie w lokalizacji jego pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po otworeniu interfejsu użytkownika rejestracji serwera wybierz **pozycję Zaloguj się,** aby rozpocząć.

Po zalogowaniu się zostanie wyświetlony monit o następujące informacje:

![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subskrypcja platformy Azure:** subskrypcja zawierająca usługę synchronizacji magazynu (zobacz [Wdrażanie usługi synchronizacji magazynu).](#deploy-the-storage-sync-service) 
- **Grupa zasobów:** grupa zasobów zawierająca usługę synchronizacji magazynu.
- **Usługa synchronizacji magazynu:** nazwa usługi synchronizacji magazynu, za pomocą której chcesz się zarejestrować.

Po wybraniu odpowiednich informacji wybierz pozycję **Zarejestruj,** aby ukończyć rejestrację serwera. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Postępuj zgodnie z instrukcjami dotyczącymi Azure Portal programu PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać jeden punkt końcowy chmury, który reprezentuje udział plików platformy Azure, i co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanym serwerze. Serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji. Możesz utworzyć tyle grup synchronizacji, ile potrzebujesz, aby odpowiednio opisać żądaną topologię synchronizacji.

Punkt końcowy w chmurze jest wskaźnikiem do udziału plików platformy Azure. Wszystkie punkty końcowe serwera zostaną zsynchronizowane z punktem końcowym w chmurze, dzięki czemu punkt końcowy w chmurze stanie się centrum. Konto magazynu dla udziału plików platformy Azure musi znajdować się w tym samym regionie co usługa synchronizacji magazynu. Cały udział plików platformy Azure zostanie zsynchronizowany, z jednym wyjątkiem: zostanie aprow zawierający folder specjalny, porównywalny z ukrytym folderem "Informacje o woluminie systemowym" na woluminie NTFS. Ten katalog nosi nazwę ". SystemShareInformation". Zawiera on ważne metadane synchronizacji, które nie będą synchronizowane z innymi punktami końcowymi. Nie używaj ani nie usuwaj go!

> [!Important]  
> Możesz wprowadzać zmiany w dowolnym punkcie końcowym chmury lub punkcie końcowym serwera w grupie synchronizacji i synchronizować pliki z innymi punktami końcowymi w grupie synchronizacji. Jeśli bezpośrednio wprowadzasz zmiany w punkcie końcowym w chmurze (udział plików platformy Azure), zmiany należy najpierw wykryć za pomocą Azure File Sync wykrywania zmian. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, [zobacz Azure Files często zadawanych pytań.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)

Administrator tworzący punkt końcowy w chmurze musi być członkiem roli zarządzania **Właściciel** konta magazynu zawierającego udział plików platformy Azure, na który wskaże punkt końcowy chmury. Można to skonfigurować w obszarze **Access Control (IAM)** w Azure Portal konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć grupę synchronizacji, w [Azure Portal](https://portal.azure.com/)przejdź do usługi synchronizacji magazynu, a następnie wybierz **pozycję + Grupa synchronizacji:**

![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

- **Nazwa grupy synchronizacji:** nazwa grupy synchronizacji, która ma zostać utworzona. Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia.
- **Subskrypcja:** subskrypcja, w której wdrożono usługę synchronizacji magazynu w ramach [wdrażania usługi synchronizacji magazynu.](#deploy-the-storage-sync-service)
- **Konto magazynu:** w przypadku wybrania opcji Wybierz **konto** magazynu zostanie wyświetlone kolejne okienko, w którym można wybrać konto magazynu z udziałem plików platformy Azure, z którym chcesz synchronizować dane.
- **Udział plików platformy Azure:** nazwa udziału plików platformy Azure, z którym chcesz zsynchronizować.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć grupę synchronizacji, wykonaj następujący skrypt programu PowerShell. Pamiętaj, aby `<my-sync-group>` zastąpić odpowiednią nazwą grupy synchronizacji.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po pomyślnym utworzeniu grupy synchronizacji możesz utworzyć punkt końcowy w chmurze. Pamiętaj, aby zastąpić `<my-storage-account>` wartości `<my-file-share>` i oczekiwanymi wartościami.

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

Użyj polecenia [az storagesync sync-group,](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) aby utworzyć nową grupę synchronizacji.  Aby skonfigurować grupę zasobów dla wszystkich poleceń interfejsu wiersza polecenia, użyj [polecenia az configure](/cli/azure/reference-index#az_configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Użyj polecenia [az storagesync sync-group cloud-endpoint,](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) aby utworzyć nowy punkt końcowy w chmurze.

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
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Punkt końcowy serwera podlega następującym warunkiem:

- Punkt końcowy serwera musi być ścieżką na zarejestrowanym serwerze (a nie w zainstalowanym udziału). Magazyn dołączony do sieci (NAS) nie jest obsługiwany.
- Mimo że punkt końcowy serwera może być na woluminie systemowym, punkty końcowe serwera na woluminie systemowym mogą nie używać warstw w chmurze.
- Zmiana ścieżki lub litery dysku po nawiązyniu punktu końcowego serwera na woluminie nie jest obsługiwana. Upewnij się, że używasz końcowej ścieżki na zarejestrowanym serwerze.
- Zarejestrowany serwer może obsługiwać wiele punktów końcowych serwera, jednak grupa synchronizacji może w danym momencie mieć tylko jeden punkt końcowy serwera na zarejestrowany serwer. Inne punkty końcowe serwera w grupie synchronizacji muszą znajdować się na różnych zarejestrowanych serwerach.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby dodać punkt końcowy serwera, przejdź do nowo utworzonej grupy synchronizacji, a następnie wybierz **pozycję Dodaj punkt końcowy serwera**.

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

- **Zarejestrowany serwer:** nazwa serwera lub klastra, na którym chcesz utworzyć punkt końcowy serwera.
- **Ścieżka:** ścieżka systemu Windows Server do zsynchronizowania w ramach grupy synchronizacji.
- **Warstwy w chmurze:** przełącznik umożliwiający włączanie lub wyłączanie obsługi warstw w chmurze. W przypadku warstw w chmurze rzadko używane lub używane pliki, do których uzyskiwany jest dostęp, mogą być Azure Files.
- **Wolne miejsce na woluminie:** ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Jeśli na przykład ilość wolnego miejsca na woluminie jest ustawiona na 50% na woluminie, który ma punkt końcowy pojedynczego serwera, mniej więcej połowa ilości danych jest warstwowa na Azure Files. Niezależnie od tego, czy włączono obsługę warstw w chmurze, udział plików platformy Azure zawsze zawiera pełną kopię danych w grupie synchronizacji.
- **Tryb pobierania początkowego:** jest to opcjonalny wybór, począwszy od wersji 11 agenta, który może być przydatny, gdy w udziałach plików platformy Azure znajdują się pliki, ale nie na serwerze. Taka sytuacja może istnieć na przykład w przypadku utworzenia punktu końcowego serwera w celu dodania innego serwera biura oddziału do grupy synchronizacji lub odzyskiwania po awarii serwera, który zakończył się niepowodzeniem. Jeśli jest włączona funkcja obsługi warstw w chmurze, wartością domyślną jest tylko odwoływanie się do przestrzeni nazw, a początkowo nie ma zawartości pliku. Jest to przydatne, jeśli uważasz, że żądania dostępu użytkowników powinny decydować, która zawartość pliku jest przywołyowana na serwerze. Jeśli warstwy w chmurze są wyłączone, domyślnie przestrzeń nazw zostanie pobrana jako pierwsza, a następnie pliki zostaną odwołane na podstawie znacznika czasu ostatniej modyfikacji, dopóki nie zostanie osiągnięta pojemność lokalna. Można jednak zmienić tryb pobierania początkowego tylko na przestrzeń nazw. Trzeciego trybu można używać tylko w przypadku wyłączenia warstw w chmurze dla tego punktu końcowego serwera. Ten tryb pozwala uniknąć odwoływania się najpierw do przestrzeni nazw. Pliki będą wyświetlane na serwerze lokalnym tylko wtedy, gdy będą miały możliwość pełnego pobrania. Ten tryb jest przydatny, jeśli na przykład aplikacja wymaga, aby były obecne pełne pliki i nie można tolerować plików warstwowych w jej przestrzeni nazw.

Aby dodać punkt końcowy serwera, wybierz pozycję **Utwórz**. Pliki są teraz synchronizowane między udziałami plików platformy Azure i systemem Windows Server. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujące polecenia programu PowerShell, aby utworzyć punkt końcowy serwera, i zastąp elementy i żądanymi wartościami, a następnie sprawdź opcjonalne ustawienie opcjonalnych `<your-server-endpoint-path>` `<your-volume-free-space>` początkowych zasad pobierania.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az storagesync sync-group server-endpoint,](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) aby utworzyć nowy punkt końcowy serwera.

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
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurowanie ustawień zapory i sieci wirtualnej

### <a name="portal"></a>Portal
Jeśli chcesz skonfigurować synchronizację plików platformy Azure do pracy z ustawieniami zapory i sieci wirtualnej, wykonaj następujące czynności:

1. W Azure Portal przejdź do konta magazynu, które chcesz zabezpieczyć.
1. Wybierz **pozycję Sieć** w menu po lewej stronie.
1. W **obszarze Wybrane sieci** w obszarze **Zezwalaj na dostęp z .**
1. Upewnij się, że adres IP lub sieć wirtualna serwerów znajduje się na liście w **sekcji Zakres** adresów.
1. Upewnij **się, że zaznaczono pole usługi firmy Microsoft Zezwalaj zaufanym** użytkownikom na dostęp do tego konta magazynu.
1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

    ![Konfigurowanie ustawień zapory i sieci wirtualnej do pracy z usługą Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Dołączanie za pomocą Azure File Sync
Zalecane kroki dołączania do aplikacji Azure File Sync po raz pierwszy bez przestojów przy zachowaniu pełnej wierności plików i listy kontroli dostępu (ACL) są następujące:
 
1. Wdrażanie usługi synchronizacji magazynu.
1. Utwórz grupę synchronizacji.
1. Zainstaluj Azure File Sync agenta na serwerze z pełnym zestawem danych.
1. Zarejestruj ten serwer i utwórz punkt końcowy serwera w udziału. 
1. Pozwól synchronizować pełne przekazywanie do udziału plików platformy Azure (punktu końcowego w chmurze).  
1. Po zakończeniu początkowego przekazywania zainstaluj Azure File Sync na każdym z pozostałych serwerów.
1. Utwórz nowe udziały plików na każdym z pozostałych serwerów.
1. W razie potrzeby utwórz punkty końcowe serwera w nowych udziałach plików przy użyciu zasad warstw w chmurze. (Ten krok wymaga dodatkowego magazynu, aby był dostępny dla konfiguracji początkowej).
1. Pozwól Azure File Sync wykonać szybkie przywracanie pełnej przestrzeni nazw bez rzeczywistego transferu danych. Po pełnej synchronizacji przestrzeni nazw aparat synchronizacji wypełni miejsce na dysku lokalnym na podstawie zasad warstw w chmurze dla punktu końcowego serwera. 
1. Upewnij się, że synchronizacja jest ukończona, i przetestuj topologię zgodnie z potrzebami. 
1. Przekieruj użytkowników i aplikacje do tego nowego udziału.
1. Opcjonalnie możesz usunąć wszystkie zduplikowane udziały na serwerach.
 
Jeśli nie masz dodatkowego magazynu na początkowe dołączanie i chcesz dołączyć je do istniejących udziałów, możesz wstępnie zainicjować dane w udziałach plików platformy Azure. Takie podejście jest sugerowane tylko wtedy, gdy można zaakceptować przestój i całkowicie zagwarantować, że podczas początkowego procesu dołączania nie zostaną wprowadzone żadne zmiany danych w udziałach serwera. 
 
1. Upewnij się, że dane na żadnym z serwerów nie mogą ulec zmianie podczas procesu dołączania.
1. Wstępne iniekcją udziałów plików platformy Azure z danymi serwera przy użyciu dowolnego narzędzia transferu danych za pośrednictwem SMB. Na przykład robocopy. Możesz również użyć programu AzCopy za pośrednictwem rest. Upewnij się, że używasz programu AzCopy z odpowiednimi przełącznikami, aby zachować znaczniki czasu i atrybuty ACL.
1. Utwórz Azure File Sync z punktami końcowymi żądanego serwera, które wskazują istniejące udziały.
1. Pozwól synchronizacji zakończyć proces uzgadniania we wszystkich punktach końcowych. 
1. Po zakończeniu uzgadniania można otworzyć udziały dla zmian.
 
Obecnie podejście wstępnego iniekcjowania ma kilka ograniczeń: 
- Zmiany danych na serwerze przed pełnym uruchomieniem topologii synchronizacji mogą powodować konflikty w punktach końcowych serwera.  
- Po utworzeniu punktu końcowego w chmurze Azure File Sync proces wykrywania plików w chmurze przed rozpoczęciem synchronizacji początkowej. Czas ukończenia tego procesu różni się w zależności od różnych czynników, takich jak szybkość sieci, dostępna przepustowość oraz liczba plików i folderów. W celu oszacowania w wersji zapoznawczej proces wykrywania jest uruchamiany w przybliżeniu z 10 plikami/s.  W związku z tym nawet w przypadku szybkiego wstępnego wstępnego iniekcjowania całkowity czas uzyskania w pełni działającego systemu może być znacznie dłuższy, gdy dane są wstępnie iniekcjowane w chmurze.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Przywracanie samoobsługowe za pomocą poprzednich wersji i usługi VSS (Usługa kopiowania woluminów w tle)

> [!IMPORTANT]
> Poniższych informacji można używać tylko z wersją 9 (lub nowszą) agenta synchronizacji magazynu. Wersje starsze niż 9 nie będą mieć polecenia cmdlet StorageSyncSelfService.

Poprzednie wersje to funkcja systemu Windows, która umożliwia korzystanie z migawek VSS woluminu po stronie serwera w celu prezentowania przywracalnych wersji pliku klientowi SMB.
Umożliwia to zaawansowany scenariusz, często określany jako samoobsługowe przywracanie, bezpośrednio dla pracowników informacyjnych, a nie w zależności od przywracania od administratora IT.

Migawki usługi VSS i poprzednie wersje działają niezależnie od Azure File Sync. Jednak dla warstw w chmurze należy ustawić tryb zgodny. Wiele Azure File Sync końcowych serwera może istnieć na tym samym woluminie. Musisz wykonać następujące wywołanie programu PowerShell dla każdego woluminu, który ma nawet jeden punkt końcowy serwera, w którym planujesz lub używasz warstw w chmurze.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Migawki usługi VSS są wykonane dla całego woluminu. Domyślnie dla danego woluminu mogą istnieć maksymalnie 64 migawki, przy przyznanej wystarczającej ilości miejsca do przechowywania migawek. Usługa VSS obsługuje to automatycznie. Domyślny harmonogram migawek przyjmuje dwie migawki dziennie, od poniedziałku do piątku. Ten harmonogram można skonfigurować za pomocą zaplanowanego zadania systemu Windows. Powyższe polecenie cmdlet programu PowerShell robi dwie rzeczy:
1. Konfiguruje on warstwy w chmurze usługi Azure File Syncs na określonym woluminie tak, aby był zgodny z poprzednimi wersjami, i gwarantuje, że plik może zostać przywrócony z poprzedniej wersji, nawet jeśli został on warstwowy w chmurze na serwerze. 
1. Włącza domyślny harmonogram usługi VSS. Następnie możesz zdecydować się na jego modyfikację później. 

> [!Note]  
> Należy zwrócić uwagę na dwie ważne rzeczy:
>- Jeśli używasz parametru -Force, a usługa VSS jest obecnie włączona, zastąpi bieżący harmonogram migawek usługi VSS i zastąpi go harmonogramem domyślnym. Przed uruchomieniem polecenia cmdlet upewnij się, że konfiguracja niestandardowa jest zapisywana.
> - Jeśli używasz tego polecenia cmdlet w węźle klastra, musisz również uruchomić je na wszystkich innych węzłach w klastrze. 

Aby sprawdzić, czy włączono zgodność samoobsługowego przywracania, możesz uruchomić następujące polecenie cmdlet.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Zostanie wyświetlona lista wszystkich woluminów na serwerze oraz liczba dni zgodnych z warstwami w chmurze dla każdego z nich. Ta liczba jest obliczana automatycznie na podstawie maksymalnej liczby możliwych migawek na wolumin i domyślnego harmonogramu migawek. Dlatego domyślnie do przywracania można użyć wszystkich poprzednich wersji przedstawionych pracownikowi informacyjnemu. To samo dotyczy zmiany domyślnego harmonogramu w celu tworzenia większej liczby migawek.
Jeśli jednak zmienisz harmonogram w sposób, który spowoduje użycie dostępnej migawki na woluminie, który jest starszy niż zgodna wartość dni, użytkownicy nie będą mogli używać tej starszej migawki (poprzednia wersja) do przywrócenia.

> [!Note]
> Włączenie przywracania samoobsługowego może mieć wpływ na zużycie i opłaty za magazyn platformy Azure. Ten wpływ jest ograniczony do plików aktualnie warstwowych na serwerze. Włączenie tej funkcji zapewnia, że w chmurze jest dostępna wersja pliku, do których można się odwoływać za pośrednictwem wpisu z poprzednich wersji (migawki usługi VSS).
>
> Jeśli wyłączysz tę funkcję, zużycie magazynu platformy Azure będzie powoli spadać, dopóki nie upłynie okno zgodnych dni. Nie ma możliwości przyspieszenia tego. 

Domyślna maksymalna liczba migawek usługi VSS na wolumin (64), a także domyślny harmonogram ich tworzenia, co spowoduje, że proces roboczy informacji będzie w stanie przywrócić maksymalnie 45 dni poprzednich wersji, w zależności od liczby migawek usługi VSS, które można przechowywać na woluminie.

Jeśli maksymalnie 64 migawki usługi VSS na wolumin nie jest prawidłowym ustawieniem, możesz zmienić wartość za pomocą [klucza rejestru](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Aby nowy limit został włączony, należy ponownie uruchomić polecenie cmdlet , aby włączyć zgodność poprzedniej wersji na każdym woluminie, który był wcześniej włączony, z flagą -Force, aby uwzględnić nową maksymalną liczbę migawek usługi VSS na wolumin. Spowoduje to nowo obliczoną liczbę zgodnych dni. Należy pamiętać, że ta zmiana będzie obowiązywać tylko w przypadku nowo utworzonych plików warstwowych i zastąpi wszelkie dostosowania zgodnie z harmonogramem usługi VSS, które mogły zostać wprowadzone.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Aktywne odwoływanie nowych i zmienionych plików z udziału plików platformy Azure

W przypadku agenta w wersji 11 nowy tryb staje się dostępny w punkcie końcowym serwera. Ten tryb umożliwia firmom rozproszonym globalnie wstępne wypełnianie pamięci podręcznej serwera w regionie zdalnym, nawet zanim użytkownicy lokalni będą mieli dostęp do jakichkolwiek plików. Po włączeniu w punkcie końcowym serwera ten tryb spowoduje, że ten serwer odwoła pliki, które zostały utworzone lub zmienione w udziałach plików platformy Azure.

### <a name="scenario"></a>Scenariusz

Globalnie dystrybuowana firma ma oddziały w USA i Indiach. Rano (czas w USA) pracownicy tworzący informacje tworzą nowy folder i nowe pliki dla zupełnie nowego projektu i pracują nad nim przez cały dzień. Azure File Sync synchronizuje foldery i pliki z udziałem plików platformy Azure (punktem końcowym w chmurze). Pracownicy z informacjami w Indiach będą kontynuować pracę nad projektem w swojej strefie czasowej. Po przybyciu rano lokalny serwer z włączoną obsługą usługi Azure File Sync w Indiach musi mieć te nowe pliki dostępne lokalnie, aby zespół Indie wydajnie pracować z lokalną pamięcią podręczną. Włączenie tego trybu zapobiega wolniejszemu dostępowi do początkowego pliku z powodu odwołania na żądanie i umożliwia serwerowi aktywne odwoływanie plików natychmiast po ich zmianie lub utworzeniu w udziałach plików platformy Azure.

> [!IMPORTANT]
> Ważne jest, aby pamiętać, że śledzenie zmian w udziałach plików platformy Azure, które są blisko serwera, może zwiększyć ruch wychodzący i opłaty z platformy Azure. Jeśli pliki przywołyne na serwerze nie są faktycznie potrzebne lokalnie, niepotrzebne odwoływanie do serwera może mieć negatywne konsekwencje. Użyj tego trybu, jeśli wiesz, że wstępne wypełnienie pamięci podręcznej na serwerze ostatnimi zmianami w chmurze będzie mieć pozytywny wpływ na użytkowników lub aplikacje korzystające z plików na tym serwerze.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Włączanie punktu końcowego serwera w celu aktywnego odwoływania się do zmian w udziałach plików platformy Azure

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. W witrynie Azure Portal przejdź do usługi synchronizacji magazynu, wybierz prawidłową grupę synchronizacji, [a](https://portal.azure.com/)następnie zidentyfikuj punkt końcowy serwera, dla którego chcesz ściśle śledzić zmiany w udziałach plików platformy Azure (punkcie końcowym w chmurze).
1. W sekcji dotyczącej warstw w chmurze znajdź temat "Pobieranie udziału plików platformy Azure". Zostanie wyświetlony aktualnie wybrany tryb i będzie można go zmienić, aby dokładniej śledzić zmiany udziałów plików platformy Azure i proaktywnie przywoływować je na serwerze.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Obraz przedstawiający zachowanie pobierania udziału plików platformy Azure dla aktualnie dostępnego punktu końcowego serwera oraz przycisk umożliwiający otwarcie menu, które umożliwia jego zmianę.":::

# <a name="powershell"></a>[Program PowerShell](#tab/proactive-powershell)

Właściwości punktu końcowego serwera można modyfikować w programie PowerShell za pomocą polecenia cmdlet [Set-AzStorageSyncServerEndpoint.](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint)

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrowanie wdrożenia Replikacja systemu plików DFS (DFS-R) do Azure File Sync
Aby przeprowadzić migrację wdrożenia systemu plików DFS-R do Azure File Sync:

1. Utwórz grupę synchronizacji reprezentującą topologię systemu plików DFS-R, która jest zastępowa.
1. Uruchom na serwerze, który ma pełny zestaw danych w topologii systemu plików DFS-R do migracji. Zainstaluj Azure File Sync na tym serwerze.
1. Zarejestruj ten serwer i utwórz punkt końcowy serwera dla pierwszego serwera, który ma zostać zmigrowany. Nie włączaj obsługi warstw w chmurze.
1. Pozwól na synchronizowanie wszystkich danych z twoim udziałem plików platformy Azure (punktem końcowym w chmurze).
1. Zainstaluj i zarejestruj agenta Azure File Sync na każdym z pozostałych serwerów DFS-R.
1. Wyłącz funkcję DFS-R. 
1. Utwórz punkt końcowy serwera na każdym serwerze DFS-R. Nie włączaj obsługi warstw w chmurze.
1. Upewnij się, że synchronizacja jest ukończona, i przetestuj topologię zgodnie z potrzebami.
1. Wycofanie systemu plików DFS-R.
1. Obsługę warstw w chmurze można teraz włączyć w dowolnym punkcie końcowym serwera zgodnie z potrzebami.

Aby uzyskać więcej informacji, [zobacz Azure File Sync między sobą z rozproszony system plików (DFS).](file-sync-planning.md#distributed-file-system-dfs)

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie punktu końcowego Azure File Sync Serwera](file-sync-server-endpoint.md)
- [Zarejestruj lub wyrejestruj serwer za pomocą Azure File Sync](file-sync-server-registration.md)
- [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)