---
title: Planowanie wdrożenia usługi Azure Files | Dokumenty firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d5bf3a6df9d7292c18a93737fb7dea5d8c91f984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536499"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure
[Usługi Azure Files](storage-files-introduction.md) można wdrożyć na dwa główne sposoby: bezpośrednio montując bezserwerowe udziały plików platformy Azure lub buforując udziały plików platformy Azure lokalnie przy użyciu usługi Azure File Sync. Wybrana opcja wdrażania zmienia rzeczy, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- **Bezpośrednie instalowanie udziału plików platformy Azure:** Ponieważ usługa Azure Files zapewnia dostęp do SMB, można zainstalować udziały plików platformy Azure lokalnie lub w chmurze przy użyciu standardowego klienta SMB dostępnego w systemach Windows, macOS i Linux. Ponieważ udziały plików platformy Azure są bezserwerowe, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- **Buforuj udział plików platformy Azure lokalnie za pomocą usługi Azure File Sync:** Usługa Azure File Sync umożliwia scentralizowanie udziałów plików organizacji w plikach azure, przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca lokalny (lub chmurowy) system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. 

Ten artykuł dotyczy przede wszystkim zagadnień dotyczących wdrażania udziału plików platformy Azure, które mają być bezpośrednio montowane przez klienta lokalnego lub w chmurze. Aby zaplanować wdrożenie usługi Azure File Sync, zobacz [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Koncepcje zarządzania
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Podczas wdrażania udziałów plików platformy Azure na kontach magazynu zaleca się:

- Wdrażanie udziałów plików platformy Azure tylko na kontach magazynu z innymi udziałami plików platformy Azure. Chociaż konta magazynu GPv2 umożliwiają korzystanie z kont magazynu o mieszanym celu, ponieważ zasoby magazynu, takie jak udziały plików platformy Azure i kontenery obiektów blob, współużytkują limity konta magazynu, mieszanie zasobów może utrudnić późniejsze rozwiązywanie problemów z wydajnością. 

- Zwracanie uwagi na ograniczenia kont IOPS konta magazynu podczas wdrażania udziałów plików platformy Azure. W idealnym przypadku należy mapować udziały plików 1:1 z kontami magazynu, jednak nie zawsze może to być możliwe ze względu na różne ograniczenia i ograniczenia, zarówno w organizacji, jak i z platformy Azure. Jeśli nie jest możliwe wdrożenie tylko jednego udziału plików na jednym koncie magazynu, należy wziąć pod uwagę, które udziały będą bardzo aktywne, a które udziały będą mniej aktywne, aby upewnić się, że najgorętsze udziały plików nie zostaną umieszczone na tym samym koncie magazynu razem.

- Należy wdrażać konta GPv2 i FileStorage i uaktualniać konta GPv1 i klasycznego magazynu tylko wtedy, gdy znajdziesz je w swoim środowisku. 

## <a name="identity"></a>Tożsamość
Aby uzyskać dostęp do udziału plików platformy Azure, użytkownik udziału plików musi być uwierzytelniony i mieć autoryzację dostępu do udziału. Odbywa się to na podstawie tożsamości użytkownika uzyskującego dostęp do udziału plików. Usługa Azure Files integruje się z trzema głównymi dostawcami tożsamości:
- **Lokalne Usługi domenowe Active Directory (AD DS) lub lokalne usługi AD DS** (wersja zapoznawcza): konta magazynu platformy Azure mogą być przyłączane do należących do klienta Usług domenowych Active Directory, podobnie jak serwer plików systemu Windows Server lub urządzenie NAS. Kontrolera domeny można wdrożyć lokalnie, na maszynie Wirtualnej platformy Azure lub nawet jako maszyna wirtualna w innym dostawcy chmury; Usługa Azure Files jest niezależna od miejsca, w którym znajduje się kontroler domeny. Gdy konto magazynu zostanie przyłączone do domeny, użytkownik końcowy może zainstalować udział plików przy tym konto użytkownika, za pomocą których zalogowano się do komputera. Uwierzytelnianie oparte na utok, korzystając z protokołu uwierzytelniania Kerberos, używa protokołu uwierzytelniania Kerberos.
- **Usługi domenowe Usługi domenowe Active Directory platformy Azure (Usługi Azure AD DS):** Usługi Azure AD DS udostępnia kontroler domeny zarządzane przez firmę Microsoft, który może być używany dla zasobów platformy Azure. Przyłączanie konta magazynu do usługi Azure AD DS zapewnia podobne korzyści dla domeny łączącej ją z należącą do klienta usługą Active Directory. Ta opcja wdrażania jest najbardziej przydatna w scenariuszach podnoszenia i zmiany aplikacji, które wymagają uprawnień opartych na urzeczyw. Ponieważ usługa Azure AD DS zapewnia uwierzytelnianie oparte na usłudze AD, ta opcja używa również protokołu uwierzytelniania Kerberos.
- **Klucz konta usługi Azure storage:** udziały plików platformy Azure mogą być również instalowane przy kluczu konta magazynu platformy Azure. Aby zainstalować udział plików w ten sposób, nazwa konta magazynu jest używana jako nazwa użytkownika, a klucz konta magazynu jest używany jako hasło. Za pomocą klucza konta magazynu do zainstalowania udziału plików platformy Azure jest skutecznie operacji administratora, ponieważ zainstalowany udział plików będzie miał pełne uprawnienia do wszystkich plików i folderów w udziale, nawet jeśli mają listy ACL. Podczas korzystania z klucza konta magazynu do zainstalowania za pośrednictwem protokołu SMB używany jest protokół uwierzytelniania NTLMv2.

W przypadku klientów migrujących z lokalnych serwerów plików lub tworzących nowe udziały plików w usłudze Azure Files, które mają zachowywać się jak serwery plików systemu Windows lub urządzenia NAS, zalecana jest domena łącząca konto magazynu z należącą do **klienta usługą Active Directory.** Aby dowiedzieć się więcej o dołączaniu konta magazynu do należącej do klienta usługi Active Directory, zobacz [Omówienie usługi Azure Files Active Directory](storage-files-active-directory-overview.md).

Jeśli zamierzasz użyć klucza konta magazynu, aby uzyskać dostęp do udziałów plików platformy Azure, zaleca się używanie punktów końcowych usługi, jak opisano w sekcji [Sieci.](#networking)

## <a name="networking"></a>Networking
Udziały plików platformy Azure są dostępne z dowolnego miejsca za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą pochodzić bezpiecznie z platformy Azure lub poza nią. W wielu środowiskach klientów początkowe instalowanie udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, nawet jeśli instalacje z maszyn wirtualnych platformy Azure powiodą się. Powodem tego jest to, że wiele organizacji i dostawców usług internetowych (ISP) blokuje port używany przez SMB do komunikacji, port 445. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby odblokować dostęp do udziału plików platformy Azure, dostępne są dwie główne opcje:

- Odblokuj port 445 dla sieci lokalnej organizacji. Udziały plików platformy Azure mogą być dostępne tylko zewnętrznie za pośrednictwem publicznego punktu końcowego przy użyciu protokołów bezpiecznych w Internecie, takich jak SMB 3.0 i FileREST API. Jest to najprostszy sposób dostępu do udziału plików platformy Azure z lokalnego, ponieważ nie wymaga zaawansowanej konfiguracji sieciowej poza zmianą reguł portów wychodzących organizacji, jednak zaleca się usunięcie starszych i przestarzałych wersji protokołu SMB, a mianowicie SMB 1.0. Aby dowiedzieć się, jak to zrobić, zobacz [Zabezpieczanie systemu Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) i [zabezpieczanie systemu Linux](storage-how-to-use-files-linux.md#securing-linux).

- Uzyskaj dostęp do udziałów plików platformy Azure za pośrednictwem połączenia Usługi ExpressRoute lub VPN. Podczas uzyskiwania dostępu do udziału plików platformy Azure za pośrednictwem tunelu sieciowego można zainstalować udział plików platformy Azure, taki jak lokalny udział plików, ponieważ ruch SMB nie przekracza granic organizacji.   

Chociaż z technicznego punktu widzenia jest znacznie łatwiejsze do zainstalowania udziałów plików platformy Azure za pośrednictwem publicznego punktu końcowego, oczekujemy, że większość klientów zdecyduje się zainstalować swoje udziały plików platformy Azure za pośrednictwem połączenia Usługi ExpressRoute lub SIECI VPN. Aby to zrobić, należy skonfigurować następujące dla środowiska:  

- **Tunelowanie sieciowe przy użyciu usługi ExpressRoute, Site-to-Site lub Point-to-Site VPN:** Tunelowanie w sieci wirtualnej umożliwia dostęp do udziałów plików platformy Azure z lokalnego, nawet jeśli port 445 jest zablokowany.
- **Prywatne punkty końcowe:** Prywatne punkty końcowe nadają kontu magazynu dedykowany adres IP z przestrzeni adresowej sieci wirtualnej. Umożliwia to tunelowanie sieci bez konieczności otwierania sieci lokalnych do wszystkich zakresów adresów IP należących do klastrów magazynu platformy Azure. 
- **Przekierowanie DNS:** Skonfiguruj lokalny system DNS, aby rozpoznać nazwę `storageaccount.file.core.windows.net` konta magazynu (tj. dla regionów chmury publicznej), aby rozwiązać problem z adresem IP prywatnych punktów końcowych.

Aby zaplanować sieć skojarzoną z wdrażaniem udziału plików platformy Azure, zobacz [Zagadnienia dotyczące sieci usług Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Szyfrowanie
Usługa Azure Files obsługuje dwa różne typy szyfrowania: szyfrowanie podczas przesyłania, które odnosi się do szyfrowania używanego podczas instalowania/uzyskiwania dostępu do udziału plików platformy Azure, oraz szyfrowania w spoczynku, które odnosi się do sposobu szyfrowania danych podczas przechowywania ich na dysku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Domyślnie wszystkie konta magazynu platformy Azure mają włączone szyfrowanie podczas przesyłania. Oznacza to, że podczas instalowania udziału plików za pośrednictwem protokołu SMB lub uzyskiwania do niego dostępu za pośrednictwem protokołu FileREST (na przykład za pośrednictwem witryny Azure portal, powershell/cli lub azure SDKs), usługa Azure Files zezwala na połączenie tylko wtedy, gdy zostanie nawiązycona za pomocą protokołu SMB 3.0+ z szyfrowaniem lub protokołem HTTPS. Klienci, którzy nie obsługują SMB 3.0 lub klientów obsługujących szyfrowanie SMB 3.0, ale nie SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie podczas przesyłania jest włączone. Aby uzyskać więcej informacji o tym, które systemy operacyjne obsługują SMB 3.0 z szyfrowaniem, zapoznaj się ze szczegółową dokumentacją [systemu Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)i [Linux.](storage-how-to-use-files-linux.md) Wszystkie bieżące wersje programów PowerShell, CLI i SDK obsługują protokół HTTPS.  

Można wyłączyć szyfrowanie podczas przesyłania dla konta magazynu platformy Azure. Gdy szyfrowanie jest wyłączone, usługi Azure Files umożliwi również SMB 2.1, SMB 3.0 bez szyfrowania i niezaszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Głównym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak Dystrybucja systemu Windows Server 2008 R2 lub starsza. Usługa Azure Files zezwala tylko na połączenia SMB 2.1 w tym samym regionie platformy Azure co udział plików platformy Azure; klient SMB 2.1 poza regionem platformy Azure udziału plików platformy Azure, na przykład lokalnie lub w innym regionie platformy Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Zdecydowanie zalecamy zapewnienie szyfrowania przesyłanych danych.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w magazynie platformy Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Ogólnie rzecz biorąc, funkcje usługi Azure Files i współdziałanie z innymi usługami są takie same między udziałami plików w wersji premium a standardowymi udziałami plików, jednak istnieje kilka istotnych różnic:
- **Model rozliczania**
    - Udziały plików w uliczce są rozliczane przy użyciu aprowizowanego modelu rozliczeniowego, co oznacza, że płacisz za ilość miejsca, które udostępniasz, a nie za ilość miejsca, o które faktycznie prosisz. 
    - Standardowe udziały plików są rozliczane przy użyciu modelu płatności zgodnie z rzeczywistym użyciem, który obejmuje podstawowy koszt magazynu dla ilości magazynu, który faktycznie zużywa, a następnie dodatkowy koszt transakcji na podstawie sposobu korzystania z udziału. W przypadku standardowych udziałów plików rachunek wzrośnie, jeśli użyjesz (odczyt/zapis/montaż) więcej udziału plików platformy Azure.
- **Opcje nadmiarowości**
    - Udziały plików premium są dostępne tylko dla magazynu nadmiarowego lokalnie (LRS) i strefy nadmiarowej (ZRS). 
    - Standardowe udziały plików są dostępne dla magazynu lokalnie nadmiarowego, nadmiarowego strefy, geograficznie nadmiarowego (GRS) i nadmiarowego strefy geograficznej (GZRS).
- **Maksymalny rozmiar udziału plików**
    - Udziały plików premium mogą być aprowiowane dla maksymalnie 100 TiB bez żadnych dodatkowych prac.
    - Domyślnie standardowe udziały plików mogą obejmować tylko do 5 TiB, chociaż limit udziału można zwiększyć do 100 TiB, decydując się na flagę funkcji magazynu *dużego udziału plików.* Standardowe udziały plików mogą obejmować maksymalnie 100 TiB dla lokalnie nadmiarowych lub strefowych kont magazynu nadmiarowego. Aby uzyskać więcej informacji na temat zwiększania rozmiarów udziałów plików, zobacz [Włączanie i tworzenie dużych udziałów plików](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Dostępność regionalna**
    - Udziały plików w uyliki nie są dostępne w każdym regionie, a nadmiarowa obsługa stref jest dostępna w mniejszym podzbiorze regionów. Aby dowiedzieć się, czy udziały plików w usłudze Premium są obecnie dostępne w Twoim regionie, zobacz produkty dostępne na stronie [regionu](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure. Aby dowiedzieć się, jakie regiony obsługują usługę ZRS, zobacz [Obsługa strefy dostępności platformy Azure według regionów](../../availability-zones/az-overview.md#services-support-by-region). Aby pomóc nam ustalić priorytety nowych regionów i funkcji warstwy premium, wypełnij tę [ankietę.](https://aka.ms/pfsfeedback)
    - Standardowe udziały plików są dostępne w każdym regionie platformy Azure.
- Usługa Azure Kubernetes Service (AKS) obsługuje udziały plików w wersji premium w wersji 1.13 i nowszych.

Po utworzeniu udziału plików jako udziału premium lub standardowego udziału plików nie można go automatycznie przekonwertować na inną warstwę. Jeśli chcesz przełączyć się do innej warstwy, należy utworzyć nowy udział plików w tej warstwie i ręcznie skopiować dane z oryginalnego udziału do nowego utworzonego udziału. Zalecamy `robocopy` wykonanie tej `rsync` kopii w systemach Windows lub dla systemów macOS i Linux.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Opis inicjowania obsługi administracyjnej udziałów plików premium
Udziały plików premium są aprowiowane na podstawie stałego współczynnika GiB/IOPS/przepływności. Dla każdego aprowizowanego GiB, akcja zostanie wyemitowana jeden IOPS i 0,1 MiB / s przepustowość do maksymalnych limitów na akcję. Minimalna dozwolona inicjowanie obsługi administracyjnej wynosi 100 GiB z minimalną wydajnością IOPS/.

Na podstawie najlepszych starań, wszystkie udziały mogą pęknąć do trzech IOPS na GiB aprowizowanego magazynu przez 60 minut lub dłużej w zależności od wielkości udziału. Nowe akcje zaczynają się od pełnego kredytu seryjnego w oparciu o aprowizowanego potencjału.

Udziały muszą być aprowizowane w przyrostach 1 GiB. Minimalny rozmiar to 100 GiB, następny rozmiar to 101 GiB i tak dalej.

> [!TIP]
> Podstawowe usługi We/Wy = 1 * aprowizowana gib. (Maksymalnie 100 000 IOPS).
>
> Limit serii = 3 * podstawowe we/wy. (Maksymalnie 100 000 IOPS).
>
> szybkość wypływu = 60 MiB/s + 0,06 * aprowizowana GiB
>
> szybkość transferu danych przychodzących = 40 MiB/s + 0,04 * aprowizowana GiB

Aprowizowana wielkość udziału jest określona przez przydział udziału. Przydział udziałów można zwiększyć w dowolnym momencie, ale można go zmniejszyć dopiero po 24 godzinach od ostatniego wzrostu. Po odczekaniu 24 godzin bez zwiększenia przydziału możesz zmniejszyć przydział udziału tyle razy, ile chcesz, aż do ponownego zwiększenia. Zmiany skali we/wy/przepływności będą obowiązywać w ciągu kilku minut po zmianie rozmiaru.

Możliwe jest zmniejszenie rozmiaru aprowizowanego udziału poniżej używanego GiB. Jeśli to zrobisz, nie utracisz danych, ale nadal będą naliczane naliczane za rozmiar używany i otrzymasz wydajność (bazowy we/wy, przepływność i seria We/Wy) aprowizowanego udziału, a nie za używany rozmiar.

W poniższej tabeli przedstawiono kilka przykładów tych wzorów dla aprowizacji rozmiarów udziałów:

|Pojemność (GiB) | Bazowe we/wy | Rozerwanie we/wy | Wyjście (MiB/s) | Ruch przychodzący (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Do 300     | 66   | 44   |
|500         | 500     | Do 1500   | 90   | 60   |
|1,024       | 1,024   | Do 3 072   | 122   | 81   |
|5,120       | 5,120   | Do 15 360  | 368   | 245   |
|10,240      | 10,240  | Do 30 720  | 675 | 450   |
|33,792      | 33,792  | Do 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Do 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Do 100 000 | 6,204 | 4,136   |

> [!NOTE]
> Wydajność udziałów plików podlega limitom sieci maszyn, dostępnej przepustowości sieci, rozmiarom we/wy, równoległości, wśród wielu innych czynników. Na przykład, na podstawie wewnętrznych testów z 8 kib odczytu / zapisu we/wy rozmiary, pojedyncza maszyna wirtualna systemu Windows, *Standard F16s_v2*, podłączony do udziału plików premium przez SMB może osiągnąć 20K odczytU IOPS i 15K zapisU IOPS. Przy rozmiarach we/wy odczytu/zapisu 512 MiB ten sam maszynka wirtualna może osiągnąć przepływność 1,1 GiB/s i przepływność 370 MiB/s przychodzących. Aby osiągnąć maksymalną skalę wydajności, rozłóż obciążenie na wielu maszynach wirtualnych. Zapoznaj się z [przewodnikiem rozwiązywania problemów,](storage-troubleshooting-files-performance.md) aby uzyskać typowe problemy z wydajnością i obejścia.

#### <a name="bursting"></a>Pęknięcie
Udziały plików premium mogą pęknąć ich we/wy do trzech. Bursting jest zautomatyzowany i działa w oparciu o system kredytowy. Bursting działa na podstawie najlepszego wysiłku, a limit serii nie jest gwarancją, udziały plików mogą *pęknąć do* limitu.

Kredyty gromadzą się w zasobniku serii, gdy ruch dla udziału plików jest poniżej linii bazowej We/Wy. Na przykład udział 100 GiB ma 100 bazowych operacji We/Wy. Jeśli rzeczywisty ruch na udziale był 40 We/Wy dla określonego interwału 1-sekundowego, a następnie 60 nieużywanych we/wy we/wy są zapisywane na zasobniku serii. Kredyty te zostaną wykorzystane później, gdy operacje przekroczą bazowe operacje We/Wy.

> [!TIP]
> Rozmiar wiadra rozerwanego = Podstawowe IOPS * 2 * 3600.

Za każdym razem, gdy udział przekracza podstawowe we/wy i ma kredyty w wiadrze serii, pęknie. Akcje mogą nadal pękać tak długo, jak długo kredyty pozostają, choć akcje mniejsze niż 50 TiB pozostaną na granicy wybuchu tylko przez godzinę. Akcje większe niż 50 TiB mogą technicznie przekroczyć ten limit jednej godziny, do dwóch godzin, ale jest to oparte na liczbie naliczonych kredytów seryjnych. Każdy we/wy poza bazowymi we/wy zużywa jeden kredyt, a po zużyciu wszystkich kredytów udział powróci do bazowego we/wy.

Kredyty akcji mają trzy stany:

- Naliczanie, gdy udział plików jest używany mniej niż bazowy we/wy.
- Maleje, gdy udział plików jest pęknięcie.
- Stała pozostająca, gdy nie ma żadnych kredytów lub bazowych we/wy są używane.

Nowe udziały plików zaczynają się od pełnej liczby kredytów w wiadrze burst. Kredyty serii nie będą naliczane, jeśli współudział We/Wy spadnie poniżej linii bazowej We/Wy z powodu ograniczania przepustowości przez serwer.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włącz standardowe udziały plików do 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Ograniczenia
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migracja
W wielu przypadkach nie będzie ustanawiać nowego udziału plików netto dla organizacji, ale zamiast tego migrujesz istniejący udział plików z lokalnego serwera plików lub urządzenia NAS do usługi Azure Files. Istnieje wiele narzędzi, dostarczonych zarówno przez firmę Microsoft, jak i 3-te strony, aby wykonać migrację do udziału plików, ale można je z grubsza podzielić na dwie kategorie:

- **Narzędzia, które utrzymują atrybuty systemu plików, takie jak listy ACL i sygnatury czasowe:**
    - **[Usługa Azure File Sync:](storage-sync-files-planning.md)** Usługa Azure File Sync może służyć jako metoda pozyskiwania danych do udziału plików platformy Azure, nawet jeśli żądane wdrożenie końcowe nie jest do utrzymania obecności lokalnej. Synchronizacja plików platformy Azure może być zainstalowana w istniejących wdrożeniach systemów Windows Server 2012 R2, Windows Server 2016 i Windows Server 2019. Zaletą korzystania z usługi Azure File Sync jako mechanizm pozyskiwania jest, że użytkownicy końcowi mogą nadal korzystać z istniejącego udziału plików w miejscu; cut-over do udziału plików platformy Azure może wystąpić po zakończeniu przekazywania wszystkich danych w tle.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy to dobrze znane narzędzie do kopiowania, które jest dostarczane z systemami Windows i Windows Server. Robocopy może służyć do przesyłania danych do usługi Azure Files przez zamontowanie udziału plików lokalnie, a następnie przy użyciu zainstalowanej lokalizacji jako miejsca docelowego w robocopy polecenia.

- **Narzędzia, które nie zachowują atrybutów systemu plików:**
    - **Pole danych:** Pole danych udostępnia mechanizm transferu danych w trybie offline do fizycznego przesyłania danych na platformę Azure. Ta metoda ma na celu zwiększenie przepływności i zaoszczędzenie przepustowości, ale obecnie nie obsługuje atrybutów systemu plików, takich jak znaczniki czasu i listy ACL.
    - **[AzCopy:](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z usługi Azure Files, a także usługi Azure Blob storage, przy użyciu prostych poleceń o optymalnej wydajności.

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md)
* [Wdrażanie plików platformy Azure](storage-files-deployment-guide.md)
* [Wdrażanie synchronizacji plików platformy Azure](storage-sync-files-deployment-guide.md)
