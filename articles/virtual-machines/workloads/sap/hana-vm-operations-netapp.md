---
title: SAP HANA konfiguracja ANF maszyny wirtualnej platformy Azure | Microsoft Docs
description: Azure NetApp Files zalecenia dotyczące magazynu SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, migawka
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666616"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA

Azure NetApp Files zapewnia natywne udziały NFS, które mogą być używane dla woluminów **/Hana/Shared**, **/Hana/Data** i **/Hana/log** . Używanie udziałów NFS opartych na ANF dla woluminów **/Hana/Data** i **/Hana/log** wymaga użycia protokołu NFS w wersji 4.1. Protokół NFS v3 nie jest obsługiwany w przypadku używania woluminów **/Hana/Data** i **/Hana/log** w przypadku, gdy udziały są oparte na ANF. 


> [!IMPORTANT]
> Protokół NFS v3 zaimplementowany w Azure NetApp Files **nie** jest obsługiwany do użycia dla **/Hana/Data** i **/Hana/log**. Użycie systemu plików NFS 4,1 jest obowiązkowe dla woluminów **/Hana/Data** i **/Hana/log** z punktu widzenia funkcjonalnego. W przypadku woluminu **/Hana/Shared** można użyć systemu NFS v3 lub protokołu NFS v 4.1 z punktu widzenia funkcjonalnego.

## <a name="important-considerations"></a>Istotne zagadnienia

Rozważając Azure NetApp Files dla oprogramowania SAP NetWeaver i SAP HANA, należy pamiętać o następujących kwestiach:

- Minimalna Pula pojemności to 4 TiB  
- Minimalny rozmiar woluminu to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których są zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie
- Ważne jest, aby maszyny wirtualne wdrożone w pobliżu usługi Azure NetApp Storage w małych opóźnieniach.  
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files
- Upewnij się, że opóźnienie z serwera bazy danych do woluminu ANF jest mierzone i poniżej 1 milisekundy
- Przepływność woluminu NetApp Azure to funkcja limitu przydziału woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Podczas ustalania wielkości woluminów platformy Azure NetApp na platformie HANA należy upewnić się, że przepływność spełnia wymagania systemowe HANA
- Spróbuj "skonsolidować" woluminy, aby uzyskać większą wydajność w większym woluminie, na przykład użyj jednego woluminu dla/sapmnt,/usr/SAP/Trans,... Jeśli to możliwe  
- Azure NetApp Files oferuje [zasady eksportowania](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.   
- Identyfikator użytkownika dla identyfikatora <b>SID</b>adm i identyfikator grupy dla `sapsys` maszyn wirtualnych muszą być zgodne z konfiguracją w Azure NetApp Files. 

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia są krytyczne. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości.  

> [!IMPORTANT]
> Jeśli istnieje niezgodność między IDENTYFIKATORem użytkownika dla identyfikatora <b>SID</b>adm a identyfikatorem grupy `sapsys` między maszyną wirtualną a konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach NetApp platformy Azure zainstalowane na maszynie wirtualnej byłyby wyświetlane jako `nobody` . Upewnij się, że określisz prawidłowy identyfikator użytkownika dla <b>identyfikatora SID</b>adm i identyfikator grupy dla `sapsys` , podczas gdy [Nowy system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ma Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ustalanie wielkości dla bazy danych HANA na Azure NetApp Files

Przepływność woluminu NetApp Azure to funkcja rozmiaru woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Ważne jest, aby zrozumieć, że wydajność jest zależna od rozmiaru i że istnieją fizyczne limity dla LIF (interfejs logiczny) SVM (maszyna wirtualna magazynu).

W poniższej tabeli pokazano, że warto utworzyć duży wolumin "standardowy" do przechowywania kopii zapasowych i nie ma sensu tworzenia woluminu "Ultra" większego niż 12 TB, ponieważ pojemność przepustowości fizycznej jednego LIFu zostanie przekroczona. 

Maksymalna przepływność dla LIF i jednej sesji systemu Linux wynosi od 1,2 do 1,4 GB/s. Jeśli jest wymagana większa przepływność dla/Hana/Data, można użyć partycjonowania woluminów danych SAP HANA do rozdzielenia działania we/wy podczas ponownego ładowania danych lub platformy HANA punktów zapisu w wielu plikach danych platformy HANA, które znajdują się w wielu udziałach NFS. Aby uzyskać więcej informacji na temat rozłożenia woluminu danych platformy HANA, przeczytaj następujące artykuły:

- [Podręcznik administratora platformy HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog o SAP HANA — partycjonowanie woluminów danych](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [#2400005 uwagi SAP](https://launchpad.support.sap.com/#/notes/2400005)
- [#2700123 uwagi SAP](https://launchpad.support.sap.com/#/notes/2700123)


| Rozmiar  | Standard przepływności | Przepływność Premium | Przepływność Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/s | 64 MB/s | 128 MB/s |
| 2 TB | 32 MB/s | 128 MB/s | 256 MB/s |
| 4 TB | 64 MB/s | 256 MB/s | 512 MB/s |
| 10 TB | 160 MB/s | 640 MB/s | 1 280 MB/s |
| 15 TB | 240 MB/s | 960 MB/s | 1 400 MB/s |
| 20 TB | 320 MB/s | 1 280 MB/s | 1 400 MB/s |
| 40 TB | 640 MB/s | 1 400 MB/s | 1 400 MB/s |

Ważne jest, aby zrozumieć, że dane są zapisywane w tym samym dysków SSD w zapleczu magazynu. Przydział wydajności z puli pojemności został utworzony, aby można było zarządzać środowiskiem.
Wskaźniki KPI magazynu są równe dla wszystkich rozmiarów baz danych platformy HANA. W prawie wszystkich przypadkach to założenie nie odzwierciedla rzeczywistości i oczekiwanego przez klienta. Rozmiar systemów HANA nie musi oznaczać, że mały system wymaga niskiej przepływności magazynu, a duży system wymaga dużej przepływności magazynu. Ogólnie rzecz biorąc, możemy oczekiwać wyższego poziomu przepływności dla większych wystąpień bazy danych HANA. W wyniku reguł ustalania rozmiaru dla danego sprzętu, takich jak większe wystąpienia platformy HANA, zapewnia również więcej zasobów procesora CPU i wyższą równoległość w zadaniach, takich jak ładowanie danych po ponownym uruchomieniu wystąpień. W związku z tym rozmiary woluminów należy przyjąć w zależności od oczekiwań klientów i wymagań. I nie tylko przez czyste wymagania dotyczące pojemności.

Podczas projektowania infrastruktury dla oprogramowania SAP na platformie Azure należy pamiętać o minimalnych wymaganiach dotyczących przepływności magazynu (w przypadku systemów produkcji) przez SAP, które przekładają się na minimalne charakterystyki przepływności:

| Typ woluminu i typ we/wy | Minimalny wskaźnik KPI wymagany przez SAP | Poziom usług premium | Poziom Ultra usługi |
| --- | --- | --- | --- |
| Zapis woluminu dziennika | 250 MB/s | 4 TB | 2 TB |
| Zapis woluminu danych | 250 MB/s | 4 TB | 2 TB |
| Odczyt woluminu danych | 400 MB/s | 6,3 TB | 3,2 TB |

Ze względu na to, że są wymagane wszystkie trzy kluczowe wskaźniki wydajności, wolumin **/Hana/Data** musi mieć rozmiar w kierunku większej pojemności, aby spełnić minimalne wymagania dotyczące odczytu.

W przypadku systemów HANA, które nie wymagają dużej przepustowości, rozmiary woluminów ANF mogą być mniejsze. A w przypadku, gdy system HANA wymaga większej przepływności, można dostosować wolumin, zmieniając rozmiar pojemności do trybu online. Nie zdefiniowano kluczowych wskaźników wydajności dla woluminów kopii zapasowych. Jednak przepływność woluminu kopii zapasowej jest istotna dla dobrze wykonywanego środowiska. Dziennik — wydajność woluminu danych musi być zaprojektowana pod kątem oczekiwań klientów.

> [!IMPORTANT]
> Niezależna od pojemności wdrożonej na pojedynczym woluminie systemu plików NFS przepływność oczekuje na Plateau w zakresie przepustowości 1,2-1.4 GB/s wykorzystanej przez odbiorcę na maszynie wirtualnej. Należy to zrobić z podstawową architekturą oferty ANF i powiązanymi limitami sesji systemu Linux w systemie plików NFS. Numery wydajności i przepływności zgodnie z opisem w artykule [wyniki testów porównawczych wydajności artykułu dla Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) zostały wykonane na jednym udostępnionym woluminie NFS z wieloma maszynami wirtualnymi klienta i w wyniku wielu sesji. Ten scenariusz jest różny dla scenariusza, który mierzę w oprogramowaniu SAP. Gdzie mierzy przepływność z pojedynczej maszyny wirtualnej do woluminu systemu plików NFS. Hostowane w witrynie ANF.

W celu spełnienia minimalnych wymagań dotyczących przepływności oprogramowania SAP dla danych i dziennika oraz zgodnie z wytycznymi dla **/Hana/Shared** zalecaną wielkością będzie wyglądać:

| Wolumin | Rozmiar<br /> Warstwa Premium Storage | Rozmiar<br /> Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| skalowanie w górę/Hana/Shared | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | v3 lub v 4.1 |
| skalowanie w poziomie/Hana/Shared | 1 x MB pamięci RAM węzła procesu roboczego<br /> na 4 węzły procesu roboczego  | 1 x MB pamięci RAM węzła procesu roboczego<br /> na 4 węzły procesu roboczego  | v3 lub v 4.1 |
| /hana/logbackup | 3 x pamięci RAM  | 3 x pamięci RAM | v3 lub v 4.1 |
| /hana/backup | 2 x pamięci RAM  | 2 x pamięci RAM | v3 lub v 4.1 |

W przypadku wszystkich woluminów system plików NFS w wersji 4.1 jest zdecydowanie zalecany

Rozmiary woluminów kopii zapasowych są szacowane. Dokładne wymagania muszą być zdefiniowane w zależności od obciążenia i procesów operacyjnych. W przypadku kopii zapasowych można skonsolidować wiele woluminów dla różnych wystąpień SAP HANA na jeden (lub dwa) większe woluminy, które mogą mieć niższy poziom usług ANF.

> [!NOTE]
> Azure NetApp Files, zalecenia dotyczące ustalania wielkości podane w tym dokumencie są zgodne z minimalnymi wymaganiami SAP Express do ich dostawców infrastruktury. W rzeczywistych scenariuszach wdrażania klientów i obciążeń, które mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i adaptacji na podstawie wymagań określonego obciążenia.  

W związku z tym można rozważyć wdrożenie podobnej przepływności dla woluminów ANF, tak jak w przypadku magazynu Ultra Disk. Należy również wziąć pod uwagę rozmiary rozmiarów wymienionych dla woluminów dla różnych jednostek SKU maszyn wirtualnych, które są już wykonywane w tabelach Ultra Disk.

> [!TIP]
> Woluminy Azure NetApp Files można zmieniać dynamicznie, bez konieczności stosowania `unmount` woluminów, zatrzymywać maszyny wirtualne lub zatrzymywać SAP HANA. Dzięki temu można elastycznie spełnić wymagania dotyczące przepływności aplikacji zarówno w oczekiwany sposób, jak i nieprzewidziane.

Dokumentacja dotycząca sposobu wdrażania SAP HANA skalowania w poziomie za pomocą węzła rezerwy przy użyciu woluminów NFS w wersji 4.1, które są hostowane w ANF, jest publikowana w [SAP HANA skalowanie w poziomie za pomocą węzła gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Dostępność
Aktualizacje i uaktualnienia systemu ANF są stosowane bez wpływu na środowisko klienta. Zdefiniowana [Umowa SLA wynosi 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Woluminy i adresy IP oraz pule pojemności
Mając ANF, ważne jest, aby zrozumieć, jak tworzona jest podstawowa infrastruktura. Pula pojemności ma tylko strukturę, która ułatwia tworzenie modelu rozliczeń dla ANF. Pula pojemności nie ma fizycznej relacji z podstawową infrastrukturą. Jeśli tworzysz pulę pojemności, zostanie utworzona powłoka, która może być naliczana, nie więcej. Podczas tworzenia woluminu pierwszy SVM (maszyna wirtualna magazynu) jest tworzony w klastrze kilku systemów NetApp. Pojedynczy adres IP jest tworzony dla tego SVM, aby uzyskać dostęp do woluminu. W przypadku tworzenia kilku woluminów wszystkie woluminy są dystrybuowane w tym SVM przez wielokontrolerowy klaster NetApp. Nawet jeśli otrzymasz tylko jeden adres IP, dane są dystrybuowane za pośrednictwem kilku kontrolerów. ANF ma logikę, która automatycznie dystrybuuje obciążenia klientów, gdy woluminy lub/i pojemność skonfigurowanego magazynu osiągnie wewnętrzny wstępnie zdefiniowany poziom. Można zauważyć, że te przypadki są przypisane do nowych adresów IP w celu uzyskania dostępu do woluminów.

##<a name="log-volume-and-log-backup-volume"></a>Wolumin dziennika i kopia zapasowa dziennika
"Wolumin dziennika" (**/Hana/log**) służy do zapisywania dziennika ponownego wykonywania w trybie online. W rezultacie istnieją otwarte pliki znajdujące się w tym woluminie i nie ma sensu tworzenia migawek tego woluminu. Pliki dziennika ponownego wykonywania w trybie online są archiwizowane lub mają kopie zapasowe w woluminie kopii zapasowej dziennika po zapełnieniu tego pliku lub wykonaniu kopii zapasowej dziennika wykonaj ponownie. Aby zapewnić odpowiednią wydajność kopii zapasowych, wolumin kopii zapasowej dziennika wymaga odpowiedniej przepływności. Aby zoptymalizować koszty magazynowania, warto mieć sens skonsolidowania dziennika kopii zapasowych na woluminie wielu wystąpień platformy HANA. Dzięki temu wiele wystąpień platformy HANA wykorzystuje ten sam wolumin i zapisuje ich kopie zapasowe w różnych katalogach. Korzystając z takiej konsolidacji, można uzyskać większą przepływność, ponieważ trzeba zwiększyć rozmiar woluminu. 

To samo dotyczy woluminu, który jest używany do zapisu pełnych kopii zapasowych bazy danych HANA do.  
 

## <a name="backup"></a>Backup
Oprócz przesyłania strumieniowego kopii zapasowych i usługi Azure back Service tworzenie kopii zapasowych baz danych SAP HANA, zgodnie z opisem w artykule [Przewodnik tworzenia kopii zapasowych dla SAP HANA na platformie Azure Virtual Machines](./sap-hana-backup-guide.md), Azure NetApp Files otwiera możliwość wykonywania kopii zapasowych migawek opartych na magazynie. 

SAP HANA obsługuje:

- Tworzenie kopii zapasowych migawek opartych na magazynie z SAP HANA 1,0 SPS7 na
- Obsługa kopii zapasowych migawek opartych na magazynie dla środowisk datadatabase Container (MDC) HANA z SAP HANA 2,0 SPS4 na


Tworzenie kopii zapasowych migawek opartych na magazynie jest prostą procedurą z czterema krokami, 
1. Tworzenie migawki bazy danych platformy HANA (wewnętrznej) — działanie, które należy wykonać lub narzędzia 
1. SAP HANA zapisuje dane do datafiles w celu utworzenia spójnego stanu w magazynie — HANA wykonuje ten krok w wyniku utworzenia migawki platformy HANA
1. Utwórz migawkę na woluminie **/Hana/Data** w magazynie — krok lub narzędzia, które należy wykonać. Nie ma potrzeby wykonywania migawek na woluminie **/Hana/log**
1. Usuwanie migawki bazy danych HANA (wewnętrzna) i wznawianie normalnej operacji — krok lub narzędzia potrzebne do wykonania

> [!WARNING]
> Brak ostatniego kroku lub niepowodzenie wykonywania ostatniego kroku ma poważnie wpływ na zapotrzebowanie na pamięć SAP HANA i może doprowadzić do zatrzymania SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Kopia zapasowa migawek ANF dla SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![Kopia zapasowa migawek ANF SAP HANA part2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Tę procedurę tworzenia kopii zapasowej migawek można zarządzać na różne sposoby przy użyciu różnych narzędzi. Przykładem jest skrypt w języku Python "ntaphana_azure. pr" dostępny w witrynie GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) jest to przykładowy kod, dostarczany "AS-IS" bez żadnej konserwacji lub pomocy technicznej.



> [!CAUTION]
> Migawka nie jest chronioną kopią zapasową, ponieważ znajduje się w tym samym magazynie fizycznym co wolumin, dla którego właśnie została wykonana migawka. Obowiązkowe jest "Ochrona" co najmniej jednej migawki dziennie w innej lokalizacji. Można to zrobić w tym samym środowisku, w zdalnym regionie platformy Azure lub w usłudze Azure Blob Storage.


Dla użytkowników produktów do tworzenia kopii zapasowych programu CommVault druga opcja to CommVault IntelliSnap V. 11.21 i nowszych. Ta lub nowsza wersja programu CommVault oferują pomoc techniczną Azure NetApp Files. Artykuł [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) zawiera więcej informacji.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Tworzenie kopii zapasowej migawki przy użyciu usługi Azure Blob Storage
Tworzenie kopii zapasowych w usłudze Azure Blob Storage to ekonomiczna i szybka metoda zapisywania kopii zapasowych migawek magazynu bazy danych HANA opartych na ANF. Aby można było zapisać migawki w usłudze Azure Blob Storage, preferowane jest narzędzie AzCopy. Pobierz najnowszą wersję tego narzędzia i zainstaluj ją, na przykład w katalogu bin, w którym zainstalowano skrypt języka Python z witryny GitHub.
Pobierz najnowszą wersję narzędzia AzCopy:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

Najbardziej zaawansowaną funkcją jest opcja synchronizacji. W przypadku korzystania z opcji Synchronizuj AzCopy utrzymuje zsynchronizowane źródło i katalog docelowy. Użycie parametru **--delete-Destination** jest ważne. Bez tego parametru AzCopy nie usuwa plików w lokacji docelowej, a wykorzystanie miejsca na stronie docelowej zostanie powiększone. Utwórz kontener blokowych obiektów BLOB na koncie usługi Azure Storage. Następnie Utwórz klucz sygnatury dostępu współdzielonego dla kontenera obiektów blob i zsynchronizuj folder migawek z kontenerem obiektów blob platformy Azure.

Na przykład jeśli do kontenera obiektów blob platformy Azure należy synchronizować codzienne migawki w celu ochrony danych. I tylko jedną migawkę należy zachować, można użyć poniższego polecenia.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem:

- [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](./sap-hana-availability-overview.md)