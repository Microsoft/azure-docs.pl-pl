---
title: Co to jest Windows Virtual Desktop? — Azure
description: Omówienie Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a6d98f827c39f973714441e308dcc4f2bd061c9b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835582"
---
# <a name="what-is-windows-virtual-desktop"></a>Co to jest Windows Virtual Desktop?

Windows Virtual Desktop to usługa wirtualizacji pulpitu i aplikacji, która działa w chmurze.

Oto, co można zrobić po uruchomieniu aplikacji Windows Virtual Desktop na platformie Azure:

* Konfigurowanie wdrożenia wielosesyjnego Windows 10, które zapewnia pełną Windows 10 skalowalność
* Wirtualizacja Microsoft 365 Apps dla przedsiębiorstw i optymalizowanie jej pod kątem uruchamiania w scenariuszach wirtualnych z wieloma użytkownikami
* Udostępnij pulpity wirtualne systemu Windows 7 z bezpłatnymi rozszerzonymi aktualizacjami zabezpieczeń
* Bring your existing Usługi pulpitu zdalnego (RDS) and Windows Server desktops and apps to any computer
* Wirtualizacja komputerów stacjonarnych i aplikacji
* Zarządzanie Windows 10 komputerami stacjonarnymi i aplikacjami z systemami Windows Server i Windows 7 za pomocą ujednoliconego zarządzania

## <a name="introductory-video"></a>Klip wideo wprowadzający

Dowiedz się Windows Virtual Desktop, dlaczego są unikatowe i co nowego w tym filmie wideo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Aby uzyskać więcej filmów wideo dotyczących Windows Virtual Desktop, zobacz naszą [listę odtwarzania](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Najważniejsze możliwości

Za Windows Virtual Desktop można skonfigurować skalowalne i elastyczne środowisko:

* Utwórz pełne środowisko wirtualizacji pulpitu w ramach subskrypcji platformy Azure bez konieczności uruchamiania dodatkowych serwerów bramy.
* Opublikuj tyle pul hostów, ile potrzebujesz, aby obsłużyć różne obciążenia.
* Korzystanie z własnego obrazu na potrzeby obciążeń produkcyjnych lub testowanie z galerii platformy Azure.
* Zmniejsz koszty za pomocą zasobów w puli, z wieloma sesjami. Dzięki nowej funkcji wielu sesji usługi Windows 10 Enterprise dostępnej wyłącznie dla roli hosta sesji usług Windows Virtual Desktop i Pulpit zdalny (RDSH) w systemie Windows Server można znacznie zmniejszyć liczbę maszyn wirtualnych i narzutów systemu operacyjnego przy jednoczesnym zapewnieniu użytkownikom tych samych zasobów.
* Zapewnianie indywidualnej własności za pośrednictwem osobistych (trwałych) komputerów stacjonarnych.

Pulpity wirtualne można wdrażać i zarządzać nimi:

* Użyj interfejsów Azure Portal, Windows Virtual Desktop PowerShell i REST do konfigurowania pul hostów, tworzenia grup aplikacji, przypisywania użytkowników i publikowania zasobów.
* Publikowanie pełnych pulpitów lub pojedynczych aplikacji zdalnych z pojedynczej puli hostów, tworzenie poszczególnych grup aplikacji dla różnych zestawów użytkowników, a nawet przypisywanie użytkowników do wielu grup aplikacji w celu zmniejszenia liczby obrazów.
* Podczas zarządzania środowiskiem użyj wbudowanego delegowanego dostępu, aby przypisywać role i zbierać dane diagnostyczne w celu zrozumienia różnych błędów konfiguracji lub użytkowników.
* Użyj nowej usługi diagnostyki do rozwiązywania problemów z błędami.
* Zarządzanie tylko obrazem i maszynami wirtualnymi, a nie infrastrukturą. Nie musisz zarządzać rolami zarządzania Pulpit zdalny, tak jak w przypadku Usługi pulpitu zdalnego, tylko maszynami wirtualnymi w subskrypcji platformy Azure.

Możesz również przypisywać użytkowników i łączyć się z pulpitami wirtualnymi:

* Po przypisaniu użytkownicy mogą uruchamiać dowolny Windows Virtual Desktop, aby połączyć użytkowników z opublikowanymi pulpitami i aplikacjami systemu Windows. Nawiązywanie połączeń z dowolnego urządzenia za pośrednictwem aplikacji natywnej na urządzeniu lub Windows Virtual Desktop klienta internetowego HTML5.
* Bezpiecznie ustanawiaj użytkowników za pośrednictwem połączeń zwrotnych z usługą, dzięki czemu nigdy nie musisz pozostawiać otwartych portów przychodzących.

## <a name="requirements"></a>Wymagania

Istnieje kilka rzeczy, które należy skonfigurować, aby Windows Virtual Desktop pomyślnie połączyć użytkowników z ich komputerami stacjonarnymi i aplikacjami z systemem Windows.

Obsługujemy następujące systemy operacyjne, dlatego upewnij [](https://azure.microsoft.com/pricing/details/virtual-desktop/) się, że masz odpowiednie licencje dla użytkowników oparte na komputerach i aplikacjach, które planujesz wdrożyć:

|System operacyjny|Wymagana licencja|
|---|---|
|Windows 10 Enterprise wielu sesji lub Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licencja dostępu klienta usług pulpitu zdalnego (CAL) z pakiet Software Assurance|

Twoja infrastruktura wymaga następujących czynności do obsługi Windows Virtual Desktop:

* Element [Azure Active Directory](../active-directory/index.yml).
* Synchronizacja Windows Server Active Directory z Azure Active Directory. Można to skonfigurować przy użyciu Azure AD Connect (dla organizacji hybrydowych) lub Azure AD Domain Services (dla organizacji hybrydowych lub w chmurze).
  * Synchronizacja Windows Server AD z Azure Active Directory. Użytkownik jest pozyowany z Windows Server AD, a maszyna wirtualna Windows Virtual Desktop do Windows Server AD domeny.
  * Synchronizacja Windows Server AD z Azure Active Directory. Użytkownik jest pozyowany z Windows Server AD, a maszyna wirtualna Windows Virtual Desktop jest przysłana do Azure AD Domain Services domeny.
  * Domena Azure AD Domain Services domeny. Użytkownik pochodzi z Azure Active Directory, a maszyna wirtualna Windows Virtual Desktop jest przysłana do Azure AD Domain Services domeny.
* Subskrypcja platformy Azure, nadrzędna dla tej samej dzierżawy usługi Azure AD, która zawiera sieć wirtualną zawierającą lub połączeniową z Windows Server Active Directory lub Azure AD DS wystąpieniem.

Wymagania użytkownika dotyczące nawiązywania połączenia z Windows Virtual Desktop:

* Użytkownik musi pochodzić z tej samej usługi Active Directory, która jest połączona z usługą Azure AD. Windows Virtual Desktop nie obsługuje kont B2B ani MSA.
* Nazwa UPN, która jest używana do subskrybowania Windows Virtual Desktop musi istnieć w domenie usługi Active Directory, do których jest do niej przyłączona maszyna wirtualna.

Maszyny wirtualne platformy Azure, które tworzysz dla Windows Virtual Desktop muszą być:

* [Przyłączony do domeny standardowej](../active-directory-domain-services/compare-identity-solutions.md) [lub przyłączony hybrydowo do usługi AD.](../active-directory/devices/hybrid-azuread-join-plan.md) Maszyny wirtualne nie mogą być przyłączone do usługi Azure AD.
* Uruchomienie jednego z następujących [obsługiwanych obrazów systemu operacyjnego.](#supported-virtual-machine-os-images)

>[!NOTE]
>Jeśli potrzebujesz subskrypcji platformy Azure, możesz utworzyć konto bezpłatnej wersji [próbnej na miesiąc.](https://azure.microsoft.com/free/) Jeśli używasz bezpłatnej wersji próbnej platformy Azure, użyj usługi Azure AD Domain Services, aby zachować synchronizację Windows Server Active Directory z Azure Active Directory.

Aby uzyskać listę adresów URL, które należy odblokować, aby wdrożenie Windows Virtual Desktop działało zgodnie z zamierzeniami, zobacz naszą listę [wymaganych adresów URL](safe-url-list.md).

Windows Virtual Desktop obejmuje komputery stacjonarne i aplikacje z systemem Windows, które dostarczasz użytkownikom, oraz rozwiązanie do zarządzania, które jest hostowane jako usługa na platformie Azure przez firmę Microsoft. Komputery stacjonarne i aplikacje można wdrażać na maszynach wirtualnych w dowolnym regionie świadczenia usługi Azure, a rozwiązanie do zarządzania i dane dla tych maszyn wirtualnych będą znajdować się w Stany Zjednoczone. Może to spowodować transfer danych do Stany Zjednoczone.

Aby uzyskać optymalną wydajność, upewnij się, że sieć spełnia następujące wymagania:

* Opóźnienie rundy (RTT) z sieci klienta do regionu świadczenia usługi Azure, w którym wdrożono pule hostów, powinno być mniejsze niż 150 ms. Użyj narzędzia [do szacowania doświadczenia,](https://azure.microsoft.com/services/virtual-desktop/assessment) aby wyświetlić kondycję połączenia i zalecany region świadczenia usługi Azure.
* Ruch sieciowy może przepływać poza granice kraju/regionu, gdy maszyny wirtualne hostów komputerów stacjonarnych i aplikacji łączą się z usługą zarządzania.
* Aby zoptymalizować pod kątem wydajności sieci, zalecamy, aby maszyny wirtualne hosta sesji zostały kolokowane w tym samym regionie świadczenia usługi Azure co usługa zarządzania.

Typową konfigurację architektury architektury dla Windows Virtual Desktop można zobaczyć [](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)w naszej architekturze.

## <a name="supported-remote-desktop-clients"></a>Obsługiwani Pulpit zdalny klientów

Następujące klienty Pulpit zdalny obsługują Windows Virtual Desktop:

* [Windows Desktop](connect-windows-7-10.md)
* [Sieć Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store klienta

> [!IMPORTANT]
> Windows Virtual Desktop nie obsługuje klienta połączeń usług RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

Aby dowiedzieć się więcej na temat adresów URL, które należy odblokować, aby korzystać z klientów, zobacz [listę Bezpieczny adres URL](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Obsługiwane obrazy systemu operacyjnego maszyny wirtualnej

Windows Virtual Desktop obsługuje następujące obrazy systemu operacyjnego x64:

* Windows 10 Enterprise wielu sesji, wersja 1809 lub nowsza
* Windows 10 Enterprise, wersja 1809 lub nowsza (tylko Półroczny kanał)
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 z dodatkiem R2

Windows Virtual Desktop nie obsługuje obrazów systemu operacyjnego x86 (32-bitowego), Windows 10 Enterprise N, Windows 10 Pro ani Windows 10 Enterprise systemu operacyjnego KN. System Windows 7 nie obsługuje również żadnych rozwiązań profilów opartych na dyskach VHD lub VHDX hostowanych w zarządzanej usłudze Azure Storage z powodu ograniczenia rozmiaru sektora.

Dostępne opcje automatyzacji i wdrażania zależą od tego, który system operacyjny i wersję wybierzesz, jak pokazano w poniższej tabeli:

|System operacyjny|Galeria obrazów platformy Azure|Ręczne wdrażanie maszyny wirtualnej|Azure Resource Manager szablonu|Aprowizuj pule hostów na Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (wiele sesji), wersja 2004|Tak|Tak|Tak|Tak|
|Windows 10 Enterprise (wiele sesji), wersja 1909|Tak|Tak|Tak|Tak|
|Windows 10 Enterprise (wiele sesji), wersja 1903|Tak|Tak|Nie|Nie|
|Windows 10 Enterprise (wiele sesji), wersja 1809|Tak|Tak|Nie|Nie|
|Windows 7 Enterprise|Tak|Tak|Nie|Nie|
|Windows Server 2019|Tak|Tak|Nie|Nie|
|Windows Server 2016|Tak|Tak|Tak|Tak|
|Windows Server 2012 z dodatkiem R2|Tak|Tak|Nie|Nie|

## <a name="next-steps"></a>Następne kroki

Jeśli używasz usługi Windows Virtual Desktop (klasycznej), możesz rozpocząć pracę z naszym samouczkiem na stronie Tworzenie dzierżawy [w](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)Windows Virtual Desktop .

Jeśli używasz puli hostów Windows Virtual Desktop integracji Azure Resource Manager, musisz zamiast tego utworzyć pulę hostów. Przejdź do następującego samouczka, aby rozpocząć pracę.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów za pomocą witryny Azure Portal](create-host-pools-azure-marketplace.md)
