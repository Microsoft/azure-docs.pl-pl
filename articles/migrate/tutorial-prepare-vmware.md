---
title: Przygotowywanie maszyn wirtualnych VMware do oceny/migracji za pomocą usługi Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych VMware za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677300"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Przygotowywanie maszyny wirtualnej środowiska VMware do oceny i migracji na platformę Azure

Ten artykuł ułatwia przygotowanie do oceny i/lub migracji lokalnych maszyn wirtualnych VMware na platformę Azure przy użyciu [usługi Azure Migrate.](migrate-services-overview.md)



Ten samouczek jest pierwszym z serii, który pokazuje, jak oceniać i migrować maszyny wirtualne VMware. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z programem Azure Migrate.
> * Przygotuj VMware do oceny maszyn wirtualnych za pomocą narzędzia Azure Migrate:Server Assessment.
> * Przygotuj VMware do migracji maszyn wirtualnych za pomocą narzędzia Migracji:Migracja serwera platformy Azure. 

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Są one przydatne, gdy dowiesz się, jak skonfigurować wdrożenie i jako szybki dowód koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Te uprawnienia są potrzebne do tych zadań na platformie Azure, zanim będzie można ocenić lub przeprowadzić migrację maszyn wirtualnych VMware.

**Zadanie** | **Szczegóły** 
--- | --- 
**Tworzenie projektu migracji platformy Azure** | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela, aby utworzyć projekt. 
**Rejestrowanie dostawców zasobów** | Usługa Azure Migrate używa lekkiego urządzenia migracji platformy Azure do odnajdywania i oceny maszyn wirtualnych vmware oraz do migracji ich na platformę Azure za pomocą usługi Azure Migrate:Server Assessment.<br/><br/> Podczas rejestracji urządzenia dostawcy zasobów są rejestrowani w subskrypcji wybranej w urządzeniu. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Aby zarejestrować dostawców zasobów, potrzebujesz roli współautora lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia usługa Azure Migrate tworzy aplikacje usługi Azure Active Directory (Azure AD). <br/><br/> — pierwsza aplikacja jest używana do komunikacji między agentami działającymi na urządzeniu a ich odpowiednimi usługami uruchomionymi na platformie Azure.<br/><br/> - Druga aplikacja jest używana wyłącznie do dostępu KeyVault utworzone w subskrypcji użytkownika dla bezagentowej migracji VMware VM. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potrzebne są uprawnienia do tworzenia aplikacji usługi Azure AD (dostępnych w roli dewelopera aplikacji).
**Tworzenie magazynu kluczy** | Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji bez agenta, usługa Azure Migrate tworzy magazyn kluczy do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji.<br/><br/> Aby utworzyć magazyn, potrzebujesz uprawnień przypisania ról do grupy zasobów, w której znajduje się projekt migracji platformy Azure.




### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Powinieneś mieć uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.

### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia

Aby zarejestrować urządzenie, należy przypisać uprawnienia do migracji platformy Azure do tworzenia aplikacji usługi Azure AD podczas rejestracji urządzenia. Uprawnienia można przypisać przy użyciu jednej z następujących metod:

- **Udziel uprawnień:** dzierżawca/administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
- **Przypisz rolę dewelopera aplikacji:** Dzierżawca/administrator globalny może przypisać rolę dewelopera aplikacji (która ma uprawnienia) do konta.

> [!NOTE]
> - Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż te opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestracji nowego urządzenia. Uprawnienia można usunąć po skonfigurowaniu urządzenia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Jeśli chcesz, aby dzierżawca/administrator globalny udzielał uprawnień, wykonaj następujące czynności:

1. W usłudze Azure AD administrator dzierżawy/globalnej powinien przejść do**ustawień użytkownika****użytkowników** > usługi Azure **Active Directory** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **Tak**. Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Przypisywanie roli dewelopera aplikacji

Alternatywnie dzierżawca/administrator globalny można przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) o przypisywaniu roli.

### <a name="assign-permissions-to-create-a-key-vault"></a>Przypisywanie uprawnień do tworzenia magazynu kluczy

Aby włączyć usługę Azure Migrate w celu utworzenia magazynu kluczy, należy przypisać uprawnienia w następujący sposób:

1. W grupie zasobów w witrynie Azure portal wybierz pozycję **Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.

    - Aby uruchomić ocenę serwera, uprawnienia **współautora** są wystarczające.
    - Aby uruchomić migrację serwera bez agenta, należy mieć uprawnienia **Właściciel** (lub Administrator dostępu **współautora** i **użytkownika).**

3. Jeśli nie masz wymaganych uprawnień, poproś o nie właściciela grupy zasobów.



## <a name="prepare-for-vmware-vm-assessment"></a>Przygotowanie do oceny VMware VM

Aby przygotować się do oceny VMware VM, należy:

- **Sprawdź ustawienia VMware**. Upewnij się, że serwer vCenter i maszyny wirtualne, które chcesz przeprowadzić migrację, spełniają wymagania.
- **Skonfiguruj konto do oceny**. Usługa Azure Migrate używa tego konta do uzyskiwania dostępu do serwera vCenter Server, aby odnajdować maszyny wirtualne do oceny.
- **Sprawdź wymagania urządzenia**. Przed wdrożeniem należy zweryfikować wymagania dotyczące wdrażania urządzenia migracji platformy Azure.

### <a name="verify-vmware-settings"></a>Weryfikowanie ustawień VMware

1. [Sprawdź](migrate-support-matrix-vmware.md#vmware-requirements) Wymagania serwera VMware do oceny.
2. [Upewnij się,](migrate-support-matrix-vmware.md#port-access) że potrzebne porty są otwarte na serwerze vCenter.
3. Na serwerze vCenter Server upewnij się, że twoje konto ma uprawnienia do tworzenia maszyny Wirtualnej przy użyciu pliku OVA. Urządzenie migracji platformy Azure można wdrożyć jako maszynę wirtualną VMware przy użyciu pliku OVA.


### <a name="set-up-an-account-for-assessment"></a>Konfigurowanie konta do oceny

Usługa Azure Migrate musi uzyskać dostęp do serwera vCenter, aby odnajdować maszyny wirtualne do oceny i migracji bez agenta.

- Jeśli planujesz odnajdowanie aplikacji lub wizualizowanie zależności w sposób bezagentowy, utwórz konto serwera vCenter Server z dostępem tylko do odczytu wraz z włączonymi uprawnieniami dla **operacji gościa maszyn** > **wirtualnych**.

  ![Uprawnienia konta serwera vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Jeśli nie planujesz odnajdywania aplikacji i wizualizacji zależności bez agenta, skonfiguruj konto tylko do odczytu dla serwera vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Weryfikowanie ustawień urządzenia w celu oceny

Przed skonfigurowaniem urządzenia migracji platformy Azure i rozpoczęciem oceny w następnym samouczku przygotuj się do wdrożenia urządzenia.

1. [Weryfikacja](migrate-appliance.md#appliance---vmware) Wymagania dotyczące urządzenia migracji platformy Azure.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)
3. [Przejrzyj dane,](migrate-appliance.md#collected-data---vmware) które urządzenie zbiera podczas odnajdowania i oceny.
4. [Uwaga](migrate-support-matrix-vmware.md#port-access) wymagania dostępu do portu dla urządzenia.




## <a name="prepare-for-agentless-vmware-migration"></a>Przygotuj się na bezagentową migrację VMware

Przejrzyj wymagania dotyczące [bezagentowej migracji](server-migrate-overview.md) maszyn wirtualnych VMware.

1. [Weryfikacja](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Wymagania dotyczące serwera VMware.
2. [Przejrzyj uprawnienia,](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) które usługa Azure Migrate musi uzyskać dostęp do serwera vCenter Server.
3. [Weryfikacja](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Wymagania dotyczące maszyn wirtualnych VMware.
4. [Przejrzyj](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) wymagania dotyczące urządzenia migracji platformy Azure.
5. Zwróć uwagę na dostęp do adresów URL wymaganych dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)
6. Przejrzyj wymagania dotyczące [dostępu do portów.](migrate-support-matrix-vmware-migration.md#agentless-ports)

## <a name="prepare-for-agent-based-vmware-migration"></a>Przygotowanie do migracji VMware opartej na agentach

Przejrzyj wymagania dotyczące migracji maszyn wirtualnych [opartych na agentach.](server-migrate-overview.md)

1. [Weryfikacja](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Wymagania dotyczące serwera VMware.
2. [Przejrzyj uprawnienia](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Usługa Azure Migrate musi uzyskać dostęp do serwera vCenter Server.
2. [Weryfikacja](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Wymagania dotyczące maszyn wirtualnych VMware, w tym instalacja usługi mobilności na każdej maszynie wirtualnej, którą chcesz przeprowadzić migrację.
3. Migracja oparta na agentach używa urządzenia replikacji:
    - [Przejrzyj](migrate-replication-appliance.md#appliance-requirements) wymagania dotyczące wdrażania urządzenia replikacji.
    - [Przejrzyj opcje](migrate-replication-appliance.md#mysql-installation) instalacji MySQL na urządzeniu.
    - Zwróć uwagę na dostęp do adresów URL wymaganych dla chmur [publicznych](migrate-replication-appliance.md#url-access) i [rządowych.](migrate-replication-appliance.md#azure-government-url-access)
    - Przejrzyj wymagania [dotyczące dostępu do portów](migrate-replication-appliance.md#port-access) dla urządzenia replikacji.
    
## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie uprawnień platformy Azure.
> * Przygotowane VMware do oceny i migracji.


Przejdź do drugiego samouczka, aby skonfigurować projekt migracji platformy Azure i ocenić maszyny wirtualne VMware do migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocena maszyn wirtualnych VMware](./tutorial-assess-vmware.md)
