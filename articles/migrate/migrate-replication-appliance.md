---
title: Urządzenie replikacji w usłudze Azure Migrate
description: Dowiedz się więcej o urządzeniu replikacji migracji platformy Azure do migracji VMWare opartej na agentach.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 85641f514fc4367f02901eb1dd394cfa204c3ec4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535217"
---
# <a name="replication-appliance"></a>Urządzenie replikacji

W tym artykule opisano urządzenie replikacji używane przez [narzędzie Azure Migrate: Narzędzie migracji serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) podczas migracji maszyn wirtualnych VMware, komputerów fizycznych i maszyn wirtualnych chmury prywatnej/publicznej na platformę Azure przy użyciu migracji opartej na agentach. 


## <a name="overview"></a>Omówienie

Urządzenie replikacji jest wdrażane podczas konfigurowania migracji maszyn wirtualnych vmware lub serwerów fizycznych na podstawie agenta. Jest on wdrażany jako pojedynczy komputer lokalny, jako maszyna wirtualna VMware lub serwer fizyczny. Działa:

- **Urządzenie replikacji:** Urządzenie replikacji koordynuje komunikację i zarządza replikacją danych dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych replikujących na platformie Azure.
- **Serwer przetwarzania:** Serwer przetwarzania, który jest instalowany domyślnie na urządzeniu replikacji i wykonuje następujące czynności:
    - **Brama replikacji:** działa jako brama replikacji. Odbiera dane replikacji z maszyn włączonych do replikacji. Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania i wysyła je na platformę Azure.
    - **Instalator agenta**: Wykonuje instalację wypychaną usługi mobilności. Ta usługa musi być zainstalowana i uruchomiona na każdym komputerze lokalnym, który ma zostać zreplikowany w celu migracji.

## <a name="appliance-deployment"></a>Wdrażanie urządzeń

**Używana do** | **Szczegóły**
--- |  ---
**Migracja oparta na maszynach VMware na podstawie agenta** | Pobierz szablon ova z centrum migracji platformy Azure i zaimportuj do serwera vCenter Server, aby utworzyć maszynę wirtualną urządzenia.
**Migracja oparta na agentach komputera fizycznego** | Jeśli nie masz infrastruktury VMware lub nie możesz utworzyć maszyny wirtualnej VMware przy użyciu szablonu ova, pobierz instalator oprogramowania z centrum migracji platformy Azure i uruchom go w celu skonfigurowania komputera urządzenia.

> [!NOTE]
> Jeśli wdrażasz w usłudze Azure Government, użyj pliku instalacyjnego, aby wdrożyć urządzenie replikacji.

## <a name="appliance-requirements"></a>Wymagania dotyczące urządzeń

Podczas konfigurowania urządzenia replikacji przy użyciu szablonu ova dostępnego w centrum migracji platformy Azure urządzenie uruchamia system Windows Server 2016 i spełnia wymagania pomocy technicznej. Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami.

**Składnik** | **Wymaganie**
--- | ---
 | **Urządzenie VMware VM**
PowerCLI (WYCHOWA) | [PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) należy zainstalować, jeśli urządzenie replikacji jest uruchomione na maszynie wirtualnej VMware.
Typ karty sieciowej | VMXNET3 (jeśli urządzenie jest maszyną wirtualną VMware)
 | **Ustawienia sprzętu**
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | Po trzecie: dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania.
Wolne miejsce na dysku (pamięć podręczna) | 600 GB
Wolne miejsce na dysku (dysk retencyjny) | 600 GB
**Ustawienia oprogramowania** |
System operacyjny | Windows Server 2016 lub Windows Server 2012 R2
Licencja | Urządzenie jest wyposażone w licencję ewaluacją systemu Windows Server 2016, która jest ważna przez 180 dni.<br/><br/> Jeśli okres oceny jest bliski wygaśnięcia, zaleca się pobranie i wdrożenie nowego urządzenia lub aktywowanie licencji systemu operacyjnego maszyny Wirtualnej urządzenia.
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
TLS | TLS 1.2 powinien być włączony.
.NET Framework | .NET Framework 4.6 lub nowsza powinna być zainstalowana na komputerze (z włączoną silną kryptografią).
MySQL | MySQL powinien być zainstalowany na urządzeniu.<br/> MySQL powinien być zainstalowany. Można zainstalować ręcznie lub Site Recovery można zainstalować go podczas wdrażania urządzenia.
Inne aplikacje | Nie uruchamiaj innych aplikacji na urządzeniu replikacji.
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Zasady grupy | Nie włączaj tych zasad grupy: <br> - Uniemożliwić dostęp do wiersza polecenia. <br> - Uniemożliwić dostęp do narzędzi do edycji rejestru. <br> - Logika zaufania dla załączników plików. <br> - Włącz wykonywanie skryptów. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Brak istniejącej wcześniej domyślnej strony internetowej <br> - Brak istniejącej strony internetowej / nasłuchiwania aplikacji na porcie 443 <br>- Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Ustawienia sieciowe** |
Typ adresu IP | Statyczny
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)
Typ karty sieciowej | VMXNET3

## <a name="mysql-installation"></a>Instalacja MySQL 

MySQL musi być zainstalowany na urządzeniu replikacji. Można go zainstalować przy użyciu jednej z tych metod.

**Metoda** | **Szczegóły**
--- | ---
Pobieranie i instalowanie ręcznie | Pobierz aplikację MySQL & umieść ją w folderze C:\Temp\ASRSetup, a następnie zainstaluj ręcznie.<br/> Po skonfigurowaniu urządzenia MySQL będzie wyświetlane jako już zainstalowane.
Bez pobierania online | Umieść aplikację instalatora MySQL w folderze C:\Temp\ASRSetup. Po zainstalowaniu urządzenia i kliknięciu, aby pobrać i zainstalować MySQL, instalator użyje dodanego instalatora.
Pobieranie i instalowanie w usłudze Azure Migrate | Po zainstalowaniu urządzenia i wyświetleniu monitu o mysql wybierz pozycję **Pobierz i zainstaluj**.

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie replikacji potrzebuje dostępu do tych adresów URL w chmurze publicznej platformy Azure.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.com | Służy do replikowanego transferu i koordynacji danych
\*.store.core.windows.net | Służy do replikowanego transferu i koordynacji danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu, które przechowuje replikowane dane
\*.hypervrecoverymanager.windowsazure.com | Używane do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Używane do operacji zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używany do celów telemetrycznych (jest opcjonalny)
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Konfiguracja urządzenia wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez usługę Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby zakończyć pobieranie MySQL. W kilku regionach pobieranie może zostać przekierowane do adresu URL sieci CDN. Upewnij się, że adres URL sieci CDN jest również dozwolony w razie potrzeby.


## <a name="azure-government-url-access"></a>Dostęp do adresu URL platformy Azure dla instytucji rządowych

Urządzenie replikacji potrzebuje dostępu do tych adresów URL w usłudze Azure Government.

**Adres URL** | **Szczegóły**
--- | ---
\*backup.windowsazure.us .backup.windowsazure.us | Służy do replikowanego transferu i koordynacji danych
\*.store.core.windows.net | Służy do replikowanego transferu i koordynacji danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu, które przechowuje replikowane dane
\*hypervrecoverymanager.windowsazure.us . | Używane do operacji zarządzania replikacją i koordynacji
https:\//management.usgovcloudapi.net | Używane do operacji zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używany do celów telemetrycznych (jest opcjonalny)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Konfiguracja urządzenia z urządzeniem typu OVA wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez usługę Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby zakończyć pobieranie MySQL. W kilku regionach pobieranie może zostać przekierowane do adresu URL sieci CDN. Upewnij się, że adres URL sieci CDN jest również dozwolony w razie potrzeby.

## <a name="port-access"></a>Dostęp do portu

**Urządzenie** | **Połączenia**
--- | ---
Maszyny wirtualne | Usługa mobilności uruchomiona na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwerem konfiguracji) na porcie HTTPS 443 przychodzącym w celu zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 przychodzącym. Ten port można modyfikować.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pomocą portu 443 wychodzącego.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej rozpoczyna się replikacja początkowa do magazynu platformy Azure przy użyciu określonych zasad replikacji. 
2. Ruch jest replikowane do publicznych punktów końcowych magazynu platformy Azure przez Internet. Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej lokacji (VPN) z lokacji lokalnej na platformę Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja delta. Śledzone zmiany dla maszyny są rejestrowane.
4. Komunikacja odbywa się w następujący sposób:
    - Maszyny wirtualne komunikują się z urządzeniem replikacji na porcie HTTPS 443 przychodzącym w celu zarządzania replikacją.
    - Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na urządzeniu replikacji) na porcie HTTPS 9443 przychodzącym. Ten port można modyfikować.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pomocą portu 443 wychodzącego.
5. Dzienniki danych replikacji najpierw lądują na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure.

![Architektura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Modernizacje urządzeń

Urządzenie jest uaktualniane ręcznie z centrum migracji platformy Azure. Zalecamy, aby zawsze uruchamiać najnowszą wersję.

1. W obszarze Migracja > serwerów > usługi Azure Migrate: Ocena serwera, serwery infrastruktury kliknij pozycję **Serwery konfiguracji**.
2. W **obszarze Serwery konfiguracji**w wersji **agenta** pojawia się łącze, gdy dostępna jest nowa wersja urządzenia replikacji. 
3. Pobierz instalatora na urządzenie replikacji i zainstaluj uaktualnienie. Instalator wykrywa bieżący wersję uruchomiony na urządzeniu.
 
## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) skonfigurować urządzenie replikacji do migracji maszyn wirtualnych VMware oparte na agentach.
- [Dowiedz się, jak](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) skonfigurować urządzenie replikacji dla serwerów fizycznych.
