---
title: Planowanie wdrożenia Azure Files wdrożenia | Microsoft Docs
description: Zrozumienie planowania wdrożenia Azure Files wdrożenia. Możesz bezpośrednio zainstalować udział plików platformy Azure lub buforować udział plików platformy Azure lokalnie przy użyciu Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cb3bee770653173f1a40b209c27d2dc92c7df11
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718039"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure
[Azure Files](storage-files-introduction.md) można wdrożyć na dwa główne sposoby: przez bezpośrednie instalowanie bez serwera udziałów plików platformy Azure lub buforowanie udziałów plików platformy Azure lokalnie przy użyciu Azure File Sync. Wybór opcji wdrażania zmienia czynności, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- Bezpośrednia instalacji udziału plików platformy **Azure:** ponieważ usługa Azure Files zapewnia dostęp do bloku komunikatów serwera (SMB) lub sieciowego systemu plików (NFS), udziały plików platformy Azure można zainstalować lokalnie lub w chmurze przy użyciu standardowych klientów SMB lub NFS dostępnych w systemie operacyjnym. Ponieważ udziały plików platformy Azure są bez serwera, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- Buforowanie lokalnego udziału plików platformy Azure za pomocą programu **Azure File Sync**: Azure File Sync umożliwia scentralizowanie udziałów plików organizacji w usłudze Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Azure File Sync przekształca lokalną (lub w chmurze) system Windows Server w szybką pamięć podręczną udziału plików SMB platformy Azure. 

Ten artykuł dotyczy przede wszystkim kwestii wdrażania udziału plików platformy Azure, który ma zostać zainstalowany bezpośrednio przez klienta lokalnego lub w chmurze. Aby zaplanować wdrożenie Azure File Sync wdrożenia, zobacz [Planowanie wdrożenia Azure File Sync wdrożenia.](storage-sync-files-planning.md)

## <a name="available-protocols"></a>Dostępne protokoły

Azure Files oferuje dwa protokoły, które mogą być używane podczas uwierzytelniania udziałów plików, SMB i sieciowego systemu plików (NFS). Aby uzyskać szczegółowe informacje na temat tych protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Większość zawartości tego artykułu dotyczy tylko udziałów SMB. Wszystko, co ma zastosowanie do udziałów NFS, będzie w szczególności oznaczać, że ma zastosowanie.

## <a name="management-concepts"></a>Pojęcia związane z zarządzaniem
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Podczas wdrażania udziałów plików platformy Azure na kontach magazynu zalecamy:

- Wdrażanie udziałów plików platformy Azure tylko na kontach magazynu z innymi udziałami plików platformy Azure. Mimo że konta magazynu GPv2 umożliwiają korzystanie z kont magazynu o różnych celach, ponieważ zasoby magazynu, takie jak udziały plików platformy Azure i kontenery obiektów blob, współdzielą limity konta magazynu, łączenie zasobów ze sobą może utrudnić późniejsze rozwiązywanie problemów z wydajnością. 

- Zwracając uwagę na ograniczenia dotyczące IOPS konta magazynu podczas wdrażania udziałów plików platformy Azure. Najlepiej byłoby mapować udziały plików 1:1 na konta magazynu, jednak nie zawsze jest to możliwe z powodu różnych ograniczeń, zarówno ze strony organizacji, jak i z platformy Azure. Jeśli nie jest możliwe wdrożenie tylko jednego udziału plików na jednym koncie magazynu, należy rozważyć, które udziały będą wysoce aktywne, a które będą mniej aktywne, aby zagwarantować, że najgorętsze udziały plików nie zostaną razem wprowadzone na tym samym koncie magazynu.

- Wdrażaj konta GPv2 i FileStorage oraz uaktualniaj konta GPv1 i klasyczne konta magazynu tylko wtedy, gdy znajdziesz je w środowisku. 

## <a name="identity"></a>Tożsamość
Aby uzyskać dostęp do udziału plików platformy Azure, użytkownik udziału plików musi być uwierzytelniony i mieć autoryzację dostępu do udziału. Odbywa się to na podstawie tożsamości użytkownika, który uzyskuje dostęp do udziału plików. Azure Files integruje się z trzema głównymi dostawcami tożsamości:
- **Lokalne konta Active Directory Domain Services (AD DS** lub lokalne AD DS): konta usługi Azure Storage mogą być przyłączone do domeny należącej do klienta, Active Directory Domain Services, podobnie jak serwer plików systemu Windows Server lub urządzenie NAS. Kontroler domeny można wdrożyć lokalnie, na maszynie wirtualnej platformy Azure, a nawet jako maszynę wirtualną u innego dostawcy chmury. Azure Files jest niezależny od tego, gdzie jest hostowany kontroler domeny. Po dołączeniu konta magazynu do domeny użytkownik końcowy może zainstalować udział plików przy użyciu konta użytkownika, za pomocą których zalogował się na komputerze. Uwierzytelnianie oparte na u usługach AD używa protokołu uwierzytelniania Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS kontroler domeny zarządzany przez firmę Microsoft, który może być używany dla zasobów platformy Azure. Przyłączanie domeny do konta magazynu Azure AD DS zapewnia podobne korzyści, jak przyłączanie go do usługi Active Directory należącej do klienta. Ta opcja wdrażania jest najbardziej przydatna w przypadku scenariuszy "lift-and-shift" aplikacji, które wymagają uprawnień opartych na u usługach AD. Ponieważ Azure AD DS uwierzytelnianie oparte na usługach AD, ta opcja korzysta również z protokołu uwierzytelniania Kerberos.
- **Klucz konta usługi Azure Storage:** udziały plików platformy Azure mogą być również instalowane przy użyciu klucza konta usługi Azure Storage. Aby zainstalować udział plików w ten sposób, nazwa konta magazynu jest używana jako nazwa użytkownika, a klucz konta magazynu jest używany jako hasło. Użycie klucza konta magazynu do instalacji udziału plików platformy Azure jest operacją administratora, ponieważ zainstalowany udział plików będzie miał pełne uprawnienia do wszystkich plików i folderów w tym udziału, nawet jeśli mają listy ACL. Podczas uwierzytelniania za pośrednictwem protokołu SMB przy użyciu klucza konta magazynu używany jest protokół uwierzytelniania NTLMv2.

W przypadku klientów migrujących z lokalnych serwerów plików lub tworzących nowe udziały plików w usłudze Azure Files przeznaczonych do działania jak serwery plików systemu Windows lub urządzenia NAS, zalecaną opcją jest przyłączanie konta magazynu do usługi **Active Directory** należącej do klienta. Aby dowiedzieć się więcej na temat przyłączanie konta magazynu do usługi Active Directory należącej do klienta, zobacz Azure Files Active Directory overview (Omówienie usługi [Active Directory).](storage-files-active-directory-overview.md)

Jeśli zamierzasz używać klucza konta magazynu do uzyskiwania dostępu do udziałów plików platformy Azure, zalecamy użycie punktów końcowych usługi zgodnie z opisem w [sekcji](#networking) Sieć.

## <a name="networking"></a>Sieć
Udziały plików platformy Azure są dostępne z dowolnego miejsca za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą pochodzić bezpiecznie z platformy Azure lub poza platformą Azure. W wielu środowiskach klienta początkowa instalacji udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, mimo że instalacji z maszyn wirtualnych platformy Azure zakończy się pomyślnie. Wynika to z tego, że wiele organizacji i usługodawców internetowych blokuje port 445 używany przez SMB do komunikacji. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby odblokować dostęp do udziału plików platformy Azure, masz dwie główne opcje:

- Odblokuj port 445 dla sieci lokalnej organizacji. Dostęp do udziałów plików platformy Azure można uzyskiwać zewnętrznie tylko za pośrednictwem publicznego punktu końcowego przy użyciu bezpiecznych protokołów internetowych, takich jak SMB 3.0 i interfejs API FileREST. Jest to najprostszy sposób uzyskiwania dostępu do udziału plików platformy Azure ze środowisk lokalnych, ponieważ nie wymaga zaawansowanej konfiguracji sieci poza zmianą reguł portów wychodzących w organizacji, jednak zalecamy usunięcie starszych i przestarzałych wersji protokołu SMB, czyli protokołu SMB 1.0. Aby dowiedzieć się, jak to zrobić, zobacz [Securing Windows/Windows Server (Zabezpieczanie systemu Windows/Windows Server)](storage-how-to-use-files-windows.md#securing-windowswindows-server) i [Securing Linux (Zabezpieczanie systemu Linux).](storage-how-to-use-files-linux.md#securing-linux)

- Uzyskiwanie dostępu do udziałów plików platformy Azure za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Gdy uzyskujesz dostęp do udziału plików platformy Azure za pośrednictwem tunelu sieciowego, możesz zainstalować udział plików platformy Azure, taki jak udział plików w środowisku lokalnym, ponieważ ruch SMB nie przechodzi przez granicę organizacyjną.   

Chociaż z perspektywy technicznej znacznie łatwiej jest zainstalować udziały plików platformy Azure za pośrednictwem publicznego punktu końcowego, oczekujemy, że większość klientów zdecyduje się zainstalować swoje udziały plików platformy Azure za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Instalowanie przy użyciu tych opcji jest możliwe w przypadku udziałów SMB i NFS. W tym celu należy skonfigurować następujące elementy dla środowiska:  

- Tunelowanie sieciowe przy użyciu usługi **ExpressRoute,** sieci VPN typu lokacja-lokacja lub punkt-lokacja: tunelowanie do sieci wirtualnej umożliwia dostęp do udziałów plików platformy Azure ze środowiska lokalnego, nawet jeśli port 445 jest zablokowany.
- **Prywatne punkty końcowe:** prywatne punkty końcowe zapewniają kontu magazynu dedykowany adres IP z przestrzeni adresowej sieci wirtualnej. Umożliwia to tunelowanie sieci bez konieczności otwierania sieci lokalnych do wszystkich zakresów adresów IP należących do klastrów usługi Azure Storage. 
- **Przekazywanie dalej DNS:** skonfiguruj lokalną usługę DNS tak, aby rozpoznała nazwę konta magazynu (w regionach chmury publicznej) w celu rozpoznania adresu IP prywatnych `storageaccount.file.core.windows.net` punktów końcowych.

Aby zaplanować sieć skojarzoną z wdrażaniem udziału plików platformy Azure, [zobacz Azure Files dotyczące sieci.](storage-files-networking-overview.md)

## <a name="encryption"></a>Szyfrowanie
Azure Files obsługuje dwa różne typy szyfrowania: szyfrowanie podczas przesyłania, które odnosi się do szyfrowania używanego podczas instalowaniem/uzyskiwaniem dostępu do udziału plików platformy Azure, oraz szyfrowanie magazynowanych danych, które odnosi się do sposobu szyfrowania danych przechowywanych na dysku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!IMPORTANT]
> Ta sekcja obejmuje szyfrowanie w szczegółach przesyłania dla udziałów SMB. Aby uzyskać szczegółowe informacje dotyczące szyfrowania podczas przesyłania z udziałami NFS, zobacz [Zabezpieczenia](storage-files-compare-protocols.md#security).

Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. Oznacza to, że w przypadku uwierzytelniania udziału plików za pośrednictwem protokołu SMB lub uzyskiwania do niego dostępu za pośrednictwem protokołu FileREST (na przykład za pośrednictwem protokołu Azure Portal, programu PowerShell/interfejsu wiersza polecenia lub zestawów SDK platformy Azure) usługa Azure Files zezwoli na połączenie tylko wtedy, gdy jest ono nawiązyne przy użyciu protokołu SMB 3.0+ z szyfrowaniem lub protokołem HTTPS. Klienci, którzy nie obsługują funkcji SMB 3.0 lub obsługują szyfrowanie SMB 3.0, ale nie obsługują szyfrowania SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie podczas przesyłania jest włączone. Aby uzyskać więcej informacji o tym, które systemy operacyjne obsługują szyfrowanie SMB 3.0, zobacz szczegółową dokumentację dla [systemów Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)i [Linux.](storage-how-to-use-files-linux.md) Wszystkie bieżące wersje programu PowerShell, interfejsu wiersza polecenia i zestawów SDK obsługują protokół HTTPS.  

Szyfrowanie podczas przesyłania dla konta usługi Azure Storage można wyłączyć. Gdy szyfrowanie jest wyłączone, usługa Azure Files zezwoli również na protokół SMB 2.1, SMB 3.0 bez szyfrowania i nieszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Głównym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak Windows Server 2008 R2 lub starsza dystrybucja systemu Linux. Azure Files zezwala tylko na połączenia SMB 2.1 w tym samym regionie platformy Azure co udział plików platformy Azure; Klient SMB 2.1 spoza regionu platformy Azure udziału plików platformy Azure, takiego jak lokalnie lub w innym regionie świadczenia usługi Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Zdecydowanie zalecamy zapewnienie włączonego szyfrowania danych podczas przesyłania.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Ochrona danych
Azure Files wielowarstwowe podejście do zapewniania kopii zapasowej danych, ich odzyskiwania i ochrony przed zagrożeniami bezpieczeństwa.

### <a name="soft-delete"></a>Usuwanie nietrwałe
Usuwanie nie soft dla udziałów plików (wersja zapoznawcza) to ustawienie na poziomie konta magazynu, które umożliwia odzyskanie udziału plików po jego przypadkowym usunięciu. Usunięcie udziału plików powoduje przejście do stanu usunięcia nieutrwałych zamiast trwałego usunięcia. Możesz skonfigurować czas, przez który nieużytecznie usunięte dane będą możliwe do odzyskania, zanim zostaną trwale usunięte, i cofnąć usunięcie udziału w dowolnym momencie w tym okresie przechowywania. 

Zalecamy włączenie usuwania nie soft dla większości udziałów plików. Jeśli masz przepływ pracy, w którym usuwanie udziałów jest typowe i oczekiwane, możesz zdecydować się na krótki okres przechowywania lub w ogóle nie mieć włączonego usuwania nie soft.

Aby uzyskać więcej informacji na temat usuwania nie soft, zobacz [Zapobieganie przypadkowemu usunięciu danych.](./storage-files-prevent-file-share-deletion.md)

### <a name="backup"></a>Backup
Kopię zapasową udziału plików platformy Azure można utworzyć za pomocą migawek [udziałów,](./storage-snapshots-files.md)które są kopiami udziału tylko do odczytu w czasie. Migawki są przyrostowe, co oznacza, że zawierają tylko tyle danych, ile uległo zmianie od czasu poprzedniej migawki. Można mieć maksymalnie 200 migawek na udział plików i przechowywać je przez maksymalnie 10 lat. Możesz ręcznie wykonać te migawki w programie Azure Portal, za pomocą programu PowerShell lub interfejsu wiersza polecenia (CLI) albo użyć [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Migawki są przechowywane w ramach udziału plików, co oznacza, że usunięcie udziału plików spowoduje również usunięcie migawek. Aby chronić kopie zapasowe migawek przed przypadkowym usunięciem, upewnij się, że dla udziału włączono usuwanie nieumyślne.

[Azure Backup udziałów plików platformy Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) obsługuje planowanie i przechowywanie migawek. Dzięki możliwościom gfS można samodzielnie samodzielnie chować migawki codziennie, co tydzień, co miesiąc i co rok. Każdy z nich ma odrębny okres przechowywania. Azure Backup także organizowanie włączania usuwania nie softowego i przyjmuje blokadę usuwania na koncie magazynu, gdy tylko dowolny udział plików w nim jest skonfigurowany do tworzenia kopii zapasowej. Na koniec Azure Backup pewne kluczowe funkcje monitorowania i alertów, które umożliwiają klientom skonsolidowany widok ich zasobów kopii zapasowych.

Przywracanie na poziomie elementu i na poziomie udziału można wykonać w Azure Portal przy użyciu Azure Backup. Wystarczy wybrać punkt przywracania (określoną migawkę), konkretny plik lub katalog, jeśli ma to zastosowanie, a następnie lokalizację (oryginalną lub alternatywną), do którą chcesz przywrócić. Usługa tworzenia kopii zapasowych obsługuje kopiowanie danych migawki i pokazuje postęp przywracania w portalu.

Aby uzyskać więcej informacji o kopii zapasowej, zobacz [About Azure file share backup (Informacje o kopii zapasowej udziału plików platformy Azure).](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-defender-for-azure-files"></a>Azure Defender for Azure Files 
Usługa Azure Defender dla usługi Azure Storage (wcześniej Advanced Threat Protection dla usługi Azure Storage) zapewnia dodatkową warstwę analizy zabezpieczeń, która udostępnia alerty w przypadku wykrycia nietypowej aktywności na koncie magazynu, na przykład nietypowych prób dostępu. Uruchamia również analizę reputacji skrótów złośliwego oprogramowania i ostrzega o znanym złośliwym oprogramowaniu. Możesz skonfigurować Azure Defender subskrypcji lub na poziomie konta magazynu za pośrednictwem Azure Security Center. 

Aby uzyskać więcej informacji, zobacz Introduction to Azure Defender for Storage (Wprowadzenie [do usługi Storage).](../../security-center/defender-for-storage-introduction.md)

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włączanie standardowych udziałów plików o rozmiarze do 100 TiB
Domyślnie standardowe udziały plików mogą obejmować tylko 5 TiB, ale można zwiększyć limit udziału do 100 TiB. Aby dowiedzieć się, jak zwiększyć limit udziału, zobacz [Włączanie i tworzenie dużych udziałów plików.](storage-files-how-to-create-large-file-share.md)


#### <a name="limitations"></a>Ograniczenia
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migracja
W wielu przypadkach nie zostanie ustanawianie nowego udziału plików sieciowego dla organizacji, ale zamiast tego będzie migrowanie istniejącego udziału plików z lokalnego serwera plików lub urządzenia NAS do systemu Azure Files. Wybór odpowiedniej strategii i narzędzia migracji dla twojego scenariusza jest ważny dla powodzenia migracji. 

Artykuł [z omówieniem migracji](storage-files-migration-overview.md) zawiera krótko podstawowe informacje i tabelę, która prowadzi do przewodników migracji, które prawdopodobnie obejmują Twój scenariusz.

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia Azure File Sync wdrożenia](storage-sync-files-planning.md)
* [Wdrażanie Azure Files](./storage-how-to-create-file-share.md)
* [Wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md)
* [Zapoznaj się z artykułem z omówieniem migracji, aby znaleźć przewodnik migracji dla swojego scenariusza](storage-files-migration-overview.md)