---
title: Planowanie wdrożenia Azure Files | Microsoft Docs
description: Opis planowania wdrożenia Azure Files. Można bezpośrednio zainstalować udział plików platformy Azure lub lokalnie buforować udział plików platformy Azure przy użyciu Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: a35c34a08dba625b16940d7ec5fb870952dba36b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630247"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure
[Azure Files](storage-files-introduction.md) można wdrożyć na dwa sposoby: przez bezpośrednie zainstalowanie udziałów plików platformy Azure bezserwerowych lub buforowanie udziałów plików platformy Azure lokalnie przy użyciu Azure File Sync. Wybór opcji wdrożenia powoduje zmianę warunków, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- **Bezpośrednia instalacja udziału plików platformy Azure** : ponieważ Azure Files zapewnia dostęp do bloku komunikatów serwera (SMB) lub sieciowy system plików (NFS), można instalować udziały plików platformy Azure lokalnie lub w chmurze przy użyciu standardowych klientów SMB lub NFS dostępnych w systemie operacyjnym. Ponieważ udziały plików platformy Azure są bezserwerowe, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- **Buforowanie udziałów plików platformy Azure w środowisku lokalnym za pomocą Azure File Sync** : Azure File Sync pozwala na scentralizowanie udziały plików w organizacji w Azure Files, przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Azure File Sync przekształca lokalnego (lub w chmurze) systemu Windows Server w szybką pamięć podręczną udziału plików SMB platformy Azure. 

Ten artykuł dotyczy głównie zagadnień związanych z wdrażaniem udziału plików platformy Azure, który ma być bezpośrednio instalowany przez klienta lokalnego lub w chmurze. Aby zaplanować wdrożenie Azure File Sync, zapoznaj się z tematem [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Dostępne protokoły

Azure Files oferuje dwa protokoły, które mogą być używane podczas instalowania udziałów plików, SMB i sieciowego systemu plików (NFS). Aby uzyskać szczegółowe informacje na temat tych protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Większość zawartości tego artykułu dotyczy tylko udziałów SMB. Wszystkie elementy, które mają zastosowanie do udziałów NFS, zostaną w tym samym stanie.

## <a name="management-concepts"></a>Pojęcia związane z zarządzaniem
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

W przypadku wdrażania udziałów plików platformy Azure na kontach magazynu zalecamy:

- Wdrażaj udziały plików platformy Azure na kontach magazynu z innymi udziałami plików platformy Azure. Mimo że konta magazynu GPv2 umożliwiają korzystanie z kont magazynu w trybie mieszanym, ponieważ zasoby magazynu, takie jak udziały plików platformy Azure i kontenery obiektów blob, współużytkują limity konta magazynu, mieszanie zasobów może utrudnić rozwiązanie problemów z wydajnością później. 

- Podczas wdrażania udziałów plików platformy Azure należy zwrócić uwagę na ograniczenia IOPS konta magazynu. W idealnym przypadku należy zamapować udziały plików 1:1 z kontami magazynu, ale może to nie zawsze być możliwe z powodu różnych limitów i ograniczeń zarówno z organizacji, jak i z platformy Azure. Gdy nie można mieć tylko jednego udziału plików wdrożonego na jednym koncie magazynu, należy wziąć pod uwagę, które udziały będą wysoce aktywne i które udziały będą mniej aktywne, aby upewnić się, że udziały plików okienko nie zostaną umieszczone w tym samym koncie magazynu jednocześnie.

- Należy wdrażać tylko konta GPv2 i FileStorage oraz uaktualnić GPv1 i klasyczne konta magazynu, gdy znajdziesz je w Twoim środowisku. 

## <a name="identity"></a>Tożsamość
Aby uzyskać dostęp do udziału plików platformy Azure, użytkownik musi zostać uwierzytelniony i ma autoryzację dostępu do udziału. Jest to wykonywane na podstawie tożsamości użytkownika uzyskującego dostęp do udziału plików. Azure Files integruje się z trzema głównymi dostawcami tożsamości:
- **Lokalne Active Directory Domain Services (AD DS lub lokalne AD DS)** : konta usługi Azure Storage mogą być przyłączone do domeny, Active Directory Domain Services, podobnie jak serwer plików lub urządzenie nas z systemem Windows Server. Kontroler domeny można wdrożyć lokalnie, na maszynie wirtualnej platformy Azure, a nawet jako maszynę wirtualną w innym dostawcy chmury. Azure Files jest niezależny od do lokalizacji, w której znajduje się kontroler domeny. Gdy konto magazynu zostanie przyłączone do domeny, użytkownik końcowy może zainstalować udział plików przy użyciu konta użytkownika, które zarejestrowano na komputerze przy użyciu programu. Uwierzytelnianie oparte na usłudze AD korzysta z protokołu uwierzytelniania Kerberos.
- **Azure Active Directory Domain Services (AD DS platformy Azure)** : usługa Azure AD DS udostępnia kontroler domeny zarządzany przez firmę Microsoft, który może być używany przez zasoby platformy Azure. Dołączenie domeny do konta magazynu do usługi Azure AD DS zapewnia podobne korzyści do dołączania do domeny Active Directory należącej do klienta. Ta opcja wdrażania jest najbardziej przydatna dla scenariuszy podnoszenia i przesunięcia aplikacji, które wymagają uprawnień opartych na usłudze AD. Ponieważ usługa Azure AD DS zapewnia uwierzytelnianie oparte na usłudze AD, ta opcja używa również protokołu uwierzytelniania Kerberos.
- **Klucz konta usługi Azure Storage** : udziały plików platformy Azure mogą być również instalowane przy użyciu klucza konta usługi Azure Storage. Aby można było zainstalować udział plików w ten sposób, nazwa konta magazynu jest używana jako nazwa użytkownika, a klucz konta magazynu jest używany jako hasło. Użycie klucza konta magazynu do zainstalowania udziału plików platformy Azure jest skuteczną operacją administratora, ponieważ zainstalowany udział plików będzie miał pełne uprawnienia do wszystkich plików i folderów w udziale, nawet jeśli mają listy ACL. W przypadku korzystania z klucza konta magazynu w celu zainstalowania za pośrednictwem protokołu SMB jest używany protokół uwierzytelniania NTLMv2.

W przypadku klientów migrowania z lokalnych serwerów plików lub tworzenia nowych udziałów plików w Azure Files przeznaczonych do zachowania, takich jak serwery plików systemu Windows lub urządzenia NAS, domena przyłączania do konta magazynu do **Active Directory należącego do klienta** jest zalecaną opcją. Aby dowiedzieć się więcej na temat dołączania do konta magazynu do Active Directory należącego do klienta, zobacz [Azure Files Active Directory przegląd](storage-files-active-directory-overview.md).

Jeśli zamierzasz korzystać z klucza konta magazynu w celu uzyskania dostępu do udziałów plików platformy Azure, zalecamy używanie punktów końcowych usługi zgodnie z opisem w sekcji dotyczącej [sieci](#networking) .

## <a name="networking"></a>Networking
Udziały plików platformy Azure są dostępne z dowolnego miejsca za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą bezpiecznie pochodziły z platformy Azure lub spoza niej. W wielu środowiskach klienta początkowa instalacja udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, nawet jeśli instalacje z maszyn wirtualnych platformy Azure powiodą się. Przyczyną tego jest to, że wiele organizacji i usługodawców internetowych (ISP) blokują port wykorzystywany przez protokół SMB do komunikacji, port 445. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby odblokować dostęp do udziału plików platformy Azure, masz dwie opcje główne:

- Odblokuj port 445 dla sieci lokalnej w organizacji. Do udziałów plików platformy Azure można uzyskać dostęp tylko zewnętrznie za pośrednictwem publicznego punktu końcowego za pomocą bezpiecznych protokołów internetowych, takich jak SMB 3,0 i interfejs API FileREST. Jest to najprostszy sposób na dostęp do udziału plików platformy Azure z lokalnego, ponieważ nie wymaga zaawansowanej konfiguracji sieci poza zmianą reguł portów wychodzących w organizacji, ale zalecamy usunięcie starszych i przestarzałych wersji protokołu SMB, czyli SMB 1,0. Aby dowiedzieć się, jak to zrobić, zobacz [Zabezpieczanie systemu Windows/systemu Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) i [Zabezpieczanie Linux](storage-how-to-use-files-linux.md#securing-linux).

- Dostęp do udziałów plików platformy Azure za pośrednictwem połączenia ExpressRoute lub sieci VPN. Gdy uzyskujesz dostęp do udziału plików platformy Azure za pośrednictwem tunelu sieciowego, możesz zainstalować udział plików platformy Azure, taki jak lokalny udział plików, ponieważ ruch SMB nie przechodzi przez granicę organizacyjną.   

Chociaż z perspektywy technicznej znacznie łatwiej jest instalować udziały plików platformy Azure za pośrednictwem publicznego punktu końcowego, oczekuje się, że większość klientów będzie instalować udziały plików platformy Azure za pośrednictwem połączenia ExpressRoute lub sieci VPN. Instalowanie z tymi opcjami jest możliwe zarówno w przypadku udziałów SMB, jak i NFS. W tym celu należy skonfigurować następujące elementy dla danego środowiska:  

- **Tunelowanie sieciowe przy użyciu ExpressRoute, lokacja-lokacja lub sieci VPN typu punkt-lokacja** : tunelowanie do sieci wirtualnej umożliwia dostęp do udziałów plików platformy Azure z lokalnego, nawet jeśli port 445 jest zablokowany.
- **Prywatne punkty końcowe** : prywatne punkty końcowe zapewniają konto magazynu dedykowany adres IP z przestrzeni adresowej sieci wirtualnej. Umożliwia to tunelowanie sieciowe bez konieczności otwierania sieci lokalnych do wszystkich zakresów adresów IP należących do klastrów usługi Azure Storage. 
- **Przekazywanie DNS** : Skonfiguruj lokalny serwer DNS, aby rozpoznać nazwę konta magazynu (tj. `storageaccount.file.core.windows.net` dla regionów chmury publicznej) w celu rozpoznania adresu IP prywatnych punktów końcowych.

Aby zaplanować sieć skojarzoną z wdrażaniem udziału plików platformy Azure, zapoznaj się z tematem [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md).

## <a name="encryption"></a>Szyfrowanie
Azure Files obsługuje dwa różne typy szyfrowania: szyfrowanie podczas przesyłania, które odnosi się do szyfrowania używanego podczas instalowania/uzyskiwania dostępu do udziału plików platformy Azure oraz szyfrowania w spoczynku, które odnosi się do sposobu szyfrowania danych przechowywanych na dysku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!IMPORTANT]
> Ta sekcja zawiera szczegółowe informacje dotyczące szyfrowania dla udziałów SMB. Aby uzyskać szczegółowe informacje dotyczące szyfrowania podczas przesyłania z udziałami NFS, zobacz [zabezpieczenia](storage-files-compare-protocols.md#security).

Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. Oznacza to, że podczas instalowania udziału plików przez protokół SMB lub uzyskiwania dostępu do niego za pośrednictwem protokołu FileREST (na przykład za pośrednictwem Azure Portal, PowerShell/interfejsu wiersza polecenia lub zestawów SDK platformy Azure), Azure Files będzie zezwalać tylko na połączenie, jeśli zostało wykonane przy użyciu protokołu SMB 3.0 + z szyfrowaniem lub HTTPS. Klienci, którzy nie obsługują protokołu SMB 3,0 ani klientów obsługujących protokół SMB 3,0, ale nie szyfrowania SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie jest włączone. Więcej informacji o tym, które systemy operacyjne obsługują protokół SMB 3,0 z szyfrowaniem, można znaleźć w naszej szczegółowej dokumentacji dotyczącej [systemów Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md). Wszystkie bieżące wersje programu PowerShell, interfejsu wiersza polecenia i zestawów SDK obsługują protokół HTTPS.  

Można wyłączyć szyfrowanie podczas przesyłania dla konta usługi Azure Storage. Gdy szyfrowanie jest wyłączone, Azure Files również zezwala na SMB 2,1, SMB 3,0 bez szyfrowania i niezaszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Podstawowym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchomiona w starszym systemie operacyjnym, takim jak Windows Server 2008 R2 lub starsza dystrybucja systemu Linux. Azure Files zezwala tylko na połączenia SMB 2,1 w tym samym regionie świadczenia usługi Azure co udział plików platformy Azure; klient SMB 2,1 spoza regionu platformy Azure udziału plików platformy Azure, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Zdecydowanie zalecamy zagwarantowanie, że szyfrowanie danych jest włączone.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Ochrona danych
Azure Files ma wielowarstwowe podejście do zapewnienia, że kopie zapasowe danych są tworzone, odzyskiwalne i chronione przed zagrożeniami bezpieczeństwa.

### <a name="soft-delete"></a>Usuwanie nietrwałe
Usuwanie nietrwałe dla udziałów plików (wersja zapoznawcza) to ustawienie poziomu konta magazynu, które pozwala na odzyskanie udziału plików po jego przypadkowym usunięciu. Po usunięciu udziału plików przechodzi do nietrwałego stanu usuniętego, a nie na stałe wymazywanie. Można skonfigurować ilość czasu nietrwałego usuwania danych, zanim zostanie on trwale usunięty, i Cofnij usunięcie udziału w dowolnym momencie w tym okresie przechowywania. 

Zalecamy włączenie usuwania nietrwałego dla większości udziałów plików. W przypadku przepływu pracy, w którym jest używane usuwanie udziałów i oczekiwanie, możesz zdecydować, że masz bardzo krótki okres przechowywania lub nie masz włączonego usuwania nietrwałego.

Aby uzyskać więcej informacji na temat usuwania nietrwałego, zobacz [zapobieganie przypadkowemu](./storage-files-prevent-file-share-deletion.md)usuwaniu danych.

### <a name="backup"></a>Backup
Można utworzyć kopię zapasową udziału plików platformy Azure za pośrednictwem [migawek udziałów](./storage-snapshots-files.md), które są tylko do odczytu i są kopiami danego udziału. Migawki są przyrostowe, co oznacza, że zawierają one tylko ilość danych, które uległy zmianie od poprzedniej migawki. Na udział plików można uzyskać maksymalnie 200 migawek i przechowywać je przez maksymalnie 10 lat. Można ręcznie wykonać te migawki w Azure Portal za pomocą programu PowerShell lub interfejsu wiersza polecenia (CLI) lub użyć [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json). Migawki są przechowywane w udziale plików, co oznacza, że jeśli usuniesz udział plików, migawki również zostaną usunięte. Aby chronić kopie zapasowe migawki przed przypadkowym usunięciem, upewnij się, że w danym udziale jest włączona funkcja usuwania nietrwałego.

[Azure Backup dla udziałów plików platformy Azure](../../backup/azure-file-share-backup-overview.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) obsługuje planowanie i przechowywanie migawek. Jej możliwości dla dziadka-ojciec-syn (GFS) oznaczają, że możesz wykonywać migawki codziennie, co tydzień, co miesiąc i co roku, z których każdy ma własny odrębny okres przechowywania. Azure Backup również organizuje włączenie usuwania nietrwałego i przyjmuje blokadę usuwania na koncie magazynu zaraz po skonfigurowaniu wszystkich udziałów plików w ramach tej kopii zapasowej. Na koniec Azure Backup zapewnia pewne kluczowe funkcje monitorowania i alertów, które umożliwiają klientom skonsolidowany widok ich kopii zapasowej.

Można wykonywać zarówno przywracanie na poziomie elementu, jak i na poziomie udziału w Azure Portal przy użyciu Azure Backup. Wystarczy wybrać punkt przywracania (konkretną migawkę), określony plik lub katalog, jeśli ma zastosowanie, a następnie lokalizację (oryginalną lub alternatywną), do której chcesz przywrócić. Usługa Backup obsługuje kopiowanie danych migawek do i pokazuje postęp przywracania w portalu.

Aby uzyskać więcej informacji na temat kopii zapasowej, zobacz [Informacje o kopii zapasowej udziału plików platformy Azure](../../backup/azure-file-share-backup-overview.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Files (wersja zapoznawcza)
Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która zapewnia alerty w przypadku wykrycia nietypowej aktywności na koncie magazynu, na przykład nietypowe próby dostępu do konta magazynu. ATP uruchamia również analizę reputacji skrótu złośliwego oprogramowania i alertuje o znanym złośliwym oprogramowaniu. ATP można skonfigurować na poziomie subskrypcji lub konta magazynu za pośrednictwem Azure Security Center. 

Aby uzyskać więcej informacji, zobacz [Advanced Threat Protection for Azure Storage](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Ogólnie rzecz biorąc, funkcje Azure Files i współdziałanie z innymi usługami są takie same między udziałami plików w warstwie Premium a standardowymi udziałami plików (w tym optymalizacjami transakcji, gorącą i chłodnymi udziałami plików), jednak istnieje kilka istotnych różnic:
- **Model rozliczania**
    - Udziały plików w warstwie Premium są rozliczane przy użyciu modelu rozliczania z zainicjowaną obsługą, co oznacza, że opłata jest naliczana zgodnie z ustaloną ceną za ilość miejsca w magazynie, która jest używana. Nie ma dodatkowych kosztów transakcji i metadanych w spoczynku.
    - Standardowe udziały plików są rozliczane przy użyciu modelu płatności zgodnie z rzeczywistym użyciem, który obejmuje podstawowy koszt magazynu, w którym faktycznie zużywa się magazyn, a następnie dodatkowy koszt transakcji na podstawie sposobu korzystania z udziału. W przypadku standardowych udziałów plików rachunek zostanie zwiększony w przypadku użycia (odczytu/zapisu/instalacji) udziału plików platformy Azure.
- **Opcje nadmiarowości**
    - Udziały plików w warstwie Premium są dostępne tylko dla magazynu lokalnie nadmiarowego (LRS) i strefy nadmiarowego (ZRS).
    - Standardowe udziały plików są dostępne lokalnie nadmiarowe, nadmiarowe strefy, Geograficznie nadmiarowy (GRS) i strefę geograficzną nadmiarowy (GZRS).
- **Maksymalny rozmiar udziału plików**
    - Udziały plików w warstwie Premium można obsługiwać nawet do 100 TiB bez żadnej dodatkowej pracy.
    - Domyślnie standardowe udziały plików mogą obejmować maksymalnie 5 TiB, chociaż limit udostępniania można zwiększyć do 100 TiB przez wypróbowanie flagi funkcji konta magazynu *dużych udziałów plików* . Standardowe udziały plików mogą obejmować maksymalnie 100 TiB w przypadku kont magazynu lokalnie nadmiarowe lub strefy nadmiarowe. Aby uzyskać więcej informacji na temat zwiększania rozmiarów udziałów plików, zobacz [Włączanie i tworzenie dużych udziałów plików](./storage-files-how-to-create-large-file-share.md).
- **Dostępność regionalna**
    - Udziały plików w warstwie Premium są dostępne w większości regionów świadczenia usługi Azure z wyjątkiem kilku regionów. Obsługa strefowo nadmiarowy jest dostępna w podzestawie regionów. Aby dowiedzieć się, czy w Twoim regionie są obecnie dostępne udziały plików w warstwie Premium, zobacz stronę [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure. Aby dowiedzieć się, które regiony obsługują ZRS, zobacz [Magazyn strefowo nadmiarowy](../common/storage-redundancy.md#zone-redundant-storage). Aby ułatwić nam określanie priorytetów nowych regionów i funkcji warstwy Premium, Wypełnij tę [ankietę](https://aka.ms/pfsfeedback).
    - Standardowe udziały plików są dostępne w każdym regionie świadczenia usługi Azure.
- Usługa Azure Kubernetes Service (AKS) obsługuje udziały plików w warstwie Premium w wersji 1,13 i nowszych.

Gdy udział plików zostanie utworzony jako udział plików w warstwie Premium lub standardowa, nie można go automatycznie przekonwertować na drugą warstwę. Jeśli chcesz przełączyć się do innej warstwy, musisz utworzyć nowy udział plików w tej warstwie i ręcznie skopiować dane z oryginalnego udziału do utworzonego nowego udziału. `robocopy` `rsync` Aby wykonać tę kopię, zalecamy użycie dla systemu Windows lub dla systemów MacOS i Linux.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Informacje o aprowizacji dla udziałów plików w warstwie Premium
Udziały plików w warstwie Premium są udostępniane na podstawie stałego współczynnika GiB/IOPS/przepływności. Dla każdego zainicjowanej GiB udział zostanie wystawiony przez wiele operacji we/wy i 0,1 MiB/s do maksymalnej liczby limitów na udział. Minimalną dozwoloną alokacją jest 100 GiB z minimalnymi operacjami IOPS/przepływności.

Na podstawie najlepszego wysiłku wszystkie udziały mogą wykonać maksymalnie trzy liczby operacji we/wy na sekundę GiB magazynu, 60 w zależności od rozmiaru udziału. Nowe udziały zaczynają pełne środki na korzystanie z systemu na podstawie zainicjowanej pojemności.

Udziały muszą być udostępniane w 1 przyrostach GiB. Minimalny rozmiar to 100 GiB, następny rozmiar to 101 GiB i tak dalej.

> [!TIP]
> Liczba operacji we/wy dla linii bazowej = 1 * zainicjowana GiB. (Maksymalnie 100 000 operacji we/wy).
>
> Limit serii = 3 * Liczba operacji wejścia/wyjścia dla linii bazowej. (Maksymalnie 100 000 operacji we/wy).
>
> szybkość ruchu wychodzącego = 60 MiB/s + 0,06 * zainicjowana GiB
>
> transfer danych przychodzących = 40 MiB/s + 0,04 * aprowizacji GiB

Udostępniony rozmiar udziału jest określany przez przydział udziału. Przydział udziału można zwiększyć w dowolnym momencie, ale może zostać zmniejszony dopiero po 24 godzinach od momentu ostatniego wzrostu. Po odczekaniu przez 24 godziny bez zwiększenia limitu przydziału można zmniejszyć przydział udziału dowolną liczbę razy, aż do momentu jego zwiększenia. Zmiany skali operacji we/wy na sekundę będą obowiązywać w ciągu kilku minut od zmiany rozmiaru.

Istnieje możliwość zmniejszenia rozmiaru udziału przystosowanego poniżej użytych GiB. Jeśli to zrobisz, nie utracisz danych, ale nadal będą naliczane opłaty za używany rozmiar i zostanie uzyskana wydajność (liczba operacji wejścia/wyjścia na sekundę, przepływność i liczba IOPS dla operacji wejścia/wyjścia na sekundę) udziału, który nie jest używany.

W poniższej tabeli przedstawiono kilka przykładów tych wzorów dla rozmiarów udostępnianych udziałów:

|Pojemność (GiB) | Liczba IOPS linii bazowej | Operacje we/wy na sekundę | Ruch wychodzący (MiB/s) | Ruch przychodzący (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Do 300     | 66   | 44   |
|500         | 500     | Do 1 500   | 90   | 60   |
|1 024       | 1 024   | Do 3 072   | 122   | 81   |
|5 120       | 5 120   | Do 15 360  | 368   | 245   |
|10 240      | 10 240  | Do 30 720  | 675 | 450   |
|33 792      | 33 792  | Do 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Do 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Do 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Wydajność udziałów plików zależy od ograniczeń sieci maszynowych, dostępnej przepustowości sieci, rozmiarów we/wy, równoległości, między wieloma innymi czynnikami. Na przykład w oparciu o testowanie wewnętrzne z 8 KiB rozmiaru we/wy odczytu/zapisu, Pojedyncza maszyna wirtualna z systemem Windows, *standardowa F16s_v2* , połączona z udziałem plików w warstwie Premium za pośrednictwem protokołu SMB, może osiągnąć 20 000 odczyt operacji wejścia/wyjścia (IOPS) i 15 000 W przypadku rozmiaru operacji we/wy odczytu i zapisu na 512 MiB ta sama maszyna wirtualna może osiągnąć 1,1 GiB/s ruch wychodzący i 370 przepływność transferu danych (MiB/s). Aby osiągnąć maksymalną skalę wydajności, należy rozłożyć obciążenie na wiele maszyn wirtualnych. Zapoznaj się [z przewodnikiem rozwiązywania problemów](storage-troubleshooting-files-performance.md) w przypadku niektórych typowych problemów z wydajnością i obejść.

#### <a name="bursting"></a>Rozszerzanie możliwości
Udziały plików w warstwie Premium mogą zwiększać liczbę operacji we/wy na sekundę. Rozliczanie jest zautomatyzowane i działa na podstawie systemu kredytowego. Przeszukiwanie odbywa się na podstawie najlepszego nakładu pracy, a limit graniczny nie jest gwarancją, udziały *up to* plików mogą przekroczyć limit.

Kredyty są gromadzone w zasobniku, gdy ruch dla udziału plików jest poniżej liczby operacji wejścia/wyjścia na sekundę. Na przykład udział GiB 100 ma 100 liczby operacji wejścia/wyjścia na sekundę. Jeśli rzeczywisty ruch w udziale był 40 operacji we/wy dla określonego interwału 1 sekund, wówczas liczba nieużywanych operacji 60 we/wy na sekundę jest księgowana w zasobniku serii. Te kredyty zostaną następnie użyte później, gdy operacje przekroczą liczbę IOPs linii bazowej.

> [!TIP]
> Rozmiar zasobnika szeregowego = liczba IOPS linii bazowej * 2 * 3600.

Za każdym razem, gdy udział przekracza liczbę operacji we/wy na sekundę i ma kredyty w zasobniku szeregowym, spowoduje to utworzenie serii. Udziały mogą w dalszym ciągu przekroczyć czas trwania obciążeń, chociaż udziały mniejsze niż 50 TiB będą przechowywane tylko przy maksymalnej szybkości przez maksymalnie godzinę. Udziały większe niż 50 TiB mogą technicznie przekroczyć ten limit godzin, do dwóch godzin, ale jest to oparte na liczbie naliczanych kredytów na korzystanie z serii. Poszczególne operacje we/wy poza linią bazową zużywają jedno środki, a po wykorzystaniu wszystkich środków udział zwróci wartość do operacji wejścia/wyjścia na sekundę.

Kredyty na udostępnianie mają trzy stany:

- Naliczanie, gdy udział plików używa mniejszej niż liczba operacji we/wy na sekundę.
- Odrzucanie, gdy udział plików jest rozruchowy.
- Pozostała stała, gdy nie ma żadnych kredytów lub operacji wejścia/wyjścia linii bazowej.

Nowe udziały plików zaczynają się od pełnej liczby kredytów w swoim zasobniku. Środki na korzystanie z serii nie zostaną naliczone, jeśli liczba operacji we/wy udziałów spadnie poniżej liczby operacji wejścia/wyjścia na sekundę z powodu ograniczenia przez serwer.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włącz standardowe udziały plików do 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Ograniczenia
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migracja
W wielu przypadkach nie zostanie nadany nowy udział plików w sieci dla organizacji, ale zamiast tego zostanie przemigrowany istniejący udział plików z lokalnego serwera plików lub urządzenia NAS do Azure Files. Wybranie odpowiedniej strategii migracji i narzędzia dla danego scenariusza jest istotne dla sukcesu migracji. 

[Artykuł Omówienie migracji](storage-files-migration-overview.md) krótko dotyczy podstaw i zawiera tabelę zawierającą wskazówki dotyczące migracji, które mogą obejmować twój scenariusz.

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
* [Wdrażanie Azure Files](storage-files-deployment-guide.md)
* [Wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md)
* [Zapoznaj się z artykułem Omówienie migracji, aby znaleźć przewodnik migracji dla danego scenariusza](storage-files-migration-overview.md)