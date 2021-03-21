---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — Optymalizuj chmurę prywatną CloudSimple dla programu Oracle RAC
description: Opisuje sposób wdrażania nowego klastra i optymalizowania maszyny wirtualnej pod kątem instalacji i konfiguracji klastrów (RAC) firmy Oracle
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899273"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Zoptymalizuj chmurę prywatną CloudSimple na potrzeby instalacji programu Oracle RAC

W środowisku chmury prywatnej CloudSimple można wdrażać klastry aplikacji (RAC) firmy Oracle. W tym przewodniku opisano, jak wdrożyć nowy klaster i zoptymalizować maszynę wirtualną dla rozwiązania Oracle RAC. Po wykonaniu kroków opisanych w tym temacie można zainstalować i skonfigurować certyfikat Oracle RAC.

## <a name="storage-policy"></a>Zasady magazynu

Pomyślne wdrożenie certyfikatu Oracle RAC wymaga odpowiedniej liczby węzłów w klastrze.  W zasadach magazynu sieci vSAN błędy tolerowane (FTT) są stosowane do dysków danych używanych do przechowywania danych, dzienników i ponownego wykonywania dysków.  Wymagana liczba węzłów do efektywnego tolerowania niepowodzeń to 2N + 1, gdzie N jest wartością FTT.

Przykład: Jeśli żądana FTT to 2, Łączna liczba węzłów w klastrze musi wynosić 2 * 2 + 1 = 5.

## <a name="overview-of-deployment"></a>Omówienie wdrażania

W poniższych sekcjach opisano sposób konfigurowania środowiska chmury prywatnej CloudSimple dla programu Oracle RAC.

1. Najlepsze rozwiązania dotyczące konfiguracji dysków
2. Wdróż klaster vSphere chmury prywatnej CloudSimple
3. Konfigurowanie sieci dla programu Oracle RAC
4. Skonfiguruj zasady magazynu sieci vSAN
5. Tworzenie maszyn wirtualnych Oracle i Tworzenie udostępnionych dysków maszyn wirtualnych
6. Konfigurowanie reguł koligacji maszyny wirtualnej na host

## <a name="best-practices-for-disk-configuration"></a>Najlepsze rozwiązania dotyczące konfiguracji dysków

Maszyny wirtualne z programem Oracle z certyfikatem RAC mają wiele dysków, które są używane do określonej funkcji.  Dyski udostępnione są instalowane na wszystkich maszynach wirtualnych, które są używane przez klaster Oracle z CERTYFIKATem.  Dyski instalacji systemu operacyjnego i oprogramowania są instalowane tylko na poszczególnych maszynach wirtualnych.  

![Omówienie dysków maszyn wirtualnych z programem Oracle z certyfikatem RAC](media/oracle-vm-disks-overview.png)

W poniższym przykładzie zastosowano dyski zdefiniowane w poniższej tabeli.

| Dysk                                      | Przeznaczenie                                       | Dysk udostępniony |
|-------------------------------------------|-----------------------------------------------|-------------|
| System operacyjny                                        | Dysk systemu operacyjnego                         | Nie          |
| SIATKI                                      | Lokalizacja instalacji dla oprogramowania z siatką Oracle     | Nie          |
| DATABASE                                  | Lokalizacja instalacji oprogramowania bazy danych Oracle | Nie          |
| ORAHOME                                   | Podstawowa lokalizacja dla plików binarnych bazy danych Oracle    | Nie          |
| DANE1, DANE2, DATA3, DATA4                | Dysk, na którym są przechowywane pliki bazy danych Oracle   | Tak         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Wykonaj ponownie dyski dziennika                                | Tak         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Głosowanie dysków                                  | Tak         |
| FRA1, FRA2                                | Szybkie odzyskiwanie dysków                      | Tak         |

![Konfiguracja dysku maszyny wirtualnej Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej

* Każda maszyna wirtualna jest skonfigurowana za pomocą czterech kontrolerów SCSI.
* Typ kontrolera SCSI jest ustawiony na VMware paravirtual.
* Utworzono wiele dysków wirtualnych (. vmdk).
* Dyski są instalowane na różnych kontrolerach SCSI.
* Dla udostępnionych dysków klastra ustawiono typ udostępniania wiele modułów zapisujących.
* zasady magazynu sieci vSAN są zdefiniowane w celu zapewnienia wysokiej dostępności dysków.

### <a name="operating-system-and-software-disk-configuration"></a>Konfiguracja dysku systemu operacyjnego i oprogramowania

Każda maszyna wirtualna Oracle jest skonfigurowana z wieloma dyskami dla systemu operacyjnego hosta, wymiany, instalacji oprogramowania i innych funkcji systemu operacyjnego.  Te dyski nie są współdzielone między maszynami wirtualnymi.  

* Trzy dyski dla każdej maszyny wirtualnej są skonfigurowane jako dyski wirtualne i instalowane na maszynach wirtualnych z systemem Oracle z CERTYFIKATem.
    * Dysk systemu operacyjnego
    * Dysk do przechowywania plików programu Oracle Grid
    * Dysk do przechowywania plików instalacyjnych bazy danych Oracle
* Dyski można skonfigurować jako **alokowane elastycznie**.
* Każdy dysk jest instalowany na pierwszym kontrolerze SCSI (SCSI0).  
* Udostępnianie jest ustawione na **Brak udostępniania**.
* Nadmiarowość jest definiowana w magazynie przy użyciu zasad sieci vsanymi.  

![Diagram przedstawiający konfigurację fizyczną dysku systemu operacyjnego Oracle z CERTYFIKATem.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfiguracja dysku danych

Dyski danych są głównie używane do przechowywania plików bazy danych.  

* Cztery dyski są skonfigurowane jako dyski wirtualne i instalowane na wszystkich maszynach wirtualnych z systemem Oracle z CERTYFIKATem.
* Każdy dysk jest instalowany na innym kontrolerze SCSI.
* Każdy dysk wirtualny jest skonfigurowany z **Eagerą o zerowej** alokacji.  
* Udostępnianie jest ustawione na **wiele modułów zapisujących**.  
* Dyski muszą być skonfigurowane jako Grupa dysków automatycznej zarządzania magazynem (ASM).  
* Nadmiarowość jest definiowana w magazynie przy użyciu zasad sieci vsanymi.  
* Nadmiarowość ASM jest ustawiona na nadmiarowość **zewnętrzną** .

![Konfiguracja grupy dysków danych programu Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Wykonaj ponownie konfigurację dysku dziennika

Pliki dziennika wykonaj ponownie są używane do przechowywania kopii zmian wprowadzonych w bazie danych.  Pliki dziennika są używane, gdy dane muszą zostać odzyskane po wystąpieniu błędów.

* Dyski dziennika wykonaj ponownie muszą być skonfigurowane jako wiele grup dysków.  
* Sześć dysków jest tworzonych i instalowanych na wszystkich maszynach wirtualnych z systemem Oracle z CERTYFIKATem.
* Dyski są instalowane na różnych kontrolerach SCSI
* Każdy dysk wirtualny jest skonfigurowany z **Eagerą o zerowej** alokacji.
* Udostępnianie jest ustawione na **wiele modułów zapisujących**.  
* Dyski muszą być skonfigurowane jako dwie grupy dysków ASM.
* Każda grupa dysków ASM zawiera trzy dyski, które znajdują się na różnych kontrolerach SCSI.  
* Nadmiarowość ASM jest ustawiona na **normalną** nadmiarowość.
* Pięć plików dziennika ponownego wykonywania jest tworzonych w obu dziennikach ponownego wykonywania ASM

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Konfiguracja grupy dysków dziennika ponownego wykonywania w programie Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfiguracja dysku do głosowania Oracle

Dyski głosujące zapewniają funkcjonalność dysku kworum jako dodatkowy kanał komunikacyjny, aby uniknąć sytuacji, w której jest podzielona.

* Pięć dysków jest tworzonych i instalowanych na wszystkich maszynach wirtualnych z systemem Oracle z CERTYFIKATem.
* Dyski są zainstalowane na jednym kontrolerze SCSI
* Każdy dysk wirtualny jest skonfigurowany z **Eagerą o zerowej** alokacji.
* Udostępnianie jest ustawione na **wiele modułów zapisujących**.  
* Dyski muszą być skonfigurowane jako Grupa dysków ASM.  
* Nadmiarowość ASM jest ustawiona na **wysoką** nadmiarowość.

![Konfiguracja grupy dysków głosowania programu Oracle z certyfikatem RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Konfiguracja dysku obszaru odzyskiwania Fast Oracle (opcjonalnie)

Szybki odzyskiwanie obszaru (FRA) to system plików zarządzany przez grupę dysków programu Oracle ASM.  FRA udostępnia lokalizację magazynu udostępnionego dla plików kopii zapasowej i odzyskiwania. Firma Oracle tworzy zarchiwizowane dzienniki i dzienniki Flashback w obszarze szybkiego odzyskiwania. Program Oracle Recovery Manager (RMAN) może opcjonalnie przechowywać zestawy kopii zapasowych i kopie obrazu w obszarze szybkie odzyskiwanie i używa go podczas przywracania plików podczas odzyskiwania multimediów.

* Dwa dyski są tworzone i instalowane na wszystkich maszynach wirtualnych z systemem Oracle z CERTYFIKATem.
* Dyski są zainstalowane na innym kontrolerze SCSI
* Każdy dysk wirtualny jest skonfigurowany z **Eagerą o zerowej** alokacji.
* Udostępnianie jest ustawione na **wiele modułów zapisujących**.  
* Dyski muszą być skonfigurowane jako Grupa dysków ASM.  
* Nadmiarowość ASM jest ustawiona na nadmiarowość **zewnętrzną** .

![Diagram przedstawiający konfigurację grupy dysków do głosowania z certyfikatem certyfikatu Oracle.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Wdróż klaster vSphere chmury prywatnej CloudSimple

Aby wdrożyć klaster vSphere w chmurze prywatnej, wykonaj następujące czynności:

1. W portalu CloudSimple [Utwórz chmurę prywatną](create-private-cloud.md). CloudSimple tworzy domyślnego użytkownika programu vCenter o nazwie "cloudowner" w nowo utworzonej chmurze prywatnej. Aby uzyskać szczegółowe informacje dotyczące domyślnego modelu użytkownika i dostępu chmury prywatnej, zobacz [Omówienie modelu uprawnień chmury prywatnej](learn-private-cloud-permissions.md).  Ten krok powoduje utworzenie podstawowego klastra zarządzania dla chmury prywatnej.

2. W portalu CloudSimple [Rozwiń chmurę prywatną](expand-private-cloud.md) z nowym klastrem.  Ten klaster będzie używany do wdrażania certyfikatu Oracle RAC.  Wybierz liczbę węzłów na podstawie pożądanej odporności na uszkodzenia (co najmniej trzy węzły).

## <a name="set-up-networking-for-oracle-rac"></a>Konfigurowanie sieci dla programu Oracle RAC

1. W chmurze prywatnej [Utwórz dwie sieci VLAN](create-vlan-subnet.md), jedną dla sieci publicznej Oracle i jedną dla sieci prywatnej Oracle, a następnie przypisz odpowiednie maski CIDR podsieci.
2. Po utworzeniu sieci VLAN Utwórz [rozproszone grupy portów w chmurze prywatnej vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Skonfiguruj [maszynę wirtualną z serwerem DHCP i DNS](dns-dhcp-setup.md) w klastrze zarządzania dla środowiska Oracle.
4. [Skonfiguruj przekazywanie DNS na serwerze DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) zainstalowanym w chmurze prywatnej.

## <a name="set-up-vsan-storage-policies"></a>Konfigurowanie zasad magazynu sieci vSAN

Zasady sieci vSAN definiują błędy, aby tolerować i rozkładać dysku dla danych przechowywanych na dyskach maszyn wirtualnych.  Utworzone zasady magazynu należy zastosować na dyskach maszyn wirtualnych podczas tworzenia maszyny wirtualnej.

1. [Zaloguj się do klienta vSphere](./vcenter-access.md) w chmurze prywatnej.
2. Z górnego menu wybierz pozycję **zasady i profile**.
3. Z menu po lewej stronie wybierz pozycję **zasady magazynu maszyny wirtualnej** , a następnie wybierz pozycję **Utwórz zasady magazynu maszyny wirtualnej**.
4. Wprowadź zrozumiałą nazwę zasad i kliknij przycisk **dalej**.
5. W sekcji **Struktura zasad** wybierz opcję **Włącz reguły dla magazynu sieci vSAN** , a następnie kliknij przycisk **dalej**.
6. W sekcji   >  **dostępność** sieci vSAN wybierz pozycję **Brak** dla opcji Tolerancja awarii lokacji. Aby nie można było tolerować niepowodzeń, wybierz opcję **dublowania RAID** dla żądanych FTT.
    ![Ustawienia sieci vSAN ](media/oracle-rac-storage-wizard-vsan.png) .
7. W sekcji **Zaawansowane** wybierz liczbę pasków dysków dla każdego obiektu. W przypadku rezerwacji przestrzeni obiektów zaznacz opcję **szeroka obsługa administracyjna**. Wybierz pozycję **Wyłącz sumę kontrolną obiektu**. Kliknij przycisk **dalej**.
8. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby wyświetlić listę zgodnych magazynów danych sieci vSAN, przejrzyj ustawienia i Zakończ konfigurację.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Tworzenie maszyn wirtualnych Oracle i Tworzenie udostępnionych dysków maszyn wirtualnych dla platformy Oracle

Aby utworzyć maszynę wirtualną dla programu Oracle, Sklonuj istniejącą maszynę wirtualną lub Utwórz nową.  W tej sekcji opisano, jak utworzyć nową maszynę wirtualną, a następnie sklonować ją w celu utworzenia drugiej po zainstalowaniu podstawowego systemu operacyjnego.  Po utworzeniu maszyn wirtualnych można utworzyć do nich dyski do dodania.  Klaster Oracle używa dysków udostępnionych do przechowywania, danych, dzienników i ponownego wykonywania dzienników.

### <a name="create-vms"></a>Tworzenie maszyn wirtualnych

1. W programie vCenter kliknij ikonę **hosty i klastry** . Wybierz klaster, który został utworzony dla firmy Oracle.
2. Kliknij prawym przyciskiem myszy klaster i wybierz pozycję **Nowa maszyna wirtualna**.
3. Wybierz pozycję **Utwórz nową maszynę wirtualną** , a następnie kliknij przycisk **dalej**.
4. Nazwij maszynę, wybierz lokalizację maszyny wirtualnej Oracle, a następnie kliknij przycisk **dalej**.
5. Wybierz zasób klastra, a następnie kliknij przycisk **dalej**.
6. Wybierz magazyn danych sieci vSAN dla klastra, a następnie kliknij przycisk **dalej**.
7. Zachowaj domyślny wybór zgodności z ESXi 6,5 i kliknij przycisk **dalej**.
8. Wybierz system operacyjny gościa w formacie ISO dla tworzonej maszyny wirtualnej, a następnie kliknij przycisk **dalej**.
9. Wybierz rozmiar dysku twardego, który jest wymagany do zainstalowania systemu operacyjnego.
10. Aby zainstalować aplikację na innym urządzeniu, kliknij przycisk **Dodaj nowe urządzenie**.
11. Wybierz pozycję Opcje sieciowe i Przypisz grupę portów rozproszonych utworzoną dla sieci publicznej.
12. Aby dodać kolejne interfejsy sieciowe, kliknij przycisk **Dodaj nowe urządzenie** i wybierz grupę portów rozproszonych utworzoną dla sieci prywatnej.
13. W przypadku nowych stacji DC/DVD wybierz plik ISO magazynu danych zawierający ISO dla preferowanej instalacji systemu operacyjnego. Wybierz plik, który został wcześniej przekazany do folderu obrazów ISO i templates, a następnie kliknij przycisk **OK**.
14. Przejrzyj ustawienia i kliknij przycisk **OK** , aby utworzyć nową maszynę wirtualną.
15. Włącz maszynę wirtualną. Zainstaluj system operacyjny i wszystkie wymagane aktualizacje

Po zainstalowaniu systemu operacyjnego można sklonować drugą maszynę wirtualną. Kliknij prawym przyciskiem myszy wpis maszyny wirtualnej i wybierz polecenie i Klonuj opcję.

### <a name="create-shared-disks-for-vms"></a>Tworzenie dysków udostępnionych dla maszyn wirtualnych

Firma Oracle używa udostępnionego dysku do przechowywania danych, dzienników i plików dziennika ponownego wykonywania.  Można utworzyć dysk udostępniony w programie vCenter i zainstalować go na maszynach wirtualnych.  W celu uzyskania wyższej wydajności należy umieścić dyski danych w różnych kontrolerach SCSI poniższe kroki pokazują, jak utworzyć dysk udostępniony w programie vCenter, a następnie dołączyć go do maszyny wirtualnej. Klient vCenter Flash służy do modyfikowania właściwości maszyny wirtualnej.

#### <a name="create-disks-on-the-first-vm"></a>Tworzenie dysków na pierwszej maszynie wirtualnej

1. W programie vCenter kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych Oracle i wybierz pozycję **Edytuj ustawienia**.
2. W sekcji nowe urządzenie wybierz pozycję **kontroler SCSI** , a następnie kliknij przycisk **Dodaj**.
3. W sekcji nowe urządzenie wybierz pozycję **nowy dysk twardy** , a następnie kliknij przycisk **Dodaj**.
4. Rozwiń właściwości nowego dysku twardego.
5. Określ rozmiar dysku twardego.
6. Określ zasady magazynu maszyny wirtualnej jako zasady magazynu sieci vSAN zdefiniowane wcześniej.
7. Wybierz lokalizację jako folder w magazynie danych sieci vSAN. Lokalizacja ułatwia przeglądanie i dołączanie dysków do drugiej maszyny wirtualnej.
8. W przypadku inicjowania obsługi dysku wybierz opcję **grube udostępnianie eager zero**.
9. W obszarze Udostępnianie Określ **wiele modułów zapisujących**.
10. W węźle urządzenie wirtualne wybierz nowy kontroler SCSI, który został utworzony w kroku 2.

    ![Zrzut ekranu, który wyróżnia pola, które są konieczne do utworzenia dysków na pierwszej maszynie wirtualnej.](media/oracle-rac-new-hard-disk.png)

Powtórz kroki od 2 do 10 dla wszystkich nowych dysków wymaganych dla plików bazy danych Oracle, dzienników i powtórzeń.

#### <a name="attach-disks-to-second-vm"></a>Dołącz dyski do drugiej maszyny wirtualnej

1. W programie vCenter kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych Oracle i wybierz pozycję **Edytuj ustawienia**.
2. W sekcji nowe urządzenie wybierz pozycję **kontroler SCSI** , a następnie kliknij przycisk **Dodaj**.
3. W sekcji nowe urządzenie wybierz pozycję **istniejący dysk twardy** , a następnie kliknij przycisk **Dodaj**.
4. Przejdź do lokalizacji, w której utworzono dysk dla pierwszej maszyny wirtualnej, a następnie wybierz plik VMDK.
5. Określ zasady magazynu maszyny wirtualnej jako zasady magazynu sieci vSAN zdefiniowane wcześniej.
6. W przypadku inicjowania obsługi dysku wybierz opcję **grube udostępnianie eager zero**.
7. W obszarze Udostępnianie Określ **wiele modułów zapisujących**.
8. W węźle urządzenie wirtualne wybierz nowy kontroler SCSI, który został utworzony w kroku 2.

    ![Utwórz dyski na pierwszej maszynie wirtualnej](media/oracle-rac-existing-hard-disk.png)

Powtórz kroki od 2 do 7 dla wszystkich nowych dysków wymaganych dla plików bazy danych Oracle, dzienników i powtórzeń.

## <a name="set-up-vm-host-affinity-rules"></a>Konfigurowanie reguł koligacji hosta maszyny wirtualnej

Reguły koligacji z MASZYNami wirtualnymi zapewniają, że maszyna wirtualna jest uruchamiana na żądanym hoście.  Można zdefiniować reguły w programie vCenter, aby upewnić się, że maszyna wirtualna Oracle działa na hoście z odpowiednimi zasobami i spełnia wymagania dotyczące konkretnych licencji.

1. W portalu CloudSimple należy [eskalować uprawnienia](escalate-private-cloud-privileges.md) użytkownika cloudowner.
2. Zaloguj się do klienta vSphere w chmurze prywatnej.
3. W kliencie vSphere wybierz klaster, w którym są wdrożone maszyny wirtualne Oracle, a następnie kliknij przycisk **Konfiguruj**.
4. W obszarze Konfiguracja wybierz pozycję **VM/grupy hostów**.
5. Kliknij pozycję **+** .
6. Dodaj grupę maszyn wirtualnych. Wybierz **grupę maszyn wirtualnych** jako typ. Wprowadź nazwę grupy. Wybierz Maszyny wirtualne, a następnie kliknij przycisk **OK** , aby utworzyć grupę.
6. Dodaj grupę hostów. Wybierz **grupę hostów** jako typ. Wprowadź nazwę grupy. Wybierz hosty, na których będą uruchamiane maszyny wirtualne, a następnie kliknij przycisk **OK** , aby utworzyć grupę.
7. Aby utworzyć regułę, kliknij pozycję **maszyny wirtualne/reguły hosta**.
8. Kliknij pozycję **+** .
9. Wprowadź nazwę reguły i zaznacz pole wyboru **Włącz**.
10. W polu Typ reguły wybierz pozycję **Virtual Machines do hosta**.
11. Wybierz grupę maszyn wirtualnych, która zawiera maszyny wirtualne Oracle.
12. Wybierz opcję **należy uruchomić na hostach w tej grupie**.
13. Wybierz utworzoną grupę hostów.
14. Kliknij przycisk **OK**, aby utworzyć regułę.

## <a name="references"></a>Odwołania

* [Informacje o zasadach sieci vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atrybut wieloskładnikowego programu VMware dla udostępnionych dysków VMDK](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
