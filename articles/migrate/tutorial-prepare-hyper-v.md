---
title: Przygotowywanie maszyn wirtualnych funkcji Hyper-V do oceny/migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych funkcji Hyper-V za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109624"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Przygotowanie do oceny i migracji maszyn wirtualnych funkcji Hyper-V na platformę Azure

Ten artykuł ułatwia przygotowanie się do oceny i migracji lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool)i [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool).


Ten samouczek jest pierwszą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne funkcji Hyper-V na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure do pracy z Azure Migrate.
> * Przygotuj się do oceny maszyn wirtualnych funkcji Hyper-V.
> * Przygotowanie do migrowania maszyn wirtualnych funkcji Hyper-V 

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Tabela zawiera podsumowanie zadań, które należy wykonać na platformie Azure. Instrukcje są zgodne z tabelą.

**Zadanie** | **Szczegóły** | **Uprawnienia**
--- | --- | ---
**Tworzenie projektu Azure Migrate** | Projekt Azure Migrate stanowi centralną lokalizację organizowania ocen i migracji oraz zarządzania nimi za pomocą narzędzi Azure Migrate, narzędzi firmy Microsoft i ofert innych firm. | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela w grupie zasobów, w której znajduje się projekt.
**Zarejestruj urządzenie** | Azure Migrate umożliwia odnajdywanie i ocenianie maszyn wirtualnych funkcji Hyper-V przy użyciu urządzenia Azure Migrate Lightweight. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration). | Aby zarejestrować urządzenie, konto platformy Azure musi mieć uprawnienia współautora lub właściciela subskrypcji platformy Azure.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia Azure Migrate tworzy aplikację Azure Active Directory (Azure AD), która jest używana do komunikacji między agentami działającymi na urządzeniu a Azure Migrate. | Twoje konto platformy Azure wymaga uprawnień do tworzenia aplikacji usługi Azure AD.
**Tworzenie maszyny wirtualnej** | Musisz mieć uprawnienia do tworzenia maszyny wirtualnej w grupie zasobów i sieci wirtualnej oraz do zapisu na dysku zarządzanym platformy Azure. | Twoje konto platformy Azure wymaga roli współautor maszyny wirtualnej.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)**.
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Przypisywanie uprawnień do tworzenia aplikacji usługi Azure AD

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

### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta, aby mieć uprawnienia do:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na dysku zarządzanym platformy Azure. 


### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

[Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Maszyny lokalne są replikowane do usługi Azure Managed Disks. Po przełączeniu w tryb failover na platformie Azure na potrzeby migracji maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do skonfigurowanej sieci platformy Azure.


## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Funkcję Hyper-V do oceny maszyn wirtualnych można przygotować ręcznie lub za pomocą skryptu konfiguracji. Oto kroki przygotowania. W przypadku przygotowania do skryptu są one konfigurowane automatycznie.

**Krok** | **Skrypt** | **Ręczne**
--- | --- | ---
**Sprawdź wymagania dotyczące hosta funkcji Hyper-V** | Skrypt sprawdza, czy na hoście jest uruchomiona obsługiwana wersja funkcji Hyper-V i rola funkcji Hyper-V.<br/><br/> Włącza usługę WinRM i otwiera porty 5985 (HTTP) i 5986 (HTTPS) na hoście (wymaganym do zbierania metadanych). | Sprawdź [wymagania dotyczące hosta funkcji Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) na potrzeby oceny serwera.<br/><br/> Upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#port-access) są otwarte na hostach funkcji Hyper-V.
**Weryfikuj wersję programu PowerShell** | Sprawdza, czy skrypt jest uruchamiany w obsługiwanej wersji programu PowerShell. | Sprawdź, czy na hoście funkcji Hyper-V jest uruchomiony program PowerShell w wersji 4,0 lub nowszej.
**Tworzenie konta** | Sprawdza, czy użytkownika (użytkownik uruchamiający skrypt) ma uprawnienia administracyjne na hoście funkcji Hyper-V.<br/><br/>  Umożliwia utworzenie konta użytkownika lokalnego (nie administratora), którego usługa Azure Migrate używa do komunikacji z hostem funkcji Hyper-V. To konto użytkownika jest dodawane do tych grup na hoście:<br/><br/> -Użytkownicy zarządzania zdalnego<br/><br/> -Administratorzy funkcji Hyper-V<br/><br/>— Użytkownicy monitora wydajności | Skonfiguruj domenę lub konto użytkownika lokalnego z uprawnieniami administratora na hostach lub klastrach funkcji Hyper-V.<br/><br/> — Wymagane jest jedno konto dla wszystkich hostów i klastrów, które mają zostać uwzględnione w odnajdywaniu.<br/><br/> — Konto może być kontem lokalnym lub w domenie. Zalecamy, aby mieć uprawnienia administratora na hostach lub klastrach funkcji Hyper-V.<br/><br/> Alternatywnie, jeśli nie chcesz przypisywać uprawnień administratora, potrzebne są następujące uprawnienia: Użytkownicy zarządzania zdalnego; Administratorzy funkcji Hyper-V; Użytkownicy monitora wydajności.
**Włącz obsługę zdalną programu PowerShell** | Włącza obsługę zdalną programu PowerShell na hoście, dzięki czemu urządzenie Azure Migrate może uruchamiać polecenia programu PowerShell na hoście przy użyciu połączenia usługi WinRM.| Aby skonfigurować program, na każdym hoście Otwórz konsolę programu PowerShell jako administrator i uruchom następujące polecenie:<br/><br/>``` Enable-PSRemoting -force ```
**Konfigurowanie usług integracji funkcji Hyper-V** | Sprawdza, czy usługi integracji funkcji Hyper-V są włączone na wszystkich maszynach wirtualnych zarządzanych przez hosta. |  [Włącz usługi integracji funkcji Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na każdej maszynie wirtualnej.<br/><br/> Jeśli korzystasz z systemu Windows Server 2003, [postępuj zgodnie z tymi instrukcjami](prepare-windows-server-2003-migration.md).
**Delegowanie poświadczeń, jeśli dyski maszyny wirtualnej znajdują się na zdalnych udziałach SMB** | Poświadczenia delegatów skryptów. | [Włącz protokół CredSSP](#enable-credssp-to-delegate-credentials) , aby delegować poświadczenia.

### <a name="run-the-script"></a>Uruchamianie skryptu

Uruchom skrypt w następujący sposób:

1. Upewnij się, że na hoście funkcji Hyper-V jest zainstalowany program PowerShell w wersji 4,0 lub nowszej.
2. Pobierz skrypt z [Centrum pobierania Microsoft](https://aka.ms/migrate/script/hyperv). Skrypt jest kryptograficznie podpisany przez firmę Microsoft.
3. Zweryfikuj integralność skryptu przy użyciu plików MD5 lub SHA256 skrótów. Wartości hasztagów są poniżej. Uruchom to polecenie, aby wygenerować skrót dla skryptu:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Przykład użycia: 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Po zweryfikowaniu integralności skryptu Uruchom skrypt na każdym hoście funkcji Hyper-V za pomocą tego polecenia programu PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Wartości hasztagów

Wartości skrótu to:

| **Skrót** | **Wartość** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Włącz uwierzytelnianie CredSSP w celu delegowania poświadczeń

Jeśli host zawiera maszyny wirtualne z dyskami, które znajdują się w udziałach SMB, wykonaj ten krok na hoście.

- To polecenie można uruchomić zdalnie na wszystkich hostach funkcji Hyper-V.
- W przypadku dodania nowych węzłów hosta do klastra są one automatycznie dodawane do odnajdywania, ale w razie potrzeby należy ręcznie włączyć protokół CredSSP w nowych węzłach.

Włącz w następujący sposób:

1. Zidentyfikuj hosty funkcji Hyper-V z uruchomionymi maszynami wirtualnymi funkcji Hyper-V z dyskami w udziałach SMB.
2. Uruchom następujące polecenie na każdym zidentyfikowanym hoście funkcji Hyper-V:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Po skonfigurowaniu urządzenia należy zakończyć konfigurowanie dostawcy CredSSP, [włączając je na urządzeniu](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Jest to opisane w następnym samouczku w tej serii.


## <a name="prepare-for-appliance-deployment"></a>Przygotowanie do wdrożenia urządzenia

Przed skonfigurowaniem urządzenia Azure Migrate i rozpoczęciem oceny w następnym samouczku Przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---hyper-v) wymagania dotyczące urządzeń.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych](migrate-appliance.md#government-cloud-urls) . Jeśli używasz zapory lub serwera proxy opartego na adresie URL, upewnij się, że zezwala na dostęp do wymaganych adresów URL.
3. Przejrzyj dane, które urządzenie będzie zbierać podczas odnajdywania i oceny.
4. [Przejrzyj](migrate-appliance.md#collected-data---hyper-v) wymagania dotyczące dostępu do portów dla urządzenia.


## <a name="prepare-for-hyper-v-migration"></a>Przygotowanie do migracji funkcji Hyper-V

1. [Przegląd](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Wymagania hosta funkcji Hyper-V dotyczące migracji oraz adresy URL platformy Azure, do których hosty i klastry funkcji Hyper-V potrzebują dostępu do migracji maszyn wirtualnych.
2. [Zapoznaj](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) się z wymaganiami dotyczącymi maszyn wirtualnych funkcji Hyper-V, które chcesz zmigrować na platformę Azure.
3. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
    - Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.



## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj uprawnienia konta platformy Azure.
> * Przygotowane hosty i maszyny wirtualne funkcji Hyper-V na potrzeby oceny i migracji.
> * Przygotowano do wdrożenia urządzenia Azure Migrateowego.

Przejdź do następnego samouczka, aby utworzyć projekt Azure Migrate, wdrożyć urządzenie oraz odnajdywać i oceniać maszyny wirtualne funkcji Hyper-V na potrzeby migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocenianie maszyn wirtualnych funkcji Hyper-V](./tutorial-assess-hyper-v.md)
