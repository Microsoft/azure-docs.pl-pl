---
title: Przegląd — Azure Files Autoryzacja oparta na tożsamościach
description: Azure Files obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem Azure Active Directory Domain Services (AD DS) i Active Directory. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536588"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Omówienie usługi Azure Files uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć lokalne uwierzytelnianie Active Directory Domain Services dla udziałów plików platformy Azure (wersja zapoznawcza), zobacz [Włączanie uwierzytelniania lokalnego Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md).

Aby dowiedzieć się, jak włączyć uwierzytelnianie AD DS Azure dla udziałów plików platformy Azure, zobacz [Włączanie uwierzytelniania Azure Active Directory Domain Services na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Słownik 
Warto zrozumieć niektóre kluczowe terminy związane z uwierzytelnianiem w usłudze Azure AD Domain Service za pośrednictwem protokołu SMB dla udziałów plików platformy Azure:

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania używany do weryfikacji tożsamości użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz temat [uwierzytelnianie Kerberos — Omówienie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół bloku komunikatów serwera (SMB)**

    SMB jest standardowym protokołem udostępniania plików w branży. Protokół SMB jest również znany jako wspólny internetowy system plików lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [Omówienie protokołów SMB i CIFS firmy Microsoft](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu. Przyłączone do usługi Azure AD maszyny wirtualne z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (AD DS platformy Azure)**

    Usługa Azure AD DS udostępnia zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM. Te usługi są w pełni zgodne z Active Directory Domain Services. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services lokalne (AD DS)**

    Integracja Active Directory Domain Services lokalnych (AD DS) z programem Azure Files (wersja zapoznawcza) zapewnia metody przechowywania danych katalogowych podczas udostępniania ich użytkownikom i administratorom sieci. Zabezpieczenia są zintegrowane z AD DS za pomocą uwierzytelniania logowania i kontroli dostępu do obiektów w katalogu. Za pomocą pojedynczego logowania do sieci administratorzy mogą zarządzać danymi katalogowymi i organizacją w całej sieci, a autoryzowani użytkownicy sieci mogą uzyskiwać dostęp do zasobów w dowolnym miejscu w sieci. AD DS jest zazwyczaj akceptowana przez przedsiębiorstwa w środowiskach lokalnych, a poświadczenia AD DS są używane jako tożsamość kontroli dostępu. Aby uzyskać więcej informacji, zobacz [Active Directory Domain Services przegląd](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Access Control oparte na rolach (RBAC) na platformie Azure**

    Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Za pomocą RBAC można zarządzać dostępem do zasobów, przyznając użytkownikom najmniejsze uprawnienia potrzebne do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Typowe przypadki użycia

Uwierzytelnianie oparte na tożsamości i pomoc techniczna dla list ACL systemu Windows w Azure Files jest najlepiej wykorzystane w następujących przypadkach użycia:

### <a name="replace-on-premises-file-servers"></a>Zastępowanie lokalnych serwerów plików

Przestarzałe i zastępowane lokalne serwery plików są typowym problemem, który każde przedsiębiorstwo napotyka w trakcie jego modernizacji. Udziały plików platformy Azure z uwierzytelnianiem lokalnym AD DS (wersja zapoznawcza) są najlepszym rozwiązaniem w tym miejscu, gdy można migrować dane do Azure Files. Pełna migracja umożliwi skorzystanie z zalet wysokiej dostępności i skalowalności, jednocześnie minimalizując zmiany po stronie klienta. Zapewnia bezproblemowe środowisko migracji użytkownikom końcowym, dzięki czemu może nadal uzyskiwać dostęp do danych za pomocą tych samych poświadczeń przy użyciu istniejących maszyn przyłączonych do domeny.

### <a name="lift-and-shift-applications-to-azure"></a>Podnieś i Przenieś aplikacje na platformę Azure

Podczas podnoszenia i przesunięcia aplikacji do chmury chcesz zachować ten sam model uwierzytelniania dla danych. W miarę jak rozszerzono środowisko kontroli dostępu oparte na tożsamościach do udziałów plików platformy Azure, eliminuje konieczność zmiany aplikacji na nowoczesne metody uwierzytelniania i przyspieszanie wdrażania chmury. Udziały plików platformy Azure udostępniają opcję integracji z usługą Azure AD DS lub AD DS lokalnym (wersja zapoznawcza) na potrzeby uwierzytelniania. Jeśli plan ma być 100% w chmurze i minimalizuje wysiłki związane z zarządzaniem infrastrukturami w chmurze, platforma Azure AD DS będzie lepiej dopasowana jako w pełni zarządzana usługa domeny. Jeśli wymagana jest pełna zgodność z możliwościami AD DS, warto rozważyć rozszerzenie środowiska AD DS na chmurę przez samoobsługowe kontrolery domeny na maszynach wirtualnych. W obu przypadkach zapewniamy elastyczność wybierania usług domenowych, które odpowiadają potrzebom biznesowym.

### <a name="backup-and-disaster-recovery-dr"></a>Kopia zapasowa i odzyskiwanie po awarii (DR)

Jeśli przechowujesz podstawowy magazyn plików lokalnie, udziały plików platformy Azure mogą działać jako idealny magazyn do tworzenia kopii zapasowych i odzyskiwania po awarii, aby zwiększyć ciągłość działania firmy. Udziałów plików platformy Azure można używać do tworzenia kopii zapasowych danych z istniejących serwerów plików, zachowując jednocześnie listy DACL systemu Windows. W przypadku scenariuszy odzyskiwania po awarii można skonfigurować opcję uwierzytelniania, która będzie obsługiwać odpowiednie wymuszanie kontroli dostępu w trybie failover.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Poniższa tabela zawiera podsumowanie obsługiwanych scenariuszy uwierzytelniania udziałów plików platformy Azure dla usługi Azure AD DS i lokalnych AD DS (wersja zapoznawcza). Zalecamy wybranie usługi domeny, która została przyjęta dla środowiska klienta na potrzeby integracji z Azure Files. Jeśli masz AD DS (wersja zapoznawcza), która jest już skonfigurowana lokalnie lub na platformie Azure, w której Twoje urządzenia są przyłączone do usługi AD, należy wybrać opcję korzystania z AD DS (wersja zapoznawcza) uwierzytelniania udziałów plików platformy Azure. Podobnie, jeśli już przyjęto usługę Azure AD DS (GA), należy ją użyć do uwierzytelniania udziałów plików platformy Azure.


|Uwierzytelnianie za pomocą usługi Azure AD DS  | Uwierzytelnianie AD DS lokalnego (wersja zapoznawcza)  |
|---------|---------|
|Przyłączone do AD DS maszyny z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD za pośrednictwem protokołu SMB.     |Lokalne przyłączone do AD DS maszyny z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure z lokalnymi poświadczeniami Active Directory, które są synchronizowane z usługą Azure AD za pośrednictwem protokołu SMB.         |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

- Usługa Azure AD DS i lokalne uwierzytelnianie AD DS nie obsługują uwierzytelniania dla kont komputerów. Zamiast tego możesz rozważyć użycie konta logowania do usługi.
- Uwierzytelnianie za pomocą usługi Azure AD DS nie obsługuje uwierzytelniania w przypadku urządzeń przyłączonych do usługi Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Zalety uwierzytelniania opartego na tożsamościach
Uwierzytelnianie oparte na tożsamościach dla Azure Files oferuje kilka korzyści z używania uwierzytelniania klucza współużytkowanego:

-   **Rozpoznaj tradycyjne środowisko dostępu do udziału plików oparte na tożsamościach w chmurze dzięki lokalnym AD DS i platformie Azure AD DS**  
    Jeśli planujesz podnieśność i przeniesiesz aplikację do chmury, Zastąp tradycyjne serwery plików z udziałami plików platformy Azure, a następnie możesz zalogować się do aplikacji przy użyciu lokalnych AD DS lub poświadczeń AD DS platformy Azure, aby uzyskać dostęp do danych plików. Azure Files obsługuje używanie lokalnych AD DS lub poświadczeń AD DS platformy Azure do uzyskiwania dostępu do udziałów plików platformy Azure za pośrednictwem protokołu SMB z lokalnych AD DS lub platformy Azure AD DS maszyn wirtualnych przyłączonych do domeny.

-   **Wymuś szczegółową kontrolę dostępu w udziałach plików platformy Azure**  
    Można przyznać uprawnienia do określonej tożsamości na poziomie udziału, katalogu lub pliku. Załóżmy na przykład, że masz kilka zespołów korzystających z jednego udziału plików platformy Azure do współpracy z projektem. Można udzielić wszystkim zespołom dostępu do niewrażliwych katalogów, ograniczając dostęp do katalogów zawierających poufne dane finansowe tylko do zespołu finansowego. 

-   **Tworzenie kopii zapasowych list ACL systemu Windows (znanych również jako NTFS) wraz z danymi**  
    Za pomocą udziałów plików platformy Azure można tworzyć kopie zapasowe istniejących lokalnych udziałów plików. Azure Files zachowuje listy kontroli dostępu wraz z danymi podczas tworzenia kopii zapasowej udziału plików w udziałach plików platformy Azure za pośrednictwem protokołu SMB.

## <a name="how-it-works"></a>Jak to działa

Udziały plików platformy Azure obsługują uwierzytelnianie Kerberos w celu integracji z usługą Azure AD DS lub AD DS lokalnym (wersja zapoznawcza). Aby można było włączyć uwierzytelnianie w udziałach plików platformy Azure, należy najpierw skonfigurować środowisko domeny. W przypadku uwierzytelniania za pomocą usługi Azure AD DS należy włączyć Azure AD Domain Services i dołączanie domeny do maszyn wirtualnych, z których planujesz uzyskać dostęp do danych plików. Przyłączona do domeny maszyna wirtualna musi znajdować się w tej samej sieci wirtualnej co AD DS platformy Azure. Podobnie w przypadku uwierzytelniania lokalnego AD DS (wersja zapoznawcza) musisz skonfigurować kontroler domeny i domenę dołączać do maszyn lub maszyn wirtualnych.

Gdy tożsamość skojarzona z aplikacją działającą na maszynie wirtualnej próbuje uzyskać dostęp do danych w udziałach plików platformy Azure, żądanie jest wysyłane do usługi Azure AD DS w celu uwierzytelnienia tożsamości. W przypadku pomyślnego uwierzytelnienia usługa Azure AD DS zwraca token Kerberos. Aplikacja wysyła żądanie zawierające token Kerberos, a udziały plików platformy Azure używają tego tokenu do autoryzowania żądania. Udziały plików platformy Azure otrzymują tylko token i nie utrwalają poświadczeń usługi Azure AD DS. Lokalne uwierzytelnianie AD DS działa w podobny sposób, gdzie AD DS udostępnia token Kerberos.

![Zrzut ekranu przedstawiający diagram uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Włączanie uwierzytelniania opartego na tożsamościach

Uwierzytelnianie oparte na tożsamościach można włączyć za pomocą usługi Azure AD DS lub lokalnego AD DS (wersja zapoznawcza) dla udziałów plików platformy Azure na nowych i istniejących kontach magazynu. Tylko jedna usługa domeny może być używana do uwierzytelniania dostępu do plików na koncie magazynu, które ma zastosowanie do wszystkich udziałów plików na koncie. Szczegółowe wskazówki dotyczące konfigurowania udziałów plików na potrzeby uwierzytelniania przy użyciu usługi Azure AD DS w naszym artykule [umożliwiają Azure Active Directory Domain Services uwierzytelnianie w Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) i wskazówki dotyczące AD DS lokalnych (wersja zapoznawcza) w naszym innym artykule. [włącz lokalne uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Skonfiguruj uprawnienia na poziomie udziału dla Azure Files

Po włączeniu uwierzytelniania usługi Azure AD DS lub lokalnego AD DS (wersja zapoznawcza) można używać wbudowanych ról RBAC lub konfigurować role niestandardowe dla tożsamości usługi Azure AD i przypisywać prawa dostępu do dowolnych udziałów plików na kontach magazynu. Przypisane uprawnienie umożliwia uzyskanie dostępu tylko do udziału, a nie w katalogu głównym. Nadal trzeba oddzielnie skonfigurować uprawnienia na poziomie katalogu lub pliku dla udziałów plików platformy Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie katalogu lub pliku dla Azure Files

Udziały plików platformy Azure wymuszają standardowe uprawnienia do plików systemu Windows zarówno na poziomie katalogu, jak i pliku, łącznie z katalogiem głównym. Konfiguracja uprawnień na poziomie katalogu lub pliku jest obsługiwana w przypadku protokołu SMB i REST. Zainstaluj docelowy udział plików z maszyny wirtualnej i Skonfiguruj uprawnienia za pomocą Eksploratora plików systemu Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)systemu Windows lub polecenia [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Użyj klucza konta magazynu dla uprawnień administratora

Użytkownik z kluczem konta magazynu może uzyskać dostęp do udziałów plików platformy Azure z uprawnieniami administratora. Uprawnienia administratora omijają wszystkie ograniczenia kontroli dostępu.

> [!IMPORTANT]
> Zalecanym najlepszym rozwiązaniem w zakresie zabezpieczeń jest uniknięcie udostępniania kluczy konta magazynu i użycia uwierzytelniania opartego na tożsamościach, jeśli jest to możliwe.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachowaj listy ACL katalogów i plików podczas importowania danych do udziałów plików platformy Azure

Azure Files obsługuje zachowanie list ACL na poziomie katalogu lub pliku podczas kopiowania danych do udziałów plików platformy Azure. Listy ACL z katalogu lub pliku można kopiować do udziałów plików platformy Azure przy użyciu Azure File Sync lub wspólnych zestawów narzędzi przenoszenia plików. Na przykład można użyć [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) z `/copy:s` flagą, aby skopiować dane, a także listy ACL do udziału plików platformy Azure. Listy ACL są domyślnie zachowywane, nie jest wymagane włączenie uwierzytelniania opartego na tożsamościach na koncie magazynu w celu zachowania list ACL.

## <a name="pricing"></a>Cennik
Nie ma dodatkowej opłaty za usługę w celu włączenia uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB na koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [Azure Files ceny](https://azure.microsoft.com/pricing/details/storage/files/) i [Azure AD Domain Services cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Azure Files i uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Planowanie wdrażania usługi Pliki Azure](storage-files-planning.md)
- [Włącz lokalne uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md)
- [Włącz uwierzytelnianie Azure Active Directory Domain Services na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Często zadawane pytania](storage-files-faq.md)
