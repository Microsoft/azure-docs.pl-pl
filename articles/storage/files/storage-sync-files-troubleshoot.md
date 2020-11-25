---
title: Rozwiązywanie problemów Azure File Sync | Microsoft Docs
description: Rozwiązywanie typowych problemów ze wdrożeniem na Azure File Sync, których można użyć do przekształcenia systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 6/12/2020
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: c7405ada800bd5fb9161e9d96bd4c8b0484be620
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005339"
---
# <a name="troubleshoot-azure-file-sync"></a>Rozwiązywanie problemów z usługą Azure File Sync
Użyj Azure File Sync, aby scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

Ten artykuł ma na celu pomoc w rozwiązywaniu problemów i rozwiązywaniu problemów, które mogą wystąpić podczas wdrażania Azure File Sync. Opisano również sposób zbierania ważnych dzienników z systemu w przypadku, gdy jest wymagane szczegółowe badanie problemu. Jeśli nie widzisz odpowiedzi na pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

1. [Microsoft Q&stronie pytania dotyczącej usługi Azure Storage](/answers/products/azure?product=storage).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie obsługi, w Azure Portal na karcie **Pomoc** wybierz przycisk **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mam problem z Azure File Sync na serwerze (synchronizacja, Obsługa warstw w chmurze itp.). Czy należy usunąć i ponownie utworzyć mój punkt końcowy serwera?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalacja agenta i Rejestracja serwera
<a id="agent-installation-failures"></a>**Rozwiązywanie problemów z błędami instalacji agenta**  
Jeśli instalacja agenta Azure File Sync nie powiedzie się, w wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenie, aby włączyć rejestrowanie podczas instalacji agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Przejrzyj Instalatora. log, aby ustalić przyczynę niepowodzenia instalacji.

<a id="agent-installation-on-DC"></a>**Instalacja agenta kończy się niepowodzeniem na kontrolerze domena usługi Active Directory**  
W przypadku próby zainstalowania agenta synchronizacji na kontrolerze domeny Active Directory, w którym właściciel roli PDC znajduje się w systemie Windows Server 2008 R2 lub nowszym wersja systemu operacyjnego, może wystąpić problem polegający na tym, że Instalacja agenta synchronizacji zakończy się niepowodzeniem.

Aby rozwiązać ten problem, Przenieś rolę PDC na inny kontroler domeny z systemem Windows Server 2012 R2 lub nowszy, a następnie Zainstaluj synchronizację.

<a id="parameter-is-incorrect"></a>**Uzyskiwanie dostępu do woluminu w systemie Windows Server 2012 R2 kończy się niepowodzeniem z powodu błędu: parametr jest niepoprawny**  
Po utworzeniu punktu końcowego serwera w systemie Windows Server 2012 R2 wystąpił następujący błąd podczas uzyskiwania dostępu do woluminu:

litera dysku: \ nie jest dostępny.  
Parametr jest nieprawidłowy.

Aby rozwiązać ten problem, zainstaluj program [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) i ponownie uruchom serwer. Jeśli ta aktualizacja nie zostanie zainstalowana, ponieważ jest już zainstalowana nowsza aktualizacja, przejdź do Windows Update, zainstaluj najnowsze aktualizacje systemu Windows Server 2012 R2 i ponownie uruchom serwer.

<a id="server-registration-missing-subscriptions"></a>**Rejestracja serwera nie zawiera wszystkich subskrypcji platformy Azure**  
Podczas rejestrowania serwera przy użyciu ServerRegistration.exe nie ma subskrypcji, po kliknięciu listy rozwijanej subskrypcja platformy Azure.

Ten problem występuje, ponieważ ServerRegistration.exe nie obsługuje obecnie środowisk wielodostępnych. Ten problem zostanie rozwiązany w przyszłej aktualizacji agenta Azure File Sync.

Aby obejść ten problem, należy użyć następujących poleceń programu PowerShell do zarejestrowania serwera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Rejestracja serwera wyświetla następujący komunikat: "Brak wymagań wstępnych"**  
Ten komunikat jest wyświetlany, gdy nie zainstalowano modułu PowerShell AZ lub AzureRM w programie PowerShell 5,1. 

> [!Note]  
> ServerRegistration.exe nie obsługuje programu PowerShell 6. x. Aby zarejestrować serwer, można użyć polecenia cmdlet Register-AzStorageSyncServer w programie PowerShell 6. x.

Aby zainstalować moduł AZ lub AzureRM w programie PowerShell 5,1, wykonaj następujące czynności:

1. Wpisz **PowerShell** w wierszu polecenia z podwyższonym poziomem uprawnień i naciśnij klawisz ENTER.
2. Zainstaluj najnowszy moduł AZ lub AzureRM, postępując zgodnie z dokumentacją:
    - [AZ module (wymaga programu .NET 4.7.2)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [Moduł AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Uruchom ServerRegistration.exe i Ukończ pracę kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

<a id="server-already-registered"></a>**Rejestracja serwera wyświetla następujący komunikat: "ten serwer jest już zarejestrowany"** 

![Zrzut ekranu okna dialogowego rejestracji serwera z komunikatem o błędzie "serwer jest już zarejestrowany"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ten komunikat jest wyświetlany, jeśli serwer został wcześniej zarejestrowany w usłudze synchronizacji magazynu. Aby wyrejestrować serwer z bieżącej usługi synchronizacji magazynu, a następnie zarejestrować się w nowej usłudze synchronizacji magazynu, wykonaj kroki opisane w [Wyrejestruj serwer z Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Jeśli serwer nie znajduje się na liście **zarejestrowanych serwerów** w usłudze synchronizacji magazynu, na serwerze, który ma zostać wyrejestrowany, uruchom następujące polecenia programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Jeśli serwer jest częścią klastra, można użyć opcjonalnego parametru *Reset-StorageSyncServer-CleanClusterRegistration* , aby usunąć także rejestrację klastra.

<a id="web-site-not-trusted"></a>**Po zarejestrowaniu serwera widzę wiele odpowiedzi "niezaufane witryny sieci Web". Zalet?**  
Ten problem występuje, gdy zasady **zabezpieczeń programu Internet Explorer** są włączone podczas rejestracji serwera. Aby uzyskać więcej informacji o tym, jak prawidłowo wyłączyć **ulepszone zasady zabezpieczeń programu Internet Explorer** , zobacz [Przygotowywanie systemu Windows Server do użycia z programem Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) i [wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Serwer nie znajduje się na liście zarejestrowanych serwerów w Azure Portal**  
Jeśli serwer nie znajduje się na liście **zarejestrowanych serwerów** dla usługi synchronizacji magazynu:
1. Zaloguj się na serwerze, który chcesz zarejestrować.
2. Otwórz Eksploratora plików, a następnie przejdź do katalogu instalacyjnego agenta synchronizacji magazynu (domyślna lokalizacja to C:\Program Files\Azure\StorageSyncAgent). 
3. Uruchom ServerRegistration.exe i Ukończ pracę kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

## <a name="sync-group-management"></a>Zarządzanie grupami synchronizacji

### <a name="cloud-endpoint-creation-errors"></a>Błędy tworzenia punktów końcowych w chmurze

<a id="cloud-endpoint-using-share"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem z powodu błędu: "określony udział plików platformy Azure jest już używany przez inny CloudEndpoint"**  
Ten błąd występuje, jeśli udział plików platformy Azure jest już używany przez inny punkt końcowy w chmurze. 

Jeśli zobaczysz ten komunikat i udział plików platformy Azure aktualnie nie jest używany przez punkt końcowy w chmurze, wykonaj następujące kroki, aby wyczyścić Azure File Sync metadane w udziale plików platformy Azure:

> [!Warning]  
> Usuwanie metadanych w udziale plików platformy Azure, który jest obecnie używany przez punkt końcowy w chmurze, powoduje niepowodzenie operacji Azure File Sync. 

1. W Azure Portal przejdź do udziału plików platformy Azure.  
2. Kliknij prawym przyciskiem myszy udział plików platformy Azure, a następnie wybierz polecenie **Edytuj metadane**.
3. Kliknij prawym przyciskiem myszy pozycję **SyncService**, a następnie wybierz pozycję **Usuń**.

<a id="cloud-endpoint-authfailed"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem z powodu tego błędu: "AuthorizationFailed"**  
Ten błąd występuje, jeśli konto użytkownika nie ma wystarczających uprawnień do utworzenia punktu końcowego w chmurze. 

Aby utworzyć punkt końcowy w chmurze, konto użytkownika musi mieć następujące uprawnienia do autoryzacji firmy Microsoft:  
* Odczyt: pobieranie definicji roli
* Zapis: Tworzenie lub aktualizowanie definicji roli niestandardowej
* Odczyt: pobieranie przypisania roli
* Zapis: Tworzenie przypisania roli

Następujące wbudowane role mają wymagane uprawnienia do autoryzacji firmy Microsoft:  
* Właściciel
* Administrator dostępu użytkowników

Aby określić, czy Twoje konto użytkownika ma wymagane uprawnienia:  
1. W Azure Portal wybierz pozycję **grupy zasobów**.
2. Wybierz grupę zasobów, w której znajduje się konto magazynu, a następnie wybierz pozycję **Kontrola dostępu (IAM)**.
3. Wybierz kartę **przypisania ról** .
4. Wybierz **rolę** (na przykład właściciela lub współautora) dla konta użytkownika.
5. Na liście **dostawca zasobów** wybierz pozycję **autoryzacja firmy Microsoft**. 
    * **Przypisanie roli** powinno mieć uprawnienia do **odczytu** i **zapisu** .
    * **Definicja roli** powinna mieć uprawnienia do **odczytu** i **zapisu** .

### <a name="server-endpoint-creation-and-deletion-errors"></a>Błędy tworzenia i usuwania punktu końcowego serwera

<a id="-2134375898"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2134375898 lub 0x80c80226)**  
Ten błąd występuje, gdy ścieżka punktu końcowego serwera znajduje się na woluminie systemowym i obsługa warstw w chmurze jest włączona. Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.

<a id="-2147024894"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2147024894 lub 0x80070002)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera jest nieprawidłowa. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Należy pamiętać, że Azure File Sync nie obsługuje zmapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134375640"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2134375640 lub 0x80c80328)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera nie jest woluminem NTFS. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Należy pamiętać, że Azure File Sync nie obsługuje zmapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134347507"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2134347507 lub 0x80c8710d)**  
Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje punktów końcowych serwera na woluminach, które mają skompresowany katalog informacji o woluminie systemowym. Aby rozwiązać ten problem, zdekompresuj katalog informacji o woluminie systemowym. Jeśli katalog informacji o woluminie systemowym jest jedynym skompresowanym katalogiem na woluminie, wykonaj następujące czynności:

1. Pobierz narzędzie [PsExec](/sysinternals/downloads/psexec) .
2. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia uruchomiony w ramach konta systemowego: **PsExec.exe-i-s-d cmd**
3. W wierszu polecenia uruchomionym w ramach konta systemowego wpisz następujące polecenia i naciśnij klawisz Enter:   
    **CD/d "litera dysku: \ informacje o woluminie systemowym"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2134376345 lub 0x80C80067)**  
Ten błąd występuje, gdy zostanie osiągnięty limit liczby punktów końcowych serwera. Usługa Azure File Sync obsługuje obecnie do 30 punktów końcowych serwera na serwer. Aby uzyskać więcej informacji, zobacz [Azure File Sync celu skalowania](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2134376427 lub 0x80c80015)**  
Ten błąd występuje, gdy inny punkt końcowy serwera synchronizuje określoną ścieżkę punktu końcowego serwera. Usługa Azure File Sync nie obsługuje synchronizacji tego samego katalogu lub woluminu w wielu punktach końcowych serwera.

<a id="-2160590967"></a>**Tworzenie punktu końcowego serwera nie powiodło się; z powodu tego błędu: "MgmtServerJobFailed" (kod błędu:-2160590967 lub 0x80c80077)**  
Ten błąd występuje, gdy ścieżka punktu końcowego serwera zawiera oddzielone pliki warstwowe. Jeśli punkt końcowy serwera został niedawno usunięty, poczekaj na zakończenie czyszczenia oddzielonych plików warstwowych. Zdarzenie o IDENTYFIKATORze 6662 jest rejestrowane w dzienniku zdarzeń telemetrii po rozpoczęciu oczyszczania oddzielonych plików warstwowych. Zdarzenie o IDENTYFIKATORze 6661 jest rejestrowane po zakończeniu oczyszczania oddzielonych plików warstwowych, a punkt końcowy serwera można odtworzyć przy użyciu ścieżki. Jeśli Tworzenie punktu końcowego serwera zakończy się niepowodzeniem po zakończeniu oczyszczania plików warstwowych lub jeśli nie można znaleźć zdarzenia o IDENTYFIKATORze 6661 w dzienniku zdarzeń telemetrii ze względu na przerzucenie dziennika zdarzeń, Usuń oddzielone pliki warstwowe, wykonując kroki opisane w [plikach warstwowych nie są dostępne na serwerze po usunięciu sekcji punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .

<a id="-2134347757"></a>**Usuwanie punktu końcowego serwera nie powiodło się, z powodu tego błędu: "MgmtServerJobExpired" (kod błędu:-2134347757 lub 0x80c87013)**  
Ten błąd występuje wtedy, gdy serwer jest w trybie offline lub nie ma łączności sieciowej. Jeśli serwer nie jest już dostępny, wyrejestruj serwer w portalu, co spowoduje usunięcie punktów końcowych serwera. Aby usunąć punkty końcowe serwera, wykonaj kroki opisane w [Wyrejestruj serwer z Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Kondycja punktu końcowego serwera

<a id="server-endpoint-provisioningfailed"></a>**Nie można otworzyć strony właściwości punktu końcowego serwera lub zaktualizować zasad obsługi warstw w chmurze**  
Ten problem może wystąpić, jeśli operacja zarządzania w punkcie końcowym serwera nie powiodła się. Jeśli strona właściwości punktu końcowego serwera nie zostanie otwarta w Azure Portal, aktualizowanie punktu końcowego serwera za pomocą poleceń programu PowerShell z serwera może rozwiązać ten problem. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Punkt końcowy serwera ma stan kondycji "brak działania" lub "oczekiwanie", a stan serwera w bloku zarejestrowane serwery to "pojawia się w trybie offline"**  

Ten problem może wystąpić, jeśli proces monitora synchronizacji magazynu (AzureStorageSyncMonitor.exe) nie jest uruchomiony lub serwer nie może uzyskać dostępu do usługi Azure File Sync.

Na serwerze, który jest wyświetlany jako "pojawia się w trybie offline" w portalu, poszukaj zdarzenia o IDENTYFIKATORze 9301 w dzienniku zdarzeń telemetrii (w obszarze aplikacje i Services\Microsoft\FileSync\Agent w Podgląd zdarzeń), aby określić, dlaczego serwer nie może uzyskać dostępu do usługi Azure File Sync. 

- Jeśli **GetNextJob zakończył działanie: 0** jest rejestrowany, serwer może komunikować się z usługą Azure File Sync. 
    - Otwórz Menedżera zadań na serwerze i upewnij się, że proces programu Storage Sync Monitor (AzureStorageSyncMonitor.exe) jest uruchomiony. Jeśli proces nie jest uruchomiony, najpierw spróbuj uruchomić ponownie serwer. Jeśli ponowne uruchomienie serwera nie rozwiąże problemu, uaktualnij agenta usługi Azure File Sync do najnowszej [wersji](./storage-files-release-notes.md). 

- Jeśli **GetNextJob zostało zakończone ze stanem:-2134347756** jest rejestrowane, serwer nie może komunikować się z usługą Azure File Sync z powodu zapory lub serwera proxy. 
    - Jeśli serwer znajduje się za zaporą, sprawdź, czy ruch wychodzący na port 443 jest dozwolony. Jeśli Zapora ogranicza ruch do określonych domen, upewnij się, że domeny wymienione w [dokumentacji](./storage-sync-files-firewall-and-proxy.md#firewall) zapory są dostępne.
    - Jeśli serwer znajduje się za serwerem proxy, skonfiguruj ustawienia serwera proxy dotyczące całego komputera lub aplikacji, wykonując czynności opisane w [dokumentacji](./storage-sync-files-firewall-and-proxy.md#proxy)serwera proxy.
    - Użyj polecenia cmdlet Test-StorageSyncNetworkConnectivity, aby sprawdzić łączność sieciową z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [test łączności sieciowej z punktami końcowymi usługi](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).

- Jeśli **GetNextJob zakończyła się stanem:-2134347764** jest rejestrowane, serwer nie może komunikować się z usługą Azure File Sync ze względu na certyfikat wygasły lub usunięty.  
    - Uruchom następujące polecenie programu PowerShell na serwerze, aby zresetować certyfikat używany do uwierzytelniania:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Punkt końcowy serwera ma stan kondycji "brak działania", a stan serwera w bloku zarejestrowane serwery to "online"**  

Stan kondycji punktu końcowego serwera „Brak działania” oznacza, że punkt końcowy serwera nie zarejestrował działania synchronizacji w ciągu ostatnich dwóch godzin.

Aby sprawdzić bieżącą aktywność synchronizacji na serwerze, zobacz [Jak mogę monitorować postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

Punkt końcowy serwera może nie rejestrować aktywności synchronizacji przez kilka godzin z powodu błędu lub niewystarczających zasobów systemowych. Sprawdź, czy zainstalowano najnowszą [wersję agenta](./storage-files-release-notes.md) Azure File Sync. Jeśli problem będzie się powtarzać, Otwórz żądanie obsługi.

> [!Note]  
> Jeśli stan serwera w bloku zarejestrowane serwery jest "pojawia się w trybie offline", wykonaj kroki opisane w [punkcie końcowym serwera ma stan kondycji "brak działania" lub "oczekiwanie", a stan serwera w bloku zarejestrowane serwery to "pojawia się w trybie offline"](#server-endpoint-noactivity) .

## <a name="sync"></a>Synchronizuj
<a id="afs-change-detection"></a>**Jeśli plik został utworzony bezpośrednio w udziale plików platformy Azure za pośrednictwem protokołu SMB lub za pośrednictwem portalu, jak długo trwa synchronizacja pliku z serwerami w grupie synchronizacji?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Kondycja punktu końcowego serwera jest w stanie oczekiwania przez kilka godzin**  
Ten problem jest oczekiwany w przypadku utworzenia punktu końcowego w chmurze i użycia udziału plików platformy Azure, który zawiera dane. Zadanie wyliczania zmian, które skanuje w poszukiwaniu zmian w udziale plików platformy Azure, musi zostać zakończone, zanim pliki będą synchronizowane między punktami końcowymi chmury i serwera. Czas do ukończenia zadania zależy od rozmiaru przestrzeni nazw w udziale plików platformy Azure. Kondycja punktu końcowego serwera powinna zostać zaktualizowana po zakończeniu zadania wyliczania zmian.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Jak mogę monitorowanie kondycji synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W ramach każdej grupy synchronizacji można przejść do szczegółów poszczególnych punktów końcowych serwera, aby zobaczyć stan ostatnich ukończonych sesji synchronizacji. Zielona kolumna kondycji i pliki, które nie są synchronizowane wartość 0 wskazują, że synchronizacja działa zgodnie z oczekiwaniami. Jeśli tak nie jest, zobacz poniżej, aby zapoznać się z listą typowych błędów synchronizacji i jak obsłużyć pliki, które nie są synchronizowane. 

![Zrzut ekranu przedstawiający Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server) (Serwer)
Przejdź do dzienników telemetrii serwera, które znajdują się w Podgląd zdarzeń pod adresem `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Zdarzenie 9102 odpowiada zakończonej sesji synchronizacji; Aby uzyskać najnowszy stan synchronizacji, poszukaj najnowszego zdarzenia o IDENTYFIKATORze 9102. SyncDirection informuje o tym, czy ta sesja była przekazaniem czy pobraniem. Jeśli HResult ma wartość 0, sesja synchronizacji zakończyła się pomyślnie. Wynik o wartości innej niż zero oznacza, że wystąpił błąd podczas synchronizacji; Lista typowych błędów znajduje się poniżej. Jeśli wartość PerItemErrorCount jest większa od 0, oznacza to, że niektóre pliki lub foldery nie zostały prawidłowo zsynchronizowane. Istnieje możliwość, że wynik HResult równy 0, ale PerItemErrorCount jest większy niż 0.

Poniżej znajduje się przykład pomyślnego przekazania. Ze względu na zwięzłości są wyświetlane tylko niektóre wartości zawarte w każdym zdarzeniu 9102. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Z drugiej strony przekazywanie nieudane może wyglądać następująco:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Czasami synchronizacja sesji kończy się niepowodzeniem lub mieć PerItemErrorCount o wartości innej niż zero, ale nadal trwa synchronizacja niektórych plików. Może to być widoczne w zastosowanych * polach (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes), które informują o tym, ile sesji kończy się powodzeniem. Jeśli zobaczysz wiele sesji synchronizacji w wierszu, który kończy się niepowodzeniem, ale ma zwiększoną liczbę zastosowanych * licznik, należy dać czas synchronizacji, aby spróbować ponownie przed otwarciem biletu pomocy technicznej.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorować postęp bieżącej sesji synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W ramach grupy synchronizacji przejdź do punktu końcowego serwera, a następnie zapoznaj się z sekcją aktywność synchronizacji, aby zobaczyć liczbę plików przekazanych lub pobranych w bieżącej sesji synchronizacji. Pamiętaj, że ten stan zostanie opóźniony o około 5 minut. Jeśli sesja synchronizacji jest wystarczająco mała, aby mogła zostać zakończona w tym okresie, może nie zostać zgłoszona w portalu. 

# <a name="server"></a>[Server](#tab/server) (Serwer)
Zapoznaj się z najnowszym zdarzeniem 9302 w dzienniku telemetrii na serwerze (w Podgląd zdarzeń przejdź do pozycji aplikacje i usługi Logs\Microsoft\FileSync\Agent\Telemetry). To zdarzenie wskazuje stan bieżącej sesji synchronizacji. TotalItemCount wskazuje, ile plików ma być synchronizowanych, AppliedItemCount liczbę plików, które zostały zsynchronizowane do tej pory, i PerItemErrorCount liczbę plików, które nie są synchronizowane (zobacz poniżej, aby dowiedzieć się, jak to zrobić).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Jak sprawdzić, czy serwery są zsynchronizowane ze sobą?
# <a name="portal"></a>[Portal](#tab/portal1)
Upewnij się, że dla każdego serwera w danej grupie synchronizacji:
- Sygnatury czasowe ostatniej próby synchronizacji zarówno do przekazywania, jak i pobierania są ostatnie.
- Stan jest zielony w przypadku przekazywania i pobierania.
- W polu działanie synchronizacji są wyświetlane bardzo mało plików, które mają zostać zsynchronizowane.
- W przypadku plików, które nie są synchronizowane, jest wartością 0 w przypadku przekazywania i pobierania.

# <a name="server"></a>[Server](#tab/server) (Serwer)
Zapoznaj się z zakończonymi sesjami synchronizacji, które są oznaczone przez 9102 zdarzeń w dzienniku zdarzeń telemetrii dla każdego serwera (w Podgląd zdarzeń, przejdź do `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Na dowolnym serwerze upewnij się, że najnowsze sesje przekazywania i pobierania zostały ukończone pomyślnie. W tym celu należy sprawdzić, czy wartość HResult i PerItemErrorCount są równe 0 w przypadku przekazywania i pobierania (pole SyncDirection wskazuje, czy dana sesja jest sesjami przekazywania lub pobierania). Należy pamiętać, że jeśli ostatnio ukończona sesja synchronizacji nie zostanie wyświetlona, prawdopodobnie sesja synchronizacji jest obecnie w toku, która jest oczekiwana w przypadku dodania lub zmodyfikowania dużej ilości danych.
2. Gdy serwer jest w pełni aktualny z chmurą i nie ma żadnych zmian do synchronizacji w dowolnym kierunku, zostaną wyświetlone puste sesje synchronizacji. Są one wskazywane przez przekazywanie i pobieranie zdarzeń, w których wszystkie pola Sync * (SyncFileCount, SyncDirCount, SyncTombstoneCount i SyncSizeBytes) są równe zero, co oznacza, że nie ma niczego do zsynchronizowania. Zwróć uwagę na to, że te puste sesje synchronizacji mogą nie występować na serwerach o wysokim poziomie zmian, ponieważ zawsze istnieje nowa synchronizacja. Jeśli nie ma żadnych działań synchronizacji, powinny one wystąpić co 30 minut. 
3. Jeśli wszystkie serwery są aktualne w chmurze, oznacza to, że ich ostatnie sesje przekazywania i pobierania są pustymi sesjami synchronizacji, możesz powiedzieć, że system jako całość jest zsynchronizowany. 
    
Należy pamiętać, że w przypadku wprowadzenia zmian bezpośrednio w udziale plików platformy Azure Azure File Sync nie będzie wykrywać tej zmiany do momentu uruchomienia wyliczenia zmian, co ma miejsce co 24 godziny. Istnieje możliwość, że serwer zamówi, że jest on aktualny w chmurze, gdy w rzeczywistości brakuje najnowszych zmian wprowadzonych bezpośrednio w udziale plików platformy Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak mogę sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane?
Jeśli PerItemErrorCount na serwerze lub plikach, które nie są synchronizowane w portalu, są większe niż 0 dla danej sesji synchronizacji, co oznacza, że niektóre elementy nie są synchronizowane. Pliki i foldery mogą mieć właściwości, które uniemożliwiają ich synchronizację. Te cechy mogą być trwałe i wymagać jawnej akcji w celu wznowienia synchronizacji, na przykład usunięcie nieobsługiwanych znaków z nazwy pliku lub folderu. Mogą to być również przejściowe, co oznacza, że plik lub folder zostanie automatycznie wznowione synchronizację; na przykład pliki z otwartymi dojściami spowodują automatyczne wznowienie synchronizacji po zamknięciu pliku. Gdy aparat Azure File Sync wykryje ten problem, zostanie wygenerowany dziennik błędów, który można analizować, aby wyświetlić listę elementów, które aktualnie nie są synchronizowane.

Aby wyświetlić te błędy, uruchom skrypt programu **FileSyncErrorsReport.ps1** PowerShell (znajdujący się w katalogu instalacji agenta agenta Azure File Sync), aby zidentyfikować pliki, których nie można zsynchronizować z powodu otwartych dojść, nieobsługiwanych znaków lub innych problemów. Pole ścieżki elementu informuje o lokalizacji pliku w odniesieniu do głównego katalogu synchronizacji. Zapoznaj się z listą typowych błędów synchronizacji poniżej, aby poznać kroki zaradcze.

> [!Note]  
> Jeśli skrypt FileSyncErrorsReport.ps1 zwraca "nie znaleziono błędów plików" lub nie wyświetla listę błędów poszczególnych elementów dla grupy synchronizacji, przyczyna to:
>
>- Przyczyna 1: Ostatnia zakończona sesja synchronizacji nie ma błędów poszczególnych elementów. Portal powinien zostać wkrótce zaktualizowany, aby wyświetlić 0 plików, których nie można zsynchronizować. 
>    - Sprawdź [Identyfikator zdarzenia 9102](?tabs=server%252cazure-portal#broken-sync) w dzienniku zdarzeń telemetrii, aby potwierdzić, że PerItemErrorCount wynosi 0. 
>
>- Przyczyna 2: dziennik zdarzeń ItemResults na serwerze zawinięty z powodu zbyt dużej liczby błędów poszczególnych elementów, a dziennik zdarzeń nie zawiera już błędów dla tej grupy synchronizacji.
>    - Aby uniknąć tego problemu, Zwiększ rozmiar dziennika zdarzeń ItemResults. Dziennik zdarzeń ItemResults można znaleźć w sekcji "aplikacje i usługi Logs\Microsoft\FileSync\Agent" w Podgląd zdarzeń. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Rozwiązywanie problemów na błędy synchronizacji plików/katalogów
**Błędy synchronizacji dla elementu dziennika ItemResults**  

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Plik warstwowy na serwerze nie jest dostępny. Ten problem występuje, gdy plik warstwowy nie został odwołany przed usunięciem punktu końcowego serwera. | Aby rozwiązać ten problem, zobacz [pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał przez kilka dni, Użyj skryptu FileSyncErrorsReport.ps1 PowerShell, aby określić, dlaczego folder zależny nie jest jeszcze zsynchronizowany. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał przez kilka dni, Użyj skryptu FileSyncErrorsReport.ps1 PowerShell, aby określić, dlaczego folder zależny nie jest jeszcze zsynchronizowany. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany i sesja synchronizacji nie powiodła się. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać, zbadaj błąd sesji synchronizacji. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę podanego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwanych znaków](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę podanego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwanych znaków](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze w celu synchronizowania plików, które mają otwarte dojścia. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Plik został zmieniony, ale zmiana nie została jeszcze wykryta przez synchronizację. Po wykryciu tej zmiany synchronizacja zostanie odzyskana. | Żadna akcja nie jest wymagana. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Plik został usunięty, a synchronizacja nie ma informacji o zmianie. | Żadna akcja nie jest wymagana. Synchronizacja spowoduje zatrzymywanie rejestrowania tego błędu, gdy wykrycie zmiany wykryje, że plik został usunięty. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Nie można zsynchronizować pliku lub katalogu, ponieważ element został już usunięty w miejscu docelowym, a synchronizacja nie ma informacji o zmianie. | Żadna akcja nie jest wymagana. Synchronizacja spowoduje zatrzymywanie rejestrowania tego błędu, gdy wykrywanie zmian zostanie uruchomione w miejscu docelowym, a synchronizacja wykryje, że element został usunięty. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Plik lub katalog został pominięty, ale zostanie zsynchronizowany podczas następnej sesji synchronizacji. Jeśli ten błąd jest raportowany podczas pobierania elementu, nazwa pliku lub katalogu jest większa niż prawdopodobnie nieprawidłowa. | Jeśli ten błąd jest raportowany podczas przekazywania pliku, nie jest wymagana żadna akcja. Jeśli błąd jest raportowany podczas pobierania pliku, należy zmienić nazwę danego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwanych znaków](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Nie można zsynchronizować pliku lub katalogu, ponieważ element już istnieje w miejscu docelowym, a synchronizacja nie ma informacji o zmianie. | Żadna akcja nie jest wymagana. Synchronizacja spowoduje zatrzymywanie rejestrowania tego błędu, gdy wykrywanie zmian zostanie uruchomione w miejscu docelowym, a synchronizacja jest świadoma tego nowego elementu. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Nie można zsynchronizować pliku, ponieważ osiągnięto limit udziału plików platformy Azure. | Aby rozwiązać ten problem, zobacz sekcję [Limit magazynu udziału plików platformy Azure](?tabs=portal1%252cazure-portal#-2134351810) w przewodniku rozwiązywania problemów. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Plik jest szyfrowany za pomocą nieobsługiwanego rozwiązania (na przykład NTFS EFS). | Odszyfruj plik i użyj obsługiwanego rozwiązania szyfrowania. Listę obsługiwanych rozwiązań można znaleźć w sekcji [Rozwiązania do szyfrowania](./storage-sync-files-planning.md#encryption) w przewodniku planowania. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Plik znajduje się w folderze replikacji tylko do odczytu systemu plików DFS. | Plik znajduje się w folderze replikacji tylko do odczytu systemu plików DFS. Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Aby uzyskać więcej informacji, zobacz [Przewodnik planowania](./storage-sync-files-planning.md#distributed-file-system-dfs) . |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Plik ma stan oczekiwania na usunięcie. | Żadna akcja nie jest wymagana. Plik zostanie usunięty po zamknięciu wszystkich dojść do otwartego pliku. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Nie można zsynchronizować pliku, ponieważ ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Dodaj adres IP lub sieć wirtualną serwera, wykonując czynności opisane w sekcji [Konfigurowanie ustawień zapory i sieci wirtualnej](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w Podręczniku wdrażania. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Nie można zsynchronizować pliku, ponieważ rozmiar deskryptora zabezpieczeń przekracza limit 64 KiB. | Aby rozwiązać ten problem, usuń wpisy kontroli dostępu (ACE) w pliku w celu zmniejszenia rozmiaru deskryptora zabezpieczeń. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Nie można zsynchronizować pliku z powodu nieoczekiwanego błędu. | Jeśli błąd będzie się powtarzać przez kilka dni, Otwórz przypadek pomocy technicznej. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Plik został zmieniony podczas synchronizacji, więc musi zostać ponownie zsynchronizowany. | Żadna akcja nie jest wymagana. |
| 0x80070017 | -2147024873 | ERROR_CRC | Nie można zsynchronizować pliku z powodu błędu CRC. Ten błąd może wystąpić, jeśli plik warstwowy nie został odwywoływany przed usunięciem punktu końcowego serwera lub jeśli plik jest uszkodzony. | Aby rozwiązać ten problem, zobacz [pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) , aby usunąć oddzielone pliki warstwowe. Jeśli błąd nadal występuje po usunięciu oddzielonych plików warstwowych, uruchom [program CHKDSK](/windows-server/administration/windows-commands/chkdsk) na woluminie. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Nie można zsynchronizować pliku, ponieważ osiągnięto maksymalną liczbę plików konfliktów. Azure File Sync obsługuje pliki konfliktów 100 na plik. Aby dowiedzieć się więcej na temat konfliktów plików, zobacz Azure File Sync [często zadawane pytania](./storage-files-faq.md#afs-conflict-resolution). | Aby rozwiązać ten problem, zmniejsz liczbę plików konfliktów. Plik zostanie zsynchronizowany, gdy liczba plików konfliktów jest mniejsza niż 100. |

#### <a name="handling-unsupported-characters"></a>Obsługa nieobsługiwanych znaków
Jeśli skrypt **FileSyncErrorsReport.ps1** PowerShell pokazuje błędy synchronizacji poszczególnych elementów z powodu nieobsługiwanych znaków (kod błędu 0x8007007B lub 0x80c80255), należy usunąć lub zmienić nazwy znaków z odpowiednich nazw plików. Program PowerShell prawdopodobnie drukuje te znaki jako znaki zapytania lub puste prostokąty, ponieważ większość z tych znaków nie ma standardowego kodowania wizualnego. 
> [!Note]  
> [Narzędzie do oceny](storage-sync-files-planning.md#evaluation-cmdlet) może służyć do identyfikowania znaków, które nie są obsługiwane. Jeśli zestaw danych zawiera kilka plików z nieprawidłowymi znakami, Użyj skryptu [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) , aby zmienić nazwy plików, które zawierają nieobsługiwane znaki.

Poniższa tabela zawiera wszystkie znaki Unicode, Azure File Sync nie są jeszcze obsługiwane.

| Zestaw znaków | Liczba znaków |
|---------------|-----------------|
| <ul><li>0x0000009D (polecenie systemu operacyjnego OSC)</li><li>0x00000090 (ciąg sterowania urządzeniem DCS)</li><li>0x0000008F (SS3 pojedyncze przesunięcie trzy)</li><li>0x00000081 (wysokie ustawienie wstępne oktetu)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (kanał odwrotny wiersza)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Formularze prezentacji arabskiej-a) | 32 |
| 0x0000FFF0-0x0000FFFF (specjalne) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (nie znak)</li><li>0x0002FFFE-0x0002FFFF = 2 (nie znak)</li><li>0x0003FFFE-0x0003FFFF = 2 (nie znak)</li><li>0x0004FFFE-0x0004FFFF = 2 (nie znak)</li><li>0x0005FFFE-0x0005FFFF = 2 (nie znak)</li><li>0x0006FFFE-0x0006FFFF = 2 (nie znak)</li><li>0x0007FFFE-0x0007FFFF = 2 (nie znak)</li><li>0x0008FFFE-0x0008FFFF = 2 (nie znak)</li><li>0x0009FFFE-0x0009FFFF = 2 (nie znak)</li><li>0x000AFFFE-0x000AFFFF = 2 (nie znak)</li><li>0x000BFFFE-0x000BFFFF = 2 (nie znak)</li><li>0x000CFFFE-0x000CFFFF = 2 (nie znak)</li><li>0x000DFFFE-0x000DFFFF = 2 (nie znak)</li><li>0x000EFFFE-0x000EFFFF = 2 (niezdefiniowany)</li><li>0x000FFFFE-0x000FFFFF = 2 (dodatkowy obszar użytku prywatnego)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Typowe błędy synchronizacji
<a id="-2147023673"></a>**Sesja synchronizacji została anulowana.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (dziesiętny)** | -2147023673 | 
| **Ciąg błędu** | ERROR_CANCELLED |
| **Wymagana korekta** | Nie |

Sesje synchronizacji mogą się nie powieść z różnych przyczyn, w tym serwera, który jest ponownie uruchamiany lub aktualizowany, migawki usługi VSS itp. Mimo że ten błąd wygląda na to, że wymaga monitowania, można bezpiecznie zignorować ten błąd, chyba że będzie trwały okres kilku godzin.

<a id="-2147012889"></a>**Nie można nawiązać połączenia z usługą.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (dziesiętny)** | -2147012889 | 
| **Ciąg błędu** | WININET_E_NAME_NOT_RESOLVED |
| **Wymagana korekta** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Żądanie użytkownika zostało ograniczone przez usługę.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (dziesiętny)** | -2134376372 |
| **Ciąg błędu** | ECS_E_USER_REQUEST_THROTTLED |
| **Wymagana korekta** | Nie |

Nie jest wymagana żadna akcja; serwer ponowi próbę. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364043"></a>**Synchronizacja jest zablokowana do momentu zakończenia operacji przywracania zmian**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dziesiętny)** | -2134364043 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Wymagana korekta** | Nie |

Nie jest wymagana żadna akcja. Po przywróceniu pliku lub udziału plików (punktu końcowego w chmurze) przy użyciu Azure Backup synchronizacja jest blokowana do momentu zakończenia wykrywania zmian w udziale plików platformy Azure. Wykrywanie zmian jest uruchamiane natychmiast po zakończeniu przywracania, a czas trwania zależy od liczby plików w udziale plików.

<a id="-2147216747"></a>**Synchronizacja nie powiodła się, ponieważ baza danych synchronizacji została zwolniona.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (dziesiętny)** | -2147216747 |
| **Ciąg błędu** | SYNC_E_METADATA_INVALID_OPERATION |
| **Wymagana korekta** | Nie |

Ten błąd występuje zazwyczaj wtedy, gdy aplikacja kopii zapasowej tworzy migawkę usługi VSS, a baza danych synchronizacji jest zwalniana. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364065"></a>**Synchronizacja nie może uzyskać dostępu do udziału plików platformy Azure określonego w punkcie końcowym w chmurze.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dziesiętny)** | -2134364065 |
| **Ciąg błędu** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ agent usługi Azure File Sync nie może uzyskać dostępu do udziału plików platformy Azure, co może być spowodowane tym, że udział plików platformy Azure lub konto magazynu, na którym udział się znajduje, już nie istnieje. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure już istnieje.](#troubleshoot-azure-file-share)
3. [Upewnij się, że Azure File Sync ma dostęp do konta magazynu.](#troubleshoot-rbac)
4. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Synchronizacja nie powiodła się, ponieważ żądanie nie ma autoryzacji do wykonania tej operacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (dziesiętny)** | -2134351804 |
| **Ciąg błędu** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ agent Azure File Sync nie ma uprawnień dostępu do udziału plików platformy Azure. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure już istnieje.](#troubleshoot-azure-file-share)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Upewnij się, że Azure File Sync ma dostęp do konta magazynu.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Nie można rozpoznać używanej nazwy konta magazynu.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (dziesiętny)** | -2134364064 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Wymagana korekta** | Tak |

1. Sprawdź, czy można rozpoznać nazwę DNS magazynu z serwera.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Wystąpił nieznany błąd podczas uzyskiwania dostępu do konta magazynu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (dziesiętny)** | -2134364022 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Wymagana korekta** | Tak |

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synchronizacja nie powiodła się, ponieważ konto magazynu zostało zablokowane.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (dziesiętny)** | -2134364014 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ konto magazynu ma [blokadę zasobów](../../azure-resource-manager/management/lock-resources.md)tylko do odczytu. Aby rozwiązać ten problem, Usuń blokadę zasobów tylko do odczytu na koncie magazynu. 

<a id="-1906441138"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (dziesiętny)** | -1906441138 |
| **Ciąg błędu** | JET_errWriteConflict |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy występuje problem dotyczący wewnętrznej bazy danych używanej przez Azure File Sync. Jeśli wystąpi ten problem, Utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc Ci rozwiązać ten problem.

<a id="-2134364053"></a>**Wersja agenta Azure File Sync zainstalowana na serwerze nie jest obsługiwana.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (dziesiętny)** | -2134364053 |
| **Ciąg błędu** | ECS_E_AGENT_VERSION_BLOCKED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy wersja agenta usługi Azure File Sync zainstalowana na serwerze nie jest obsługiwana. Aby rozwiązać ten problem, należy [przeprowadzić uaktualnienie]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) do [obsługiwanej wersji agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Osiągnięto limit magazynu udziałów plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (dziesiętny)** | -2134351810 |
| **Ciąg błędu** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Wymagana korekta** | Tak |

Ten błąd występuje po przekroczeniu limitu magazynowania udziałów plików platformy Azure, co może wystąpić w przypadku zastosowania limitu przydziału dla udziału plików platformy Azure lub przekroczenia limitów użycia dla udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [bieżące limity dla udziału plików platformy Azure](storage-files-scale-targets.md).

1. Przejdź do grupy synchronizacji w ramach usługi synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartym okienku.
4. Wybierz połączone konto magazynu. Jeśli łącze nie powiedzie się, konto magazynu, do którego istnieje odwołanie, zostało usunięte.

    ![Zrzut ekranu przedstawiający okienko szczegółów punktu końcowego w chmurze z linkiem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Wybierz pozycję **pliki** , aby wyświetlić listę udziałów plików.
6. Kliknij trzy kropki na końcu wiersza dla udziału plików platformy Azure, do którego odwołuje się punkt końcowy w chmurze.
7. Sprawdź, czy pozycja **Użycie** znajduje się poniżej pozycji **Limit przydziału**. Uwaga o ile nie określono alternatywnego przydziału, przydział będzie pasował do [maksymalnego rozmiaru udziału plików platformy Azure](storage-files-scale-targets.md).

    ![Zrzut ekranu przedstawiający właściwości udziału plików platformy Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Jeśli udział jest pełny i nie ustawiono limitu przydziału, jednym z możliwych rozwiązań tego problemu jest przekształcenie każdego podfolderu bieżącego punktu końcowego serwera we własny punkt końcowy serwera w oddzielnych grupach synchronizacji. W ten sposób każdy podfolder będzie synchronizowany z poszczególnymi udziałami plików platformy Azure.

<a id="-2134351824"></a>**Nie można odnaleźć udziału plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (dziesiętny)** | -2134351824 |
| **Ciąg błędu** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny. Aby rozwiązać problem:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure już istnieje.](#troubleshoot-azure-file-share)

Jeśli udział plików platformy Azure został usunięty, należy utworzyć nowy udział plików, a następnie ponownie utworzyć grupę synchronizacji. 

<a id="-2134364042"></a>**Synchronizacja została wstrzymana, gdy ta subskrypcja platformy Azure jest zawieszona.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (dziesiętny)** | -2134364042 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy subskrypcja platformy Azure jest zawieszona. Synchronizacja zostanie ponownie włączona po przywróceniu subskrypcji platformy Azure. Zobacz [Dlaczego moja subskrypcja platformy Azure jest wyłączona i jak ją uaktywnić?](../../cost-management-billing/manage/subscription-disabled.md) Aby uzyskać więcej informacji.

<a id="-2134375618"></a>**Konto magazynu ma skonfigurowane zaporę lub sieci wirtualne.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (dziesiętny)** | -2134375618 |
| **Ciąg błędu** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny z powodu zapory konta magazynu lub gdy konto magazynu należy do sieci wirtualnej. Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są skonfigurowane prawidłowo. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory i sieci wirtualnej](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (dziesiętny)** | -2134375911 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Wymagana korekta** | Nie |

Ten błąd zwykle rozwiązuje się sam i może wystąpić w następujących przypadkach:

* Duża liczba zmian w plikach na serwerach w grupie synchronizacji.
* Duża liczba błędów poszczególnych plików i katalogów.

Jeśli ten błąd będzie się powtarzać dłużej niż kilka godzin, Utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc w rozwiązaniu tego problemu.

<a id="-2146762487"></a>**Serwer nie może nawiązać bezpiecznego połączenia. Usługa w chmurze odebrała nieoczekiwany certyfikat.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (dziesiętny)** | -2146762487 |
| **Ciąg błędu** | CERT_E_UNTRUSTEDROOT |
| **Wymagana korekta** | Tak |

Ten błąd może wystąpić, jeśli w organizacji jest używany końcowy serwer proxy protokołu TLS lub gdy złośliwa jednostka przechwytuje ruch między serwerem a usługą Azure File Sync. Jeśli masz pewność, że jest to oczekiwane (ponieważ organizacja korzysta z serwera proxy usługi TLS), Pomiń weryfikację certyfikatu z zastępowaniem rejestru.

1. Utwórz wartość rejestru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Uruchom ponownie usługę synchronizacji na zarejestrowanym serwerze.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ustawienie tej wartości rejestru powoduje, że Agent Azure File Sync akceptuje każdy Lokalny zaufany certyfikat TLS/SSL podczas przesyłania danych między serwerem a usługą w chmurze.

<a id="-2147012894"></a>**Nie można nawiązać połączenia z usługą.**  

| | |
|-|-|
| **HRESULT** | 0x80072EE2 |
| **HRESULT (dziesiętny)** | -2147012894 |
| **Ciąg błędu** | WININET_E_TIMEOUT |
| **Wymagana korekta** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizacja nie powiodła się z powodu problemu z uwierzytelnianiem.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (dziesiętny)** | -2134375680 |
| **Ciąg błędu** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Wymagana korekta** | Tak |

Ten błąd zazwyczaj występuje, ponieważ czas serwera jest niepoprawny. Jeśli serwer jest uruchomiony na maszynie wirtualnej, sprawdź, czy czas na hoście jest prawidłowy.

<a id="-2134364040"></a>**Synchronizacja nie powiodła się z powodu wygaśnięcia certyfikatu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (dziesiętny)** | -2134364040 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ wygasł certyfikat używany do uwierzytelniania.

Aby sprawdzić, czy certyfikat wygasł, wykonaj następujące czynności:  
1. Otwórz przystawkę MMC Certyfikaty, wybierz pozycję konto komputera i przejdź do opcji certyfikaty (komputer lokalny) \Personal\Certificates.
2. Sprawdź, czy certyfikat uwierzytelniania klienta wygasł.

Jeśli certyfikat uwierzytelniania klienta wygasł, wykonaj następujące czynności, aby rozwiązać ten problem:

1. Sprawdź, czy zainstalowano agenta Azure File Sync w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizacja nie powiodła się z powodu nieznalezienia certyfikatu uwierzytelniania.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (dziesiętny)** | -2134375896 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ nie znaleziono certyfikatu używanego do uwierzytelniania.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Sprawdź, czy zainstalowano agenta Azure File Sync w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizacja nie powiodła się z powodu nieznalezienia tożsamości uwierzytelniania.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (dziesiętny)** | -2134364039 |
| **Ciąg błędu** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ usuwanie punktu końcowego serwera nie powiodło się, a punkt końcowy jest teraz w stanie częściowo usuniętym. Aby rozwiązać ten problem, spróbuj ponownie usunąć punkt końcowy serwera.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Na woluminie, na którym znajduje się punkt końcowy serwera, jest mało wolnego miejsca na dysku.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dziesiętny)** | -1906441711 |
| **Ciąg błędu** | JET_errLogDiskFull |
| **Wymagana korekta** | Tak |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (dziesiętny)** | -2134375654 |
| **Ciąg błędu** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ wolumin jest pełny. Ten błąd często występuje, gdy pliki spoza punktu końcowego serwera zużywają przestrzeń na woluminie. Zwolnij miejsce na woluminie poprzez dodanie kolejnych punktów końcowych serwera, przeniesienie plików na inny wolumin lub zwiększenie rozmiaru woluminu, na którym znajduje się punkt końcowy serwera.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Usługa nie jest jeszcze gotowa do synchronizacji z tym punktem końcowym serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (dziesiętny)** | -2134364145 |
| **Ciąg błędu** | ECS_E_REPLICA_NOT_READY |
| **Wymagana korekta** | Nie |

Ten błąd występuje, ponieważ punkt końcowy w chmurze został utworzony przy użyciu zawartości już istniejącej w udziale plików platformy Azure. Azure File Sync musi skanować udział plików platformy Azure pod kątem całej zawartości przed umożliwieniem, aby punkt końcowy serwera kontynuował synchronizację początkową.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizacja nie powiodła się z powodu problemów z wieloma pojedynczymi plikami.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dziesiętny)** | -2134375877 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Wymagana korekta** | Tak |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (dziesiętny)** | -2134375908 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Wymagana korekta** | Tak |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (dziesiętny)** | -2134375853 |
| **Ciąg błędu** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Wymagana korekta** | Tak |

Sesje synchronizacji kończą się niepowodzeniem z jednym z tych błędów, gdy istnieje wiele plików, które nie są synchronizowane z błędami poszczególnych elementów. Wykonaj kroki opisane w [Jak mogę Sprawdź, czy istnieją określone pliki lub foldery, które nie są synchronizowane?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) aby rozwiązać błędy poszczególnych elementów. W przypadku błędu synchronizacji ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED Otwórz przypadek pomocy technicznej.

> [!NOTE]
> Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze w celu synchronizowania plików, które mają otwarte dojścia.

<a id="-2134376423"></a>**Synchronizacja nie powiodła się z powodu problemu z ścieżką punktu końcowego serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (dziesiętny)** | -2134376423 |
| **Ciąg błędu** | ECS_E_SYNC_INVALID_PATH |
| **Wymagana korekta** | Tak |

Upewnij się, że ścieżka istnieje, znajduje się na lokalnym woluminie NTFS i nie jest punktem ponownej analizy lub istniejącym punktem końcowym serwera.

<a id="-2134375817"></a>**Synchronizacja nie powiodła się, ponieważ wersja sterownika filtru nie jest zgodna z wersją agenta**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (dziesiętny)** | -2134375817 |
| **Ciąg błędu** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ załadowana wersja sterownika filtru obsługi warstw w chmurze (StorageSync.sys) jest niezgodna z usługą agenta synchronizacji usługi Storage (FileSyncSvc). Jeśli agent usługi Azure File Sync został uaktualniony, ponownie uruchom serwer, aby zakończyć instalację. Jeśli błąd będzie nadal występował, odinstaluj agenta, uruchom ponownie serwer i ponownie zainstaluj agenta usługi Azure File Sync.

<a id="-2134376373"></a>**Usługa jest obecnie niedostępna.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (dziesiętny)** | -2134376373 |
| **Ciąg błędu** | ECS_E_SERVICE_UNAVAILABLE |
| **Wymagana korekta** | Nie |

Ten błąd występuje, ponieważ usługa Azure File Sync jest niedostępna. Ten błąd zostanie automatycznie rozwiązany, gdy usługa Azure File Sync stanie się ponownie dostępna.

<a id="-2146233088"></a>**Synchronizacja nie powiodła się z powodu wyjątku.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (dziesiętny)** | -2146233088 |
| **Ciąg błędu** | COR_E_EXCEPTION |
| **Wymagana korekta** | Nie |

Ten błąd występuje, ponieważ synchronizacja nie powiodła się z powodu wyjątku. Jeśli błąd będzie się powtarzał przez kilka godzin, Utwórz żądanie pomocy technicznej.

<a id="-2134364045"></a>**Synchronizacja nie powiodła się, ponieważ konto magazynu zostało przełączone w tryb failover do innego regionu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (dziesiętny)** | -2134364045 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ konto magazynu zostało przełączone w tryb failover do innego regionu. Usługa Azure File Sync nie obsługuje funkcji przełączania konta magazynu w tryb failover. Kont magazynu zawierających udziały plików platformy Azure używane jako punkty końcowe w chmurze w usłudze Azure File Sync nie należy przełączać w tryb failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych. Aby rozwiązać ten problem, przenieś konto magazynu do regionu podstawowego.

<a id="-2134375922"></a>**Synchronizacja nie powiodła się z powodu przejściowego problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dziesiętny)** | -2134375922 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Wymagana korekta** | Nie |

Ten błąd występuje z powodu wewnętrznego problemu z bazą danych synchronizacji. Ten błąd zostanie automatycznie rozwiązany przy ponownych próbach synchronizacji. Jeśli ten błąd będzie nadal trwać przez dłuższy czas, Utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc w rozwiązaniu tego problemu.

<a id="-2134364024"></a>**Synchronizacja nie powiodła się z powodu zmiany w dzierżawie Azure Active Directory**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (dziesiętny)** | -2134364024 | 
| **Ciąg błędu** | ECS_E_INVALID_AAD_TENANT |
| **Wymagana korekta** | Tak |

Upewnij się, że masz najnowszą Azure File Sync agenta. W przypadku agenta v10 Azure File Sync obsługuje przeniesienie subskrypcji do innej dzierżawy Azure Active Directoryowej.
 
Gdy masz najnowszą wersję agenta, musisz nadać aplikacji Microsoft. StorageSync dostęp do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Synchronizacja nie powiodła się z powodu nieskonfigurowania wyjątku zapory i sieci wirtualnej**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (dziesiętny)** | -2134364010 | 
| **Ciąg błędu** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy ustawienia zapory i sieci wirtualnej są włączone na koncie magazynu, a wyjątek "Zezwalaj na dostęp zaufanych usług firmy Microsoft do tego konta magazynu" nie jest zaznaczony. Aby rozwiązać ten problem, wykonaj czynności opisane w sekcji [Konfigurowanie ustawień zapory i sieci wirtualnej](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w podręczniku wdrażania.

<a id="-2147024891"></a>**Synchronizacja nie powiodła się, ponieważ uprawnienia w folderze informacji o woluminie systemowym są nieprawidłowe.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (dziesiętny)** | -2147024891 |
| **Ciąg błędu** | ERROR_ACCESS_DENIED |
| **Wymagana korekta** | Tak |

Ten błąd może wystąpić, jeśli konto NT AUTHORITY\SYSTEM nie ma uprawnień do folderu System Volume Information na woluminie, na którym znajduje się punkt końcowy serwera. Uwaga Jeśli pojedyncze pliki nie są synchronizowane z ERROR_ACCESS_DENIED, wykonaj kroki opisane w sekcji [Rozwiązywanie problemów według błędów synchronizacji plików/katalogów](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Pobierz narzędzie [Psexec](/sysinternals/downloads/psexec).
2. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia przy użyciu konta systemowego: **PsExec.exe-i-s-d cmd** 
3. Z poziomu wiersza polecenia uruchomionego w ramach konta systemowego uruchom następujące polecenie, aby potwierdzić, że konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information: **cacls "litera dysku:\system volume information" /T /C**
4. Jeśli konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information, uruchom następujące polecenie: **cacls "litera dysku:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Jeśli krok 4 zakończy się niepowodzeniem z powodu odmowy dostępu, uruchom następujące polecenie, aby przejąć na własność folder System Volume Information, a następnie powtórz krok 4: **takeown /A /R /F "litera dysku:\System Volume Information"**

<a id="-2134375810"></a>**Synchronizacja nie powiodła się, ponieważ udział plików platformy Azure został usunięty i utworzony ponownie.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (dziesiętny)** | -2134375810 |
| **Ciąg błędu** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje usuwania i ponownego tworzenia udziału plików platformy Azure w tej samej grupie synchronizacji. 

Aby rozwiązać ten problem, usuń i utwórz ponownie grupę synchronizacji, wykonując następujące czynności:

1. Usuń wszystkie punkty końcowe serwera z grupy synchronizacji.
2. Usuń punkt końcowy w chmurze. 
3. Usuń grupę synchronizacji.
4. Jeśli włączono obsługę warstw w chmurze w punkcie końcowym serwera, Usuń oddzielone pliki warstwowe na serwerze, wykonując kroki opisane w [plikach warstwowych nie są dostępne na serwerze po usunięciu sekcji punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .
5. Utwórz ponownie grupę synchronizacji.

<a id="-2145844941"></a>**Synchronizacja nie powiodła się, ponieważ żądanie HTTP zostało przekierowane**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (dziesiętny)** | -2145844941 |
| **Ciąg błędu** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ Azure File Sync nie obsługuje przekierowania HTTP (kod stanu 3xx). Aby rozwiązać ten problem, należy wyłączyć Przekierowywanie HTTP na serwerze proxy lub urządzeniu sieciowym.

<a id="-2134364027"></a>**Przekroczono limit czasu podczas transferu danych w trybie offline, ale nadal trwa.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (dziesiętny)** | -2134364027 |
| **Ciąg błędu** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Wymagana korekta** | Nie |

Ten błąd występuje, gdy operacja pozyskiwania danych przekracza limit czasu. Ten błąd można zignorować, jeśli synchronizacja jest w toku (AppliedItemCount jest większa niż 0). Zobacz [Jak mogę Monitoruj postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Typowe kroki rozwiązywania problemów
<a id="troubleshoot-storage-account"></a>**Sprawdź, czy konto magazynu istnieje.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy synchronizacji w ramach usługi synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartym okienku.
4. Wybierz połączone konto magazynu. Jeśli łącze nie powiedzie się, konto magazynu, do którego istnieje odwołanie, zostało usunięte.
    ![Zrzut ekranu przedstawiający okienko szczegółów punktu końcowego w chmurze z linkiem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Upewnij się, że udział plików platformy Azure już istnieje.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij pozycję **Przegląd** w spisie treści po lewej stronie, aby powrócić do strony głównej konta magazynu.
2. Wybierz pozycję **pliki** , aby wyświetlić listę udziałów plików.
3. Sprawdź, czy udział plików, do którego odwołuje się punkt końcowy w chmurze, pojawia się na liście udziałów plików (należy to zanotować w kroku 1 powyżej).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Upewnij się, że Azure File Sync ma dostęp do konta magazynu.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij pozycję **Kontrola dostępu (IAM)** po lewej stronie spisu treści.
1. Kliknij kartę **przypisania roli** , aby wyświetlić listę użytkowników i aplikacji (*nazwy główne usługi*), które mają dostęp do konta magazynu.
1. Sprawdź, czy na liście zostanie wyświetlona usługa **Microsoft. StorageSync** lub **hybrydowy File Sync Service** (stara nazwa aplikacji) z rolą **czytelnik i dostęp do danych** . 

    ![Zrzut ekranu jednostki usługi hybrydowej usługi File Sync na karcie Kontrola dostępu konta magazynu](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Jeśli na liście nie ma **programu Microsoft. StorageSync** lub **hybrydowej usługi File Sync** , wykonaj następujące czynności:

    - Kliknij pozycję **Dodaj**.
    - W polu **rola** wybierz pozycję **czytnik i dostęp do danych**.
    - W polu **Wybierz** wpisz **Microsoft. StorageSync**, wybierz rolę, a następnie kliknij przycisk **Zapisz**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Jak mogę uniemożliwić użytkownikom tworzenie plików zawierających nieobsługiwane znaki na serwerze?
Przy użyciu [osłon plików Menedżer zasobów serwera plików](/windows-server/storage/fsrm/file-screening-management) można blokować tworzenie plików z nieobsługiwanymi znakami w ich nazwach na serwerze. Może być konieczne wykonanie tej czynności przy użyciu programu PowerShell, ponieważ większość nieobsługiwanych znaków nie jest drukowana i dlatego należy rzutować ich reprezentacje szesnastkowe jako znaki.

Najpierw utwórz grupę plików Menedżera zasobów serwera plików przy użyciu [polecenia cmdlet New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Ten przykład definiuje grupę, aby zawierała tylko dwa nieobsługiwane znaki, ale można dołączyć dowolną liczbę znaków w grupie plików.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Po zdefiniowaniu grupy plików Menedżera zasobów serwera plików można utworzyć ekran plików Menedżera zasobów serwera plików przy użyciu polecenia cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Należy zauważyć, że osłony plików powinny być używane tylko do blokowania tworzenia znaków nieobsługiwanych przez Azure File Sync. Jeśli ekrany plików są używane w innych scenariuszach, synchronizacja będzie stale próbować pobrać pliki z udziału plików platformy Azure na serwer i zostanie zablokowana z powodu ekranu pliku, co spowodowało duże Wyjście danych. 

## <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Istnieją dwie ścieżki dla błędów w warstwach chmury:

- Do warstwy mogą się nie powieść, co oznacza, że Azure File Sync nie powiodła się próba przetworzenia warstwy pliku do Azure Files.
- Odwoływanie się do plików może zakończyć się niepowodzeniem, co oznacza, że filtr systemu plików Azure File Sync (StorageSync.sys) nie może pobrać danych, gdy użytkownik próbuje uzyskać dostęp do pliku, który został warstwowy.

Istnieją dwie główne klasy błędów, które mogą być wykonywane za pośrednictwem każdej ścieżki błędu:

- Awarie magazynu w chmurze
    - *Przejściowe problemy z dostępnością usługi magazynu*. Aby uzyskać więcej informacji, zobacz [Umowa dotycząca poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Niedostępny udział plików platformy Azure*. Ten błąd zazwyczaj występuje po usunięciu udziału plików platformy Azure, gdy nadal jest to punkt końcowy w chmurze w grupie synchronizacji.
    - *Niedostępne konto magazynu*. Ten błąd zazwyczaj występuje po usunięciu konta magazynu, gdy nadal ma udział plików platformy Azure, który jest punktem końcowym w chmurze grupy synchronizacji. 
- Błędy serwera 
  - *Filtr systemu plików Azure File Sync (StorageSync.sys) nie został załadowany*. Aby można było odpowiedzieć na żądania obsługi warstw/odwołań, należy załadować filtr systemu plików Azure File Sync. Nie można załadować filtru z kilku powodów, ale najbardziej typowym powodem jest to, że administrator został zwolniony ręcznie. Filtr systemu plików Azure File Sync musi być załadowany przez cały czas, aby Azure File Sync działał prawidłowo.
  - *Brak lub uszkodzenie punktu ponownej analizy w innym przypadku*. Punkt ponownej analizy to specjalna struktura danych w pliku, który składa się z dwóch części:
    1. Tag ponownej analizy, który wskazuje systemowi operacyjnemu, że filtr systemu plików Azure File Sync (StorageSync.sys) może potrzebować wykonać pewne akcje na operacji we/wy do pliku. 
    2. Ponowne analizowanie danych wskazujących na system plików filtr identyfikatora URI pliku w skojarzonym punkcie końcowym w chmurze (udział plików platformy Azure). 
        
       Najbardziej typowym sposobem uszkodzenia punktu ponownej analizy jest to, że administrator próbuje zmodyfikować tag lub jego dane. 
  - *Problemy z łącznością sieciową*. Aby można było warstwy lub odwołać plik, serwer musi mieć łączność z Internetem.

Poniższe sekcje zawierają informacje dotyczące rozwiązywania problemów z obsługą warstw w chmurze i określania, czy problem dotyczy magazynu w chmurze, czy problem z serwerem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Jak monitorować aktywność warstw na serwerze  
Aby monitorować aktywność warstwową na serwerze, należy użyć zdarzenia o IDENTYFIKATORze 9003, 9016 i 9029 w dzienniku zdarzeń telemetrii (znajdującym się w obszarze aplikacje i Services\Microsoft\FileSync\Agent w Podgląd zdarzeń).

- Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład całkowita liczba błędów, ErrorCode itp. Zwróć uwagę, że jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9016 zawiera wyniki duplikowania dla woluminu. Na przykład ilość wolnego miejsca (%) to liczba plików zduplikowanych w sesji, liczba plików, których nie można zduplikować itd.
- Identyfikator zdarzenia 9029 zawiera zduplikowane informacje o sesji dla punktu końcowego serwera. Na przykład liczba plików, które próbowano wykonać w sesji, liczba plików warstwowych w sesji, liczba plików, które zostały już warstwowe itp.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Jak monitorować aktywność odwołań na serwerze
Aby monitorować działanie odwoływania na serwerze, należy użyć zdarzenia o IDENTYFIKATORze 9005, 9006, 9009 i 9059 w dzienniku zdarzeń telemetrii (w obszarze aplikacje i Services\Microsoft\FileSync\Agent w Podgląd zdarzeń).

- Identyfikator zdarzenia 9005 zapewnia niezawodność odwoływania się do punktu końcowego serwera. Na przykład całkowita liczba dostępnych unikatowych plików, Łączna liczba unikatowych plików z dostępem zakończonych niepowodzeniem itd.
- Identyfikator zdarzenia 9006 zapewnia dystrybuowanie błędów odwołania dla punktu końcowego serwera. Na przykład całkowita liczba nieudanych żądań, kod błędu itd. Zwróć uwagę, że jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9009 zawiera informacje o sesji odwoływania dla punktu końcowego serwera. Na przykład DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed itd.
- Identyfikator zdarzenia 9059 zapewnia dystrybucję odwoływania aplikacji dla punktu końcowego serwera. Na przykład ShareId, nazwa aplikacji i TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Jak rozwiązywać problemy z niepowodzeniem warstwy
Jeśli nie ma warstwy do Azure Files:

1. W Podgląd zdarzeń zapoznaj się z dziennikami danych telemetrycznych, operacyjnych i zdarzeń diagnostycznych, które znajdują się w obszarze aplikacje i Services\Microsoft\FileSync\Agent. 
   1. Sprawdź, czy pliki istnieją w udziale plików platformy Azure.

      > [!NOTE]
      > Aby można było korzystać z warstw, należy synchronizować plik z udziałem plików platformy Azure.

   2. Sprawdź, czy serwer ma łączność z Internetem. 
   3. Sprawdź, czy Azure File Sync sterowniki filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
       - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy na liście znajdują się sterowniki filtrów StorageSync.sys i StorageSyncGuard.sys.

> [!NOTE]
> Zdarzenie o IDENTYFIKATORze 9003 jest rejestrowane raz w dzienniku zdarzeń telemetrii, jeśli plik nie ulegnie awarii (jedno zdarzenie jest rejestrowane na kod błędu). Sprawdź [błędy i sekcję korygowanie warstw](#tiering-errors-and-remediation) , aby sprawdzić, czy w kodzie błędu są wymienione czynności zaradcze.

### <a name="tiering-errors-and-remediation"></a>Błędy i korygowanie warstw

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Nie można wykonać warstwy dla pliku, ponieważ trwa wstępne przekazywanie. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy po zakończeniu ładowania początkowego. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Nie można wykonać warstwy dla pliku, ponieważ jest on używany. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy, gdy nie jest już używany. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Nie można wykonać warstwy dla pliku, ponieważ jest on wykluczony przez synchronizację. | Żadna akcja nie jest wymagana. Pliki na liście wykluczeń synchronizacji nie mogą być warstwowe. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Nie można wykonać warstwy dla pliku, ponieważ nie znaleziono go na serwerze. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź, czy plik istnieje na serwerze. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Nie można wykonać warstwy dla pliku, ponieważ został on usunięty w udziale plików platformy Azure. | Żadna akcja nie jest wymagana. Plik powinien zostać usunięty na serwerze, gdy zostanie uruchomiona Następna sesja synchronizacji pobierania. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Nie można przeprowadzić warstwy dla pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Nie można przeprowadzić warstwy dla pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Nie można przeprowadzić warstwy dla pliku z powodu błędu odmowy dostępu. Ten błąd może wystąpić, jeśli plik znajduje się w folderze replikacji tylko do odczytu systemu plików DFS. | Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Aby uzyskać więcej informacji, zobacz [Przewodnik planowania](./storage-sync-files-planning.md#distributed-file-system-dfs) . |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Nie można przeprowadzić warstwy dla pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Nie można wykonać warstwy dla pliku, ponieważ rozmiar pliku jest mniejszy niż obsługiwany rozmiar. | Jeśli wersja agenta jest mniejsza niż 9,0, minimalny obsługiwany rozmiar pliku to 64 KB. Jeśli wersja agenta to 9,0 i nowszego, minimalny obsługiwany rozmiar pliku zależy od rozmiaru klastra systemu plików (rozmiar klastra o podwójnej wielkości plików). Jeśli na przykład rozmiar klastra systemu plików to 4 KB, minimalny rozmiar pliku to rozmiarze 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Nie można przeprowadzić warstwy dla pliku z powodu problemu z usługą Azure Storage. | Jeśli błąd będzie się powtarzać, Otwórz żądanie obsługi. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Nie można wykonać warstwy dla pliku, ponieważ został on odwywoływany w tym samym czasie. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy po zakończeniu odwoływania, a plik nie jest już używany. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Nie można wykonać warstwy dla pliku, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Nie można wykonać warstwy dla pliku, ponieważ nie uruchomiono sterownika filtru warstwowego chmury (storagesync.sys). | Aby rozwiązać ten problem, Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie: `fltmc load storagesync`<br>Jeśli nie można załadować sterownika filtru storagesync podczas uruchamiania polecenia polecenie fltmc, Odinstaluj agenta Azure File Sync, ponownie uruchom serwer i ponownie zainstaluj agenta Azure File Sync. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Brak warstwy pliku z powodu niewystarczającej ilości miejsca na dysku w woluminie, na którym znajduje się punkt końcowy serwera. | Aby rozwiązać ten problem, zwolnij co najmniej 100 MB miejsca na dysku w woluminie, na którym znajduje się punkt końcowy serwera. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Nie można wykonać warstwy dla pliku, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Nie można wykonać warstwy dla pliku, ponieważ jest on nieobsługiwanym punktem ponownej analizy. | Jeśli plik jest punktem ponownej analizy deduplikacji danych, wykonaj kroki opisane w [przewodniku planowania](./storage-sync-files-planning.md#data-deduplication) , aby włączyć obsługę deduplikacji danych. Pliki z punktami ponownej analizy inne niż Deduplikacja danych nie są obsługiwane i nie zostaną warstwowe.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Nie można wykonać warstwy dla pliku, ponieważ został on zmodyfikowany. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy, gdy zmodyfikowany plik zostanie zsynchronizowany z udziałem plików platformy Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Nie można wykonać warstwy dla pliku, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80072EE2 | -2147012894 | WININET_E_TIMEOUT | Nie można przeprowadzić warstwy dla pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Nie można wykonać warstwy dla pliku, ponieważ został on zmodyfikowany. | Żadna akcja nie jest wymagana. Plik zostanie warstwowy, gdy zmodyfikowany plik zostanie zsynchronizowany z udziałem plików platformy Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Nie można przeprowadzić warstwy dla pliku z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra wyczerpuje zasoby systemowe. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Nie można przeprowadzić warstwy dla pliku z powodu błędu we/wy podczas zapisywania do bazy danych warstw chmury. | Jeśli błąd będzie się powtarzać, uruchom polecenie chkdsk na woluminie i Sprawdź sprzęt magazynu. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Nie można wykonać warstwy dla pliku, ponieważ baza danych warstw chmury nie jest uruchomiona. | Aby rozwiązać ten problem, uruchom ponownie usługę lub serwer FileSyncSvc. Jeśli błąd będzie się powtarzać, uruchom polecenie chkdsk na woluminie i Sprawdź sprzęt magazynu. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Jak rozwiązywać problemy z niepowodzeniem odwoływania się do plików  
Jeśli nie można odwołać plików:
1. W Podgląd zdarzeń zapoznaj się z dziennikami danych telemetrycznych, operacyjnych i zdarzeń diagnostycznych, które znajdują się w obszarze aplikacje i Services\Microsoft\FileSync\Agent.
    1. Sprawdź, czy pliki istnieją w udziale plików platformy Azure.
    2. Sprawdź, czy serwer ma łączność z Internetem. 
    3. Otwórz przystawkę MMC usług i sprawdź, czy jest uruchomiona usługa agenta synchronizacji magazynu (FileSyncSvc).
    4. Sprawdź, czy Azure File Sync sterowniki filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
        - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy na liście znajdują się sterowniki filtrów StorageSync.sys i StorageSyncGuard.sys.

> [!NOTE]
> Zdarzenie o IDENTYFIKATORze 9006 jest rejestrowane raz na godzinę w dzienniku zdarzeń telemetrii, jeśli nie można odwołać pliku (jedno zdarzenie jest rejestrowane na kod błędu). Zapoznaj się z sekcją [Błędy odwołania i korygowanie](#recall-errors-and-remediation) , aby sprawdzić, czy w kodzie błędu są wymienione czynności zaradcze.

### <a name="recall-errors-and-remediation"></a>Błędy i korygowanie odwołania

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Odzyskanie pliku nie powiodło się z powodu przekroczenia limitu czasu operacji we/wy. Ten problem może wystąpić z kilku powodów: ograniczenia zasobów serwera, niska łączność sieciowa lub problem z usługą Azure Storage (na przykład ograniczenie przepustowości). | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać przez kilka godzin, otwórz zgłoszenie do pomocy technicznej. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Odzyskanie pliku nie powiodło się z powodu problemu z siecią.  | Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Odwoływanie pliku nie powiodło się, ponieważ punkt końcowy serwera został usunięty. | Aby rozwiązać ten problem, zobacz [pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Nie można odwołać pliku z powodu błędu odmowy dostępu. Ten problem może wystąpić, jeśli ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Aby rozwiązać ten problem, Dodaj adres IP lub sieć wirtualną serwera, wykonując czynności opisane w sekcji [Konfigurowanie ustawień zapory i sieci wirtualnej](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w Podręczniku wdrażania. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Nie można odwołać pliku, ponieważ nie jest on dostępny w udziale plików platformy Azure. | Aby rozwiązać ten problem, sprawdź, czy plik istnieje w udziale plików platformy Azure. Jeśli plik istnieje w udziale plików platformy Azure, przeprowadź uaktualnienie do najnowszej [wersji agenta](./storage-files-release-notes.md#supported-versions)Azure File Sync. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Nie można odwołać pliku z powodu błędu autoryzacji dla konta magazynu. | Aby rozwiązać ten problem, sprawdź, czy [Azure File Sync ma dostęp do konta magazynu](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Nie można odwołać pliku, ponieważ udział plików platformy Azure jest niedostępny. | Sprawdź, czy udział plików istnieje i jest dostępny. Jeśli udział plików został usunięty i ponownie utworzony, wykonaj kroki opisane w sekcji [Synchronizacja nie powiodła się, ponieważ udział plików platformy Azure został usunięty i ponownie utworzony](?tabs=portal1%252cazure-portal#-2134375810) , aby usunąć i ponownie utworzyć grupę synchronizacji. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Nie można odwołać pliku z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra wyczerpuje zasoby systemowe. |
| 0x8007000E | -2147024882 | ERROR_OUTOFMEMORY | Nie można odwołać pliku z powodu niewystarczającej ilości pamięci. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra powoduje wystąpienie niskiej ilości pamięci. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Nie można odwołać pliku z powodu niewystarczającej ilości miejsca na dysku. | Aby rozwiązać ten problem, zwolnij miejsce na woluminie przez przeniesienie plików na inny wolumin, zwiększenie rozmiaru woluminu lub wymuszenie plików do warstwy za pomocą polecenia cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Pliki wielowarstwowe są niedostępne na serwerze po usunięciu punktu końcowego serwera
Pliki warstwowe na serwerze staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera.

Błędy zarejestrowane, jeśli pliki warstwowe nie są dostępne
- Podczas synchronizowania pliku kod błędu — 2147942467 (0x80070043-ERROR_BAD_NET_NAME) jest rejestrowany w dzienniku zdarzeń ItemResults
- Podczas ponownego wywoływania pliku kod błędu — 2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) jest rejestrowany w dzienniku zdarzeń RecallResults

Przywrócenie dostępu do plików warstwowych jest możliwe w przypadku spełnienia następujących warunków:
- Punkt końcowy serwera został usunięty w ciągu ostatnich 30 dni
- Punkt końcowy w chmurze nie został usunięty 
- Udział plików nie został usunięty
- Grupa synchronizacji nie została usunięta

Jeśli powyższe warunki są spełnione, można przywrócić dostęp do plików na serwerze przez ponowne utworzenie punktu końcowego serwera w tej samej ścieżce na serwerze w ramach tej samej grupy synchronizacji w ciągu 30 dni. 

Jeśli powyższe warunki nie są spełnione, przywrócenie dostępu nie jest możliwe, ponieważ te pliki warstwowe na serwerze są teraz oddzielone. Postępuj zgodnie z poniższymi instrukcjami, aby usunąć oddzielone pliki warstwowe.

**Uwagi**
- Gdy pliki warstwowe nie są dostępne na serwerze, cały plik powinien być dostępny, jeśli bezpośredni dostęp do udziału plików platformy Azure.
- Aby uniemożliwić oddzielone pliki warstwowe w przyszłości, wykonaj kroki opisane w temacie [Usuwanie punktu końcowego serwera](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) podczas usuwania punktu końcowego serwera.

<a id="get-orphaned"></a>**Jak uzyskać listę oddzielonych plików warstwowych** 

1. Sprawdź, czy zainstalowano agenta Azure File Sync w wersji 5.1 lub nowszej.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Zapisz plik wyjściowy OrphanTieredFiles.txt w przypadku, gdy pliki zostaną przywrócone z kopii zapasowej po ich usunięciu.

<a id="remove-orphaned"></a>**Jak usunąć oddzielone pliki warstwowe** 

*Opcja 1: usuwanie oddzielonych plików warstwowych*

Ta opcja usuwa oddzielone pliki warstwowe na serwerze z systemem Windows, ale wymaga usunięcia punktu końcowego serwera, jeśli istnieje, z powodu ponownego tworzenia po upływie 30 dni lub połączenia z inną grupą synchronizacji. Konflikty plików będą nastąpić, jeśli pliki zostaną zaktualizowane w systemie Windows Server lub udziale plików platformy Azure przed odtworzeniem punktu końcowego serwera.

1. Sprawdź, czy zainstalowano agenta Azure File Sync w wersji 5.1 lub nowszej.
2. Utwórz kopię zapasową udziału plików platformy Azure i lokalizacji punktu końcowego serwera.
3. Usuń punkt końcowy serwera w grupie synchronizacji (jeśli istnieje), wykonując czynności opisane w temacie [Usuwanie punktu końcowego serwera](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Jeśli punkt końcowy serwera nie zostanie usunięty przed użyciem polecenia cmdlet Remove-StorageSyncOrphanedTieredFiles, usunięcie oddzielonego pliku warstwowego na serwerze spowoduje usunięcie całego pliku z udziału plików platformy Azure. 

4. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Zapisz plik wyjściowy OrphanTieredFiles.txt w przypadku, gdy pliki zostaną przywrócone z kopii zapasowej po ich usunięciu.
6. Uruchom następujące polecenia programu PowerShell, aby usunąć oddzielone pliki warstwowe:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Uwagi** 
- Pliki warstwowe zmodyfikowane na serwerze, które nie są synchronizowane z udziałem plików platformy Azure, zostaną usunięte.
- Pliki warstwowe, które są dostępne (oddzielone) nie zostaną usunięte.
- Pliki niewarstwowe pozostaną na serwerze.

7. Opcjonalne: Utwórz ponownie punkt końcowy serwera, jeśli został usunięty w kroku 3.

*Opcja 2: Instalowanie udziału plików platformy Azure i kopiowanie plików lokalnie, które są oddzielone na serwerze*

Ta opcja nie wymaga usunięcia punktu końcowego serwera, ale wymaga wystarczającej ilości miejsca na dysku do kopiowania pełnych plików lokalnie.

1. [Zainstaluj](./storage-how-to-use-files-windows.md) udział plików platformy Azure na serwerze z systemem Windows, który ma oddzielone pliki warstwowe.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Użyj pliku wyjściowego OrphanTieredFiles.txt, aby zidentyfikować oddzielone pliki warstwowe na serwerze.
4. Zastąp oddzielone pliki warstwowe, kopiując pełny plik z udziału plików platformy Azure do systemu Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Jak rozwiązywać problemy z nieoczekiwanym wywołaniem plików na serwerze  
Program antywirusowy, kopia zapasowa i inne aplikacje, które odczytują dużą liczbę plików, powodują niezamierzone wywołania, chyba że przestrzegają one atrybutu pomijania offline i pomijają odczytywanie zawartości tych plików. Pomijanie plików offline w przypadku produktów, które obsługują tę opcję, umożliwia uniknięcie niezamierzonych odwołań podczas operacji takich jak skanowanie antywirusowe lub zadania kopii zapasowej.

Skonsultuj się z dostawcą oprogramowania, aby dowiedzieć się, w jaki sposób skonfigurować rozwiązanie tak, aby odczytywanie plików offline było pomijane.

Niezamierzone odwołania mogą również wystąpić w innych scenariuszach, na przykład podczas przeglądania plików w Eksploratorze plików. Otwarcie folderu, w którym znajdują się pliki w warstwach chmury, w Eksploratorze plików na serwerze może spowodować niezamierzone odwołania. Prawdopodobieństwo wystąpienia takiej sytuacji jest wyższe, jeśli na serwerze włączono rozwiązanie antywirusowe.

> [!NOTE]
>Użyj zdarzenia o IDENTYFIKATORze 9059 w dzienniku zdarzeń telemetrii, aby określić, które aplikacje powodują wywoływanie. To zdarzenie zapewnia dystrybucję odwołań aplikacji dla punktu końcowego serwera i jest rejestrowane raz na godzinę.

### <a name="tls-12-required-for-azure-file-sync"></a>Protokół TLS 1,2 wymagany do Azure File Sync

Ustawienia protokołu TLS można wyświetlić na serwerze, przeglądając [Ustawienia rejestru](/windows-server/security/tls/tls-registry-settings). 

Jeśli używasz serwera proxy, zapoznaj się z dokumentacją serwera proxy i upewnij się, że jest on skonfigurowany do korzystania z protokołu TLS 1.2.

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów
Jeśli wystąpią problemy z Azure File Sync na serwerze, należy najpierw wykonać następujące czynności:
1. W Podgląd zdarzeń, Przejrzyj dzienniki zdarzeń telemetrii, operacyjne i diagnostyczne.
    - Problemy z synchronizacją, warstwami i odwołaniami są rejestrowane w dziennikach danych telemetrycznych, diagnostycznych i operacyjnych w obszarze aplikacje i Services\Microsoft\FileSync\Agent.
    - Problemy związane z zarządzaniem serwerem (na przykład ustawienia konfiguracji) są rejestrowane w dziennikach zdarzeń operacyjnych i diagnostycznych w obszarze aplikacje i Services\Microsoft\FileSync\Management.
2. Sprawdź, czy usługa Azure File Sync jest uruchomiona na serwerze:
    - Otwórz przystawkę MMC usług i sprawdź, czy jest uruchomiona usługa agenta synchronizacji magazynu (FileSyncSvc).
3. Sprawdź, czy Azure File Sync sterowniki filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
    - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy na liście znajdują się sterowniki filtrów StorageSync.sys i StorageSyncGuard.sys.

Jeśli problem nie zostanie rozwiązany, uruchom narzędzie AFSDiag i Wyślij plik. zip do inżyniera pomocy technicznej przypisanego do Twojego przypadku w celu przeprowadzenia dalszej diagnostyki.

Aby uruchomić AFSDiag, wykonaj poniższe kroki.

W przypadku agenta w wersji v11 lub nowszej:
1. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (naciśnij klawisz Enter po każdym poleceniu):

    > [!NOTE]
    >AFSDiag utworzy katalog wyjściowy i folder Temp w nim przed zbieraniem dzienników i spowoduje usunięcie folderu tymczasowego po wykonaniu. Określ lokalizację wyjściową, która nie zawiera danych.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Odtwórz problem. Po zakończeniu wprowadź **D**.
3. Plik. zip zawierający pliki dzienników i plików śledzenia jest zapisywany w katalogu wyjściowym, który został określony. 

W przypadku agenta w wersji V10 i starszych:
1. Utwórz katalog, w którym zostaną zapisane dane wyjściowe AFSDiag (na przykład C:\Output).
    > [!NOTE]
    >AFSDiag usunie całą zawartość z katalogu wyjściowego przed zbieraniem dzienników. Określ lokalizację wyjściową, która nie zawiera danych.
2. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (naciśnij klawisz Enter po każdym poleceniu):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Na poziomie śledzenia Azure File Sync trybu jądra wprowadź **1** (jeśli nie określono inaczej), aby utworzyć więcej śladów pełnych, a następnie naciśnij klawisz ENTER.
4. Dla poziomu śledzenia Azure File Sync trybu użytkownika wprowadź wartość **1** (chyba że określono inaczej, aby utworzyć więcej śladów pełnych), a następnie naciśnij klawisz ENTER.
5. Odtwórz problem. Po zakończeniu wprowadź **D**.
6. Plik. zip zawierający pliki dzienników i plików śledzenia jest zapisywany w katalogu wyjściowym, który został określony.


## <a name="see-also"></a>Zobacz też
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Azure Files często zadawane pytania](storage-files-faq.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
