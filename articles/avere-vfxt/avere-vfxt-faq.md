---
title: Często zadawane pytania — avere vFXT for Azure
description: Użyj tych często zadawanych pytań, aby zdecydować, czy avere vFXT for Azure odpowiada Twoim potrzebom. Dowiedz się, jak avere vFXT dla platformy Azure współpracuje z innymi składnikami platformy Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342369"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure — często zadawane pytania

W tym artykule znajdują się odpowiedzi na pytania, które mogą pomóc w określeniu, czy avere vFXT for Azure są odpowiednie dla Twoich potrzeb. Zawiera podstawowe informacje o avere vFXT i wyjaśniono, jak współpracuje z innymi składnikami platformy Azure i produktami spoza dostawców.

## <a name="general"></a>Ogólne

### <a name="what-is-avere-vfxt-for-azure"></a>Co to jest system Avere vFXT for Azure?

Avere vFXT for Azure to system plików o wysokiej wydajności, który umożliwia buforowanie aktywnych danych na platformie Azure w celu wydajnego przetwarzania krytycznych obciążeń.

### <a name="is-avere-vfxt-a-storage-solution"></a>Czy avere vFXT rozwiązanie do magazynowania?

Nie. Avere vFXT for Azure to *pamięć podręczna* systemu plików, która jest dołączana do środowisk magazynu, takich jak serwer nas EMC lub NetApp lub kontener obiektów blob platformy Azure. Avere vFXT usprawnia żądania danych od klientów i buforuje dane, które służy do zwiększenia wydajności w skali i w miarę upływu czasu. Avere vFXT nie przechowuje danych. Nie zawiera on informacji o ilości przechowywanych danych.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Czy avere vFXT rozwiązanie do obsługi warstw?

Avere vFXT for Azure nie automatycznie warstwuje danych między gorącą i chłodną warstwą.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Jak mogę wiedzieć, czy środowisko jest odpowiednie dla avere vFXT?

Najlepszym sposobem na zadawanie tego pytania jest "czy obciążenie buforowaniem?". Oznacza to, czy obciążenie ma wysoki współczynnik odczytu i zapisu? Przykładem jest 80/20 lub 70/30 odczyt/zapis.

Rozważ avere vFXT dla platformy Azure, jeśli masz potok analityczny oparty na plikach, który działa na dużej liczbie maszyn wirtualnych platformy Azure, i spełnia co najmniej jeden z następujących warunków:

* Ogólna wydajność jest niska lub niespójna z powodu długich czasów dostępu do pliku (dziesiątki milisekund lub sekund, w zależności od wymagań). To opóźnienie jest nieakceptowalne dla klienta.

* Dane wymagane do przetworzenia są zlokalizowane na dalekiej końcu środowiska sieci WAN i ciągłe przeniesienie danych jest niepraktyczne. Dane mogą znajdować się w innym regionie świadczenia usługi Azure lub w centrum danych klienta.

* Znaczna liczba klientów żądających danych — na przykład w klastrze obliczeniowym o wysokiej wydajności (HPC). Duża liczba równoczesnych żądań może zwiększyć opóźnienia.

* Klient chce uruchomić swój bieżący potok "zgodnie z oczekiwaniami" w usłudze Azure Virtual Machines i musi mieć rozwiązanie magazynu udostępnionego (lub buforowanie) oparte na systemie POSIX w celu zapewnienia skalowalności. Za pomocą programu avere vFXT for Azure nie trzeba reprojektować potoku służbowego, aby wykonywać natywne wywołania usługi Azure Blob Storage.

* Aplikacja HPC jest oparta na klientach NFSv3. (W niektórych przypadkach może korzystać z klientów SMB 2,1, ale wydajność jest ograniczona).

Poniższy diagram może pomóc odpowiedzieć na to pytanie. Bliżej tego przepływu pracy znajduje się w prawym górnym rogu, tym bardziej najprawdopodobniej jest to, że rozwiązanie avere vFXT for Azure buforowania jest odpowiednie dla danego środowiska.

![Diagram grafu przedstawiający duże obciążenia z tysiącami klientów są lepiej dopasowane do avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Na czym polega skalowanie klientów w ramach rozwiązania avere vFXT?

Rozwiązanie pamięci podręcznej avere vFXT jest stworzone z myślą o obsłudze setek, tysięcy lub dziesiątych tysięcy rdzeni obliczeniowych. Jeśli masz kilka maszyn z działającą jasnym działaniem, avere vFXT nie jest odpowiednim rozwiązaniem.

Typowi klienci avere vFXT uruchamiają wysokie obciążenia, zaczynając od około 1 000 rdzeni procesora. Te środowiska mogą mieć maksymalnie 50 000 rdzeni. Ponieważ avere vFXT jest skalowalny, można dodać węzły do obsługi tych obciążeń w miarę ich wzrostu, aby wymagać większej przepływności lub większej liczby operacji we/wy na sekundę.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Ile danych może być avere środowiska vFXT?

Avere vFXT for Azure to pamięć podręczna. Nie jest to jednak przeznaczone do przechowywania danych. Używa kombinacji pamięci RAM i dysków SSD do przechowywania danych w pamięci podręcznej. Dane są trwale przechowywane w systemie magazynu zaplecza (na przykład NetApp system NAS lub kontener obiektów BLOB). System avere vFXT nie zawiera informacji o ilości przechowywanych za nią danych. Avere vFXT tylko buforuje podzbiór danych, do których żąda klient.  

### <a name="what-regions-are-supported"></a>Jakie regiony są obsługiwane?

Avere vFXT for Azure są obsługiwane we wszystkich regionach z wyjątkiem suwerennych regionów (Chiny, Niemcy). Upewnij się, że region, który ma być używany, może obsługiwać dużą liczbę rdzeni obliczeniowych i wystąpień maszyn wirtualnych, które są potrzebne do utworzenia klastra avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Jak mogę uzyskać pomoc dotyczącą avere vFXT?

Wyspecjalizowana Grupa personelu pomocy technicznej oferuje pomoc dotyczącą avere vFXT dla platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Uzyskaj pomoc](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) dotyczącą systemu, aby otworzyć bilet pomocy technicznej z Azure Portal.

### <a name="is-avere-vfxt-highly-available"></a>Czy avere vFXT o wysokiej dostępności?

Tak, avere vFXT działa wyłącznie jako rozwiązanie o wysokiej dostępności.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Czy program avere vFXT for Azure obsługuje również inne usługi w chmurze?

Tak, klienci mogą używać więcej niż jednego dostawcy chmury z klastrem avere vFXT. Obsługuje ona zasobniki standardowe AWS S3, zasobniki w warstwie Standardowa Google Cloud Services i kontenery obiektów blob platformy Azure.

> [!NOTE]
> Opłata za oprogramowanie ma zastosowanie do korzystania z usługi avere vFXT w usłudze AWS lub Google Cloud Storage. Nie ma dodatkowej opłaty za korzystanie z usługi Azure Blob Storage.

## <a name="technical-compute"></a>Techniczne: obliczenia

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Czy można opisać, jak wygląda środowisko avere vFXT "?

Avere vFXT to urządzenie klastrowane wykonane z wielu maszyn wirtualnych platformy Azure. Biblioteka języka Python obsługuje tworzenie, usuwanie i modyfikowanie klastra. Przeczytaj artykuł [co to jest avere vFXT for Azure?](avere-vfxt-overview.md) aby dowiedzieć się więcej.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Na jakich typach maszyn wirtualnych platformy Azure jest avere vFXT?  

Avere vFXT dla klastra platformy Azure Microsoft Azure używa E32s_v3 maszyn wirtualnych.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Czy skalowanie środowiska avere vFXT?

Klaster avere vFXT może być tak mały jak trzy węzły maszyny wirtualnej lub maksymalnie 24 węzły. Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać pomoc dotyczącą planowania, jeśli uważasz, że potrzebujesz klastra z więcej niż dziewięcioma węzłami. Większa liczba węzłów wymaga większej architektury wdrożenia.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Czy środowisko avere vFXT "Automatyczne skalowanie"?

Nie. Rozmiar klastra można skalować w górę i w dół, ale Dodawanie lub usuwanie węzłów klastra jest krokiem ręcznym.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Czy można uruchomić klaster avere vFXT jako zestaw skalowania maszyn wirtualnych?

Avere vFXT nie obsługuje wdrożenia zestawu skalowania maszyn wirtualnych. Niektóre wbudowane mechanizmy obsługi dostępności są przeznaczone tylko dla niepodzielnych maszyn wirtualnych uczestniczących w klastrze.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Czy można uruchomić klaster avere vFXT na maszynach wirtualnych o niskim priorytecie?

Nie, system wymaga podstawowego stabilnego zestawu maszyn wirtualnych.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Czy można uruchomić klaster avere vFXT w kontenerach?

Nie, avere vFXT musi być wdrożony jako aplikacja niezależna.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Czy liczba maszyn wirtualnych avere vFXT jest liczona względem limitu przydziału obliczeń?

Tak. Upewnij się, że masz wystarczający przydział w regionie, aby obsługiwać klaster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Czy można uruchamiać maszyny klastrowe avere vFXT w różnych strefach dostępności?

Nie. Model wysokiej dostępności w programie avere vFXT obecnie nie obsługuje pojedynczych członków klastra avere vFXT znajdujących się w różnych strefach dostępności.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Czy można klonować maszyny wirtualne avere vFXT?

Nie, musisz użyć obsługiwanego skryptu języka Python, aby dodać lub usunąć węzły w klastrze avere vFXT. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zarządzanie klastrem avere vFXT](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Czy istnieje wersja "VM" oprogramowania, którą można uruchomić w środowisku lokalnym?

Nie, system jest oferowany jako urządzenie klastrowane i testowany na określonych typach maszyn wirtualnych. To ograniczenie pomaga klientom uniknąć tworzenia systemu, który nie obsługuje wymagań o wysokiej wydajności typowego przepływu pracy avere vFXT.

## <a name="technical-disks"></a>Techniczne: dyski

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jakie typy dysków są obsługiwane przez maszyny wirtualne platformy Azure?

Avere vFXT for Azure może korzystać z 1 TB lub 4 TB konfiguracji dysków SSD w warstwie Premium. Konfigurację dysku SSD w warstwie Premium można wdrożyć jako wiele dysków zarządzanych.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Czy klaster obsługuje dyski niezarządzane?

Nie, klaster wymaga dysków zarządzanych.

### <a name="does-the-system-support-local-attached-ssds"></a>Czy system obsługuje lokalne (dołączone) dysków SSD?

Program avere vFXT for Azure nie obsługuje obecnie lokalnego dysków SSD. Dyski używane do avere vFXT muszą być w stanie zamknąć i uruchomić ponownie, ale lokalna dołączona dysków SSD w tej konfiguracji może zostać przerwana.

### <a name="does-the-system-support-ultra-ssds"></a>Czy system obsługuje Ultra dysków SSD?

Nie, system obsługuje tylko konfiguracje SSD w warstwie Premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Czy mogę odłączyć moją dysków SSD Premium i ponownie dołączyć je później, aby zachować zawartość pamięci podręcznej między użyciem?

Odłączanie i ponowne dołączanie dysków SSD nie jest obsługiwane. Metadane lub zawartość pliku w źródle mogły ulec zmianie między użyciem, co może spowodować problemy z integralnością danych.

### <a name="does-the-system-encrypt-the-cache"></a>Czy system szyfruje pamięć podręczną?

Dane są rozłożone na dyski, ale nie są szyfrowane. Dyski mogą jednak być szyfrowane. Aby uzyskać więcej informacji, zobacz [bezpieczne i Używanie zasad na maszynach wirtualnych na platformie Azure](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Techniczne: sieć

### <a name="what-network-is-recommended"></a>Jaka sieć jest zalecana?

Jeśli używasz magazynu lokalnego z avere vFXT, należy mieć 1 GB/s lub lepsze połączenie sieciowe między magazynem a klastrem. Jeśli masz niewielką ilość danych i chcesz skopiować dane do chmury przed uruchomieniem zadań, być może jest wystarczająca łączność z siecią VPN.

> [!TIP]
> Wolniejsze łącze sieciowe to wolniejsze początkowe odczyty "zimne". Wolne odczyty zwiększają opóźnienia potoku pracy.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Czy można uruchomić avere vFXT w innej sieci wirtualnej niż mój klaster obliczeniowy?

Tak, możesz utworzyć system avere vFXT w innej sieci wirtualnej. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Planowanie systemu avere vFXT](avere-vfxt-deploy-plan.md) .

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Czy avere vFXT wymaga własnej podsieci?

Tak. Avere vFXT działa wyłącznie jako klaster wysokiej dostępności (HA) i wymaga wielu adresów IP do działania. Jeśli klaster znajduje się w własnej podsieci, unika się ryzyka konfliktów adresów IP, co może spowodować problemy z instalacją i normalną operacją. Podsieć klastra może znajdować się w sieci wirtualnej używanej przez inne zasoby, pod warunkiem, że żadne adresy IP nie nakładają się na siebie.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Czy można uruchomić avere vFXT na InfiniBand?

Nie, avere vFXT używa tylko sieci Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Jak mogę dostęp do lokalnego środowiska NAS z avere vFXT?

Środowisko avere vFXT jest podobne do wszystkich innych maszyn wirtualnych platformy Azure, które wymagają dostępu kierowanego za pośrednictwem bramy sieci lub sieci VPN do centrum danych klienta (i z powrotem). Rozważ użycie łączności z usługą Azure ExpressRoute, jeśli jest ona dostępna w Twoim środowisku.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jakie są wymagania dotyczące przepustowości dla avere vFXT?

Ogólne wymagania dotyczące przepustowości są zależne od dwóch czynników:

* Ilość danych żądanych ze źródła
* Tolerancja systemu klienta na czas oczekiwania podczas początkowego ładowania danych  

W przypadku środowisk z uwzględnieniem opóźnień należy użyć rozwiązania Fiber z minimalną szybkością łącza wynoszącą 1 GB/s. Użyj ExpressRoute, jeśli jest dostępny.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Czy można uruchamiać avere vFXT z publicznymi adresami IP?

Nie, avere vFXT jest przeznaczony do działania w środowisku sieciowym zabezpieczonym przy użyciu najlepszych rozwiązań.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Czy mogę ograniczyć dostęp do Internetu z sieci wirtualnej mojego klastra?

Ogólnie rzecz biorąc, w razie potrzeby można skonfigurować dodatkowe zabezpieczenia w sieci wirtualnej, ale niektóre ograniczenia mogą zakłócać działanie klastra.

Na przykład ograniczenie wychodzącego dostępu do Internetu z sieci wirtualnej powoduje problemy z klastrem, chyba że zostanie dodana również reguła, która jawnie zezwala na dostęp do AzureCloud. Ta sytuacja została opisana w [dodatkowej dokumentacji w witrynie GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Aby uzyskać pomoc dotyczącą niestandardowych zabezpieczeń, skontaktuj się z pomocą techniczną zgodnie z opisem w artykule [Uzyskaj pomoc](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)dotyczącą systemu.

## <a name="technical-back-end-storage-core-filers"></a>Techniczne: Magazyn zaplecza (podstawowe pliki)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Ilu plików głównych obsługuje pojedynczy avere środowiska vFXT?

Klaster avere vFXT obsługuje maksymalnie 20 plików podstawowych.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Jak środowisko avere vFXT przechowuje dane?

Avere vFXT nie jest magazynem. Jest to pamięć podręczna, która odczytuje i zapisuje dane z wielu miejsc docelowych o nazwie plików podstawowych. Dane przechowywane na dyskach SSD w warstwie Premium w avere vFXT są przejściowe i ostatecznie opróżniane do magazynu podstawowej bazy danych zaplecza.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Które podstawowe pliki avere vFXT obsługują?

Ogólnie rzecz biorąc, avere vFXT for Azure obsługuje następujące systemy jako pliki podstawowe:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 i 8,1) 
* NetApp ONTAP (tryb klastrowany 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) i (7-Mode 7. *, 8.0-8.3)

* Kontenery obiektów blob platformy Azure (tylko Magazyn lokalnie nadmiarowy)
* Zasobniki AWS S3
* Przedziały chmury Google

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Dlaczego avere vFXT nie obsługuje wszystkich plików NFS?

Mimo że wszystkie platformy NFS spełniają te same standardy IETF, w praktyce Każda implementacja ma własne osobliwości. Te szczegóły wpływają na to, jak avere vFXT współdziała z systemem magazynu. Obsługiwane systemy to najczęściej używane platformy w portalu Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Czy avere vFXT obsługuje magazyn obiektów prywatnych (na przykład SwiftStack)?

Avere vFXT nie obsługuje prywatnego magazynu obiektów.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak można uzyskać określony produkt magazynujący w obszarze Pomoc techniczna?

Pomoc techniczna jest zależna od ilości popytu w polu. Jeśli masz wystarczającą liczbę żądań opartych na przychodach do obsługi rozwiązania NAS, rozważmy ją. Wysyłaj żądania przez pomoc techniczną platformy Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Czy mogę użyć magazynu obiektów blob platformy Azure jako podstawowego pliku?

Tak, avere vFXT for Azure może używać kontenera blokowych obiektów BLOB jako podstawowego pliku w chmurze.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jakie są wymagania dotyczące konta magazynu dla podstawowego pliku obiektu BLOB?

Konto magazynu musi być kontem ogólnego przeznaczenia w wersji 2 (GPv2) i być skonfigurowane wyłącznie do lokalnego nadmiarowego magazynu. Magazyn Geograficznie nadmiarowy i magazyn strefowo nadmiarowy nie są obsługiwane.

Aby uzyskać więcej szczegółowych informacji na temat wymagań dotyczących konta magazynu, przeczytaj artykuł dotyczący [podstawowego pliku usługi Azure Blob Storage Cloud](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) .

### <a name="can-i-use-archive-blob-storage"></a>Czy można używać archiwum BLOB Storage?

Nie. Umowa dotycząca poziomu usług (SLA) dla magazynu archiwum nie jest zgodna z wymaganiami dotyczącymi dostępu do katalogów i plików w czasie rzeczywistym w systemie avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Czy można używać chłodnego magazynu obiektów BLOB?

Magazyn obiektów BLOB warstwy chłodnej nie jest zazwyczaj zalecany dla programu avere vFXT for Azure Core. Warstwa chłodna oferuje niższe koszty magazynowania, ale wyższe koszty operacji. (Zobacz [Cennik blokowania obiektów BLOB](<https://azure.microsoft.com/pricing/details/storage/blobs/>) , aby uzyskać więcej informacji). Jeśli dane będą dostępne i modyfikowane lub usuwane często, należy rozważyć użycie warstwy gorąca.

[Warstwy dostępu](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) udostępniają więcej informacji o tym, kiedy warto używać magazynu warstwy chłodnej jako podstawowego pliku vFXT.

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak mogę zaszyfrować kontenera obiektów BLOB?

Szyfrowanie obiektów BLOB można skonfigurować na platformie Azure (preferowany) lub na poziomie podstawowego pliku avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Czy mogę użyć własnego klucza szyfrowania dla pliku obiektu BLOB Core?

Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft dla obiektów blob, tabel i kolejek platformy Azure, a także Azure Files. Możesz przenieść własny klucz do szyfrowania dla usługi BLOB Storage i Azure Files. Jeśli zdecydujesz się używać szyfrowania avere vFXT, musisz użyć klucza wygenerowanego przez avere i zapisać go lokalnie.

## <a name="purchasing"></a>Zakup

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak mogę pobrać avere vFXT dla licencjonowania platformy Azure?

Uzyskanie licencji na usługę avere vFXT for Azure jest łatwe w portalu Azure Marketplace. Utwórz konto platformy Azure, a następnie postępuj zgodnie z instrukcjami w temacie [wdrażanie klastra avere vFXT](avere-vfxt-deploy.md) , aby utworzyć klaster avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Ile kosztuje avere vFXT?

Na platformie Azure nie ma dodatkowej opłaty licencyjnej za używanie klastrów avere vFXT. Klienci są odpowiedzialni za przechowywanie i inne opłaty za użycie platformy Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Czy maszyny wirtualne vFXT avere można uruchamiać jako niski priorytet?

Nie, klastry avere vFXT wymagają usługi "Always On". Klastry mogą być wyłączone, gdy nie jest to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z programem avere vFXT for Azure, przeczytaj następujące artykuły, aby dowiedzieć się, jak planować i wdrażać własny system:

* [Planowanie systemu Avere vFXT](avere-vfxt-deploy-plan.md)
* [Omówienie wdrażania](avere-vfxt-deploy-overview.md)
* [Przygotowywanie do utworzenia klastra avere vFXT](avere-vfxt-prereqs.md)
* [Wdrażanie klastra Avere vFXT](avere-vfxt-deploy.md)

Aby dowiedzieć się więcej o możliwościach i przypadkach użycia programu avere vFXT, odwiedź stronę [avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).