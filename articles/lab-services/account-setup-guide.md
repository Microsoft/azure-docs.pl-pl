---
title: Przewodnik konfigurowania konta laboratorium przyspieszonego dla Azure Lab Services
description: Ten przewodnik ułatwia administratorom szybkie skonfigurowanie konta laboratorium do użycia w ramach szkoły.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4d9a64fe23c3e5b74e77e704154f5e74bf2066d9
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490563"
---
# <a name="lab-account-setup-guide"></a>Przewodnik konfigurowania konta laboratorium
Aby skonfigurować środowisko Azure Lab Services, Administratorzy muszą najpierw skonfigurować **konto laboratorium** w ramach Twojej subskrypcji platformy Azure. Konto laboratorium jest kontenerem dla laboratoriów i może potrwać kilka minut.

Ten przewodnik zawiera trzy sekcje:
-  Pierwsza sekcja koncentruje się na wymaganiach wstępnych, które należy wykonać *przed* skonfigurowaniem konta laboratorium.
-  Druga sekcja zawiera wskazówki dotyczące planowania ustawień konta laboratorium.
-  Trzecia sekcja zawiera instrukcje krok po kroku dotyczące konfigurowania konta laboratorium.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Wymagania wstępne dotyczące konfigurowania konta laboratorium
W tej sekcji opisano wymagania wstępne, które należy wykonać, zanim będzie możliwe skonfigurowanie konta laboratorium.

### <a name="obtain-an-azure-subscription"></a>Uzyskaj subskrypcję platformy Azure
Aby utworzyć konto laboratorium, musisz mieć dostęp do subskrypcji platformy Azure, która została skonfigurowana dla Twojej szkoły. Twoja szkoła może mieć co najmniej jedną subskrypcję. Za pomocą subskrypcji można zarządzać rozliczeniami i zabezpieczeniami dla wszystkich zasobów i usług platformy Azure, w tym kont laboratorium.  Subskrypcje platformy Azure są zwykle zarządzane przez dział IT.  Aby uzyskać więcej informacji, przeczytaj następujący temat:
 - [Podręcznik administratora — subskrypcja](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Oszacuj liczbę potrzebnych maszyn wirtualnych i rozmiarów maszyn wirtualnych
Należy oszacować liczbę maszyn wirtualnych i [rozmiary maszyn](https://docs.microsoft.com/azure/lab-services/administrator-guide#vm-sizing) , których potrzebuje Twoja szkoła.  Przeczytaj następujący wpis w blogu, aby uzyskać wskazówki dotyczące struktury labs\images.  Ten wpis w blogu pomoże również określić liczbę maszyn wirtualnych i rozmiary maszyn wirtualnych, które będą potrzebne:
- [Przeniesienie z laboratorium fizycznego do Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Zapoznaj się również z tym artykułem wyjaśniającym dodatkowe wskazówki dotyczące struktury laboratoriów:
- [Przewodnik administratora — laboratorium](https://docs.microsoft.com/azure/lab-services/administrator-guide#classroom-lab)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Informacje o limitach maszyn wirtualnych i pojemności regionalnej maszyny wirtualnej
Gdy masz szacunkową liczbę maszyn wirtualnych i rozmiary maszyn wirtualnych dla laboratoriów, musisz wykonać następujące działania:

- Upewnij się, że limit pojemności subskrypcji platformy Azure umożliwia określenie liczby maszyn wirtualnych i rozmiaru maszyny wirtualnej, który ma być używany w laboratoriach.

- Utwórz konto laboratorium w regionie, w którym dostępna jest wystarczająca pojemność maszyn wirtualnych.

Przeczytaj następujący wpis w blogu, aby dowiedzieć się więcej: [limity subskrypcji maszyn wirtualnych i pojemność regionalna](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Zdecyduj, ile kont laboratorium do utworzenia

Aby szybko rozpocząć pracę, Utwórz pojedyncze konto laboratorium w ramach własnej grupy zasobów.  Później można utworzyć dodatkowe konta laboratorium (i grupy zasobów) zgodnie z wymaganiami. Na przykład może istnieć jedno konto laboratorium i Grupa zasobów dla każdego działu jako sposób na wyraźne oddzielenie kosztów.  Przeczytaj następujące artykuły, aby dowiedzieć się więcej o kontach laboratorium, grupach zasobów i rozdzieleniu kosztów:
- [Podręcznik administratora — Grupa zasobów](https://docs.microsoft.com/azure/lab-services/administrator-guide#resource-group)
- [Przewodnik administratora — konto laboratorium](https://docs.microsoft.com/azure/lab-services/administrator-guide#lab-account) 
- [Zarządzanie kosztami dla Azure Lab Services](https://docs.microsoft.com/azure/lab-services/cost-management-guide)

## <a name="planning-your-lab-accounts-settings"></a>Planowanie ustawień konta laboratorium

Aby zaplanować ustawienia konta laboratorium, należy wziąć pod uwagę poniższe pytania.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kto powinna być właścicielami i współautorami konta laboratorium?

   Administratorzy IT szkoły są zazwyczaj właścicielami i współautorami konta laboratorium. Są one odpowiedzialne za zarządzanie zasadami, które mają zastosowanie do wszystkich laboratoriów zawartych w ramach konta laboratorium. Osoba, która tworzy konto laboratorium, jest automatycznie właścicielem. Możesz dodać dodatkowych właścicieli i współautorów z dzierżawy usługi Azure Active Directory (AD) skojarzonej z Twoją subskrypcją. Aby uzyskać więcej informacji na temat właściciela konta laboratorium i ról współautorów, Przeczytaj:
   -  [Przewodnik administratora — Zarządzanie tożsamościami](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Użytkownicy laboratorium widzą tylko jedną listę maszyn wirtualnych, do których mają dostęp między dzierżawcami w ramach Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Kto będzie uprawniony do tworzenia laboratoriów?

   Możesz zdecydować, aby członkowie zespołu IT i wykładowców mogli tworzyć laboratoria. Gdy użytkownik tworzy laboratorium, automatycznie są przypisywane jako właściciel laboratorium.  Aby utworzyć laboratoria, użytkownicy (zazwyczaj z dzierżawy usługi Azure AD skojarzonej z Twoją subskrypcją) muszą być przypisani do roli twórca laboratorium w ramach konta laboratorium.  Aby uzyskać więcej informacji na temat roli twórca laboratorium, Przeczytaj:
   -  [Przewodnik administratora-Zarządzanie tożsamościami](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kto będzie uprawniony do posiadania i zarządzania laboratoriami?

   Możesz również wybrać, aby członkowie zespołu own\manage laboratoria, *bez* nadawania im możliwości tworzenia laboratoriów.  W takim przypadku użytkownicy z dzierżawy usługi Azure AD Twojej subskrypcji są przypisani jako właściciel lub współautor dla istniejących laboratoriów.  Aby uzyskać więcej informacji na temat ról właściciela i współautora laboratorium, Przeczytaj:
   - [Przewodnik administratora-Zarządzanie tożsamościami](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Czy chcesz zapisać obrazy, które mogą być współużytkowane przez laboratoria?
Udostępniona Galeria obrazów jest repozytorium, którego można użyć do zapisywania i udostępniania obrazów. Dla klas, które wymagają tego samego obrazu, twórcy laboratorium mogą utworzyć obraz, a następnie wyeksportować go do galerii obrazów udostępnionych.  Po wyeksportowaniu obrazów do galerii obrazów udostępnionych może służyć do tworzenia nowych laboratoriów.

Ponadto możesz chcieć utworzyć obrazy w swoim środowisku fizycznym, a następnie zaimportować je do galerii obrazów udostępnionych.  Aby uzyskać więcej informacji na temat tego procesu, przeczytaj następujący wpis w blogu: 
- [Importowanie obrazu niestandardowego do galerii obrazów udostępnionych](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Jeśli zdecydujesz się na użycie udostępnionej galerii obrazów, musisz utworzyć lub dołączyć udostępnioną galerię obrazów do konta laboratorium. Ponadto można odroczyć tę decyzję do późniejszej, ponieważ będzie ona mogła zostać dołączona do konta laboratorium w dowolnym momencie.  Aby uzyskać więcej informacji na temat galerii obrazów udostępnionych, Przeczytaj:
- [Przewodnik administratora — Galeria obrazów udostępnionych](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery)
- [Przewodnik administratora — ceny udostępnionej galerii obrazów](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Które obrazy w portalu Azure Marketplace będą używane w laboratoriach?
Witryna Azure Marketplace udostępnia setki obrazów, które można włączyć, aby twórcy laboratorium mogli korzystać z obrazu do tworzenia ich laboratorium. Niektóre obrazy mogą zawierać wszystko, co laboratorium jest już potrzebne. W innych przypadkach można użyć obrazu jako punktu wyjścia, a następnie twórca laboratorium może go dostosować, instalując dodatkowe aplikacje lub narzędzia.

Jeśli nie wiesz, które obrazy są potrzebne, możesz wrócić później, aby je włączyć. Ponadto najlepszym sposobem, aby zobaczyć, które obrazy są dostępne, jest pierwsze utworzenie konta laboratorium. Zapewnia to dostęp, aby można było przejrzeć listę dostępnych obrazów i ich zawartości.  Aby uzyskać więcej informacji na temat obrazów z portalu Marketplace, Przeczytaj:
- [Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure lub lokalnych?
Po skonfigurowaniu konta laboratorium można także połączyć się z nim za pomocą komunikacji równorzędnej z siecią wirtualną (VNet).  Należy pamiętać, że zarówno konto sieci wirtualnej, jak i laboratorium musi znajdować się w tym samym regionie.  Aby zdecydować, czy musisz być elementem równorzędnym z siecią wirtualną, należy wziąć pod uwagę następujące scenariusze:

- **Dostęp do serwera licencjonowania**
  
   W przypadku korzystania z obrazów portalu Azure Marketplace koszt licencji systemu operacyjnego jest uwzględniany w cenach usług Lab Services. Nie trzeba jednak podawać licencji dla samego systemu operacyjnego. Jednak w przypadku dodatkowego oprogramowania i zainstalowanych aplikacji należy zapewnić odpowiednią licencję.  Aby uzyskać dostęp do serwera licencjonowania:
   - Można nawiązać połączenie z lokalnym serwerem licencjonowania.  Połączenie z lokalnym serwerem licencjonowania wymaga dodatkowej konfiguracji.
   - Kolejną opcją, która jest szybsza konfiguracja, jest utworzenie serwera licencjonowania, który jest hostem na maszynie wirtualnej platformy Azure.  Maszyna wirtualna platformy Azure znajduje się w sieci wirtualnej, która jest równorzędna z Twoim kontem laboratorium.

- **Dostęp do innych zasobów lokalnych, takich jak udział plików lub baza danych**

   Utwórz sieć wirtualną, aby zapewnić dostęp do zasobów lokalnych, zazwyczaj przy użyciu bramy sieci wirtualnej typu lokacja-lokacja. Skonfigurowanie tego typu środowiska zajmie dodatkowy czas.

- **Dostęp do innych zasobów platformy Azure, które znajdują się poza siecią wirtualną**

   Jeśli potrzebujesz dostępu do zasobów platformy Azure, które *nie* są zabezpieczone w sieci wirtualnej, możesz uzyskać dostęp do tych zasobów za pomocą publicznego Internetu, bez konieczności przeprowadzania komunikacji równorzędnej.

Aby uzyskać więcej informacji na temat sieci wirtualnych, Przeczytaj:
- [Podstawy architektury — Virtual Network](https://docs.microsoft.com/azure/lab-services/classroom-labs-fundamentals#virtual-network)
- [Jak nawiązać połączenie z siecią wirtualną](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network)
- [Jak utworzyć laboratorium z udostępnionym zasobem w Azure Lab Services](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource)

## <a name="set-up-your-lab-account"></a>Konfigurowanie konta laboratorium

Po zakończeniu planowania możesz przystąpić do konfigurowania konta laboratorium.  Te same kroki dotyczą konfigurowania Azure Lab Services laboratoryjnych [za pomocą zespołów](https://docs.microsoft.com/azure/lab-services/lab-services-within-teams-overview).

1. **Utwórz konto laboratorium.** Aby uzyskać instrukcje, zapoznaj się z samouczkiem dotyczącym [tworzenia konta laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .
   
    Aby uzyskać więcej wskazówek dotyczących nazewnictwa, zapoznaj się z następującym artykułem:

   - [Wskazówki dotyczące nazewnictwa dla zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Dodaj użytkowników do roli twórca laboratorium.** Aby uzyskać instrukcje, zobacz [Dodawanie użytkowników do roli twórca laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).


3. **Nawiąż połączenie z równorzędną siecią wirtualną.** Aby uzyskać instrukcje, zobacz [łączenie sieci laboratorium z równorzędną siecią wirtualną](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Może być również konieczne zapoznaj się z instrukcjami dotyczącymi [konfigurowania zakresu adresów maszyn wirtualnych laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Włącz i przejrzyj obrazy.** Aby uzyskać instrukcje, zobacz artykuł [Włączanie obrazów w portalu Azure Marketplace dla twórców laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Aby przejrzeć zawartość każdego obrazu portalu Azure Marketplace, wybierz nazwę obrazu. Na przykład poniższy zrzut ekranu przedstawia szczegóły dotyczące obrazu Data Science VM Ubuntu:

   ![Zrzut ekranu przedstawiający przegląd obrazów w portalu Azure Marketplace](./media/setup-guide/review-marketplace-images.png)

   Jeśli Galeria obrazów udostępnionych jest dołączona do konta laboratorium i chcesz włączyć udostępnianie obrazów niestandardowych przez twórców laboratorium, wykonaj podobne kroki, jak pokazano na poniższym zrzucie ekranu:

   ![Zrzut ekranu przedstawiający Włączanie niestandardowych obrazów w galerii obrazów udostępnionych](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Następne kroki

Następne kroki, które są wspólne dla konfigurowania środowiska laboratoryjnego:

- [Zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)
- [Przewodnik po konfiguracji laboratorium](setup-guide.md)
