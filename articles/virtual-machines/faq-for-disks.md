---
title: Często zadawane pytania dotyczące dysków
description: Często zadawane pytania dotyczące dysków maszyn wirtualnych z systemem Linux i dysków w warstwie Premium systemu Azure IaaS (zarządzane i niezarządzane)
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3d199ae202cbe99176f3ca5d0ae82ecdeec84ca4
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684512"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Często zadawane pytania dotyczące dysków maszyn wirtualnych IaaS platformy Azure oraz dysków zarządzanych i niezarządzanych w warstwie Premium

W tym artykule przedstawiono kilka często zadawanych pytań dotyczących usługi Azure Managed Disks i Azure SSD w warstwie Premium Disks.

## <a name="managed-disks"></a>Dyski zarządzane

**Co to jest platforma Azure Managed Disks?**

Managed Disks to funkcja, która upraszcza zarządzanie dyskami maszyn wirtualnych platformy Azure IaaS przez obsługę zarządzania kontami magazynu. Aby uzyskać więcej informacji, zobacz [omówienie Managed disks](managed-disks-overview.md).

**Jeśli utworzysz dysk zarządzany w warstwie Standardowa na podstawie istniejącego wirtualnego dysku twardego o 80 GB, ile to kosztuje?**

Dysk zarządzany w warstwie Standardowa utworzony przy użyciu dysku VHD 80-GB jest traktowany jako następny dostępny standardowy rozmiar dysku, który jest dyskiem S10. Opłata jest naliczana zgodnie z cennikiem dysku S10. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy istnieją jakieś koszty transakcji dla dysków zarządzanych w warstwie Standardowa?**

Tak. Opłata jest naliczana za każdą transakcję. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy w przypadku dysku zarządzanego w warstwie Standardowa zostanie naliczona opłata za rzeczywisty rozmiar danych na dysku lub dla pojemności dysku?**

Opłata jest naliczana na podstawie zainicjowanej pojemności dysku. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czym różnią się ceny dysków zarządzanych w warstwie Premium od dysków niezarządzanych?**

Ceny dysków zarządzanych w warstwie Premium są takie same jak niezarządzane dyski Premium.

**Czy mogę zmienić typ konta magazynu (w warstwie Standardowa lub Premium) dla moich dysków zarządzanych?**

Tak. Typ konta magazynu dla dysków zarządzanych można zmienić przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

**Czy mogę użyć pliku VHD na koncie usługi Azure Storage, aby utworzyć dysk zarządzany z inną subskrypcją?**

Tak.

**Czy mogę użyć pliku VHD na koncie usługi Azure Storage w celu utworzenia dysku zarządzanego w innym regionie?**

Nie.

**Czy istnieją jakieś ograniczenia skali dla klientów korzystających z usługi Managed disks?**

Managed Disks eliminuje ograniczenia związane z kontami magazynu. Jednak maksymalny limit to 50 000 dysków zarządzanych na region i dla każdego typu dysku dla subskrypcji.

**Czy maszyny wirtualne w zestawie dostępności składają się z kombinacji dysków zarządzanych i niezarządzanych?**

Nie. Maszyny wirtualne w zestawie dostępności muszą używać wszystkich dysków zarządzanych lub wszystkich dysków niezarządzanych. Podczas tworzenia zestawu dostępności można wybrać typ dysków, które mają być używane.

**Czy Managed Disks domyślną opcją w Azure Portal?**

Tak.

**Czy mogę utworzyć pusty dysk zarządzany?**

Tak. Można utworzyć pusty dysk. Dysk zarządzany można utworzyć niezależnie od maszyny wirtualnej, na przykład bez dołączania go do maszyny wirtualnej.

**Jaka jest obsługiwana liczba domen błędów dla zestawu dostępności korzystającego z Managed Disks?**

W zależności od regionu, w którym znajduje się zestaw dostępności, który używa Managed Disks, obsługiwana liczba domen błędów wynosi 2 lub 3.

**Jak jest skonfigurowane konto magazynu w warstwie Standardowa na potrzeby konfiguracji diagnostyki?**

Należy skonfigurować prywatne konto magazynu na potrzeby diagnostyki maszyn wirtualnych.

**Jakiego rodzaju Role-Based wsparcia Access Control jest dostępny dla Managed Disks?**

Managed Disks obsługuje trzy role domyślne klucza:

* Właściciel: może zarządzać wszystkimi, w tym dostępem
* Współautor: może zarządzać wszystko z wyjątkiem dostępu
* Czytelnik: może wyświetlać wszystko, ale nie może wprowadzać zmian

**Czy istnieje sposób, aby można było skopiować lub wyeksportować dysk zarządzany do prywatnego konta magazynu?**

Można wygenerować identyfikator URI sygnatury dostępu współdzielonego (SAS) tylko do odczytu dla dysku zarządzanego i użyć go do skopiowania zawartości na prywatne konto magazynu lub magazyn lokalny. Identyfikatora URI sygnatury dostępu współdzielonego można użyć przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub [AzCopy](../storage/common/storage-use-azcopy-v10.md)

**Czy mogę utworzyć kopię dysku zarządzanego?**

Klienci mogą wykonać migawkę zarządzanych dysków, a następnie użyć migawki do utworzenia innego dysku zarządzanego.

**Czy dyski niezarządzane są nadal obsługiwane?**

Tak, obsługiwane są zarówno dyski niezarządzane, jak i zarządzane. Zalecamy używanie dysków zarządzanych do obsługi nowych obciążeń i migrowanie bieżących obciążeń do dysków zarządzanych.

**Czy mogę umieścić dyski niezarządzane i zarządzane na tej samej maszynie wirtualnej?**

Nie.

**Jeśli tworzę dysk 128 GB, a następnie zwiększę rozmiar do 130 gibibajtach (GiB), zostanie naliczona opłata za następny rozmiar dysku (256 GiB)?**

Tak.

**Czy można utworzyć magazyn lokalnie nadmiarowy, magazyn Geograficznie nadmiarowy i dyski zarządzane przez strefowo nadmiarowe magazyny?**

Usługa Azure Managed Disks obecnie obsługuje tylko lokalnie nadmiarowe dyski zarządzane magazynu.

**Czy mogę zmniejszyć lub Downsize moje dyski zarządzane?**

Nie. Ta funkcja nie jest obecnie obsługiwana.

**Czy mogę przerwać dzierżawę na moim dysku?**

Nie. Nie jest to obecnie obsługiwane w przypadku dzierżawy, aby zapobiec przypadkowemu usunięciu, gdy dysk jest używany.

**Czy można zmienić właściwość nazwy komputera, gdy jest używany specjalny (nieutworzony przy użyciu narzędzia przygotowania systemu lub uogólniony) dysk systemu operacyjnego do aprowizacji maszyny wirtualnej?**

Nie. Nie można zaktualizować właściwości Nazwa komputera. Nowa maszyna wirtualna dziedziczy ją z nadrzędnej maszyny wirtualnej, która została użyta do utworzenia dysku systemu operacyjnego. 

**Gdzie można znaleźć przykładowe szablony Azure Resource Manager do tworzenia maszyn wirtualnych z dyskami zarządzanymi?**
* [Lista szablonów przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Czy podczas tworzenia dysku na podstawie obiektu BLOB istnieje ciągle istniejąca relacja z tym źródłowym obiektem BLOB?**

Nie, podczas tworzenia nowego dysku jest w tej chwili pełną kopią autonomiczną tego obiektu BLOB i nie ma połączenia między nimi. Jeśli chcesz, po utworzeniu dysku źródłowy obiekt BLOB może zostać usunięty bez wpływu na nowo utworzony dysk w jakikolwiek sposób.

**Czy mogę zmienić nazwę dysku zarządzanego lub niezarządzanego po jego utworzeniu?**

W przypadku dysków zarządzanych nie można zmienić ich nazw. Można jednak zmienić nazwę dysku niezarządzanego, o ile nie jest on obecnie dołączony do dysku VHD lub maszyny wirtualnej.

**Czy można używać partycjonowania GPT na dysku platformy Azure?**

Obrazy generacji 1 mogą używać tylko partycjonowania GPT na dyskach danych, a nie na dyskach systemu operacyjnego. Dyski systemu operacyjnego muszą używać stylu partycji MBR.

[Obrazy generacji 2](./generation-2.md) mogą używać partycjonowania GPT na dysku systemu operacyjnego, a także dysków z danymi.

**Jakie typy dysków obsługują migawki?**

SSD w warstwie Premium, standardowy dysk SSD i standardowe dyski twarde obsługują migawki. Dla tych trzech typów dysków migawki są obsługiwane dla wszystkich rozmiarów dysków (łącznie z dyskami o rozmiarze do 32 TiB). Dyski Ultra nie obsługują migawek.

**Co to są rezerwacje dysku platformy Azure?**
Rezerwacja dysku to opcja zakupu jednego roku magazynu dyskowego z wyprzedzeniem i zmniejszenia łącznego kosztu. Aby uzyskać szczegółowe informacje dotyczące rezerwacji dysku platformy Azure, zobacz nasz artykuł w temacie: [informacje na temat sposobu zastosowania rabatu rezerwacji na dysk platformy Azure](../cost-management-billing/reservations/understand-disk-reservations.md).

**Jakie opcje oferuje oferta rezerwacji dysku platformy Azure?**    
Rezerwacja dysku platformy Azure udostępnia opcję zakupu dysków SSD Premium w określonych jednostkach SKU z P30 (1 TiB) do P80 (32 TiB) przez okres jednego roku. Nie ma ograniczenia dotyczącego minimalnej ilości dysków koniecznych do zakupu rezerwacji dysku. Ponadto możesz wybrać opcję płacenia z jedną, z góry płatnością lub miesięczną płatnością. Managed Disks SSD w warstwie Premium nie ma dodatkowych kosztów transakcyjnych.    

Rezerwacje są wykonywane w postaci dysków, a nie pojemności. Innymi słowy, gdy zarezerwujesz dysk z systemem P80 (32 TiB), otrzymujesz jeden dysk P80, ale nie można podzielić tego konkretnej rezerwacji na dwa mniejsze dyski P70 (16 TiB). Można oczywiście zarezerwować dowolną liczbę lub kilka dysków, w tym dwa oddzielne dyski P70 (16 TiB).

**Jak jest zastosowana rezerwacja dysku platformy Azure?**    
Rezerwacja dysków jest zgodna z modelem podobnym do wystąpień zarezerwowanych maszyn wirtualnych. Różnica polega na tym, że rezerwacja dysku nie może zostać zastosowana do różnych jednostek SKU, podczas gdy wystąpienie maszyny wirtualnej może. Aby uzyskać więcej informacji o wystąpieniach maszyn wirtualnych, zobacz temat [Zapisywanie kosztów w Azure Reserved VM Instances](./linux/prepay-reserved-vm-instances.md) .     

**Czy mogę użyć magazynu danych zakupionego w ramach rezerwacji na dyskach platformy Azure w wielu regionach?**    
Rezerwacja dysków Azure jest zakupionych dla określonego regionu i jednostki SKU (na przykład P30 w regionie Wschodnie stany USA 2) i dlatego nie można jej używać poza tymi konstrukcjami. Możesz zawsze zakupić dodatkowe rezerwacje platformy Azure dla potrzeb magazynu dyskowego w innych regionach lub jednostkach SKU.    

**Co się stanie w przypadku wygaśnięcia rezerwacji z usługi Azure disks?**    
Powiadomienia e-mail będą wysyłane na 30 dni przed wygaśnięciem i od daty wygaśnięcia. Po wygaśnięciu rezerwacji wdrożone dyski będą nadal działać i opłaty są naliczane przy użyciu najnowszych [stawek płatności zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/managed-disks/)użyciem.

**Czy SSD w warstwie Standardowa dyski obsługują "umowę SLA maszyny wirtualnej o pojedynczym wystąpieniu"?**

Tak, wszystkie typy dysków obsługują umowę SLA maszyny wirtualnej z jednym wystąpieniem.

### <a name="azure-shared-disks"></a>Dyski udostępnione platformy Azure

**Czy funkcja dysków udostępnionych jest obsługiwana w przypadku dysków niezarządzanych lub stronicowych obiektów BLOB?**

Nie, jest obsługiwana tylko w przypadku dysków Ultra i Managed disks w warstwie Premium.

**Jakie regiony obsługują dyski udostępnione?**

Informacje regionalne znajdują się w naszym [artykule koncepcyjnym](disks-shared.md).

**Czy dyski udostępnione mogą być używane jako dysk systemu operacyjnego?**

Nie, dyski udostępnione są obsługiwane tylko w przypadku dysków z danymi.

**Jakie rozmiary dysków obsługują dyski udostępnione?**

Aby uzyskać obsługiwane rozmiary, zobacz [artykuł dotyczący koncepcyjnych](disks-shared.md)zagadnień.

**Jeśli mam istniejący dysk, czy mogę włączyć na nim dyski udostępnione?**

Wszystkie dyski zarządzane utworzone przy użyciu interfejsu API w wersji 2019-07-01 lub nowszej mogą umożliwić udostępnianie dysków. W tym celu należy odinstalować dysk ze wszystkich maszyn wirtualnych, do których jest dołączony. Następnie Edytuj Właściwość **maxShares** na dysku.

**Jeśli nie chcesz już używać dysku w trybie udostępniania, jak go wyłączyć?**

Odinstaluj dysk ze wszystkich maszyn wirtualnych, do których jest dołączony. Następnie zmień właściwość maxShare na dysku na 1.

**Czy można zmienić rozmiar dysku udostępnionego?**

Tak.

**Czy mogę włączyć akcelerator zapisu na dysku, na którym również są włączone udostępnione dyski?**

Nie.

**Czy mogę włączyć buforowanie hosta dla dysku z włączonym dyskiem udostępnionym?**

Jedyną obsługiwaną opcją buforowania hosta jest **none**.

## <a name="ultra-disks"></a>Dyski w warstwie Ultra

**Jak należy ustawić moją przepływność na dysku?**
Jeśli nie masz pewności, w jaki sposób ustawić przepływność dysku, zalecamy rozpoczęcie od zagwarantowania, że rozmiar we/wy wynoszący 16 KiB i dostosowanie wydajności w trakcie monitorowania aplikacji. Formuła: przepływność w MB/s = liczba operacji we/wy * 16/1000.

**Mam skonfigurowany dysk do 40000 IOPS, ale widzę tylko 12800 operacji we/wy na sekundę, dlaczego nie widzę wydajności dysku?**
Oprócz ograniczenia dysku istnieje ograniczenie we/wy, które jest nakładane na poziomie maszyny wirtualnej. Upewnij się, że rozmiar maszyny wirtualnej, z której korzystasz, może obsługiwać poziomy skonfigurowane na dyskach. Aby uzyskać szczegółowe informacje dotyczące limitów we/wy narzuconych przez maszynę wirtualną, zobacz [rozmiary maszyn wirtualnych na platformie Azure](sizes.md).

**Czy mogę użyć poziomów buforowania z dyskiem o bardzo wysokiej rozdzielczości?**
Nie, Ultra disks nie obsługuje różnych metod buforowania, które są obsługiwane przez inne typy dysków. Ustaw buforowanie dysku na **Brak**.

**Czy mogę dołączyć dysk Ultra do istniejącej maszyny wirtualnej?**
Może to być, że maszyna wirtualna musi znajdować się w stacji i strefie dostępności, która obsługuje Ultra Disks. Aby uzyskać szczegółowe informacje, zobacz Wprowadzenie do usługi [Ultra disks](disks-enable-ultra-ssd.md) .

**Czy mogę użyć dyskietki jako dysku systemu operacyjnego dla mojej maszyny wirtualnej?**
Nie. niezwykle dyski są obsługiwane tylko jako dyski danych i są obsługiwane tylko jako dyski natywne 4 k.

**Czy mogę przekonwertować istniejący dysk na dysk?**
Nie, ale można migrować dane z istniejącego dysku na dysk Ultra. Aby przeprowadzić migrację istniejącego dysku na dysk, podłącz oba dyski do tej samej maszyny wirtualnej i skopiuj dane dysku z jednego dysku do drugiego lub Skorzystaj z rozwiązania innej firmy do migracji danych.

**Czy można tworzyć migawki dla Ultra disks?**
Nie, migawki nie są jeszcze dostępne.

**Czy jest Azure Backup dostępne dla Ultra disks?**
Nie, pomoc techniczna Azure Backup nie jest jeszcze dostępna.

**Czy mogę dołączyć dysk Ultra do maszyny wirtualnej działającej w zestawie dostępności?**
Nie, to nie jest jeszcze obsługiwane.

**Czy mogę włączyć Azure Site Recovery dla maszyn wirtualnych przy użyciu usługi Ultra disks?**
Nie, Azure Site Recovery nie jest jeszcze obsługiwana dla Ultra Disks.

## <a name="uploading-to-a-managed-disk"></a>Przekazywanie na dysk zarządzany

**Czy mogę przekazać dane na istniejący dysk zarządzany?**

Nie, przekazywania można używać tylko podczas tworzenia nowego pustego dysku ze stanem **ReadyToUpload** .

**Jak mogę przekazać do dysku zarządzanego?**

Utwórz dysk zarządzany [z właściwością creationData o wartości](/rest/api/compute/disks/createorupdate#diskcreateoption) " [creationData](/rest/api/compute/disks/createorupdate#creationdata) upload", a następnie możesz przekazać do niej dane.

**Czy mogę dołączyć dysk do maszyny wirtualnej, gdy jest ona w stanie przekazywania?**

Nie.

**Czy mogę zrobić migawkę dysku z pamięcią podpisaną w stanie przekazywania?**

Nie.

## <a name="standard-ssd-disks"></a>SSD w warstwie Standardowa dyski

**Co to jest Azure SSD w warstwie Standardowa disks?**
Dyski SSD w warstwie Standardowa są dyskami standardowymi, które są obsługiwane przez nośnik półprzewodnikowy, zoptymalizowane pod kątem oszczędnego magazynu dla obciążeń wymagających spójnej wydajności na niższych poziomach IOPS.

<a id="standard-ssds-azure-regions"></a>**Jakie regiony są obecnie obsługiwane dla SSD w warstwie Standardowa dysków?**
Wszystkie regiony platformy Azure obsługują teraz SSD w warstwie Standardowa dysków.

**Czy jest Azure Backup dostępne w przypadku korzystania ze standardowego dysków SSD?**
Tak, Azure Backup jest teraz dostępny.

**Jakie korzyści przynosi korzystanie z SSD w warstwie Standardowa dysków zamiast DYSKowych?**
SSD w warstwie Standardowa dyski zapewniają lepsze opóźnienia, spójność, dostępność i niezawodność w porównaniu z dyskami TWARDYmi. Obciążenia aplikacji działają znacznie bardziej płynnie w SSD w warstwie Standardowa z tego powodu. Należy pamiętać, że dyski SSD w warstwie Premium są zalecanym rozwiązaniem dla większości obciążeń produkcyjnych intensywnie korzystających z operacji we/wy.

**Czy można używać standardowych dysków SSD jako dysków niezarządzanych?**
Nie, standardowe dyski dysków SSD są dostępne tylko jako Managed Disks.

## <a name="migrate-to-managed-disks"></a>Migrowanie do funkcji Dyski zarządzane

**Czy istnieje jakikolwiek wpływ migracji na wydajność Managed Disks?**

Migracja obejmuje przenoszenie dysku z jednej lokalizacji magazynu do innej. Jest on zorganizowany za pośrednictwem kopii w tle danych, co może potrwać kilka godzin, zwykle krótszy niż 24 godziny, w zależności od ilości danych na dyskach. W tym czasie aplikacja może korzystać z wyższego poziomu niż zwykłe opóźnienie odczytu, ponieważ niektóre odczyty mogą zostać przekierowane do oryginalnej lokalizacji i może trwać dłużej. Nie ma to wpływu na opóźnienie zapisu w tym okresie.  

**Jakie zmiany są wymagane w istniejącej konfiguracji usługi Azure Backup przed migracją do Managed Disks?**

Nie są wymagane żadne zmiany.

**Czy moje kopie zapasowe maszyn wirtualnych utworzone za pośrednictwem usługi Azure Backup przed kontynuowaniem migracji?**

Tak, kopie zapasowe działają bezproblemowo.

**Jakie zmiany są wymagane we wstępnie istniejącej konfiguracji szyfrowania dysków Azure przed migracją do Managed Disks?**

Nie są wymagane żadne zmiany.

**Czy zautomatyzowana migracja istniejącego zestawu skalowania maszyn wirtualnych z dysków niezarządzanych do Managed Disks obsługiwana?**

Nie. Nowy zestaw skalowania można utworzyć przy użyciu Managed Disks obrazu ze starego zestawu skalowania z dyskami niezarządzanymi.

**Czy można utworzyć dysk zarządzany na podstawie migawki obiektu BLOB strony wykonanej przed migracją do Managed Disks?**

Nie. Migawkę obiektu BLOB stronicowania można wyeksportować jako elementowy obiekt BLOB, a następnie utworzyć dysk zarządzany na podstawie wyeksportowanego obiektu BLOB strony.

**Czy mogę przejść do trybu failover maszyn lokalnych chronionych przez Azure Site Recovery na maszynę wirtualną z Managed Disks?**

Tak, możesz wybrać opcję przejścia do trybu failover z maszyną wirtualną przy użyciu Managed Disks.

**Czy istnieje jakikolwiek wpływ migracji na maszyny wirtualne platformy Azure chronione przez Azure Site Recovery za pośrednictwem platformy Azure do replikacji platformy Azure?**

Nie. Dostępne są Azure Site Recovery ochrony platformy Azure na platformie Azure dla maszyn wirtualnych z Managed Disks.

**Czy można migrować maszyny wirtualne z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które zostały wcześniej zaszyfrowane do dysków zarządzanych?**

Tak

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks i szyfrowanie usługi Storage

**Czy szyfrowanie po stronie serwera jest domyślnie włączone podczas tworzenia dysku zarządzanego?**

Tak. Managed Disks są szyfrowane za pomocą szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez platformę. 

**Czy wolumin rozruchowy jest szyfrowany domyślnie na dysku zarządzanym?**

Tak. Domyślnie wszystkie dyski zarządzane są szyfrowane, łącznie z dyskiem systemu operacyjnego.

**Kto zarządza kluczami szyfrowania?**

Klucze zarządzane przez platformę są zarządzane przez firmę Microsoft. Możesz również użyć własnych kluczy przechowywanych w Azure Key Vault i zarządzać nimi. 

**Czy można wyłączyć szyfrowanie po stronie serwera dla moich dysków zarządzanych?**

Nie.

**Czy szyfrowanie po stronie serwera jest dostępne tylko w określonych regionach?**

Nie. Szyfrowanie po stronie serwera z kluczami zarządzanymi przez platformę i klienta są dostępne we wszystkich regionach, w których Managed Disks są dostępne. 

**Czy Azure Site Recovery obsługuje szyfrowanie po stronie serwera z kluczem zarządzanym przez klienta na potrzeby scenariuszy odzyskiwania po awarii platformy Azure i platformy Azure do platformy Azure?**

Tak. 

**Czy można tworzyć kopie zapasowe Managed Disks szyfrowane za pomocą szyfrowania po stronie serwera z kluczem zarządzanym przez klienta za pomocą usługi Azure Backup?**

Tak.

**Czy zarządzane migawki i obrazy są szyfrowane?**

Tak. Wszystkie zarządzane migawki i obrazy są szyfrowane automatycznie. 

**Czy mogę przekonwertować maszyny wirtualne z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które zostały wcześniej zaszyfrowane do dysków zarządzanych?**

Tak

**Czy plik wirtualnego dysku twardego z dysku zarządzanego lub migawki zostanie również zaszyfrowany?**

Nie. Jednak w przypadku eksportowania dysku VHD na zaszyfrowane konto magazynu z zaszyfrowanego dysku zarządzanego lub migawki jest on szyfrowany. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dyski w warstwie Premium: zarządzane i niezarządzane

**Jeśli maszyna wirtualna korzysta z serii rozmiarów, która obsługuje SSD w warstwie Premium dysków, takich jak DSv2, czy można dołączyć dyski danych w warstwie Premium i Standardowa?** 

Tak.

**Czy można dołączyć dyski danych w warstwie Premium i standardowa do serii rozmiarów, która nie obsługuje SSD w warstwie Premium dysków, takich jak D, Dv2, G czy Seria F?**

Nie. Do maszyn wirtualnych można dołączać tylko standardowe dyski danych, które nie używają serii rozmiaru obsługującej SSD w warstwie Premium dysków.

**Jeśli utworzysz dysk danych w warstwie Premium na podstawie istniejącego wirtualnego dysku twardego o 80 GB, ile będzie kosztować?**

Dysk danych w warstwie Premium utworzony na podstawie dysku VHD 80 GB jest traktowany jako kolejny dostępny rozmiar dysku Premium, który jest dyskiem P10. Opłata jest naliczana zgodnie z cennikiem dysku P10.

**Czy istnieją koszty transakcji do korzystania z SSD w warstwie Premium dysków?**

Istnieje stały koszt dla każdego rozmiaru dysku, który jest dostarczany z określonymi limitami operacji we/wy i przepływności. Pozostałe koszty to wychodząca przepustowość i pojemność migawki, jeśli ma to zastosowanie. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jakie są limity dla operacji we/wy i przepływności, które mogę pobrać z pamięci podręcznej dysku?**

Łączne limity dla pamięci podręcznej i lokalnego SSD dla serii DS są 4 000 operacji we/wy na rdzeń i 33 MiB na sekundę na rdzeń. Seria GS oferuje 5 000 operacji we/wy na rdzeń i 50 MiB na sekundę na rdzeń.

**Czy na maszynie wirtualnej Managed Disks jest obsługiwany lokalny dysk SSD?**

Lokalny dysk SSD jest magazynem tymczasowym, który jest dołączony do maszyny wirtualnej Managed Disks. Ten magazyn tymczasowy nie ma dodatkowych kosztów. Zalecamy, aby nie używać tego lokalnego dysku SSD do przechowywania danych aplikacji, ponieważ nie jest on utrwalany w usłudze Azure Blob Storage.

**Czy istnieją jakiekolwiek konsekwencje użycia przycinania na dyskach w warstwie Premium?**

Na dyskach w warstwie Premium lub standard nie ma Minusem do użycia opcji TRIM na dyskach platformy Azure.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nowe rozmiary dysków: zarządzane i niezarządzane

**Jakie regiony obsługują możliwości tworzenia pojemności dla odpowiedniego rozmiaru dysku SSD w warstwie Premium?**

Możliwość tworzenia serii jest obecnie obsługiwana we wszystkich regionach w chmurze publicznej platformy Azure, dzięki czemu wkrótce będzie dostępna obsługa dla suwerennych chmur. 

**Które regiony są 4/8/16 GiB rozmiary dysków zarządzanych (P1/P2/P3, E1/E2/E3) obsługiwane w?**

Te nowe rozmiary dysków są obecnie obsługiwane we wszystkich regionach w chmurze publicznej platformy Azure z obsługą dla suwerennych chmur wkrótce. 

**Czy rozmiary dysków P1/P2/P3 są obsługiwane dla dysków niezarządzanych lub stronicowych obiektów BLOB?**

Nie, jest obsługiwana tylko w przypadku dysków zarządzanych SSD w warstwie Premium. 

**Czy rozmiary dysków E1/E2/E3 są obsługiwane dla dysków niezarządzanych lub stronicowych obiektów BLOB?**

Nie, nie można używać standardowego dysku Managed SSD o dowolnym rozmiarze z dyskami niezarządzanymi ani stronicowymi obiektami BLOB.

**Jaki jest największy obsługiwany rozmiar dysku zarządzanego dla systemu operacyjnego i dysków z danymi na maszynach wirtualnych Gen1?**

Typ partycji obsługiwanej przez platformę Azure dla dysków systemu operacyjnego Gen1 jest głównym rekordem rozruchowym (MBR). Dyski systemu operacyjnego Gen1 obsługują tylko dyski MBR obsługujące GPT. Chociaż można przydzielić maksymalnie 4 TiB dyskiem systemu operacyjnego, typ partycji MBR może używać maksymalnie 2 TiB tego miejsca na dysku dla systemu operacyjnego. Platforma Azure obsługuje do 32 TiB dla zarządzanych dysków danych.

**Jaki jest największy obsługiwany rozmiar dysku zarządzanego dla systemu operacyjnego i dysków z danymi na maszynach wirtualnych Gen2?**

Typ partycji obsługiwanej przez platformę Azure dla dysków systemu operacyjnego Gen2 to tabela partycji GUID (GPT). Maszyny wirtualne Gen2 obsługują do 4 TiB systemu operacyjnego. Platforma Azure obsługuje do 32 TiB dla zarządzanych dysków danych.


**Jaki jest największy niezarządzany rozmiar dysku dla systemu operacyjnego i dysków z danymi?**

Typ partycji obsługiwanej przez platformę Azure dla dysku systemu operacyjnego przy użyciu dysków niezarządzanych jest głównym rekordem rozruchowym (MBR).  Chociaż można przydzielić maksymalnie 4 TiB dyskiem systemu operacyjnego, typ partycji MBR może używać maksymalnie 2 TiB tego miejsca na dysku dla systemu operacyjnego. Platforma Azure obsługuje maksymalnie 4 TiB dla niezarządzanych dysków danych.


**Jaki jest największy rozmiar obiektu BLOB stronicowania, który jest obsługiwany?**

Największy rozmiar obiektu BLOB strony obsługiwany przez platformę Azure to 8 TiB (8 191 GiB). Maksymalny rozmiar obiektu BLOB strony w przypadku dołączenia do maszyny wirtualnej jako dysk z danymi lub systemem operacyjnym to 4 TiB (4 095 GiB).

**Czy muszę używać nowej wersji narzędzi platformy Azure do tworzenia, dołączania, zmiany rozmiaru i przekazywania dysków większych niż 1 TiB?**

Nie musisz uaktualniać istniejących narzędzi platformy Azure, aby tworzyć, dołączać lub zmieniać rozmiaru dysków większych niż 1 TiB. Aby przekazać plik VHD z lokalnego miejsca bezpośrednio do platformy Azure jako obiekt BLOB strony lub dysk niezarządzany, należy użyć najnowszych zestawów narzędzi wymienionych poniżej. Obsługujemy tylko przekazywanie wirtualnego dysku twardego do 8 TiB.

|Narzędzia platformy Azure      | Obsługiwane wersje                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numer wersji 4.1.0: wydanie 2017 czerwca lub nowszej|
|Interfejs wiersza polecenia platformy Azure w wersji 1     | Numer wersji 0.10.13:2017 maja lub nowszej|
|Interfejs wiersza polecenia platformy Azure w wersji 2     | Numer wersji 2.0.12: wydanie 2017 lipca lub nowszej|
|AzCopy              | Numer wersji 6.1.0: wydanie 2017 czerwca lub nowszej|

**Czy rozmiary dysków P4 i P6 są obsługiwane w przypadku dysków niezarządzanych lub stronicowych obiektów BLOB?**

Rozmiary dysków P4 (32 GiB) i P6 (64 GiB) nie są obsługiwane jako domyślne warstwy dysków dla dysków niezarządzanych i stronicowych obiektów BLOB. Należy jawnie [ustawić warstwę obiektów BLOB](/rest/api/storageservices/set-blob-tier) na P4 i P6, aby dysk był mapowany na te warstwy. W przypadku wdrażania niezarządzanego dysku lub stronicowego obiektu BLOB przy użyciu rozmiaru dysku lub zawartości mniejszej niż 32 GiB lub między 32 GiB do 64 GiB bez ustawiania warstwy obiektów blob, będzie można nadal korzystać z P10 z usługami 500 IOPS i 100 MiB/s oraz z zamapowanej warstwy cenowej.

**Jeśli mój istniejący dysk zarządzany w warstwie Premium o rozmiarze mniejszym niż 64 GiB został utworzony przed włączeniem małego dysku (około 15 czerwca 2017), jak jest rozliczany?**

Istniejące dyski małego Premium mniejsze niż 64 GiB nadal są rozliczane zgodnie z warstwą cenową P10.

**Jak można zmienić warstwę dysku małych dysków Premium mniejszych niż 64 GiB z P10 na P4 lub P6?**

Możesz wykonać migawkę małych dysków, a następnie utworzyć dysk w celu automatycznego przełączenia warstwy cenowej na P4 lub P6 na podstawie rozmiaru aprowizacji.

**Czy można zmienić rozmiar istniejących Managed Disks od rozmiaru mniejszej niż 4 tebibajtów (TiB), aby nowe nowo wprowadzone rozmiary dysku do 32 TiB?**

Tak.

**Jakie są największe rozmiary dysków obsługiwane przez usługi Azure Backup i Azure Site Recovery?**

Największy rozmiar dysku obsługiwany przez Azure Backup to 32 TiB (4 TiB dla szyfrowanych dysków). Największy rozmiar dysku obsługiwany przez Azure Site Recovery to 8 TiB. Obsługa większych dysków do 32 TiB nie jest jeszcze dostępna w Azure Site Recovery.

**Jakie są zalecane rozmiary maszyn wirtualnych dla większych rozmiarów dysków (>4 TiB) dla dysków SSD w warstwie Standardowa i HDD w warstwie Standardowa w celu osiągnięcia zoptymalizowanych operacji we/wy na dysku?**

Aby osiągnąć przepływność dysków SSD w warstwie Standardowa i HDD w warstwie Standardowa duże rozmiary dysków (>4 TiB) poza 500 IOPS i 60 MiB/s, zalecamy wdrożenie nowej maszyny wirtualnej na podstawie jednego z następujących rozmiarów maszyn wirtualnych w celu zoptymalizowania wydajności: serii B, serii DSv2, serii Dsv3, ESv3 serii, serii FS, Seria Fsv2, serii M, Seria GS, Seria NCv2, Seria Seria NCV3 lub maszyny wirtualne z serii LS. Dołączanie dużych dysków do istniejących maszyn wirtualnych lub maszyn wirtualnych, które nie używają zalecanych rozmiarów, może obniżyć wydajność.

**Jak uaktualnić moje dyski (>4 TiB), które zostały wdrożone podczas większego rozmiaru dysku w wersji zapoznawczej w celu uzyskania większej liczby operacji we/wy & przepustowości?**

Można zatrzymać i uruchomić maszynę wirtualną, do której jest dołączony dysk, lub odłączyć i ponownie dołączyć dysk. Wydajność większych rozmiarów dysków została zwiększona zarówno w przypadku wersji Premium dysków SSD, jak i standardowej dysków SSD.

**Których regionów są rozmiary dysków zarządzanych 8 TiB, 16 TiB i 32 TiB obsługiwane w?**

8 TiB, 16 TiB i 32 jednostek SKU dysku TiB są obsługiwane we wszystkich regionach w ramach globalnych platform Azure, Microsoft Azure Government i Azure Chiny 21Vianet.

**Czy obsługujemy Włączanie buforowania hosta dla wszystkich rozmiarów dysków?**

Buforowanie hosta (**ReadOnly** i **Odczyt/zapis**) jest obsługiwane na dyskach o rozmiarze mniejszym niż 4 TIB. Oznacza to, że każdy dysk o pojemności do 4095 GiB może korzystać z buforowania hosta. Buforowanie hosta nie jest obsługiwane w przypadku dysków o rozmiarze większym lub równym 4096 GiB. Na przykład dysk P50 Premium zainicjowany w 4095 GiB może korzystać z pamięci podręcznej hosta, a dysk P50 z obsługą administracyjną na 4096 GiB nie może korzystać z buforowania hosta. Zalecamy korzystanie z pamięci podręcznej w przypadku mniejszych rozmiarów dysków, w których można oczekiwać, że lepsze zwiększenie wydajności w przypadku danych zapisanych w pamięci podręcznej do maszyny wirtualnej.

## <a name="private-links-for-securely-exporting-and-importing-managed-disks"></a>Prywatne linki do bezpiecznego eksportowania i importowania Managed Disks

**Jaka jest korzyść z używania prywatnych linków do eksportowania i importowania Managed Disks?**

Za pomocą prywatnych linków można ograniczyć eksport i import do Managed Disks tylko z sieci wirtualnej platformy Azure. 

**Co mogę zrobić, aby można było wyeksportować lub zaimportować dysk tylko za pośrednictwem prywatnych linków?**

Należy ustawić `DiskAccessId` Właściwość na wystąpienie obiektu dostępu do dysku, a także ustawić właściwość NetworkAccessPolicy na `AllowPrivate` .

**Czy mogę połączyć wiele sieci wirtualnych z tym samym obiektem dostępu do dysku?**

Nie. Obecnie można połączyć obiekt dostępu do dysku tylko z jedną siecią wirtualną.

**Czy można połączyć sieć wirtualną z obiektem dostępu do dysku w innej subskrypcji?**

Nie. Obecnie można połączyć obiekt dostępu do dysku z siecią wirtualną w tej samej subskrypcji.

**Czy można połączyć sieć wirtualną z obiektem dostępu do dysku w innej subskrypcji?**

Nie. Obecnie można połączyć obiekt dostępu do dysku z siecią wirtualną w tej samej subskrypcji.

**Ile eksportu lub importów korzystających z tego samego obiektu dostępu do dysku może nastąpić w tym samym czasie?**

5

**Czy można użyć identyfikatora URI sygnatury dostępu współdzielonego dysku/migawki w celu pobrania bazowego wirtualnego dysku twardego maszyny wirtualnej w tej samej podsieci, w której znajduje się podsieć prywatnego punktu końcowego skojarzonego z dyskiem?**

Tak.

**Czy mogę użyć identyfikatora URI sygnatury dostępu współdzielonego dysku/migawki do pobrania źródłowego wirtualnego dysku twardego maszyny wirtualnej, która nie znajduje się w tej samej podsieci, co podsieć prywatnego punktu końcowego nieskojarzonej z dyskiem?**

Nie.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli w tym miejscu nie udzielono odpowiedzi na moje pytanie?

Jeśli pytania nie ma na liście, poinformuj nas o tym, a my pomożemy Ci znaleźć odpowiedź. W komentarzach można opublikować pytanie na końcu tego artykułu. Aby skontaktować się z zespołem usługi Azure Storage i innymi członkami społeczności dotyczącymi tego artykułu, użyj strony pytań i odpowiedzi [Microsoft&pytań i odpowiedzi na temat usługi Azure Storage](/answers/products/azure?product=storage).

Aby zażądać funkcji, Prześlij swoje wnioski i pomysły do [forum opinii o usłudze Azure Storage](https://feedback.azure.com/forums/217298-storage).