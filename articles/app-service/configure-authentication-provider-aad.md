---
title: Konfigurowanie uwierzytelniania w usłudze Azure AD
description: Dowiedz się, jak skonfigurować Azure Active Directory uwierzytelniania jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 2805500e4a4c98ad7b8360393e7d69ad9fb704a3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563340"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Skonfiguruj App Service lub aplikację Azure Functions do korzystania z logowania za pomocą usługi Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule opisano sposób konfigurowania uwierzytelniania dla Azure App Service lub Azure Functions, dzięki czemu aplikacja loguje się do użytkowników przy użyciu usługi Azure Active Directory (Azure AD) jako dostawcy uwierzytelniania.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Skonfiguruj przy użyciu ustawień ekspresowych

Opcja **ekspresowa** została zaprojektowana, aby umożliwić proste Włączanie uwierzytelniania i wymaga zaledwie kilku kliknięć.

Ustawienia ekspresowe automatycznie spowodują rejestrację aplikacji korzystającą z punktu końcowego Azure Active Directory v1. Aby użyć [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (w tym [MSAL](../active-directory/develop/msal-overview.md)), postępuj zgodnie z [instrukcjami dotyczącymi konfiguracji zaawansowanej](#advanced).

> [!NOTE]
> Opcja **ekspresowa** nie jest dostępna dla chmur dla instytucji rządowych.

Aby włączyć uwierzytelnianie przy użyciu opcji **Express** , wykonaj następujące kroki:

1. W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację.
2. W lewym okienku nawigacji wybierz pozycję **uwierzytelnianie/autoryzacja**  >  **na**.
3. Wybierz pozycję **Azure Active Directory**  >  **Express**.

   Jeśli chcesz wybrać istniejącą rejestrację aplikacji:

   1. Wybierz **pozycję Wybierz istniejącą aplikację usługi AD**, a następnie kliknij przycisk **aplikacja usługi Azure AD**.
   2. Wybierz istniejącą rejestrację aplikacji, a następnie kliknij przycisk **OK**.

4. Wybierz **przycisk OK** , aby zarejestrować aplikację App Service w Azure Active Directory. Zostanie utworzona nowa Rejestracja aplikacji.

    ![Ustawienia ekspresowe w Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)

5. Obowiązkowe Domyślnie App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji. Aby ograniczyć dostęp do aplikacji tylko do użytkowników uwierzytelnionych przez Azure Active Directory, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się przy użyciu Azure Active Directory**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione do Azure Active Directory na potrzeby uwierzytelniania.

    > [!CAUTION]
    > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalaj na żądania anonimowe (żadna akcja)** może być preferowana, a aplikacja ręcznie rozpoczyna logowanie się. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).
6. Wybierz pozycję **Zapisz**.

Przykład konfigurowania logowania do usługi Azure AD dla aplikacji sieci Web, która uzyskuje dostęp do usługi Azure Storage i Microsoft Graph, można znaleźć w [tym samouczku](scenario-secure-app-authentication-app-service.md).

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfiguruj z ustawieniami zaawansowanymi

Aby usługa Azure AD działała jako dostawca uwierzytelniania dla aplikacji, musisz zarejestrować aplikację. Opcja ekspresowa robi to automatycznie. Opcja zaawansowane umożliwia ręczne rejestrowanie aplikacji, dostosowanie rejestracji i ręczne wprowadzenie szczegółów rejestracji z powrotem do App Service. Jest to przydatne, jeśli na przykład chcesz użyć rejestracji aplikacji z innej dzierżawy usługi Azure AD niż ta, w której znajduje się App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service

Najpierw utworzysz rejestrację aplikacji. W takim przypadku należy zebrać poniższe informacje, które będą potrzebne później podczas konfigurowania uwierzytelniania w aplikacji App Service:

- Identyfikator klienta
- Identyfikator dzierżawy
- Klucz tajny klienta (opcjonalnie)
- Identyfikator URI identyfikatora aplikacji

Aby zarejestrować aplikację, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal], Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. Zanotuj **adres URL** aplikacji. Zostanie ona użyta do skonfigurowania rejestracji aplikacji Azure Active Directory.
1. W menu portalu wybierz pozycję **Azure Active Directory**, a następnie przejdź do karty **rejestracje aplikacji** i wybierz pozycję **Nowa rejestracja**.
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web** i wpisz `<app-url>/.auth/login/aad/callback` . Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Wybierz pozycję **Zarejestruj**.
1. Po utworzeniu rejestracji aplikacji Skopiuj **Identyfikator aplikacji (klienta)** i **Identyfikator katalogu (dzierżawcy)** w przyszłości.
1. Wybierz pozycję **Uwierzytelnianie**. W obszarze **niejawne Przyznaj** **tokeny identyfikatora** , aby zezwolić OpenID Connect na logowanie użytkowników z App Service.
1. Obowiązkowe Wybierz **znakowanie**. W polu **adres URL strony głównej** wprowadź adres url aplikacji App Service i wybierz pozycję **Zapisz**.
1. Wybierz opcję **Uwidocznij zestaw interfejsów API**  >  . W przypadku aplikacji z jedną dzierżawą Wklej adres URL aplikacji App Service i wybierz pozycję **Zapisz** i dla aplikacji z wieloma dzierżawcami, wklej adres URL oparty na jednej z zweryfikowanych domen dzierżawy, a następnie wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Ta wartość to **Identyfikator URI identyfikatora aplikacji** rejestracji aplikacji. Jeśli aplikacja sieci Web wymaga dostępu do interfejsu API w chmurze, podczas konfigurowania zasobu App Service chmury potrzebny jest **Identyfikator URI aplikacji** sieci Web. Możesz użyć tego przykładu, jeśli chcesz, aby usługa w chmurze jawnie zezwalała na dostęp do aplikacji sieci Web.

1. Wybierz polecenie **Dodaj zakres**.
   1. W polu **Nazwa zakresu** wprowadź *user_impersonation*.
   1. W polach tekstowych wprowadź nazwę i opis zakresu zgody, które użytkownicy mają zobaczyć na stronie zgody. Na przykład wprowadź *dostęp do mojej aplikacji*.
   1. Wybierz pozycję **Dodaj zakres**.
1. Obowiązkowe Aby utworzyć klucz tajny klienta, wybierz pozycję **Certyfikaty &** wpisy tajne  >  **Nowy klient Dodaj wpis tajny**  >  . Skopiuj wartość klucza tajnego klienta podaną na stronie. Nie zostanie on wyświetlony ponownie.
1. Obowiązkowe Aby dodać wiele **adresów URL odpowiedzi**, wybierz pozycję **uwierzytelnianie**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Włączanie Azure Active Directory w aplikacji App Service

1. W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację.
1. W lewym okienku w obszarze **Ustawienia** wybierz pozycję **uwierzytelnianie/autoryzacja**  >  **na**.
1. Obowiązkowe Domyślnie uwierzytelnianie App Service zezwala na nieuwierzytelniony dostęp do aplikacji. Aby wymusić uwierzytelnianie użytkowników, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się za pomocą Azure Active Directory**.
1. W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**.
1. W obszarze **tryb zarządzania** wybierz pozycję **Zaawansowane** i Skonfiguruj uwierzytelnianie App Service zgodnie z poniższą tabelą:

    |Pole|Opis|
    |-|-|
    |Identyfikator klienta| Użyj **identyfikatora aplikacji (klienta)** rejestracji aplikacji. |
    |Adres URL wystawcy| Użyj `<authentication-endpoint>/<tenant-id>/v2.0` i Zamień na *\<authentication-endpoint>* [punkt końcowy uwierzytelniania dla środowiska chmury](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (np. " https://login.microsoftonline.com " dla globalnej platformy Azure), zastępując *\<tenant-id>* go **identyfikatorem katalogu (dzierżawy)** , w którym została utworzona Rejestracja aplikacji. Ta wartość jest używana do przekierowywania użytkowników do poprawnej dzierżawy usługi Azure AD, a także do pobierania odpowiednich metadanych w celu określenia odpowiednich kluczy podpisywania tokenu i wartości na przykład przez wystawcę tokenów. W przypadku aplikacji korzystających z usługi Azure AD w wersji 1 i dla aplikacji Azure Functions Pomiń `/v2.0` adres URL.|
    |Klucz tajny klienta (opcjonalnie)| Użyj klucza tajnego klienta wygenerowanego podczas rejestracji aplikacji.|
    |Dozwolone odbiorcy tokenu| Jeśli jest to aplikacja w chmurze lub na serwerze i chcesz zezwolić na tokeny uwierzytelniania z aplikacji sieci Web, w tym miejscu Dodaj **Identyfikator URI aplikacji** sieci Web. Skonfigurowany **Identyfikator klienta** jest *zawsze* niejawnie uznawany za dozwolonych odbiorców. |

2. Wybierz przycisk **OK**, a następnie wybierz pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji App Service za pomocą Azure Active Directory.

## <a name="configure-client-apps-to-access-your-app-service"></a>Skonfiguruj aplikacje klienckie, aby uzyskiwać dostęp do App Service

W poprzedniej sekcji zarejestrowano App Service lub funkcję platformy Azure w celu uwierzytelniania użytkowników. W tej sekcji opisano sposób rejestrowania natywnych aplikacji klienta lub demonów, dzięki czemu mogą oni zażądać dostępu do interfejsów API narażonych przez App Service w imieniu użytkowników lub na siebie. Wykonanie kroków opisanych w tej sekcji nie jest wymagane, jeśli chcesz tylko uwierzytelniać użytkowników.

### <a name="native-client-application"></a>Natywna aplikacja kliencka

Możesz zarejestrować natywnych klientów, aby zażądać dostępu do interfejsów API aplikacji App Service w imieniu zalogowanego użytkownika.

1. W [Azure Portal]wybierz pozycję **Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W polu **Identyfikator URI przekierowania** wybierz pozycję **Klient publiczny (Mobile & Desktop)** , a następnie wpisz adres URL `<app-url>/.auth/login/aad/callback` . Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > W przypadku aplikacji Microsoft Store Użyj [identyfikatora SID pakietu](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) jako identyfikatora URI.
1. Wybierz przycisk **Utwórz**.
1. Po utworzeniu rejestracji aplikacji skopiuj wartość **Identyfikator aplikacji (klienta)**.
1. Wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**  >  **Moje interfejsy API**.
1. Wybierz rejestrację aplikacji utworzoną wcześniej dla aplikacji App Service. Jeśli nie widzisz rejestracji aplikacji, upewnij się, że dodano zakres **user_impersonation** w temacie [Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service](#register).
1. W obszarze **delegowane uprawnienia** wybierz pozycję **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

Skonfigurowano natywną aplikację kliencką, która może zażądać dostępu do aplikacji App Service w imieniu użytkownika.

### <a name="daemon-client-application-service-to-service-calls"></a>Aplikacja kliencka demona (wywołania między usługami)

Aplikacja może uzyskać token do wywoływania internetowego interfejsu API hostowanego w App Service lub aplikacji funkcji w imieniu samego siebie (a nie w imieniu użytkownika). Ten scenariusz jest przydatny w przypadku aplikacji nieinterakcyjnego demona, które wykonują zadania bez zalogowanego użytkownika. Używa on standardowego udzielenia [poświadczeń klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) OAuth 2,0.

1. W [Azure Portal]wybierz pozycję **Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji demona.
1. W przypadku aplikacji demona nie jest potrzebny identyfikator URI przekierowania, aby można było zachować tę wartość pustą.
1. Wybierz przycisk **Utwórz**.
1. Po utworzeniu rejestracji aplikacji skopiuj wartość **Identyfikator aplikacji (klienta)**.
1. Wybierz pozycję **Certyfikaty &** wpisy tajne  >  **Nowy klient Dodaj wpis tajny**  >  . Skopiuj wartość klucza tajnego klienta podaną na stronie. Nie zostanie on wyświetlony ponownie.

Teraz można [zażądać tokenu dostępu przy użyciu identyfikatora klienta i klucza tajnego klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) przez ustawienie `resource` PARAMETRU na **Identyfikator URI aplikacji** docelowej. Otrzymany token dostępu można następnie przedstawić dla aplikacji docelowej przy użyciu standardowego [nagłówka autoryzacji OAuth 2,0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource), a App Service uwierzytelnianie/autoryzacja będzie sprawdzać poprawność i używanie tokenu w zwykły sposób, aby wskazać, że obiekt wywołujący (aplikacja w tym przypadku, a nie użytkownik) jest uwierzytelniany.

Teraz dzięki temu _każda_ aplikacja kliencka w dzierżawie usługi Azure AD żąda tokenu dostępu i uwierzytelnia się w aplikacji docelowej. Jeśli chcesz również wymusić _autoryzację_ , aby zezwolić tylko na niektóre aplikacje klienckie, musisz wykonać dodatkową konfigurację.

1. [Zdefiniuj rolę aplikacji](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) w manifeście rejestracji aplikacji reprezentującej App Service lub aplikację funkcji, która ma być chroniona.
1. Na stronie Rejestracja aplikacji reprezentującej klienta wymagającego autoryzacji wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**  >  **Moje interfejsy API**.
1. Wybierz utworzoną wcześniej rejestrację aplikacji. Jeśli nie widzisz rejestracji aplikacji, upewnij się, że została [dodana rola aplikacji](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. W obszarze **uprawnienia aplikacji** Wybierz utworzoną wcześniej rolę aplikacji, a następnie wybierz pozycję **Dodaj uprawnienia**.
1. Upewnij się, kliknij przycisk **Udziel zgody administratora** , aby autoryzować aplikację kliencką do żądania uprawnień.
1. Podobnie jak w poprzednim scenariuszu (przed dodaniem dowolnych ról), możesz teraz [zażądać tokenu dostępu](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) dla tego samego elementu docelowego `resource` , a token dostępu będzie zawierać `roles` żądanie zawierające role aplikacji autoryzowane dla aplikacji klienckiej.
1. W App Service docelowym lub kodzie aplikacji funkcji można teraz sprawdzić, czy oczekiwane role są obecne w tokenie (nie jest to wykonywane przez App Service uwierzytelniania/autoryzacji). Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do oświadczeń użytkowników](app-service-authentication-how-to.md#access-user-claims).

Już skonfigurowano aplikację kliencką demona, która może uzyskiwać dostęp do aplikacji App Service przy użyciu własnej tożsamości.

## <a name="best-practices"></a>Najlepsze rozwiązania

Niezależnie od konfiguracji używanej do konfigurowania uwierzytelniania, następujące najlepsze rozwiązania spowodują, że Twoja dzierżawa i aplikacje będą bezpieczniejsze:

- Nadaj każdej aplikacji App Service swoje własne uprawnienia i zgodę.
- Skonfiguruj każdą aplikację App Service ze swoją rejestracją.
- Unikaj udostępniania uprawnień między środowiskami przy użyciu oddzielnych rejestracji aplikacji dla oddzielnych miejsc wdrożenia. W przypadku testowania nowego kodu to rozwiązanie może pomóc zapobiec problemom związanym z aplikacją produkcyjną.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników w aplikacji sieci Web, która uzyskuje dostęp do usługi Azure Storage i Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników w usłudze Azure App Service](tutorial-auth-aad.md)
<!-- URLs. -->

[Witryna Azure Portal]: https://portal.azure.com/
