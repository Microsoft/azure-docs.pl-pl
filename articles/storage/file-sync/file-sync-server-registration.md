---
title: Zarządzanie zarejestrowanymi serwerami za pomocą Azure File Sync | Microsoft Docs
description: Dowiedz się, jak zarejestrować i wyrejestrować system Windows Server za pomocą usługi synchronizacji Azure File Sync Storage.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a85fb653636333beec5f53912a646b3e5619e37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797225"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Zarządzanie zarejestrowanymi serwerami za pomocą Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. W tym celu można przekształcić serwery z systemem Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W poniższym artykule pokazano, jak zarejestrować serwer i zarządzać nim za pomocą usługi synchronizacji magazynu. Zobacz [How to deploy Azure File Sync](file-sync-deployment-guide.md) (Jak wdrożyć Azure File Sync), aby uzyskać informacje na temat Azure File Sync wdrażania aplikacji na wszystkich platformach.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Rejestrowanie/wyrejestrowanie serwera w usłudze synchronizacji magazynu
Zarejestrowanie serwera w usłudze Azure File Sync ustanawia relację zaufania między systemem Windows Server i platformą Azure. Ta relacja może następnie  służyć do tworzenia punktów końcowych serwera na serwerze, które reprezentują określone foldery, które powinny być synchronizowane z udziałem plików platformy Azure (nazywanym również punktem *końcowym w chmurze).* 

### <a name="prerequisites"></a>Wymagania wstępne
Aby zarejestrować serwer w usłudze synchronizacji magazynu, należy najpierw przygotować serwer z niezbędnymi warunkami wstępnymi:

* Na serwerze musi działać obsługiwana wersja systemu Windows Server. Aby uzyskać więcej informacji, [zobacz Azure File Sync wymagania systemowe i współdziałanie](file-sync-planning.md#windows-file-server-considerations).
* Upewnij się, że wdrożono usługę synchronizacji magazynu. Aby uzyskać więcej informacji na temat wdrażania usługi synchronizacji magazynu, zobacz [Jak wdrożyć usługę Azure File Sync](file-sync-deployment-guide.md).
* Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna.
* Wyłącz konfigurację zwiększonych zabezpieczeń programu IE dla administratorów za pomocą Menedżer serwera użytkownika.
    
    ![Menedżer serwera użytkownika z wyróżniona konfiguracja zwiększonych zabezpieczeń programu IE](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Upewnij się, Azure PowerShell zainstalowany na serwerze. Jeśli serwer jest członkiem klastra trybu failover, każdy węzeł w klastrze będzie wymagał modułu Az. Więcej szczegółowych informacji na temat sposobu instalowania modułu Az można znaleźć na stronie Instalowanie i [konfigurowanie Azure PowerShell.](/powershell/azure/install-Az-ps)

    > [!Note]  
    > Zalecamy użycie najnowszej wersji modułu Az programu PowerShell do zarejestrowania/wyrejestrniania serwera. Jeśli pakiet Az został wcześniej zainstalowany na tym serwerze (a wersja programu PowerShell na tym serwerze ma wartość 5.* lub nowszą), możesz zaktualizować ten pakiet za pomocą polecenia `Update-Module` cmdlet . 
* Jeśli korzystasz z serwera proxy sieci w swoim środowisku, skonfiguruj ustawienia serwera proxy na serwerze, z których będzie korzystać agent synchronizacji.
    1. Określanie adresu IP serwera proxy i numeru portu
    2. Edytuj te dwa pliki:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Dodaj wiersze na rysunku 1 (poniżej tej sekcji) w obszarze /System.ServiceModel w powyższych dwóch plikach, zmieniając adres 127.0.0.1:8888 na poprawny adres IP (zastąp 127.0.0.1) i popraw numer portu (zastąp 8888):
    4. Ustaw ustawienia serwera proxy WinHTTP za pomocą wiersza polecenia:
        * Pokaż serwer proxy: netsh winhttp show proxy
        * Ustaw serwer proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Resetowanie serwera proxy: netsh winhttp reset proxy
        * Jeśli jest to konfiguracja po zainstalowaniu agenta, uruchom ponownie naszego agenta synchronizacji: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Rejestrowanie serwera w usłudze synchronizacji magazynu
Aby można było użyć  serwera jako punktu końcowego serwera w grupie Azure File Sync *synchronizacji,* należy go zarejestrować w usłudze *synchronizacji magazynu.* Jednocześnie serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu.

#### <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
1. [Pobierz agenta Azure File Sync .](https://go.microsoft.com/fwlink/?linkid=858257)
2. Uruchom instalatora Azure File Sync agenta.
    
    ![Pierwsze okienko instalatora Azure File Sync agenta](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Pamiętaj, aby włączyć aktualizacje agenta Azure File Sync przy użyciu Microsoft Update. Jest to ważne, ponieważ krytyczne poprawki zabezpieczeń i ulepszenia funkcji pakietu serwera są dostarczane za pośrednictwem Microsoft Update.

    ![Upewnij Microsoft Update, że włączono Microsoft Update okienku Azure File Sync instalatora agenta](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Jeśli serwer nie został wcześniej zarejestrowany, interfejs użytkownika rejestracji serwera pojawi się natychmiast po zakończeniu instalacji.

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, agent Azure File Sync musi być zainstalowany w każdym węźle klastra.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Rejestrowanie serwera przy użyciu interfejsu użytkownika rejestracji serwera
1. Jeśli interfejs użytkownika rejestracji serwera nie został uruchomiony natychmiast po zakończeniu instalacji agenta usługi Azure File Sync, można go uruchomić ręcznie, wykonując `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Kliknij *pozycję Zaloguj się,* aby uzyskać dostęp do subskrypcji platformy Azure. 

    ![Otwieranie okna dialogowego interfejsu użytkownika rejestracji serwera](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wybierz poprawną subskrypcję, grupę zasobów i usługę synchronizacji magazynu z okna dialogowego.

    ![Informacje o usłudze synchronizacji magazynu](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. W wersji zapoznawczej do ukończenia tego procesu jest wymagane jeszcze jedno logowanie. 

    ![Okno dialogowe logowania](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, każdy serwer musi uruchomić rejestrację serwera. Podczas wyświetlania zarejestrowanych serwerów w witrynie Azure Portal program Azure File Sync automatycznie rozpoznaje każdy węzeł jako członka tego samego klastra trybu failover i odpowiednio je grupuje.

#### <a name="register-the-server-with-powershell"></a>Rejestrowanie serwera przy użyciu programu PowerShell
Rejestrację serwera można również przeprowadzić za pomocą programu PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Wyrejestrowanie serwera z usługą synchronizacji magazynu
Istnieje kilka kroków, które są wymagane do wyrejestrowania serwera z usługą synchronizacji magazynu. Przyjrzyjmy się, jak prawidłowo wyrejestrować serwer.

> [!Warning]  
> Nie należy próbować rozwiązywać problemów z synchronizacją, warstwami w chmurze ani żadnym innym aspektem usługi Azure File Sync przez wyrejestrowanie i zarejestrowanie serwera albo usunięcie i ponowne zarejestrowanie punktów końcowych serwera, chyba że zostanie to jawnie zalecane przez inżyniera firmy Microsoft. Wyrejestrowanie serwera i usunięcie punktów końcowych serwera jest operacją destruktywną, a pliki warstwowe na woluminach z punktami końcowymi serwera nie zostaną "ponownie nawiązane" do ich lokalizacji w udziałach plików platformy Azure po ponownym utworzeniem zarejestrowanych serwerów i punktów końcowych serwera, co spowoduje błędy synchronizacji. Należy również zauważyć, że pliki warstwowe, które istnieją poza przestrzenią nazw punktu końcowego serwera, mogą zostać trwale utracone. Pliki warstwowe mogą istnieć w punktach końcowych serwera, nawet jeśli nie włączono obsługi warstw w chmurze.

#### <a name="optional-recall-all-tiered-data"></a>(Opcjonalnie) Odwoływanie wszystkich danych warstwowych
Jeśli chcesz, aby pliki, które są obecnie warstwowe, były dostępne po usunięciu programu Azure File Sync (tj. jest to środowisko produkcyjne, a nie testowe), odwołaj wszystkie pliki na każdym woluminie zawierającym punkty końcowe serwera. Wyłącz obsługę warstw w chmurze dla wszystkich punktów końcowych serwera, a następnie uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Jeśli wolumin lokalny hostowany przez punkt końcowy serwera nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, `Invoke-StorageSyncFileRecall` polecenie cmdlet nie powiedzie się.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Usuwanie serwera ze wszystkich grup synchronizacji
Przed wyrejestrowaniem serwera w usłudze synchronizacji magazynu należy usunąć wszystkie punkty końcowe serwera na tym serwerze. Można to zrobić za pomocą Azure Portal:

1. Przejdź do usługi synchronizacji magazynu, w której zarejestrowano serwer.
2. Usuń wszystkie punkty końcowe serwera dla tego serwera w każdej grupie synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Można to również zrobić za pomocą prostego skryptu programu PowerShell:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Wyrejestruj serwer
Teraz, gdy wszystkie dane zostały odwołane i serwer został usunięty ze wszystkich grup synchronizacji, można wyrejestrować serwer. 

1. W Azure Portal przejdź do sekcji *Zarejestrowane serwery* w usłudze synchronizacji magazynu.
2. Kliknij prawym przyciskiem myszy serwer, który chcesz wyrejestrować, a następnie kliknij pozycję "Wyrejestruj serwer".

    ![Wyrejestruj serwer](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zapewnienie Azure File Sync jest dobrym sąsiadem w centrum danych 
Ponieważ Azure File Sync rzadko będzie jedyną usługą uruchamianą w centrum danych, można ograniczyć użycie sieci i magazynu Azure File Sync.

> [!Important]  
> Ustawienie zbyt niskich limitów będzie miało wpływ na wydajność Azure File Sync synchronizacji i odwoływania.

### <a name="set-azure-file-sync-network-limits"></a>Ustawianie Azure File Sync sieci
Możesz ograniczać wykorzystanie sieci przez Azure File Sync polecenia `StorageSyncNetworkLimit` cmdlet.

> [!Note]  
> Limity sieci nie mają zastosowania w przypadku dostępu do pliku warstwowego.

Możesz na przykład utworzyć nowy limit ograniczania przepustowości, aby zagwarantować, że program Azure File Sync nie będzie używać więcej niż 10 Mb/s między 9:00 a 17:00 (17:00) w tygodniu pracy: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Limit można wyświetlić za pomocą następującego polecenia cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Aby usunąć limity sieci, `Remove-StorageSyncNetworkLimit` użyj . Na przykład następujące polecenie usuwa wszystkie limity sieci:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Korzystanie z funkcji QoS magazynu systemu Windows Server 
Jeśli Azure File Sync jest hostowana na maszynie wirtualnej działającej na hoście wirtualizacji systemu Windows Server, można użyć funkcji QoS magazynu (jakości usługi magazynu) do regulacji użycia we/wy magazynu. Zasady QoS magazynu można ustawić jako wartość maksymalną (lub limit, np. sposób wymuszania limitu StorageSyncNetwork powyżej) lub jako wartość minimalną (lub rezerwację). Ustawienie wartości minimalnej zamiast maksymalnej umożliwia Azure File Sync użycia dostępnej przepustowości magazynu, jeśli inne obciążenia z niej nie korzystają. Aby uzyskać więcej informacji, zobacz [Storage Quality of Service](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Wdrażanie usługi Azure File Sync](file-sync-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](file-sync-troubleshoot.md)