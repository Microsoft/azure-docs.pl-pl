---
title: Urządzenie replikacji w usłudze Azure Migrate
description: Dowiedz się więcej o urządzeniu replikacji Azure Migrate na potrzeby migracji oprogramowania VMWare opartego na agentach.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754100"
---
# <a name="replication-appliance"></a>Urządzenie replikacji

W tym artykule opisano urządzenie do replikacji używane przez [Azure Migrate: Narzędzie do migracji serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) podczas migrowania maszyn wirtualnych VMware, maszyn fizycznych i prywatnych/publicznych maszyn wirtualnych do platformy Azure przy użyciu migracji opartej na agentach. 


## <a name="overview"></a>Omówienie

Urządzenie replikacji jest wdrażane podczas konfigurowania migracji opartej na agentach maszyn wirtualnych VMware lub serwerów fizycznych. Jest ona wdrażana jako pojedyncza maszyna lokalna jako maszyna wirtualna VMware lub serwer fizyczny. Działa:

- **Urządzenie replikacji**: urządzenie replikacji koordynuje komunikację i zarządza replikacją danych dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych replikowanych do platformy Azure.
- **Serwer przetwarzania**: serwer przetwarzania, który jest instalowany domyślnie na urządzeniu replikacji, i wykonuje następujące czynności:
    - **Brama replikacji**: działa jako brama replikacji. Odbiera dane replikacji z maszyn, na których włączono replikację. Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania, a następnie wysyła je do platformy Azure.
    - **Instalator agenta**: wykonuje instalację wypychaną usługi mobilności. Ta usługa musi być zainstalowana i uruchomiona na każdej maszynie lokalnej, która ma zostać zreplikowana do migracji.

## <a name="appliance-deployment"></a>Wdrażanie urządzenia

**Sposób użycia** | **Szczegóły**
--- |  ---
**Migracja oparta na agentach maszyn wirtualnych VMware** | Szablon komórki jajowe można pobrać z centrum Azure Migrate i zaimportować do vCenter Server, aby utworzyć maszynę wirtualną urządzenia.
**Migracja oparta na agencie maszyn fizycznych** | Jeśli nie masz infrastruktury VMware lub nie można utworzyć maszyny wirtualnej VMware przy użyciu szablonu komórki jajowe, pobierz instalator oprogramowania z centrum Azure Migrate i uruchom go, aby skonfigurować maszynę.

> [!NOTE]
> W przypadku wdrażania programu w programie Azure Government należy użyć pliku instalacyjnego do wdrożenia urządzenia replikacji.

## <a name="appliance-requirements"></a>Wymagania dotyczące urządzeń

Po skonfigurowaniu urządzenia do replikacji przy użyciu szablonu komórki jajowe dostarczonego w centrum Azure Migrate na urządzeniu jest uruchomiony system Windows Server 2016 i jest zgodny z wymaganiami dotyczącymi obsługi. Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami.

**Składnik** | **Wymaganie**
--- | ---
 | **Urządzenie maszyny wirtualnej VMware**
PowerCLI | Jeśli urządzenie replikacji jest uruchomione na maszynie wirtualnej VMware, należy zainstalować [PowerCLI w wersji 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Typ karty sieciowej | VMXNET3 (Jeśli urządzenie jest maszyną wirtualną VMware)
 | **Ustawienia sprzętu**
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | Trzy: dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania.
Wolne miejsce na dysku (pamięć podręczna) | 600 GB
Wolne miejsce na dysku (dysk przechowywania) | 600 GB
**Ustawienia oprogramowania** |
System operacyjny | Windows Server 2016 lub Windows Server 2012 R2
Licencja | Urządzenie jest dostarczane z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/><br/> Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
TLS | Protokół TLS 1,2 powinien być włączony.
.NET Framework | Na maszynie należy zainstalować .NET Framework 4,6 lub nowszą (z włączonym silnym kryptografią.
MySQL | Baza danych MySQL powinna być zainstalowana na urządzeniu.<br/> Należy zainstalować MySQL. Można zainstalować go ręcznie lub Site Recovery można go zainstalować podczas wdrażania urządzenia.
Inne aplikacje | Nie uruchamiaj innych aplikacji na urządzeniu replikacji.
Role systemu Windows Server | Nie należy włączać tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br> — Logika zaufania dla plików załączników. <br> — Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Brak istniejącej domyślnej witryny sieci Web <br> — Żadna istniejąca witryna sieci Web/aplikacja nasłuchu na porcie 443 <br>-Włącz  [uwierzytelnianie anonimowe](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Włącz ustawienie [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Ustawienia sieciowe** |
Typ adresu IP | Static
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)
Typ karty sieciowej | VMXNET3

## <a name="mysql-installation"></a>Instalacja MySQL 

Pakiet MySQL musi być zainstalowany na komputerze z urządzeniem replikacji. Można go zainstalować przy użyciu jednej z tych metod.

**Metoda** | **Szczegóły**
--- | ---
Pobierz i zainstaluj ręcznie | Pobierz aplikację MySQL & umieść ją w folderze C:\Temp\ASRSetup, a następnie zainstaluj ręcznie.<br/> Po skonfigurowaniu urządzenia MySQL będą wyświetlane jako już zainstalowane.
Bez pobierania online | Umieść aplikację instalatora MySQL w folderze C:\Temp\ASRSetup. Po zainstalowaniu urządzenia i kliknięciu w celu pobrania i zainstalowania programu MySQL Instalator użyje dodanego Instalatora.
Pobierz i zainstaluj w Azure Migrate | Po zainstalowaniu urządzenia i wyświetleniu monitu o wprowadzenie do bazy danych MySQL wybierz pozycję **Pobierz i zainstaluj**.

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie replikacji musi mieć dostęp do tych adresów URL w chmurze publicznej platformy Azure.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.com | Używany do transferu i koordynacji replikowanych danych
\*.store.core.windows.net | Używany do transferu i koordynacji replikowanych danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane
\*.hypervrecoverymanager.windowsazure.com | Używany do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Używany do operacji zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używane na potrzeby telemetrii (jest opcjonalne)
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.Live.com <br/> https: \/ /Graph.Windows.NET <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.Microsoft.com  | Konfiguracja urządzenia wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby ukończyć pobieranie bazy danych MySQL. W kilku regionach pobieranie może zostać przekierowane na adres URL sieci CDN. Upewnij się, że adres URL usługi CDN jest również dozwolony w razie konieczności.


## <a name="azure-government-url-access"></a>Azure Government dostęp do adresów URL

Urządzenie replikacji musi mieć dostęp do tych adresów URL w Azure Government.

**Adres URL** | **Szczegóły**
--- | ---
\*. backup.windowsazure.us | Używany do transferu i koordynacji replikowanych danych
\*.store.core.windows.net | Używany do transferu i koordynacji replikowanych danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane
\*. hypervrecoverymanager.windowsazure.us | Używany do operacji zarządzania replikacją i koordynacji
https:\//management.usgovcloudapi.net | Używany do operacji zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używane na potrzeby telemetrii (jest opcjonalne)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.Live.com <br/> https: \/ /Graph.Windows.NET <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.Microsoft.com  | Konfiguracja urządzenia z komórkami jajowymi wymaga dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby ukończyć pobieranie bazy danych MySQL. W kilku regionach pobieranie może zostać przekierowane na adres URL sieci CDN. Upewnij się, że adres URL usługi CDN jest również dozwolony w razie konieczności.

## <a name="port-access"></a>Dostęp do portu

**Urządzenie** | **Połączenie**
--- | ---
Maszyny wirtualne | Usługa mobilności działająca na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwer konfiguracji) na porcie HTTPS 443 przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpocznie się replikacja początkowa do usługi Azure Storage przy użyciu określonych zasad replikacji. 
2. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Śledzone zmiany dla maszyny są rejestrowane.
4. Komunikacja odbywa się w następujący sposób:
    - Maszyny wirtualne komunikują się z urządzeniem replikacji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na urządzeniu replikacji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.
5. Dane replikacji są najpierw przechowywane na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure.

![Diagram przedstawia architekturę procesu replikacji.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie jest uaktualniane ręcznie z centrum Azure Migrate. Zalecamy, aby zawsze była uruchamiana Najnowsza wersja.

1. W Azure Migrate > serwery > Azure Migrate: Ocena serwera, serwery infrastruktury, kliknij przycisk **serwery konfiguracji**.
2. W obszarze **serwery konfiguracji** w **wersji agenta** pojawia się łącze, gdy dostępna jest nowa wersja urządzenia replikacji. 
3. Pobierz instalatora na komputer z urządzeniem replikacji i zainstaluj uaktualnienie. Instalator wykryje bieżącą wersję uruchomioną na urządzeniu.
 
## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) skonfigurować urządzenie do replikacji na potrzeby migracji maszyn wirtualnych VMware opartych na agentach.
- [Dowiedz się, jak](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) skonfigurować urządzenie do replikacji dla serwerów fizycznych.
