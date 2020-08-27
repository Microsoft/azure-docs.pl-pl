---
title: Przygotuj serwery fizyczne do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji serwerów fizycznych za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aba1608c9219e7e8dffe66344b04fa3f085b06f3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927378"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Przygotowanie do oceny i migracji serwerów fizycznych na platformę Azure

W tym artykule opisano sposób przygotowania do oceny lokalnych serwerów fizycznych za pomocą [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](./migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać serwery fizyczne przy użyciu Azure Migrate. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia dla konta i zasobów platformy Azure do pracy z Azure Migrate.
> * Przygotuj lokalne serwery fizyczne do oceny serwera.


> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z informacjami na temat oceny serwerów fizycznych.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure-for-server-assessment"></a>Przygotuj platformę Azure do oceny serwera

Skonfiguruj platformę Azure do pracy z Azure Migrate. 

**Zadanie** | **Szczegóły** 
--- | --- 
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela do utworzenia projektu. 
**Zarejestruj dostawców zasobów (tylko Ocena)** | Azure Migrate używa uproszczonego urządzenia Azure Migrate do odnajdywania i oceniania maszyn przy użyciu Azure Migrate: Ocena serwera.<br/><br/> Podczas rejestracji urządzenia dostawcy zasobów są rejestrowani z subskrypcją wybraną w urządzeniu. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Do zarejestrowania dostawców zasobów należy mieć rolę współautor lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD (tylko Ocena)** | Podczas rejestrowania urządzenia Azure Migrate tworzy aplikację Azure Active Directory (Azure AD), która jest używana do komunikacji między agentami działającymi na urządzeniu i odpowiednimi usługami działającymi na platformie Azure. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Musisz mieć uprawnienia do tworzenia aplikacji usługi Azure AD (dostępnych w roli Deweloper aplikacji).
**Tworzenie magazynu kluczy** | Key Vault jest tworzony w ramach rejestracji urządzenia i służy do zarządzania certyfikatem pobranym na urządzeniu podczas jego konfiguracji.<br/><br/>Aby umożliwić Azure Migrate tworzenia Key Vault, konto platformy Azure musi mieć uprawnienia współautora w grupie zasobów, w której znajduje się projekt Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu 

Sprawdź, czy masz uprawnienia do tworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do zarejestrowania urządzenia 

Można przypisać uprawnienia dla Azure Migrate, aby utworzyć aplikację usługi Azure AD podczas rejestracji urządzenia, korzystając z jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

> [!NOTE]
> - Aplikacja nie ma żadnych innych uprawnień dostępu do subskrypcji innej niż opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób:

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do **Azure Active Directory**  >  **Users**  >  **ustawień użytkownika**Azure Active Directory użytkownicy.
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="prepare-azure-for-physical-server-migration"></a>Przygotowanie platformy Azure do migracji serwera fizycznego

Przygotuj platformę Azure do migracji serwerów fizycznych przy użyciu migracji serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu Azure Migrate** | Twoje konto platformy Azure musi mieć uprawnienia współautora lub właściciela, aby utworzyć projekt.
**Weryfikowanie uprawnień do konta platformy Azure** | Twoje konto platformy Azure wymaga uprawnień do utworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.
**Tworzenie sieci platformy Azure** | Skonfiguruj sieć na platformie Azure.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta platformy Azure. Zapewnia to następujące uprawnienia:
  - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
  - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
  - Zapisz na dysku zarządzanym platformy Azure. 

### <a name="create-an-azure-network"></a>Tworzenie sieci platformy Azure

[Skonfiguruj](../virtual-network/manage-virtual-network.md#create-a-virtual-network) sieć wirtualną platformy Azure. Podczas replikowania na platformę Azure maszyny wirtualne platformy Azure są tworzone i łączone do sieci wirtualnej platformy Azure, która została określona podczas konfigurowania migracji.


## <a name="prepare-for-physical-server-assessment"></a>Przygotowanie do oceny serwera fizycznego

Aby przygotować się do oceny serwera fizycznego, należy sprawdzić ustawienia serwera fizycznego i zweryfikować ustawienia wdrożenia urządzenia:

### <a name="verify-physical-server-settings"></a>Sprawdź ustawienia serwera fizycznego

1. Sprawdź [wymagania dotyczące serwera fizycznego](migrate-support-matrix-physical.md#physical-server-requirements) na potrzeby oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-physical.md#port-access) są otwarte na serwerach fizycznych.


### <a name="verify-appliance-settings"></a>Weryfikowanie ustawień urządzenia

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---physical) wymagania dotyczące urządzeń dla serwerów fizycznych.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) .
3. [Sprawdź](migrate-appliance.md#collected-data---vmware) , czy urządzenie będzie zbierane podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-physical.md#port-access) na wymagania dostępu portów Ocena serwera fizycznego.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Skonfiguruj konto do odnajdowania serwera fizycznego

Azure Migrate wymaga uprawnień do odnajdywania serwerów lokalnych.

- **System Windows:** Do odnajdywania serwerów przyłączonych do domeny należy konto domeny i konto lokalne w celu odnalezienia maszyn, które nie są przyłączone do domeny. Konto użytkownika należy dodać do tych grup: Użytkownicy zarządzania zdalnego, użytkownicy monitora wydajności i Użytkownicy dzienników wydajności.
- System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne.

## <a name="prepare-for-physical-server-migration"></a>Przygotowanie do migracji serwera fizycznego

Zapoznaj się z wymaganiami dotyczącymi migracji serwerów fizycznych.

> [!NOTE]
> Podczas migrowania maszyn fizycznych Azure Migrate: Migracja serwera korzysta z tej samej architektury replikacji co w przypadku odzyskiwania po awarii opartego na agencie w usłudze Azure Site Recovery, a niektóre składniki mają tę samą bazę kodu. Część zawartości może zostać połączona z dokumentacją Site Recovery.

1. [Przejrzyj](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania dotyczące serwera fizycznego na potrzeby migracji.
2. Azure Migrate: Migracja serwera używa serwera replikacji do migracji serwera fizycznego:
    - [Zapoznaj](migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia replikacji i [opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
    - Przejrzyj [adresy URL platformy Azure](migrate-appliance.md#url-access) wymagane przez urządzenie replikacji, aby uzyskać dostęp do chmur publicznych i instytucji rządowych.
    - Zapoznaj się z tematem [port] (Migrowanie-replikacja-urządzenie. MD # port-dostęp) wymagania dostępu dla urządzenia replikacji.
3. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
    - Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.



## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia konta platformy Azure.
> * Przygotowano serwery fizyczne do oceny.

Przejdź do następnego samouczka, aby utworzyć projekt Azure Migrate i ocenić serwery fizyczne do migracji na platformę Azure

> [!div class="nextstepaction"]
> [Ocena serwerów fizycznych](./tutorial-assess-physical.md)
