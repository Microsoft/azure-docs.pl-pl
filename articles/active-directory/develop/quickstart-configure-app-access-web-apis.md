---
title: 'Szybki start: uzyskiwanie dostępu do internetowych interfejsów API aplikacji — platforma tożsamości firmy Microsoft | Azure'
description: W tym przewodniku Szybki start skonfiguruj aplikację zarejestrowaną na platformie tożsamości firmy Microsoft w taki sposób, aby uwzględniała identyfikatory URI przekierowania, poświadczenia lub uprawnienia dostępu do internetowych interfejsów API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082326"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Szybki start: konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do internetowych interfejsów API

W tym przewodniku Szybki start należy dodać identyfikatory URI przekierowania, poświadczenia lub uprawnienia dostępu do interfejsów API sieci Web dla aplikacji. Aplikacja klienta sieci web lub poufne musi ustanowić bezpieczne poświadczenia do udziału w przepływie udzielania autoryzacji, który wymaga uwierzytelniania. Domyślną metodą uwierzytelniania obsługiwaną w witrynie Azure Portal jest użycie identyfikatora klienta i klucza tajnego. Aplikacja uzyskuje token dostępu podczas tego procesu.

Zanim klient będzie mógł uzyskać dostęp do internetowego interfejsu API udostępnianego przez aplikację zasobów, taką jak interfejs API programu Microsoft Graph, struktura zgody zapewnia, że klient uzyskuje udzielić uprawnień wymaganych dla żądanych uprawnień. Domyślnie wszystkie aplikacje mogą żądać uprawnień z interfejsu API programu Microsoft Graph.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [programu Szybki start: zarejestruj aplikację na platformie tożsamości firmy Microsoft](quickstart-register-app.md).
* Przegląd [uprawnień i zgody w punkcie końcowym platformy tożsamości firmy Microsoft](v2-permissions-and-consent.md).
* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli twoje konto daje ci dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. W obszarze **Zarządzanie**wybierz pozycję **Rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zostanie wyświetlona **strona przeglądowa** aplikacji lub główna rejestracja.

Poniższe procedury można skonfigurować w celu uzyskania dostępu do interfejsów API sieci Web.

## <a name="add-redirect-uris-to-your-application"></a>Dodawanie identyfikatorów URI przekierowania do aplikacji

Można dodać niestandardowe identyfikatory URI przekierowania i sugerowane przekierowanie identyfikatorów URI do aplikacji. Aby dodać niestandardowy identyfikator URI przekierowania dla aplikacji klienckich sieci Web i publicznych:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **Uwierzytelnianie**.
1. Zlokalizuj **identyfikatory URI przekierowania**. Może być konieczne **wybranie opcji Przełącz do starego środowiska**.
1. Wybierz typ budowanej aplikacji: **Web** lub **Public client/native (mobile & desktop)**.
1. Podaj identyfikator URI przekierowania dla aplikacji.

   * Dla aplikacji internetowych podaj podstawowy adres URL aplikacji. Na przykład ciąg `http://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą używać tego adresu URL, aby zalogować się do internetowej aplikacji klienckiej.
   * W przypadku aplikacji publicznych podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla `https://MyFirstApp`aplikacji, na przykład: .
1. Wybierz pozycję **Zapisz**.

Aby wybrać sugerowane identyfikatory URI przekierowania dla klientów publicznych, wykonaj następujące kroki:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **Uwierzytelnianie**.
1. Znajdź **sugerowane identyfikatory URI przekierowania dla klientów publicznych (mobilnych, stacjonarnych).** Może być konieczne **wybranie opcji Przełącz do starego środowiska**.
1. Wybierz jeden lub więcej identyfikatorów URI przekierowania dla aplikacji. Można również wprowadzić niestandardowy identyfikator URI przekierowania. Jeśli nie masz pewności, czego użyć, zapoznaj się z dokumentacją biblioteki.
1. Wybierz pozycję **Zapisz**.

Niektóre ograniczenia mają zastosowanie do przekierowania identyfikatorów URI. Aby uzyskać więcej informacji, zobacz [Przekierowywanie ograniczeń i ograniczeń adresów URL identyfikatorów URI/odpowiedzi](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Wypróbuj nowe środowisko ustawień **uwierzytelniania,** w którym możesz skonfigurować ustawienia aplikacji na podstawie platformy lub urządzenia, na które chcesz kierować reklamy.
>
> Aby wyświetlić ten widok, wybierz pozycję **Wypróbuj nowe środowisko** ze strony **Uwierzytelnianie.**
>
> ![Kliknij "Wypróbuj nowe środowisko", aby wyświetlić widok konfiguracji platformy](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Spowoduje to przejście do [nowej strony **konfiguracji platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurowanie ustawień zaawansowanych dla aplikacji

W zależności od zarejestrowanej aplikacji istnieje kilka dodatkowych ustawień, które mogą być potrzebne do skonfigurowania, takie jak:

* **Adres URL wylogowania**.
* W przypadku aplikacji jednostronicowych można włączyć **niejawne udzielanie** dotacji i wybrać tokeny, które mają być wystawiane przez punkt końcowy autoryzacji.
* W przypadku aplikacji klasycznych, które nabywają tokeny przy użyciu zintegrowanego uwierzytelniania systemu Windows, przepływu kodu urządzenia lub nazwy użytkownika/hasła w sekcji **Domyślny typ klienta,** ustaw ustawienie **Traktuj aplikację jako klienta publicznego** na **Tak**.
* W przypadku starszych aplikacji, które integrowały się z usługą konta Microsoft przy użyciu live SDK, skonfiguruj **obsługę live SDK**. Nowe aplikacje nie wymagają tego ustawienia.
* **Domyślny typ klienta**.
* **Obsługiwane typy kont**.

### <a name="modify-supported-account-types"></a>Modyfikowanie obsługiwanych typów kont

**Obsługiwane typy kont** określają, kto może korzystać z aplikacji lub uzyskać dostęp do interfejsu API.

Jeśli typy obsługiwanych kont zostały skonfigurowane podczas rejestracji aplikacji, można zmienić to ustawienie tylko za pomocą edytora manifestów aplikacji, jeśli:

* Typy kont można zmienić z **AzureADMyOrg** lub **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**, lub odwrotnie, lub
* Typy kont można zmienić z **AzureADMyOrg** na **AzureADMultipleOrgs**lub odwrotnie.

Aby zmienić obsługiwane typy kont dla istniejącej rejestracji aplikacji, zaktualizuj `signInAudience` klucz. Aby uzyskać więcej informacji, zobacz [Konfigurowanie manifestu aplikacji](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurowanie ustawień platformy dla aplikacji

![Konfigurowanie ustawień aplikacji na podstawie platformy lub urządzenia](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Aby skonfigurować ustawienia aplikacji na podstawie platformy lub urządzenia, kierowane są następujące opcje:

1. Na stronie **Konfiguracje platformy** wybierz pozycję **Dodaj platformę** i wybierz jedną z dostępnych opcji.

   ![Pokazuje stronę Konfigurowanie platform](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Wprowadź informacje o ustawieniach na podstawie wybranej platformy.

   | Platforma                | Ustawienia konfiguracji            |
   |-------------------------|-----------------------------------|
   | **Sieć Web**              | Wprowadź **identyfikator URI przekierowania** dla aplikacji. |
   | **iOS / macOS**              | Wpisz identyfikator **pakietu**aplikacji , który znajdziesz w XCode w Info.plist lub Ustawienia kompilacji. Dodanie identyfikatora pakietu automatycznie tworzy identyfikator URI przekierowania dla aplikacji. |
   | **Android**          | Podaj **nazwę pakietu**aplikacji , którą można znaleźć w pliku AndroidManifest.xml.<br/>Generowanie i wprowadzanie **skrótu Podpis**. Dodanie skrótu podpisu automatycznie tworzy identyfikator URI przekierowania dla aplikacji.  |
   | **Aplikacje mobilne i stacjonarne**  | Element opcjonalny. Wybierz jedną z zalecanych **sugerowanych identyfikatorów URI przekierowania,** jeśli budujesz aplikacje na komputery i urządzenia.<br/>Element opcjonalny. Wprowadź **niestandardowy identyfikator URI przekierowania**, który jest używany jako lokalizacja, w której usługa Azure AD przekieruje użytkowników w odpowiedzi na żądania uwierzytelniania. Na przykład dla aplikacji .NET Core, `http://localhost`w których chcesz interakcję, użyj . |

   > [!NOTE]
   > W usługach federacyjnych Active Directory (AD FS) i usłudze Azure AD B2C należy również określić numer portu.  Na przykład: `http://localhost:1234`. 

   > [!IMPORTANT]
   > W przypadku aplikacji mobilnych, które nie korzystają z najnowszej biblioteki uwierzytelniania firmy Microsoft (MSAL) lub nie korzystają z brokera, należy skonfigurować identyfikatory URI przekierowania dla tych aplikacji w **programach Desktop + devices**.

W zależności od wybranej platformy mogą istnieć dodatkowe ustawienia, które można skonfigurować. W przypadku aplikacji **sieci Web** można:

* Dodaj więcej identyfikatorów URI przekierowania
* Skonfiguruj **niejawne udzielanie dotacji,** aby wybrać tokeny, które mają być wystawiane przez punkt końcowy autoryzacji:

  * W przypadku aplikacji jednostronicowych wybierz zarówno **tokeny programu Access,** jak i **tokeny identyfikatorów**
  * W przypadku aplikacji sieci Web wybierz **tokeny identyfikatorów**

## <a name="add-credentials-to-your-web-application"></a>Dodawanie poświadczeń do aplikacji internetowej

Aby dodać poświadczenia do aplikacji sieci web, dodaj certyfikat lub utwórz klucz tajny klienta. Aby dodać certyfikat, wykonaj następujące czynności:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty & wpisy tajne.**
1. Wybierz pozycję **Przekaż certyfikat**.
1. Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
1. Wybierz pozycję **Dodaj**.

Aby dodać klucz tajny klienta:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty & wpisy tajne.**
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Dodaj opis wpisu tajnego klienta.
1. Wybierz czas trwania.
1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Po zapisaniu zmian w konfiguracji kolumna najdalej z prawej strony będzie zawierać wartość wpisu tajnego klienta. **Pamiętaj o skopiowaniu wartości** do użycia w kodzie aplikacji klienckiej, ponieważ nie będą one dostępne po opuszczeniu tej strony.

## <a name="add-permissions-to-access-web-apis"></a>Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API

Domyślnie jest zaznaczone [uprawnienie logowania i odczytu profilu użytkownika](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) interfejsu API wykresu. Można wybrać jeden z [dwóch typów uprawnień](developer-glossary.md#permissions) dla każdego internetowego interfejsu API:

* **Uprawnienia aplikacji**. Aplikacja kliencka musi uzyskać dostęp do internetowego interfejsu API bezpośrednio jako siebie, bez kontekstu użytkownika. Ten typ uprawnień wymaga zgody administratora. To uprawnienie nie jest dostępne dla aplikacji klienckich dla komputerów i urządzeń przenośnych.
* **Uprawnienia delegowane**. twoja aplikacja kliencka musi mieć dostęp do internetowego interfejsu API w imieniu zalogowanego użytkownika, ale z dostępem ograniczonym przez wybrane uprawnienie. Tego rodzaju uprawnienie może być udzielane przez użytkownika, chyba że wymaga ono zgody administratora.

  > [!NOTE]
  > Dodanie uprawnienia delegowanego do aplikacji nie powoduje automatycznego udzielenia zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrażać zgodę na dodane uprawnienia delegowane w czasie wykonywania, chyba że administrator wyrazi zgodę w imieniu wszystkich użytkowników.

Aby dodać uprawnienia dostępu do interfejsów API zasobów od klienta:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **Uprawnienia interfejsu API**.
1. W obszarze **Skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Domyślnie widok umożliwia wybranie **interfejsów API firmy Microsoft**. Wybierz sekcję interfejsów API, która Cię interesuje:

    * **Interfejsy API firmy Microsoft**. Umożliwia wybranie uprawnień dla interfejsów API firmy Microsoft, takich jak Microsoft Graph.
    * **Interfejsy API używane przez moją organizację**. Umożliwia wybranie uprawnień do interfejsów API udostępnianych przez organizację lub interfejsów API, z którymi organizacja została zintegrowana.
    * **Moje interfejsy API**. Umożliwia wybranie uprawnień do interfejsów API, które można udostępnić.

1. Po wybraniu interfejsów API zobaczysz stronę **Żądanie uprawnień interfejsu API**. Jeśli interfejs API udostępnia uprawnienia delegowane i aplikacji, wybierz typ uprawnień, którego potrzebuje Twoja aplikacja.
1. Po zakończeniu wybierz pozycję **Dodaj uprawnienia**.

Powrót do strony **uprawnień interfejsu API.** Uprawnienia zostały zapisane i dodane do tabeli.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Opis uprawnień interfejsu API i interfejsu użytkownika zgody administratora

### <a name="configured-permissions"></a>Skonfigurowane uprawnienia

W tej sekcji przedstawiono uprawnienia, które zostały jawnie skonfigurowane w obiekcie aplikacji. Te uprawnienia są częścią listy dostępu do zasobów wymaganych aplikacji. Uprawnienia można dodawać lub usuwać z tej tabeli. Jako administrator możesz również udzielić lub odwołać zgodę administratora na zestaw uprawnień interfejsu API lub indywidualnych uprawnień.

### <a name="other-permissions-granted"></a>Inne przyznane uprawnienia

Jeśli aplikacja jest zarejestrowana w dzierżawie, może zostać wyświetlona dodatkowa sekcja **zatytułowana Inne uprawnienia przyznane dzierżawcy.** W tej sekcji przedstawiono uprawnienia przyznane dzierżawcy, które nie zostały jawnie skonfigurowane w obiekcie aplikacji. Uprawnienia te były dynamicznie wymagane i wyrażane zgodę. Ta sekcja jest wyświetlana tylko wtedy, gdy ma zastosowanie co najmniej jedno uprawnienie.

Do sekcji **Skonfigurowane uprawnienia** można dodać zestaw uprawnień interfejsu API lub indywidualnych uprawnień wyświetlanych w tej sekcji. Jako administrator możesz również odwołać zgodę administratora dla poszczególnych interfejsów API lub uprawnień w tej sekcji.

### <a name="admin-consent-button"></a>Przycisk Zgoda administratora

Jeśli aplikacja jest zarejestrowana w dzierżawie, zostanie wyświetlony **grant zgody administratora dla dzierżawy** przycisku. Jest wyłączona, jeśli nie jesteś administratorem lub jeśli nie skonfigurowano żadnych uprawnień dla aplikacji.
Ten przycisk umożliwia administratorowi udzielenie zgody administratora na uprawnienia skonfigurowane dla aplikacji. Kliknięcie przycisku zgody administratora powoduje uruchomienie nowego okna z monitem o zgodę przedstawiającym wszystkie skonfigurowane uprawnienia.

> [!NOTE]
> Występuje opóźnienie między uprawnieniami skonfigurowane dla aplikacji i ich pojawiające się w wierszu zgody. Jeśli nie widzisz wszystkich skonfigurowanych uprawnień w wierszu zgody, zamknij go i uruchom ponownie.

Jeśli masz uprawnienia, które zostały przyznane, ale nie skonfigurowane, przycisk zgody administratora monituje o obsługę tych uprawnień. Można dodać je do skonfigurowanych uprawnień lub można je usunąć.

Monit o zgodę umożliwia **opcję Zaakceptuj** lub **Anuluj**. Wybierz **pozycję Zaakceptuj,** aby udzielić zgody administratora. Jeśli wybierzesz **opcję Anuluj,** zgoda administratora nie zostanie udzielona. Komunikat o błędzie stwierdza, że zgoda została odrzucona.

> [!NOTE]
> Istnieje opóźnienie między udzieleniem zgody administratora przez **wybranie opcji Zaakceptuj** w wierszu zgody a odzwierciedleniem stanu zgody administratora w portalu.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak udostępnić internetowe interfejsy API.
> [!div class="nextstepaction"]
> [Szybki start: konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md)

* Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

* Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).

* [Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)

* [Szybki start: modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)

* [Szybki start: usuwanie aplikacji zarejestrowanej na platformie tożsamości firmy Microsoft](quickstart-remove-app.md)
