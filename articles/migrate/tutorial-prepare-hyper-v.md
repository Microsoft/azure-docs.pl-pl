---
title: Przygotowywanie maszyn wirtualnych funkcji Hyper V do oceny/migracji za pomocą usługi Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji maszyn wirtualnych funkcji Hyper-V za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: e86e634812e9f2782a003a11b48cfecf91d95359
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535302"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Przygotowanie do oceny i migracji maszyn wirtualnych z programem Hyper-V na platformę Azure

W tym artykule opisano sposób przygotowania do oceny lokalnych maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia Azure Migrate:Server Assessment(migrate-services-overview.md#azure-migrate-server-assessment-tool) oraz migracji maszyn wirtualnych z funkcji Hyper-V za pomocą [usługi Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).


Ten samouczek jest pierwszym z serii, który pokazuje, jak ocenić i migrować maszyny wirtualne funkcji Hyper-V na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia do konta platformy Azure i zasoby do pracy z programem Azure Migrate.
> * Przygotuj lokalne hosty funkcji Hyper-V i maszyny wirtualne do oceny serwera. Można przygotować za pomocą skryptu konfiguracji lub ręcznie.
> * Przygotuj się do wdrożenia urządzenia migracji platformy Azure. Urządzenie służy do odnajdywanie i ocena lokalnych maszyn wirtualnych.
> * Przygotowanie lokalnych hostów funkcji Hyper-V i maszyn wirtualnych do migracji serwera.


> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami dotyczącymi oceny i migracji funkcji Hyper-V.


Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Należy skonfigurować uprawnienia do wdrażania migracji platformy Azure.

**Zadanie** | **Szczegóły** 
--- | --- 
**Tworzenie projektu migracji platformy Azure** | Twoje konto platformy Azure wymaga uprawnień programu Contributer lub Owner do utworzenia projektu. | 
**Rejestrowanie dostawców zasobów** | Usługa Azure Migrate używa lekkiego urządzenia migracji platformy Azure do odnajdywania i oceny maszyn wirtualnych funkcji Hyper-V za pomocą oceny serwera migracji platformy Azure.<br/><br/> Podczas rejestracji urządzenia dostawcy zasobów są rejestrowani w subskrypcji wybranej w urządzeniu. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Aby zarejestrować dostawców zasobów, potrzebujesz roli współautora lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia usługa Azure Migrate tworzy aplikację usługi Azure Active Directory (Azure AD), która jest używana do komunikacji między agentami działającymi na urządzeniu z odpowiednimi usługami uruchomionymi na platformie Azure. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potrzebne są uprawnienia do tworzenia aplikacji usługi Azure AD (dostępnych w roli dewelopera aplikacji).



### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu migracji platformy Azure.

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Powinieneś mieć uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia

Można przypisać uprawnienia do migracji platformy Azure, aby utworzyć aplikację usługi Azure AD podczas rejestracji urządzenia, przy użyciu jednej z następujących metod:

- Dzierżawca/administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
- Dzierżawca/administrator globalny może przypisać rolę dewelopera aplikacji (która ma uprawnienia) do konta.

> [!NOTE]
> - Aplikacja nie ma żadnych innych uprawnień dostępu do subskrypcji innych niż te opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestracji nowego urządzenia. Uprawnienia można usunąć po skonfigurowaniu urządzenia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawca/administrator globalny może udzielić uprawnień w następujący sposób:

1. W usłudze Azure AD administrator dzierżawy/globalnej powinien przejść do**ustawień użytkownika****użytkowników** > usługi Azure **Active Directory** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **Tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Przypisywanie roli dewelopera aplikacji

Dzierżawca/administrator globalny można przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Przygotowanie funkcji Hyper-V do oceny

Funkcji Hyper-V można przygotować dla oceny maszyny Wirtualnej ręcznie lub przy użyciu skryptu konfiguracji. Etapy przygotowania są następujące:
- [Weryfikacja](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Ustawienia hosta funkcji Hyper-V i upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#port-access) są otwarte na hostach funkcji Hyper-V.
- Konfigurowanie komunikacji zdalnej programu PowerShell na każdym hoście, aby urządzenie migracji platformy Azure można uruchamiać polecenia programu PowerShell na hoście za pomocą połączenia WinRM.
- Delegow poświadczenie, jeśli dyski maszyn wirtualnych znajdują się w zdalnych udziałach SMB.
- Skonfiguruj konto, którego urządzenie będzie używać do odnajdywanie maszyn wirtualnych na hostach funkcji Hyper-V.
- Skonfiguruj usługi integracji funkcji Hyper-V na każdej maszynie wirtualnej, którą chcesz odnajdować i ocenić. Ustawienia domyślne po włączeniu usług integracji są wystarczające dla migracji platformy Azure.

    ![Włączanie usług integracyjnych](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Przygotowywanie za pomocą skryptu

Skrypt wykonuje następujące czynności:

- Sprawdza, czy skrypt jest uruchomiony w obsługiwanej wersji programu PowerShell.
- Sprawdza, czy użytkownik (użytkownik uruchamiacy skrypt) ma uprawnienia administracyjne na hoście funkcji Hyper-V.
- Umożliwia utworzenie konta użytkownika lokalnego (nie administratora), którego usługa Azure Migrate używa do komunikowania się z hostem funkcji Hyper-V. To konto użytkownika jest dodawane do tych grup na hoście:
    - Użytkownicy zarządzania zdalnego
    - Administratorzy funkcji Hyper-V
    - Użytkownicy monitora wydajności
- Sprawdza, czy na hoście jest uruchomiona obsługiwana wersja funkcji Hyper-V i roli funkcji Hyper-V.
- Włącza usługę WinRM i otwiera porty 5985 (HTTP) i 5986 (HTTPS) na hoście (wymagane do zbierania metadanych).
- Włącza tworzenie komunikacji zdalnej programu PowerShell na hoście.
- Sprawdza, czy usługi integracji funkcji Hyper-V są włączone na wszystkich maszynach wirtualnych zarządzanych przez hosta.
- W razie potrzeby włącza credssp na hoście.

Uruchom skrypt w następujący sposób:

1. Upewnij się, że na hoście funkcji Hyper-V jest zainstalowany program PowerShell w wersji 4.0 lub nowszej.
2. Pobierz skrypt z [Centrum pobierania Microsoft](https://aka.ms/migrate/script/hyperv). Skrypt jest podpisany kryptograficznie przez firmę Microsoft.
3. Sprawdź poprawność integralności skryptu przy użyciu plików skrótów MD5 lub SHA256. Wartości hashtagów znajdują się poniżej. Uruchom to polecenie, aby wygenerować skrót dla skryptu:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Przykład użycia: 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    Po sprawdzeniu poprawności integralności skryptu uruchom skrypt na każdym hoście funkcji Hyper-V za pomocą tego polecenia programu PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Wartości hashtagów

Wartości skrótu to:

| **Mieszania** | **Wartość** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256 ( SHA256 )** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Przygotowywanie ręcznie

Postępuj zgodnie z procedurami opisanymi w tej sekcji, aby ręcznie przygotować funkcji Hyper-V, zamiast używać skryptu.

### <a name="verify-powershell-version"></a>Weryfikowanie wersji programu PowerShell

Upewnij się, że na hoście funkcji Hyper-V jest zainstalowany program PowerShell w wersji 4.0 lub nowszej.



### <a name="set-up-an-account-for-vm-discovery"></a>Konfigurowanie konta do odnajdowania maszyn wirtualnych

Usługa Azure Migrate potrzebuje uprawnień do odnajdywać lokalne maszyny wirtualne.

- Skonfiguruj domenę lub lokalne konto użytkownika z uprawnieniami administratora do hostów/klastra funkcji Hyper-V.

    - Potrzebujesz jednego konta dla wszystkich hostów i klastrów, które chcesz uwzględnić w odnajdowaniu.
    - Konto może być kontem lokalnym lub kontem domeny. Firma Microsoft zaleca, aby miał uprawnienia administratora do hostów lub klastrów funkcji Hyper-V.
    - Alternatywnie, jeśli nie chcesz przypisywać uprawnień administratora, potrzebne są następujące uprawnienia:
        - Użytkownicy zarządzania zdalnego
        - Administratorzy funkcji Hyper-V
        - Użytkownicy monitora wydajności

### <a name="verify-hyper-v-host-settings"></a>Weryfikowanie ustawień hosta funkcji Hyper-V

1. Sprawdź [wymagania hosta funkcji Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) do oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-hyper-v.md#port-access) są otwarte na hostach funkcji Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Włączanie komunikacji zdalnej programu PowerShell na hostach

Konfigurowanie komunikacji zdalnej programu PowerShell na każdym hoście w następujący sposób:

1. Na każdym hoście otwórz konsolę programu PowerShell jako administrator.
2. Uruchom następujące polecenie:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Włączanie usług integracji na maszynach wirtualnych

Integration Services powinny być włączone na każdej maszynie wirtualnej, tak aby usługa Azure Migrate może przechwytywać informacje o systemie operacyjnym na maszynie wirtualnej.

Na maszynach wirtualnych, które chcesz odnajdywać i oceniać, włącz [usługi integracji funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na każdej maszynie wirtualnej.


### <a name="enable-credssp-on-hosts"></a>Włączanie credssp na hostach

Jeśli host ma maszyny wirtualne z dyskami znajdują się na udziałach SMB, wykonaj ten krok na hoście.

- To polecenie można uruchomić zdalnie na wszystkich hostach funkcji Hyper-V.
- Jeśli dodasz nowe węzły hosta w klastrze, są one automatycznie dodawane do odnajdowania, ale w razie potrzeby należy ręcznie włączyć credssp w nowych węzłach.

Włącz w następujący sposób:

1. Identyfikowanie hostów funkcji Hyper-V z uruchomionymi maszynami wirtualnymi z dyskami w udziałach SMB.
2. Uruchom następujące polecenie na każdym zidentyfikowanym hoście funkcji Hyper-V:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Po skonfigurowaniu urządzenia można zakończyć konfigurowanie programu CredSSP, [włączając go w urządzeniu](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Jest to opisane w następnym samouczku w tej serii.


## <a name="prepare-for-appliance-deployment"></a>Przygotowanie do wdrożenia urządzeń

Przed skonfigurowaniem urządzenia migracji platformy Azure i rozpoczęciem oceny w następnym samouczku przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---hyper-v) wymagania urządzenia.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls) Jeśli używasz zapory lub serwera proxy opartego na adresach URL, upewnij się, że umożliwia dostęp do wymaganych adresów URL.
3. Przejrzyj dane, które urządzenie będzie zbierać podczas odnajdowania i oceny.
4. [Przejrzyj](migrate-appliance.md#collected-data---hyper-v) wymagania dotyczące dostępu do portu dla urządzenia.




## <a name="prepare-for-hyper-v-migration"></a>Przygotowanie do migracji funkcji Hyper-V

1. [Weryfikacja](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Wymagania dotyczące hosta funkcji Hyper-V dotyczące migracji oraz adresy URL platformy Azure, do których hosty i klastry funkcji Hyper-V potrzebują dostępu do migracji maszyn wirtualnych.
2. [Przejrzyj](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) wymagania dotyczące maszyn wirtualnych funkcji Hyper-V, które chcesz przeprowadzić migrację na platformę Azure.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie uprawnień konta platformy Azure.
> * Przygotowane hosty funkcji Hyper-V i maszyny wirtualne do oceny i migracji.
> * Przygotowane do wdrożenia urządzenia migracji platformy Azure.

Przejdź do następnego samouczka, aby utworzyć projekt migracji platformy Azure, wdrożyć urządzenie oraz odnajdować i ocenić maszyny wirtualne funkcji Hyper-V w celu migracji na platformę Azure.

> [!div class="nextstepaction"]
> [Ocena maszyn wirtualnych funkcji Hyper V](./tutorial-assess-hyper-v.md)
