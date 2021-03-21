---
title: Zarządzanie zarejestrowanymi serwerami przy użyciu Azure File Sync | Microsoft Docs
description: Dowiedz się, jak zarejestrować i wyrejestrować system Windows Server za pomocą usługi synchronizacji magazynu Azure File Sync.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013925"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Zarządzanie zarejestrowanymi serwerami za pomocą Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez transformowanie serwerów z systemem Windows do szybkiej pamięci podręcznej udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W poniższym artykule przedstawiono sposób rejestrowania serwera i zarządzania nim za pomocą usługi synchronizacji magazynu. Zapoznaj się z artykułem [jak wdrożyć Azure File Sync](storage-sync-files-deployment-guide.md) , aby uzyskać informacje na temat sposobu wdrażania Azure File Sync na końcu.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Rejestrowanie/Wyrejestrowywanie serwera z usługą synchronizacji magazynu
Zarejestrowanie serwera z Azure File Sync ustanawia relację zaufania między systemem Windows Server i platformą Azure. Ta relacja może następnie służyć do tworzenia *punktów końcowych serwera* na serwerze, które reprezentują konkretne foldery, które powinny być synchronizowane z udziałem plików platformy Azure (znanym także jako *punkt końcowy w chmurze*). 

### <a name="prerequisites"></a>Wymagania wstępne
Aby zarejestrować serwer z usługą synchronizacji magazynu, należy najpierw przygotować serwer z wymaganiami wstępnymi:

* Na serwerze musi być uruchomiona obsługiwana wersja systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Azure File Sync wymagania systemowe i współdziałanie](storage-sync-files-planning.md#windows-file-server-considerations).
* Upewnij się, że została wdrożona usługa synchronizacji magazynu. Aby uzyskać więcej informacji na temat wdrażania usługi synchronizacji magazynu, zobacz [wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md).
* Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna.
* Wyłącz konfigurację zwiększonych zabezpieczeń programu IE dla administratorów za pomocą interfejsu użytkownika Menedżer serwera.
    
    ![Menedżer serwera interfejsie użytkownika z wyróżnioną konfiguracją zwiększonych zabezpieczeń programu IE](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Upewnij się, że moduł Azure PowerShell jest zainstalowany na serwerze. Jeśli serwer należy do klastra trybu failover, każdy węzeł w klastrze będzie wymagał elementu AZ module. Więcej informacji na temat sposobu instalowania tego modułu można znaleźć na stronie [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > Zalecamy używanie najnowszej wersji modułu AZ PowerShell module do rejestrowania/wyrejestrowywania serwera. Jeśli pakiet AZ został wcześniej zainstalowany na tym serwerze (a wersja programu PowerShell na tym serwerze to 5. * lub nowsza), możesz użyć `Update-Module` polecenia cmdlet, aby zaktualizować ten pakiet. 
* W przypadku korzystania z serwera proxy sieci w środowisku Skonfiguruj ustawienia serwera proxy na serwerze, aby Agent synchronizacji mógł go używać.
    1. Określ adres IP i numer portu serwera proxy
    2. Edytuj te dwa pliki:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Dodaj wiersze na rysunku 1 (poniżej tej sekcji) w obszarze/System.ServiceModel w powyższych dwóch plikach, zmieniając 127.0.0.1:8888 na prawidłowy adres IP (Zastąp adres 127.0.0.1) i poprawnego numeru portu (Zastąp 8888):
    4. Ustaw ustawienia serwera proxy WinHTTP za pośrednictwem wiersza polecenia:
        * Pokaż serwer proxy: netsh WinHTTP show proxy
        * Ustaw serwer proxy: netsh WinHTTP Set proxy 127.0.0.1:8888
        * Zresetuj serwer proxy: netsh WinHTTP Reset proxy
        * Jeśli jest to konfiguracja po zainstalowaniu agenta, uruchom ponownie nasz agent synchronizacji: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Rejestrowanie serwera z usługą synchronizacji magazynu
Aby można było używać serwera jako *punktu końcowego serwera* w *grupie synchronizacji* Azure File Sync, musi on być zarejestrowany w *usłudze synchronizacji magazynu*. Serwer może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu jednocześnie.

#### <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
1. [Pobierz agenta Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Uruchom Instalatora agenta Azure File Sync.
    
    ![Pierwsze okienko instalatora agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Należy pamiętać o włączeniu aktualizacji agenta Azure File Sync przy użyciu Microsoft Update. Ważne jest, ponieważ krytyczne poprawki zabezpieczeń i ulepszenia funkcji do pakietu serwera są dostarczane za pośrednictwem Microsoft Update.

    ![Upewnij się, że Microsoft Update jest włączona w okienku Microsoft Update instalatora agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Jeśli serwer nie został wcześniej zarejestrowany, interfejs użytkownika rejestracji serwera zostanie wyskakujący natychmiast po zakończeniu instalacji.

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, Agent Azure File Sync musi być zainstalowany na każdym węźle w klastrze.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Rejestrowanie serwera przy użyciu interfejsu użytkownika rejestracji serwera
1. Jeśli interfejs użytkownika rejestracji serwera nie został uruchomiony bezpośrednio po zakończeniu instalacji agenta Azure File Sync, można go uruchomić ręcznie, wykonując polecenie `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Kliknij pozycję *Zaloguj* się, aby uzyskać dostęp do subskrypcji platformy Azure. 

    ![Otwieranie okna dialogowego interfejsu użytkownika rejestracji serwera](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wybierz prawidłową subskrypcję, grupę zasobów i usługę synchronizacji magazynu z okna dialogowego.

    ![Informacje o usłudze synchronizacji magazynu](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. W wersji zapoznawczej do ukończenia procesu jest wymagane logowanie jednokrotne. 

    ![Okno dialogowe logowania](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Jeśli serwer jest członkiem klastra trybu failover, każdy serwer musi uruchomić rejestrację serwera. Gdy przeglądasz zarejestrowane serwery w witrynie Azure Portal, Azure File Sync automatycznie rozpoznaje każdy węzeł jako element członkowski tego samego klastra trybu failover i odpowiednio grupuje je.

#### <a name="register-the-server-with-powershell"></a>Rejestrowanie serwera przy użyciu programu PowerShell
Rejestrację serwera można również wykonać za pomocą programu PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Wyrejestrowywanie serwera z usługą synchronizacji magazynu
Istnieje kilka kroków, które są wymagane do wyrejestrowania serwera z usługą synchronizacji magazynu. Przyjrzyjmy się poprawnym wyrejestrowaniu serwera.

> [!Warning]  
> Nie należy próbować rozwiązywać problemów z synchronizacją, warstwami chmury ani jakimkolwiek innym aspektem Azure File Sync przez Wyrejestrowanie i rejestrację serwera, a także usuwanie i ponowne tworzenie punktów końcowych serwera, chyba że jest to wyraźnie nawiązane przez inżyniera firmy Microsoft. Wyrejestrowanie serwera i usuwanie punktów końcowych serwera jest operacją niszczącą, a pliki warstwowe na woluminach z punktami końcowymi serwera nie będą "ponownie połączone" z ich lokalizacjami w udziale plików platformy Azure po odtworzeniu zarejestrowanego serwera i punktów końcowych serwera, co spowoduje błędy synchronizacji. Należy również pamiętać, że pliki warstwowe istniejące poza przestrzenią nazw punktu końcowego serwera mogą zostać trwale utracone. Pliki warstwowe mogą istnieć w punktach końcowych serwera, nawet jeśli Obsługa warstw w chmurze nigdy nie została włączona.

#### <a name="optional-recall-all-tiered-data"></a>Obowiązkowe Odwołaj wszystkie dane warstwowe
Jeśli chcesz, aby pliki, które są obecnie warstwowo dostępne po usunięciu Azure File Sync (tj. to jest produkcja, a nie test, środowisko), Odwołaj wszystkie pliki na każdym woluminie zawierającym punkty końcowe serwera. Wyłącz obsługę warstw w chmurze dla wszystkich punktów końcowych serwera, a następnie uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Jeśli wolumin lokalny obsługujący punkt końcowy serwera nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, `Invoke-StorageSyncFileRecall` polecenie cmdlet zakończy się niepowodzeniem.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Usuń serwer ze wszystkich grup synchronizacji
Przed wyrejestrowaniem serwera w usłudze synchronizacji magazynu należy usunąć wszystkie punkty końcowe serwera na tym serwerze. Można to zrobić za pośrednictwem Azure Portal:

1. Przejdź do usługi synchronizacji magazynu, w której zarejestrowano serwer.
2. Usuń wszystkie punkty końcowe serwera dla tego serwera w każdej grupie synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku Grupa synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Można to również zrobić przy użyciu prostego skryptu programu PowerShell:

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
Teraz, gdy wszystkie dane zostały ponownie odwołane i serwer został usunięty ze wszystkich grup synchronizacji, można wyrejestrować serwer. 

1. W Azure Portal przejdź do sekcji *zarejestrowane serwery* usługi synchronizacji magazynu.
2. Kliknij prawym przyciskiem myszy serwer, który chcesz wyrejestrować, a następnie kliknij polecenie "Wyrejestruj serwer".

    ![Wyrejestruj serwer](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zapewnienie Azure File Sync jest dobrym sąsiadem w centrum danych 
Ponieważ Azure File Sync rzadko będzie jedyną usługą działającą w centrum danych, możesz ograniczyć użycie Azure File Sync sieci i magazynu.

> [!Important]  
> Ustawienie zbyt niskich limitów będzie miało wpływ na wydajność synchronizacji i odwoływania Azure File Sync.

### <a name="set-azure-file-sync-network-limits"></a>Ustaw Azure File Sync limity sieci
Możesz ograniczyć użycie sieci przez Azure File Sync za pomocą `StorageSyncNetworkLimit` poleceń cmdlet.

> [!Note]  
> Limity sieci nie są stosowane podczas uzyskiwania dostępu do pliku warstwowego.

Na przykład można utworzyć nowy limit ograniczania przepustowości, aby zapewnić, że Azure File Sync nie używa więcej niż 10 MB/s między 9 am i 5 PM (17:00h) podczas tygodnia pracy: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Limit można zobaczyć przy użyciu następującego polecenia cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Aby usunąć limity sieci, użyj `Remove-StorageSyncNetworkLimit` . Na przykład następujące polecenie usuwa wszystkie limity sieci:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Korzystanie z funkcji QoS magazynu systemu Windows Server 
Gdy Azure File Sync jest hostowana na maszynie wirtualnej uruchomionej na hoście wirtualizacji systemu Windows Server, można użyć funkcji QoS magazynu (jakość usługi magazynu), aby regulować użycie operacji we/wy magazynu. Dla zasad QoS magazynu można ustawić wartość maksymalną (lub limit, na przykład sposób wymuszony limit StorageSyncNetwork) lub minimalną (lub rezerwację). Ustawienie wartości minimalnej zamiast maksymalnej umożliwia Azure File Sync na rozerwanie korzystanie z dostępnej przepustowości magazynu, jeśli inne obciążenia nie będą używane. Aby uzyskać więcej informacji, zobacz [magazyn Quality of Service](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)