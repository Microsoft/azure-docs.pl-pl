---
title: Tworzenie dzierżawy w systemie Windows Virtual Desktop (klasyczny) — Azure
description: Opisuje sposób konfigurowania dzierżawców klasycznego systemu Windows (klasyczny) w Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f41584194f8f5e8afde630405116b8b169cb5656
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542040"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Samouczek: Tworzenie dzierżawy w systemie Windows Virtual Desktop (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

Tworzenie dzierżawy w systemie Windows Virtual Desktop to pierwszy krok w kierunku tworzenia rozwiązania do wirtualizacji pulpitu. Dzierżawca jest grupą co najmniej jednej puli hostów. Każda pula hostów składa się z wielu hostów sesji uruchomionych jako maszyny wirtualne na platformie Azure i zarejestrowane w usłudze pulpitu wirtualnego systemu Windows. Każda pula hostów składa się również z co najmniej jednej grupy aplikacji, która jest używana do publikowania zasobów pulpitu zdalnego i aplikacji zdalnych użytkownikom. Dzierżawy umożliwiają tworzenie pul hostów, tworzenie grup aplikacji, przypisywanie użytkowników i nawiązywanie połączeń za pomocą usługi.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przyznaj Azure Active Directory uprawnienia do usługi pulpitu wirtualnego systemu Windows.
> * Przypisz rolę aplikacji TenantCreator do użytkownika w dzierżawie Azure Active Directory.
> * Utwórz dzierżawę pulpitu wirtualnego systemu Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Co należy zrobić, aby skonfigurować dzierżawcę

Przed rozpoczęciem konfigurowania dzierżawy pulpitu wirtualnego systemu Windows upewnij się, że masz następujące elementy:

* Identyfikator dzierżawy [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) dla użytkowników pulpitu wirtualnego systemu Windows.
* Konto administratora globalnego w dzierżawie Azure Active Directory.
   * Dotyczy to również organizacji z dostawcami rozwiązań w chmurze, które tworzą dzierżawę pulpitów wirtualnych systemu Windows dla swoich klientów. Jeśli jesteś w organizacji programu CSP, musisz być w stanie zalogować się jako Administrator globalny Azure Active Directory wystąpienia klienta.
   * Konto administratora musi być źródłem Azure Active Directory dzierżawy, w której próbujesz utworzyć dzierżawę pulpitu wirtualnego systemu Windows. Ten proces nie obsługuje Azure Active Directory kont B2B (gościa).
   * Konto administratora musi być kontem służbowym.
* Subskrypcja platformy Azure.

Aby proces opisany w tym samouczku działał prawidłowo, musisz mieć już identyfikator dzierżawy, konto administratora globalnego i subskrypcję platformy Azure.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Przyznawanie uprawnień do pulpitu wirtualnego systemu Windows

Jeśli masz już uprawnienia do pulpitu wirtualnego systemu Windows dla tego wystąpienia Azure Active Directory Pomiń tę sekcję.

Przyznanie uprawnień do usługi pulpitów wirtualnych systemu Windows umożliwia tworzenie zapytań Azure Active Directory na potrzeby zadań administracyjnych i użytkowników końcowych.

Aby udzielić uprawnień usługi:

1. Otwórz przeglądarkę i Rozpocznij przepływ zgody administratora na [aplikację serwera pulpitu wirtualnego systemu Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > W przypadku zarządzania klientem i konieczności udzielenia zgody administratora na katalog klienta wprowadź następujący adres URL w przeglądarce i Zastąp ciąg {dzierżawca} nazwą domeny usługi Azure AD. Na przykład jeśli organizacja klienta zarejestrowała nazwę domeny usługi Azure AD contoso.onmicrosoft.com, Zastąp ciąg {dzierżawca} z contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Zaloguj się na stronie zgody na pulpit wirtualny systemu Windows przy użyciu konta administratora globalnego. Na przykład jeśli jesteś w organizacji contoso, Twoje konto może być admin@contoso.com lub admin@contoso.onmicrosoft.com .
3. Wybierz pozycję **Zaakceptuj**.
4. Poczekaj chwilę, aby usługa Azure AD mogła rejestrować zgodę.
5. Otwórz przeglądarkę i Rozpocznij przepływ zgody administratora na [aplikację klienta pulpitu wirtualnego systemu Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > W przypadku zarządzania klientem i konieczności udzielenia zgody administratora na katalog klienta wprowadź następujący adres URL w przeglądarce i Zastąp ciąg {dzierżawca} nazwą domeny usługi Azure AD. Na przykład jeśli organizacja klienta zarejestrowała nazwę domeny usługi Azure AD contoso.onmicrosoft.com, Zastąp ciąg {dzierżawca} z contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Zaloguj się na stronie zgody pulpitu wirtualnego systemu Windows jako Administrator globalny, tak jak w kroku 2.
7. Wybierz pozycję **Zaakceptuj**.

## <a name="assign-the-tenantcreator-application-role"></a>Przypisywanie roli aplikacji TenantCreator

Przypisanie użytkownika Azure Active Directory rola aplikacji TenantCreator umożliwia temu użytkownikowi utworzenie dzierżawy pulpitu wirtualnego systemu Windows skojarzonej z wystąpieniem Azure Active Directory. Musisz użyć konta administratora globalnego, aby przypisać rolę TenantCreator.

Aby przypisać rolę aplikacji TenantCreator:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać rolą aplikacji TenantCreator. Wyszukaj i wybierz pozycję **aplikacje dla przedsiębiorstw**. Jeśli pracujesz z wieloma dzierżawcami Azure Active Directory, najlepszym rozwiązaniem jest otwarcie sesji przeglądarki prywatnej i skopiowanie i wklejenie adresów URL na pasku adresu.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający wyszukiwanie aplikacji dla przedsiębiorstw w Azure Portal](../media/azure-portal-enterprise-applications.png)

2. W obszarze **aplikacje dla przedsiębiorstw**Wyszukaj pozycję **pulpit wirtualny systemu Windows**. Zobaczysz dwie aplikacje, które podano w poprzedniej sekcji. Z tych dwóch aplikacji wybierz pozycję **pulpit wirtualny systemu Windows**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający wyniki wyszukiwania podczas wyszukiwania "pulpit wirtualny systemu Windows" w "aplikacje dla przedsiębiorstw". Aplikacja o nazwie "pulpit wirtualny systemu Windows" jest wyróżniona.](../media/tenant-enterprise-app.png)

3. Wybierz pozycję **Użytkownicy i grupy**. Może się okazać, że administrator, który udzielił zgody na dostęp do aplikacji, znajduje się już na liście z domyślną przypisaną rolą **dostępu** . Nie jest to wystarczające, aby utworzyć dzierżawę pulpitu wirtualnego systemu Windows. Wykonaj poniższe instrukcje, aby dodać rolę **TenantCreator** do użytkownika.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu użytkowników i grup przypisanych do zarządzania aplikacją Enterprise "pulpit wirtualny systemu Windows". Zrzut ekranu zawiera tylko jedno przypisanie, które jest przeznaczone dla "dostępu domyślnego".](../media/tenant-default-access.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** na karcie **Dodaj przypisanie** .
5. Wyszukaj konto użytkownika, które będzie tworzyć dzierżawcę pulpitów wirtualnych systemu Windows. Dla uproszczenia może to być konto administratora globalnego.
   - Jeśli używasz dostawcy tożsamości firmy Microsoft, takiego jak contosoadmin@live.com lub contosoadmin@outlook.com , możesz nie być w stanie zalogować się do pulpitu wirtualnego systemu Windows. Zalecamy użycie konta specyficznego dla domeny, takiego jak admin@contoso.com lub admin@contoso.onmicrosoft.com .

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający Wybieranie użytkownika do dodania jako "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > Musisz wybrać użytkownika (lub grupę zawierającą użytkownika), która jest źródłem z tego wystąpienia Azure Active Directory. Nie możesz wybrać użytkownika (B2B) ani nazwy głównej usługi.

6. Wybierz konto użytkownika, wybierz przycisk **Wybierz** , a następnie wybierz pozycję **Przypisz**.
7. Na stronie **pulpity wirtualne systemu Windows — użytkownicy i grupy** Sprawdź, czy jest wyświetlany nowy wpis z rolą **TenantCreator** przypisaną do użytkownika, który utworzy dzierżawę pulpitów wirtualnych systemu Windows.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu użytkowników i grup przypisanych do zarządzania aplikacją Enterprise "pulpit wirtualny systemu Windows". Zrzut ekranu zawiera teraz drugą pozycję użytkownika przypisaną do roli "TenantCreator".](../media/tenant-tenant-creator-added.png)

Przed kontynuowaniem tworzenia dzierżawy pulpitu wirtualnego systemu Windows potrzebne są dwie informacje:

   - Identyfikator dzierżawy Azure Active Directory (lub **Identyfikator katalogu**)
   - Identyfikator subskrypcji platformy Azure

Aby znaleźć identyfikator dzierżawy Azure Active Directory (lub **Identyfikator katalogu**):
1. W tej samej sesji [Azure Portal](https://portal.azure.com) Wyszukaj i wybierz pozycję **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla "Azure Active Directory" w Azure Portal. Zostanie wyróżniony wynik wyszukiwania w obszarze "usługi".](../media/tenant-search-azure-active-directory.png)

2. Przewiń w dół do momentu znalezienia **Właściwości**, a następnie wybierz ją.
3. Wyszukaj **Identyfikator katalogu**, a następnie wybierz ikonę schowka. Wklej ją w wygodnym miejscu, aby można było użyć jej później jako wartości **AadTenantId** .

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający właściwości Azure Active Directory. Wskaźnik myszy znajduje się nad ikoną schowka dla "identyfikator katalogu", aby skopiować i wkleić.](../media/tenant-directory-id.png)

Aby znaleźć identyfikator subskrypcji platformy Azure:
1. W tej samej sesji [Azure Portal](https://portal.azure.com) Wyszukaj i wybierz pozycję **subskrypcje**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla "Azure Active Directory" w Azure Portal. Wynik wyszukiwania dla "usług" został wyróżniony.](../media/tenant-search-subscription.png)

2. Wybierz subskrypcję platformy Azure, której chcesz używać do odbierania powiadomień usługi pulpitu wirtualnego systemu Windows.
3. Poszukaj **identyfikatora subskrypcji**, a następnie umieść wskaźnik myszy nad wartością, dopóki nie zostanie wyświetlona ikona Schowka. Wybierz ikonę schowka i wklej ją w wygodnym miejscu, aby można było użyć jej później jako wartości **AzureSubscriptionId** .

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający właściwości subskrypcji platformy Azure. Wskaźnik myszy znajduje się nad ikoną schowka dla "Identyfikator subskrypcji", aby skopiować i wkleić.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Tworzenie dzierżawy pulpitu wirtualnego systemu Windows

Teraz, gdy masz uprawnienia usługi pulpitu wirtualnego systemu Windows do wykonywania zapytań Azure Active Directory i przypisywania roli TenantCreator do konta użytkownika, możesz utworzyć dzierżawę pulpitu wirtualnego systemu Windows.

Najpierw [Pobierz i zaimportuj moduł pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu konta użytkownika TenantCreator za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie utwórz nową dzierżawę pulpitu wirtualnego systemu Windows skojarzoną z dzierżawcą Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Zastąp wartości ujęte w nawiasy wartościami istotnymi dla Twojej organizacji i dzierżawy. Wybrana nazwa nowej dzierżawy pulpitu wirtualnego systemu Windows powinna być globalnie unikatowa. Załóżmy na przykład, że jesteś TenantCreator pulpitu wirtualnego systemu Windows dla organizacji contoso. Uruchomione polecenie cmdlet będzie wyglądać następująco:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Dobrym pomysłem jest przydzielenie dostępu administracyjnego do drugiego użytkownika na wypadek, gdyby kiedykolwiek wcześniej się wyszukali z Twojego konta. Możesz też przejść na wakacje i potrzebować kogoś, kto może działać jako Administrator dzierżawy w Twojej nieobecności. Aby przypisać dostęp administratora do drugiego użytkownika, uruchom następujące polecenie cmdlet w programie i zastąp go `<TenantName>` `<Upn>` nazwą dzierżawy oraz z identyfikatorem UPN drugiego użytkownika.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Następne kroki
Po utworzeniu dzierżawy należy utworzyć nazwę główną usługi w Azure Active Directory i przypisać ją do roli w ramach pulpitu wirtualnego systemu Windows. Jednostka usługi umożliwi pomyślne wdrożenie oferty Azure Marketplace pulpitu wirtualnego systemu Windows w celu utworzenia puli hostów. Aby dowiedzieć się więcej na temat pul hostów, przejdź do samouczka dotyczącego tworzenia puli hostów w programie Virtual Desktop systemu Windows.

> [!div class="nextstepaction"]
> [Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](create-service-principal-role-powershell.md)
