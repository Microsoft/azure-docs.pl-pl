---
title: Przygotowywanie maszyn wirtualnych VMware do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych VMware za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677300"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyny wirtualnej środowiska VMware do oceny i migracji na platformę Azure

Ten artykuł ułatwia przygotowanie się do oceny i/lub migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md).



Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z Azure Migrate.
> * Przygotuj oprogramowanie VMware do oceny maszyn wirtualnych za pomocą narzędzia do oceny serwera Azure Migrate:.
> * Przygotuj program VMware do migracji maszyn wirtualnych za pomocą narzędzia do migracji Azure Migrate serwera. 

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Są one przydatne, gdy dowiesz się, jak skonfigurować wdrożenie i jak szybko sprawdzać koncepcje. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Te uprawnienia są potrzebne do tych zadań na platformie Azure, zanim będzie można ocenić lub zmigrować maszyny wirtualne VMware.

**Zadanie** | **Szczegóły** 
--- | --- 
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela do utworzenia projektu. 
**Zarejestruj dostawców zasobów** | Azure Migrate używa uproszczonego urządzenia Azure Migrate do odnajdywania i oceniania maszyn wirtualnych VMware oraz do migrowania ich do platformy Azure za pomocą Azure Migrate: Ocena serwera.<br/><br/> Podczas rejestracji urządzenia dostawcy zasobów są rejestrowani z subskrypcją wybraną w urządzeniu. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Do zarejestrowania dostawców zasobów należy mieć rolę współautor lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia Azure Migrate tworzy aplikacje Azure Active Directory (Azure AD). <br/><br/> — Pierwsza aplikacja jest używana do komunikacji między agentami działającymi na urządzeniu a swoimi usługami działającymi na platformie Azure.<br/><br/> -Druga aplikacja jest używana wyłącznie w celu uzyskania dostępu do magazynu kluczy utworzonego w ramach subskrypcji użytkownika dla migracji maszyn wirtualnych VMware bez agentów. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Musisz mieć uprawnienia do tworzenia aplikacji usługi Azure AD (dostępnych w roli Deweloper aplikacji).
**Tworzenie magazynu kluczy** | Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji bez wykorzystania agentów, Azure Migrate tworzy Key Vault do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji.<br/><br/> Aby utworzyć magazyn, musisz mieć uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt Azure Migrate.




### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia

Aby zarejestrować urządzenie, należy przypisać uprawnienia Azure Migrate do tworzenia aplikacji usługi Azure AD podczas rejestracji urządzenia. Uprawnienia można przypisać przy użyciu jednej z następujących metod:

- **Udziel uprawnień**: dzierżawy/Administrator globalny mogą udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- **Przypisywanie roli dewelopera aplikacji**: dzierżawy/Administrator globalny mogą przypisać rolę dewelopera aplikacji (która ma uprawnienia) do konta.

> [!NOTE]
> - Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Jeśli chcesz udzielić uprawnień dla dzierżawy/administratora globalnego, wykonaj następujące czynności:

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do**ustawień użytkownika** **Azure Active Directory** > **Użytkownicy** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**. Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji

Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) na temat przypisywania roli.

### <a name="assign-permissions-to-create-a-key-vault"></a>Przypisywanie uprawnień do tworzenia Key Vault

Aby umożliwić Azure Migrate tworzenia Key Vault, należy przypisać uprawnienia w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.

    - Aby uruchomić ocenę serwera, uprawnienia **współautora** są wystarczające.
    - Aby uruchomić migrację serwera bez agenta, należy mieć uprawnienia **właściciela** (lub **współautora** i **administrator dostępu użytkowników**).

3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów.



## <a name="prepare-for-vmware-vm-assessment"></a>Przygotowanie do oceny maszyny wirtualnej VMware

Aby przygotować się do oceny maszyn wirtualnych VMware, należy:

- **Sprawdź ustawienia programu VMware**. Upewnij się, że vCenter Server i maszyny wirtualne, które chcesz zmigrować, spełniają wymagania.
- **Skonfiguruj konto do oceny**. Azure Migrate używa tego konta w celu uzyskania dostępu do vCenter Server w celu odnalezienia maszyn wirtualnych do oceny.
- **Sprawdź wymagania dotyczące urządzeń**. Przed wdrożeniem programu Sprawdź wymagania dotyczące wdrażania urządzenia Azure Migrate.

### <a name="verify-vmware-settings"></a>Sprawdź ustawienia programu VMware

1. [Sprawdź](migrate-support-matrix-vmware.md#vmware-requirements) Wymagania dotyczące oceny serwera VMware.
2. [Upewnij](migrate-support-matrix-vmware.md#port-access) się, że wymagane porty są otwarte na vCenter Server.
3. Na vCenter Server upewnij się, że konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych. Należy wdrożyć urządzenie Azure Migrate jako maszynę wirtualną VMware przy użyciu pliku komórki jajowe.


### <a name="set-up-an-account-for-assessment"></a>Skonfiguruj konto do oceny

Azure Migrate musi uzyskać dostęp do vCenter Server w celu odnalezienia maszyn wirtualnych na potrzeby oceny i migracji bez wykorzystania agentów.

- Jeśli planujesz odnajdywanie aplikacji lub wizualizowanie zależności w sposób niezależny od agenta, Utwórz konto vCenter Server z dostępem tylko do odczytu wraz z włączonymi uprawnieniami dla > **operacji gościa** **maszyn wirtualnych**.

  ![uprawnienia vCenter Server konta](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Jeśli nie planujesz przeprowadzać odnajdywania aplikacji ani wizualizacji zależności bez agenta, skonfiguruj konto tylko do odczytu dla vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Weryfikuj ustawienia urządzenia do oceny

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---vmware) Wymagania dotyczące urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) .
3. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#port-access) na wymagania dotyczące dostępu do portów dla urządzenia.




## <a name="prepare-for-agentless-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware bez agentów

Zapoznaj się z wymaganiami dotyczącymi [migracji bez agentów](server-migrate-overview.md) maszyn wirtualnych VMware.

1. [Przegląd](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Wymagania dotyczące serwera VMware.
2. [Przejrzyj uprawnienia](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) , które Azure Migrate muszą uzyskać dostęp do vCenter Server.
3. [Przegląd](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Wymagania dotyczące maszyn wirtualnych VMware.
4. [Zapoznaj](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) się z wymaganiami dotyczącymi urządzeń Azure Migrate.
5. Zanotuj dostęp do adresu URL wymaganego dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .
6. Przejrzyj wymagania dotyczące [dostępu do portów](migrate-support-matrix-vmware-migration.md#agentless-ports) .

## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware opartego na agencie

Zapoznaj się z wymaganiami dotyczącymi migracji maszyn wirtualnych VMware [na podstawie agenta](server-migrate-overview.md) .

1. [Przegląd](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Wymagania dotyczące serwera VMware.
2. [Przejrzyj uprawnienia](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate musi uzyskać dostęp do vCenter Server.
2. [Przegląd](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Wymagania dotyczące maszyn wirtualnych VMware, w tym instalacja usługi mobilności na każdej maszynie wirtualnej, która ma zostać poddana migracji.
3. Migracja oparta na agencie korzysta z urządzenia replikacji:
    - [Zapoznaj](migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia replikacji.
    - [Zapoznaj się z opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
    - Zanotuj dostęp do adresu URL wymaganego dla chmur [publicznych](migrate-replication-appliance.md#url-access) i dla [instytucji rządowych](migrate-replication-appliance.md#azure-government-url-access) .
    - Przejrzyj wymagania dotyczące [dostępu do portów](migrate-replication-appliance.md#port-access) dla urządzenia replikacji.
    
## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia platformy Azure.
> * Przygotowano oprogramowanie VMware do oceny i migracji.


Przejdź do drugiego samouczka, aby skonfigurować projekt Azure Migrate i ocenić maszyny wirtualne VMware pod kątem migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocenianie maszyn wirtualnych VMware](./tutorial-assess-vmware.md)
