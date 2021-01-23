---
title: Co to jest Windows Virtual Desktop? — Azure
description: Omówienie pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36a15560b88c823ff2ae41f160839796bf21e4f8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730789"
---
# <a name="what-is-windows-virtual-desktop"></a>Co to jest Windows Virtual Desktop?

Windows Virtual Desktop to usługa wirtualizacji pulpitu i aplikacji działająca w chmurze.

Oto co można zrobić w przypadku uruchamiania pulpitu wirtualnego systemu Windows na platformie Azure:

* Konfigurowanie wdrożenia obejmującego wiele sesji systemu Windows 10, które zapewnia pełną skalowalność systemu Windows 10
* Wirtualizacja Microsoft 365 aplikacje dla przedsiębiorstw i optymalizacja do działania w scenariuszach wirtualnych obejmujących wiele użytkowników
* Udostępnianie pulpitów wirtualnych systemu Windows 7 z bezpłatnymi rozszerzonymi aktualizacjami zabezpieczeń
* Przenoszenie istniejących Usługi pulpitu zdalnego (RDS) i komputerów stacjonarnych i aplikacji z systemem Windows Server do dowolnego komputera
* Wirtualizacja zarówno komputerów stacjonarnych, jak i aplikacji
* Zarządzanie komputerami stacjonarnymi i aplikacjami z systemem Windows 10, Windows Server i Windows 7 za pomocą ujednoliconego środowiska zarządzania

## <a name="introductory-video"></a>Wstępne wideo

Dowiedz się więcej o usłudze Windows Virtual Desktop, dlaczego jest ona unikatowa i co nowego w tym filmie wideo:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Aby uzyskać więcej filmów wideo dotyczących pulpitu wirtualnego systemu Windows, zobacz [nasze listy odtwarzania](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Najważniejsze możliwości

Za pomocą pulpitu wirtualnego systemu Windows można skonfigurować skalowalne i elastyczne środowisko:

* Utwórz pełne środowisko wirtualizacji pulpitu w ramach subskrypcji platformy Azure bez konieczności uruchamiania dodatkowych serwerów bramy.
* Publikuj tyle pul hostów, ile potrzebujesz do obsługi różnych obciążeń.
* Korzystaj z własnego obrazu dla obciążeń produkcyjnych lub testów z galerii platformy Azure.
* Obniż koszty dzięki zasobom w puli, wielu sesjach. Dzięki nowej funkcji wielosesyjnej systemu Windows 10 Enterprise na wyłączność dla pulpitu wirtualnego systemu Windows i roli hosta sesji Pulpit zdalny w systemie Windows Server można znacznie zmniejszyć liczbę maszyn wirtualnych i nakładów pracy systemu operacyjnego, jednocześnie dostarczając te same zasoby użytkownikom.
* Udostępnianie poszczególnych własności za poorednictwem osobistych (trwałych) komputerów stacjonarnych.

Pulpity wirtualne można wdrażać i zarządzać nimi:

* Użyj Azure Portal, programu PowerShell dla pulpitu wirtualnego systemu Windows i interfejsów REST, aby skonfigurować pule hostów, tworzyć grupy aplikacji, przypisywać użytkowników i publikować zasoby.
* Publikowanie pełnych pulpitów lub pojedynczych aplikacji zdalnych z jednej puli hostów, tworzenie pojedynczych grup aplikacji dla różnych zestawów użytkowników, a nawet przypisywanie użytkowników do wielu grup aplikacji w celu zmniejszenia liczby obrazów.
* Podczas zarządzania środowiskiem użyj wbudowanego delegowanego dostępu, aby przypisać role i zbierać diagnostykę, aby poznać różne konfiguracje lub błędy użytkowników.
* Użyj nowej usługi diagnostycznej do rozwiązywania problemów z błędami.
* Zarządzaj obrazami i maszynami wirtualnymi, a nie z infrastrukturą. Nie musisz zarządzać rolami Pulpit zdalny, takimi jak w przypadku Usługi pulpitu zdalnego, tylko maszyn wirtualnych w ramach subskrypcji platformy Azure.

Możesz również przypisywać użytkowników i łączyć ich z pulpitami wirtualnymi:

* Po przypisaniu użytkownicy mogą uruchomić dowolnego klienta pulpitu wirtualnego systemu Windows, aby połączyć użytkowników z opublikowanymi pulpitami i aplikacjami systemu Windows. Połącz się z dowolnego urządzenia za pośrednictwem aplikacji natywnej na urządzeniu lub klienta sieci Web HTML5 programu Windows Virtual Desktop.
* Bezpiecznie ustanawiaj użytkowników przez połączenia odwrotne do usługi, dzięki czemu nie musisz opuszczać żadnych otwartych portów przychodzących.

## <a name="requirements"></a>Wymagania

Istnieje kilka rzeczy potrzebnych do skonfigurowania pulpitu wirtualnego systemu Windows i pomyślnego połączenia użytkowników z komputerami stacjonarnymi i aplikacjami z systemem Windows.

Obsługiwane są następujące systemy operacyjne, dlatego należy upewnić się, że masz [odpowiednie licencje](https://azure.microsoft.com/pricing/details/virtual-desktop/) dla użytkowników na podstawie pulpitu i aplikacji, które planujesz wdrożyć:

|System operacyjny|Wymagana licencja|
|---|---|
|Wiele sesji systemu Windows 10 Enterprise lub Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licencja dostępu klienta usług pulpitu zdalnego z programem Software Assurance|

Aby zapewnić obsługę pulpitu wirtualnego systemu Windows, Twoja infrastruktura wymaga następujących czynności:

* [Azure Active Directory](../active-directory/index.yml).
* Active Directory systemu Windows Server synchronizacja z Azure Active Directory. Tę konfigurację można skonfigurować przy użyciu Azure AD Connect (dla organizacji hybrydowych) lub Azure AD Domain Services (dla organizacji hybrydowej lub w chmurze).
  * Windows Server AD w synchronizacji z Azure Active Directory. Użytkownik pochodzi z systemu Windows Server AD, a maszyna wirtualna z systemem Windows jest dołączona do domeny usługi AD systemu Windows Server.
  * Windows Server AD w synchronizacji z Azure Active Directory. Użytkownik pochodzi z systemu Windows Server AD, a maszyna wirtualna z systemem Windows jest przyłączona do domeny Azure AD Domain Services.
  * Domena Azure AD Domain Services. Użytkownik pochodzi z Azure Active Directory, a maszyna wirtualna pulpitu wirtualnego systemu Windows jest przyłączona do Azure AD Domain Services domeny.
* Subskrypcja platformy Azure, która jest nadrzędna w tej samej dzierżawie usługi Azure AD, która zawiera sieć wirtualną, która zawiera lub jest połączona z wystąpieniem programu Windows Server Active Directory lub AD DS platformy Azure.

Wymagania użytkownika dotyczące nawiązywania połączenia z pulpitem wirtualnym systemu Windows:

* Użytkownik musi być źródłem tego samego Active Directory, które jest połączone z usługą Azure AD. Pulpit wirtualny systemu Windows nie obsługuje kont B2B ani MSA.
* Nazwa UPN używana do subskrybowania pulpitu wirtualnego systemu Windows musi znajdować się w domenie Active Directory, do której jest dołączona maszyna wirtualna.

Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą być:

* [Standardowe przyłączone do domeny](../active-directory-domain-services/compare-identity-solutions.md) lub [hybrydowe usługi AD](../active-directory/devices/hybrid-azuread-join-plan.md). Maszyny wirtualne nie mogą być przyłączone do usługi Azure AD.
* Uruchamianie jednego z następujących [obsługiwanych obrazów systemu operacyjnego](#supported-virtual-machine-os-images).

>[!NOTE]
>Jeśli potrzebujesz subskrypcji platformy Azure, możesz [zarejestrować się w celu korzystania z miesięcznej bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). W przypadku korzystania z bezpłatnej wersji próbnej platformy Azure należy używać Azure AD Domain Services, aby zapewnić synchronizację Active Directory systemu Windows Server z Azure Active Directory.

Aby uzyskać listę adresów URL, które należy odblokować dla wdrożenia pulpitu wirtualnego systemu Windows, aby działały zgodnie z oczekiwaniami, zobacz naszą [bezpieczną listę adresów URL](safe-url-list.md).

Pulpit wirtualny systemu Windows składa się z komputerów stacjonarnych i aplikacji z systemem Windows dostarczanych użytkownikom i rozwiązanie do zarządzania, które jest hostowane jako usługa na platformie Azure przez firmę Microsoft. Komputery stacjonarne i aplikacje można wdrażać na maszynach wirtualnych w dowolnym regionie świadczenia usługi Azure, a rozwiązanie do zarządzania i dane dla tych maszyn wirtualnych będą znajdować się w Stany Zjednoczone. Może to spowodować transfer danych do Stany Zjednoczone.

W celu uzyskania optymalnej wydajności upewnij się, że sieć spełnia następujące wymagania:

* Opóźnienie rundy (RTT) od sieci klienta do regionu platformy Azure, w którym wdrożono pule hostów, powinno być mniejsze niż 150 MS. Użyj [szacowania doświadczenia](https://azure.microsoft.com/services/virtual-desktop/assessment) , aby wyświetlić kondycję połączenia i zalecany region platformy Azure.
* Ruch sieciowy może przepływać poza granicami kraju/regionu, gdy maszyny wirtualne, które obsługują komputery stacjonarne i aplikacje, łączą się z usługą zarządzania.
* Aby zoptymalizować pod kątem wydajności sieci, zalecamy zapoznanie się z maszynami wirtualnymi hosta sesji w tym samym regionie platformy Azure co usługa zarządzania.

Typową konfigurację architektury pulpitu wirtualnego systemu Windows dla przedsiębiorstwa można zobaczyć w naszej [architekturze zawiera](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Obsługiwani klienci Pulpit zdalny

Następujący klienci Pulpit zdalny obsługują pulpit wirtualny systemu Windows:

* [Pulpit systemu Windows](connect-windows-7-10.md)
* [Sieć Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Klient Microsoft Store

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

Aby dowiedzieć się więcej na temat adresów URL, które należy odblokować, aby korzystać z klientów, zobacz [listę bezpiecznych adresów URL](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Obsługiwane obrazy systemu operacyjnego maszyny wirtualnej

Pulpit wirtualny systemu Windows obsługuje następujące obrazy systemu operacyjnego x64:

* Wiele sesji systemu Windows 10 Enterprise, wersja 1809 lub nowsza
* Windows 10 Enterprise, wersja 1809 lub nowsza
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 z dodatkiem R2

Pulpit wirtualny systemu Windows nie obsługuje obrazów systemu operacyjnego Windows 10 Enterprise N, Windows 10 Pro lub Windows 10 Enterprise w wersji x86 (32-bitowych). System Windows 7 nie obsługuje również żadnych rozwiązań profilu opartych na dyskach VHD lub VHDX hostowanych w zarządzanym magazynie Azure z powodu ograniczenia rozmiaru sektora.

Dostępne opcje automatyzacji i wdrażania zależą od wybranego systemu operacyjnego i wersji, jak pokazano w poniższej tabeli:

|System operacyjny|Galeria obrazów platformy Azure|Ręczne wdrożenie maszyny wirtualnej|Integracja z szablonem Azure Resource Manager|Inicjowanie obsługi pul hostów w witrynie Azure Marketplace|
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

Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego), możesz rozpocząć pracę z naszym samouczkiem w temacie [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Jeśli używasz pulpitu wirtualnego systemu Windows z integracją Azure Resource Manager, musisz najpierw utworzyć pulę hostów. Przejdź do następującego samouczka, aby rozpocząć pracę.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów za pomocą witryny Azure Portal](create-host-pools-azure-marketplace.md)