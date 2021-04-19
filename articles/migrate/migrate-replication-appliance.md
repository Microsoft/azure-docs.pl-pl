---
title: Urządzenie replikacji w usłudze Azure Migrate
description: Dowiedz się więcej o Azure Migrate replikacji na podstawie agenta migracji oprogramowania VMware.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5f63b033c3995932662fc9b68c1397bf57b0326e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714978"
---
# <a name="replication-appliance"></a>Urządzenie replikacji

W tym artykule opisano urządzenie replikacji używane przez narzędzie [Azure Migrate: Narzędzie](migrate-services-overview.md#azure-migrate-server-migration-tool) do migracji serwera podczas migrowania maszyn wirtualnych VMware, maszyn fizycznych i prywatnych/publicznych maszyn wirtualnych w chmurze na platformę Azure przy użyciu migracji opartej na agentach. 


## <a name="overview"></a>Omówienie

Urządzenie replikacji jest wdrażane podczas instalacji migracji maszyn wirtualnych VMware lub serwerów fizycznych opartych na agentach. Jest ona wdrażana jako pojedyncza maszyna lokalna, jako maszyna wirtualna VMware lub serwer fizyczny. Jest on uruchamiany:

- **Urządzenie replikacji:** urządzenie replikacji koordynuje komunikację i zarządza replikacją danych dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych replikowania do platformy Azure.
- **Serwer przetwarzania:** serwer przetwarzania instalowany domyślnie na urządzeniu replikacji i przetwarzany w następujący sposób:
    - **Brama replikacji:** działa jako brama replikacji. Odbiera dane replikacji z maszyn z włączoną replikacją. Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania oraz wysyła je na platformę Azure.
    - **Instalator agenta:** wykonuje instalację wypychaną usługi mobilności. Ta usługa musi być zainstalowana i uruchomiona na każdej maszynie lokalnej, która ma zostać zreplikowana w celu migracji.

## <a name="appliance-deployment"></a>Wdrażanie urządzenia

**Sposób użycia** | **Szczegóły**
--- |  ---
**Migracja oparta na agentach maszyn wirtualnych VMware** | Pobierz szablon OVA z centrum Azure Migrate i zaimportuj go do vCenter Server, aby utworzyć maszynę wirtualną urządzenia.
**Migracja oparta na agencie maszyny fizycznej** | Jeśli nie masz infrastruktury VMware lub nie możesz utworzyć maszyny wirtualnej VMware przy użyciu szablonu OVA, pobierz instalator oprogramowania z centrum Azure Migrate i uruchom go, aby skonfigurować maszynę urządzenia.

> [!NOTE]
> Jeśli wdrażasz w programie Azure Government, użyj pliku instalacyjnego, aby wdrożyć urządzenie replikacji.

## <a name="appliance-requirements"></a>Wymagania dotyczące urządzenia

Po skonfigurowaniu urządzenia replikacji przy użyciu szablonu OVA dostarczonego w centrum Azure Migrate urządzenie uruchamia system Windows Server 2016 i spełnia wymagania dotyczące obsługi. W przypadku ręcznego skonfigurowania urządzenia replikacji na serwerze fizycznym upewnij się, że spełnia ono wymagania.

**Składnik** | **Wymaganie**
--- | ---
 | **Urządzenie maszyny wirtualnej VMware**
PowerCLI | Jeśli urządzenie replikacji jest uruchomione na maszynie wirtualnej VMware, należy zainstalować program PowerCLI w wersji [6.0.](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)
Typ karty sieciowej | VMXNET3 (jeśli urządzenie jest maszyną wirtualną VMware)
 | **Ustawienia sprzętu**
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | Trzy: dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania.
Wolne miejsce na dysku (pamięć podręczna) | 600 GB
Wolne miejsce na dysku (dysk przechowywania) | 600 GB
**Ustawienia oprogramowania** |
System operacyjny | Windows Server 2016 lub Windows Server 2012 R2
Licencja | Urządzenie jest dostarczany z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/><br/> Jeśli okres ewaluowania jest bliski wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia lub aktywowanie licencji systemu operacyjnego maszyny wirtualnej urządzenia.
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
TLS | Należy włączyć obsługę TLS 1.2.
.NET Framework | .NET Framework 4.6 lub nowszego powinny być zainstalowane na maszynie (z włączoną silną kryptografią.
MySQL | Na urządzeniu powinien być zainstalowany program MySQL.<br/> Należy zainstalować program MySQL. Instalację można zainstalować ręcznie lub Site Recovery zainstalować podczas wdrażania urządzenia.
Inne aplikacje | Nie uruchamiaj innych aplikacji na urządzeniu replikacji.
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Zasady grupy | Nie włączaj tych zasad grupy: <br> - Uniemożliwić dostęp do wiersza polecenia. <br> — Uniemożliwianie dostępu do narzędzi do edytowania rejestru. <br> — Logika zaufania dla załączników plików. <br> — Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Brak istniejącej domyślnej witryny internetowej <br> - Brak istniejącej witryny internetowej/aplikacji nasłuchuje na porcie 443 <br>- Włącz  [uwierzytelnianie anonimowe](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Włącz [ustawienie FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Ustawienia sieciowe** |
Typ adresu IP | Static
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)
Typ karty sieciowej | VMXNET3

## <a name="mysql-installation"></a>Instalacja programu MySQL 

Program MySQL musi być zainstalowany na maszynie urządzenia replikacji. Można go zainstalować przy użyciu jednej z tych metod.

**Metoda** | **Szczegóły**
--- | ---
Ręczne pobieranie i instalowanie | Pobierz aplikację MySQL& umieść ją w folderze C:\Temp\ASRSetup, a następnie zainstaluj ją ręcznie.<br/> Po skonfigurowaniu urządzenia program MySQL będzie pokazywany jako już zainstalowany.
Bez pobierania online | Umieść aplikację instalatora MySQL w folderze C:\Temp\ASRSetup. Po zainstalowaniu urządzenia i wybraniu opcji pobierania i instalowania programu MySQL instalator użyje dodanego instalatora.
Pobieranie i instalowanie w programie Azure Migrate | Po zainstalowaniu urządzenia i wyświetleniu monitu o program MySQL wybierz **pozycję Pobierz i zainstaluj**.

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie replikacji musi mieć dostęp do tych adresów URL w chmurze publicznej Azure.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.com | Służy do transferu i koordynacji replikowanych danych
\*.store.core.windows.net | Służy do transferu i koordynacji replikowanych danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu, które przechowuje replikowane dane
\*.hypervrecoverymanager.windowsazure.com | Służy do zarządzania replikacją i koordynacji
https:\//management.azure.com | Służy do zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używane do celów rejestrowania (jest to opcjonalne)
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | Konfiguracja urządzenia wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby ukończyć pobieranie oprogramowania MySQL. W kilku regionach pobieranie może zostać przekierowane do adresu URL usługi CDN. Upewnij się, że adres URL usługi CDN jest również dozwolony w razie potrzeby.


## <a name="azure-government-url-access"></a>Azure Government adresu URL

Urządzenie replikacji musi mieć dostęp do tych adresów URL w Azure Government.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.us | Służy do transferu i koordynacji replikowanych danych
\*.store.core.windows.net | Służy do transferu i koordynacji replikowanych danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu, które przechowuje replikowane dane
\*.hypervrecoverymanager.windowsazure.us | Służy do zarządzania replikacją i koordynacji
https:\//management.usgovcloudapi.net | Służy do zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używany do celów rejestrowania (jest opcjonalny)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | Konfiguracja urządzenia przy użyciu OVA wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby ukończyć pobieranie oprogramowania MySQL. W kilku regionach pobieranie może zostać przekierowane do adresu URL usługi CDN. Upewnij się, że adres URL usługi CDN jest również dozwolony w razie potrzeby.  

>[!Note]
>
> Jeśli projekt migracji ma łączność z prywatnym punktem końcowym, będziesz potrzebować dostępu do następujących adresów URL za pośrednictwem lub powyżej dostępu do linku prywatnego:   
> - *.blob.core.windows.com — aby uzyskać dostęp do konta magazynu, które przechowuje replikowane dane. Jest to opcjonalne i nie jest wymagane, jeśli konto magazynu ma dołączony prywatny punkt końcowy. 
> - https: \/ /management.azure.com operacji zarządzania replikacją i koordynacji. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https: \/ /www.live.com _i_ <br/> https: \/ /www.microsoft.com do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory

## <a name="port-access"></a>Dostęp do portów

**Urządzenie** | **Połączenie**
--- | ---
Maszyny wirtualne | Serwer usługa mobilności uruchomiony na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwerem konfiguracji) na porcie PRZYCHODZĄCYM HTTPS 443 w celu zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na maszynie serwera konfiguracji) na porcie PRZYCHODZĄCYM HTTPS 9443. Ten port można zmodyfikować.
Urządzenie replikacji | Urządzenie replikacji aranżuje replikację z platformą Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage za pośrednictwem portu 443 dla ruchu wychodzącego.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpoczyna się replikacja początkowa do usługi Azure Storage przy użyciu określonych zasad replikacji. 
2. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja z lokacji lokalnej na platformę Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Rejestrowane są śledzone zmiany dla maszyny.
4. Komunikacja odbywa się w następujący sposób:
    - Maszyny wirtualne komunikują się z urządzeniem replikacji na porcie HTTPS 443 dla ruchu przychodzącego w celu zarządzania replikacją.
    - Urządzenie replikacji aranżuje replikację z platformą Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na urządzeniu replikacji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port można zmodyfikować.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage za pośrednictwem portu 443 dla ruchu wychodzącego.
5. Dzienniki danych replikacji najpierw trafią na konto magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure.

![Diagram przedstawiający architekturę procesu replikacji.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie jest uaktualniane ręcznie z Azure Migrate centrum. Zalecamy, aby zawsze uruchamiać najnowszą wersję.

1. W Azure Migrate > serwery > Azure Migrate: Ocena serwera, Serwery infrastruktury, wybierz **pozycję Serwery konfiguracji.**
2. Na **serwerach konfiguracji** link jest wyświetlany w **wersji agenta,** gdy dostępna jest nowa wersja urządzenia replikacji. 
3. Pobierz instalatora na maszynę urządzenia replikacji i zainstaluj uaktualnienie. Instalator wykrywa bieżącą wersję na urządzeniu.
 
## <a name="next-steps"></a>Następne kroki

- [Dowiedz się,](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) jak skonfigurować urządzenie replikacji do migracji maszyn wirtualnych VMware opartych na agentach.
- [Dowiedz się,](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) jak skonfigurować urządzenie replikacji dla serwerów fizycznych.
