---
title: Przegląd — Azure Files Autoryzacja oparta na tożsamościach
description: Azure Files obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem Azure Active Directory Domain Services (AD DS) i Active Directory. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519796"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Azure Files Omówienie opcji uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć lokalne uwierzytelnianie Active Directory Domain Services dla udziałów plików platformy Azure, zobacz [włączenie lokalnego uwierzytelniania Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md).

Aby dowiedzieć się, jak włączyć uwierzytelnianie AD DS Azure dla udziałów plików platformy Azure, zobacz [Włączanie uwierzytelniania Azure Active Directory Domain Services na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Słownik 
Warto zrozumieć niektóre kluczowe terminy związane z uwierzytelnianiem w usłudze Azure AD Domain Service za pośrednictwem protokołu SMB dla udziałów plików platformy Azure:

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania używany do weryfikacji tożsamości użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz temat [uwierzytelnianie Kerberos — Omówienie](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół bloku komunikatów serwera (SMB)**

    SMB jest standardowym protokołem udostępniania plików w branży. Protokół SMB jest również znany jako wspólny internetowy system plików lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [Omówienie protokołów SMB i CIFS firmy Microsoft](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu. Przyłączone do usługi Azure AD maszyny wirtualne z systemem Windows nie mogą uzyskać dostępu do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (AD DS platformy Azure)**

    Usługa Azure AD DS udostępnia zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM. Te usługi są w pełni zgodne z Active Directory Domain Services. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services lokalne (AD DS)**

    Integracja Active Directory Domain Services lokalnych (AD DS) z programem Azure Files udostępnia metody przechowywania danych katalogowych podczas udostępniania ich użytkownikom i administratorom sieci. Zabezpieczenia są zintegrowane z AD DS za pomocą uwierzytelniania logowania i kontroli dostępu do obiektów w katalogu. Za pomocą pojedynczego logowania do sieci administratorzy mogą zarządzać danymi katalogowymi i organizacją w całej sieci, a autoryzowani użytkownicy sieci mogą uzyskiwać dostęp do zasobów w dowolnym miejscu w sieci. AD DS jest zazwyczaj akceptowana przez przedsiębiorstwa w środowiskach lokalnych, a poświadczenia AD DS są używane jako tożsamość kontroli dostępu. Aby uzyskać więcej informacji, zobacz [Active Directory Domain Services przegląd](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)**

    Kontrola dostępu oparta na rolach (Azure RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Korzystając z funkcji RBAC platformy Azure, możesz zarządzać dostępem do zasobów, przydzielając użytkownikom najmniejszą liczbę wymaganych uprawnień do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Typowe przypadki użycia

Uwierzytelnianie oparte na tożsamości i pomoc techniczna dla list ACL systemu Windows w Azure Files jest najlepiej wykorzystane w następujących przypadkach użycia:

### <a name="replace-on-premises-file-servers"></a>Zastępowanie lokalnych serwerów plików

Przestarzałe i zastępowane lokalne serwery plików są typowym problemem, który każde przedsiębiorstwo napotyka w trakcie jego modernizacji. Udziały plików platformy Azure z lokalnym uwierzytelnianiem AD DS są najlepszym rozwiązaniem w tym miejscu, gdy można migrować dane do Azure Files. Pełna migracja umożliwi skorzystanie z zalet wysokiej dostępności i skalowalności, jednocześnie minimalizując zmiany po stronie klienta. Zapewnia bezproblemowe środowisko migracji użytkownikom końcowym, dzięki czemu może nadal uzyskiwać dostęp do danych za pomocą tych samych poświadczeń przy użyciu istniejących maszyn przyłączonych do domeny.

### <a name="lift-and-shift-applications-to-azure"></a>Podnieś i Przenieś aplikacje na platformę Azure

Podczas podnoszenia i przesunięcia aplikacji do chmury chcesz zachować ten sam model uwierzytelniania dla danych. W miarę jak rozszerzono środowisko kontroli dostępu oparte na tożsamościach do udziałów plików platformy Azure, eliminuje konieczność zmiany aplikacji na nowoczesne metody uwierzytelniania i przyspieszanie wdrażania chmury. Udziały plików platformy Azure udostępniają opcję integracji z usługą Azure AD DS lub AD DS lokalnym na potrzeby uwierzytelniania. Jeśli plan ma być 100% w chmurze i minimalizuje wysiłki związane z zarządzaniem infrastrukturami w chmurze, platforma Azure AD DS będzie lepiej dopasowana jako w pełni zarządzana usługa domeny. Jeśli wymagana jest pełna zgodność z możliwościami AD DS, warto rozważyć rozszerzenie środowiska AD DS na chmurę przez samoobsługowe kontrolery domeny na maszynach wirtualnych. W obu przypadkach zapewniamy elastyczność wybierania usług domenowych, które odpowiadają potrzebom biznesowym.

### <a name="backup-and-disaster-recovery-dr"></a>Kopia zapasowa i odzyskiwanie po awarii (DR)

Jeśli przechowujesz podstawowy magazyn plików lokalnie, udziały plików platformy Azure mogą działać jako idealny magazyn do tworzenia kopii zapasowych i odzyskiwania po awarii, aby zwiększyć ciągłość działania firmy. Udziałów plików platformy Azure można używać do tworzenia kopii zapasowych danych z istniejących serwerów plików, zachowując jednocześnie listy DACL systemu Windows. W przypadku scenariuszy odzyskiwania po awarii można skonfigurować opcję uwierzytelniania, która będzie obsługiwać odpowiednie wymuszanie kontroli dostępu w trybie failover.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Poniższa tabela zawiera podsumowanie obsługiwanych scenariuszy uwierzytelniania udziałów plików platformy Azure dla usługi Azure AD DS i AD DS lokalnych. Zalecamy wybranie usługi domeny, która została przyjęta dla środowiska klienta na potrzeby integracji z Azure Files. Jeśli AD DS już skonfigurowano lokalnie lub na platformie Azure, w której urządzenia są przyłączone do usługi AD, należy skorzystać z AD DS uwierzytelniania udziałów plików platformy Azure. Podobnie, jeśli usługa Azure AD DS została już przyjęta, należy ją użyć do uwierzytelniania w udziałach plików platformy Azure.


|Uwierzytelnianie za pomocą usługi Azure AD DS  | Lokalne uwierzytelnianie AD DS  |
|---------|---------|
|Przyłączone do AD DS maszyny z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD za pośrednictwem protokołu SMB.     |Lokalnie przyłączone do AD DS lub maszyny z systemem Windows przyłączone do platformy AD DS Azure mogą uzyskiwać dostęp do udziałów plików platformy Azure z lokalnymi poświadczeniami Active Directory, które są synchronizowane z usługą Azure AD za pośrednictwem protokołu SMB. Twój klient musi mieć linię wglądu w AD DS.        |

### <a name="restrictions"></a>Ograniczenia

- Usługa Azure AD DS i lokalne uwierzytelnianie AD DS nie obsługują uwierzytelniania dla kont komputerów. Zamiast tego możesz rozważyć użycie konta logowania do usługi.
- W przypadku urządzeń przyłączonych do usługi Azure AD lub urządzeń zarejestrowanych w usłudze Azure AD nie jest obsługiwane uwierzytelnianie AD DS platformy Azure ani uwierzytelnianie lokalne AD DS.
- Udziały plików platformy Azure obsługują tylko uwierzytelnianie oparte na tożsamościach dla jednej z następujących usług domeny: [Azure Active Directory Domain Services (AD DS platformy Azure)](#azure-ad-ds) lub [Active Directory Domain Services lokalnych (AD DS)](#ad-ds).
- Żadna metoda uwierzytelniania oparta na tożsamości nie jest obsługiwana w systemie plików NFS, który jest w wersji zapoznawczej.

## <a name="advantages-of-identity-based-authentication"></a>Zalety uwierzytelniania opartego na tożsamościach
Uwierzytelnianie oparte na tożsamościach dla Azure Files oferuje kilka korzyści z używania uwierzytelniania klucza współużytkowanego:

-   **Rozpoznaj tradycyjne środowisko dostępu do udziału plików oparte na tożsamościach w chmurze dzięki lokalnym AD DS i platformie Azure AD DS**  
    Jeśli planujesz podnieśność i przeniesiesz aplikację do chmury, Zastąp tradycyjne serwery plików z udziałami plików platformy Azure, a następnie możesz zalogować się do aplikacji przy użyciu lokalnych AD DS lub poświadczeń AD DS platformy Azure, aby uzyskać dostęp do danych plików. Azure Files obsługuje używanie lokalnych AD DS lub poświadczeń AD DS platformy Azure do uzyskiwania dostępu do udziałów plików platformy Azure za pośrednictwem protokołu SMB z lokalnych AD DS lub platformy Azure AD DS maszyn wirtualnych przyłączonych do domeny.

-   **Wymuś szczegółową kontrolę dostępu w udziałach plików platformy Azure**  
    Można przyznać uprawnienia do określonej tożsamości na poziomie udziału, katalogu lub pliku. Załóżmy na przykład, że masz kilka zespołów korzystających z jednego udziału plików platformy Azure do współpracy z projektem. Można udzielić wszystkim zespołom dostępu do niewrażliwych katalogów, ograniczając dostęp do katalogów zawierających poufne dane finansowe tylko do zespołu finansowego. 

-   **Tworzenie kopii zapasowych list ACL systemu Windows (znanych również jako NTFS) wraz z danymi**  
    Za pomocą udziałów plików platformy Azure można tworzyć kopie zapasowe istniejących lokalnych udziałów plików. Azure Files zachowuje listy kontroli dostępu wraz z danymi podczas tworzenia kopii zapasowej udziału plików w udziałach plików platformy Azure za pośrednictwem protokołu SMB.

## <a name="how-it-works"></a>Jak to działa

Udziały plików platformy Azure wykorzystują protokół Kerberos do uwierzytelniania za pomocą lokalnych AD DS lub AD DS platformy Azure. Gdy tożsamość skojarzona z użytkownikiem lub aplikacją działającą na kliencie próbuje uzyskać dostęp do danych w udziałach plików platformy Azure, żądanie jest wysyłane do usługi domeny, AD DS lub Azure AD DS w celu uwierzytelnienia tożsamości. W przypadku pomyślnego uwierzytelnienia zwraca token Kerberos. Klient wysyła żądanie zawierające token Kerberos i udziały plików platformy Azure używają tego tokenu do autoryzowania żądania. Udziały plików platformy Azure odbierają tylko tokeny Kerberos, a nie dostęp do poświadczeń.

Aby można było włączyć uwierzytelnianie oparte na tożsamościach w udziałach plików platformy Azure, należy najpierw skonfigurować środowisko domeny.

### <a name="ad-ds"></a>AD DS

W przypadku lokalnego uwierzytelniania AD DS należy skonfigurować kontrolery domeny usługi AD i domenę Przyłącz się do maszyn lub maszyn wirtualnych. Kontrolery domeny można hostować na maszynach wirtualnych platformy Azure lub lokalnie. W obu przypadkach klienci przyłączeni do domeny muszą mieć linię wglądu w usługę domenową, dlatego muszą znajdować się w sieci firmowej lub w sieci wirtualnej (VNET) usługi domeny.

Na poniższym diagramie przedstawiono lokalne uwierzytelnianie AD DS do udziałów plików platformy Azure za pośrednictwem protokołu SMB. AD DS Premium musi być zsynchronizowany z usługą Azure AD przy użyciu funkcji Azure AD Connect Sync. Tylko użytkownicy hybrydowi, którzy istnieją zarówno w lokalnych AD DS, jak i w usłudze Azure AD, mogą być uwierzytelniani i autoryzowani dla dostępu do udziału plików platformy Azure. Wynika to z faktu, że uprawnienie na poziomie udziału jest skonfigurowane pod kątem tożsamości reprezentowanej w usłudze Azure AD, w której jest wymuszane uprawnienie poziomu katalogu/pliku przy użyciu tego w AD DS. Upewnij się, że uprawnienia są prawidłowo skonfigurowane dla tego samego hybrydowego użytkownika.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagram przedstawiający lokalne uwierzytelnianie AD DS do udziałów plików platformy Azure za pośrednictwem protokołu SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

W przypadku uwierzytelniania za pomocą usługi Azure AD DS należy włączyć Azure AD Domain Services i dołączanie domeny do maszyn wirtualnych, z których planujesz uzyskać dostęp do danych plików. Przyłączona do domeny maszyna wirtualna musi znajdować się w tej samej sieci wirtualnej co AD DS platformy Azure. 

Poniższy diagram przedstawia przepływ pracy na potrzeby uwierzytelniania usługi Azure AD DS w udziałach plików platformy Azure za pośrednictwem protokołu SMB. Jest to podobny wzorzec do Premium AD DS uwierzytelniania w udziałach plików platformy Azure. Istnieją dwie istotne różnice:

- Najpierw nie trzeba tworzyć tożsamości na platformie Azure AD DS, aby reprezentować konto magazynu. Jest to wykonywane przez proces włączania w tle.

- Po drugie, wszyscy użytkownicy, którzy istnieją w usłudze Azure AD, mogą być uwierzytelniani i autoryzowani. Użytkownik może być tylko w chmurze lub hybrydowym. Synchronizacja z usługi Azure AD do platformy Azure AD DS jest zarządzana przez platformę bez konieczności konfigurowania użytkownika. Jednak klient musi być przyłączony do domeny w usłudze Azure AD DS, nie może być przyłączony lub zarejestrowany w usłudze Azure AD. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="4b":::

### <a name="enable-identity-based-authentication"></a>Włączanie uwierzytelniania opartego na tożsamościach

Uwierzytelnianie oparte na tożsamościach można włączyć za pomocą usługi Azure AD DS lub AD DS lokalnych dla udziałów plików platformy Azure na nowych i istniejących kontach magazynu. Tylko jedna usługa domeny może być używana do uwierzytelniania dostępu do plików na koncie magazynu, które ma zastosowanie do wszystkich udziałów plików na koncie. Szczegółowe wskazówki dotyczące konfigurowania udziałów plików na potrzeby uwierzytelniania przy użyciu usługi Azure AD DS w naszym artykule [umożliwiają Azure Active Directory Domain Services uwierzytelnianie w Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) i wskazówki dotyczące AD DS lokalnych w naszym innym artykule. [włącz lokalne uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Skonfiguruj uprawnienia na poziomie udziału dla Azure Files

Po włączeniu uwierzytelniania AD DS platformy Azure AD DS lub lokalnego można użyć wbudowanych ról platformy Azure lub skonfigurować role niestandardowe dla tożsamości usługi Azure AD i przypisać prawa dostępu do wszystkich udziałów plików na kontach magazynu. Przypisane uprawnienie umożliwia uzyskanie dostępu tylko do udziału, a nie w katalogu głównym. Nadal trzeba oddzielnie skonfigurować uprawnienia na poziomie katalogu lub pliku dla udziałów plików platformy Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie katalogu lub pliku dla Azure Files

Udziały plików platformy Azure wymuszają standardowe uprawnienia do plików systemu Windows zarówno na poziomie katalogu, jak i pliku, łącznie z katalogiem głównym. Konfiguracja uprawnień na poziomie katalogu lub pliku jest obsługiwana w przypadku protokołu SMB i REST. Zainstaluj docelowy udział plików z maszyny wirtualnej i Skonfiguruj uprawnienia za pomocą Eksploratora plików systemu Windows, [icacls](/windows-server/administration/windows-commands/icacls)systemu Windows lub polecenia [Set-ACL](/powershell/module/microsoft.powershell.security/get-acl) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Użyj klucza konta magazynu dla uprawnień administratora

Użytkownik z kluczem konta magazynu może uzyskać dostęp do udziałów plików platformy Azure z uprawnieniami administratora. Uprawnienia administratora omijają wszystkie ograniczenia kontroli dostępu.

> [!IMPORTANT]
> Zalecanym najlepszym rozwiązaniem w zakresie zabezpieczeń jest uniknięcie udostępniania kluczy konta magazynu i użycia uwierzytelniania opartego na tożsamościach, jeśli jest to możliwe.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachowaj listy ACL katalogów i plików podczas importowania danych do udziałów plików platformy Azure

Azure Files obsługuje zachowanie list ACL na poziomie katalogu lub pliku podczas kopiowania danych do udziałów plików platformy Azure. Listy ACL z katalogu lub pliku można kopiować do udziałów plików platformy Azure przy użyciu Azure File Sync lub wspólnych zestawów narzędzi przenoszenia plików. Na przykład można użyć [Robocopy](/windows-server/administration/windows-commands/robocopy) z `/copy:s` flagą, aby skopiować dane, a także listy ACL do udziału plików platformy Azure. Listy ACL są domyślnie zachowywane, nie jest wymagane włączenie uwierzytelniania opartego na tożsamościach na koncie magazynu w celu zachowania list ACL.

## <a name="pricing"></a>Ceny
Nie ma dodatkowej opłaty za usługę w celu włączenia uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB na koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [Azure Files ceny](https://azure.microsoft.com/pricing/details/storage/files/) i [Azure AD Domain Services cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Azure Files i uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Włącz lokalne uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md)
- [Włącz uwierzytelnianie Azure Active Directory Domain Services na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Często zadawane pytania](storage-files-faq.md)