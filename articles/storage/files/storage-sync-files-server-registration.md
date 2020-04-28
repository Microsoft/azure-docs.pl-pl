---
title: Zarządzanie zarejestrowanymi serwerami za pomocą usługi Azure File Sync | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarejestrować i wyrejestrować system Windows Server za pomocą usługi synchronizacji magazynu synchronizacji plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255095"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Zarządzanie zarejestrowanymi serwerami za pomocą usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. W tym celu przekształcając serwery systemu Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W poniższym artykule pokazano sposób rejestrowania serwera i zarządzania nim za pomocą usługi synchronizacji magazynu. Zobacz [Jak wdrożyć synchronizację plików platformy Azure,](storage-sync-files-deployment-guide.md) aby uzyskać informacje na temat wdrażania kompleksowej synchronizacji plików platformy Azure.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Rejestrowanie/wyrejestrowywę serwera za pomocą usługi synchronizacji magazynu
Rejestrowanie serwera za pomocą usługi Azure File Sync ustanawia relację zaufania między systemem Windows Server i Azure. Ta relacja może być następnie używana do tworzenia *punktów końcowych serwera* na serwerze, które reprezentują określone foldery, które powinny być synchronizowane z udziałem plików platformy Azure (znanym również jako punkt końcowy *chmury*). 

### <a name="prerequisites"></a>Wymagania wstępne
Aby zarejestrować serwer w usłudze synchronizacji magazynu, należy najpierw przygotować serwer z niezbędnymi wymaganiami wstępnymi:

* Na serwerze musi być obsługiwana wersja systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Wymagania systemowe i interoperacyjność usługi Azure File Sync](storage-sync-files-planning.md#windows-file-server-considerations).
* Upewnij się, że usługa synchronizacji magazynu została wdrożona. Aby uzyskać więcej informacji na temat wdrażania usługi synchronizacji magazynu, zobacz [Jak wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).
* Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna.
* Wyłącz konfigurację zabezpieczeń rozszerzonych IE dla administratorów za pomocą interfejsu użytkownika Menedżera serwera.
    
    ![Interfejs użytkownika menedżera serwera z wyróżnioną konfiguracją zabezpieczeń ie](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Upewnij się, że moduł programu Azure PowerShell jest zainstalowany na serwerze. Jeśli serwer jest członkiem klastra trybu failover, każdy węzeł w klastrze będzie wymagać modułu Az. Więcej informacji na temat instalowania modułu Az można znaleźć w [instalacji i konfigurowaniu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Zalecamy używanie najnowszej wersji modułu Az PowerShell do rejestrowania/wyrejestrowywać serwer. Jeśli pakiet Az został wcześniej zainstalowany na tym serwerze (a wersja programu PowerShell na tym `Update-Module` serwerze ma rozmiar 5.* lub więcej), można użyć polecenia cmdlet, aby zaktualizować ten pakiet. 
* Jeśli korzystasz z sieciowego serwera proxy w twoim środowisku, skonfiguruj ustawienia serwera proxy na serwerze, aby agent synchronizacji był używany.
    1. Określanie adresu IP serwera proxy i numeru portu
    2. Edytuj te dwa pliki:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Dodaj wiersze na rysunku 1 (poniżej tej sekcji) w obszarze /System.ServiceModel w powyższych dwóch plikach zmieniających 127.0.0.1:8888 na poprawny adres IP (zastąp 127.0.0.1) i poprawny numer portu (zastąp 8888):
    4. Ustaw ustawienia serwera proxy WinHTTP za pomocą wiersza polecenia:
        * Pokaż serwer proxy: netsh winhttp show proxy
        * Ustawianie serwera proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Resetowanie serwera proxy: netsh winhttp reset proxy
        * jeśli jest to konfiguracja po zainstalowaniu agenta, a następnie ponownie uruchomić nasz agent synchronizacji: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Rejestrowanie serwera w usłudze synchronizacji magazynu
Aby serwer mógł być używany jako *punkt końcowy serwera* w grupie *synchronizacji synchronizacji*plików azure, musi być zarejestrowany w *usłudze synchronizacji magazynu*. Serwer może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu naraz.

#### <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
1. [Pobierz agenta usługi Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Uruchom instalator agenta usługi Azure File Sync.
    
    ![Pierwsze okienko instalatora agenta usługi Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Pamiętaj, aby włączyć aktualizacje agenta usługi Azure File Sync przy użyciu witryny Microsoft Update. Jest to ważne, ponieważ krytyczne poprawki zabezpieczeń i ulepszenia funkcji pakietu serwera są dostarczane za pośrednictwem witryny Microsoft Update.

    ![Upewnij się, że usługa Microsoft Update jest włączona w okienku Microsoft Update instalatora agenta usługi Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Jeśli serwer nie został wcześniej zarejestrowany, interfejs użytkownika rejestracji serwera pojawi się natychmiast po zakończeniu instalacji.

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, agent synchronizacji plików azure musi być zainstalowany w każdym węźle w klastrze.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Rejestrowanie serwera przy użyciu interfejsu użytkownika rejestracji serwera
> [!Important]  
> Subskrypcje dostawcy rozwiązań w chmurze (CSP) nie mogą korzystać z interfejsu użytkownika rejestracji serwera. Zamiast tego należy użyć programu PowerShell (poniżej tej sekcji).

1. Jeśli interfejs użytkownika rejestracji serwera nie został uruchomiony natychmiast po zakończeniu instalacji agenta usługi Azure `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`File Sync, można go uruchomić ręcznie, wykonując program .
2. Kliknij *pozycję Zaloguj się,* aby uzyskać dostęp do subskrypcji platformy Azure. 

    ![Otwieranie okna dialogowego interfejsu użytkownika rejestracji serwera](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wybierz z okna dialogowego poprawną subskrypcję, grupę zasobów i usługę synchronizacji magazynu.

    ![Informacje o usłudze synchronizacji magazynu](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. W wersji zapoznawczej do ukończenia procesu wymagane jest jeszcze jedno zalogowanie. 

    ![Okno dialogowe logowania](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, każdy serwer musi uruchomić rejestrację serwera. Podczas wyświetlania zarejestrowanych serwerów w witrynie Azure Portal usługa Azure File Sync automatycznie rozpoznaje każdy węzeł jako członka tego samego klastra trybu failover i odpowiednio je grupuje.

#### <a name="register-the-server-with-powershell"></a>Rejestrowanie serwera w programie PowerShell
Rejestrację serwera można również przeprowadzić za pośrednictwem programu PowerShell. Jest to jedyny obsługiwany sposób rejestracji serwera dla subskrypcji dostawcy rozwiązań w chmurze (CSP):This is the only supported way of server registration for Cloud Solution Provider (CSP) subscriptions:

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Wyrejestrowanie serwera za pomocą usługi synchronizacji magazynu
Istnieje kilka kroków, które są wymagane do wyrejestrowania serwera z usługą synchronizacji magazynu. Przyjrzyjmy się, jak prawidłowo wyrejestrować serwer.

> [!Warning]  
> Nie próbuj rozwiązywać problemów z synchronizacją, warstwą chmury lub innym aspektem synchronizacji plików platformy Azure, wyrejestrowywanie i rejestrowanie serwera lub usuwanie i odtworzenie punktów końcowych serwera, chyba że zostanie to wyraźnie poinstruowane przez inżyniera firmy Microsoft. Wyrejestrowanie serwera i usuwanie punktów końcowych serwera jest operacją destrukcyjną, a pliki warstwowe na woluminach z punktami końcowymi serwera nie zostaną "ponownie połączone" z ich lokalizacjami w udziale plików platformy Azure po odtworzeniu zarejestrowanych punktów końcowych serwera i serwera, co spowoduje błędy synchronizacji. Należy również zauważyć, że pliki warstwowe, które istnieją poza obszarem nazw punktu końcowego serwera mogą zostać trwale utracone. Pliki warstwowe mogą istnieć w punktach końcowych serwera, nawet jeśli warstwa w chmurze nigdy nie została włączona.

#### <a name="optional-recall-all-tiered-data"></a>(Opcjonalnie) Odwoływanie wszystkich danych warstwowych
Jeśli chcesz, aby pliki, które są obecnie warstwowe, były dostępne po usunięciu usługi Azure File Sync (tj. jest to produkcja, a nie test, środowisko), należy odwołać wszystkie pliki na każdym woluminie zawierającym punkty końcowe serwera. Wyłącz warstwę chmury dla wszystkich punktów końcowych serwera, a następnie uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Jeśli wolumin lokalny obsługujący punkt końcowy serwera nie ma wystarczającej ilości `Invoke-StorageSyncFileRecall` wolnego miejsca, aby odwołać wszystkie dane warstwowe, polecenie cmdlet zakończy się niepowodzeniem.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Usuwanie serwera ze wszystkich grup synchronizacji
Przed wyrejestrowaniem serwera w usłudze synchronizacji magazynu wszystkie punkty końcowe serwera na tym serwerze muszą zostać usunięte. Można to zrobić za pośrednictwem portalu Azure:

1. Przejdź do usługi synchronizacji magazynu, w której jest zarejestrowany serwer.
2. Usuń wszystkie punkty końcowe serwera dla tego serwera w każdej grupie synchronizacji w usłudze synchronizacji magazynu. Można to osiągnąć, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Można to również osiągnąć za pomocą prostego skryptu programu PowerShell:

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

#### <a name="unregister-the-server"></a>Wyrejestrowyj serwer
Teraz, gdy wszystkie dane zostały odwołane, a serwer został usunięty ze wszystkich grup synchronizacji, serwer może być wyrejestrowany. 

1. W witrynie Azure portal przejdź do sekcji *Zarejestrowane serwery* usługi synchronizacji magazynu.
2. Kliknij prawym przyciskiem myszy serwer, który chcesz wyrejestrować i kliknij "Wyrejestruj serwer".

    ![Wyrejestruj serwer](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zapewnienie, że usługa Azure File Sync jest dobrym sąsiadem w twoim centrum danych 
Ponieważ usługa Azure File Sync rzadko będzie jedyną usługą działającą w centrum danych, można ograniczyć użycie sieci i magazynu usługi Azure File Sync.

> [!Important]  
> Zbyt niskie limity ustawień będą miały wpływ na wydajność synchronizacji i wycofywania synchronizacji plików platformy Azure.

### <a name="set-azure-file-sync-network-limits"></a>Ustawianie limitów sieci azure file sync
Można ograniczyć wykorzystanie sieci usługi Azure File Sync `StorageSyncNetworkLimit` przy użyciu poleceń cmdlet.

> [!Note]  
> Limity sieciowe nie mają zastosowania, gdy jest dostępny plik warstwowy lub używane jest polecenie cmdlet Invoke-StorageSyncFileRecall.

Na przykład można utworzyć nowy limit przepustnicy, aby upewnić się, że usługa Azure File Sync nie używa więcej niż 10 Mb/s między 9:00 a 17:00 (17:00h) w ciągu tygodnia roboczego: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Limit można zobaczyć za pomocą następującego polecenia cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Aby usunąć limity `Remove-StorageSyncNetworkLimit`sieciowe, użyj pliku . Na przykład następujące polecenie usuwa wszystkie limity sieciowe:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Korzystanie z qoS magazynu systemu Windows Server 
Gdy usługa Azure File Sync jest hostowana na maszynie wirtualnej uruchomionej na hoście wirtualizacji systemu Windows Server, można użyć QoS magazynu (jakość magazynu usługi) do regulowania zużycia we/wy magazynu. Zasady QoS magazynu można ustawić jako maksimum (lub limit, na przykład jak limit StorageSyncNetwork jest wymuszany powyżej) lub jako minimum (lub rezerwacji). Ustawienie minimum zamiast maksimum umożliwia synchronizacji plików platformy Azure do serii do korzystania z dostępnej przepustowości magazynu, jeśli inne obciążenia nie są przy użyciu go. Aby uzyskać więcej informacji, zobacz [Jakość usługi magazynu](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
