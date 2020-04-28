---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ad36547b0cf9457d7ff4fdd919a0bb1362539eb5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522717"
---
W tym artykule odpowiedzieć na niektóre często zadawane pytania dotyczące dysków zarządzanych platformy Azure i dysków SSD w wersji Premium platformy Azure.

## <a name="managed-disks"></a>Dyski zarządzane

**Co to są dyski zarządzane platformy Azure?**

Dyski zarządzane to funkcja, która upraszcza zarządzanie dyskami dla maszyn wirtualnych usługi Azure IaaS, obsługując zarządzanie kontami magazynu. Aby uzyskać więcej informacji, zobacz [omówienie dysków zarządzanych](../articles/virtual-machines/windows/managed-disks-overview.md).

**Jeśli utworzym standardowy dysk zarządzany z istniejącego dysku VHD o masie 80 GB, ile to będzie mnie kosztować?**

Standardowy dysk zarządzany utworzony na podstawie dysku VHD o rozmiarze 80 GB jest traktowany jako następny dostępny standardowy rozmiar dysku, który jest dyskiem S10. Opłata jest naliczana zgodnie z cennikiem dysku S10. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy są jakieś koszty transakcji dla standardowych dysków zarządzanych?**

Tak. Opłata jest naliczana za każdą transakcję. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy w przypadku standardowego dysku zarządzanego zostanie naliczona opłata za rzeczywisty rozmiar danych na dysku lub za aprowizowanego pojemność dysku?**

Opłata jest naliczana na podstawie aprowizowanego pojemność dysku. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czym różni się wycena dysków zarządzanych w wersji premium od dysków niezarządzanych?**

Ceny dysków zarządzanych w wersji premium są takie same jak niezarządzane dyski premium.

**Czy mogę zmienić typ konta magazynu (Standard lub Premium) dysków zarządzanych?**

Tak. Typ konta magazynu dysków zarządzanych można zmienić za pomocą witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

**Czy mogę użyć pliku VHD na koncie magazynu platformy Azure, aby utworzyć dysk zarządzany z inną subskrypcją?**

Tak.

**Czy można użyć pliku VHD na koncie magazynu platformy Azure, aby utworzyć dysk zarządzany w innym regionie?**

Nie.

**Czy istnieją ograniczenia skali dla klientów korzystających z dysków zarządzanych?**

Dyski zarządzane eliminują limity związane z kontami magazynu. Jednak maksymalny limit wynosi 50 000 dysków zarządzanych na region i na typ dysku dla subskrypcji.

**Czy maszyny wirtualne w zestawie dostępności mogą składać się z kombinacji dysków zarządzanych i niezarządzanych?**

Nie. Maszyny wirtualne w zestawie dostępności muszą używać wszystkich dysków zarządzanych lub wszystkich dysków niezarządzanych. Podczas tworzenia zestawu dostępności można wybrać typ dysków, których chcesz użyć.

**Czy dyski zarządzane są domyślną opcją w witrynie Azure portal?**

Tak.

**Czy można utworzyć pusty dysk zarządzany?**

Tak. Można utworzyć pusty dysk. Dysk zarządzany można utworzyć niezależnie od maszyny Wirtualnej, na przykład bez dołączania go do maszyny Wirtualnej.

**Jaka jest obsługiwana liczba domen błędów dla zestawu dostępności korzystającego z dysków zarządzanych?**

W zależności od regionu, w którym znajduje się zestaw dostępności korzystający z dysków zarządzanych, liczba obsługiwanych domen błędów wynosi 2 lub 3.

**W jaki sposób skonfigurowane jest standardowe konto magazynu dla diagnostyki?**

Skonfiguruj konto magazynu prywatnego dla diagnostyki maszyny Wirtualnej.

**Jaki rodzaj obsługi kontroli dostępu opartej na rolach jest dostępny dla dysków zarządzanych?**

Dyski zarządzane obsługują trzy kluczowe role domyślne:

* Właściciel: Może zarządzać wszystkim, w tym dostępem
* Współautor: może zarządzać wszystkim, z wyjątkiem dostępu
* Czytnik: Może wyświetlać wszystko, ale nie można wprowadzać zmian

**Czy istnieje sposób kopiowania lub eksportowania dysku zarządzanego na prywatne konto magazynu?**

Można wygenerować identyfikator URI podpisu dostępu współdzielonego tylko do odczytu dla dysku zarządzanego i użyć go do skopiowania zawartości na prywatne konto magazynu lub do magazynu lokalnego. Identyfikatora URI sygnatury dostępu Współdzielonego można używać przy użyciu witryny Azure portal, azure powershell, interfejsu wiersza polecenia platformy Azure lub [interfejsu wiersza polecenia AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Czy mogę utworzyć kopię dysku zarządzanego?**

Klienci mogą zrobić migawkę swoich dysków zarządzanych, a następnie użyć migawki do utworzenia innego dysku zarządzanego.

**Czy dyski niezarządzane są nadal obsługiwane?**

Tak, obsługiwane są zarówno dyski niezarządzane, jak i zarządzane. Zaleca się używanie dysków zarządzanych dla nowych obciążeń i migrowanie bieżących obciążeń do dysków zarządzanych.

**Czy mogę wspólnie zlokalizować dyski niezarządzane i zarządzane na tej samej maszynie wirtualnej?**

Nie.

**Jeśli utworzym dysk 128 GB, a następnie zwiększyć rozmiar do 130 gibibajtów (GiB), będę naliczany za następny rozmiar dysku (256 GiB)?**

Tak.

**Czy można tworzyć lokalnie nadmiarowe dyski magazynowe, magazyn geograficznie nadmiarowy i dyski zarządzane magazynu strefowego nadmiarowego?**

Dyski zarządzane platformy Azure obsługują obecnie tylko lokalnie nadmiarowe dyski zarządzane magazynu.

**Czy mogę zmniejszyć lub zmniejszyć rozmiar dysków zarządzanych?**

Nie. Ta funkcja nie jest obecnie obsługiwana.

**Czy mogę przerwać dzierżawę na dysku?**

Nie. Obecnie nie jest to obsługiwane, ponieważ dzierżawa jest obecna, aby zapobiec przypadkowemu usunięciu dysku, gdy dysk jest używany.

**Czy można zmienić właściwość nazwy komputera, gdy wyspecjalizowany (nie utworzony za pomocą narzędzia Przygotowanie systemu lub uogólniony) dysk systemu operacyjnego jest używany do aprowizowania maszyny Wirtualnej?**

Nie. Nie można zaktualizować właściwości nazwa komputera. Nowa maszyna wirtualna dziedziczy ją z nadrzędnej maszyny Wirtualnej, która została użyta do utworzenia dysku systemu operacyjnego. 

**Gdzie można znaleźć przykładowe szablony usługi Azure Resource Manager do tworzenia maszyn wirtualnych z dyskami zarządzanymi?**
* [Lista szablonów przy użyciu dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Czy podczas tworzenia dysku z obiektu blob istnieje stale istniejąca relacja z tym źródłowym obiektem blob?**

Nie, po utworzeniu nowego dysku jest pełna autonomiczna kopia tego obiektu blob w tym czasie i nie ma połączenia między nimi. Jeśli chcesz, po utworzeniu dysku źródłowy obiekt blob może zostać usunięty bez wpływu na nowo utworzony dysk w jakikolwiek sposób.

**Czy mogę zmienić nazwę dysku zarządzanego lub niezarządzanego po jego utworzeniu?**

W przypadku dysków zarządzanych nie można ich zmienić. Można jednak zmienić nazwę dysku niezarządzanego, o ile nie jest on obecnie podłączony do dysku VHD lub maszyny Wirtualnej.

**Czy można używać partycjonowania GPT na dysku platformy Azure?**

Obrazy generacji 1 mogą używać partycjonowania GPT tylko na dyskach z danymi, a nie na dyskach systemu operacyjnego. Dyski systemu operacyjnego muszą używać stylu partycji MBR.

[Obrazy generacji 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) mogą używać partycjonowania GPT na dysku systemu operacyjnego, a także na dyskach z danymi.

**Jakie typy dysków obsługują migawki?**

Dysk SSD premium, standardowy dysk SSD i standardowe dyski twarde obsługują migawki. W przypadku tych trzech typów dysków migawki są obsługiwane dla wszystkich rozmiarów dysków (w tym dysków o rozmiarze do 32 TiB). Dyski ultra nie obsługują migawek.

**Co to są rezerwacje dysków platformy Azure?**
Rezerwacja dysku jest opcją zakupu jednego roku pamięci masowej na dysku z wyprzedzeniem, co zmniejsza całkowity koszt. Aby uzyskać szczegółowe informacje dotyczące rezerwacji dysków platformy Azure, zobacz nasz artykuł na ten temat: [Dowiedz się, jak rabat za rezerwację jest stosowany do usługi Azure Disk.](../articles/cost-management-billing/reservations/understand-disk-reservations.md)

**Jakie opcje oferuje rezerwacja dysku platformy Azure?**    
Rezerwacja dysku platformy Azure umożliwia zakup dysków SSD premium w określonych jednostkach SKU od P30 (1 TiB) do P80 (32 TiB) na okres jednego roku. Nie ma ograniczeń co do minimalnej ilości dysków niezbędnych do zakupu rezerwacji dysku. Dodatkowo możesz płacić jedną płatnością z góry lub płatnościami miesięcznymi. Nie ma żadnych dodatkowych kosztów transakcyjnych stosowanych dla dysków zarządzanych dysków SSD premium.    

Rezerwacje są dokonywane w formie dysków, a nie pojemności. Innymi słowy, po zarezerwowania dysku P80 (32 TiB) otrzymujesz jeden dysk P80, nie można podzielić tej konkretnej rezerwacji na dwa mniejsze dyski P70 (16 TiB). Możesz oczywiście zarezerwować tyle lub więcej dysków, ile chcesz, w tym dwa oddzielne dyski P70 (16 TiB).

**W jaki sposób stosowana jest rezerwacja dysku platformy Azure?**    
Rezerwacja dysków jest zgodna z modelem podobnym do wystąpienia zarezerwowanej maszyny wirtualnej (VM). Różnica polega na tym, że rezerwacji dysku nie można zastosować do różnych jednostek SKU, podczas gdy wystąpienie maszyny Wirtualnej może. Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [Zapisywanie kosztów za pomocą wystąpienia zarezerwowanych maszyn wirtualnych](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) platformy Azure.     

**Czy mogę użyć magazynu danych zakupionego za pośrednictwem rezerwacji dysków platformy Azure w wielu regionach?**    
Rezerwacja dysków platformy Azure są kupowane dla określonego regionu i jednostki SKU (jak P30 we wschodnich stanach USA 2) i dlatego nie można używać poza tymi konstrukcjami. Zawsze można zakupić dodatkową rezerwację dysków platformy Azure dla potrzeb magazynu dysków w innych regionach lub jednostek SKU.    

**Co się stanie, gdy moja rezerwacja dysków platformy Azure wygaśnie?**    
Otrzymasz powiadomienia e-mail 30 dni przed wygaśnięciem i ponownie w dniu wygaśnięcia. Po wygaśnięciu rezerwacji wdrożone dyski będą nadal działać i będą rozliczane z [najnowszymi stawkami płatności zgodnie z rzeczywistym użyciem.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Dyski udostępnione platformy Azure

**Czy funkcja dysków udostępnionych jest obsługiwana dla dysków niezarządzanych lub obiektów blob stron?**

Nie, jest obsługiwany tylko dla dysków zarządzanych w wersji premium SSD.

**Jakie regiony obsługują dyski udostępnione?**

Obecnie tylko zachodnio-środkowe stany USA.

**Czy dyski udostępnione mogą być używane jako dysk systemu operacyjnego?**

Nie, dyski udostępnione są obsługiwane tylko dla dysków z danymi.

**Jakie rozmiary dysków obsługują dyski współużytkowane?**

Tylko dyski SSD premium, które są P15 lub większe wsparcie dysków udostępnionych.

**Jeśli mam istniejący dysk SSD w wersji premium, czy mogę włączyć dyski współdzielone?**

Wszystkie dyski zarządzane utworzone za pomocą interfejsu API w wersji 2019-07-01 lub nowszej mogą włączyć dyski udostępnione. Aby to zrobić, należy odinstalować dysk ze wszystkich maszyn wirtualnych, do których jest dołączony. Następnie edytuj `maxShares` właściwość na dysku.

**Jeśli nie chcę już używać dysku w trybie udostępnionym, jak go wyłączyć?**

Odinstaluj dysk ze wszystkich maszyn wirtualnych, do których jest dołączony. Następnie edytuj właściwość maxShare na dysku do 1.

**Czy można zmienić rozmiar udostępnionego dysku?**

Tak.

**Czy mogę włączyć akcelerator zapisu na dysku, na którym włączono również dyski udostępnione?**

Nie.

**Czy mogę włączyć buforowanie hosta na dysku, na którym jest włączony dysk udostępniony?**

Jedyną obsługiwaną opcją buforowania hosta jest "Brak".

## <a name="ultra-disks"></a>Dyski w warstwie Ultra

**Na co należy ustawić przepustowość dysku ultra?**
Jeśli nie masz pewności, na co ustawić przepływność dysku, zalecamy rozpoczęcie od przy założeniu rozmiaru we/wy 16 KiB i dostosowanie wydajności z tego miejsca podczas monitorowania aplikacji. Formuła jest: Przepustowość w MBps = # IOPS * 16 / 1000.

**Skonfigurowałem dysk do 40000 IOPS, ale widzę tylko 12800 IOPS, dlaczego nie widzę wydajności dysku?**
Oprócz przepustnicy dysku istnieje przepustnica we/wy, która zostanie nałożona na poziomie maszyny Wirtualnej. Upewnij się, że używany rozmiar maszyny Wirtualnej może obsługiwać poziomy skonfigurowane na dyskach. Aby uzyskać szczegółowe informacje dotyczące limitów we/wy nałożonych przez maszynę wirtualną, zobacz [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure.](../articles/virtual-machines/windows/sizes.md)

**Czy mogę używać poziomów buforowania z ultra dyskiem?**
Nie, dyski ultra nie obsługują różnych metod buforowania, które są obsługiwane na innych typach dysków. Ustaw buforowanie dysku na Brak.

**Czy mogę podłączyć dysk ultra do istniejącej maszyny Wirtualnej?**
Być może maszyna wirtualna musi znajdować się w parach strefy regionu i dostępności, która obsługuje dyski Ultra. Szczegółowe informacje można znaleźć w [punkcie rozpoczynania pracy z dyskami ultra.](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md)

**Czy mogę używać dysku ultra jako dysku systemu operacyjnego dla mojej maszyny Wirtualnej?**
Nie, dyski ultra są obsługiwane tylko jako dyski z danymi i są obsługiwane tylko jako dyski macierzyste 4K.

**Czy można przekonwertować istniejący dysk na dysk ultra?**
Nie, ale można migrować dane z istniejącego dysku na dysk ultra. Aby przeprowadzić migrację istniejącego dysku na dysk ultra, dołącz oba dyski do tej samej maszyny wirtualnej i skopiuj dane dysku z jednego dysku na drugi lub wykorzystaj rozwiązanie innej firmy do migracji danych.

**Czy można tworzyć migawki dla dysków ultra?**
Nie, migawki nie są jeszcze dostępne.

**Czy usługa Azure Backup jest dostępna dla dysków ultra?**
Nie, pomoc techniczna usługi Azure Backup nie jest jeszcze dostępna.

**Czy mogę podłączyć dysk ultra do maszyny Wirtualnej uruchomionej w zestawie dostępności?**
Nie, nie jest to jeszcze obsługiwane.

**Czy mogę włączyć usługę Azure Site Recovery dla maszyn wirtualnych przy użyciu dysków ultra?**
Nie, usługa Azure Site Recovery nie jest jeszcze obsługiwana dla dysków ultra.

## <a name="uploading-to-a-managed-disk"></a>Przekazywanie na dysk zarządzany

**Czy mogę przekazać dane na istniejący dysk zarządzany?**

Nie, przekazywanie może być używane tylko podczas tworzenia nowego pustego dysku ze stanem **ReadyToUpload.**

**Jak przekazać na dysk zarządzany?**

Utwórz dysk zarządzany z właściwością [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) [creationData ustawioną](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) na "Przekaż", a następnie możesz przekazać do niego dane.

**Czy mogę dołączyć dysk do maszyny Wirtualnej, gdy jest w stanie przekazywania?**

Nie.

**Czy mogę zrobić migawkę dysku manged w stanie przekazywania?**

Nie.

## <a name="standard-ssd-disks"></a>Standardowe dyski SSD

**Co to są standardowe dyski SSD platformy Azure?**
Standardowe dyski SSD to dyski standardowe wspierane przez nośniki półprzewodnikowe, zoptymalizowane jako ekonomiczna pamięć masowa dla obciążeń, które wymagają stałej wydajności przy niższych poziomach we/wy.

<a id="standard-ssds-azure-regions"></a>**Jakie regiony są obecnie obsługiwane dla standardowych dysków SSD?**
Wszystkie regiony platformy Azure obsługują teraz standardowe dyski SSD.

**Czy usługa Azure Backup jest dostępna podczas korzystania ze standardowych ssd?**
Tak, usługa Azure Backup jest już dostępna.

**Jak utworzyć standardowe dyski SSD?**
Standardowe dyski SSD można tworzyć przy użyciu szablonów usługi Azure Resource Manager, SDK, PowerShell lub CLI. Poniżej znajdują się parametry potrzebne w szablonie Menedżera zasobów do tworzenia standardowych dysków SSD:

* *apiVersion* for Microsoft.Compute musi `2018-04-01` być ustawiony jako (lub nowszy)
* Określ *typ konta managedDisk.storageAccounttype* jako`StandardSSD_LRS`

W poniższym przykładzie przedstawiono sekcję *properties.storageProfile.osDisk* dla maszyny Wirtualnej korzystającej ze standardowych dysków SSD:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Aby uzyskać pełny przykład szablonu tworzenia standardowego dysku SSD z szablonem, zobacz [Tworzenie maszyny wirtualnej z obrazu systemu Windows ze standardowymi dyskami danych SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Czy można przekonwertować istniejące dyski na standardowy dysk SSD?**
Tak, można. Aby uzyskać ogólne wskazówki dotyczące konwertowania dysków zarządzanych, zapoznaj się [z witryną Konwertuj magazyn dysków zarządzanych platformy Azure ze standardowego na premium i odwrotnie.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) Aby zaktualizować typ dysku do standardowego dysku SSD, należy użyć następującej wartości.
-AccountType StandardSSD_LRS

**Jakie są korzyści z używania standardowych dysków SSD zamiast dysków twardych?**
Standardowe dyski SSD zapewniają lepsze opóźnienia, spójność, dostępność i niezawodność w porównaniu z dyskami twardymi. Obciążenia aplikacji działają o wiele płynniej na standardowym ssd z tego powodu. Uwaga: dyski SSD w wersji Premium są zalecanym rozwiązaniem dla większości obciążeń produkcyjnych intensywnie korzystających z we/wy.

**Czy mogę używać standardowych dysków SSD jako dysków niezarządzanych?**
Nie, standardowe dyski SSD są dostępne tylko jako dyski zarządzane.

**Czy standardowe dyski SSD obsługują "pojedynczą sferę SLA VM"?**
Nie, standardowe ssd nie mają pojedynczego wystąpienia VM SLA. Użyj dysków SSD premium dla pojedynczego wystąpienia VM SLA.

## <a name="migrate-to-managed-disks"></a>Migrowanie do funkcji Dyski zarządzane

**Czy migracja ma jakikolwiek wpływ na wydajność dysków zarządzanych?**

Migracja obejmuje przenoszenie dysku z jednej lokalizacji magazynu do drugiej. Jest to organizowane za pomocą kopii danych w tle, co może potrwać kilka godzin, zazwyczaj mniej niż 24 godziny w zależności od ilości danych na dyskach. W tym czasie aplikacja może wystąpić wyższe niż zwykle opóźnienia odczytu, jak niektóre odczyty można uzyskać przekierowane do oryginalnej lokalizacji i może potrwać dłużej, aby zakończyć. Nie ma wpływu na opóźnienie zapisu w tym okresie.  

**Jakie zmiany są wymagane w istniejącej konfiguracji usługi Azure Backup przed/po migracji do dysków zarządzanych?**

Żadne zmiany nie są wymagane.

**Czy moje kopie zapasowe maszyn wirtualnych utworzone za pośrednictwem usługi Azure Backup przed migracją będą nadal działać?**

Tak, kopie zapasowe działają bezproblemowo.

**Jakie zmiany są wymagane w istniejącej konfiguracji szyfrowania dysków platformy Azure przed/po migracji do dysków zarządzanych?**

Żadne zmiany nie są wymagane.

**Czy automatyczna migracja istniejącej skali maszyny wirtualnej jest skonfigurowana z dysków niezarządzanych do dysków zarządzanych?**

Nie. Można utworzyć nowy zestaw skalowania z dyskami zarządzanymi przy użyciu obrazu ze starego zestawu skalowania z dyskami niezarządzanymi.

**Czy można utworzyć dysk zarządzany z migawki obiektu blob strony wykonanej przed migracją na dyski zarządzane?**

Nie. Migawkę obiektu blob strony można wyeksportować jako obiekt blob strony, a następnie utworzyć dysk zarządzany z wyeksportowanego obiektu blob strony.

**Czy mogę awaryjnie nad komputerami lokalnymi chronionymi przez usługę Azure Site Recovery na maszynie wirtualnej z dyskami zarządzanymi?**

Tak, można wybrać opcję pracy awaryjnej na maszynie wirtualnej z dyskami zarządzanymi.

**Czy istnieje jakikolwiek wpływ migracji na maszyny wirtualne platformy Azure chronione przez usługę Azure Site Recovery za pośrednictwem platformy Azure do replikacji platformy Azure?**

Nie. Dostępna jest usługa Azure Site Recovery Azure Azure to Azure protection for VMs with Managed Disks.

**Czy mogę migrować maszyny wirtualne z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które są lub były wcześniej szyfrowane na dyskach zarządzanych?**

Yes

## <a name="managed-disks-and-storage-service-encryption"></a>Szyfrowanie dysków zarządzanych i usługi magazynowania

**Czy szyfrowanie usługi Azure Storage jest domyślnie włączone podczas tworzenia dysku zarządzanego?**

Tak.

**Czy wolumin rozruchowy jest domyślnie szyfrowany na dysku zarządzanym?**

Tak. Domyślnie wszystkie dyski zarządzane są szyfrowane, w tym dysk systemu operacyjnego.

**Kto zarządza kluczami szyfrowania?**

Firma Microsoft zarządza kluczami szyfrowania.

**Czy mogę wyłączyć szyfrowanie usługi magazynu dla dysków zarządzanych?**

Nie.

**Czy szyfrowanie usługi magazynu jest dostępne tylko w określonych regionach?**

Nie. Jest on dostępny we wszystkich regionach, w których dyski zarządzane są dostępne. Dyski zarządzane są dostępne we wszystkich regionach publicznych i Niemczech. Jest również dostępny w Chinach, jednak tylko dla kluczy zarządzanych firmy Microsoft, a nie kluczy zarządzanych przez klienta.

**Jak mogę sprawdzić, czy mój dysk zarządzany jest zaszyfrowany?**

Możesz dowiedzieć się, kiedy dysk zarządzany został utworzony z witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell. Jeśli czas jest po 9 czerwca 2017 r., dysk jest szyfrowany.

**Jak zaszyfrować istniejące dyski utworzone przed 10 czerwca 2017 r.?**

Od 10 czerwca 2017 r. nowe dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane. Planujemy również szyfrowanie istniejących danych, a szyfrowanie będzie odbywać się asynchronicznie w tle. Jeśli musisz teraz zaszyfrować istniejące dane, utwórz kopię dysku. Nowe dyski będą szyfrowane.

* [Kopiowanie dysków zarządzanych przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiowanie dysków zarządzanych przy użyciu programu PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Czy zarządzane migawki i obrazy są szyfrowane?**

Tak. Wszystkie zarządzane migawki i obrazy utworzone po 9 czerwca 2017 r. są automatycznie szyfrowane. 

**Czy można konwertować maszyny wirtualne z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które są lub były wcześniej szyfrowane na dyski zarządzane?**

Yes

**Czy wyeksportowany dysk VHD z dysku zarządzanego lub migawka również będzie szyfrowany?**

Nie. Jeśli jednak eksportujesz dysk VHD do zaszyfrowanego konta magazynu z zaszyfrowanego dysku zarządzanego lub migawki, zostanie on zaszyfrowany. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dyski premium: zarządzane i niezarządzane

**Jeśli maszyna wirtualna używa serii rozmiarów obsługujących dyski SSD premium, takie jak DSv2, czy mogę dołączyć zarówno dyski premium, jak i standardowe dyski z danymi?** 

Tak.

**Czy mogę dołączyć dyski premium i standardowe do serii rozmiarów, które nie obsługują dysków premium SSD, takich jak seria D, Dv2, G lub F?**

Nie. Do maszyn wirtualnych, które nie używają serii rozmiarów obsługujących dyski SSD w wersji Premium, można dołączyć tylko standardowe dyski z danymi.

**Jeśli utworzym dysk danych premium z istniejącego dysku VHD, który był 80 GB, ile to będzie kosztować?**

Dysk danych premium utworzony na podstawie dysku VHD o rozmiarze 80 GB jest traktowany jako następny dostępny rozmiar dysku premium, który jest dyskiem P10. Opłata jest naliczana zgodnie z cennikiem dysku P10.

**Czy korzystanie z dysków SSD premium jest związane z kosztami transakcji?**

Istnieje koszt stały dla każdego rozmiaru dysku, który jest aprowizowana z określonymi limitami na we/wy i przepływności. Pozostałe koszty to przepustowość wychodząca i pojemność migawki, jeśli ma to zastosowanie. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jakie są limity dla we/wy i przepływności, które można uzyskać z pamięci podręcznej dysku?**

Łączne limity dla pamięci podręcznej i lokalnego SSD dla serii DS to 4000 IOPS na rdzeń i 33 MiB na sekundę na rdzeń. Seria GS oferuje 5000 IOPS na rdzeń i 50 MiB na sekundę na rdzeń.

**Czy lokalny dysk SSD jest obsługiwany dla maszyny wirtualnej dysków zarządzanych?**

Lokalny dysk SSD jest magazynem tymczasowym dołączonym do maszyny wirtualnej dysków zarządzanych. Ta tymczasowa pamięć masowa nie jest uliczę dodatkowej kosztów. Firma Microsoft zaleca, aby nie używać tego lokalnego dysku SSD do przechowywania danych aplikacji, ponieważ nie jest zachowywany w magazynie obiektów Blob platformy Azure.

**Czy istnieją jakieś reperkusje dla stosowania TRIM na dyskach premium?**

Nie ma żadnych wad użycia trim na dyskach platformy Azure na dyskach premium lub standardowych.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nowe rozmiary dysków: zarządzane i niezarządzane

**Jakie regiony obsługują możliwość rozerwania dla odpowiedniego rozmiaru dysku SSD premium?**

Funkcja rozbłysku jest obecnie obsługiwana we wszystkich regionach usługi Azure Public Cloud, a wkrótce pojawi się obsługa suwerennych chmur. 

**W jakich regionach są obsługiwane rozmiary dysków zarządzanych GiB 4/8/16 (P1/P2/P3, E1/E2/E3)?**

Te nowe rozmiary dysków są obecnie obsługiwane we wszystkich regionach usługi Azure Public Cloud, a wkrótce pojawi się obsługa chmur suwerennych. 

**Czy rozmiary dysków P1/P2/P3 są obsługiwane dla dysków niezarządzanych lub obiektów blob stron?**

Nie, jest obsługiwany tylko na dyskach zarządzanych dysków SSD w wersji premium. 

**Czy rozmiary dysków E1/E2/E3 są obsługiwane dla dysków niezarządzanych lub obiektów blob stron?**

Nie, standardowe dyski zarządzane SSD o dowolnym rozmiarze nie mogą być używane z dyskami niezarządzanymi ani obiektami blob stron.

**Jaki jest największy rozmiar dysku zarządzanego obsługiwany dla systemów operacyjnych i dysków z danymi?**

Typ partycji, który platforma Azure obsługuje dla dysku systemu operacyjnego, jest głównym rekordem rozruchowym (MBR). Format MBR obsługuje dysk o rozmiarze do 2 TiB. Największy rozmiar obsługiwana przez platformę Azure dla dysku systemu operacyjnego to 2 TiB. Platforma Azure obsługuje do 32 TiB dla zarządzanych dysków z danymi.

**Jaki jest największy rozmiar dysku niezarządzanego obsługiwany dla dysków operacyjnych i dysków z danymi?**

Typ partycji, który platforma Azure obsługuje dla dysku systemu operacyjnego, jest głównym rekordem rozruchowym (MBR). Format MBR obsługuje dysk o rozmiarze do 2 TiB. Największy rozmiar, który obsługuje platformy Azure dla systemu operacyjnego Niezarządzany dysk jest 2 TiB. Platforma Azure obsługuje do 4 TiB dla dysków niezarządzanych danych.

**Jaki jest największy rozmiar obiektu blob strony, który jest obsługiwany?**

Największy rozmiar obiektu blob strony, który obsługuje platformy Azure jest 8 TiB (8,191 GiB). Maksymalny rozmiar obiektu blob strony po podłączeniu do maszyny Wirtualnej jako dysków z danymi lub systemem operacyjnym wynosi 4 TiB (4095 GiB).

**Czy muszę używać nowej wersji narzędzi platformy Azure do tworzenia, dołączania, zmiany rozmiaru i przekazywania dysków większych niż 1 TiB?**

Nie trzeba uaktualniać istniejących narzędzi platformy Azure, aby utworzyć, dołączyć lub zmienić rozmiar dysków większych niż 1 TiB. Aby przekazać plik VHD z lokalnego bezpośrednio do platformy Azure jako obiekt blob strony lub dysk niezarządzany, należy użyć najnowszych zestawów narzędzi wymienionych poniżej. Obsługujemy tylko przesyłanie VHD do 8 TiB.

|Narzędzia platformy Azure      | Obsługiwane wersje                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numer wersji 4.1.0: wydanie z czerwca 2017 r. lub nowsze|
|Narzędzie cli platformy Azure w wersji 1     | Numer wersji 0.10.13: wydanie z maja 2017 r. lub nowsze|
|Narzędzie cli platformy Azure w wersji 2     | Numer wersji 2.0.12: wydanie z lipca 2017 r. lub nowsze|
|Narzędzie AzCopy              | Numer wersji 6.1.0: wydanie z czerwca 2017 r. lub nowsze|

**Czy rozmiary dysków P4 i P6 są obsługiwane dla dysków niezarządzanych lub obiektów blob stron?**

Rozmiary dysków P4 (32 GiB) i P6 (64 GiB) nie są obsługiwane jako domyślne warstwy dysków dla dysków niezarządzanych i obiektów blob stron. Należy jawnie [ustawić warstwy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) do P4 i P6, aby dysk mapowane do tych warstw. Jeśli wdrożysz niezarządzany dysk lub obiekt blob strony o rozmiarze dysku lub długości zawartości mniejszej niż 32 GiB lub między 32 GiB do 64 GiB bez ustawiania warstwy obiektów blob, nadal będzie wyładowywać na P10 z 500 IOPS i 100 MiB/s i mapowaną warstwą cenową.

**Jeśli mój istniejący dysk zarządzany premium mniej niż 64 GiB został utworzony przed włączeniem małego dysku (około 15 czerwca 2017), jak jest rozliczany?**

Istniejące małe dyski premium mniejsze niż 64 GiB nadal są rozliczane zgodnie z warstwą cenową P10.

**Jak mogę przełączyć warstwę dysków małych dysków premium poniżej 64 GiB z P10 na P4 lub P6?**

Można zrobić migawkę małych dysków, a następnie utworzyć dysk, aby automatycznie przełączyć warstwę cenową na P4 lub P6 na podstawie aprowizowanego rozmiaru.

**Czy można zmienić rozmiar istniejących dysków zarządzanych z rozmiarów mniejszych niż 4 tebibajtów (TiB) do nowych nowo wprowadzonych rozmiarów dysków do 32 TiB?**

Tak.

**Jakie są największe rozmiary dysków obsługiwane przez usługę Azure Backup i usługę Azure Site Recovery?**

Największy rozmiar dysku obsługiwany przez usługę Azure Backup to 32 TiB (4 TiB dla zaszyfrowanych dysków). Największy rozmiar dysku obsługiwany przez usługę Azure Site Recovery to 8 TiB. Obsługa większych dysków o rozmiarze do 32 TiB nie jest jeszcze dostępna w usłudze Azure Site Recovery.

**Jakie są zalecane rozmiary maszyn wirtualnych dla większych rozmiarów dysków (>4 TiB) dla standardowych dysków SSD i standardowych dysków twardych w celu uzyskania zoptymalizowanych iOPS dysku i przepustowości?**

Aby osiągnąć przepustowość dysku standardowych dysków SSD i standardowych dysków twardych o dużych rozmiarach (>4 TiB) powyżej 500 IOPS i 60 MiB/s, zalecamy wdrożenie nowej maszyny Wirtualnej z jednego z następujących rozmiarów maszyn wirtualnych w celu optymalizacji wydajności: seria B, seria DSv2, seria DSv3, seria ESv3, seria Fs, seria Fs, seria M, seria M, seria GS, seria NCv2, seria NCv3 lub maszyny wirtualne z serii Ls. Dołączanie dużych dysków do istniejących maszyn wirtualnych lub maszyn wirtualnych, które nie używają zalecanych rozmiarów powyżej może wystąpić niższa wydajność.

**Jak mogę uaktualnić dyski (>4 TiB), które zostały wdrożone podczas podglądu większych rozmiarów dysków, aby uzyskać wyższą przepustowość IOPS & w GA?**

Można zatrzymać i uruchomić maszynę wirtualną, do której jest podłączony dysk, lub odłączyć i ponownie podłączyć dysk. Zwiększono cele wydajności większych rozmiarów dysków zarówno dla dysków SSD premium, jak i standardowych dysków SSD w ga.

**W jakich regionach są obsługiwane rozmiary dysków zarządzanych 8 TiB, 16 TiB i 32 TiB?**

Jednostki SKU dysków 8 TiB, 16 TiB i 32 TiB są obsługiwane we wszystkich regionach w obszarze globalnej platformy Azure, platformy Microsoft Azure dla instytucji rządowych i platformy Azure China 21Vianet.

**Czy obsługujemy włączanie buforowania hostów na wszystkich rozmiarach dysków?**

Obsługujemy buforowanie hostów readonly i odczytu/zapisu na dyskach o rozmiarach mniejszych niż 4 TiB. W przypadku dysków o rozmiarach więcej niż 4 TiB nie obsługujemy opcji buforowania ustawień innych niż Brak. Firma Microsoft zaleca korzystanie z buforowania dla mniejszych rozmiarów dysków, gdzie można oczekiwać, aby zaobserwować lepszy wzrost wydajności z danych buforowanych na maszynie Wirtualnej.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli nie ma tutaj odpowiedzi na moje pytanie?

Jeśli twojego pytania nie ma na liście, poinformuj nas o tym, a my pomożemy Ci znaleźć odpowiedź. Możesz zadać pytanie na końcu tego artykułu w komentarzach. Aby nawiązać kontakt z zespołem usługi Azure Storage i innymi członkami społeczności na temat tego artykułu, skorzystaj z forum usługi [MSDN Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Aby zażądać funkcji, prześlij swoje żądania i pomysły na [forum opinii usługi Azure Storage](https://feedback.azure.com/forums/217298-storage).
