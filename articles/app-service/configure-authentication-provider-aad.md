---
title: Konfigurowanie uwierzytelniania w usłudze Azure AD
description: Dowiedz się, jak skonfigurować uwierzytelnianie usługi Azure Active Directory jako dostawcę tożsamości dla usługi app service lub aplikacji Usługi Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: f625f5df4f33c6516bd5c50f97c52404d76757a0
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024459"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurowanie usługi aplikacji lub aplikacji Usługi Azure Functions do używania logowania usługi Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure App Service lub usługi Azure Functions do używania usługi Azure Active Directory (Azure AD) jako dostawcy uwierzytelniania.

> [!NOTE]
> Przepływ ustawień ekspresowych konfiguruje rejestrację aplikacji AAD V1. Jeśli chcesz korzystać z [usługi Azure Active Directory w wersji 2.0](../active-directory/develop/v2-overview.md) (w tym [MSAL),](../active-directory/develop/msal-overview.md)postępuj zgodnie z [instrukcjami konfiguracji zaawansowanej.](#advanced)

Podczas konfigurowania aplikacji i uwierzytelniania należy postępować zgodnie z tymi najlepszymi rozwiązaniami:

- Nadaj każdej aplikacji usługi App Service własne uprawnienia i zgodę.
- Skonfiguruj każdą aplikację usługi App Service z własną rejestracją.
- Unikaj udostępniania uprawnień między środowiskami przy użyciu oddzielnych rejestracji aplikacji dla oddzielnych gniazd wdrażania. Podczas testowania nowego kodu ta praktyka może pomóc zapobiec problemy z wpływu na aplikację produkcyjną.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurowanie z ustawieniami ekspresowymi

> [!NOTE]
> Opcja **Express** nie jest dostępna dla chmur rządowych. 

1. W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację.
2. Z lewej strony wybierz **pozycję Uwierzytelnianie / Autoryzacja** > **wł.**
3. Wybierz **usługę Azure Active Directory** > **Express**.

   Jeśli chcesz wybrać istniejącą rejestrację aplikacji:

   1. Wybierz **pozycję Wybierz istniejącą aplikację USŁUGI AD,** a następnie kliknij pozycję **Aplikacja Usługi Azure AD**.
   2. Wybierz istniejącą rejestrację aplikacji i kliknij przycisk **OK**.

3. Wybierz **przycisk OK,** aby zarejestrować aplikację usługi App Service w usłudze Azure Active Directory. Zostanie utworzona nowa rejestracja aplikacji.
   
    ![Ustawienia ekspresowe w usłudze Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Opcjonalnie) Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. Musisz autoryzować użytkowników w kodzie aplikacji. Aby ograniczyć dostęp do aplikacji tylko do użytkowników uwierzytelnionych przez usługę Azure Active Directory, ustaw **akcję do podjęcia, gdy żądanie nie jest uwierzytelnione,** aby **zalogować się za pomocą usługi Azure Active Directory**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje również wszystkie nieuwierzyliczone do usługi Azure Active Directory do uwierzytelniania.

    > [!CAUTION]
    > Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane w przypadku aplikacji, które mają publicznie dostępną stronę główną, jak w wielu aplikacjach jednostronicowych. W przypadku takich aplikacji może być preferowane **zezwalanie na żądania anonimowe (brak akcji),** a aplikacja ręcznie uruchamia samo logowanie. Aby uzyskać więcej informacji, zobacz [Przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).
5. Wybierz pozycję **Zapisz**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurowanie z ustawieniami zaawansowanymi

Ustawienia aplikacji można skonfigurować ręcznie, jeśli chcesz użyć rejestracji aplikacji z innej dzierżawy usługi Azure AD. Aby ukończyć tę konfigurację niestandardową:

1. Utwórz rejestrację w usłudze Azure AD.
2. Podaj niektóre szczegóły rejestracji do usługi App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji usługi App Service

Podczas konfigurowania aplikacji usługi App Service potrzebne są następujące informacje:

- Identyfikator klienta
- Identyfikator dzierżawy
- Klucz tajny klienta (opcjonalnie)
- Identyfikator URI aplikacji

Wykonaj poniższe czynności:

1. Zaloguj się do [witryny Azure portal], wyszukaj i wybierz **usługi aplikacji**, a następnie wybierz aplikację. Zanotuj **adres URL**aplikacji . Użyjesz go do skonfigurowania rejestracji aplikacji usługi Azure Active Directory.
1. Wybierz**rejestracje** > aplikacji **usługi Azure Active Directory** > Nowa**rejestracja**.
1. Na stronie **Zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W **obszarze Przekierowanie identyfikatora URI**wybierz pozycję **Web** i wpisz . `<app-url>/.auth/login/aad/callback` Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Wybierz pozycję **Utwórz**.
1. Po utworzeniu rejestracji aplikacji skopiuj **identyfikator aplikacji (klienta)** i **identyfikator katalogu (dzierżawy)** na później.
1. Wybierz pozycję **Uwierzytelnianie**. W obszarze **Niejawne przyznanie**włącz **tokeny identyfikatorów,** aby zezwolić na logowanie użytkowników OpenID Connect z usługi App Service.
1. (Opcjonalnie) Wybierz **opcję Znakowanie**. W **polu URL strony głównej**wprowadź adres URL aplikacji App Service i wybierz pozycję **Zapisz**.
1. Wybierz **pozycję Uwidaczniaj** > **zestaw**interfejsu API . W przypadku aplikacji z jedną dzierżawą wklej adres URL aplikacji Usługi App Service i wybierz pozycję **Zapisz** i dla aplikacji z wieloma dzierżawami, wklej adres URL oparty na jednej z zweryfikowanych domen dzierżawy, a następnie wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Ta wartość jest **identyfikatorem URI** identyfikatora aplikacji rejestracji aplikacji. Jeśli aplikacja sieci web wymaga dostępu do interfejsu API w chmurze, podczas konfigurowania zasobu usługi App Service w chmurze potrzebny jest **identyfikator URI identyfikatora aplikacji** sieci web. Można użyć tego, na przykład, jeśli chcesz, aby usługa w chmurze jawnie udzielić dostępu do aplikacji sieci web.

1. Wybierz polecenie **Dodaj zakres**.
   1. W **nazwie zakresu**wprowadź *user_impersonation*.
   1. W polach tekstowych wprowadź nazwę zakresu zgody i opis, który użytkownicy mają widzieć na stronie zgody. Na przykład wprowadź *dostęp do mojej aplikacji*. 
   1. Wybierz **pozycję Dodaj zakres**.
1. (Opcjonalnie) Aby utworzyć klucz tajny klienta, wybierz **pozycję Certyfikaty & wpisy tajne** > **Nowy klucz tajny** > klienta**Dodaj**. Skopiuj wartość tajnego klienta wyświetlaną na stronie. Nie będzie wyświetlany ponownie.
1. (Opcjonalnie) Aby dodać wiele **adresów URL odpowiedzi,** wybierz pozycję **Uwierzytelnianie**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Włączanie usługi Azure Active Directory w aplikacji usługi App Service

1. W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. 
1. W lewym okienku w obszarze **Ustawienia**wybierz pozycję **Uwierzytelnianie / Autoryzacja** > **wł.**
1. (Opcjonalnie) Domyślnie uwierzytelnianie usługi App Service umożliwia nieuwierzytywy dostęp do aplikacji. Aby wymusić uwierzytelnianie użytkowników, ustaw **akcję do podjęcia, gdy żądanie nie jest uwierzytelnione,** aby **zalogować się za pomocą usługi Azure Active Directory**.
1. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Azure Active Directory**.
1. W **trybie zarządzania**wybierz opcję **Zaawansowane** i skonfiguruj uwierzytelnianie usługi App Service zgodnie z następującą tabelą:

    |Pole|Opis|
    |-|-|
    |Identyfikator klienta| Użyj identyfikatora **aplikacji (klienta)** rejestracji aplikacji. |
    |Adres URL wystawcy| Użyj `<authentication-endpoint>/<tenant-id>/v2.0`programu , i zastąp * \<>punktu końcowego uwierzytelniania* [punktem końcowym uwierzytelniania dla środowiska chmury](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (np.https://login.microsoft.com" dla globalnej platformy Azure), zastępując * \<również identyfikatora dzierżawy>* **identyfikatorem katalogu (dzierżawy),** w którym została utworzona rejestracja aplikacji. Ta wartość jest używana do przekierowywania użytkowników do poprawnej dzierżawy usługi Azure AD, a także do pobierania odpowiednich metadanych w celu określenia odpowiednich kluczy podpisywania tokenu i wartości oświadczenia wystawcy tokenu na przykład. Sekcja `/v2.0` może zostać pominięta dla aplikacji korzystających z usługi AAD w wersji 1. |
    |Klucz tajny klienta (opcjonalnie)| Użyj klucza tajnego klienta wygenerowanego podczas rejestracji aplikacji.|
    |Dozwolone grupy odbiorców tokenów| Jeśli jest to aplikacja w chmurze lub serwera i chcesz zezwolić tokeny uwierzytelniania z aplikacji sieci web, dodaj **identyfikator URI identyfikatora aplikacji** w aplikacji sieci web tutaj. Skonfigurowany **identyfikator klienta** jest *zawsze* niejawnie uważany za dozwoloną grupę odbiorców. |

2. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zapisz**.

Teraz możesz używać usługi Azure Active Directory do uwierzytelniania w aplikacji usługi App Service.

## <a name="configure-a-native-client-application"></a>Konfigurowanie natywnej aplikacji klienckiej

Można zarejestrować klientów natywnych, aby zezwolić na uwierzytelnianie w interfejsie API sieci Web hostowanym w aplikacji przy użyciu biblioteki klienta, takiej jak **Biblioteka uwierzytelniania usługi Active Directory.**

1. W [portalu Azure]wybierz pozycję**Rejestracje** > aplikacji **usługi Active Directory** > **Nowa rejestracja**.
1. Na stronie **Zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W **obszarze Przekierowanie identyfikatora URI**wybierz pozycję Klient `<app-url>/.auth/login/aad/callback`publiczny **(mobilny & pulpit)** i wpisz adres URL . Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > W przypadku aplikacji ze sklepu Microsoft Store należy użyć [identyfikatora SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identyfikatora URI.
1. Wybierz pozycję **Utwórz**.
1. Po utworzeniu rejestracji aplikacji skopiuj wartość **identyfikatora aplikacji (klienta).**
1. Wybierz **uprawnienia** > interfejsu API**Dodaj uprawnienie** > **Moje interfejsy API**.
1. Wybierz rejestrację aplikacji utworzoną wcześniej dla aplikacji usługi App Service. Jeśli nie widzisz rejestracji aplikacji, upewnij się, że dodano zakres **user_impersonation** w [obszarze Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji usługi App Service.](#register)
1. Wybierz **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

Teraz skonfigurowano natywną aplikację kliencką, która może uzyskiwać dostęp do aplikacji usługi App Service w imieniu użytkownika.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
