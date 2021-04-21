---
title: Rozwiązywanie problemów Azure File Sync | Microsoft Docs
description: Rozwiązywanie typowych problemów we wdrożeniu na platformie Azure File Sync, których można użyć do przekształcenia systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/20/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 386a95b46bd4787ea9ad2925ea1d2b2a0627a05e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797209"
---
# <a name="troubleshoot-azure-file-sync"></a>Rozwiązywanie problemów z usługą Azure File Sync
Użyj Azure File Sync, aby scentralizować udziały plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

Ten artykuł ma na celu pomoc w rozwiązywaniu i rozwiązywaniu problemów, które mogą wystąpić podczas Azure File Sync wdrożenia. Opisano również sposób zbierania ważnych dzienników z systemu, jeśli wymagane jest bardziej dogłębne badanie problemu. Jeśli nie widzisz odpowiedzi na swoje pytanie, możesz skontaktować się z nami za pośrednictwem następujących kanałów (w kolejności eskalacji):

- [Microsoft Q&Pytanie dotyczące usługi Azure Storage.](/answers/products/azure?product=storage)
- [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
- Pomoc techniczna Microsoft. Aby utworzyć nowy wniosek o pomoc techniczną,  w Azure Portal pomocy wybierz przycisk Pomoc **i obsługa techniczna,** a następnie wybierz pozycję **Nowy wniosek o pomoc techniczną.**

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mam problem z Azure File Sync na serwerze (synchronizacja, warstwy w chmurze itp.). Czy należy usunąć i ponownie utworzyć punkt końcowy serwera?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalacja agenta i rejestracja serwera
<a id="agent-installation-failures"></a>**Rozwiązywanie problemów z błędami instalacji agenta**  
Jeśli instalacja Azure File Sync agenta nie powiedzie się, w wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenie, aby włączyć rejestrowanie podczas instalacji agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Przejrzyj plik installer.log, aby ustalić przyczynę niepowodzenia instalacji.

<a id="agent-installation-gpo"></a>**Instalacja agenta kończy się niepowodzeniem z powodu błędu: agent synchronizacji magazynu Kreator instalacji przedwcześnie zakończył się z powodu błędu**

W dzienniku instalacji agenta jest rejestrowany następujący błąd:

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

Ten problem występuje, jeśli zasady wykonywania [programu PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) są skonfigurowane przy użyciu zasad grupy, a ustawienie zasad to "Zezwalaj tylko na podpisane skrypty". Wszystkie skrypty dołączone do agenta Azure File Sync są podpisane. Instalacja Azure File Sync nie powiedzie się, ponieważ instalator wykonuje skrypt przy użyciu ustawienia zasad Pomiń wykonywanie.

Aby rozwiązać ten problem, tymczasowo wyłącz [ustawienie](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) zasad grupy Włącz wykonywanie skryptu na serwerze. Po zakończeniu instalacji agenta można ponownie włączyć ustawienie zasad grupy.

<a id="agent-installation-on-DC"></a>**Instalacja agenta na kontrolerze domena usługi Active Directory kończy się niepowodzeniem**  
Jeśli spróbujesz zainstalować agenta synchronizacji na kontrolerze domeny usługi Active Directory, w którym właściciel roli podstawowego kontrolera domeny znajduje się w wersji systemu operacyjnego Windows Server 2008 R2 lub awarii, może wystąpić problem, który spowoduje niepowodzenie instalacji agenta synchronizacji.

Aby rozwiązać ten problem, przenieś rolę podstawowego kontrolera domeny na inny kontroler domeny z systemem Windows Server 2012 R2 lub jego najnowszą wersji, a następnie zainstaluj synchronizację.

<a id="parameter-is-incorrect"></a>**Uzyskiwanie dostępu do woluminu w systemie Windows Server 2012 R2 kończy się niepowodzeniem z błędem: Parametr jest nieprawidłowy**  
Po utworzeniu punktu końcowego serwera w systemie Windows Server 2012 R2 podczas uzyskiwania dostępu do woluminu występuje następujący błąd:

driveletter:\ nie jest dostępny.  
Parametr jest nieprawidłowy.

Aby rozwiązać ten problem, zainstaluj [aktualizację KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) i uruchom ponownie serwer. Jeśli ta aktualizacja nie zostanie zainstalowana, ponieważ jest już zainstalowana, przejdź do witryny Windows Update, zainstaluj najnowsze aktualizacje systemu Windows Server 2012 R2 i uruchom ponownie serwer.

<a id="server-registration-missing-subscriptions"></a>**Rejestracja serwera nie zawiera wszystkich subskrypcji platformy Azure**  
Podczas rejestrowania serwera przy użyciu ServerRegistration.exe subskrypcji brakuje subskrypcji po kliknięciu listy rozwijanej Subskrypcja platformy Azure.

Ten problem występuje, ServerRegistration.exe pobiera subskrypcje tylko z pierwszych 5 dzierżaw usługi Azure AD. 

Aby zwiększyć limit dzierżawy rejestracji serwera na serwerze, utwórz wartość DWORD o nazwie ServerRegistrationTenantLimit w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync o wartości większej niż 5.

Możesz również obejść ten problem, używając następujących poleceń programu PowerShell do zarejestrowania serwera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Rejestracja serwera wyświetla następujący komunikat: "Brakuje wymagań wstępnych"**  
Ten komunikat jest wyświetlany, jeśli moduł Az lub AzureRM PowerShell nie jest zainstalowany w programie PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe nie obsługuje programu PowerShell 6.x. Aby zarejestrować serwer, Register-AzStorageSyncServer polecenia cmdlet programu PowerShell 6.x.

Aby zainstalować moduł Az lub AzureRM w programie PowerShell 5.1, wykonaj następujące kroki:

1. Wpisz **powershell w** wierszu polecenia z podwyższonym poziomem uprawnień i naciśnij klawisz Enter.
2. Zainstaluj najnowszy moduł Az lub AzureRM, korzystając z dokumentacji:
    - [Moduł Az (wymaga .NET 4.7.2)](/powershell/azure/install-az-ps)
    - [Moduł AzureRM](https://go.microsoft.com/fwlink/?linkid=856959)
3. Uruchom ServerRegistration.exe i ukończ kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

<a id="server-already-registered"></a>**Rejestracja serwera wyświetla następujący komunikat: "Ten serwer jest już zarejestrowany"** 

![Zrzut ekranu przedstawiający okno dialogowe Rejestracji serwera z komunikatem o błędzie "Serwer jest już zarejestrowany"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ten komunikat jest wyświetlany, jeśli serwer został wcześniej zarejestrowany w usłudze synchronizacji magazynu. Aby wyrejestrować serwer z bieżącej usługi synchronizacji magazynu, a następnie zarejestrować się w nowej usłudze synchronizacji magazynu, wykonaj kroki opisane w te tematze [Wyrejestrowanie](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)serwera za pomocą Azure File Sync .

Jeśli serwer nie znajduje  się na liście zarejestrowane serwery w usłudze synchronizacji magazynu, na serwerze, który chcesz wyrejestrować, uruchom następujące polecenia programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Jeśli serwer jest częścią klastra, możesz również usunąć rejestrację klastra za pomocą opcjonalnego parametru *Reset-StorageSyncServer -CleanClusterRegistration.*

<a id="web-site-not-trusted"></a>**Podczas rejestrowania serwera widzę wiele odpowiedzi "witryna internetowa nie jest zaufana". Dlaczego?**  
Ten problem występuje, gdy rozszerzone **zasady Internet Explorer zabezpieczeń** są włączone podczas rejestracji serwera. Aby uzyskać więcej informacji na temat poprawnego wyłączania zasad **zwiększonych zabezpieczeń usługi Internet Explorer,** zobacz Przygotowanie systemu [Windows Server](file-sync-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) do użycia z programem Azure File Sync i Jak [wdrożyć Azure File Sync](file-sync-deployment-guide.md).

<a id="server-registration-missing"></a>**Serwer nie znajduje się na liście zarejestrowanych serwerów w Azure Portal**  
Jeśli serwer nie znajduje się na liście **w obszarze Zarejestrowane serwery** dla usługi synchronizacji magazynu:
1. Zaloguj się na serwerze, który chcesz zarejestrować.
2. Otwórz Eksplorator plików, a następnie przejdź do katalogu instalacyjnego agenta synchronizacji magazynu (domyślna lokalizacja to C:\Program Files\Azure\StorageSyncAgent). 
3. Uruchom ServerRegistration.exe i ukończ kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

## <a name="sync-group-management"></a>Zarządzanie grupą synchronizacji

### <a name="cloud-endpoint-creation-errors"></a>Błędy tworzenia punktu końcowego w chmurze

<a id="cloud-endpoint-using-share"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem z tym błędem: "Określony udział plików platformy Azure jest już w użyciu przez inny punkt końcowy CloudEndpoint"**  
Ten błąd występuje, jeśli udział plików platformy Azure jest już używany przez inny punkt końcowy w chmurze. 

Jeśli widzisz ten komunikat, a udział plików platformy Azure nie jest obecnie w użyciu przez punkt końcowy w chmurze, wykonaj następujące kroki, aby wyczyścić metadane Azure File Sync udziału plików platformy Azure:

> [!Warning]  
> Usunięcie metadanych w udziałach plików platformy Azure, które są obecnie w użyciu przez punkt końcowy w chmurze, powoduje niepowodzenie Azure File Sync plików. Jeśli następnie użyjemy tego udziału plików do synchronizacji w innej grupie synchronizacji, utrata danych dla plików w starej grupie synchronizacji będzie prawie pewna.

1. W witrynie Azure Portal przejdź do udziału plików platformy Azure.  
2. Kliknij prawym przyciskiem myszy udział plików platformy Azure, a następnie wybierz polecenie **Edytuj metadane.**
3. Kliknij prawym przyciskiem myszy **pozycję SyncService,** a następnie wybierz polecenie **Usuń.**

<a id="cloud-endpoint-authfailed"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem z tym błędem: "AuthorizationFailed"**  
Ten błąd występuje, gdy konto użytkownika nie ma wystarczających uprawnień do utworzenia punktu końcowego w chmurze. 

Aby utworzyć punkt końcowy w chmurze, twoje konto użytkownika musi mieć następujące uprawnienia autoryzacji firmy Microsoft:  
* Odczyt: uzyskiwanie definicji roli
* Zapis: tworzenie lub aktualizowanie definicji roli niestandardowej
* Odczyt: Uzyskiwanie przypisania roli
* Zapis: Tworzenie przypisania roli

Następujące wbudowane role mają wymagane uprawnienia autoryzacji firmy Microsoft:  
* Właściciel
* Administrator dostępu użytkowników

Aby określić, czy rola konta użytkownika ma wymagane uprawnienia:  
1. W Azure Portal wybierz pozycję **Grupy zasobów.**
2. Wybierz grupę zasobów, w której znajduje się konto magazynu, a następnie wybierz **pozycję Kontrola dostępu (IAM).**
3. Wybierz **kartę Przypisania** ról.
4. Wybierz **rolę** (na przykład Właściciel lub Współautor) dla konta użytkownika.
5. Na liście **Dostawca zasobów** wybierz pozycję **Autoryzacja firmy Microsoft.** 
    * **Przypisanie roli** powinno mieć **uprawnienia do** odczytu **i** zapisu.
    * **Definicja roli** powinna mieć **uprawnienia do odczytu** **i** zapisu.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Błędy tworzenia i usuwania punktu końcowego serwera

<a id="-2134375898"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134375898 lub 0x80c80226)**  
Ten błąd występuje, gdy ścieżka punktu końcowego serwera znajduje się na woluminie systemowym i obsługa warstw w chmurze jest włączona. Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.

<a id="-2147024894"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2147024894 lub 0x80070002)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera jest nieprawidłowa. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Usługa Azure File Sync nie obsługuje zamapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134375640"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134375640 lub 0x80c80328)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera nie jest woluminem NTFS. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Usługa Azure File Sync nie obsługuje zamapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134347507"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134347507 lub 0x80c8710d)**  
Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje punktów końcowych serwera na woluminach, które mają skompresowany katalog informacji o woluminie systemowym. Aby rozwiązać ten problem, zdekompresuj katalog informacji o woluminie systemowym. Jeśli katalog informacji o woluminie systemowym jest jedynym skompresowanym katalogiem na woluminie, wykonaj następujące czynności:

1. Pobierz [narzędzie PsExec.](/sysinternals/downloads/psexec)
2. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia uruchomiony na koncie systemowym: **PsExec.exe -i -s -d cmd**
3. W wierszu polecenia uruchomionym w ramach konta systemowego wpisz następujące polecenia i naciśnij klawisz Enter:   
    **cd /d "litera dysku:\Informacje o woluminie systemowym"**  
    **compact /u /s**

<a id="-2134376345"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134376345 lub 0x80C80067)**  
Ten błąd występuje, gdy zostanie osiągnięty limit liczby punktów końcowych serwera. Usługa Azure File Sync obsługuje obecnie do 30 punktów końcowych serwera na serwer. Aby uzyskać więcej informacji, zobacz [Azure File Sync skalowania docelowego](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134376427 lub 0x80c80015)**  
Ten błąd występuje, gdy inny punkt końcowy serwera synchronizuje określoną ścieżkę punktu końcowego serwera. Usługa Azure File Sync nie obsługuje synchronizacji tego samego katalogu lub woluminu w wielu punktach końcowych serwera.

<a id="-2160590967"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem z tym błędem: "MgmtServerJobFailed" (kod błędu: -2160590967 lub 0x80c80077)**  
Ten błąd występuje, jeśli ścieżka punktu końcowego serwera zawiera oddzielone pliki warstwowe. Jeśli punkt końcowy serwera został niedawno usunięty, poczekaj na zakończenie czyszczenia oddzielonych plików warstwowych. Zdarzenie o identyfikatorze 6662 jest rejestrowane w dzienniku zdarzeń telemetrii po zakończeniu oddzielonego oczyszczania plików warstwowych. Zdarzenie o identyfikatorze 6661 jest rejestrowane po ukończeniu oddzielonego oczyszczania plików warstwowych i ponownego tworzenia punktu końcowego serwera przy użyciu ścieżki. Jeśli tworzenie punktu końcowego serwera zakończy się niepowodzeniem po zakończeniu oczyszczania plików warstwowych lub jeśli w dzienniku zdarzeń telemetrii nie można znaleźć identyfikatora zdarzenia 6661 z powodu przechowania dziennika zdarzeń, usuń oddzielone pliki warstwowe, wykonując kroki opisane w sekcji Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego [serwera.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**Usuwanie punktu końcowego serwera kończy się niepowodzeniem z powodu tego błędu: "MgmtServerJobExpired" (kod błędu: -2134347757 lub 0x80c87013)**  
Ten błąd występuje wtedy, gdy serwer jest w trybie offline lub nie ma łączności sieciowej. Jeśli serwer nie jest już dostępny, wyrejestruj serwer w portalu, co spowoduje usunięcie punktów końcowych serwera. Aby usunąć punkty końcowe serwera, wykonaj kroki opisane w te [tematze Wyrejestruj serwer za](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)pomocą Azure File Sync .

### <a name="server-endpoint-health"></a>Kondycja punktu końcowego serwera

<a id="server-endpoint-provisioningfailed"></a>**Nie można otworzyć strony właściwości punktu końcowego serwera lub zaktualizować zasad warstw w chmurze**  
Ten problem może wystąpić, jeśli operacja zarządzania w punkcie końcowym serwera zakończy się niepowodzeniem. Jeśli strona właściwości punktu końcowego serwera nie zostanie otwarta w oknie Azure Portal, zaktualizowanie punktu końcowego serwera przy użyciu poleceń programu PowerShell z serwera może rozwiązać ten problem. 

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
<a id="server-endpoint-noactivity"></a>**Stan kondycji punktu końcowego serwera to "Brak aktywności" lub "Oczekiwanie", a stan serwera w bloku zarejestrowanych serwerów to "Pojawia się w trybie offline"**  

Ten problem może wystąpić, jeśli proces monitora synchronizacji magazynu (AzureStorageSyncMonitor.exe) nie jest uruchomiony lub serwer nie może uzyskać dostępu do Azure File Sync usługi.

Na serwerze wyświetlanym jako "Pojawia się w trybie offline" w portalu sprawdź identyfikator zdarzenia 9301 w dzienniku zdarzeń telemetrii (w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w programie Podgląd zdarzeń), aby ustalić, dlaczego serwer nie może uzyskać dostępu do usługi Azure File Sync. 

- Jeśli **getNextJob zakończone ze stanem: 0** jest rejestrowany, serwer może komunikować się z Azure File Sync usługi. 
    - Otwórz Menedżera zadań na serwerze i upewnij się, że proces programu Storage Sync Monitor (AzureStorageSyncMonitor.exe) jest uruchomiony. Jeśli proces nie jest uruchomiony, najpierw spróbuj uruchomić ponownie serwer. Jeśli ponowne uruchomienie serwera nie rozwiąże problemu, uaktualnij agenta usługi Azure File Sync do najnowszej [wersji](file-sync-release-notes.md). 

- Jeśli **ukończono getNextJob ze stanem: -2134347756,** serwer nie może komunikować się z usługą Azure File Sync z powodu konfiguracji zamówienia zapory, serwera proxy lub zestawu szyfrowania TLS. 
    - Jeśli serwer znajduje się za zaporą, sprawdź, czy ruch wychodzący na port 443 jest dozwolony. Jeśli zapora ogranicza ruch do określonych domen, upewnij się, że domeny wymienione w dokumentacji [zapory](file-sync-firewall-and-proxy.md#firewall) są dostępne.
    - Jeśli serwer znajduje się za serwerem proxy, skonfiguruj ustawienia serwera proxy dla całej maszyny lub aplikacji, korzystając z procedury opisanej w dokumentacji serwera [proxy.](file-sync-firewall-and-proxy.md#proxy)
    - Użyj Test-StorageSyncNetworkConnectivity cmdlet , aby sprawdzić łączność sieciową z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [Testowanie łączności sieciowej z punktami końcowymi usługi](file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Jeśli kolejność szyfrowania TLS jest skonfigurowana na serwerze, możesz użyć zasad grupy lub polecenia cmdlet TLS, aby dodać zestawy szyfrowania:
        - Aby użyć zasad grupy, zobacz Konfigurowanie kolejności [szyfrowania TLS przy użyciu zasady grupy](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Aby użyć poleceń cmdlet TLS, zobacz Konfigurowanie kolejności szyfrowania TLS za pomocą poleceń [cmdlet TLS programu PowerShell.](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)
    
        Azure File Sync obecnie obsługuje następujące pakiety szyfrowania dla protokołu TLS 1.2:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Jeśli **getNextJob zakończone ze stanem: -2134347764** jest rejestrowany, serwer nie może komunikować się z usługą Azure File Sync z powodu wygasłego lub usuniętego certyfikatu.  
    - Uruchom następujące polecenie programu PowerShell na serwerze, aby zresetować certyfikat używany do uwierzytelniania:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Punkt końcowy serwera ma stan kondycji "Brak aktywności", a stan serwera w bloku zarejestrowanych serwerów to "Online"**  

Stan kondycji punktu końcowego serwera „Brak działania” oznacza, że punkt końcowy serwera nie zarejestrował działania synchronizacji w ciągu ostatnich dwóch godzin.

Aby sprawdzić bieżące działanie synchronizacji na serwerze, zobacz [Jak mogę monitorowania postępu bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Punkt końcowy serwera może nie rejestrować aktywności synchronizacji przez kilka godzin z powodu usterki lub niewystarczających zasobów systemowych. Sprawdź, czy zainstalowano najnowszą Azure File Sync [agenta.](file-sync-release-notes.md) Jeśli problem będzie się powtarzać, otwórz wniosek o pomoc techniczną.

> [!Note]  
> Jeśli stan serwera w bloku zarejestrowanych serwerów to "Pojawia się w trybie offline", wykonaj kroki udokumentowane w punkcie końcowym serwera ze stanem kondycji ["Brak aktywności" lub "Oczekiwanie", a](#server-endpoint-noactivity) stan serwera w bloku zarejestrowanych serwerów to "Pojawia się w trybie offline".

## <a name="sync"></a>Synchronizuj
<a id="afs-change-detection"></a>**Jeśli plik został utworzony bezpośrednio w moim udziałach plików platformy Azure za pośrednictwem SMB lub portalu, jak długo trwa synchronizowanie pliku z serwerami w grupie synchronizacji?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Kondycja punktu końcowego serwera jest w stanie oczekiwania przez kilka godzin**  
Ten problem jest oczekiwany, jeśli utworzysz punkt końcowy w chmurze i użyjemy udziału plików platformy Azure zawierającego dane. Zadanie wyliczania zmian, które skanuje w poszukiwaniu zmian w udziałach plików platformy Azure, musi zostać ukończone, zanim pliki będą synchronizowane między punktami końcowymi chmury i serwera. Czas ukończenia zadania zależy od rozmiaru przestrzeni nazw w udziałach plików platformy Azure. Kondycja punktu końcowego serwera powinna zostać zaktualizowana po zakończeniu zadania wyliczania zmian.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Jak mogę monitorowanie kondycji synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W każdej grupie synchronizacji można przejść do szczegółów poszczególnych punktów końcowych serwera, aby wyświetlić stan ostatnich ukończonych sesji synchronizacji. Zielona kolumna Kondycja i wartość 0 niesynchronizuj plików wskazują, że synchronizacja działa zgodnie z oczekiwaniami. Jeśli tak nie jest, zobacz poniżej listę typowych błędów synchronizacji i sposób obsługi plików, które nie są synchronizowane. 

![Zrzut ekranu przedstawiający Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server) (Serwer)
Przejdź do dzienników telemetrii serwera, które można znaleźć w Podgląd zdarzeń stronie `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Zdarzenie 9102 odpowiada zakończonej sesji synchronizacji; Aby uzyskać najnowszy stan synchronizacji, poszukaj najnowszego zdarzenia o identyfikatorze 9102. SyncDirection informuje o tym, czy ta sesja była przekazywaniem, czy pobieraniem. Jeśli dla ustawienia HResult jest 0, sesja synchronizacji powiodła się. Wartość HResult niezerowa oznacza, że wystąpił błąd podczas synchronizacji; Poniżej znajduje się lista typowych błędów. Jeśli wartość perItemErrorCount jest większa niż 0, oznacza to, że niektóre pliki lub foldery nie zostały prawidłowo zsynchronizowane. Możliwe jest, że wartość HResult wynosi 0, ale wartość PerItemErrorCount jest większa niż 0.

Poniżej znajduje się przykład pomyślnego przekazania. W celu zachowania zwięzłości poniżej wymieniono tylko niektóre wartości zawarte w każdym zdarzeniu 9102. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Z drugiej jednak wynika to z tego, że przekazywanie nie powiodło się:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Czasami sesje synchronizacji ogólnie się nie powiodą lub mają niezerową wartość PerItemErrorCount, ale nadal postępują, a niektóre pliki są synchronizowane pomyślnie. Można to zobaczyć w polach Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes), które informują o tym, jaka część sesji zakończyła się powodzeniem. Jeśli w wierszu widzisz wiele sesji synchronizacji, które się nie popełnią, ale mają rosnącą liczbę Zastosowanych*, należy dać czas synchronizacji, aby spróbować ponownie przed otwarciem biletu pomocy technicznej.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorować postęp bieżącej sesji synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W grupie synchronizacji przejdź do punktu końcowego serwera, o którym mowa, i zapoznaj się z sekcją Działanie synchronizacji, aby zobaczyć liczbę plików przekazanych lub pobranych w bieżącej sesji synchronizacji. Pamiętaj, że ten stan zostanie opóźniony o około 5 minut. Jeśli sesja synchronizacji jest wystarczająco mała, aby mogła zostać zakończona w tym okresie, może nie zostać zgłoszona w portalu. 

# <a name="server"></a>[Server](#tab/server) (Serwer)
Sprawdź najnowsze zdarzenie 9302 w dzienniku telemetrii na serwerze (w witrynie Podgląd zdarzeń przejdź do folderu Dzienniki aplikacji i usług\Microsoft\FileSync\Agent\Telemetry). To zdarzenie wskazuje stan bieżącej sesji synchronizacji. TotalItemCount określa liczbę plików do zsynchronizowania, AppliedItemCount liczbę plików, które zostały zsynchronizowane do tej pory, i PerItemErrorCount liczbę plików, których nie można zsynchronizować (zobacz poniżej, jak sobie z tym poradzić).

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
Dla każdego serwera w danej grupie synchronizacji upewnij się, że:
- Znaczniki czasu ostatniej próby synchronizacji dla przekazywania i pobierania są aktualne.
- Stan przekazywania i pobierania jest zielony.
- Pole Działanie synchronizacji zawiera niewiele plików lub nie ma ich do zsynchronizowania.
- Pole Pliki nie synchronizuje ma wartości 0 zarówno w przypadku przekazywania, jak i pobierania.

# <a name="server"></a>[Server](#tab/server) (Serwer)
Przyjrzyj się ukończonym sesjom synchronizacji, które są oznaczone zdarzeniami 9102 w dzienniku zdarzeń telemetrii dla każdego serwera (w Podgląd zdarzeń przejdź do . `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` 

1. Na dowolnym serwerze chcesz się upewnić, że najnowsze sesje przekazywania i pobierania zostały ukończone pomyślnie. W tym celu sprawdź, czy wartości HResult i PerItemErrorCount mają 0 zarówno dla przekazywania, jak i pobierania (pole SyncDirection wskazuje, czy danej sesji jest sesja przekazywania, czy pobierania). Pamiętaj, że jeśli nie widzisz ostatnio ukończonej sesji synchronizacji, prawdopodobnie trwa sesja synchronizacji, czego można oczekiwać, jeśli właśnie dodano lub zmodyfikowano dużą ilość danych.
2. Gdy serwer jest w pełni aktualny z chmurą i nie ma żadnych zmian do zsynchronizowania w dowolnym kierunku, zobaczysz puste sesje synchronizacji. Są one wskazywane przez zdarzenia przekazywania i pobierania, w których wszystkie pola Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount i SyncSizeBytes) mają wartość zero, co oznacza, że nie było niczego do zsynchronizowania. Należy pamiętać, że te puste sesje synchronizacji mogą nie występować na serwerach o wysokim poziomie zmian, ponieważ zawsze trzeba synchronizować coś nowego. Jeśli synchronizacja nie jest aktywnością, powinna występować co 30 minut. 
3. Jeśli wszystkie serwery są aktualne w chmurze, co oznacza, że ostatnie sesje przekazywania i pobierania są pustymi sesjami synchronizacji, możesz z rozsądnym pewnością powiedzieć, że cały system jest zsynchronizowany. 
    
Należy pamiętać, że jeśli zmiany zostały wprowadzone bezpośrednio w twoim udziału plików platformy Azure, program Azure File Sync nie wykryje tej zmiany, dopóki nie zostanie wykonane wyliczenie zmiany, co następuje co 24 godziny. Istnieje możliwość, że serwer będzie twierdzić, że jest aktualny z chmurą, gdy w rzeczywistości brakuje ostatnich zmian wprowadzonych bezpośrednio w udziałach plików platformy Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak mogę sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane?
Jeśli twoja wartość PerItemErrorCount na serwerze lub liczba plików niesynchronizowanych w portalu jest większa niż 0 dla dowolnej sesji synchronizacji, oznacza to, że niektóre elementy nie są synchronizowane. Pliki i foldery mogą mieć właściwości, które uniemożliwiają ich synchronizację. Te cechy mogą być trwałe i wymagają jawnej akcji w celu wznowienia synchronizacji, na przykład usunięcia nieobsługiwanych znaków z nazwy pliku lub folderu. Mogą być również przejściowe, co oznacza, że plik lub folder zostanie automatycznie wznowiony synchronizacji; Na przykład pliki z otwartymi dojściami automatycznie wznowią synchronizację po zamknięciu pliku. Gdy aparat Azure File Sync wykryje taki problem, zostanie wyprodukowany dziennik błędów, który można rejestrować w celu listy elementów, które obecnie nie są prawidłowo synchronizowane.

Aby wyświetlić te błędy, uruchom skrypt programu **FileSyncErrorsReport.ps1** PowerShell (znajdujący się w katalogu instalacyjnym agenta agenta programu Azure File Sync), aby zidentyfikować pliki, których nie można zsynchronizować z powodu otwartych dojść, nieobsługiwanych znaków lub innych problemów. Pole ItemPath informuje o lokalizacji pliku w odniesieniu do katalogu głównego synchronizacji. Zapoznaj się z listą typowych błędów synchronizacji poniżej, aby uzyskać informacje o krokach korygowania.

> [!Note]  
> Jeśli skrypt FileSyncErrorsReport.ps1 zwraca błąd "Nie znaleziono żadnych błędów plików" lub nie ma listy błędów 1 elementu dla grupy synchronizacji, przyczyną jest:
>
>- Przyczyna 1: Ostatnia ukończona sesja synchronizacji nie miała błędów 1 elementu. Portal powinien zostać wkrótce zaktualizowany w celu pokazania, że 0 plików nie jest synchronizowanych. 
>    - Sprawdź identyfikator [zdarzenia 9102](?tabs=server%252cazure-portal#broken-sync) w dzienniku zdarzeń telemetrii, aby potwierdzić, że metryka PerItemErrorCount ma 0. 
>
>- Przyczyna 2: Dziennik zdarzeń ItemResults na serwerze opakowany z powodu zbyt wielu błędów 1 elementu, a dziennik zdarzeń nie zawiera już błędów dla tej grupy synchronizacji.
>    - Aby uniknąć tego problemu, zwiększ rozmiar dziennika zdarzeń ItemResults. Dziennik zdarzeń ItemResults można znaleźć w obszarze "Dzienniki aplikacji i usług\Microsoft\FileSync\Agent" w Podgląd zdarzeń. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Rozwiązywanie problemów z błędami synchronizacji plików/katalogów
**Dziennik ItemResults — błędy synchronizacji 1 elementu**  

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Plik warstwowy na serwerze jest niedostępny. Ten problem występuje, gdy plik warstwowy nie został odwołany przed usunięciem punktu końcowego serwera. | Aby rozwiązać ten problem, zobacz Pliki warstwowe nie są dostępne na serwerze po [usunięciu punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać przez kilka dni, użyj skryptu programu FileSyncErrorsReport.ps1 PowerShell, aby ustalić, dlaczego folder zależny nie jest jeszcze zsynchronizowany. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać przez kilka dni, użyj skryptu programu FileSyncErrorsReport.ps1 PowerShell, aby ustalić, dlaczego folder zależny nie jest jeszcze zsynchronizowany. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie jest jeszcze zsynchronizowany i sesja synchronizacji nie powiodła się. Ten element zostanie zsynchronizowany po zsynchronizowaniu zależnych zmian. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać, zbadaj niepowodzenie sesji synchronizacji. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę pliku lub katalogu, o którym mowa. Aby [uzyskać więcej informacji,](?tabs=portal1%252cazure-portal#handling-unsupported-characters) zobacz Obsługa nieobsługiwanych znaków. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę pliku lub katalogu, o którym mowa. Aby [uzyskać więcej informacji,](?tabs=portal1%252cazure-portal#handling-unsupported-characters) zobacz Obsługa nieobsługiwanych znaków. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Nie można zsynchronizować pliku, ponieważ jest on w użyciu. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze, aby synchronizować pliki z otwartymi dojściami. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Plik został zmieniony, ale zmiana nie została jeszcze wykryta przez synchronizację. Synchronizacja zostanie odzyskana po wykryciu tej zmiany. | Żadna akcja nie jest wymagana. |
| 0x80070002 | -2147024894 | Error_file_not_found | Plik został usunięty, a synchronizacja nie wie o zmianie. | Żadna akcja nie jest wymagana. Synchronizacja spowoduje zatrzymanie rejestrowania tego błędu, gdy wykrywanie zmian wykryje, że plik został usunięty. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Nie można zsynchronizować usunięcia pliku lub katalogu, ponieważ element został już usunięty w miejscu docelowym, a synchronizacja nie wie o zmianie. | Żadna akcja nie jest wymagana. Synchronizacja spowoduje zatrzymanie rejestrowania tego błędu, gdy wykrywanie zmian zostanie uruchomiony w miejscu docelowym i synchronizacja wykryje, że element został usunięty. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Plik lub katalog został pominięty, ale zostanie zsynchronizowany podczas następnej sesji synchronizacji. Jeśli ten błąd jest zgłaszany podczas pobierania elementu, nazwa pliku lub katalogu jest bardziej niż prawdopodobna nieprawidłowa. | Jeśli ten błąd zostanie zgłoszony podczas przekazywania pliku, nie jest wymagana żadna akcja. Jeśli błąd jest zgłaszany podczas pobierania pliku, zmień nazwę pliku lub katalogu, których dotyczy problem. Aby [uzyskać więcej informacji,](?tabs=portal1%252cazure-portal#handling-unsupported-characters) zobacz Obsługa nieobsługiwanych znaków. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Nie można zsynchronizować tworzenia pliku lub katalogu, ponieważ element już istnieje w miejscu docelowym, a synchronizacja nie wie o zmianie. | Żadna akcja nie jest wymagana. Usługa Sync przestanie rejestrować ten błąd, gdy wykrywanie zmian zostanie uruchomione w miejscu docelowym, a usługa Sync będzie mieć informacje o istnieniu nowego elementu. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Nie można zsynchronizować pliku, ponieważ osiągnięto limit udziału plików platformy Azure. | Aby rozwiązać ten problem, zobacz [sekcję Osiągnięto limit](?tabs=portal1%252cazure-portal#-2134351810) magazynu udziałów plików platformy Azure w przewodniku rozwiązywania problemów. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Plik znajduje się w folderze replikacji tylko do odczytu systemu plików DFS-R. | Plik znajduje się w folderze replikacji tylko do odczytu systemu plików DFS-R. Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Aby uzyskać więcej informacji, zobacz [przewodnik planowania](file-sync-planning.md#distributed-file-system-dfs). |
| 0x80070005 | -2147024891 | Error_access_denied | Plik ma stan oczekiwania na usunięcie. | Żadna akcja nie jest wymagana. Plik zostanie usunięty po zamknięciu wszystkich otwartych dojść do plików. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Nie można zsynchronizować pliku, ponieważ ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Dodaj adres IP serwera lub sieć wirtualną, [](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) korzystając z procedury opisanej w sekcji Konfigurowanie ustawień zapory i sieci wirtualnej w przewodniku wdrażania. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Nie można zsynchronizować pliku, ponieważ rozmiar deskryptora zabezpieczeń przekracza limit 64 KiB. | Aby rozwiązać ten problem, usuń wpisy kontroli dostępu (ACE) w pliku w celu zmniejszenia rozmiaru deskryptora zabezpieczeń. |
| 0x8000ffff | -2147418113 | E_unexpected | Nie można zsynchronizować pliku z powodu nieoczekiwanego błędu. | Jeśli błąd będzie się powtarzać przez kilka dni, otwórz sprawę pomocy technicznej. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Nie można zsynchronizować pliku, ponieważ jest on w użyciu. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Plik został zmieniony podczas synchronizacji, więc należy go ponownie zsynchronizować. | Żadna akcja nie jest wymagana. |
| 0x80070017 | -2147024873 | ERROR_CRC | Nie można zsynchronizować pliku z powodu błędu CRC. Ten błąd może wystąpić, jeśli plik warstwowy nie został odwołany przed usunięciem punktu końcowego serwera lub jeśli plik jest uszkodzony. | Aby rozwiązać ten problem, zobacz [Pliki](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera w celu usunięcia oddzielonych plików warstwowych. Jeśli błąd będzie nadal występować po usunięciu oddzielonych plików warstwowych, uruchom [chkdsk](/windows-server/administration/windows-commands/chkdsk) na woluminie. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Nie można zsynchronizować pliku, ponieważ osiągnięto maksymalną liczbę plików konfliktów. Azure File Sync obsługuje 100 plików konfliktów na plik. Aby dowiedzieć się więcej na temat konfliktów plików, zobacz Azure File Sync [— często zadawane pytania.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-conflict-resolution) | Aby rozwiązać ten problem, zmniejsz liczbę plików powodujących konflikt. Plik zostanie zsynchronizowany, gdy liczba plików powodujących konflikt będzie mniejsza niż 100. |

#### <a name="handling-unsupported-characters"></a>Obsługa nieobsługiwanych znaków
Jeśli skrypt **programuFileSyncErrorsReport.ps1** PowerShell pokazuje błędy synchronizacji poszczególnych elementów spowodowane nieobsługiwanymi znakami (kod błędu 0x8007007b lub 0x80c80255), usuń lub zmień nazwy znaków w razie błędu na odpowiednie nazwy plików. Program PowerShell prawdopodobnie będzie drukować te znaki jako znaki zapytania lub puste prostokąty, ponieważ większość z nich nie ma standardowego kodowania wizualnego. 
> [!Note]  
> Narzędzie [do oceny](file-sync-planning.md#evaluation-cmdlet) może służyć do identyfikowania znaków, które nie są obsługiwane. Jeśli zestaw danych zawiera kilka plików z nieprawidłowymi znakami, użyj skryptu [ScanUnsupportedChars,](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) aby zmienić nazwy plików zawierających nieobsługiwane znaki.

W poniższej tabeli przedstawiono wszystkie znaki Unicode, które Azure File Sync jeszcze nie obsługują.

| Zestaw znaków | Liczba znaków |
|---------------|-----------------|
| 0x00000000 — 0x0000001F (znaki kontrolne) | 32 |
| 0x0000FDD0 — 0x0000FDDD (formularze prezentacji arabskich-a) | 14 |
| <ul><li>0x00000022 (cudzysłów)</li><li>0x0000002A (gwiazdka)</li><li>0x0000002F (ukośnik)</li><li>0x0000003A (dwukropek)</li><li>0x0000003C (mniejsze niż)</li><li>0x0000003E (większe niż)</li><li>0x0000003F (znak zapytania)</li><li>0x0000005C (ukośnik odwrotny)</li><li>0x0000007C (potok lub pasek)</li></ul> | 9 |
| <ul><li>0x0004FFFE — 0x0004FFFF = 2 (nieznakowo)</li><li>0x0008FFFE — 0x0008FFFF = 2 (nieznakowo)</li><li>0x000CFFFE - 0x000CFFFF = 2 (noncharacter)</li><li>0x0010FFFE — 0x0010FFFF = 2 (nieznakowo)</li></ul> | 8 |
| <ul><li>0x0000009D (polecenie systemu operacyjnego osc)</li><li>0x00000090 (ciąg sterowania urządzenia dcs)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (ustawienie wstępne wysokiego oktetu)</li><li>0x0000007F (del delete)</li><li>0x0000008D (odwrotne źródło danych ri)</li></ul> | 6 |
| 0x0000FFF0, 0x0000FFFD, 0x0000FFFE, 0x0000FFFF (specjalne) | 4 |
| Pliki lub katalogi z okresem | 1 |

### <a name="common-sync-errors"></a>Typowe błędy synchronizacji
<a id="-2147023673"></a>**Sesja synchronizacji została anulowana.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (dziesiętny)** | -2147023673 | 
| **Ciąg błędu** | ERROR_CANCELLED |
| **Wymagane korygowanie** | Nie |

Sesje synchronizacji mogą się nie powieść z różnych powodów, w tym ponownego uruchamiania lub aktualizowania serwera, migawek usługi VSS itp. Chociaż ten błąd wygląda na to, że wymaga kolejnej czynności, można bezpiecznie zignorować ten błąd, chyba że będzie on nadal występował przez kilka godzin.

<a id="-2147012889"></a>**Nie można nawiązyć połączenia z usługą.**    

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (dziesiętny)** | -2147012889 | 
| **Ciąg błędu** | WININET_E_NAME_NOT_RESOLVED |
| **Wymagane korygowanie** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Żądanie użytkownika zostało ograniczone przez usługę.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (dziesiętny)** | -2134376372 |
| **Ciąg błędu** | ECS_E_USER_REQUEST_THROTTLED |
| **Wymagane korygowanie** | Nie |

Nie jest wymagana żadna akcja; Serwer spróbuje ponownie. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364043"></a>**Synchronizacja jest blokowana do momentu zakończenia wykrywania zmian po przywróceniu**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dziesiętny)** | -2134364043 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Wymagane korygowanie** | Nie |

Nie jest wymagana żadna akcja. Po przywróceniu pliku lub udziału plików (punktu końcowego w chmurze) przy użyciu usługi Azure Backup synchronizacja jest blokowana do momentu ukończenia wykrywania zmian w udziałach plików platformy Azure. Wykrywanie zmian jest uruchamiane natychmiast po zakończeniu przywracania, a czas trwania zależy od liczby plików w udziale plików.

<a id="-2147216747"></a>**Synchronizacja nie powiodła się, ponieważ baza danych synchronizacji została zwolniona.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (dziesiętny)** | -2147216747 |
| **Ciąg błędu** | SYNC_E_METADATA_INVALID_OPERATION |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje zazwyczaj wtedy, gdy aplikacja kopii zapasowej tworzy migawkę usługi VSS, a baza danych synchronizacji jest zwalniana. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364065"></a>**Usługa synchronizacji nie może uzyskać dostępu do udziału plików platformy Azure określonego w punkcie końcowym chmury.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dziesiętny)** | -2134364065 |
| **Ciąg błędu** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ agent usługi Azure File Sync nie może uzyskać dostępu do udziału plików platformy Azure, co może być spowodowane tym, że udział plików platformy Azure lub konto magazynu, na którym udział się znajduje, już nie istnieje. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure istnieje.](#troubleshoot-azure-file-share)
3. [Upewnij Azure File Sync, że masz dostęp do konta magazynu.](#troubleshoot-rbac)
4. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Synchronizacja nie powiodła się, ponieważ żądanie nie ma autoryzacji do wykonania tej operacji.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (dziesiętny)** | -2134351804 |
| **Ciąg błędu** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ Azure File Sync nie ma autoryzacji dostępu do udziału plików platformy Azure. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure istnieje.](#troubleshoot-azure-file-share)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Upewnij Azure File Sync, że Azure File Sync dostęp do konta magazynu.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Nie można rozpoznać użytej nazwy konta magazynu.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (dziesiętny)** | -2134364064 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Wymagane korygowanie** | Tak |

1. Sprawdź, czy możesz rozpoznać nazwę DNS magazynu z serwera.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Wystąpił nieznany błąd podczas uzyskiwania dostępu do konta magazynu.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (dziesiętny)** | -2134364022 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Wymagane korygowanie** | Tak |

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synchronizacja nie powiodła się z powodu zablokowanego konta magazynu.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (dziesiętny)** | -2134364014 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ konto magazynu ma blokadę zasobu tylko [do odczytu.](../../azure-resource-manager/management/lock-resources.md) Aby rozwiązać ten problem, usuń blokadę zasobów tylko do odczytu na koncie magazynu. 

<a id="-1906441138"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (dziesiętny)** | -1906441138 |
| **Ciąg błędu** | JET_errWriteConflict |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy występuje problem z wewnętrzną bazą danych używaną przez Azure File Sync. W przypadku wystąpienia tego problemu utwórz wniosek o pomoc techniczną, a my skontaktujemy się z Tobą w celu rozwiązania tego problemu.

<a id="-2134364053"></a>**Wersja Azure File Sync agenta zainstalowanego na serwerze nie jest obsługiwana.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (dziesiętny)** | -2134364053 |
| **Ciąg błędu** | ECS_E_AGENT_VERSION_BLOCKED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy wersja agenta usługi Azure File Sync zainstalowana na serwerze nie jest obsługiwana. Aby rozwiązać ten problem, [uaktualnij]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) program do [obsługiwanej wersji agenta.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**Osiągnięto limit magazynu udziałów plików platformy Azure.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (dziesiętny)** | -2134351810 |
| **Ciąg błędu** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje po przekroczeniu limitu magazynowania udziałów plików platformy Azure, co może wystąpić w przypadku zastosowania limitu przydziału dla udziału plików platformy Azure lub przekroczenia limitów użycia dla udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [bieżące limity dla udziału plików platformy Azure.](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

1. Przejdź do grupy synchronizacji w ramach usługi synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartych okienkach.
4. Wybierz połączone konto magazynu. Jeśli to łącze nie powiedzie się, konto magazynu, do których się odwołujesz, zostało usunięte.

    ![Zrzut ekranu przedstawiający okienko szczegółów punktu końcowego w chmurze z linkiem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Wybierz **pozycję** Pliki, aby wyświetlić listę udziałów plików.
6. Kliknij trzy kropki na końcu wiersza dla udziału plików platformy Azure, do których odwołuje się punkt końcowy w chmurze.
7. Sprawdź, czy pozycja **Użycie** znajduje się poniżej pozycji **Limit przydziału**. Pamiętaj, że o ile nie określono alternatywnego limitu przydziału, limit przydziału będzie odpowiadać maksymalnego [rozmiaru udziału plików platformy Azure.](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

    ![Zrzut ekranu przedstawiający właściwości udziału plików platformy Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Jeśli udział jest pełny i nie ustawiono limitu przydziału, jednym z możliwych rozwiązań tego problemu jest przekształcenie każdego podfolderu bieżącego punktu końcowego serwera we własny punkt końcowy serwera w oddzielnych grupach synchronizacji. W ten sposób każdy podfolder będzie synchronizowany z poszczególnymi udziałami plików platformy Azure.

<a id="-2134351824"></a>**Nie można odnaleźć udziału plików platformy Azure.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (dziesiętny)** | -2134351824 |
| **Ciąg błędu** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny. Aby rozwiązać problem:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że udział plików platformy Azure istnieje.](#troubleshoot-azure-file-share)

Jeśli udział plików platformy Azure został usunięty, musisz utworzyć nowy udział plików, a następnie ponownie utworzyć grupę synchronizacji. 

<a id="-2134364042"></a>**Synchronizacja została wstrzymana, gdy ta subskrypcja platformy Azure jest wstrzymana.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (dziesiętny)** | -2134364042 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy subskrypcja platformy Azure jest zawieszona. Synchronizacja zostanie ponownie włączona po przywróceniu subskrypcji platformy Azure. Aby [uzyskać więcej informacji,](../../cost-management-billing/manage/subscription-disabled.md) zobacz Dlaczego moja subskrypcja platformy Azure jest wyłączona i jak mogę ją ponownie uaktywnić?.

<a id="-2134375618"></a>**Konto magazynu ma skonfigurowaną zaporę lub sieci wirtualne.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (dziesiętny)** | -2134375618 |
| **Ciąg błędu** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny z powodu zapory konta magazynu lub gdy konto magazynu należy do sieci wirtualnej. Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory i sieci wirtualnej.](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 

<a id="-2134375911"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (dziesiętny)** | -2134375911 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Wymagane korygowanie** | Nie |

Ten błąd zwykle rozwiązuje się sam i może wystąpić w następujących przypadkach:

* Duża liczba zmian plików na serwerach w grupie synchronizacji.
* Duża liczba błędów w poszczególnych plikach i katalogach.

Jeśli ten błąd będzie się powtarzać dłużej niż kilka godzin, utwórz wniosek o pomoc techniczną, a my skontaktujemy się z Tobą w celu rozwiązania tego problemu.

<a id="-2146762487"></a>**Serwer nie może nawiązać bezpiecznego połączenia. Usługa w chmurze odebrała nieoczekiwany certyfikat.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (dziesiętny)** | -2146762487 |
| **Ciąg błędu** | CERT_E_UNTRUSTEDROOT |
| **Wymagane korygowanie** | Tak |

Ten błąd może wystąpić, jeśli organizacja korzysta z serwera proxy przerywania połączenia TLS lub jeśli złośliwa jednostka przechwytuje ruch między serwerem a usługą Azure File Sync sieci. Jeśli masz pewność, że jest to oczekiwane (ponieważ organizacja używa serwera proxy przerywania TLS), możesz pominąć weryfikację certyfikatu za pomocą zastąpienia rejestru.

1. Utwórz wartość rejestru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Uruchom ponownie usługę synchronizacji na zarejestrowanym serwerze.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ustawiając tę wartość rejestru, agent Azure File Sync zaakceptuje dowolny lokalnie zaufany certyfikat protokołu TLS/SSL podczas transferu danych między serwerem a usługą w chmurze.

<a id="-2147012894"></a>**Nie można nawiązyć połączenia z usługą.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (dziesiętny)** | -2147012894 |
| **Ciąg błędu** | WININET_E_TIMEOUT |
| **Wymagane korygowanie** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizacja nie powiodła się z powodu problemu z uwierzytelnianiem.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (dziesiętny)** | -2134375680 |
| **Ciąg błędu** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Wymagane korygowanie** | Tak |

Ten błąd zazwyczaj występuje, ponieważ czas serwera jest niepoprawny. Jeśli serwer jest uruchomiony na maszynie wirtualnej, sprawdź, czy czas na hoście jest poprawny.

<a id="-2134364040"></a>**Synchronizacja nie powiodła się z powodu wygaśnięcia certyfikatu.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (dziesiętny)** | -2134364040 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ wygasł certyfikat używany do uwierzytelniania.

Aby sprawdzić, czy certyfikat wygasł, wykonaj następujące czynności:  
1. Otwórz przystawkę MMC Certyfikaty, wybierz pozycję Konto komputera i przejdź do opcji Certyfikaty (komputer lokalny)\Osobiste\Certyfikaty.
2. Sprawdź, czy certyfikat uwierzytelniania klienta wygasł.

Jeśli certyfikat uwierzytelniania klienta wygasł, wykonaj następujące czynności, aby rozwiązać ten problem:

1. Sprawdź Azure File Sync czy zainstalowano agenta w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizacja nie powiodła się z powodu nie znaleziono certyfikatu uwierzytelniania.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (dziesiętny)** | -2134375896 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ nie znaleziono certyfikatu używanego do uwierzytelniania.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Sprawdź Azure File Sync czy zainstalowano agenta w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizacja nie powiodła się z powodu nie znaleziono tożsamości uwierzytelniania.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (dziesiętny)** | -2134364039 |
| **Ciąg błędu** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usuwanie punktu końcowego serwera nie powiodło się, a punkt końcowy jest teraz w stanie częściowo usuniętym. Aby rozwiązać ten problem, spróbuj ponownie usunąć punkt końcowy serwera.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Na woluminie, na którym znajduje się punkt końcowy serwera, jest mała ilość miejsca na dysku.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dziesiętny)** | -1906441711 |
| **Ciąg błędu** | JET_errLogDiskFull |
| **Wymagane korygowanie** | Tak |

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (dziesiętny)** | -2134375654 |
| **Ciąg błędu** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ wolumin jest pełny. Ten błąd często występuje, gdy pliki spoza punktu końcowego serwera zużywają przestrzeń na woluminie. Wolne miejsce na woluminie przez dodanie dodatkowych punktów końcowych serwera, przeniesienie plików do innego woluminu lub zwiększenie rozmiaru woluminu, na którym znajduje się punkt końcowy serwera.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Usługa nie jest jeszcze gotowa do synchronizacji z tym punktem końcowym serwera.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (dziesiętny)** | -2134364145 |
| **Ciąg błędu** | ECS_E_REPLICA_NOT_READY |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ punkt końcowy w chmurze został utworzony z zawartością już istniejącą w udziałach plików platformy Azure. Azure File Sync przeskanować udział plików platformy Azure pod poszukiwaniu całej zawartości przed umożliwieniem punktowi końcowemu serwera kontynuowania synchronizacji początkowej.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizacja nie powiodła się z powodu problemów z wieloma poszczególnymi plikami.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dziesiętny)** | -2134375877 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (dziesiętny)** | -2134375908 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (dziesiętny)** | -2134375853 |
| **Ciąg błędu** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Wymagane korygowanie** | Tak |

Sesje synchronizacji nie powiodą się z jednym z tych błędów, gdy istnieje wiele plików, których nie można zsynchronizować z błędami per-item. Wykonaj kroki opisane w sekcji [Jak mogę,](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) czy istnieją określone pliki lub foldery, które nie są synchronizowane? w celu usunięcia błędów poszczególnych elementów. W przypadku błędów ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED otwórz przypadek pomocy technicznej.

> [!NOTE]
> Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze, aby synchronizować pliki z otwartymi dojściami.

<a id="-2134376423"></a>**Synchronizacja nie powiodła się z powodu problemu ze ścieżką punktu końcowego serwera.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (dziesiętny)** | -2134376423 |
| **Ciąg błędu** | ECS_E_SYNC_INVALID_PATH |
| **Wymagane korygowanie** | Tak |

Upewnij się, że ścieżka istnieje, znajduje się na lokalnym woluminie NTFS i nie jest punktem ponownej oceny ani istniejącym punktem końcowym serwera.

<a id="-2134375817"></a>**Synchronizacja nie powiodła się, ponieważ wersja sterownika filtru nie jest zgodna z wersją agenta**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (dziesiętny)** | -2134375817 |
| **Ciąg błędu** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ załadowana wersja sterownika filtru obsługi warstw w chmurze (StorageSync.sys) jest niezgodna z usługą agenta synchronizacji usługi Storage (FileSyncSvc). Jeśli agent usługi Azure File Sync został uaktualniony, ponownie uruchom serwer, aby zakończyć instalację. Jeśli błąd będzie nadal występował, odinstaluj agenta, uruchom ponownie serwer i ponownie zainstaluj agenta usługi Azure File Sync.

<a id="-2134376373"></a>**Usługa jest obecnie niedostępna.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (dziesiętny)** | -2134376373 |
| **Ciąg błędu** | ECS_E_SERVICE_UNAVAILABLE |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ usługa Azure File Sync jest niedostępna. Ten błąd zostanie automatycznie rozwiązany, gdy usługa Azure File Sync stanie się ponownie dostępna.

<a id="-2146233088"></a>**Synchronizacja nie powiodła się z powodu wyjątku.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (dziesiętny)** | -2146233088 |
| **Ciąg błędu** | Cor_e_exception |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ synchronizacja nie powiodła się z powodu wyjątku. Jeśli błąd będzie się powtarzać przez kilka godzin, utwórz wniosek o pomoc techniczną.

<a id="-2134364045"></a>**Synchronizacja nie powiodła się, ponieważ konto magazynu zostało przejechowane w tryb fail over do innego regionu.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (dziesiętny)** | -2134364045 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ konto magazynu zostało przełączone w tryb failover do innego regionu. Usługa Azure File Sync nie obsługuje funkcji przełączania konta magazynu w tryb failover. Kont magazynu zawierających udziały plików platformy Azure używane jako punkty końcowe w chmurze w usłudze Azure File Sync nie należy przełączać w tryb failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych. Aby rozwiązać ten problem, przenieś konto magazynu do regionu podstawowego.

<a id="-2134375922"></a>**Synchronizacja nie powiodła się z powodu przejściowego problemu z bazą danych synchronizacji.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dziesiętny)** | -2134375922 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje z powodu wewnętrznego problemu z bazą danych synchronizacji. Ten błąd zostanie automatycznie rozwiązany przy ponownych próbach synchronizacji. Jeśli ten błąd będzie się wydłużać przez dłuższy czas, utwórz wniosek o pomoc techniczną, a my skontaktujemy się z Tobą w celu rozwiązania tego problemu.

<a id="-2134364024"></a>**Synchronizacja nie powiodła się z powodu zmiany Azure Active Directory dzierżawy**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (dziesiętny)** | -2134364024 | 
| **Ciąg błędu** | ECS_E_INVALID_AAD_TENANT |
| **Wymagane korygowanie** | Tak |

Upewnij się, że masz najnowszą Azure File Sync agenta. Od wersji 10 agent Azure File Sync przenoszenie subskrypcji do innej dzierżawy Azure Active Directory dzierżawy.
 
Gdy masz najnowszą wersję agenta, musisz udzielić aplikacji Microsoft.StorageSync dostępu do konta magazynu (zobacz Ensure Azure File Sync has access to the storage account ( Upewnij się, że Azure File Sync ma [dostęp do konta magazynu).](#troubleshoot-rbac)

<a id="-2134364010"></a>**Synchronizacja nie powiodła się z powodu nieskonfigurowanych wyjątków zapory i sieci wirtualnej**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (dziesiętny)** | -2134364010 | 
| **Ciąg błędu** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy na koncie magazynu są włączone ustawienia zapory i sieci wirtualnej, a wyjątek "Zezwalaj zaufanej usługi firmy Microsoft na dostęp do tego konta magazynu" nie jest sprawdzany. Aby rozwiązać ten problem, wykonaj czynności opisane w sekcji [Konfigurowanie ustawień zapory i sieci wirtualnej](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w podręczniku wdrażania.

<a id="-2147024891"></a>**Synchronizacja nie powiodła się, ponieważ uprawnienia w folderze informacji o woluminie systemowym są nieprawidłowe.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (dziesiętny)** | -2147024891 |
| **Ciąg błędu** | Error_access_denied |
| **Wymagane korygowanie** | Tak |

Ten błąd może wystąpić, jeśli konto NT AUTHORITY\SYSTEM nie ma uprawnień do folderu System Volume Information na woluminie, na którym znajduje się punkt końcowy serwera. Jeśli nie można zsynchronizować poszczególnych plików z ERROR_ACCESS_DENIED, wykonaj kroki opisane w sekcji Rozwiązywanie problemów z błędami synchronizacji poszczególnych [plików/katalogów.](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Pobierz narzędzie [Psexec](/sysinternals/downloads/psexec).
2. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia przy użyciu konta systemowego: **PsExec.exe -i -s -d cmd** 
3. Z poziomu wiersza polecenia uruchomionego w ramach konta systemowego uruchom następujące polecenie, aby potwierdzić, że konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information: **cacls "litera dysku:\system volume information" /T /C**
4. Jeśli konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information, uruchom następujące polecenie: **cacls "litera dysku:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Jeśli krok 4 zakończy się niepowodzeniem z powodu odmowy dostępu, uruchom następujące polecenie, aby przejąć na własność folder System Volume Information, a następnie powtórz krok 4: **takeown /A /R /F "litera dysku:\System Volume Information"**

<a id="-2134375810"></a>**Synchronizacja nie powiodła się, ponieważ udział plików platformy Azure został usunięty i ponownie utworzony.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (dziesiętny)** | -2134375810 |
| **Ciąg błędu** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje usuwania i ponownego tworzenia udziału plików platformy Azure w tej samej grupie synchronizacji. 

Aby rozwiązać ten problem, usuń i utwórz ponownie grupę synchronizacji, wykonując następujące czynności:

1. Usuń wszystkie punkty końcowe serwera w grupie synchronizacji.
2. Usuń punkt końcowy w chmurze. 
3. Usuń grupę synchronizacji.
4. Jeśli w punkcie końcowym serwera włączono obsługę warstw w chmurze, usuń oddzielone pliki warstwowe na serwerze, wykonując kroki opisane w sekcji Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego [serwera.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)
5. Utwórz ponownie grupę synchronizacji.

<a id="-2145844941"></a>**Synchronizacja nie powiodła się, ponieważ żądanie HTTP zostało przekierowane**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (dziesiętny)** | -2145844941 |
| **Ciąg błędu** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje przekierowania HTTP (kod stanu 3xx). Aby rozwiązać ten problem, należy wyłączyć przekierowywanie HTTP na serwerze proxy lub urządzeniu sieciowym.

<a id="-2134364027"></a>**Podczas transferu danych w trybie offline wystąpił limit czasu, ale nadal jest w toku.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (dziesiętny)** | -2134364027 |
| **Ciąg błędu** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, gdy operacja pozyskiwania danych przekracza limit czasu. Ten błąd można zignorować, jeśli synchronizacja postępuje (wartość AppliedItemCount jest większa niż 0). Zobacz [Jak mogę monitorować postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Synchronizacja nie powiodła się, ponieważ nie można odnaleźć ścieżki punktu końcowego serwera na serwerze.**  

| Błąd | Kod |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (dziesiętny)** | -2134375814 |
| **Ciąg błędu** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, jeśli nazwa katalogu używanego jako ścieżka punktu końcowego serwera została zmieniona lub usunięta. Jeśli nazwa katalogu została zmieniona, zmień nazwę katalogu z powrotem na oryginalną nazwę i uruchom ponownie usługę agenta synchronizacji magazynu (FileSyncSvc).

Jeśli katalog został usunięty, wykonaj następujące kroki, aby usunąć istniejący punkt końcowy serwera i utworzyć nowy punkt końcowy serwera przy użyciu nowej ścieżki:

1. Usuń punkt końcowy serwera w grupie synchronizacji, korzystając z procedury opisanej w te [tematze Usuwanie punktu końcowego serwera](file-sync-server-endpoint.md#remove-a-server-endpoint).
2. Utwórz nowy punkt końcowy serwera w grupie synchronizacji, korzystając z procedury opisanej w tece [Dodawanie punktu końcowego serwera](file-sync-server-endpoint.md#add-a-server-endpoint).

### <a name="common-troubleshooting-steps"></a>Typowe kroki rozwiązywania problemów
<a id="troubleshoot-storage-account"></a>**Sprawdź, czy konto magazynu istnieje.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy synchronizacji w usłudze synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartych okienkach.
4. Wybierz połączone konto magazynu. Jeśli to łącze nie powiedzie się, konto magazynu, do których się odwołujesz, zostało usunięte.
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

<a id="troubleshoot-azure-file-share"></a>**Upewnij się, że udział plików platformy Azure istnieje.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij **pozycję** Przegląd w spisie treści po lewej stronie, aby wrócić do strony głównej konta magazynu.
2. Wybierz **pozycję** Pliki, aby wyświetlić listę udziałów plików.
3. Sprawdź, czy udział plików, do których odwołuje się punkt końcowy w chmurze, znajduje się na liście udziałów plików (należy to zauważyć w kroku 1 powyżej).

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

<a id="troubleshoot-rbac"></a>**Upewnij Azure File Sync, że Azure File Sync dostęp do konta magazynu.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij **pozycję Kontrola dostępu (IAM)** w spisie treści po lewej stronie.
1. Kliknij **kartę Przypisania ról,** aby wyświetlićlistę użytkowników i aplikacji (jednostki usługi), którzy mają dostęp do konta magazynu.
1. Sprawdź, czy na liście jest wyświetlana nazwa File Sync **Microsoft.StorageSync** lub **Hybrid File Sync Service** (stara nazwa aplikacji) z rolą Czytelnik i Dostęp **do** danych. 

    ![Zrzut ekranu przedstawiający jednostkę File Sync Usługi hybrydowej na karcie kontroli dostępu konta magazynu](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Jeśli na liście nie ma File Sync **Microsoft.StorageSync** lub **Hybrid File Sync Service,** wykonaj następujące kroki:

    - Kliknij pozycję **Dodaj**.
    - W polu **Rola** wybierz pozycję **Czytelnik i dostęp do danych.**
    - W polu **Wybierz** wpisz **Microsoft.StorageSync,** wybierz rolę i kliknij przycisk **Zapisz.**

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

## <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Istnieją dwie ścieżki błędów w warstwie w chmurze:

- Pliki mogą się nie powieść w warstwie, co oznacza, Azure File Sync nieudanych prób przesłonić warstwę pliku w celu Azure Files.
- Nie można odwołać plików, co oznacza, że filtr systemu plików usługi Azure File Sync (StorageSync.sys) nie może pobrać danych, gdy użytkownik próbuje uzyskać dostęp do pliku, który został warstwowy.

Istnieją dwie główne klasy błędów, które mogą wystąpić za pośrednictwem jednej ze ścieżek błędów:

- Błędy magazynu w chmurze
    - *Przejściowe problemy z dostępnością usługi magazynu*. Aby uzyskać więcej informacji, zobacz [Umowa dotycząca poziomu usług (SLA) for Azure Storage (Umowa SLA) dla usługi Azure Storage.](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)
    - *Niedostępny udział plików platformy Azure.* Ten błąd zwykle występuje, gdy usuniesz udział plików platformy Azure, gdy nadal jest on punktem końcowym w chmurze w grupie synchronizacji.
    - *Niedostępne konto magazynu.* Ten błąd występuje zwykle w przypadku usunięcia konta magazynu, gdy nadal ma on udział plików platformy Azure, który jest punktem końcowym chmury w grupie synchronizacji. 
- Błędy serwera 
  - *Azure File Sync system plików (StorageSync.sys) nie jest ładowany.* Aby odpowiadać na żądania warstw/odwoływania, Azure File Sync musi zostać załadowany filtr systemu plików. Filtr, który nie jest ładowany, może nastąpić z kilku powodów, ale najczęstszym powodem jest to, że administrator zwolnił go ręcznie. Filtr Azure File Sync systemu plików musi być ładowany przez cały czas, aby Azure File Sync działać prawidłowo.
  - *Brakujący, uszkodzony lub w inny* sposób uszkodzony punkt ponownej ponownej dystrybucji . Punkt ponownej ponownej dystrybucji jest specjalną strukturą danych w pliku, który składa się z dwóch części:
    1. Tag ponownejanalizy, który wskazuje systemowi operacyjneowi, że filtr systemu plików programu Azure File Sync (StorageSync.sys) może wymagać pewnego działania na we/wy do pliku. 
    2. Ponownie przeanalizuj dane, co wskazuje systemowi plików filtrowanie URI pliku w skojarzonym punkcie końcowym w chmurze (udział plików platformy Azure). 
        
       Najczęstszym sposobem uszkodzenia punktu ponownej próby jest próba zmodyfikowania tagu lub jego danych przez administratora. 
  - *Problemy z łącznością sieciową.* Aby można było użyć warstwy lub odwołać pliku, serwer musi mieć łączność z Internetem.

W poniższych sekcjach opisano sposób rozwiązywania problemów z warstwami w chmurze i określania, czy problem dotyczy magazynu w chmurze, czy problemu z serwerem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Jak monitorować aktywność warstw na serwerze  
Aby monitorować działanie obsługi warstw na serwerze, użyj zdarzeń o identyfikatorach 9003, 9016 i 9029 w dzienniku zdarzeń telemetrii (w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w programie Podgląd zdarzeń).

- Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład Łączna liczba błędów, Kod błędu itp. Należy pamiętać, że jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9016 zawiera wyniki dla woluminu. Na przykład procent wolnego miejsca to, liczba plików w sesji, liczba plików, których nie udało się odimówić itp.
- Zdarzenie o identyfikatorze 9029 zawiera informacje o sesji w przypadku punktu końcowego serwera. Na przykład Liczba plików, które podjęto próbę w sesji, Liczba plików z warstwami w sesji, Liczba plików już warstwowych itp.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Jak monitorować aktywność odwołań na serwerze
Aby monitorować aktywność odwoływania na serwerze, użyj zdarzeń o identyfikatorach 9005, 9006, 9009 i 9059 w dzienniku zdarzeń telemetrii (w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w programie Podgląd zdarzeń).

- Identyfikator zdarzenia 9005 zapewnia niezawodność odwoływania dla punktu końcowego serwera. Na przykład Łączna liczba unikatowych plików, Łączna liczba unikatowych plików z nieudanym dostępem itp.
- Identyfikator zdarzenia 9006 zapewnia dystrybucję błędów odwołania dla punktu końcowego serwera. Na przykład Łączna liczba żądań nieudanych, Kod błędu itp. Należy pamiętać, że jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9009 zawiera informacje o sesji odwołania dla punktu końcowego serwera. Na przykład DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed itp.
- Zdarzenie o identyfikatorze 9059 zapewnia dystrybucję odwołań aplikacji dla punktu końcowego serwera. Na przykład ShareId, Nazwa aplikacji i TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Jak rozwiązywać problemy z plikami, których warstwa nie powiodła się
Jeśli nie można przesłonić warstwy Azure Files:

1. W Podgląd zdarzeń przejrzyj dzienniki zdarzeń telemetrycznych, operacyjnych i diagnostycznych znajdujące się w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent. 
   1. Sprawdź, czy pliki istnieją w udziałach plików platformy Azure.

      > [!NOTE]
      > Plik musi zostać zsynchronizowany z udziałem plików platformy Azure, zanim będzie można go podzielić na warstwy.

   2. Sprawdź, czy serwer ma łączność z Internetem. 
   3. Sprawdź, Azure File Sync sterowników filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
       - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy StorageSync.sys i StorageSyncGuard.sys filtrów systemu plików są wyświetlane.

> [!NOTE]
> Identyfikator zdarzenia 9003 jest rejestrowany raz na godzinę w dzienniku zdarzeń telemetrii, jeśli plik nie będzie działać w warstwie (jedno zdarzenie jest rejestrowane na kod błędu). Sprawdź [sekcję Błędy obsługi warstw i korygowanie,](#tiering-errors-and-remediation) aby sprawdzić, czy na liście znajdują się kroki korygowania dotyczące kodu błędu.

### <a name="tiering-errors-and-remediation"></a>Błędy warstw i korygowanie

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Nie można przesłonić pliku, ponieważ początkowe przekazywanie jest w toku. | Żadna akcja nie jest wymagana. Plik będzie warstwowy po zakończeniu początkowego przekazywania. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Nie można przewarstwowyć pliku, ponieważ jest on w użyciu. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie, gdy nie będzie już używany. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Nie można przesłonić pliku, ponieważ został on wykluczony przez synchronizację. | Żadna akcja nie jest wymagana. Pliki na liście wykluczeń synchronizacji nie mogą być umieszczane w warstwie. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Nie można przesłonić pliku, ponieważ nie znaleziono go na serwerze. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź, czy plik istnieje na serwerze. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Nie można podzielić pliku na warstwy, ponieważ został usunięty z udziału plików platformy Azure. | Żadna akcja nie jest wymagana. Plik powinien zostać usunięty na serwerze, gdy zostanie uruchomiona następna sesja synchronizacji pobierania. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Warstwa pliku nie powiodła się z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Warstwa pliku nie powiodła się z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80070005 | -2147024891 | Error_access_denied | Warstwa pliku nie powiodła się z powodu błędu odmowy dostępu. Ten problem może występować, gdy plik znajduje się w folderze replikacji tylko do odczytu usługi DFS-R. | Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Aby uzyskać więcej informacji, zobacz [przewodnik planowania](file-sync-planning.md#distributed-file-system-dfs). |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Nie można warstwy pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Nie można przewarstwowyć pliku, ponieważ rozmiar pliku jest mniejszy niż obsługiwany rozmiar. | Jeśli agent jest w wersji starszej niż 9.0, minimalny obsługiwany rozmiar pliku to 64 KB. Jeśli agent jest w wersji 9.0 lub nowszej, minimalny obsługiwany rozmiar pliku zależy od rozmiaru klastra systemu plików (rozmiar jest dwukrotnie większy od rozmiaru klastra systemu plików). Jeśli na przykład rozmiar klastra systemu plików wynosi 4 kb, minimalny rozmiar pliku to 8 kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Warstwa pliku nie powiodła się z powodu problemu z usługą Azure Storage. | Jeśli błąd będzie się powtarzać, prześlij wniosek o pomoc techniczną. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Nie można przesłonić pliku, ponieważ został odwołany w tym samym czasie. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zakończeniu odwoływania, gdy nie będzie już używany. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Nie można przedzielić pliku na warstwę, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Nie można przefiltrować pliku, ponieważ sterownik filtru warstw w chmurze (storagesync.sys) nie jest uruchomiony. | Aby rozwiązać ten problem, otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie: `fltmc load storagesync`<br>Jeśli nie można załadować sterownika filtru storagesync podczas uruchamiania polecenia fltmc, odinstaluj agenta programu Azure File Sync, uruchom ponownie serwer i ponownie zainstaluj Azure File Sync agenta. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Warstwa pliku nie powiodła się z powodu niewystarczającej ilości miejsca na dysku na woluminie, na którym znajduje się punkt końcowy serwera. | Aby rozwiązać ten problem, zwolnij co najmniej 100 MB miejsca na dysku w woluminie, w którym znajduje się punkt końcowy serwera. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Nie można przedzielić pliku na warstwę, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Nie można przesłonić pliku, ponieważ jest to nieobsługiwany punkt ponownej ponownej wersji. | Jeśli plik jest punktem ponownej analizy deduplikacji danych, wykonaj kroki opisane w [przewodniku planowania](file-sync-planning.md#data-deduplication), aby włączyć obsługę deduplikacji danych. Pliki z punktami ponownej analizy innymi niż deduplikacja danych nie są obsługiwane i nie będą umieszczane w warstwie.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Nie można przewarstwowować pliku, ponieważ został on zmodyfikowany. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zsynchronizowaniu zmodyfikowanego pliku z udziałem plików platformy Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Nie można podzielić pliku na warstwy, ponieważ nie został on zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zsynchronizowaniu z udziałem plików platformy Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Nie można warstwy pliku z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Nie można przewarstwowować pliku, ponieważ został on zmodyfikowany. | Żadna akcja nie jest wymagana. Plik zostanie umieszczony w warstwie po zsynchronizowaniu zmodyfikowanego pliku z udziałem plików platformy Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Nie można przesłonić pliku z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra wyczerpuje zasoby systemowe. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Nie można przesłonić warstwy pliku z powodu błędu we/wy podczas zapisywania w bazie danych obsługi warstw w chmurze. | Jeśli błąd będzie się powtarzać, uruchom program chkdsk na woluminie i sprawdź sprzęt magazynu. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Nie można warstwy pliku, ponieważ baza danych warstw w chmurze nie jest uruchomiona. | Aby rozwiązać ten problem, uruchom ponownie usługę lub serwer FileSyncSvc. Jeśli błąd będzie się powtarzać, uruchom program chkdsk na woluminie i sprawdź sprzęt magazynu. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Jak rozwiązywać problemy z plikami, których nie można odwołać  
Jeśli nie można odwołać plików:
1. W Podgląd zdarzeń przejrzyj dzienniki zdarzeń telemetrycznych, operacyjnych i diagnostycznych znajdujące się w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent.
    1. Sprawdź, czy pliki istnieją w udziałach plików platformy Azure.
    2. Sprawdź, czy serwer ma łączność z Internetem. 
    3. Otwórz przystawkę Programu MMC usług i sprawdź, czy usługa agenta synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
    4. Sprawdź, Azure File Sync sterowniki filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
        - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy StorageSync.sys i StorageSyncGuard.sys filtrów systemu plików.

> [!NOTE]
> Identyfikator zdarzenia 9006 jest rejestrowany raz na godzinę w dzienniku zdarzeń telemetrii, jeśli nie można odwołać pliku (jedno zdarzenie jest rejestrowane na kod błędu). Sprawdź [sekcję Błędy odwoływania i korygowanie,](#recall-errors-and-remediation) aby sprawdzić, czy kroki korygowania są wymienione dla kodu błędu.

### <a name="recall-errors-and-remediation"></a>Błędy odwołania i korygowanie

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Nie można odwołać pliku z powodu przeoczania limitu czasu we/wy. Ten problem może wystąpić z kilku powodów: ograniczeń zasobów serwera, słabej łączności sieciowej lub problemu z usługą Azure Storage (na przykład z ograniczaniem przepustowości). | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać przez kilka godzin, otwórz zgłoszenie do pomocy technicznej. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Nie można odwołać pliku z powodu problemu z siecią.  | Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Nie można odwołać pliku, ponieważ punkt końcowy serwera został usunięty. | Aby rozwiązać ten problem, zobacz Pliki warstwowe nie są dostępne na serwerze po [usunięciu punktu końcowego serwera](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | Error_access_denied | Nie można odwołać pliku z powodu błędu odmowy dostępu. Ten problem występuje, gdy ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Aby rozwiązać ten problem, dodaj adres IP serwera lub sieć [](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) wirtualną, korzystając z procedury opisanej w sekcji Konfigurowanie ustawień zapory i sieci wirtualnej w przewodniku wdrażania. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Nie można odwołać pliku, ponieważ nie jest on dostępny w udziałach plików platformy Azure. | Aby rozwiązać ten problem, sprawdź, czy plik istnieje w udziale plików platformy Azure. Jeśli plik istnieje w udziałach plików platformy Azure, uaktualnij program do najnowszej Azure File Sync [wersji agenta](file-sync-release-notes.md#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Nie można odwołać pliku z powodu błędu autoryzacji na koncie magazynu. | Aby rozwiązać ten problem, [Azure File Sync ma dostęp do konta magazynu.](?tabs=portal1%252cazure-portal#troubleshoot-rbac) |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Nie można odwołać pliku, ponieważ udział plików platformy Azure jest niedostępny. | Sprawdź, czy udział plików istnieje i jest dostępny. Jeśli udział plików został usunięty i ponownie utworzony, wykonaj kroki opisane w sekcji Synchronizacja nie powiodło się, ponieważ udział plików platformy [Azure](?tabs=portal1%252cazure-portal#-2134375810) został usunięty i ponownie utworzony, aby usunąć i ponownie utworzyć grupę synchronizacji. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Nie można odwołać pliku z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra wyczerpuje zasoby systemowe. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Nie można odwołać pliku z powodu niewystarczającej ilości pamięci. | Jeśli błąd będzie się powtarzał, sprawdź, która aplikacja lub sterownik trybu jądra powoduje niedobór pamięci. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Nie można odwołać pliku z powodu niewystarczającej ilości miejsca na dysku. | Aby rozwiązać ten problem, zwolnij miejsce na woluminie poprzez przeniesienie plików na inny wolumin, zwiększenie rozmiaru woluminu lub wymuszenie umieszczenia plików w warstwie za pomocą polecenia Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Pliki wielowarstwowe są niedostępne na serwerze po usunięciu punktu końcowego serwera
Pliki warstwowe na serwerze staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera.

Błędy rejestrowane, jeśli pliki warstwowe nie są dostępne
- Podczas synchronizowania pliku kod błędu -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) jest rejestrowany w dzienniku zdarzeń ItemResults
- Podczas odwoływania się do pliku kod błędu -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) jest rejestrowany w dzienniku zdarzeń RecallResults

Przywrócenie dostępu do plików warstwowych jest możliwe w przypadku spełnienia następujących warunków:
- Punkt końcowy serwera został usunięty w ciągu ostatnich 30 dni
- Punkt końcowy w chmurze nie został usunięty 
- Udział plików nie został usunięty
- Grupa synchronizacji nie została usunięta

Jeśli powyższe warunki są spełnione, można przywrócić dostęp do plików na serwerze przez ponowne utworzenie punktu końcowego serwera w tej samej ścieżce na serwerze w ramach tej samej grupy synchronizacji w ciągu 30 dni. 

Jeśli powyższe warunki nie są spełnione, przywrócenie dostępu nie jest możliwe, ponieważ te pliki warstwowe na serwerze są teraz oddzielone. Postępuj zgodnie z poniższymi instrukcjami, aby usunąć oddzielone pliki warstwowe.

**Uwagi**
- Jeśli pliki warstwowe nie są dostępne na serwerze, pełny plik powinien być nadal dostępny, jeśli bezpośrednio uzyskujesz dostęp do udziału plików platformy Azure.
- Aby zapobiec oddzielonym plikom warstwowym w przyszłości, wykonaj kroki opisane w części [Usuwanie](file-sync-server-endpoint.md#remove-a-server-endpoint) punktu końcowego serwera podczas usuwania punktu końcowego serwera.

<a id="get-orphaned"></a>**Jak uzyskać listę oddzielonych plików warstwowych** 

1. Sprawdź Azure File Sync czy zainstalowano agenta w wersji 5.1 lub nowszej.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Zapisz plik wyjściowy OrphanTieredFiles.txt na wypadek, gdy pliki muszą zostać przywrócone z kopii zapasowej po ich usunięciu.

<a id="remove-orphaned"></a>**Jak usunąć oddzielone pliki warstwowe** 

*Opcja 1. Usuwanie oddzielonych plików warstwowych*

Ta opcja usuwa oddzielone pliki warstwowe w systemie Windows Server, ale wymaga usunięcia punktu końcowego serwera, jeśli istnieje z powodu przerwy po 30 dniach lub połączenia z inną grupą synchronizacji. Konflikty plików wystąpią, jeśli pliki zostaną zaktualizowane w systemie Windows Server lub w udziałach plików platformy Azure przed odtworzeniem punktu końcowego serwera.

1. Sprawdź Azure File Sync czy zainstalowano agenta w wersji 5.1 lub nowszej.
2. Tworzenie kopii zapasowej udziału plików platformy Azure i lokalizacji punktu końcowego serwera.
3. Usuń punkt końcowy serwera w grupie synchronizacji (jeśli istnieje), korzystając z procedury opisanej w tece [Usuwanie punktu końcowego serwera](file-sync-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Jeśli punkt końcowy serwera nie zostanie usunięty przed użyciem polecenia cmdlet Remove-StorageSyncOrphanedTieredFiles, usunięcie oddzielonego pliku warstwowego na serwerze spowoduje usunięcie pełnego pliku z udziału plików platformy Azure. 

4. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Zapisz plik wyjściowy OrphanTieredFiles.txt na wypadek, gdy pliki muszą zostać przywrócone z kopii zapasowej po ich usunięciu.
6. Uruchom następujące polecenia programu PowerShell, aby usunąć oddzielone pliki warstwowe:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Uwagi** 
- Pliki warstwowe zmodyfikowane na serwerze, które nie są synchronizowane z udziałem plików platformy Azure, zostaną usunięte.
- Pliki warstwowe, które są dostępne (nie oddzielone), nie zostaną usunięte.
- Pliki niewarstwowe pozostaną na serwerze.

7. Opcjonalnie: utwórz ponownie punkt końcowy serwera, jeśli został usunięty w kroku 3.

*Opcja 2. Zainstaluj udział plików platformy Azure i skopiuj lokalnie pliki oddzielone na serwerze*

Ta opcja nie wymaga usunięcia punktu końcowego serwera, ale wymaga wystarczającego miejsca na dysku, aby skopiować pełne pliki lokalnie.

1. [Zainstaluj](../files/storage-how-to-use-files-windows.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) udział plików platformy Azure w systemie Windows Server, który ma oddzielone pliki warstwowe.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę oddzielonych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Użyj OrphanTieredFiles.txt wyjściowego, aby zidentyfikować oddzielone pliki warstwowe na serwerze.
4. Zastąp oddzielone pliki warstwowe, kopiując pełny plik z udziału plików platformy Azure do systemu Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Jak rozwiązywać problemy z plikami nieoczekiwanie przywoływanymi na serwerze  
Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które odczytują dużą liczbę plików, powodują niezamierzone odwoływki, chyba że respektują atrybut pomijania trybu offline i pomijają odczytywanie zawartości tych plików. Pomijanie plików offline w przypadku produktów, które obsługują tę opcję, umożliwia uniknięcie niezamierzonych odwołań podczas operacji takich jak skanowanie antywirusowe lub zadania kopii zapasowej.

Skonsultuj się z dostawcą oprogramowania, aby dowiedzieć się, w jaki sposób skonfigurować rozwiązanie tak, aby odczytywanie plików offline było pomijane.

Niezamierzone zmiany mogą również wystąpić w innych scenariuszach, takich jak przeglądanie plików w Eksplorator plików. Otwarcie folderu, w którym znajdują się pliki w warstwach chmury, w Eksploratorze plików na serwerze może spowodować niezamierzone odwołania. Prawdopodobieństwo wystąpienia takiej sytuacji jest wyższe, jeśli na serwerze włączono rozwiązanie antywirusowe.

> [!NOTE]
>Użyj identyfikatora zdarzenia 9059 w dzienniku zdarzeń telemetrii, aby określić, które aplikacje powodują odwoływki. To zdarzenie zapewnia dystrybucję odwołań aplikacji dla punktu końcowego serwera i jest rejestrowane raz na godzinę.

### <a name="tls-12-required-for-azure-file-sync"></a>Wymagany jest TLS 1.2 dla Azure File Sync

Aby wyświetlić ustawienia TLS na serwerze, zobacz [ustawienia rejestru](/windows-server/security/tls/tls-registry-settings). 

Jeśli używasz serwera proxy, zapoznaj się z dokumentacją serwera proxy i upewnij się, że jest on skonfigurowany do korzystania z TLS1.2.

## <a name="general-troubleshooting"></a>Ogólne procedury rozwiązywania problemów
Jeśli wystąpią problemy z Azure File Sync na serwerze, rozpocznij od wykonania następujących czynności:
1. W Podgląd zdarzeń przejrzyj dzienniki zdarzeń telemetrycznych, operacyjnych i diagnostycznych.
    - Problemy z synchronizacją, warstwami i odwoływaniem są rejestrowane w dziennikach zdarzeń telemetrycznych, diagnostycznych i operacyjnych w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent.
    - Problemy związane z zarządzaniem serwerem (na przykład ustawieniami konfiguracji) są rejestrowane w operacyjnych i diagnostycznych dziennikach zdarzeń w obszarze Aplikacje i usługi\Microsoft\FileSync\Management.
2. Sprawdź, Azure File Sync usługa jest uruchomiona na serwerze:
    - Otwórz przystawkę mmc usług i sprawdź, czy usługa agenta synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
3. Sprawdź, Azure File Sync sterowników filtrów (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
    - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie `fltmc` . Sprawdź, czy StorageSync.sys i StorageSyncGuard.sys filtrów systemu plików są wyświetlane.

Jeśli problem nie zostanie rozwiązany, uruchom narzędzie AFSDiag i wyślij dane wyjściowe pliku zip do inżyniera pomocy technicznej przypisanego do Twojego przypadku w celu dalszej diagnostyki.

Aby uruchomić program AFSDiag, wykonaj poniższe kroki.

W przypadku agenta w wersji 11 lub nowszej:
1. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (naciśnij klawisz Enter po każdym poleceniu):

    > [!NOTE]
    >AfSDiag utworzy katalog wyjściowy i folder tymczasowy w nim przed zebranie dzienników i usunie folder tymczasowy po wykonaniu. Określ lokalizację wyjściową, która nie zawiera danych.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Odtwórz problem. Po zakończeniu wprowadź **D**.
3. Plik zip zawierający dzienniki i pliki śledzenia jest zapisywany w określonym katalogu wyjściowym. 

W przypadku agenta w wersji 10 lub starszej:
1. Utwórz katalog, w którym zostaną zapisane dane wyjściowe AFSDiag (na przykład C:\Output).
    > [!NOTE]
    >Przed zebraniem dzienników usługa AFSDiag usunie całą zawartość w katalogu wyjściowym. Określ lokalizację wyjściową, która nie zawiera danych.
2. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (naciśnij klawisz Enter po każdym poleceniu):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Dla Azure File Sync śledzenia trybu jądra wprowadź **wartość 1** (chyba że określono inaczej, aby utworzyć więcej pełnych śladów), a następnie naciśnij klawisz Enter.
4. W przypadku Azure File Sync śledzenia trybu użytkownika wprowadź **wartość 1** (chyba że określono inaczej, aby utworzyć więcej pełnych śladów), a następnie naciśnij klawisz Enter.
5. Odtwórz problem. Po zakończeniu wprowadź **D**.
6. Plik zip zawierający dzienniki i pliki śledzenia jest zapisywany w określonym katalogu wyjściowym.


## <a name="see-also"></a>Zobacz też
- [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)
- [Troubleshoot Azure Files problems in Linux (Rozwiązywanie problemów z usługą Azure Files w systemie Linux)](../files/storage-troubleshoot-linux-file-connection-problems.md).