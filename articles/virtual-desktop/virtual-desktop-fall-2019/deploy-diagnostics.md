---
title: Wdrażanie narzędzia diagnostycznego dla pulpitu wirtualnego systemu Windows (klasyczny) — Azure
description: Jak wdrożyć narzędzie do diagnostyki środowiska użytkownika dla pulpitu wirtualnego systemu Windows (klasyczny).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 729e22f8ad94d2119d0f3f3e9fc474cc83a493a8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023075"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Wdrażanie narzędzia diagnostycznego pulpitu wirtualnego systemu Windows (klasycznego)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

>[!IMPORTANT]
>Od 16 marca 2020, tymczasowo wyłączono zapytania diagnostyczne, które mają wpływ na środowisko użytkownika ze względu na zwiększone zapotrzebowanie na usługę. Spowoduje to, że narzędzie przestanie działać, ponieważ polega na tym, że te zapytania działają. Po ponownym udostępnieniu zapytań diagnostycznych będziemy aktualizować ten artykuł.
>
>Do tego czasu zdecydowanie zalecamy [użycie log Analytics](diagnostics-log-analytics-2019.md) do ciągłego monitorowania.

Oto co można zrobić za pomocą narzędzia diagnostycznego dla pulpitu wirtualnego systemu Windows:

- Wyszukiwanie działań diagnostycznych (zarządzania, połączenia lub źródła danych) dla pojedynczego użytkownika w okresie jednego tygodnia.
- Zbierz informacje o hoście sesji dla działań związanych z nawiązywaniem połączenia z obszaru roboczego Log Analytics.
- Przejrzyj szczegóły wydajności maszyny wirtualnej (VM) dla określonego hosta.
- Zobacz, którzy użytkownicy są zalogowani na hoście sesji.
- Wyślij wiadomość do aktywnych użytkowników na określonym hoście sesji.
- Podpisz użytkowników z hosta sesji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć szablon Azure Resource Manager dla narzędzia, należy utworzyć Azure Active Directory rejestracji aplikacji i Log Analytics obszar roboczy. Użytkownik lub administrator musi mieć następujące uprawnienia, aby:

- Właściciel subskrypcji platformy Azure
- Uprawnienie do tworzenia zasobów w ramach subskrypcji platformy Azure
- Uprawnienie do tworzenia aplikacji usługi Azure AD
- Prawa właściciela lub współautora usług pulpitu zdalnego

Przed rozpoczęciem pracy należy również zainstalować te dwa moduły programu PowerShell:

- [Moduł programu Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Moduł usługi Azure AD](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Upewnij się, że Twój identyfikator subskrypcji jest gotowy do momentu zalogowania się.

Gdy wszystko będzie w porządku, możesz utworzyć rejestrację aplikacji usługi Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Tworzenie Azure Active Directory rejestracji aplikacji

W tej sekcji przedstawiono sposób użycia programu PowerShell do tworzenia aplikacji Azure Active Directory przy użyciu nazwy głównej usługi i uzyskiwania dla niej uprawnień interfejsu API.

>[!NOTE]
>Uprawnienia interfejsu API to pulpity wirtualne systemu Windows, Log Analytics i Microsoft Graph uprawnienia interfejsu API są dodawane do aplikacji Azure Active Directory.

1. Uruchom program Windows PowerShell jako administrator.
2. Zaloguj się do platformy Azure przy użyciu konta, które ma uprawnienia właściciela lub współautora w ramach subskrypcji platformy Azure, której chcesz użyć dla narzędzia diagnostycznego:
   ```powershell
   Login-AzAccount
   ```
3. Zaloguj się do usługi Azure AD przy użyciu tego samego konta:
   ```powershell
   Connect-AzureAD
   ```
4. Przejdź do [repozytorium RDS-templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) i uruchom skrypt **CreateADAppRegistrationforDiagnostics.ps1** w programie PowerShell.
5.  Gdy skrypt prosi o podanie nazwy aplikacji, wprowadź unikatową nazwę aplikacji.


Po pomyślnym uruchomieniu skryptu powinny one zawierać następujące elementy w jego danych wyjściowych:

-  Komunikat potwierdzający, że aplikacja ma teraz przypisanie roli głównej usługi.
-  Identyfikator klienta i klucz tajny klienta, które będą potrzebne do wdrożenia narzędzia diagnostycznego.

Po zarejestrowaniu aplikacji należy skonfigurować obszar roboczy Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego Log Analytics

Aby uzyskać najlepsze możliwe środowisko, zalecamy skonfigurowanie obszaru roboczego Log Analytics z następującymi licznikami wydajności, które umożliwiają uzyskanie instrukcji dotyczących środowiska użytkownika w sesji zdalnej. Listę zalecanych liczników z sugerowanymi progami można znaleźć w temacie [progi licznika wydajności systemu Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Tworzenie obszaru roboczego usługi Azure Log Analytics przy użyciu programu PowerShell

Można uruchomić skrypt programu PowerShell, aby utworzyć obszar roboczy Log Analytics i skonfigurować zalecane liczniki wydajności systemu Windows do monitorowania środowiska użytkownika i wydajności aplikacji.

>[!NOTE]
>Jeśli masz już istniejący Log Analytics obszar roboczy, który został utworzony bez skryptu programu PowerShell, którego chcesz użyć, przejdź z wyprzedzeniem, aby [sprawdzić poprawność wyników skryptu w Azure Portal](#validate-the-script-results-in-the-azure-portal).

Aby uruchomić skrypt programu PowerShell:

1.  Otwórz program PowerShell jako administrator.
2.  Przejdź do [repozytorium RDS-templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) i uruchom skrypt **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** w programie PowerShell.
3. Wprowadź następujące wartości parametrów:

    - W polu **ResourceGroupName** wprowadź nazwę grupy zasobów.
    - W przypadku **LogAnalyticsWorkspaceName** wprowadź unikatową nazwę obszaru roboczego log Analytics.
    - W polu **Lokalizacja** wprowadź region platformy Azure, którego używasz.
    - Wprowadź **Identyfikator subskrypcji platformy Azure**, który można znaleźć w Azure Portal w obszarze **subskrypcje**.

4. Wprowadź poświadczenia użytkownika z dostępem delegowanego administratora.
5. Zaloguj się do Azure Portal przy użyciu poświadczeń tego samego użytkownika.
6. Zapisz lub znają identyfikator LogAnalyticsWorkspace w późniejszym czasie.
7. W przypadku skonfigurowania obszaru roboczego Log Analytics przy użyciu skryptu programu PowerShell, liczniki wydajności powinny już być skonfigurowane i można przejść z wyprzedzeniem, aby [sprawdzić poprawność wyników skryptu w Azure Portal](#validate-the-script-results-in-the-azure-portal). W przeciwnym razie przejdź do następnej sekcji.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurowanie liczników wydajności systemu Windows w istniejącym obszarze roboczym Log Analytics

Ta sekcja jest przeznaczony dla użytkowników, którzy chcą korzystać z istniejącego obszaru roboczego Log Analytics platformy Azure utworzonego bez skryptu programu PowerShell w poprzedniej sekcji. Jeśli skrypt nie został użyty, należy ręcznie skonfigurować zalecane liczniki wydajności systemu Windows.

Poniżej przedstawiono sposób ręcznego konfigurowania zalecanych liczników wydajności:

1. Otwórz przeglądarkę internetową i zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administracyjnego.
2. Następnie przejdź do **obszaru roboczego log Analytics** , aby przejrzeć skonfigurowane liczniki wydajności systemu Windows.
3. W sekcji **Ustawienia** wybierz pozycję  **Ustawienia zaawansowane**.
4. Następnie przejdź do **danych**  >  **liczników wydajności systemu Windows** i Dodaj następujące liczniki:

    -   Dysk logiczny ( \* ): \\ wolne miejsce (%)
    -   Dysk logiczny (C:) \\ średnia długość kolejki dysku
    -   Pamięć ( \* ) \\ dostępne MB
    -   Informacje o procesorze ( \* ) \\ czas procesora
    -   Opóźnienie wejściowe użytkownika na sesję ( \* ) \\ maksymalne opóźnienie wejściowe

Dowiedz się więcej o licznikach wydajności w [źródłach danych wydajności systemu Windows i Linux w Azure monitor](../../azure-monitor/platform/data-sources-performance-counters.md).

>[!NOTE]
>Wszystkie skonfigurowane dodatkowe liczniki nie będą wyświetlane w samym narzędziu diagnostyki. Aby pojawił się w narzędziu diagnostycznym, należy skonfigurować plik konfiguracyjny narzędzia. Instrukcje dotyczące sposobu wykonania tej czynności z zaawansowaną administracją będą dostępne w serwisie GitHub w późniejszym terminie.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Sprawdza poprawność skryptu w Azure Portal

Przed kontynuowaniem wdrażania narzędzia diagnostycznego zalecamy sprawdzenie, czy aplikacja Azure Active Directory ma uprawnienia interfejsu API, a obszar roboczy Log Analytics ma wstępnie skonfigurowane liczniki wydajności systemu Windows.

### <a name="review-your-app-registration"></a>Przejrzyj rejestrację aplikacji

Aby upewnić się, że rejestracja aplikacji ma uprawnienia interfejsu API:

1. Otwórz przeglądarkę i Połącz się z [Azure Portal](https://portal.azure.com/) przy użyciu konta administracyjnego.
2. Przejdź do **Azure Active Directory**.
3. Przejdź do **rejestracje aplikacji** i wybierz pozycję **wszystkie aplikacje**.
4. Wyszukaj rejestrację aplikacji usługi Azure AD o tej samej nazwie aplikacji wprowadzonej w kroku 5 [Tworzenie rejestracji aplikacji Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Przeglądanie obszaru roboczego Log Analytics

Aby upewnić się, że obszar roboczy Log Analytics ma wstępnie skonfigurowane liczniki wydajności systemu Windows:

1. W [Azure Portal](https://portal.azure.com/)przejdź do **obszaru log Analytics obszary robocze** , aby przejrzeć skonfigurowane liczniki wydajności systemu Windows.
2. W obszarze **Ustawienia** wybierz pozycję **Ustawienia zaawansowane**.
3. Następnie przejdź do pozycji **dane**  >  **liczniki wydajności systemu Windows**.
4. Upewnij się, że następujące liczniki są wstępnie skonfigurowane:

   - Dysk logiczny ( \* ) \\ % wolnego miejsca: Wyświetla ilość wolnego miejsca na dysku w postaci wartości procentowej.
   - Dysk logiczny (C:) \\ średnia długość kolejki dysku: długość żądania transferu dysku dla dysku C. Wartość nie może przekroczyć 2 przez dłuższy czas.
   - Pamięć ( \* ) \\ dostępna pamięć (MB): ilość dostępnej pamięci dla systemu w megabajtach.
   - Informacje o procesorze ( \* ) \\ czas procesora: wyrażony w procentach czas, przez jaki procesor zużywa nieczynny wątek.
   - Opóźnienie wejściowe użytkownika na sesję ( \* ) \\ maksymalne opóźnienie wejściowe

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Nawiązywanie połączenia z maszynami wirtualnymi w obszarze roboczym Log Analytics

Aby można było wyświetlić kondycję maszyn wirtualnych, należy włączyć Log Analytics połączenie. Wykonaj następujące kroki, aby połączyć maszyny wirtualne:

1. Otwórz przeglądarkę i zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administracyjnego.
2. Przejdź do obszaru roboczego Log Analytics.
3. W lewym panelu w obszarze źródła danych obszaru roboczego wybierz pozycję **maszyny wirtualne**.
4. Wybierz nazwę maszyny wirtualnej, z którą chcesz nawiązać połączenie.
5. Wybierz pozycję **Połącz**.

## <a name="deploy-the-diagnostics-tool"></a>Wdrażanie narzędzia diagnostycznego

Aby wdrożyć szablon zarządzania zasobami platformy Azure dla narzędzia Diagnostyka:

1.  Przejdź do [strony usługi GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Wdróż szablon na platformie Azure i postępuj zgodnie z instrukcjami w szablonie. Upewnij się, że masz dostępne następujące informacje:

    -   Client-Id
    -   Client-Secret
    -   Identyfikator obszaru roboczego usługi Log Analytics

3.  Po podaniu parametrów wejściowych Zaakceptuj warunki i postanowienia, a następnie wybierz pozycję **Kup**.

Wdrożenie zajmie 2 – 3 minuty. Po pomyślnym wdrożeniu przejdź do grupy zasobów i upewnij się, że zasoby aplikacji sieci Web i planu usługi App Service są tam dostępne.

Po wybraniu tej opcji należy ustawić identyfikator URI przekierowania.

### <a name="set-the-redirect-uri"></a>Ustaw identyfikator URI przekierowania

Aby ustawić identyfikator URI przekierowania:

1.  W [Azure Portal](https://portal.azure.com/)przejdź do pozycji **App Services** i Znajdź utworzoną aplikację.
2.  Przejdź do strony przegląd i skopiuj adres URL, który znajdziesz.
3.  Przejdź do obszaru **rejestracje aplikacji** i wybierz aplikację, którą chcesz wdrożyć.
4.  W lewym panelu w obszarze Zarządzaj wybierz pozycję **uwierzytelnianie**.
5.  Wprowadź żądany identyfikator URI przekierowania do pola tekstowego **Identyfikator URI przekierowania** , a następnie wybierz pozycję **Zapisz** w lewym górnym rogu menu.
6. W menu rozwijanym w obszarze Typ wybierz pozycję **Sieć Web** .
7. Wprowadź adres URL na stronie Przegląd aplikacji i Dodaj **/Security/SignIn-callback** na końcu. Na przykład: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![Strona URI przekierowania](../media/redirect-uri-page.png)

8. Teraz przejdź do zasobów platformy Azure, wybierz zasób App Services platformy Azure o nazwie podanej w szablonie i przejdź do adresu URL skojarzonego z nim. (Na przykład jeśli nazwa aplikacji użyta w szablonie to `contosoapp45` , wówczas skojarzony adres URL to <http://contoso.azurewebsites.net> ).
9. Zaloguj się przy użyciu odpowiedniego konta użytkownika Azure Active Directory.
10.   Wybierz pozycję **Zaakceptuj**.

## <a name="distribute-the-diagnostics-tool"></a>Dystrybucja narzędzia diagnostycznego

Przed udostępnieniem narzędzi diagnostycznych użytkownikom upewnij się, że mają one następujące uprawnienia:

- Użytkownicy potrzebują dostępu do odczytu usługi log Analytics. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../../azure-monitor/platform/roles-permissions-security.md).
-  Użytkownicy muszą również mieć dostęp do odczytu dla dzierżawy pulpitu wirtualnego systemu Windows (rola czytnika usług pulpitu zdalnego). Aby uzyskać więcej informacji, zobacz [delegowany dostęp w programie Virtual Desktop systemu Windows](delegated-access-virtual-desktop-2019.md).

Należy również nadać użytkownikom następujące informacje:

- Adres URL aplikacji
- Nazwy grupy dzierżawców, do których mogą uzyskać dostęp.

## <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego

Po zalogowaniu się do konta przy użyciu informacji otrzymanych od organizacji należy mieć nazwę UPN gotową dla użytkownika, dla którego mają być wykonywane zapytania. Wyszukiwanie spowoduje udostępnienie wszystkich działań w ramach określonego typu działania, które wystąpiły w ciągu ostatniego tygodnia.

### <a name="how-to-read-activity-search-results"></a>Jak odczytać wyniki wyszukiwania aktywności

Działania są sortowane według sygnatury czasowej, najpierw z najnowszą aktywnością. Jeśli wyniki zwracają błąd, najpierw sprawdź, czy jest to błąd usługi. W przypadku błędów usługi Utwórz bilet pomocy technicznej z informacjami o działaniu, aby pomóc nam w debugowaniu problemu. Wszystkie inne typy błędów mogą być zwykle rozwiązywane przez użytkownika lub administratora. Aby zapoznać się z listą najczęściej spotykanych scenariuszy błędów i sposobu ich rozwiązywania, zobacz [Identyfikowanie i diagnozowanie problemów](diagnostics-role-service-2019.md#common-error-scenarios).

>[!NOTE]
>Błędy usługi są nazywane "błędami zewnętrznymi" w połączonej dokumentacji. Ta wartość zostanie zmieniona podczas aktualizowania odwołania programu PowerShell.

Działania połączenia mogą mieć więcej niż jeden błąd. Można rozwinąć typ działania, aby wyświetlić wszystkie inne błędy, które użytkownik wybrał. Wybierz nazwę kodu błędu, aby otworzyć okno dialogowe, aby wyświetlić więcej informacji na jego temat.

### <a name="investigate-the-session-host"></a>Zbadaj hosta sesji

W wynikach wyszukiwania Znajdź i wybierz hosta sesji, na którym chcesz uzyskać informacje.

Kondycję hosta sesji można analizować:

- Na podstawie wstępnie zdefiniowanego progu można pobrać informacje o kondycji hosta sesji, które Log Analytics zapytań.
- Gdy nie ma aktywności lub Host sesji nie jest podłączony do Log Analytics, informacje nie będą dostępne.

Możesz również korzystać z użytkowników na hoście sesji:

- Możesz wylogować się lub wysłać wiadomość do zalogowanych użytkowników.
- Użytkownik, który został pierwotnie przeszukany, jest domyślnie wybrany, ale można również wybrać dodatkowych użytkowników do wysyłania komunikatów lub jednorazowe wylogowanie się wielu użytkowników.

### <a name="windows-performance-counter-thresholds"></a>Progi licznika wydajności systemu Windows

- Dysk logiczny ( \* ) \\ % wolnego miejsca:

    - Przedstawia wartość procentową całkowitego użytecznego miejsca na dysku logicznym, który jest bezpłatny.
    - Próg: mniej niż 20% jest oznaczone jako w złej kondycji.

- Dysk logiczny (C:) \\ średnia długość kolejki dysku:

    - Reprezentuje warunki systemu magazynu.
    - Próg: wartość wyższa niż 5 jest oznaczona jako zła.

- Pamięć ( \* ) \\ dostępne MB:

    - Dostępna pamięć dla systemu.
    - Próg: mniej niż 500 megabajtów oznaczono jako zła kondycja.

- Informacje o procesorze ( \* ) \\ czas procesora:

    - Próg: wartość wyższa niż 80% jest oznaczona jako zła.

- [Opóźnienie danych wejściowych użytkownika na sesję ( \* ) \\ maksymalne opóźnienie wejścia](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Próg: większe niż 2000 MS jest oznaczone jako złej kondycji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak monitorować dzienniki aktywności przy [użyciu diagnostyki log Analytics](diagnostics-log-analytics-2019.md).
- Przeczytaj o typowych scenariuszach błędów i sposobach ich rozwiązywania przy [identyfikowaniu i diagnozowaniu problemów](diagnostics-role-service-2019.md).