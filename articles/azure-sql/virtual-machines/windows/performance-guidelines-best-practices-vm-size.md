---
title: 'Rozmiar maszyny wirtualnej: Najlepsze rozwiązania dotyczące wydajności & wytycznych'
description: Zawiera wytyczne dotyczące rozmiaru maszyny wirtualnej i najlepsze rozwiązania dotyczące optymalizacji wydajności maszyny SQL Server maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600900"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Rozmiar maszyny wirtualnej: najlepsze rozwiązania dotyczące wydajności SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące rozmiaru maszyny wirtualnej oraz szereg najlepszych rozwiązań i wytycznych dotyczących optymalizacji wydajności SQL Server na platformie Azure Virtual Machines (maszyn wirtualnych).

Zazwyczaj istnieje związek między optymalizacją kosztów i optymalizacją pod celu utrzymania wydajności. Ta seria najlepszych rozwiązań w zakresie wydajności koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, możesz nie wymagać każdej zalecanej optymalizacji. Podczas oceniania tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, koszty i wzorce obciążeń.


## <a name="checklist"></a>Lista kontrolna

Zapoznaj się z następującą listą kontrolną, aby zapoznać się z krótkim omówieniem najlepszych rozwiązań w zakresie rozmiarów maszyn wirtualnych, które szczegółowo opisano w pozostałej części artykułu: 

- Użyj rozmiarów maszyn wirtualnych z 4 lub większą Standard_M8 procesorów wirtualnych, takich jak [Standard_M8–4 ms,](/azure/virtual-machines/m-series)E4ds_v4 [lub](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)DS12_v2 [lub](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) wyższa. 
- Użyj [rozmiarów maszyn wirtualnych zoptymalizowanych](../../../virtual-machines/sizes-memory.md) pod kątem pamięci, aby uzyskać najlepszą wydajność SQL Server obciążeń. 
- Serie [DSv2 11–15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4,](../../../virtual-machines/edv4-edsv4-series.md) [M-](/azure/virtual-machines/m-series)i [Mv2](../../../virtual-machines/mv2-series.md) oferują optymalny stosunek pamięci do liczby rdzeni wirtualnych wymagany dla obciążeń OLTP. Obie maszyny wirtualne serii M oferują najwyższy współczynnik pamięci do liczby rdzeni wirtualnych wymagany dla obciążeń o znaczeniu krytycznym i są idealne dla obciążeń magazynu danych. 
- Weź pod uwagę wyższy współczynnik pamięci do liczby rdzeni wirtualnych dla obciążeń o znaczeniu krytycznym i magazynu danych. 
- Skorzystaj z obrazów z witryny Marketplace maszyny wirtualnej platformy Azure, SQL Server ustawienia magazynu i opcje magazynu są skonfigurowane w celu uzyskania optymalnej SQL Server wydajności. 
- Zbierz charakterystyki wydajności obciążenia docelowego i użyj ich do określenia odpowiedniego rozmiaru maszyny wirtualnej dla Twojej firmy.

Aby porównać listę kontrolną rozmiaru maszyny wirtualnej z innymi, zobacz kompleksową listę kontrolną [najlepszych rozwiązań w zakresie wydajności.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Omówienie

Podczas tworzenia maszyny wirtualnej SQL Server azure należy dokładnie rozważyć typ wymaganego obciążenia. W przypadku migrowania istniejącego środowiska zbierz punkt odniesienia [wydajności,](performance-guidelines-best-practices-collect-baseline.md) aby określić, SQL Server wymagań maszyny wirtualnej platformy Azure. Jeśli jest to nowa maszyna wirtualna, utwórz nową maszynę wirtualną SQL Server na podstawie wymagań dostawcy. 

Jeśli tworzysz nową maszynę wirtualną SQL Server z nową aplikacją sbudowaną dla chmury, możesz łatwo zmieniać rozmiar maszyny wirtualnej SQL Server, gdy zmieniają się wymagania dotyczące danych i użycia.
Rozpocznij środowiska programowe od niższej warstwy serii D, B lub Av2 i rozwijaj swoje środowisko wraz z czasem. 

Użyj obrazu SQL Server witryny Marketplace z konfiguracją magazynu w portalu. Ułatwi to prawidłowe tworzenie pul magazynów niezbędnych do uzyskania rozmiaru, ilości i przepływności wymaganej do obsługi obciążeń. Ważne jest, aby wybrać SQL Server wirtualne, które obsługują magazyn Premium Storage i buforowanie magazynu w chmurze Premium Storage. Zobacz artykuł [magazynu,](performance-guidelines-best-practices-storage.md) aby dowiedzieć się więcej. 

Zalecanym minimum dla środowiska produkcyjnego OLTP są 4 rdzenie wirtualne, 32 GB pamięci i stosunek pamięci do liczby rdzeni wirtualnych na 8. W przypadku nowych środowisk rozpocznij od 4 maszyn wirtualnych i przeskaluj do 8, 16, 32 rdzeni wirtualnych lub więcej, gdy zmienią się wymagania dotyczące danych i obliczeń. W przypadku przepływności OLTP należy SQL Server maszyn wirtualnych, które mają 5000 IOPS na każdy rdzeń wirtualnych. 

SQL Server magazynu danych i środowisk o krytycznym znaczeniu często będzie konieczne skalowanie poza 8 współczynników pamięci do liczby rdzeni wirtualnych. W przypadku średnich środowisk warto wybrać stosunek pamięci do liczby rdzeni wirtualnych 16 i 32 pamięci na rdzeń wirtualne w przypadku większych środowisk magazynu danych. 

SQL Server magazynów danych często korzystają z przetwarzania równoległego większych maszyn. Z tego powodu serie M i Mv2 są mocnymi opcjami dla większych środowisk magazynu danych.

Użyj konfiguracji procesorów wirtualnych i pamięci z maszyny źródłowej jako linii bazowej do migrowania bieżącej lokalnej bazy danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure. Przynieź podstawową licencję na platformę Azure, aby korzystać z Korzyść użycia hybrydowego platformy Azure [i](https://azure.microsoft.com/pricing/hybrid-benefit/) oszczędzać na SQL Server licencjonowania.

## <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

Rozmiary [maszyn wirtualnych zoptymalizowanych pod](../../../virtual-machines/sizes-memory.md) kątem pamięci są głównym celem SQL Server wirtualnych i zalecanym wyborem przez firmę Microsoft. Maszyny wirtualne zoptymalizowane pod kątem pamięci oferują silniejsze współczynniki pamięci do mocy procesora CPU oraz opcje średnich i dużych pamięci podręcznych. 

### <a name="m-mv2-and-mdsv2-series"></a>Serie M, Mv2 i Mdsv2

Seria [M oferuje](/azure/virtual-machines/m-series) liczbę rdzeni wirtualnych i pamięć dla niektórych największych obciążeń SQL Server wirtualnych.  

Seria [Mv2 ma najwyższą](../../../virtual-machines/mv2-series.md) liczbę rdzeni wirtualnych i pamięć i jest zalecana dla obciążeń o znaczeniu krytycznym i magazynu danych. Wystąpienia maszyn wirtualnych z serii Mv2 to maszyny wirtualne zoptymalizowane pod kątem pamięci, które zapewniają niezrównaną wydajność obliczeniową na potrzeby obsługi dużych baz danych i obciążeń w pamięci o wysokim stosunku pamięci do procesora CPU, co doskonale nadaje się do obsługi serwerów relacyjnych baz danych, dużych pamięci podręcznych i analizy w pamięci.

Wynik [Standard_M64ms](/azure/virtual-machines/m-series) na przykład 28 rdzeni wirtualnych.

[Seria Mdsv2 Medium Memory](../../..//virtual-machines/msv2-mdsv2-series.md) to nowa seria [](https://aka.ms/Mv2MedMemoryPreview) M, która jest obecnie w wersji zapoznawczej, która oferuje szereg maszyn wirtualnych platformy Azure na poziomie serii M z pamięcią średnią. Te maszyny są odpowiednie dla SQL Server obciążeń z co najmniej 10 pamięcią na rdzeń vCore do 30.

Niektóre funkcje serii M i Mv2 atrakcyjne dla wydajności usługi [](../../../virtual-machines/premium-storage-performance.md) SQL Server [](../../../virtual-machines/premium-storage-performance.md#disk-caching) obejmują obsługę magazynu w chmurze Premium Storage i magazynu Premium [Storage,](../../../virtual-machines/disks-enable-ultra-ssd.md) obsługę dysków ultra i przyspieszanie [zapisu.](../../../virtual-machines/how-to-enable-write-accelerator.md)

### <a name="edsv4-series"></a>Seria Edsv4

Seria [Edsv4 jest](../../../virtual-machines/edv4-edsv4-series.md) przeznaczona dla aplikacji intensywnie obciążanych pamięcią. Te maszyny wirtualne mają dużą pojemność dysku SSD magazynu lokalnego, dużą liczbę we/wy na dysku lokalnym, do 504 GiB pamięci RAM. Większość tych maszyn wirtualnych ma niemal spójny rozmiar 8 GiB pamięci na rdzeń wirtualny, co jest idealnym rozwiązaniem dla standardowych SQL Server wirtualnych. 

W tej grupie znajduje się nowa maszyna wirtualna z maszyną [wirtualną Standard_E80ids_v4,](../../../virtual-machines/edv4-edsv4-series.md) która oferuje 80 rdzeni wirtualnych, 504 GB pamięci i współczynnik pamięci na rdzeń wirtualny wynosi 6. Ta maszyna wirtualna jest [](../../../virtual-machines/isolation.md) notowana, ponieważ jest izolowana, co oznacza, że jest gwarantowana jako jedyna maszyna wirtualna uruchomiona na hoście i dlatego jest odizolowana od innych obciążeń klientów. Ma ona stosunek pamięci do liczby rdzeni wirtualnych, który jest niższy niż zalecany dla SQL Server, dlatego należy go używać tylko wtedy, gdy izolacja jest wymagana.

Maszyny wirtualne serii Edsv4 obsługują magazyn [Premium Storage](../../../virtual-machines/premium-storage-performance.md)i [buforowanie magazynu w chmurze w chmurze Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching)Storage.

### <a name="dsv2-series-11-15"></a>Seria DSv2 11–15

Seria [DSv2 11–15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ma takie same konfiguracje pamięci i dysków jak poprzednia seria D. Ta seria ma spójny stosunek pamięci do procesora CPU na 7 we wszystkich maszynach wirtualnych. Jest to najmniejsza z serii zoptymalizowanych pod kątem pamięci i jest dobrą opcją niskich kosztów dla obciążeń SQL Server wejścia.

Seria [DSv2 11–15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) obsługuje magazyn w chmurze [Premium](../../../virtual-machines/premium-storage-performance.md) Storage i buforowanie magazynu w chmurze [Premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching)co jest zdecydowanie zalecane w celu uzyskania optymalnej wydajności.

## <a name="general-purpose"></a>Ogólnego przeznaczenia

Rozmiary [maszyn](../../../virtual-machines/sizes-general.md) wirtualnych ogólnego przeznaczenia zaprojektowano w celu zapewnienia zrównoważonych proporcji pamięci do rdzeni wirtualnych dla mniejszych obciążeń na poziomie wejściowym, takich jak tworzenie i testowanie, serwery internetowe i mniejsze serwery baz danych. 

Ze względu na mniejsze współczynniki pamięci do liczby rdzeni wirtualnych w przypadku maszyn wirtualnych ogólnego przeznaczenia ważne jest dokładne monitorowanie liczników wydajności opartych na pamięci w celu zapewnienia, że program SQL Server może uzyskać potrzebną pamięć podręczną bufora. Aby uzyskać [więcej informacji, zobacz punkt odniesienia](performance-guidelines-best-practices-collect-baseline.md#memory) wydajności pamięci. 

Ponieważ zaleceniem początkowym dla obciążeń produkcyjnych jest stosunek pamięci do liczby rdzeni wirtualnych 8, minimalna zalecana konfiguracja dla maszyny wirtualnej ogólnego przeznaczenia z systemem SQL Server to 4 procesory wirtualne i 32 GB pamięci. 

### <a name="ddsv4-series"></a>Seria DDSV4

Seria [Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferuje sprawiedliwe połączenie procesorów wirtualnych, pamięci i dysku tymczasowego, ale z mniejszą obsługą pamięci do rdzeni wirtualnych. 

Maszyny wirtualne Ddsv4 obejmują mniejsze opóźnienia i magazyn lokalny o większej szybkości.

Te maszyny doskonale nadają się do wdrożeń aplikacji i sql-by-side, które wymagają szybkiego dostępu do magazynu tymczasowego i relacyjnych baz danych relacyjnych działu. Istnieje standardowy stosunek pamięci do liczby rdzeni wirtualnych na poziomie 4 na wszystkich maszynach wirtualnych w tej serii. 

Z tego powodu zaleca się używanie maszyny wirtualnej D8ds_v4 jako maszyny wirtualnej startowej w tej serii, która ma 8 rdzeni wirtualnych i 32 GB pamięci. Największa maszyna to maszyna D64ds_v4, która ma 64 rdzenie wirtualne i 256 GB pamięci.

Maszyny [wirtualne z serii Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) obsługują magazyn w chmurze Premium [Storage](../../../virtual-machines/premium-storage-performance.md) i [buforowanie magazynu w chmurze Premium Storage.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> Seria [Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) nie ma współczynnika pamięci do liczby rdzeni wirtualnych 8, co jest zalecane SQL Server obciążeniami. W związku z tym rozważanie użycia tych maszyn wirtualnych tylko w przypadku mniejszych obciążeń aplikacji i programowych.

### <a name="b-series"></a>Seria B

Rozmiary [maszyn wirtualnych](../../../virtual-machines/sizes-b-series-burstable.md) serii B z serii B z serii burst są idealne dla obciążeń, które nie wymagają spójnej wydajności, takich jak proof of concept oraz bardzo małych serwerów aplikacji i developmentowych. 

Większość rozmiarów maszyn [wirtualnych serii B](../../../virtual-machines/sizes-b-series-burstable.md) z serii B z serii B ma współczynnik pamięci na rdzeń wirtualny 4. Największa z tych maszyn to [maszyna Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 20 rdzeni wirtualnych i 80 GB pamięci.

Ta seria jest unikatowa, ponieważ  aplikacje mogą się rozsyłać w godzinach pracy, a środki z możliwością serii różnią się w zależności od rozmiaru maszyny. 

Po wyczerpaniu środków maszyna wirtualna wraca do podstawowej wydajności maszyny.

Zaletą serii B jest oszczędność zasobów obliczeniowych, które można osiągnąć w porównaniu z innymi rozmiarami maszyn wirtualnych w innych seriach, zwłaszcza jeśli moc obliczeniowa jest potrzebna oszczędnie przez cały dzień.

Ta seria obsługuje [magazyn w chmurze Premium](../../../virtual-machines/premium-storage-performance.md)Storage, ale nie **obsługuje** [buforowania magazynu w chmurze w chmurze Premium Storage.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> Seria [B z](../../../virtual-machines/sizes-b-series-burstable.md) serii B z serii z serii burst nie ma współczynnika pamięci do liczby rdzeni wirtualnych 8, co jest zalecane SQL Server obciążeniami. W związku z tym należy rozważyć użycie tych maszyn wirtualnych tylko w przypadku mniejszych aplikacji, serwerów internetowych i obciążeń programistów.

### <a name="av2-series"></a>Seria Av2

Maszyny wirtualne [z serii Av2](../../../virtual-machines/av2-series.md) najlepiej nadają się do obciążeń na poziomie wejściowym, takich jak tworzenie i testowanie, serwery internetowe o niskim natężeniu ruchu, bazy danych małych i średnich aplikacji oraz weryfikacje koncepcji.

Tylko [](../../../virtual-machines/av2-series.md) Standard_A2m_v2 (2 rdzenie wirtualne i 16 GB pamięci), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 rdzenie wirtualne i 32 GB pamięci) oraz Standard_A8m_v2 [(8](../../../virtual-machines/av2-series.md) rdzeni wirtualnych i 64 GB pamięci) mają dobry współczynnik pamięci do liczby rdzeni wirtualnych 8 dla tych trzech maszyn wirtualnych. 

Te maszyny wirtualne są dobrymi opcjami w przypadku mniejszych maszyn SQL Server testowania. 

8 rdzeni wirtualnych [Standard_A8m_v2](../../../virtual-machines/av2-series.md) może być również dobrą opcją dla małych aplikacji i serwerów internetowych.

> [!NOTE] 
> Seria Av2 nie obsługuje magazynu w chmurze Premium i w związku z tym nie jest zalecana w przypadku obciążeń produkcyjnych SQL Server nawet w przypadku maszyn wirtualnych, które mają stosunek pamięci do liczby rdzeni wirtualnych o wartości 8.

## <a name="storage-optimized"></a>Optymalizacja pod kątem magazynu

Rozmiary [maszyn wirtualnych zoptymalizowanych pod kątem magazynu](../../../virtual-machines/sizes-storage.md) są specyficzne dla konkretnych przypadków użycia. Te maszyny wirtualne zostały zaprojektowane specjalnie z myślą o zoptymalizowanej przepływności dysku i we/wy. 

### <a name="lsv2-series"></a>Seria Lsv2

Seria [Lsv2 oferuje](../../../virtual-machines/lsv2-series.md) wysoką przepływność, małe opóźnienia i lokalny magazyn NVMe. Maszyny wirtualne serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej, zamiast używania trwałych dysków danych. 

Te maszyny wirtualne to silne opcje dla obciążeń danych big data, magazynu danych, raportowania i ETL. Wysoka przepływność i liczba IOPS lokalnego magazynu NVMe jest dobrym rozwiązaniem do przetwarzania plików, które zostaną załadowane do bazy danych, oraz innych scenariuszy, w których dane można odtworzyć z systemu źródłowego lub innych repozytoriów, takich jak usługa Azure Blob Storage lub Azure Data Lake. [Seria Lsv2](../../../virtual-machines/lsv2-series.md) Maszyny wirtualne mogą również podniesieć wydajność dysku nawet o 30 minut na raz.

Te maszyny wirtualne mają rozmiar od 8 do 80 procesorów wirtualnych z 8 GiB pamięci na procesor wirtualny, a każdy 8 procesorów wirtualnych ma dysk SSD NVMe o pojemności 1,92 TB. Oznacza [to,](../../../virtual-machines/lsv2-series.md)że dla największej maszyny wirtualnej z tej serii, L80s_v2 , jest 80 procesorów wirtualnych i 640 BiB pamięci z 10 x 1,92 TB pamięci NVMe.  Istnieje spójny stosunek pamięci do liczby rdzeni wirtualnych na 8 dla wszystkich tych maszyn wirtualnych.

Magazyn NVMe jest efemeralny, co oznacza, że dane zostaną utracone na tych dyskach, jeśli cofniesz alokację maszyny wirtualnej lub jeśli zostaną przeniesione do innego hosta w celu naprawy usługi.

Seria Lsv2 i Ls obsługuje magazyn w chmurze [Premium](../../../virtual-machines/premium-storage-performance.md)Storage, ale nie buforowanie magazynu w chmurze w chmurze Premium Storage. Tworzenie lokalnej pamięci podręcznej w celu zwiększenia liczby we/wy na komputerze nie jest obsługiwane. 

> [!WARNING]
> Przechowywanie plików danych w efemerskim magazynie NVMe może spowodować utratę danych w przypadku cofania alokacji maszyny wirtualnej. 

## <a name="constrained-vcores"></a>Ograniczone rdzenie wirtualne

Obciążenia SQL Server wymagają często większych ilości pamięci, operacji we/wy i przepływności bez większej liczby rdzeni wirtualnych. 

Większość obciążeń OLTP to bazy danych aplikacji sterowane dużą liczby mniejszych transakcji. W przypadku obciążeń OLTP tylko niewielka ilość danych jest odczytywana lub modyfikowana, ale liczba transakcji opartych na liczbach użytkowników jest znacznie większa. Ważne jest, aby mieć dostępną SQL Server pamięci podręcznej, przechowywać ostatnio używane dane w celu zapewnienia wydajności i mieć pewność, że odczyty fizyczne mogą być szybko odczytywane do pamięci. 

Te środowiska OLTP wymagają większej ilości pamięci, szybkiego magazynu i przepustowości we/wy niezbędnej do optymalnego działania. 

Aby utrzymać ten poziom wydajności bez wyższych kosztów licencjonowania SQL Server, platforma Azure oferuje rozmiary maszyn wirtualnych z ograniczoną liczbę [procesorów wirtualnych.](../../../virtual-machines/constrained-vcpu.md) 

Ułatwia to kontrolowanie kosztów licencjonowania przez zmniejszenie liczby dostępnych rdzeni wirtualnych przy zachowaniu tej samej pamięci, magazynu i przepustowości we/wy nadrzędnej maszyny wirtualnej.

Liczbę procesorów wirtualnych można ograniczyć do połowy do jednej czwartej oryginalnego rozmiaru maszyny wirtualnej. Zmniejszenie liczby rdzeni wirtualnych dostępnych dla maszyny wirtualnej spowoduje uzyskanie większej ilości pamięci na rdzeń wirtualny, ale koszt obliczeń pozostanie taki sam.

Te nowe rozmiary maszyn wirtualnych mają sufiks określający liczbę aktywnych procesorów wirtualnych, co ułatwia ich identyfikację. 

Na przykład maszyny [M64–32ms](../../../virtual-machines/constrained-vcpu.md) wymagają licencjonowania tylko 32 rdzeni wirtualnych SQL Server z pamięcią, we/wy i [przepływnością M64 ms,](/azure/virtual-machines/m-series) a maszyny [M64–16ms](../../../virtual-machines/constrained-vcpu.md) wymagają licencjonowania tylko 16 rdzeni wirtualnych.  Chociaż maszyny [M64–16ms](../../../virtual-machines/constrained-vcpu.md) mają kwartał kosztu licencjonowania usługi SQL Server M64ms, koszt obliczeń maszyny wirtualnej będzie taki sam.

> [!NOTE] 
> - Średnie i duże obciążenia magazynu danych mogą nadal korzystać z ograniczonych maszyn wirtualnych z rdzeniami [wirtualnych,](../../../virtual-machines/constrained-vcpu.md)ale obciążenia magazynu danych często charakteryzują się mniejszą liczbą użytkowników i procesów przetwarzanych w przypadku większych ilości danych za pośrednictwem planów zapytań uruchamianych równolegle. 
> - Koszt obliczeń, w tym licencjonowanie systemu operacyjnego, pozostanie taki sam jak nadrzędna maszyna wirtualna. 



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz inne artykuły z tej serii:
- [Szybka lista kontrolna](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Zbieranie punktów odniesienia](performance-guidelines-best-practices-collect-baseline.md)

Najlepsze rozwiązania w zakresie zabezpieczeń można znaleźć w [tesłudze Security considerations for SQL Server on Azure Virtual Machines](security-considerations-best-practices.md)(Zagadnienia dotyczące zabezpieczeń w usłudze Azure Virtual Machines).

Przejrzyj inne artykuły SQL Server Virtual Machine na stronie SQL Server on Azure Virtual Machines Overview (Omówienie usługi [Azure Virtual Machines Virtual Machine).](sql-server-on-azure-vm-iaas-what-is-overview.md) Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md). 
