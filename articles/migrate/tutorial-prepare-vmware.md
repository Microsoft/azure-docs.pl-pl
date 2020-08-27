---
title: Przygotowywanie maszyn wirtualnych VMware do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych VMware za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927310"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyny wirtualnej środowiska VMware do oceny i migracji na platformę Azure

Ten artykuł ułatwia przygotowanie się do oceny i migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Migrate](migrate-services-overview.md).

Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z Azure Migrate.
> * Przygotuj się do oceny maszyn wirtualnych VMware za pomocą narzędzia do oceny serwera Azure Migrate:.
> * Przygotowanie do migracji maszyn wirtualnych VMware za pomocą narzędzia do migracji serwera Azure Migrate:. 

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Są one przydatne jako szybkie Weryfikacja koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Tabela zawiera podsumowanie zadań, które należy wykonać na platformie Azure. Instrukcje dotyczące każdego zadania są zgodne z tabelą.

**Zadanie** | **Szczegóły** | **Uprawnienia**
--- | --- | ---
**Tworzenie projektu Azure Migrate** | Projekt Azure Migrate stanowi centralną lokalizację organizowania ocen i migracji oraz zarządzania nimi za pomocą narzędzi Azure Migrate, narzędzi firmy Microsoft i ofert innych firm. | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela w grupie zasobów, w której znajduje się projekt.
**Zarejestruj urządzenie** | Azure Migrate korzysta z urządzenia uproszczonego Azure Migrate w celu odnajdywania maszyn wirtualnych, oceny ich za pomocą narzędzia do oceny serwera i migracji ich przy użyciu migracji bez agenta za pomocą narzędzia migracji serwera. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration) o rejestracji. | Aby zarejestrować urządzenie, konto platformy Azure musi mieć uprawnienia współautora lub właściciela subskrypcji platformy Azure.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia Azure Migrate tworzy dwie aplikacje Active Directory platformy Azure (Azure AD). <br/><br/> — Pierwsza aplikacja jest używana do komunikacji między agentami działającymi na urządzeniu a Azure Migrate. <br/><br/> -Druga aplikacja jest używana wyłącznie w celu uzyskania dostępu do magazynu kluczy utworzonego w ramach subskrypcji użytkownika dla migracji maszyn wirtualnych VMware bez agentów.   | Twoje konto platformy Azure wymaga tych [uprawnień](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps) do tworzenia aplikacji usługi Azure AD.
**Tworzenie magazynu kluczy** | — Pierwszy Key Vault jest tworzony w ramach rejestracji urządzenia i służy do zarządzania certyfikatem pobranym na urządzeniu podczas jego konfiguracji. <br/><br/> — Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji bez wykorzystania agentów, Azure Migrate tworzy kolejną Key Vault do zarządzania kluczami dostępu do konta replikacji w ramach subskrypcji.| Aby umożliwić Azure Migrate tworzenia Key Vault, należy ustawić uprawnienia (właściciel lub współautor i administrator dostępu użytkowników) w grupie zasobów, w której znajduje się projekt Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Przypisywanie uprawnień do tworzenia aplikacji usługi Azure AD

Aby zarejestrować urządzenie, konto platformy Azure musi mieć uprawnienia do tworzenia aplikacji usługi Azure AD. Przypisz uprawnienia przy użyciu jednej z następujących metod:

- **Metoda 1. Przyznawanie uprawnień**kontu: dzierżawy/Administrator globalny mogą przyznawać uprawnienia kontom użytkowników w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- **Metoda 2. Przypisywanie roli z uprawnieniami do konta użytkownika**: dzierżawy/Administrator globalny mogą przypisać rolę dewelopera aplikacji (która ma wymagane uprawnienia) do konta użytkownika.

> [!NOTE]
> Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia. Po skonfigurowaniu urządzenia można usunąć uprawnienia.


#### <a name="method-1-grant-permissions-to-the-account"></a>Metoda 1. Przyznawanie uprawnień do konta

Udziel uprawnień do konta w następujący sposób:

1. Upewnij się, że jesteś dzierżawcą lub administratorem globalnym. Następnie w usłudze Azure AD przejdź do **Azure Active Directory**  >  **Users**  >  **ustawień użytkownika**Azure Active Directory użytkownicy.
2. Ustaw wartość **rejestracje aplikacji** na **tak**. Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Metoda 2. Przypisywanie roli dewelopera aplikacji

Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) na temat przypisywania roli.

### <a name="assign-permissions-to-create-a-key-vault"></a>Przypisywanie uprawnień do tworzenia Key Vault

Aby umożliwić Azure Migrate tworzenia Key Vault, należy przypisać uprawnienia w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.

    - Aby uruchomić ocenę serwera, uprawnienia **współautora** są wystarczające.
    - Aby uruchomić migrację serwera bez agenta, należy mieć uprawnienia **właściciela** (lub **współautora** i **administrator dostępu użytkowników**).

3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów.

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Aby przygotować się do oceny maszyn wirtualnych VMware, należy:

1. **Sprawdź ustawienia programu VMware**. Upewnij się, że vCenter Server i maszyny wirtualne, które chcesz zmigrować, spełniają wymagania.
2. **Skonfiguruj uprawnienia do oceny**. Azure Migrate używa konta vCenter w celu uzyskania dostępu do vCenter Server w celu odnalezienia i oceny maszyn wirtualnych.
3. **Sprawdź wymagania dotyczące urządzeń**. Przed wdrożeniem w następnym samouczku Sprawdź wymagania dotyczące wdrażania urządzenia Azure Migrate.

### <a name="verify-vmware-settings"></a>Sprawdź ustawienia programu VMware

1. [Sprawdź wymagania programu VMware](migrate-support-matrix-vmware.md#vmware-requirements) dotyczące oceny.
2. [Upewnij](migrate-support-matrix-vmware.md#port-access-requirements) się, że wymagane porty są otwarte na vCenter Server.
3. Na vCenter Server sprawdź, czy Twoje konto ma uprawnienia do tworzenia maszyny wirtualnej przy użyciu pliku komórek jajowych. Jest to konieczne w przypadku wdrożenia urządzenia Azure Migrate jako maszyny wirtualnej VMware przy użyciu pliku komórki jajowe.
4. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.

    - W niektórych systemach operacyjnych Azure Migrate automatycznie wprowadza te zmiany. 
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
    - Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.


### <a name="set-up-permissions-for-assessment"></a>Konfigurowanie uprawnień do oceny

Azure Migrate musi uzyskać dostęp do vCenter Server, aby urządzenie Azure Migrate mogły odnajdywać maszyny wirtualne na potrzeby oceny i migracji bez wykorzystania agentów. Skonfiguruj konto w następujący sposób:

1. W kliencie sieci Web vSphere Otwórz pozycję **Administracja**  >  **dostęp do**  >  **użytkowników i grup logowania jednokrotnego**.
2. W obszarze **Użytkownicy**kliknij ikonę **nowy użytkownik** .
3. Wpisz szczegóły nowego użytkownika.
4. Wybierz uprawnienia zgodnie z wartościami tabeli.

    **Funkcja** | **Uprawnienia konta**
    --- | ---
    [Ocenianie maszyn wirtualnych](tutorial-assess-vmware.md) | W celu dokonania oceny konto wymaga dostępu tylko do odczytu.
    [Odkryj aplikacje, role i funkcje maszyny wirtualnej](how-to-discover-applications.md) | Jeśli chcesz korzystać z funkcji odnajdywania aplikacji, konto tylko do odczytu używane do oceny wymaga uprawnień włączonych dla **Virtual machines**  >  **operacji gościa**maszyn wirtualnych.
    [Analizowanie zależności na maszynach wirtualnych (bez agentów)](how-to-create-group-machine-dependencies-agentless.md) | Jeśli chcesz analizować zależności, konto tylko do odczytu używane do oceny wymaga uprawnień włączonych dla **Virtual machines**  >  **operacji gościa**maszyn wirtualnych.
    
> [!NOTE]
> Jeśli chcesz ograniczyć odnajdywanie maszyn wirtualnych w celu oceny do określonego zakresu, zapoznaj się z [tym artykułem](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Weryfikuj ustawienia urządzenia do oceny

W [następnym samouczku](tutorial-assess-vmware.md) skonfigurujesz urządzenie Azure Migrate i rozpocznie się Ocena. Przed wykonaniem tych czynności Przejrzyj wymagania dotyczące urządzenia w następujący sposób: 

1. [Zapoznaj się z wymaganiami](migrate-appliance.md#appliance---vmware) dotyczącymi wdrażania urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) .
3. [Zanotuj](migrate-support-matrix-vmware.md#port-access-requirements) porty używane przez urządzenie.
4. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.

## <a name="prepare-for-agentless-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware bez agentów

Maszyny wirtualne VMware można migrować przy użyciu funkcji [bezagentowej lub migracji opartej na agentach](server-migrate-overview.md). Ta sekcja podsumowuje wymagania dotyczące migracji bez wykorzystania agentów.

1. [Zdecyduj](server-migrate-overview.md#compare-migration-methods) , czy chcesz korzystać z migracji bez wykorzystania agentów.
2. [Zapoznaj](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) się z wymaganiami funkcji hypervisor dla maszyn, które chcesz zmigrować.
3. [Zapoznaj](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) się z wymaganiami dotyczącymi maszyn wirtualnych VMware, które chcesz migrować przy użyciu migracji bez wykorzystania agentów.
4. [Zapoznaj](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) się z wymaganiami dotyczącymi urządzeń Azure Migrate dotyczących migracji bez wykorzystania agentów.
5. Zanotuj dostęp do adresu URL wymaganego dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .
6. Przejrzyj wymagania dotyczące [dostępu do portów](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) .
7. Skonfiguruj uprawnienia do migracji bez wykorzystania agentów zgodnie z opisem w następnej procedurze.


### <a name="assign-permissions-to-an-account"></a>Przypisywanie uprawnień do konta

Urządzenie Azure Migrate łączy się z vCenter Server w celu odnalezienia i migracji maszyn wirtualnych z migracją bez agenta. Można przypisać uprawnienia wymagane przez urządzenie do konta użytkownika lub utworzyć rolę z uprawnieniami, a następnie przypisać tę rolę do konta użytkownika.

1. W kliencie sieci Web vSphere Otwórz pozycję **Administracja**  >  **dostęp do**  >  **użytkowników i grup logowania jednokrotnego**.
2. W obszarze **Użytkownicy**kliknij ikonę **nowy użytkownik** .
3. Wpisz szczegóły nowego użytkownika.
4. Przypisz [te uprawnienia](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Utwórz rolę i przypisz ją do konta

Alternatywnie możesz utworzyć konto. Następnie Utwórz rolę i przypisz ją do konta w następujący sposób:

1. Zaloguj się do klienta w sieci Web jako administrator vCenter Server
2. Wybierz wystąpienie vCenter Server >  **Utwórz rolę**.
3. Określ nazwę roli, na przykład <em>Azure_Migrate</em>, i przypisz [wymagane uprawnienia](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) do roli.

    ![uprawnienia vCenter Server konta](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Teraz Utwórz konto i przypisz rolę do konta.

> [!NOTE]
> Jeśli chcesz ograniczyć odnajdywanie maszyn wirtualnych w celu migracji bez agentów do określonego zakresu, zapoznaj się z [tym artykułem](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji oprogramowania VMware opartego na agencie

Maszyny wirtualne VMware można migrować przy użyciu funkcji [bezagentowej lub migracji opartej na agentach](server-migrate-overview.md). Ta sekcja podsumowuje wymagania dotyczące migracji opartej na agencie.

1. [Zdecyduj](server-migrate-overview.md#compare-migration-methods) , czy chcesz korzystać z migracji opartej na agentach.
1. [Zapoznaj](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) się z wymaganiami funkcji hypervisor dla maszyn, które chcesz zmigrować.
2. [Przegląd](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Wymagania dotyczące maszyn wirtualnych VMware, w tym instalacja usługi mobilności na każdej maszynie wirtualnej, która ma zostać poddana migracji.
3. Migracja oparta na agencie korzysta z urządzenia replikacji:
    - [Zapoznaj](migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia replikacji.
    - [Zapoznaj się z opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
    - Zanotuj dostęp do adresu URL wymaganego dla chmur [publicznych](migrate-replication-appliance.md#url-access) i dla [instytucji rządowych](migrate-replication-appliance.md#azure-government-url-access) .
    - Przejrzyj wymagania dotyczące [dostępu do portów](migrate-replication-appliance.md#port-access) dla urządzenia replikacji.
4. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych. Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.



## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia platformy Azure.
> * Przygotowano oprogramowanie VMware do oceny i migracji.


Przejdź do drugiego samouczka, aby skonfigurować projekt Azure Migrate i ocenić maszyny wirtualne VMware pod kątem migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocenianie maszyn wirtualnych VMware](./tutorial-assess-vmware.md)
