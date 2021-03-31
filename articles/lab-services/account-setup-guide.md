---
title: Przewodnik konfigurowania konta laboratorium przyspieszonego dla Azure Lab Services
description: Ten przewodnik ułatwia administratorom szybkie skonfigurowanie konta laboratorium do użycia w ramach szkoły.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669393"
---
# <a name="lab-account-setup-guide"></a>Przewodnik konfigurowania konta laboratorium
Jeśli jesteś administratorem, przed skonfigurowaniem środowiska Azure Lab Services musisz najpierw utworzyć *konto laboratorium* w ramach subskrypcji platformy Azure. Konto laboratorium jest kontenerem dla jednej lub kilku laboratoriów, a jego konfiguracja trwa zaledwie kilka minut.

Ten przewodnik zawiera trzy sekcje:
-  Wymagania wstępne
-  Planowanie ustawień konta laboratorium
-  Konfigurowanie konta laboratorium

## <a name="prerequisites"></a>Wymagania wstępne
Poniższe sekcje zawierają informacje o tym, co należy zrobić przed skonfigurowaniem konta laboratorium.


### <a name="access-your-azure-subscription"></a>Dostęp do subskrypcji platformy Azure
Aby utworzyć konto laboratorium, musisz mieć dostęp do subskrypcji platformy Azure, która jest już skonfigurowana dla Twojej szkoły. Twoja szkoła może mieć co najmniej jedną subskrypcję. Za pomocą subskrypcji można zarządzać rozliczeniami i zabezpieczeniami dla wszystkich zasobów i usług platformy Azure, w tym kont laboratorium.  Subskrypcje platformy Azure są zwykle zarządzane przez dział IT.  Aby uzyskać więcej informacji, zobacz sekcję "subskrypcja" [podręcznika Azure Lab Services-administrator](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Oszacuj liczbę potrzebnych maszyn wirtualnych i rozmiarów maszyn wirtualnych
Ważne jest, aby wiedzieć, ile [maszyn wirtualnych i rozmiary maszyn](./administrator-guide.md#vm-sizing) wirtualnych są wymagane przez laboratorium szkolne. 

Aby uzyskać wskazówki dotyczące tworzenia struktury laboratoriów i obrazów, zapoznaj się z wpisem w blogu [przenoszonym z poziomu laboratorium fizycznego na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Aby uzyskać dodatkowe wskazówki dotyczące struktury laboratoriów, zobacz sekcję "Lab" [podręcznika Azure Lab Services-administrator](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Informacje o limitach maszyn wirtualnych i pojemności regionalnej maszyny wirtualnej
Po oszacowaniu liczby maszyn wirtualnych i rozmiarów maszyn wirtualnych dla laboratoriów należy wykonać następujące działania:

- Upewnij się, że limit pojemności subskrypcji platformy Azure umożliwia określenie liczby maszyn wirtualnych i rozmiaru maszyny wirtualnej, który ma być używany w laboratoriach.
- Utwórz konto laboratorium w regionie, który ma wystarczającą ilość dostępnej pojemności maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [limity subskrypcji maszyn wirtualnych i pojemność regionalna](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Zdecyduj, ile kont laboratorium do utworzenia

Aby szybko rozpocząć pracę, Utwórz pojedyncze konto laboratorium w ramach własnej grupy zasobów.  Później można utworzyć dodatkowe konta laboratorium i grupy zasobów zgodnie z wymaganiami. Na przykład może istnieć jedno konto laboratorium i Grupa zasobów dla każdego działu jako sposób na wyraźne oddzielenie kosztów. 

Aby uzyskać więcej informacji na temat kont laboratorium, grup zasobów i rozdzielania kosztów, zobacz:
- Sekcja "Grupa zasobów" [podręcznika Azure Lab Services-administrator](./administrator-guide.md#resource-group)
- Sekcja "konto laboratorium" w [podręczniku Azure Lab Services-administrator](./administrator-guide.md#lab-account) 
- [Zarządzanie kosztami dla Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planowanie ustawień konta laboratorium

Aby zaplanować ustawienia konta laboratorium, należy wziąć pod uwagę następujące kwestie.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Kto powinna być właścicielami i współautorami konta laboratorium?

Administratorzy IT szkoły zwykle przyjmują role właściciela i współautora dla konta laboratorium. Role te są odpowiedzialne za zarządzanie zasadami, które mają zastosowanie do wszystkich laboratoriów na koncie laboratorium. Osoba, która tworzy konto laboratorium, jest automatycznie właścicielem. Możesz dodać dodatkowych właścicieli i współautorów z dzierżawy usługi Azure Active Directory (Azure AD) skojarzonej z Twoją subskrypcją. 

Aby uzyskać więcej informacji na temat roli właściciela konta laboratorium i współautorów, zobacz sekcję "Zarządzanie tożsamością" w [podręczniku Azure Lab Services-administratora](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Użytkownicy laboratorium widzą tylko jedną listę maszyn wirtualnych, do których mają dostęp w ramach dzierżawców usługi Azure AD w Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Kto będzie uprawniony do tworzenia laboratoriów?

Możesz wybrać, aby zespół IT lub członkowie wykładowców mogli tworzyć laboratoria. Aby utworzyć laboratoria, należy przypisać te osoby do roli twórca laboratorium w ramach konta laboratorium. Tę rolę zwykle przypisuje się do dzierżawy usługi Azure AD, która jest skojarzona z Twoją subskrypcją szkoły. Użytkownik tworzący laboratorium jest automatycznie przypisywany jako właściciel laboratorium.  

Aby uzyskać więcej informacji na temat roli twórca laboratorium, zobacz sekcję "Zarządzanie tożsamością" [podręcznika Azure Lab Services-administratora](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kto będzie uprawniony do posiadania i zarządzania laboratoriami?

Możesz również wybrać, aby członkowie zespołu own\manage laboratoria, *bez* nadawania im możliwości tworzenia laboratoriów.  W takim przypadku użytkownicy z dzierżawy usługi Azure AD Twojej subskrypcji są przypisani jako właściciel lub współautor dla istniejących laboratoriów.  

Aby uzyskać więcej informacji o rolach właściciela i współautorów laboratorium, zobacz sekcję "Zarządzanie tożsamością" w [podręczniku Azure Lab Services-administratora](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Czy chcesz zapisać obrazy i udostępnić je w laboratoriach?

Udostępniona Galeria obrazów to usługa, która umożliwia zapisywanie i udostępnianie obrazów. Dla klas, które muszą korzystać z tego samego obrazu, twórcy laboratorium mogą utworzyć obraz, a następnie wyeksportować go do galerii obrazów udostępnionych.  Po wyeksportowaniu obrazu do galerii obrazów udostępnionych można go użyć do utworzenia nowych laboratoriów.

Możesz chcieć utworzyć obrazy w swoim środowisku fizycznym, a następnie zaimportować je do udostępnionej galerii obrazów. Aby uzyskać więcej informacji, zobacz wpis w blogu [Importowanie obrazu niestandardowego do galerii obrazów udostępnionych](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Jeśli zdecydujesz się użyć usługi udostępnionej galerii obrazów, musisz utworzyć lub dołączyć udostępnioną galerię obrazów do konta laboratorium. Możesz odłożyć tę decyzję teraz, ponieważ Galeria obrazów udostępnionych może być dołączona do konta laboratorium w dowolnym momencie.  

Aby uzyskać więcej informacji, zobacz:
- Sekcja "Galeria obrazów udostępnionych" w [podręczniku Azure Lab Services-administrator](./administrator-guide.md#shared-image-gallery)
- Sekcja "Cennik" [podręcznika Azure Lab Services-administrator](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Które obrazy w portalu Azure Marketplace będą używane w laboratoriach?

Witryna Azure Marketplace udostępnia setki obrazów, które można włączyć, aby twórcy laboratorium mogli ich używać do tworzenia laboratoriów. Niektóre obrazy mogą zawierać wszystko, co laboratorium jest już potrzebne. W innych przypadkach można użyć obrazu jako punktu wyjścia, a następnie twórca laboratorium może go dostosować, instalując dodatkowe aplikacje lub narzędzia.

Jeśli nie wiesz, które obrazy są potrzebne, możesz wrócić później, aby je włączyć. Najlepszym sposobem, aby zobaczyć, które obrazy są dostępne, jest pierwsze utworzenie konta laboratorium. Zapewnia to dostęp, aby można było przejrzeć listę dostępnych obrazów i ich zawartości.  

Aby uzyskać więcej informacji, zobacz temat [Określanie obrazów portalu Azure Marketplace, które są dostępne dla twórców laboratorium](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure lub lokalnych?

Po skonfigurowaniu konta laboratorium można także połączyć się z nim za pomocą komunikacji równorzędnej z siecią wirtualną.  Należy pamiętać, że zarówno sieć wirtualna, jak i konto laboratorium muszą znajdować się w tym samym regionie.  Aby zdecydować, czy musisz być elementem równorzędnym z siecią wirtualną, należy wziąć pod uwagę następujące scenariusze:

- **Dostęp do serwera licencji**
  
   W przypadku korzystania z obrazów portalu Azure Marketplace koszt licencji systemu operacyjnego jest uwzględniany w cenach usług Lab Services. Nie trzeba jednak podawać licencji dla samego systemu operacyjnego. W przypadku dodatkowego oprogramowania i zainstalowanych aplikacji należy zapewnić odpowiednią licencję.  Aby uzyskać dostęp do serwera licencji:
   - Możesz nawiązać połączenie z lokalnym serwerem licencji.  Połączenie z lokalnym serwerem licencji wymaga dodatkowej konfiguracji.
   - Kolejną opcją, która jest szybsza konfiguracja, jest utworzenie serwera licencji, który jest hostem na maszynie wirtualnej platformy Azure.  Maszyna wirtualna platformy Azure znajduje się w sieci wirtualnej, która jest równorzędna z Twoim kontem laboratorium.

- **Dostęp do innych zasobów lokalnych, takich jak udział plików lub baza danych**

   Zazwyczaj tworzysz sieć wirtualną w celu zapewnienia dostępu do zasobów lokalnych przy użyciu bramy sieci wirtualnej typu lokacja-lokacja. Skonfigurowanie tego typu środowiska zajmie dodatkowy czas.

- **Dostęp do innych zasobów platformy Azure, które znajdują się poza siecią wirtualną**

   Jeśli potrzebujesz dostępu do zasobów platformy Azure, które *nie* są zabezpieczone w ramach sieci wirtualnej, możesz uzyskiwać do nich dostęp za pomocą publicznego Internetu, bez konieczności przeprowadzania jakiejkolwiek komunikacji równorzędnej.

   Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz:
   - Sekcja "Sieć wirtualna" w temacie [podstawowe informacje o architekturze w Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Połącz sieć laboratorium z równorzędną siecią wirtualną w Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Tworzenie laboratorium z udostępnionym zasobem w Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Konfigurowanie konta laboratorium

Po zakończeniu planowania możesz przystąpić do konfigurowania konta laboratorium. Możesz zastosować te same kroki, aby skonfigurować [Azure Lab Services w zespołach](./lab-services-within-teams-overview.md).

1. **Utwórz konto laboratorium**. Aby uzyskać instrukcje, zobacz [Tworzenie konta laboratorium](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Aby uzyskać informacje na temat konwencji nazewnictwa, zobacz sekcję "nazewnictwo" [podręcznika Azure Lab Services-administratora](./administrator-guide.md#naming).

1. **Dodaj użytkowników do roli twórca laboratorium**. Aby uzyskać instrukcje, zobacz [Dodawanie użytkowników do roli twórca laboratorium](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Nawiąż połączenie z równorzędną siecią wirtualną**. Aby uzyskać instrukcje, zobacz [łączenie sieci laboratoryjnej z równorzędną siecią wirtualną](./how-to-connect-peer-virtual-network.md).

   Może być również konieczne zapoznaj się z instrukcjami dotyczącymi [konfigurowania zakresu adresów maszyn wirtualnych laboratorium](./how-to-configure-lab-accounts.md).

1. **Włącz i przejrzyj obrazy**. Aby uzyskać instrukcje, zobacz temat [Określanie, które obrazy w portalu Azure Marketplace są dostępne dla twórców laboratorium](./specify-marketplace-images.md).

   Aby przejrzeć zawartość każdego obrazu portalu Azure Marketplace, wybierz nazwę obrazu. Na przykład poniższy zrzut ekranu przedstawia szczegóły obrazu Data Science VM Ubuntu:

   ![Zrzut ekranu przedstawiający listę obrazów dostępnych do przejrzenia w portalu Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Jeśli Galeria obrazów udostępnionych jest dołączona do konta laboratorium i chcesz włączyć udostępnianie obrazów niestandardowych przez twórców laboratorium, wykonaj podobne kroki, jak pokazano na poniższym zrzucie ekranu:

   ![Zrzut ekranu przedstawiający listę włączonych obrazów niestandardowych w galerii obrazów udostępnionych.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania laboratoriów i zarządzania nimi, zobacz:

- [Zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)  
- [Przewodnik po konfiguracji laboratorium](setup-guide.md)
