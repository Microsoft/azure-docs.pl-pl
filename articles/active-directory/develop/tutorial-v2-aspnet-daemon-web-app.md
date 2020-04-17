---
title: Tworzenie demona wielodostępnego, który korzysta z punktu końcowego platformy tożsamości firmy Microsoft
description: W tym samouczku dowiesz się, jak wywołać ASP.NET internetowy interfejs API chroniony przez usługę Azure Active Directory z aplikacji pulpitu systemu Windows (WPF). Klient WPF uwierzytelnia użytkownika, żąda tokenu dostępu i wywołuje internetowy interfejs API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 0fb80b8a3fe9dd642b1574b35ff48b30272ce848
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533721"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Samouczek: Tworzenie demona wielodostępnego, który używa punktu końcowego platformy tożsamości firmy Microsoft

W tym samouczku dowiesz się, jak korzystać z platformy tożsamości firmy Microsoft, aby uzyskać dostęp do danych klientów biznesowych firmy Microsoft w długotrwałym, nieinterakcyjnym procesie. Przykładowy demon używa [poświadczeń klienta OAuth2 do](v2-oauth2-client-creds-grant-flow.md) uzyskania tokenu dostępu. Demon następnie używa tokenu do wywoływania [programu Microsoft Graph](https://graph.microsoft.io) i uzyskiwania dostępu do danych organizacji.

> [!div class="checklist"]
> * Integracja aplikacji demona z platformą tożsamości firmy Microsoft
> * Udzielanie uprawnień aplikacji bezpośrednio do aplikacji przez administratora
> * Uzyskiwanie tokenu dostępu do wywoływania interfejsu API programu Microsoft Graph
> * Wywołanie interfejsu API programu Microsoft Graph.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Aplikacja jest zbudowana jako aplikacja ASP.NET MVC. Używa oprogramowania pośredniczącego OWIN OpenID Connect do logowania użytkowników.

Składnik "demon" w tym przykładzie jest `SyncController.cs`kontrolerem interfejsu API, . Gdy kontroler jest wywoływany, pobiera listę użytkowników w dzierżawie usługi Azure Active Directory (Azure AD) klienta z programu Microsoft Graph. `SyncController.cs`jest wyzwalany przez wywołanie AJAX w aplikacji sieci web. Używa [biblioteki uwierzytelniania firmy Microsoft (MSAL) dla platformy .NET](msal-overview.md) do uzyskania tokenu dostępu dla programu Microsoft Graph.

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie od [programu Szybki start demona .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenariusz

Ponieważ aplikacja jest aplikacją wielodostępną dla klientów biznesowych firmy Microsoft, musi ona zapewnić klientom sposób "rejestracji" lub "łączenia" aplikacji z danymi firmy. Podczas przepływu połączenia administrator firmy najpierw udziela *uprawnień aplikacji* bezpośrednio do aplikacji, dzięki czemu może uzyskać dostęp do danych firmy w sposób nieinterakcyjny, bez obecności zalogowanego użytkownika. Większość logiki w tym przykładzie pokazuje, jak osiągnąć ten przepływ połączenia przy użyciu punktu końcowego [zgody administratora](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) platformy tożsamości.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Aby uzyskać więcej informacji na temat pojęć używanych w tym przykładzie, przeczytaj [dokumentację protokołu poświadczeń klienta dla punktu końcowego platformy tożsamości](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić przykład w tym przewodniku Szybki start, musisz:

- [Visual Studio 2017 lub 2019](https://visualstudio.microsoft.com/downloads/).
- Dzierżawa usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Jak uzyskać dzierżawę usługi Azure AD.](quickstart-create-new-tenant.md)
- Co najmniej jedno konto użytkownika w dzierżawie usługi Azure AD. Ten przykład nie będzie działać z kontem Microsoft (dawniej kontem usługi Windows Live). Jeśli zalogowano się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta Microsoft i nigdy nie utworzono konta użytkownika w katalogu, musisz to zrobić teraz.

## <a name="clone-or-download-this-repository"></a>Klonuj lub pobrać to repozytorium

W powłoce lub wierszu polecenia wprowadź to polecenie:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Lub [pobierz próbkę w pliku zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Ten przykład ma jeden projekt. Aby zarejestrować aplikację w dzierżawie usługi Azure AD, można:

- Wykonaj kroki opisane w [Rejestrze przykład z dzierżawą usługi Azure Active Directory](#register-your-application) i [konfiguruj próbkę, aby używać dzierżawy usługi Azure AD.](#choose-the-azure-ad-tenant)
- Użyj skryptów programu PowerShell, które:
  - *Automatycznie* utwórz aplikacje usługi Azure AD i powiązane obiekty (hasła, uprawnienia, zależności) dla Ciebie.
  - Modyfikowanie plików konfiguracyjnych projektów programu Visual Studio.

Jeśli chcesz korzystać z automatyzacji:

1. W systemie Windows uruchom program PowerShell i przejdź do katalogu głównego sklonowanego katalogu.
1. Uruchom następujące polecenie:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Uruchom skrypt, aby utworzyć aplikację usługi Azure AD i odpowiednio skonfigurować kod przykładowej aplikacji:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Inne sposoby uruchamiania skryptów są opisane w [skryptach tworzenia aplikacji](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Otwórz rozwiązanie programu Visual Studio i wybierz przycisk **Start,** aby uruchomić kod.

Jeśli nie chcesz używać automatyzacji, należy wykonać kroki opisane w poniższych sekcjach.

### <a name="choose-the-azure-ad-tenant"></a>Wybierz dzierżawę usługi Azure AD

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawie usługi Azure AD, wybierz swój profil w menu u góry strony, a następnie wybierz **pozycję Przełącz katalog**.
1. Zmień sesję portalu na żądaną dzierżawę usługi Azure AD.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Zarejestruj aplikację kliencką (dotnet-web-daemon-v2)

1. Przejdź do strony [Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) na platformie tożsamości firmy Microsoft dla deweloperów.
1. Wybierz **pozycję Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   - W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji. Na przykład wprowadź **dotnet-web-daemon-v2**.
   - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacji**.
   - W sekcji **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz pozycję **Web** w polu kombi i wprowadź następujące identyfikatory URI przekierowania:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     Jeśli istnieje więcej niż dwa identyfikatory URI przekierowania, należy dodać je z karty **Uwierzytelnianie** później, po pomyślnym utworzeniu aplikacji.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później. Będzie potrzebny do skonfigurowania pliku konfiguracji programu Visual Studio dla tego projektu.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**. Następnie:
   - W sekcji **Ustawienia zaawansowane** ustaw adres **URL wylogowywać** na **https://localhost:44316/Account/EndSession**.
   - W sekcji**Niejawne przyznanie** **ustawień** > zaawansowanych wybierz pozycję **Tokeny dostępu** i **tokeny identyfikatorów**. Ten przykład wymaga [niejawnego przepływu dotacji,](v2-oauth2-implicit-grant-flow.md) aby włączyć logowanie użytkownika i wywoływanie interfejsu API.
1. Wybierz pozycję **Zapisz**.
1. Na stronie **Certyfikaty & wpisy tajne** w sekcji **Wpisy tajne klienta** wybierz pozycję Nowy klucz tajny **klienta**. Następnie:

   1. Wprowadź opis klucza (na przykład **klucz tajny aplikacji),**
   1. Wybierz kluczowy czas trwania **w ciągu 1 roku**, W ciągu **2 lat**lub Nigdy nie **wygasa**.
   1. Wybierz przycisk **Add** (Dodaj).
   1. Gdy pojawi się wartość klucza, skopiuj ją i zapisz w bezpiecznym miejscu. Ten klucz będzie potrzebny później, aby skonfigurować projekt w programie Visual Studio. Nie będzie wyświetlany ponownie ani pobierany w żaden inny sposób.
1. Na liście stron aplikacji wybierz pozycję **Uprawnienia interfejsu API**. Następnie:
   1. Wybierz przycisk **Dodaj uprawnienia**.
   1. Upewnij się, że jest zaznaczona karta **Interfejsy API** firmy Microsoft.
   1. W sekcji **Często używane interfejsy API firmy Microsoft** wybierz pozycję Microsoft **Graph**.
   1. W sekcji **Uprawnienia aplikacji** upewnij się, że są zaznaczone odpowiednie uprawnienia: **User.Read.All**.
   1. Wybierz przycisk **Dodaj uprawnienia.**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurowanie przykładu do używania dzierżawy usługi Azure AD

W poniższych krokach **ClientID** jest taki sam jak "identyfikator aplikacji" lub **Identyfikator aplikacji.**

Otwórz rozwiązanie w programie Visual Studio, aby skonfigurować projekty.

### <a name="configure-the-client-project"></a>Konfigurowanie projektu klienta

Jeśli użyto skryptów konfiguracji, zostaną zastosowane następujące zmiany.

1. Otwórz plik **UserSync\Web.Config.**
1. Znajdź klucz aplikacji **ida:ClientId**. Zastąp istniejącą wartość identyfikatorem aplikacji **dotnet-web-daemon-v2** skopiowaną z witryny Azure portal.
1. Znajdź klucz aplikacji **ida:ClientSecret**. Zastąp istniejącą wartość kluczem zapisanym podczas tworzenia aplikacji **dotnet-web-demon-v2** w witrynie Azure portal.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Wyczyść rozwiązanie, odbuduj rozwiązanie, uruchom aplikację UserSync, a następnie zaloguj się jako administrator w dzierżawie usługi Azure AD. Jeśli nie masz dzierżawy usługi Azure AD do testowania, możesz [wykonać te instrukcje,](quickstart-create-new-tenant.md) aby ją uzyskać.

Po zalogowaniu się aplikacja najpierw prosi o pozwolenie na zalogowanie się i przeczytanie profilu użytkownika. Ta zgoda umożliwia aplikacji upewnienie się, że jesteś użytkownikiem biznesowym.

![Zgoda użytkownika](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Następnie aplikacja próbuje zsynchronizować listę użytkowników z dzierżawy usługi Azure AD za pośrednictwem programu Microsoft Graph. Jeśli nie, prosi cię (administratora dzierżawy), aby połączyć dzierżawę z aplikacją.

Następnie aplikacja prosi o pozwolenie na odczytanie listy użytkowników w dzierżawie.

![zgoda administratora](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Po udzieleniu uprawnień zostaniesz wylogowane z aplikacji. To wylogowanie gwarantuje, że wszystkie istniejące tokeny dostępu dla programu Microsoft Graph zostaną usunięte z pamięci podręcznej tokenów. Po ponownym zalogowaniu się, świeży token, który został uzyskany będzie miał uprawnienia niezbędne do nawiązywania wywołań programu Microsoft Graph.


Po udzieleniu uprawnień aplikacja może następnie wysyłać zapytania do użytkowników w dowolnym momencie. Możesz to sprawdzić, wybierając przycisk **Synchronizuj użytkowników** i odświeżając listę użytkowników. Spróbuj dodać lub usunąć użytkownika i ponownie zsynkować listę. (Należy jednak pamiętać, że aplikacja synchronizuje tylko pierwszą stronę użytkowników).

## <a name="about-the-code"></a>Informacje o kodzie

Odpowiedni kod dla tego przykładu znajduje się w następujących plikach:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Początkowe logowanie. W szczególności akcje na kontrolerze mają **autoryzujący** atrybut, który zmusza użytkownika do logowania. Aplikacja używa [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md) do logowania użytkownika.
- **Controllers\SyncController.cs**: Synchronizowanie listy użytkowników z lokalnym magazynem w pamięci.
- **Controllers\UserController.cs**: Wyświetlanie listy użytkowników z lokalnego magazynu w pamięci.
- **Controllers\AccountController.cs**: Uzyskiwanie uprawnień od administratora dzierżawy przy użyciu punktu końcowego zgody administratora.

## <a name="re-create-the-sample-app"></a>Ponowne tworzenie przykładowej aplikacji

1. W programie Visual Studio utwórz nowy projekt ASP.NET aplikacji sieci Web programu **Visual** **C#(.NET Framework).**
1. Na następnym ekranie wybierz szablon projektu **MVC.** Dodaj również odwołania do folderów i rdzeni dla **interfejsu API sieci Web,** ponieważ później dodasz kontroler interfejsu API sieci Web. Pozostaw wybrany tryb uwierzytelniania projektu jako domyślny: **Brak uwierzytelniania**.
1. Wybierz projekt w oknie **Eksploratora rozwiązań** i wybierz klucz **F4.**
1. We właściwościach projektu ustaw ustawienie **SSL włączone** jako **True**. Zanotuj informacje w **adresie URL SSL**. Będzie on potrzebny podczas konfigurowania rejestracji tej aplikacji w witrynie Azure portal.
1. Dodaj następujące pakiety ASP.NET oprogramowania pośredniczącego OWIN nuget:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. W folderze **App_Start:**
   1. Utwórz klasę o nazwie **Startup.Auth.cs**.
   1. Usuń **plik . App_Start** od nazwy obszaru nazw.
   1. Zastąp kod dla **Startup** klasy z kodem z tego samego pliku przykładowej aplikacji.
   Pamiętaj, aby wziąć całą definicję klasy. Definicja zmienia się z **uruchamiania klasy publicznej** na **publiczną klasę częściową Uruchamianie.**
1. W **Startup.Auth.cs**, rozwiąż brakujące odwołania, dodając **instrukcje** zgodnie z sugestią programu Visual Studio IntelliSense.
1. Kliknij prawym przyciskiem myszy projekt, wybierz polecenie **Dodaj**, a następnie wybierz polecenie **Klasa**.
1. W polu wyszukiwania wpisz **OWIN**. **Klasa uruchamiania OWIN** jest wyświetlana jako zaznaczenie. Zaznacz go i nazwij klasę **Startup.cs**.
1. W **Startup.cs**, zastąp kod dla **Startup** klasy z kodem z tego samego pliku przykładowej aplikacji. Ponownie należy zauważyć, że definicja zmienia się z **uruchamiania klasy publicznej** na **publiczną klasę częściową Uruchamianie**.
1. W folderze **Modele** dodaj nową klasę o nazwie **MsGraphUser.cs**. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
1. Dodaj nowy **kontroler MVC 5 — puste wystąpienie** o nazwie **AccountController**. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
1. Dodaj nowy **kontroler MVC 5 — puste wystąpienie** o nazwie **UserController**. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
1. Dodaj nowy **kontroler interfejsu API sieci Web 2 — puste wystąpienie** o nazwie **SyncController**. Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
1. W przypadku interfejsu użytkownika w folderze **Widoki\Konto** dodaj trzy **puste (bez modelu) wyświetlenia** o nazwie **GrantPermissions**, **Index**i **UserMismatch**. Dodaj i jeden o nazwie **Indeks** w folderze **Widoki\Użytkownik.** Zastąp implementację zawartością pliku o tej samej nazwie z przykładu.
1. Zaktualizuj **udostępniony\_layout.cshtml** i **Home\Index.cshtml,** aby poprawnie połączyć różne widoki ze sobą.

## <a name="deploy-the-sample-to-azure"></a>Wdrażanie próbki na platformie Azure

Ten projekt ma projekty aplikacji sieci web i interfejsu API sieci Web. Aby wdrożyć je w witrynach sieci Web platformy Azure, należy wykonać następujące kroki dla każdego z nich:

1. Tworzenie witryny sieci Web platformy Azure.
1. Opublikuj aplikację sieci Web i interfejsy API sieci Web w witrynie sieci Web.
1. Zaktualizuj klientów, aby zadzwonić do witryny sieci Web zamiast usługi IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Tworzenie i publikowanie dotnet-web-daemon-v2 w witrynie sieci Web platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu wybierz pozycję **Utwórz zasób**.
1. Wybierz **pozycję Web** > **Web App**, a następnie nadaj swojej witrynie nazwę. Na przykład nadaj jej nazwę **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Wybierz informacje o **subskrypcji,** **grupie zasobów**oraz **planie i lokalizacji usługi aplikacji**. **System operacyjny** to **Windows**, a **Publish** to **Code**.
1. Wybierz **pozycję Utwórz** i poczekaj na utworzenie usługi aplikacji.
1. Po otrzymaniu powiadomienia **o pomyślnym wdrożeniu** wybierz pozycję **Przejdź do zasobu,** aby przejść do nowo utworzonej usługi aplikacji.
1. Po utworzeniu witryny sieci Web znajdź ją na **pulpicie nawigacyjnym** i wybierz ją, aby otworzyć ekran **Przegląd** usługi aplikacji.
1. Na karcie **Przegląd** usługi aplikacji pobierz profil publikowania, wybierając łącze **Pobierz profil publikowania** i zapisz go. Można użyć innych mechanizmów wdrażania, takich jak wdrażanie z kontroli źródła.
1. Przełącz się do programu Visual Studio, a następnie:
   1. Przejdź do projektu **dotnet-web-daemon-v2.**
   1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie wybierz polecenie **Publikuj**.
   1. Wybierz **pozycję Zaimportuj profil** na dolnym pasku i zaimportuj wcześniej pobrany profil publikowania.
1. Wybierz pozycję **Konfiguruj**.
1. Na karcie **Połączenie** zaktualizuj docelowy adres URL, aby używał "https". Na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)użyj pliku . Wybierz opcję **Dalej**.
1. Na karcie **Ustawienia** upewnij się, że **opcja Włącz uwierzytelnianie organizacyjne** jest wyczyszczona.
1. Wybierz pozycję **Zapisz**. Wybierz **pozycję Publikuj** na ekranie głównym.

Visual Studio opublikuje projekt i automatycznie otworzy przeglądarkę do adresu URL projektu. Jeśli zostanie wyświetlonych domyślna strona internetowa projektu, publikacja zakończyła się pomyślnie.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizowanie rejestracji aplikacji dzierżawy usługi Azure AD dla dotnet-web-demon-v2

1. Wróć do witryny [Azure Portal](https://portal.azure.com).
1. W lewym okienku wybierz usługę **Azure Active Directory,** a następnie wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację **dotnet-web-demon-v2.**
1. Na stronie **Uwierzytelnianie** aplikacji zaktualizuj pola **adresu URL wylogowania** o adres usługi. Na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)użyj pliku .
1. W menu **Znakowanie** zaktualizuj **adres URL strony głównej** na adres usługi. Na przykład [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)użyj pliku .
1. Zapisz konfigurację.
1. Dodaj ten sam adres URL na liście wartości menu**URI przekierowania** **uwierzytelniania.** >  Jeśli masz wiele adresów URL przekierowań, upewnij się, że istnieje nowy wpis, który używa identyfikatora URI usługi aplikacji dla każdego adresu URL przekierowania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebne, usuń obiekt aplikacji, który został utworzony w [kroku Zarejestruj aplikację.](#register-your-application)  Aby usunąć aplikację, postępuj zgodnie z instrukcjami w [obszarze Usuń aplikację autorstwa użytkownika lub organizacji](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Uzyskiwanie pomocy

Użyj [przepełnienia stosu,](http://stackoverflow.com/questions/tagged/msal) aby uzyskać wsparcie od społeczności.
Najpierw zadaj pytania w usłudze Przepełnienie stosu i przejrzyj istniejące problemy, aby sprawdzić, czy ktoś zadał Ci pytanie wcześniej.
Upewnij się, że twoje pytania lub komentarze są oznaczone tagami "adal", "msal" i "dotnet".

Jeśli znajdziesz błąd w przykładzie, posuń problem na [temat problemów z GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Jeśli znajdziesz błąd w MSAL.NET, poruszyj problem na [MSAL.NET Problemy z GitHubem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Aby przedstawić zalecenie, przejdź do [strony Głos użytkownika](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o różnych [przepływach uwierzytelniania i scenariuszach aplikacji,](authentication-flows-app-scenarios.md) które obsługuje platforma tożsamości firmy Microsoft.

Aby uzyskać więcej informacji, zobacz następującą dokumentację koncepcyjną:

- [Dzierżawa w usłudze Azure Active Directory](single-and-multi-tenant-apps.md)
- [Opis środowisk zgody dla aplikacji usługi Azure AD](application-consent-experience.md)
- [Zaloguj się dowolnego użytkownika usługi Azure Active Directory przy użyciu wzorca aplikacji wielodostępnego](howto-convert-app-to-be-multi-tenant.md)
- [Zrozumienie zgody użytkownika i administratora](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory](app-objects-and-service-principals.md)
- [Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)
- [Szybki start: konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
- [Pobieranie tokenu dla aplikacji z przepływami poświadczeń klienta](msal-client-applications.md)

Aby uzyskać prostszą wielodożerną aplikację demona konsoli, zobacz [szybki start demona .NET Core](quickstart-v2-netcore-daemon.md).
