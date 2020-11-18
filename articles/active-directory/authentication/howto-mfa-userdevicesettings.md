---
title: Zarządzanie metodami uwierzytelniania dla usługi Azure AD Multi-Factor Authentication — Azure Active Directory
description: Dowiedz się, jak skonfigurować ustawienia użytkownika Azure Active Directory dla usługi Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1117b5194dae92a8025594ed71fc629670d875cf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838931"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Zarządzanie metodami uwierzytelniania użytkowników w usłudze Azure AD Multi-Factor Authentication

Użytkownicy w usłudze Azure AD mają dwa odrębne zestawy informacji kontaktowych:  

- Informacje kontaktowe profilu publicznego, które są zarządzane w profilu użytkownika i widoczne dla członków organizacji. W przypadku użytkowników synchronizowanych z Active Directory lokalnych te informacje są zarządzane w lokalnych Active Directory Domain Servicesach systemu Windows Server.
- Metody uwierzytelniania, które są zawsze przechowywane jako prywatne i używane tylko do uwierzytelniania, w tym uwierzytelnianie wieloskładnikowe (MFA). Administratorzy mogą zarządzać tymi metodami w bloku Metoda uwierzytelniania użytkownika, a użytkownicy mogą zarządzać swoimi metodami na stronie informacje zabezpieczające dla konta.

Podczas zarządzania metodami Multi-Factor Authentication usługi Azure AD dla użytkowników Administratorzy uwierzytelniania mogą: 

1. Dodawanie metod uwierzytelniania dla określonego użytkownika, w tym numerów telefonów używanych na potrzeby uwierzytelniania wieloskładnikowego.
1. Zresetuj hasło użytkownika.
1. Wymagaj od użytkownika ponownego zarejestrowania się w ramach usługi MFA.
1. Odwołaj istniejące sesje usługi MFA.
1. Usuń istniejące hasła aplikacji użytkownika  

## <a name="add-authentication-methods-for-a-user"></a>Dodawanie metod uwierzytelniania dla użytkownika 

Możesz dodać metody uwierzytelniania dla użytkownika za pośrednictwem Azure Portal lub Microsoft Graph.  

> [!NOTE]
> Ze względów bezpieczeństwa pola informacje kontaktowe użytkownika publicznego nie powinny być używane do wykonywania MFA. Zamiast tego użytkownicy powinni zapełniać numery metod uwierzytelniania, które mają być używane na potrzeby uwierzytelniania wieloskładnikowego.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Dodawanie metod uwierzytelniania z Azure Portal":::

Aby dodać metody uwierzytelniania dla użytkownika za pośrednictwem Azure Portal:  

1. Zaloguj się w witrynie **Azure Portal**. 
1. Przeglądaj, aby **Azure Active Directory**  >  **użytkowników**  >  **Wszyscy użytkownicy**. 
1. Wybierz użytkownika, dla którego chcesz dodać metodę uwierzytelniania, a następnie wybierz pozycję **metody uwierzytelniania**.  
1. W górnej części okna wybierz pozycję **+ Dodaj metodę uwierzytelniania**.
   1. Wybierz metodę (numer telefonu lub adres e-mail). Poczta e-mail może być używana do samoobsługowego resetowania hasła, ale nie uwierzytelniania. Podczas dodawania numeru telefonu wybierz typ telefonu i wprowadź numer telefonu z prawidłowym formatem (np. + 1 4255551234).
   1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Środowisko w wersji zapoznawczej pozwala administratorom dodawać dowolne dostępne metody uwierzytelniania dla użytkowników, podczas gdy oryginalne środowisko umożliwia tylko aktualizowanie telefonów i alternatywnych metod telefonii.

### <a name="manage-methods-using-powershell"></a>Zarządzanie metodami przy użyciu programu PowerShell:  

Zainstaluj moduł Microsoft. Graph. Identity. Signins PowerShell przy użyciu następujących poleceń. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Wyświetl listę metod uwierzytelniania na podstawie telefonu dla określonego użytkownika.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Utwórz metodę uwierzytelniania mobilnego dla określonego użytkownika.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

Usuń określoną metodę telefonu dla użytkownika

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Metodami uwierzytelniania można także zarządzać za pomocą Microsoft Graph interfejsów API. więcej informacji można znaleźć w dokumencie [Omówienie interfejsu API metod uwierzytelniania usługi Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Zarządzanie opcjami uwierzytelniania użytkownika

Jeśli masz przypisaną rolę *administrator uwierzytelniania* , możesz wymagać od użytkowników zresetowania hasła, ponownego zarejestrowania się w usłudze MFA lub odwołania istniejących sesji MFA z obiektu użytkownika. Aby zarządzać ustawieniami użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz pozycję **metody uwierzytelniania**. W górnej części okna, a następnie wybierz jedną z następujących opcji dla użytkownika:
   - **Zresetuj hasło resetuje** hasło użytkownika i przypisuje tymczasowe hasło, które należy zmienić przy następnym logowaniu.
   - **Wymagaj ponownej rejestracji usługi MFA** powoduje, że gdy użytkownik zaloguje się w następnym momencie, prosi o skonfigurowanie nowej metody uwierzytelniania MFA.
   
      > [!NOTE]
      > Metody uwierzytelniania aktualnie zarejestrowanego użytkownika nie są usuwane, gdy administrator wymaga ponownej rejestracji usługi MFA. Po ponownym zarejestrowaniu konta usługi MFA zalecamy zapoznanie się z informacjami o zabezpieczeniach i usunięcie wszelkich wcześniej zarejestrowanych metod uwierzytelniania, które nie są już używane.
   
   - **Odwoływanie sesji MFA** czyści sesje usługi MFA zalogowanych przez użytkownika i wymaga ich do wykonania usługi MFA przy następnym zażądaniu zasad na urządzeniu.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Zarządzanie metodami uwierzytelniania z poziomu Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Usuwanie haseł istniejących aplikacji użytkowników

W przypadku użytkowników ze zdefiniowanymi hasłami aplikacji Administratorzy mogą również usunąć te hasła, co spowoduje niepowodzenie w tych aplikacjach starszego uwierzytelniania. Te akcje mogą być niezbędne, jeśli trzeba będzie zapewnić pomoc użytkownikowi lub trzeba zresetować metody uwierzytelniania. Aplikacje nie korzystające z przeglądarki skojarzone z tymi hasłami aplikacji przestaną działać do momentu utworzenia nowego hasła aplikacji. 

Aby usunąć hasła aplikacji użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz Poniższy przykładowy zrzut ekranu, aby wyświetlić okno pełne Azure Portal i lokalizację menu: [ ![ Wybierz Multi-Factor Authentication z okna użytkowników w usłudze Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaznacz pole wyboru obok użytkownika lub użytkowników, którym chcesz zarządzać. Lista opcji szybkiego kroku pojawia się po prawej stronie.
1. Wybierz pozycję **Zarządzaj ustawieniami użytkownika**, a następnie zaznacz pole wyboru **Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników**, jak pokazano w następującym przykładzie: ![ Usuń wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Wybierz pozycję **Zapisz**, a następnie przycisk **Zamknij**.

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak skonfigurować poszczególne ustawienia użytkownika. Aby skonfigurować ogólne ustawienia usługi Azure AD Multi-Factor Authentication, zobacz [Configure Azure ad Multi-Factor Authentication Settings](howto-mfa-mfasettings.md).

Jeśli użytkownicy potrzebują pomocy, zapoznaj się z [podręcznikiem użytkownika dotyczącym usługi Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
