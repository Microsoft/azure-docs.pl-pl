---
title: Przewodnik konfigurowania konta laboratorium przyspieszonego dla Azure Lab Services
description: Ten przewodnik ułatwia administratorom szybkie skonfigurowanie konta laboratorium do użycia w ramach szkoły.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0855e59aced37e50c088cfe89ffeb3d0af9fcdca
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148307"
---
# <a name="lab-account-setup-guide"></a>Przewodnik konfigurowania konta laboratorium

Pierwszym krokiem jest skonfigurowanie przez administratorów konta laboratorium w ramach subskrypcji platformy Azure. Konto laboratorium to kontener dla laboratoriów zajęć i trwa tylko kilka minut.

## <a name="understand-your-schools-lab-account-requirements"></a>Poznaj wymagania dotyczące konta laboratorium szkoły

Aby zrozumieć, jak skonfigurować konto laboratorium na podstawie potrzeb szkoły, należy wziąć pod uwagę te pytania.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Czy mam dostęp do subskrypcji platformy Azure?

Aby utworzyć konto laboratorium, musisz mieć dostęp do subskrypcji platformy Azure, która jest skonfigurowana dla Twojej szkoły. Twoja szkoła może mieć co najmniej jedną subskrypcję. Za pomocą subskrypcji można zarządzać rozliczeniami i zabezpieczeniami dla wszystkich zasobów i usług platformy Azure, w tym kont laboratorium.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Ile kont laboratorium należy utworzyć?

Aby szybko rozpocząć pracę, Utwórz jedno konto laboratorium, a następnie w razie potrzeby Utwórz dodatkowe konta laboratorium. Na przykład może istnieć jedno konto laboratorium dla każdego działu.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kto powinna być właścicielami i współautorami konta laboratorium?

Administratorzy są zazwyczaj właścicielami i współautorami dla konta laboratorium. Są one odpowiedzialne za zarządzanie zasadami, które mają zastosowanie do wszystkich laboratoriów zawartych w ramach konta laboratorium. Osoba, która tworzy konto laboratorium, jest automatycznie właścicielem. Możesz dodać dodatkowych właścicieli i współautorów, zazwyczaj z dzierżawy usługi Azure Active Directory (Azure AD) skojarzonej z Twoją subskrypcją. Może to być przydatne do zarządzania kontem laboratorium przez przypisanie roli właściciela lub współautora na poziomie konta laboratorium.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Użytkownicy laboratorium widzą tylko jedną listę maszyn wirtualnych, do których mają dostęp między dzierżawcami w ramach Azure Lab Services.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Kto będzie uprawniony do tworzenia laboratoriów i zarządzania nimi?

Możesz zdecydować, że administratorzy i członkowie wykładowców tworzą laboratoria i zarządzają nimi. Ci użytkownicy (zazwyczaj z dzierżawy usługi Azure AD skojarzonej z Twoją subskrypcją) są przypisani do roli twórca laboratorium w ramach konta laboratorium.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Czy chcesz dać twórcom laboratorium możliwość zapisywania obrazów, które mogą być współużytkowane przez laboratoria?

Udostępniona Galeria obrazów jest repozytorium, którego można użyć do zapisywania i udostępniania obrazów. Jeśli masz kilka klas, które wymagają tych samych obrazów, twórcy laboratorium mogą utworzyć obraz jednokrotnie i udostępnić go w laboratoriach. Jednak aby rozpocząć pracę, nie musisz być potrzebować udostępnionej galerii obrazów, ponieważ zawsze możesz ją dodać później.

Jeśli otrzymasz odpowiedź "tak" na to pytanie, musisz utworzyć lub dołączyć udostępnioną galerię obrazów do konta laboratorium. Jeśli otrzymasz odpowiedź "nie wiem", możesz odłożyć tę decyzję do nowszej wersji.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Które obrazy w portalu Azure Marketplace będą używane przez laboratoria zajęć?

Witryna Azure Marketplace udostępnia setki obrazów, które można włączyć, aby twórcy laboratorium mogli korzystać z obrazu do tworzenia ich laboratorium. Niektóre obrazy mogą zawierać wszystko, co laboratorium jest już potrzebne. W innych przypadkach można użyć obrazu jako punktu wyjścia, a następnie twórca laboratorium może go dostosować, instalując dodatkowe aplikacje lub narzędzia.

Jeśli nie wiesz, które obrazy będą potrzebne, zawsze możesz wrócić do tej osoby później, aby je włączyć. Ponadto najlepszym sposobem, aby zobaczyć, które obrazy są dostępne, jest pierwsze utworzenie konta laboratorium. Zapewnia to dostęp do programu, aby można było przejrzeć listę dostępnych obrazów i ich zawartości.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure lub lokalnych?

Podczas konfigurowania konta laboratorium istnieje również opcja komunikacji równorzędnej z siecią wirtualną. Aby zdecydować, czy jest to konieczne, należy wziąć pod uwagę następujące kwestie:

- **Czy musisz zapewnić dostęp do serwera licencjonowania?**
  
   Jeśli planujesz korzystanie z obrazów portalu Azure Marketplace, koszt licencji systemu operacyjnego jest powiązany z cenami usług laboratoryjnych. W związku z tym nie trzeba podawać licencji dla samego systemu operacyjnego. Jednak w przypadku dodatkowego oprogramowania i zainstalowanych aplikacji należy zapewnić odpowiednią licencję.

- **Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów lokalnych, takich jak udział plików lub baza danych?**

   Można utworzyć sieć wirtualną w celu zapewnienia dostępu do zasobów lokalnych, zazwyczaj przy użyciu bramy sieci wirtualnej typu lokacja-lokacja. Jeśli nie masz skonfigurowanej sieci wirtualnej, musisz zainwestować dodatkowy czas na ten temat.

- **Czy maszyny wirtualne laboratorium muszą mieć dostęp do innych zasobów platformy Azure, które znajdują się w ramach sieci wirtualnej?**

   Jeśli potrzebujesz dostępu do zasobów platformy Azure, które *nie* są zabezpieczone w ramach sieci wirtualnej, możesz uzyskać dostęp do tych zasobów za pomocą publicznego Internetu, bez konieczności przeprowadzania komunikacji równorzędnej.

Jeśli otrzymasz odpowiedź "tak" na jedno lub więcej pytań, należy połączyć połączenie równorzędne z siecią wirtualną. Jeśli otrzymasz odpowiedź "nie wiem", możesz odłożyć tę decyzję do nowszej wersji. Po utworzeniu konta laboratorium zawsze możesz wybrać opcję komunikacji równorzędnej z siecią wirtualną.

## <a name="set-up-your-lab-account"></a>Konfigurowanie konta laboratorium

Po zrozumieniu wymagań dotyczących konta laboratorium można je skonfigurować.

1. **Utwórz konto laboratorium.** Aby uzyskać instrukcje, zapoznaj się z samouczkiem dotyczącym [tworzenia konta laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .

   Podczas tworzenia konta laboratorium pomocne może okazać się zapoznanie się z zasobami platformy Azure. Aby uzyskać więcej informacji, zobacz następujące artykuły:

   - [Subskrypcja](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupa zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Konto laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratorium zajęć](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Wybieranie regionu i lokalizacji](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Wskazówki dotyczące nazewnictwa dla zasobów](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Dodaj użytkowników do roli twórca laboratorium.** Aby uzyskać instrukcje, zobacz [Dodawanie użytkowników do roli twórca laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Ponadto, aby uzyskać więcej informacji na temat różnych ról, które mogą być przypisane do użytkowników, którzy będą zarządzać kontami laboratorium i laboratoriami, zobacz [Przewodnik dotyczący zarządzania tożsamością](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Nawiąż połączenie z równorzędną siecią wirtualną.** Aby uzyskać instrukcje, zobacz [łączenie sieci laboratorium z równorzędną siecią wirtualną](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Może być również konieczne zapoznaj się z instrukcjami dotyczącymi [konfigurowania zakresu adresów maszyn wirtualnych laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Włącz i przejrzyj obrazy.** Aby uzyskać instrukcje, zobacz artykuł [Włączanie obrazów w portalu Azure Marketplace dla twórców laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Aby przejrzeć zawartość każdego obrazu portalu Azure Marketplace, wybierz nazwę obrazu. Na przykład poniższy zrzut ekranu przedstawia szczegóły dotyczące obrazu Data Science VM Ubuntu:

   ![Zrzut ekranu przedstawiający przegląd obrazów w portalu Azure Marketplace](./media/setup-guide/review-marketplace-images.png)

   Jeśli masz dołączoną galerię obrazów do Twojego konta laboratorium i chcesz włączyć udostępnianie obrazów niestandardowych przez twórców laboratorium, wykonaj kroki podobne do tych przedstawionych na poniższym zrzucie ekranu:

   ![Zrzut ekranu przedstawiający Włączanie niestandardowych obrazów w galerii obrazów udostępnionych](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)

- [Przewodnik konfigurowania laboratorium zajęć](setup-guide.md)
