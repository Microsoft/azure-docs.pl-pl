---
title: Rozwiązanie Azure VMware firmy CloudSimple — optymalizacja chmury prywatnej CloudSimple dla oracle rac
description: W tym artykule opisano sposób wdrażania nowego klastra i optymalizacji maszyny Wirtualnej dla instalacji i konfiguracji klastrów rzeczywistych aplikacji Oracle (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868074"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optymalizacja chmury prywatnej CloudSimple pod kątem instalacji oracle RAC

Klastry rzeczywistych aplikacji Oracle (RAC) można wdrożyć w środowisku cloudsimple private cloud. W tym przewodniku opisano sposób wdrażania nowego klastra i optymalizacji maszyny Wirtualnej dla rozwiązania Oracle RAC. Po wykonaniu czynności w tym temacie można zainstalować i skonfigurować oracle rac.

## <a name="storage-policy"></a>Zasady przechowywania danych

Pomyślne wdrożenie oracle rac wymaga odpowiedniej liczby węzłów w klastrze.  W zasadach magazynu vSAN błędy tolerowania (FTT) są stosowane do dysków danych używanych do przechowywania dysków bazy danych, dziennika i ponawiania.  Wymagana liczba węzłów, aby skutecznie tolerować awarie jest 2N + 1, gdzie N jest wartością FTT.

Przykład: Jeśli żądany PTF wynosi 2, całkowita liczba węzłów w klastrze musi wynosić 2 *2+1 = 5.

## <a name="overview-of-deployment"></a>Omówienie wdrażania

W poniższych sekcjach opisano sposób konfigurowania środowiska CloudSimple Private Cloud dla środowiska Oracle RAC.

1. Najważniejsze wskazówki dotyczące konfiguracji dysku
2. Wdrażanie klastra vSphere chmury prywatnej CloudSimple
3. Konfigurowanie sieci dla oracle RAC
4. Konfigurowanie zasad przechowywania vSAN
5. Tworzenie maszyn wirtualnych Oracle i tworzenie udostępnionych dysków maszyn wirtualnych
6. Konfigurowanie reguł koligacji maszyn wirtualnych do hosta

## <a name="best-practices-for-disk-configuration"></a>Najważniejsze wskazówki dotyczące konfiguracji dysku

Maszyny wirtualne Oracle RAC mają wiele dysków, które są używane do określonych funkcji.  Dyski współużytkowane są montowane na wszystkich maszynach wirtualnych, które są używane przez Oracle RAC Cluster.  Dyski instalacyjne systemu operacyjnego i oprogramowania są instalowane tylko na poszczególnych maszynach wirtualnych.  

![Omówienie dysków maszyn wirtualnych Oracle RAC](media/oracle-vm-disks-overview.png)

W poniższym przykładzie użyto dysków zdefiniowanych w poniższej tabeli.

| Dysk                                      | Przeznaczenie                                       | Dysk udostępniony |
|-------------------------------------------|-----------------------------------------------|-------------|
| System operacyjny                                        | Dysk systemu operacyjnego                         | Nie          |
| Siatki                                      | Instalowanie lokalizacji oprogramowania Oracle Grid     | Nie          |
| Bazy danych                                  | Instalowanie aplikacji lokalizacji dla oprogramowania bazy danych Oracle | Nie          |
| ORAHOME (DOM)                                   | Lokalizacja podstawowa dla plików binarnych bazy danych Oracle    | Nie          |
| DATA1, DATA2, DATA3, DATA4                | Dysk, na którym przechowywane są pliki bazy danych Oracle   | Yes         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Ponawianie dysków dziennika                                | Yes         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Dyski do głosowania                                  | Yes         |
| FRA1, FRA2                                | Dyski obszarowe szybkiego odzyskiwania                      | Yes         |

![Konfiguracja dysku maszyny wirtualnej Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej

* Każda maszyna wirtualna jest skonfigurowana z czterema kontrolerami SCSI.
* Typ kontrolera SCSI jest ustawiony na VMware paravirtual.
* Tworzonych jest wiele dysków wirtualnych (vmdk).
* Dyski są montowane na różnych kontrolerach SCSI.
* Typ udostępniania wielu modułów zapisu jest ustawiany dla udostępnionych dysków klastrowych.
* Zasady pamięci masowej vSAN są zdefiniowane w celu zapewnienia wysokiej dostępności dysków.

### <a name="operating-system-and-software-disk-configuration"></a>Konfiguracja systemu operacyjnego i dysku programowego

Każda maszyna wirtualna Oracle jest skonfigurowana z wieloma dyskami dla systemu operacyjnego hosta, wymiany, instalacji oprogramowania i innych funkcji systemu operacyjnego.  Dyski te nie są współużytkowane przez maszyny wirtualne.  

* Trzy dyski dla każdej maszyny wirtualnej są skonfigurowane jako dyski wirtualne i zainstalowane na maszynach wirtualnych Oracle RAC.
    * Dysk systemu operacyjnego
    * Dysk do przechowywania plików Oracle Grid
    * Dysk do przechowywania plików zainstalowanych bazy danych Oracle
* Dyski można skonfigurować jako **cienkie aprowizacji**.
* Każdy dysk jest zamontowany na pierwszym kontrolerze SCSI (SCSI0).  
* Udostępnianie jest ustawione na **Brak udostępniania**.
* Nadmiarowość jest definiowana w magazynie przy użyciu zasad vSAN.  

![Konfiguracja grupy dysków danych Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfiguracja dysku danych

Dyski danych są używane głównie do przechowywania plików bazy danych.  

* Cztery dyski są skonfigurowane jako dyski wirtualne i zainstalowane na wszystkich maszynach wirtualnych Oracle RAC.
* Każdy dysk jest zamontowany na innym kontrolerze SCSI.
* Każdy dysk wirtualny jest skonfigurowany jako **gruby program Provision Eager Wyzerowany**.  
* Udostępnianie jest ustawione na **Multi-writer**.  
* Dyski muszą być skonfigurowane jako grupa dysków ASM (Automatic Storage Management).  
* Nadmiarowość jest definiowana w magazynie przy użyciu zasad vSAN.  
* Nadmiarowość ASM jest ustawiona na nadmiarowość **zewnętrzna.**

![Konfiguracja grupy dysków danych Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Ponawianie konfiguracji dysku dziennika

Ponów pliki dziennika są używane do przechowywania kopii zmian wprowadzonych w bazie danych.  Pliki dziennika są używane, gdy dane muszą zostać odzyskane po wszelkich błędach.

* Ponownie wykonaj dyski dziennika muszą być skonfigurowane jako wiele grup dysków.  
* Sześć dysków jest tworzonych i montowanych na wszystkich maszynach wirtualnych Oracle RAC.
* Dyski są montowane na różnych kontrolerach SCSI
* Każdy dysk wirtualny jest skonfigurowany jako **gruby program Provision Eager Wyzerowany**.
* Udostępnianie jest ustawione na **Multi-writer**.  
* Dyski muszą być skonfigurowane jako dwie grupy dysków ASM.
* Każda grupa dysków ASM zawiera trzy dyski, które znajdują się na różnych kontrolerach SCSI.  
* Nadmiarowość ASM jest ustawiona na **normalną** nadmiarowość.
* Pięć plików dziennika ponawianie jest tworzonych zarówno w grupie dziennika ASM Redo

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

![Oracle RAC ponawia konfigurację grupy dysków dziennika](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfiguracja dysku do głosowania Oracle

Dyski do głosowania zapewniają funkcjonalność dysku kworum jako dodatkowy kanał komunikacji, aby uniknąć sytuacji podzielonego mózgu.

* Pięć dysków jest tworzonych i montowanych na wszystkich maszynach wirtualnych Oracle RAC.
* Dyski są montowane na jednym kontrolerze SCSI
* Każdy dysk wirtualny jest skonfigurowany jako **gruby program Provision Eager Wyzerowany**.
* Udostępnianie jest ustawione na **Multi-writer**.  
* Dyski muszą być skonfigurowane jako grupa dysków ASM.  
* Nadmiarowość ASM jest ustawiona na **wysoką** redundancję.

![Konfiguracja grupy dysków do głosowania Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle szybka konfiguracja dysku obszaru odzyskiwania (opcjonalnie)

Obszar szybkiego odzyskiwania (FRA) to system plików zarządzany przez grupę dysków Oracle ASM.  FRA zapewnia udostępnioną lokalizację przechowywania plików kopii zapasowych i odzyskiwania. Oracle tworzy zarchiwizowane dzienniki i dzienniki retrospekcji w obszarze szybkiego odzyskiwania. Oracle Recovery Manager (RMAN) może opcjonalnie przechowywać swoje zestawy kopii zapasowych i kopie obrazów w obszarze szybkiego odzyskiwania i używa go podczas przywracania plików podczas odzyskiwania multimediów.

* Dwa dyski są tworzone i montowane na wszystkich maszynach wirtualnych Oracle RAC.
* Dyski są montowane na różnych kontrolerach SCSI
* Każdy dysk wirtualny jest skonfigurowany jako **gruby program Provision Eager Wyzerowany**.
* Udostępnianie jest ustawione na **Multi-writer**.  
* Dyski muszą być skonfigurowane jako grupa dysków ASM.  
* Nadmiarowość ASM jest ustawiona na nadmiarowość **zewnętrzna.**

![Konfiguracja grupy dysków do głosowania Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Wdrażanie klastra cloudsimple private cloud vsphere

Aby wdrożyć klaster vSphere w chmurze prywatnej, wykonaj ten proces:

1. Z portalu CloudSimple [utwórz chmurę prywatną](create-private-cloud.md). CloudSimple tworzy domyślnego użytkownika vCenter o nazwie "cloudowner" w nowo utworzonej chmury prywatnej. Aby uzyskać szczegółowe informacje na temat domyślnego modelu uprawnień użytkownika i uprawnienia private cloud, zobacz [Poznaj model uprawnień private cloud](learn-private-cloud-permissions.md).  Ten krok tworzy podstawowy klaster zarządzania dla chmury prywatnej.

2. Z portalu CloudSimple [rozwiń chmurę prywatną](expand-private-cloud.md) o nowy klaster.  Ten klaster będzie używany do wdrażania oracle rac.  Wybierz liczbę węzłów na podstawie żądanej odporności na uszkodzenia (minimum trzy węzły).

## <a name="set-up-networking-for-oracle-rac"></a>Konfigurowanie sieci dla oracle RAC

1. W chmurze prywatnej [utwórz dwie sieci VLAN](create-vlan-subnet.md), jedną dla sieci publicznej Oracle i jedną dla prywatnej sieci Oracle i przypisz odpowiednie tefry podsieci.
2. Po utworzeniu sieci VLAN należy utworzyć [rozproszone grupy portów w centrum vCenter private cloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Skonfiguruj [maszynę wirtualną serwera DHCP i serwera DNS](dns-dhcp-setup.md) w klastrze zarządzania dla środowiska Oracle.
4. [Skonfiguruj przekazywanie dns na serwerze DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) zainstalowanym w chmurze prywatnej.

## <a name="set-up-vsan-storage-policies"></a>Konfigurowanie zasad magazynowania vSAN

Zasady vSAN definiują błędy tolerowania i rozkładanie dysków dla danych przechowywanych na dyskach maszyn wirtualnych.  Utworzone zasady magazynowania muszą być stosowane na dyskach maszyn wirtualnych podczas tworzenia maszyny Wirtualnej.

1. [Zaloguj się do klienta vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access) w chmurze prywatnej.
2. W górnym menu wybierz pozycję **Zasady i profile**.
3. Z lewego menu wybierz pozycję **Zasady magazynowania maszyn wirtualnych,** a następnie wybierz pozycję **Utwórz zasady magazynowania maszyn wirtualnych**.
4. Wprowadź znaczącą nazwę zasad i kliknij przycisk **DALEJ**.
5. W sekcji **Struktura zasad** wybierz pozycję **Włącz reguły dla magazynu vSAN** i kliknij przycisk **DALEJ**.
6. W sekcji**Dostępność** **vSAN** > wybierz opcję **Brak** dla tolerancji awarii lokacji. Aby nie tolerować błędów, wybierz opcję **RAID - Mirroring** dla żądanego PTF.
    ![ustawienia](media/oracle-rac-storage-wizard-vsan.png)vSAN .
7. W sekcji **Zaawansowane** wybierz liczbę pasków dysku na obiekt. W obszarze Rezerwacja miejsca obiekt wybierz opcję **Gruba aprowizowana**. Wybierz **pozycję Wyłącz sumę kontrolną obiektu**. Kliknij **przycisk DALEJ**.
8. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby wyświetlić listę zgodnych magazynów danych vSAN, przejrzeć ustawienia i zakończyć konfigurację.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Tworzenie maszyn wirtualnych Oracle i tworzenie udostępnionych dysków maszyn wirtualnych dla Oracle

Aby utworzyć maszynę wirtualną dla Oracle, sklonuj istniejącą maszynę wirtualną lub utwórz nową.  W tej sekcji opisano sposób tworzenia nowej maszyny Wirtualnej, a następnie sklonować ją, aby utworzyć drugą po zainstalowaniu podstawowego systemu operacyjnego.  Po utworzeniu maszyn wirtualnych można utworzyć do nich dyski dodawania.  Klaster Oracle używa dysków udostępnionych do przechowywania, przechowywania danych, dzienników i dzienników ponawianych.

### <a name="create-vms"></a>Tworzenie maszyn wirtualnych

1. W vCenter kliknij ikonę **Hosty i klastry.** Wybierz klaster utworzony dla oracle.
2. Kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Nowa maszyna wirtualna**.
3. Wybierz **pozycję Utwórz nową maszynę wirtualną** i kliknij przycisk **Dalej**.
4. Nazwij urządzenie, wybierz lokalizację maszyny Wirtualnej Oracle i kliknij przycisk **Dalej**.
5. Zaznacz zasób klastra i kliknij przycisk **Dalej**.
6. Wybierz magazyn danych vSAN dla klastra i kliknij przycisk **Dalej**.
7. Zachowaj domyślny wybór zgodności ESXi 6.5 i kliknij przycisk **Dalej**.
8. Wybierz system operacyjny gościa iso dla maszyny Wirtualnej, którą tworzysz i kliknij przycisk **Dalej**.
9. Wybierz rozmiar dysku twardego wymagany do zainstalowania systemu operacyjnego.
10. Aby zainstalować aplikację na innym urządzeniu, kliknij pozycję **Dodaj nowe urządzenie**.
11. Wybierz opcje sieci i przypisz rozproszoną grupę portów utworzoną dla sieci publicznej.
12. Aby dodać dodatkowe interfejsy sieciowe, kliknij pozycję **Dodaj nowe urządzenie** i wybierz rozproszoną grupę portów utworzoną dla sieci prywatnej.
13. W przypadku nowego napędu DC/DVD wybierz plik ISO magazynu danych zawierający iso dla preferowanej instalacji systemu operacyjnego. Wybierz plik, który wcześniej został przesłany do folderu ISO i szablony, a następnie kliknij przycisk **OK**.
14. Przejrzyj ustawienia i kliknij przycisk **OK,** aby utworzyć nową maszynę wirtualną.
15. Zasilanie maszyny Wirtualnej. Zainstaluj system operacyjny i wszelkie wymagane aktualizacje

Po zainstalowaniu systemu operacyjnego można sklonować drugą maszynę wirtualną. Kliknij prawym przyciskiem myszy wpis maszyny Wirtualnej i wybierz opcję i sklonuj.

### <a name="create-shared-disks-for-vms"></a>Tworzenie dysków udostępnionych dla maszyn wirtualnych

Oracle używa udostępnionego dysku do przechowywania plików dziennika danych, dziennika i ponawiania.  Można utworzyć dysk udostępniony na vCenter i zainstalować go na obu maszynach wirtualnych.  Aby uzyskać wyższą wydajność, umieść dyski danych na różnych kontrolerach SCSI Poniżej pokazano, jak utworzyć dysk udostępniony w centrum vCenter, a następnie dołączyć go do maszyny wirtualnej. VCenter Flash klient służy do modyfikowania właściwości maszyny Wirtualnej.

#### <a name="create-disks-on-the-first-vm"></a>Tworzenie dysków na pierwszej maszynie wirtualnej

1. W programie vCenter kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych Oracle i wybierz polecenie **Edytuj ustawienia**.
2. W sekcji nowe urządzenie wybierz **kontroler SCSI** i kliknij przycisk **Dodaj**.
3. W sekcji Nowe urządzenie wybierz pozycję **Nowy dysk twardy** i kliknij przycisk **Dodaj**.
4. Rozwiń właściwości nowego dysku twardego.
5. Określ rozmiar dysku twardego.
6. Określ zasady magazynu maszyn wirtualnych jako zasady magazynu vSAN zdefiniowane wcześniej.
7. Wybierz lokalizację jako folder w magazynie danych vSAN. Lokalizacja pomaga w przeglądaniu i dołączaniu dysków do drugiej maszyny Wirtualnej.
8. W przypadku inicjowania obsługi administracyjnej dysku wybierz opcję **Gruba aprowiz owanie chętnie wyzerowana**.
9. Aby udostępnić, określ **multi-writer**.
10. W przypadku węzła urządzenia wirtualnego wybierz nowy kontroler SCSI utworzony w kroku 2.

    ![Tworzenie dysków na pierwszej maszynie wirtualnej](media/oracle-rac-new-hard-disk.png)

Powtórz kroki 2 – 10 dla wszystkich nowych dysków wymaganych dla danych Oracle, dzienników i ponownie plików dziennika.

#### <a name="attach-disks-to-second-vm"></a>Dołączanie dysków do drugiej maszyny Wirtualnej

1. W programie vCenter kliknij prawym przyciskiem myszy jedną z maszyn wirtualnych Oracle i wybierz polecenie **Edytuj ustawienia**.
2. W sekcji nowe urządzenie wybierz **kontroler SCSI** i kliknij przycisk **Dodaj**.
3. W sekcji nowe urządzenie wybierz **pozycję Istniejący dysk twardy** i kliknij przycisk **Dodaj**.
4. Przejdź do lokalizacji, w której utworzono dysk dla pierwszej maszyny Wirtualnej i wybierz plik VMDK.
5. Określ zasady magazynu maszyn wirtualnych jako zasady magazynu vSAN zdefiniowane wcześniej.
6. W przypadku inicjowania obsługi administracyjnej dysku wybierz opcję **Gruba aprowiz owanie chętnie wyzerowana**.
7. Aby udostępnić, określ **multi-writer**.
8. W przypadku węzła urządzenia wirtualnego wybierz nowy kontroler SCSI utworzony w kroku 2.

    ![Tworzenie dysków na pierwszej maszynie wirtualnej](media/oracle-rac-existing-hard-disk.png)

Powtórz kroki 2 – 7 dla wszystkich nowych dysków wymaganych dla danych Oracle, dzienników i ponownie plików dziennika.

## <a name="set-up-vm-host-affinity-rules"></a>Konfigurowanie reguł koligacji hosta maszyny Wirtualnej

Reguły koligacji maszyn wirtualnych do hosta zapewniają, że maszyna wirtualna działa na żądanym hoście.  Można zdefiniować reguły vCenter, aby upewnić się, że maszyna wirtualna Oracle działa na hoście z odpowiednimi zasobami i spełnia wszelkie określone wymagania licencyjne.

1. W portalu CloudSimple [eskalacji uprawnień](escalate-private-cloud-privileges.md) użytkownika cloudowner.
2. [Zaloguj się do klienta vSphere](https://docs.azure.cloudsimple.com/vsphere-access) w chmurze prywatnej.
3. W kliencie vSphere wybierz klaster, w którym są wdrażane maszyny Wirtualne Oracle, a następnie kliknij przycisk **Konfiguruj**.
4. W obszarze Konfiguruj wybierz pozycję **VM/Host Groups**.
5. Kliknij **+** przycisk .
6. Dodawanie grupy maszyn wirtualnych. Wybierz **grupę maszyn wirtualnych** jako typ. Wprowadź nazwę grupy. Wybierz maszyny wirtualne, a następnie kliknij przycisk **OK,** aby utworzyć grupę.
6. Dodawanie grupy hostów. Wybierz jako typ **pozycję Grupa hostów.** Wprowadź nazwę grupy. Wybierz hosty, na których będą uruchamiane maszyny wirtualne, a następnie kliknij przycisk **OK,** aby utworzyć grupę.
7. Aby utworzyć regułę, kliknij **reguły maszyny Wirtualnej/Hosta**.
8. Kliknij **+** przycisk .
9. Wprowadź nazwę reguły i zaznacz opcję **Włącz**.
10. Dla typu reguły wybierz pozycję **Maszyny wirtualne do hostowania**.
11. Wybierz grupę maszyn wirtualnych zawierającą maszyny wirtualne Oracle.
12. Wybierz **pozycję Musi być uruchamiany na hostach w tej grupie**.
13. Wybierz utworzoną grupę hostów.
14. Kliknij przycisk **OK**, aby utworzyć regułę.

## <a name="references"></a>Dokumentacja

* [Informacje o zasadach vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware Multi-Writer Atrybut dla udostępnionych VMDKs](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
