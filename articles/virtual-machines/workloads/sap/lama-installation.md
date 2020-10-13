---
title: Łącznik SAP LaMa dla platformy Azure | Microsoft Docs
description: Zarządzanie systemami SAP na platformie Azure przy użyciu rozwiązania SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 5e20863cd971a55142283676fe035d3238520ae1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361368"
---
# <a name="sap-lama-connector-for-azure"></a>Łącznik SAP LaMa dla platformy Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Ogólne zestawienie pomocy technicznej: zawsze otwieraj zdarzenie przy użyciu SAP na składniku BC-VCM-LVM-HYPERV, jeśli potrzebujesz wsparcia SAP LaMa lub Azure Connector.

System SAP LaMa jest używany przez wielu klientów do obsługi i monitorowania ich krajobrazu. Ponieważ SAP LaMa 3,0 SP05, jest domyślnie dostarczany z łącznikiem do platformy Azure. Za pomocą tego łącznika można cofnąć alokację i uruchomienie maszyn wirtualnych, skopiować i zmienić lokalizację dysków zarządzanych oraz usunąć dyski zarządzane. Za pomocą tych podstawowych operacji można przenosić, kopiować, klonować i odświeżać systemy SAP przy użyciu rozwiązania SAP LaMa.

W tym przewodniku opisano sposób konfigurowania łącznika platformy Azure dla oprogramowania SAP LaMa, tworzenia maszyn wirtualnych, których można użyć do instalacji adaptacyjnych systemów SAP i sposobu ich konfigurowania.

> [!NOTE]
> Łącznik jest dostępny tylko w oprogramowaniu SAP LaMa Enterprise Edition

## <a name="resources"></a>Zasoby

Poniższe uwagi dotyczące oprogramowania SAP dotyczą tematu SAP LaMa na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [2343511] |Łącznik Microsoft Azure dla zarządzania w środowisku SAP (LaMa) |
| [2350235] |Zarządzanie środowiskiem SAP 3,0 — Enterprise Edition |

Przeczytaj także [Portal pomocy SAP dla oprogramowania SAP Lama](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Uwagi ogólne

* Upewnij się, że włączono *Automatyczne tworzenie mountpoint* w instalatorze-> ustawienia — aparat >  
  Jeśli rozwiązanie SAP LaMa zainstaluje woluminy przy użyciu rozszerzeń SAP adaptacyjnych na maszynie wirtualnej, punkt instalacji musi istnieć, jeśli to ustawienie nie jest włączone.

* Użyj oddzielnej podsieci i nie używaj dynamicznych adresów IP, aby zapobiec kradzieży adresu IP "podczas wdrażania nowych maszyn wirtualnych i wystąpień oprogramowania SAP  
  W przypadku używania dynamicznego przydzielania adresów IP w podsieci, który jest również używany przez oprogramowanie SAP LaMa, przygotowywanie systemu SAP za pomocą oprogramowania SAP LaMa może zakończyć się niepowodzeniem. Jeśli system SAP jest nieprzygotowany, adresy IP nie są zarezerwowane i mogą zostać przydzielone do innych maszyn wirtualnych.

* Jeśli zalogujesz się do hostów zarządzanych, pamiętaj, aby nie blokować odinstalowywania systemów plików  
  Jeśli zalogujesz się do maszyn wirtualnych z systemem Linux i zmienisz katalog roboczy na katalog w punkcie instalacji, na przykład/usr/sap/AH1/ASCS00/exe, nie można odinstalować woluminu, a zmiana położenia lub nieprzygotowywania zakończy się niepowodzeniem.

* Upewnij się, że CLOUD_NETCONFIG_MANAGE na maszynach wirtualnych SUSE SLES Linux. Aby uzyskać więcej informacji, zobacz [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Konfigurowanie łącznika platformy Azure dla oprogramowania SAP LaMa

Łącznik platformy Azure jest dostarczany jako system SAP LaMa 3,0 SP05. Zalecamy zawsze zainstalowanie najnowszego pakietu i poprawki dla oprogramowania SAP LaMa 3,0.

Łącznik platformy Azure używa interfejsu API Azure Resource Manager do zarządzania zasobami platformy Azure. Rozwiązanie SAP LaMa może używać jednostki usługi lub tożsamości zarządzanej do uwierzytelniania względem tego interfejsu API. Jeśli na maszynie wirtualnej platformy Azure jest uruchomione oprogramowanie SAP LaMa, zalecamy użycie tożsamości zarządzanej zgodnie z opisem w rozdziale [Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do interfejsu API platformy Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Jeśli chcesz użyć nazwy głównej usługi, postępuj zgodnie z instrukcjami w rozdziale [Używanie nazwy głównej usługi do uzyskiwania dostępu do interfejsu API platformy Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Uzyskiwanie dostępu do interfejsu API platformy Azure przy użyciu nazwy głównej usługi

Łącznik platformy Azure może użyć jednostki usługi do autoryzacji przed Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi dla zarządzania SAP krajobrazem (LaMa).

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok Azure Active Directory
1. Kliknij Rejestracje aplikacji
1. Kliknij pozycję Nowa rejestracja
1. Wprowadź nazwę i kliknij pozycję Zarejestruj.
1. Wybierz nową aplikację i kliknij pozycję Certyfikaty & wpisy tajne na karcie Ustawienia.
1. Utwórz nowy klucz tajny klienta, wprowadź opis nowego klucza, wybierz, kiedy wpis tajny powinien wygasnąć, i kliknij przycisk Zapisz.
1. Zapisz wartość. Służy jako hasło dla nazwy głównej usługi
1. Zapisz identyfikator aplikacji. Jest ona używana jako nazwa główna usługi

Jednostka usługi nie ma uprawnień dostępu do zasobów platformy Azure domyślnie. Aby uzyskać dostęp do nich, należy nadać uprawnienia nazwy głównej usługi.

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok grupy zasobów
1. Wybierz grupę zasobów, której chcesz użyć
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij pozycję Dodaj przypisanie roli
1. Wybierz współautor roli
1. Wprowadź nazwę utworzonej aplikacji
1. Klikanie pozycji Zapisz.
1. Powtórz kroki od 3 do 8 dla wszystkich grup zasobów, które mają być używane w oprogramowaniu SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do interfejsu API platformy Azure

Aby można było korzystać z tożsamości zarządzanej, wystąpienie SAP LaMa musi działać na maszynie wirtualnej platformy Azure, która ma tożsamość przypisaną do systemu lub użytkownika. Aby uzyskać więcej informacji o tożsamościach zarządzanych, przeczytaj artykuł [co to są tożsamości zarządzane dla zasobów platformy Azure?](../../../active-directory/managed-identities-azure-resources/overview.md) i [Skonfiguruj zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

Zarządzana tożsamość nie ma uprawnień dostępu do zasobów platformy Azure domyślnie. Musisz nadać mu uprawnienia dostępu do nich.

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok grupy zasobów
1. Wybierz grupę zasobów, której chcesz użyć
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij pozycję Dodaj > Dodaj przypisanie roli
1. Wybierz współautor roli
1. Wybierz opcję "maszyna wirtualna" dla opcji "Przypisz dostęp do"
1. Wybierz maszynę wirtualną, na której działa wystąpienie oprogramowania SAP LaMa
1. Klikanie pozycji Zapisz.
1. Powtórz kroki dla wszystkich grup zasobów, które mają być używane w oprogramowaniu SAP LaMa

W konfiguracji łącznika SAP LaMa Azure wybierz opcję "Użyj tożsamości zarządzanej", aby włączyć użycie zarządzanej tożsamości. Jeśli chcesz użyć tożsamości przypisanej do systemu, upewnij się, że pole Nazwa użytkownika pozostaw puste. Jeśli chcesz użyć tożsamości przypisanej do użytkownika, wprowadź identyfikator tożsamości przypisanej do użytkownika w polu Nazwa użytkownika.

### <a name="create-a-new-connector-in-sap-lama"></a>Tworzenie nowego łącznika w programie SAP LaMa

Otwórz witrynę sieci Web SAP LaMa i przejdź do infrastruktury. Przejdź do karty menedżerowie chmury i kliknij przycisk Dodaj. Wybierz Adapter chmury Microsoft Azure i kliknij przycisk Dalej. Wprowadź następujące informacje:

* Etykieta: wybierz nazwę wystąpienia łącznika
* Nazwa użytkownika: Identyfikator aplikacji głównej usługi lub identyfikator przypisanej do użytkownika tożsamości maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [używanie tożsamości przypisanej do systemu lub użytkownika
* Hasło: klucz główny usługi/hasło. To pole można pozostawić puste, jeśli używasz tożsamości przypisanej do systemu lub użytkownika.
* Adres URL: Zachowaj wartość domyślną `https://management.azure.com/`
* Interwał monitorowania (w sekundach): powinien wynosić co najmniej 300
* Użyj tożsamości zarządzanej: rozwiązanie SAP LaMa może używać tożsamości przypisanej do systemu lub użytkownika do uwierzytelniania w interfejsie API platformy Azure. [Aby uzyskać dostęp do interfejsu API platformy Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) w tym przewodniku, zobacz rozdział korzystanie z tożsamości zarządzanej.
* Identyfikator subskrypcji: Identyfikator subskrypcji platformy Azure
* Azure Active Directory identyfikator dzierżawy: Identyfikator dzierżawcy Active Directory
* Host serwera proxy: Nazwa hosta serwera proxy, jeśli rozwiązanie SAP LaMa wymaga serwera proxy do łączenia się z Internetem
* Port serwera proxy: port TCP serwera proxy
* Zmień typ magazynu, aby zaoszczędzić koszty: Włącz to ustawienie, jeśli karta platformy Azure powinna zmienić typ magazynu Managed Disks, aby zmniejszyć koszty, gdy dyski nie są używane. W przypadku dysków z danymi, do których odwołuje się konfiguracja wystąpienia SAP, karta zmieni typ dysku na standardowy magazyn podczas cofania przygotowania wystąpienia i powrót do oryginalnego typu magazynu podczas przygotowywania wystąpienia. Zatrzymanie maszyny wirtualnej w programie SAP LaMa spowoduje zmianę typu magazynu wszystkich dołączonych dysków, w tym dysku systemu operacyjnego do magazynu w warstwie Standardowa. Jeśli uruchomisz maszynę wirtualną w oprogramowaniu SAP LaMa, karta zmieni typ magazynu z powrotem na oryginalny typ magazynu.

Kliknij pozycję Konfiguracja testu, aby sprawdzić poprawność danych wejściowych. Powinna zostać wyświetlona

Pomyślnie nawiązano połączenie: połączenie z chmurą Microsoft Cloud zakończyło się pomyślnie. Znaleziono 7 grup zasobów (zażądano tylko 10 grup)

w dolnej części witryny sieci Web.

## <a name="provision-a-new-adaptive-sap-system"></a>Zapewnianie nowego systemu adaptacyjnego SAP

Możesz ręcznie wdrożyć nową maszynę wirtualną lub użyć jednego z szablonów platformy Azure w [repozytorium szybkiego startu](https://github.com/Azure/azure-quickstart-templates). Zawiera szablony dla serwerów [SAP NETWEAVER ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver Application Servers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)i [Database](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Za pomocą tych szablonów można także inicjować obsługę nowych hostów w ramach kopiowania/klonu systemu itp.

Zalecamy używanie oddzielnej podsieci dla wszystkich maszyn wirtualnych, które mają być zarządzane za pomocą oprogramowania SAP LaMa i nie należy używać dynamicznych adresów IP w celu uniemożliwienia adresowania IP "kradzieży" podczas wdrażania nowych maszyn wirtualnych i wystąpień oprogramowania SAP.

> [!NOTE]
> Jeśli to możliwe, Usuń wszystkie rozszerzenia maszyn wirtualnych, ponieważ mogą one powodować długotrwałe środowiska uruchomieniowe na potrzeby odłączania dysków od maszyny wirtualnej.

Upewnij się, że \<hanasid> \<sapsid> na maszynie docelowej istnieją usługi adm, adm i Group sapsys z tym samym identyfikatorem i GID lub Użyj protokołu LDAP. Włącz i uruchom serwer NFS na maszynach wirtualnych, które powinny być używane do uruchamiania SCS SAP NetWeaver (A).

### <a name="manual-deployment"></a>Wdrażanie ręczne

Program SAP LaMa komunikuje się z maszyną wirtualną przy użyciu agenta hosta SAP. Jeśli maszyny wirtualne są wdrażane ręcznie lub nie przy użyciu szablonu Azure Resource Manager z repozytorium szybkiego startu, należy zainstalować najnowszego agenta hosta SAP i rozszerzeń SAP adaptacyjnych. Aby uzyskać więcej informacji na temat wymaganych poziomów poprawek dla platformy Azure, zobacz temat SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ręczne wdrażanie maszyny wirtualnej z systemem Linux

Utwórz nową maszynę wirtualną przy użyciu jednego z obsługiwanych systemów operacyjnych wymienionych w temacie SAP Note [2343511]. Dodaj dodatkowe konfiguracje protokołu IP dla wystąpień SAP. Każde wystąpienie wymaga co najmniej adresu IP i musi być zainstalowane przy użyciu wirtualnej nazwy hosta.

Wystąpienie SAP NetWeaver ASCS wymaga dysków dla/sapmnt/ \<SAPSID> ,/usr/SAP/ \<SAPSID> ,/usr/SAP/Trans i/usr/SAP/ \<sapsid> adm. Serwery aplikacji SAP NetWeaver nie potrzebują dodatkowych dysków. Wszystkie elementy związane z wystąpieniem SAP muszą być przechowywane w ASCS i eksportowane za pośrednictwem systemu plików NFS. W przeciwnym razie nie jest możliwe dodawanie dodatkowych serwerów aplikacji przy użyciu rozwiązania SAP LaMa.

![SAP NetWeaver ASCS w systemie Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ręczne wdrożenie dla SAP HANA

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla SAP HANA, jak wymieniono w oprogramowaniu SAP Note [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla SAP HANA i jeden dla dzierżawy platformy HANA.

SAP HANA potrzebuje dysków dla/Hana/Shared,/Hana/Backup,/Hana/Data i/Hana/log

![SAP HANA w systemie Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ręczne wdrożenie dla Oracle Database w systemie Linux

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla baz danych Oracle, jak wymieniono w programie SAP Note [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla bazy danych Oracle.

Baza danych Oracle wymaga dysków dla/Oracle,/Home/oraod1 i/Home/Oracle

![Baza danych Oracle w systemie Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ręczne wdrożenie dla Microsoft SQL Server

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla Microsoft SQL Server, jak wymieniono w oprogramowaniu SAP Note [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla wystąpienia SQL Server.

Serwer bazy danych SQL Server wymaga dysków dla danych bazy danych i plików dziennika oraz dysków dla c:\usr\sap.

![Baza danych Oracle w systemie Linux](media/lama/sap-lama-db-sql.png)

Upewnij się, że na komputerze wirtualnym, który ma zostać użyty do przeniesienia serwera aplikacji SAP NetWeaver na system lub jako element docelowy kopiowania/klonowania, zainstaluj obsługiwany sterownik Microsoft ODBC dla SQL Server.

Rozwiązanie SAP LaMa nie może zmienić położenia SQL Server, więc maszyna wirtualna, która ma zostać użyta do przeniesienia wystąpienia bazy danych do lub jako miejsce docelowe kopii/klonu systemu, jest wymagana SQL Server preinstalacji.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Wdróż maszynę wirtualną przy użyciu szablonu platformy Azure

Pobierz następujące najnowsze dostępne archiwa z [portalu oprogramowania SAP](https://support.sap.com/swdc) dla systemu operacyjnego maszyn wirtualnych:

1. SAPCAR 7,21
1. AGENT HOSTA SAP 7,21
1. ROZSZERZENIE SAP ADAPTACYJNE EXTENSION 1,0

Pobierz również następujące składniki z [Centrum pobierania Microsoft](https://www.microsoft.com/download)

1. Pakiet redystrybucyjny Microsoft Visual C++ 2010 (x64) (tylko system Windows)
1. Microsoft ODBC Driver for SQL Server (tylko SQL Server)

Składniki są wymagane do wdrożenia szablonu. Najprostszym sposobem udostępniania ich dla szablonu jest przekazanie ich do konta usługi Azure Storage i utworzenie sygnatury dostępu współdzielonego (SAS).

Szablony mają następujące parametry:

* sapSystemId: identyfikator systemu SAP. Służy do tworzenia układu dysku (na przykład/usr/SAP/ \<sapsid> ).

* nazwa_komputera: Nazwa komputera nowej maszyny wirtualnej. Ten parametr jest również używany przez oprogramowanie SAP LaMa. W przypadku użycia tego szablonu do aprowizacji nowej maszyny wirtualnej w ramach kopii systemu system SAP LaMa oczekuje na osiągnięcie hosta z tą nazwą komputera.

* osType: typ systemu operacyjnego, który chcesz wdrożyć.

* DbType: typ bazy danych. Ten parametr służy do określenia, ile dodatkowych konfiguracji protokołu IP należy dodać, oraz jak powinien wyglądać układ dysku.

* sapSystemSize: rozmiar systemu SAP, który ma zostać wdrożony. Służy do określania typu i rozmiaru wystąpienia maszyny wirtualnej.

* adminUsername: Nazwa użytkownika dla maszyny wirtualnej.

* adminPassword: hasło dla maszyny wirtualnej. Możesz również podać klucz publiczny dla protokołu SSH.

* sshKeyData: publiczny klucz SSH dla maszyn wirtualnych. Obsługiwane tylko w systemach operacyjnych Linux.

* subnetId: identyfikator podsieci, której chcesz użyć.

* deployEmptyTarget: można wdrożyć pusty cel, jeśli chcesz użyć maszyny wirtualnej jako docelowej dla wystąpienia przeznaczenie lub podobne. W takim przypadku nie są dołączone żadne dodatkowe dyski ani konfiguracje adresów IP.

* sapcarLocation: Lokalizacja aplikacji SAPCAR, która odpowiada wdrażanym systemowi operacyjnemu. SAPCAR służy do wyodrębniania archiwów udostępnianych w innych parametrach.

* sapHostAgentArchiveLocation: Lokalizacja archiwum agenta hosta SAP. Agent hosta SAP jest wdrażany w ramach wdrożenia tego szablonu.

* sapacExtLocation: Lokalizacja rozszerzeń SAP adaptacyjnych. W przypadku oprogramowania SAP Uwaga [2343511] przedstawiono minimalny poziom poprawek wymagany dla platformy Azure.

* vcRedistLocation: Lokalizacja środowiska uruchomieniowego VC, które jest wymagane do zainstalowania rozszerzeń SAP adaptacyjnych. Ten parametr jest wymagany tylko w przypadku systemu Windows.

* odbcDriverLocation: Lokalizacja sterownika ODBC, który chcesz zainstalować. Obsługiwane są tylko sterowniki ODBC firmy Microsoft dla SQL Server.

* sapadmPassword: hasło użytkownika sapadm.

* sapadmId: identyfikator użytkownika systemu Linux użytkownika sapadm. Niewymagane dla systemu Windows.

* sapsysGid: Identyfikator grupy systemu Linux grupy sapsys. Niewymagane dla systemu Windows.

* _artifactsLocation: podstawowy identyfikator URI, w którym znajdują się artefakty wymagane przez ten szablon. Gdy szablon zostanie wdrożony za pomocą towarzyszących skryptów, zostanie użyta prywatna lokalizacja w subskrypcji, która zostanie wygenerowana automatycznie. Tylko wtedy, gdy szablon nie zostanie wdrożony z usługi GitHub.

* _artifactsLocationSasToken: sasToken wymagany do uzyskania dostępu do _artifactsLocation. Gdy szablon zostanie wdrożony za pomocą towarzyszących skryptów, zostanie automatycznie wygenerowany sasToken. Tylko wtedy, gdy szablon nie zostanie wdrożony z usługi GitHub.

### <a name="sap-hana"></a>SAP HANA

W poniższych przykładach przyjęto założenie, że instalujesz SAP HANA z IDENTYFIKATORem systemowym HN1 i systemem SAP NetWeaver z IDENTYFIKATORem systemowym AH1. Wirtualne nazwy hostów to hn1-DB dla wystąpienia HANA, AH1-DB dla dzierżawy HANA używanej przez system SAP NetWeaver, AH1-ASCS dla SAP NetWeaver ASCS i AH1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalowanie oprogramowania SAP NetWeaver ASCS dla SAP HANA przy użyciu usługi Azure Managed Disks

Przed uruchomieniem Menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta ASCS. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

![Logo systemu Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Logo Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Uruchom SWPM i Użyj *AH1-ASCS* dla *nazwy hosta wystąpienia ASCS*.

![Logo systemu Linux.][Logo_Linux] Linux  
Dodaj następujący parametr profilu do profilu agenta hosta SAP, który znajduje się w/usr/SAP/hostctrl/exe/host_profile. Aby uzyskać więcej informacji, zobacz temat SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Zainstaluj oprogramowanie SAP NetWeaver ASCS dla SAP HANA na platformie Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Ta funkcja jest jeszcze niedostępna. Aby uzyskać więcej informacji, zobacz temat SAP Note [2815988] (widoczne tylko dla klientów wersji zapoznawczej).
Otwórz zdarzenie SAP na składniku BC-VCM-LVM-HYPERV i zażądaj dołączenia do karty magazynu LaMa dla Azure NetApp Files wersji zapoznawczej

ANF udostępnia system plików NFS dla systemu Azure. W kontekście SAP LaMa upraszcza to Tworzenie wystąpień usług ABAP Central Services (ASCS) i kolejnej instalacji serwerów aplikacji. Wcześniej wystąpienie ASCS musiało działać jako serwer NFS oraz parametr acosprep/nfs_paths musiał zostać dodany do host_profile SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF jest obecnie dostępna w następujących regionach:

Australia Wschodnia, środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Europa Zachodnia i zachodnie stany USA 2.

#### <a name="network-requirements"></a>Wymagania dotyczące sieci

ANF wymaga delegowanej podsieci, która musi być częścią tej samej sieci wirtualnej, co serwery SAP. Oto przykład dla takiej konfiguracji.
Ten ekran pokazuje, jak utworzyć sieć wirtualną i pierwszą podsieć:

![Środowisko SAP LaMa tworzenie sieci wirtualnej dla usługi Azure ANF ](media/lama/sap-lama-createvn-50.png)

W następnym kroku zostanie utworzona delegowana podsieć dla Microsoft. NetApp/Volumes.

![Dodawanie delegowanej podsieci SAP LaMa ](media/lama/sap-lama-addsubnet-50.png)

![Lista LaMa protokołu SAP podsieci ](media/lama/sap-lama-subnets.png)

Teraz należy utworzyć konto NetApp w ramach Azure Portal:

![LaMa systemu SAP — Utwórz konto NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Utworzono konto SAP LaMa NetApp ](media/lama/sap-lama-netappaccount.png)

W ramach konta NetApp Pula pojemności określa rozmiar i typ dysków dla każdej puli:

![LaMa systemu SAP — Tworzenie puli pojemności NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Utworzono pulę pojemności SAP LaMa NetApp ](media/lama/sap-lama-capacitypool-list.png)

Można teraz definiować woluminy NFS. Ponieważ w jednej puli będą woluminy z wieloma systemami, należy wybrać schemat nazewnictwa samodzielnego. Dodanie identyfikatora SID pomaga zgrupować powiązane woluminy. W przypadku ASCS i wystąpienia as są wymagany następujące instalacje: */sapmnt/ \<SID\> *, */usr/SAP/ \<SID\> *i */Home/ \<sid\> adm*. Opcjonalnie */usr/SAP/Trans* jest wymagany dla centralnego katalogu transportowego, który jest co najmniej używany przez wszystkie systemy o jednej poziomej.

> [!NOTE]
> W fazie BETA nazwa woluminów musi być unikatowa w ramach subskrypcji.

![System SAP LaMa — Tworzenie woluminu 1 ](media/lama/sap-lama-createvolume-80.png)

![System SAP LaMa — Tworzenie woluminu 2 ](media/lama/sap-lama-createvolume2-80.png)

![System SAP LaMa — Tworzenie woluminu 3 ](media/lama/sap-lama-createvolume3-80.png)

Te kroki należy powtórzyć również dla innych woluminów.

![Lista utworzonych woluminów SAP LaMa ](media/lama/sap-lama-volumes.png)

Teraz te woluminy należy zainstalować w systemach, w których zostanie przeprowadzona początkowa instalacja z programem SAP SWPM.

Najpierw należy utworzyć punkty instalacji. W tym przypadku identyfikator SID to AN1, więc należy wykonać następujące polecenia:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Kolejne woluminy ANF zostaną zainstalowane przy użyciu następujących poleceń:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Polecenia instalacji mogą być również wyprowadzane z portalu. Lokalne punkty instalacji muszą zostać dopasowane.

Aby sprawdzić, użyj polecenia DF-h.

![Poziom systemu operacyjnego punktów instalacji SAP LaMa ](media/lama/sap-lama-mounts.png)

Teraz należy przeprowadzić instalację z SWPM.

Te same kroki należy wykonać dla co najmniej jednego wystąpienia.

Po pomyślnej instalacji systemu należy odnaleźć system w oprogramowaniu SAP LaMa.

Punkty instalacji powinny wyglądać następująco dla ASCS i wystąpienia AS:

![Punkty instalacji SAP LaMa w LaMa ](media/lama/sap-lama-ascs.png) (jest to przykład. Adresy IP i ścieżki eksportu różnią się od tych, które zostały użyte przed


#### <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

Jeśli instalujesz SAP HANA przy użyciu narzędzia wiersza polecenia hdblcm, użyj parametru--hostname, aby podać wirtualną nazwę hosta. Należy dodać adres IP wirtualnej nazwy hosta bazy danych do interfejsu sieciowego. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

Dodaj kolejną wirtualną nazwę hosta i adres IP dla nazwy, która jest używana przez serwery aplikacji do łączenia się z dzierżawcą platformy HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Uruchom instalację wystąpienia bazy danych SWPM na maszynie wirtualnej serwera aplikacji, a nie na maszynie wirtualnej HANA. Użyj *AH1-DB* dla *hosta bazy danych* w *bazie danych okna dialogowego dla systemu SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Zainstaluj serwer aplikacji SAP NetWeaver dla SAP HANA

Przed uruchomieniem Menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta serwera aplikacji. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

![Logo systemu Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Logo Windows.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Zaleca się użycie parametru profilu NetWeaver protokołu SAP baz danych/HDB/hdb_use_ident, aby ustawić tożsamość, która jest używana do znajdowania klucza w HDB userstore. Ten parametr można dodać ręcznie po zakończeniu instalacji wystąpienia bazy danych za pomocą SWPM lub SWPM z

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

W przypadku ustawienia ręcznie należy również utworzyć nowe wpisy HDB userstore.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

W *wystąpieniu podstawowego serwera aplikacji*okna dialogowego Użyj *AH1-di-0* dla *nazwy hosta wystąpienia pas* .

#### <a name="post-installation-steps-for-sap-hana"></a>Kroki po instalacji dla SAP HANA

Upewnij się, że utworzono kopię zapasową SYSTEMDB i wszystkich baz danych dzierżaw przed podjęciem próby wykonania kopii dzierżawy, przeniesieniu dzierżawy lub utworzenia replikacji systemu.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

W poniższych przykładach przyjęto założenie, że instalujesz system SAP NetWeaver z IDENTYFIKATORem systemowym AS1. Wirtualne nazwy hostów to AS1-DB dla wystąpienia SQL Server używanego przez system SAP NetWeaver, AS1-ASCS dla oprogramowania SAP NetWeaver ASCS i AS1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Zainstaluj oprogramowanie SAP NetWeaver ASCS dla SQL Server

Przed uruchomieniem Menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta ASCS. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Uruchom SWPM i Użyj *AS1-ASCS* dla *nazwy hosta wystąpienia ASCS*.

#### <a name="install-sql-server"></a>Instalacja programu SQL Server

Należy dodać adres IP wirtualnej nazwy hosta bazy danych do interfejsu sieciowego. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Uruchom instalację wystąpienia bazy danych SWPM na maszynie wirtualnej programu SQL Server. Użyj SAPINST_USE_HOSTNAME =*AS1-DB* , aby zastąpić nazwę hosta używaną do nawiązywania połączenia z SQL Server. Jeśli maszyna wirtualna została wdrożona przy użyciu szablonu Azure Resource Manager, upewnij się, że ustawisz katalog używany dla plików danych bazy danych do *C:\sql\data* i pliku dziennika bazy danych na *C:\sql\log*.

Upewnij się, że użytkownik *NT NT\SYSTEM* ma dostęp do SQL Server i ma rolę serwera *sysadmin*. Aby uzyskać więcej informacji, zobacz uwagi dotyczące oprogramowania SAP [1877727] i [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Zainstaluj serwer aplikacji SAP NetWeaver

Przed uruchomieniem Menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta serwera aplikacji. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest instalowany przy użyciu sapacext, należy ponownie zainstalować adres IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

W *wystąpieniu podstawowego serwera aplikacji*okna dialogowego Użyj *AS1-di-0* dla *nazwy hosta wystąpienia pas* .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-and-warnings-during-discover"></a>Błędy i ostrzeżenia podczas odnajdywania

* Odmowa uprawnienia SELECT
  * Programu [Sterownik ODBC SQL Server] [SQL Server] Odmowa uprawnienia SELECT w obiekcie "log_shipping_primary_databases", bazie danych "msdb", schemacie "dbo". [SOAPFaultException]  
  Odmowa uprawnienia SELECT w obiekcie "log_shipping_primary_databases", bazie danych "msdb", schemacie "dbo".
  * Rozwiązanie  
    Upewnij się, że *NT NT\SYSTEM* ma dostęp do SQL Server. Zobacz uwagi dotyczące oprogramowania SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Błędy i ostrzeżenia dotyczące walidacji wystąpienia

* Wystąpił wyjątek podczas sprawdzania poprawności HDB userstore  
  * Zobacz Podgląd dzienników  
    com. SAP. NW. lm. ACI. Monitor. API. Validation. RuntimeValidationException: wyjątek w module walidacji o IDENTYFIKATORze "RuntimeHDBConnectionValidator" (Walidacja: "VALIDATION_HDB_USERSTORE"): nie można pobrać hdbuserstore  
    Userstore HANA nie znajduje się w poprawnej lokalizacji
  * Rozwiązanie  
    Upewnij się, że/usr/sap/AH1/hdbclient/install/installation.ini jest prawidłowy

### <a name="errors-and-warnings-during-a-system-copy"></a>Błędy i ostrzeżenia podczas kopiowania systemu

* Wystąpił błąd podczas sprawdzania poprawności kroku aprowizacji systemu
  * Spowodowane przez: com. SAP. NW. lm. ACI. aparat. Base. API. util. Exception. HAOperationException wywołujący element "/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-r ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R
  * Rozwiązanie  
    Utwórz kopię zapasową wszystkich baz danych w źródłowym systemie HANA

* *Początek* wystąpienia bazy danych na etapie kopiowania systemu
  * Operacja agenta hosta "000D3A282BC91EE8A1D76CF1F92E2944" nie powiodła się (OperationException. FaultCode: ' 127 ', komunikat: ' wykonywanie polecenia nie powiodło się. : [Microsoft] [ODBC SQL Server Driver] [SQL Server] użytkownik nie ma uprawnienia do zmiany bazy danych "AS2", baza danych nie istnieje lub baza danych nie znajduje się w stanie umożliwiającym sprawdzanie dostępu ").
  * Rozwiązanie  
    Upewnij się, że *NT NT\SYSTEM* ma dostęp do SQL Server. Zobacz uwagi dotyczące oprogramowania SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Błędy i ostrzeżenia podczas klonowania systemu

* Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia w kroku *wymuszono rejestrowanie i uruchamianie agenta wystąpienia* serwera aplikacji lub ASCS
  * Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia. (RemoteException: "nie można załadować danych wystąpienia z profilu" \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ": nie można uzyskać dostępu do profilu" \\ AS1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ": brak takiego pliku lub katalogu").
  * Rozwiązanie  
   Upewnij się, że udział sapmnt na ASCS/SCS ma pełny dostęp do SAP_AS1_GlobalAdmin

* Błąd w kroku *włączania ochrony uruchamiania dla klonu*
  * Nie można otworzyć pliku " \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0" Nmożliwa Przyczyna: Brak pliku lub katalogu
  * Rozwiązanie  
    Konto komputera serwera aplikacji musi mieć dostęp do zapisu w profilu

### <a name="errors-and-warnings-during-create-system-replication"></a>Błędy i ostrzeżenia podczas tworzenia replikacji systemu

* Wyjątek podczas klikania opcji Utwórz replikację systemu
  * Spowodowane przez: com. SAP. NW. lm. ACI. aparat. Base. API. util. Exception. HAOperationException wywołujący element "/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-r ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u system-p Hook-R
  * Rozwiązanie  
    Testuj, czy sapacext można wykonać jako `<hanasid`>adm

* Błąd, gdy pełna kopia nie jest włączona w kroku magazynu
  * Wystąpił błąd podczas raportowania komunikatu atrybutu kontekstu dla ścieżki IStorageCopyData. storageVolumeCopyList: 1 i pola targetStorageSystemId
  * Rozwiązanie  
    Ignoruj ostrzeżenia w kroku i spróbuj ponownie. Ten problem zostanie rozwiązany w nowym pakiecie obsługi/poprawek oprogramowania SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Błędy i ostrzeżenia podczas Przemieść

* Ścieżka "/usr/sap/AH1" nie jest dozwolona w przypadku reeksportów NFS.
  * Aby uzyskać szczegółowe informacje, zobacz temat SAP Note [2628497] .
  * Rozwiązanie  
    Dodaj eksporty ASCS do profilu ASCS HostAgent. Zobacz uwagi dotyczące oprogramowania SAP [2628497]

* Funkcja nie została zaimplementowana podczas relokalizacji ASCS
  * Dane wyjściowe polecenia: exportfs: Host:/usr/SAP/AX1: funkcja nie została zaimplementowana
  * Rozwiązanie  
    Upewnij się, że usługa serwera NFS jest włączona na docelowej maszynie wirtualnej.

### <a name="errors-and-warnings-during-application-server-installation"></a>Błędy i ostrzeżenia podczas instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: getProfileDir
  * Błąd: (ostatni błąd zgłoszony przez krok: przechwycono ESAPinstException w wywołaniu modułu: walidator kroku "| NW_DI | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | getSid | 0 | NW_readProfileDir | IND | IND | IND | readProfile | 0 | getProfileDir ' zgłosił błąd: węzeł \\ \as1-ascs\sapmnt\AS1\SYS\profile nie istnieje. Uruchom SAPinst w trybie interaktywnym, aby rozwiązać ten problem)
  * Rozwiązanie  
    Upewnij się, że SWPM jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Ten użytkownik można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: askUnicode
  * Błąd: (ostatni błąd zgłoszony przez krok: przechwycono ESAPinstException w wywołaniu modułu: walidator kroku "| NW_DI | IND | IND | IND | 0 | 0 | NW_GetSidFromProfiles | IND | IND | IND | getSid | 0 | NW_getUnicode | IND | IND | IND | "Unicode | 0 | askUnicode" zgłosił błąd: Uruchom SAPinst w trybie interaktywnym, aby rozwiązać ten problem.
  * Rozwiązanie  
    W przypadku korzystania z najnowszego jądra SAP SWPM nie może określić, czy system jest już systemem Unicode przy użyciu serwera komunikatów ASCS. Aby uzyskać więcej informacji, zobacz temat SAP Note [2445033] .  
    Ten problem zostanie rozwiązany w nowym pakiecie obsługi/poprawek oprogramowania SAP LaMa.  
    Aby obejść ten problem, ustaw parametr profilu OS_UNICODE = UC w profilu domyślnym systemu SAP.

* Wystąpił błąd podczas wykonywania kroku SAPinst: dCheckGivenServer
  * Wystąpił błąd podczas wykonywania kroku SAPinst: dCheckGivenServer "Version =" 1.0 "ERROR: (ostatni błąd zgłoszony przez krok: \<p> Instalacja została anulowana przez użytkownika. \</p>
  * Rozwiązanie  
    Upewnij się, że SWPM jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Ten użytkownik można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: checkClient
  * Wystąpił błąd podczas wykonywania kroku SAPinst: checkClient "Version =" 1.0 "ERROR: (ostatni błąd zgłoszony przez krok: \<p> Instalacja została anulowana przez użytkownika. \</p>)
  * Rozwiązanie  
    Upewnij się, że sterownik Microsoft ODBC dla SQL Server jest zainstalowany na maszynie wirtualnej, na której chcesz zainstalować serwer aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: copyScripts
  * Ostatni błąd zgłoszony przez krok: wywołanie systemowe nie powiodło się. Szczegóły: błąd 13 (0x0000000d) (odmowa uprawnień) podczas wykonywania wywołania systemowego "fopenU" z parametrem ( \\ \ AS1-ASCS/sapmnt/AS1/sys/exe/UC/NTAMD64/strdbs. cmd, w), wiersz (494) w pliku (\ bas/bas/749_REL/bc_749_REL/src/ins/sapinst/Impl/src/syslib/FileSystem/syxxcfstrm2.cpp), ślad stosu:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodę executescript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: call ()  
  iaxxcfile. cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring const& Name, args_t const& args)  
  iaxxcfile. cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t const& _args)  
  iaxxbfile. cpp: 773: CIaOsFile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: CSyFileImpl:: openStream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ASCS/sapmnt/AW1/sys/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Rozwiązanie  
    Upewnij się, że SWPM jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Ten użytkownik można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: askPasswords
  * Ostatni błąd zgłoszony przez krok: wywołanie systemowe nie powiodło się. Szczegóły: błąd 5 (0x00000005) (odmowa dostępu). w trakcie wykonywania wywołania systemowego "NetValidatePasswordPolicy" z parametrem (...), wierszem (359) w pliku (\ bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), ślad stosu:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodę executescript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: call ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: invokeDialog ()  
  DarkModeGuiEngine. cpp: 56: DarkModeGuiEngine:: showDialogCalledByJs ()  
  DarkModeDialog. cpp: 85: DarkModeDialog:: Submit ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodę executescript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: call ()  
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring const& Name, args_t const& args)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t const& _args)  
  iaxxbaccount. cpp: 430: CIaOsAccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Rozwiązanie  
    Upewnij się, że dodano regułę hosta z *izolacją* krokową, aby umożliwić komunikację z maszyną wirtualną z kontrolerem domeny

## <a name="next-steps"></a>Następne kroki
* [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure][hana-ops-guide]
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
