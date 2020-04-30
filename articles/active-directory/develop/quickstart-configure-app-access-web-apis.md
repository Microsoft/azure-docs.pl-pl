---
title: 'Szybki Start: dostęp do interfejsów API sieci Web dla aplikacji App-Microsoft Identity platform | Azure'
description: W tym przewodniku szybki start Skonfiguruj aplikację zarejestrowaną na platformie tożsamości firmy Microsoft w celu uwzględnienia identyfikatorów URI przekierowania, poświadczeń lub uprawnień dostępu do interfejsów API sieci Web.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82082326"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web

W tym przewodniku szybki start dowiesz się, jak dodać identyfikatory URI przekierowania, poświadczenia lub uprawnienia, aby uzyskać dostęp do interfejsów API sieci Web aplikacji. Sieć Web lub poufna aplikacja kliencka musi ustanowić bezpieczne poświadczenia do udziału w przepływie przydzielenia autoryzacji, który wymaga uwierzytelniania. Domyślną metodą uwierzytelniania obsługiwaną w witrynie Azure Portal jest użycie identyfikatora klienta i klucza tajnego. Aplikacja uzyskuje token dostępu w trakcie tego procesu.

Aby klient mógł uzyskać dostęp do internetowego interfejsu API uwidocznionego przez aplikację zasobów, taką jak Microsoft Graph API, struktura zgody gwarantuje, że klient uzyskuje uprawnienia wymagane dla żądanych uprawnień. Domyślnie wszystkie aplikacje mogą żądać uprawnień z interfejsu API Microsoft Graph.

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [przewodnika Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).
* Przegląd [uprawnień i zgody w punkcie końcowym platformy tożsamości firmy Microsoft](v2-permissions-and-consent.md).
* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Skonfiguruj sesję portalu do dzierżawy usługi Azure AD, której chcesz użyć.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zostanie wyświetlona strona **Omówienie** lub główna Rejestracja aplikacji.

Poniższe procedury służą do konfigurowania aplikacji w celu uzyskiwania dostępu do interfejsów API sieci Web.

## <a name="add-redirect-uris-to-your-application"></a>Dodawanie identyfikatorów URI przekierowania do aplikacji

Do aplikacji można dodawać niestandardowe identyfikatory URI przekierowań oraz sugerowane identyfikatory URI przekierowań. Aby dodać niestandardowy identyfikator URI przekierowania dla aplikacji sieci Web i publicznych klientów:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **uwierzytelnianie**.
1. Lokalizowanie **identyfikatorów URI przekierowania**. Może być konieczne wybranie opcji **Przełącz do starego środowiska**.
1. Wybierz typ kompilowanej aplikacji: klient **internetowy** lub **publiczny/natywny (Mobile & Desktop)**.
1. Podaj identyfikator URI przekierowania dla aplikacji.

   * Dla aplikacji internetowych podaj podstawowy adres URL aplikacji. Na przykład ciąg `http://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą używać tego adresu URL, aby zalogować się do internetowej aplikacji klienckiej.
   * W przypadku aplikacji publicznych podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla Twojej aplikacji, na przykład: `https://MyFirstApp`.
1. Wybierz pozycję **Zapisz**.

Aby wybrać spośród sugerowanych identyfikatorów URI przekierowań dla klientów publicznych, wykonaj następujące czynności:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **uwierzytelnianie**.
1. Znajdź **sugerowane identyfikatory URI przekierowań dla klientów publicznych (Mobile, Desktop)**. Może być konieczne wybranie opcji **Przełącz do starego środowiska**.
1. Wybierz co najmniej jeden identyfikator URI przekierowania dla aplikacji. Możesz również wprowadzić niestandardowy identyfikator URI przekierowania. Jeśli nie masz pewności co do użycia, zobacz dokumentację biblioteki.
1. Wybierz pozycję **Zapisz**.

Istnieją pewne ograniczenia dotyczące identyfikatorów URI przekierowania. Aby uzyskać więcej informacji, zobacz [ograniczenia i ograniczenia adresów URL przekierowania URI/odpowiedź](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Wypróbuj nowe ustawienia **uwierzytelniania** , w którym można skonfigurować ustawienia dla aplikacji na podstawie platformy lub urządzenia, które mają być docelowe.
>
> Aby wyświetlić ten widok, wybierz opcję **Wypróbuj nowe środowisko** na stronie **uwierzytelnianie** .
>
> ![Kliknij pozycję "Wypróbuj nowe środowisko", aby wyświetlić widok konfiguracji platformy](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Spowoduje to przejście do [strony nowej **konfiguracji platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Skonfiguruj ustawienia zaawansowane dla aplikacji

W zależności od używanej aplikacji istnieją pewne dodatkowe ustawienia, które mogą być potrzebne do skonfigurowania, takie jak:

* **Adres URL wylogowywania**.
* W przypadku aplikacji jednostronicowych można włączyć **niejawną zgodę** i wybrać tokeny, które mają zostać wystawione przez punkt końcowy autoryzacji.
* W przypadku aplikacji klasycznych, które uzyskują tokeny przy użyciu zintegrowanego uwierzytelniania systemu Windows, przepływu kodu urządzenia lub nazwy użytkownika/hasła w sekcji **domyślny typ klienta** , ustaw wartość **tak**dla ustawienia **Traktuj aplikację jako klienta publicznego** .
* Aby przeprowadzić integrację z usługą konto Microsoft przy użyciu zestawu Live SDK, skonfiguruj **obsługę zestawu SDK na żywo**. Nowe aplikacje nie wymagają tego ustawienia.
* **Domyślny typ klienta**.
* **Obsługiwane typy kont**.

### <a name="modify-supported-account-types"></a>Modyfikuj obsługiwane typy kont

**Obsługiwane typy kont** określają, kto może korzystać z aplikacji lub uzyskać dostęp do interfejsu API.

Jeśli podczas rejestrowania aplikacji skonfigurowano obsługiwane typy kont, można zmienić to ustawienie tylko przy użyciu edytora manifestu aplikacji, jeśli:

* Zmiany typów kont można zmienić z **AzureADMyOrg** lub **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**, a także w inny sposób.
* Należy zmienić typy kont z **AzureADMyOrg** na **AzureADMultipleOrgs**, lub odwrotnie.

Aby zmienić obsługiwane typy kont dla istniejącej rejestracji aplikacji, zaktualizuj `signInAudience` klucz. Aby uzyskać więcej informacji, zobacz [Konfigurowanie manifestu aplikacji](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurowanie ustawień platformy dla aplikacji

![Skonfiguruj ustawienia aplikacji na podstawie platformy lub urządzenia](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Aby skonfigurować ustawienia aplikacji zależnie od platformy lub urządzenia, jesteś celem:

1. Na stronie **konfiguracje platformy** wybierz opcję **Dodaj platformę** , a następnie wybierz jedną z dostępnych opcji.

   ![Pokazuje stronę Konfigurowanie platform](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Wprowadź informacje o ustawieniach w oparciu o wybraną platformę.

   | Platforma                | Ustawienia konfiguracji            |
   |-------------------------|-----------------------------------|
   | **Sieć Web**              | Wprowadź **Identyfikator URI przekierowania** dla aplikacji. |
   | **iOS/macOS**              | Wprowadź **Identyfikator pakietu**aplikacji, który można znaleźć w Xcode w oknie info. plist lub ustawienia kompilacji. Dodanie identyfikatora pakietu powoduje automatyczne utworzenie identyfikatora URI przekierowania dla aplikacji. |
   | **Android**          | Podaj **nazwę pakietu**aplikacji, którą można znaleźć w pliku pliku AndroidManifest. XML.<br/>Generowanie i wprowadzanie **skrótu podpisu**. Dodanie skrótu podpisu powoduje automatyczne utworzenie identyfikatora URI przekierowania dla aplikacji.  |
   | **Aplikacje mobilne i klasyczne**  | Opcjonalny. Wybierz jeden z zalecanych **sugerowanych identyfikatorów URI przekierowania** , jeśli tworzysz aplikacje dla komputerów stacjonarnych i urządzeń.<br/>Opcjonalny. Wprowadź **niestandardowy identyfikator URI przekierowania**, który jest używany jako lokalizacja, w której usługa Azure AD będzie przekierowywać użytkowników w odpowiedzi na żądania uwierzytelniania. Na przykład w przypadku aplikacji .NET Core, w których chcesz współdziałać, użyj `http://localhost`programu. |

   > [!NOTE]
   > Na Active Directory Federation Services (AD FS) i Azure AD B2C należy również określić numer portu.  Na przykład: `http://localhost:1234`. 

   > [!IMPORTANT]
   > W przypadku aplikacji mobilnych, które nie używają najnowszej biblioteki uwierzytelniania firmy Microsoft (MSAL) lub nie używają brokera, należy skonfigurować identyfikatory URI przekierowania dla tych aplikacji na **komputerach stacjonarnych i urządzeniach**.

W zależności od wybranej platformy mogą istnieć dodatkowe ustawienia, które można skonfigurować. W przypadku usługi **Web** Apps można:

* Dodaj więcej identyfikatorów URI przekierowania
* Skonfiguruj **niejawną zgodę** na wybór tokenów, które mają być wystawione przez punkt końcowy autoryzacji:

  * W przypadku aplikacji jednostronicowych wybierz zarówno **tokeny dostępu** , jak i **tokeny identyfikatora**
  * W przypadku aplikacji sieci Web wybierz pozycję **identyfikatory tokenów**

## <a name="add-credentials-to-your-web-application"></a>Dodawanie poświadczeń do aplikacji internetowej

Aby dodać poświadczenie do aplikacji sieci Web, Dodaj certyfikat lub Utwórz klucz tajny klienta. Aby dodać certyfikat, wykonaj następujące czynności:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty & wpisy tajne** .
1. Wybierz pozycję **Przekaż certyfikat**.
1. Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
1. Wybierz pozycję **Dodaj**.

Aby dodać klucz tajny klienta:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty & wpisy tajne** .
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Dodaj opis wpisu tajnego klienta.
1. Wybierz czas trwania.
1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Po zapisaniu zmian w konfiguracji kolumna najdalej z prawej strony będzie zawierać wartość wpisu tajnego klienta. **Pamiętaj o skopiowaniu wartości** do użycia w kodzie aplikacji klienckiej, ponieważ nie będą one dostępne po opuszczeniu tej strony.

## <a name="add-permissions-to-access-web-apis"></a>Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API

[Uprawnienie interfejs API programu Graph logowanie i odczyt profilu użytkownika](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) jest domyślnie zaznaczone. Dla każdego internetowego interfejsu API można wybrać jedną z [dwóch typów uprawnień](developer-glossary.md#permissions) :

* **Uprawnienia aplikacji**. Aplikacja kliencka musi uzyskać dostęp do internetowego interfejsu API bezpośrednio, bez kontekstu użytkownika. Ten typ uprawnień wymaga zgody administratora. To uprawnienie nie jest dostępne dla aplikacji klienckich dla komputerów stacjonarnych i mobilnych.
* **Delegowane uprawnienia**. twoja aplikacja kliencka musi mieć dostęp do internetowego interfejsu API w imieniu zalogowanego użytkownika, ale z dostępem ograniczonym przez wybrane uprawnienie. Tego rodzaju uprawnienie może być udzielane przez użytkownika, chyba że wymaga ono zgody administratora.

  > [!NOTE]
  > Dodanie uprawnienia delegowanego do aplikacji nie powoduje automatycznego udzielenia zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrażać zgodę na dodane uprawnienia delegowane w czasie wykonywania, chyba że administrator wyrazi zgodę w imieniu wszystkich użytkowników.

Aby dodać uprawnienia dostępu do interfejsów API zasobów z klienta:

1. Na stronie **Przegląd** aplikacji wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Domyślnie widok umożliwia wybranie **interfejsów API firmy Microsoft**. Wybierz sekcję interfejsów API, która Cię interesuje:

    * **Interfejsy API firmy Microsoft**. Umożliwia wybranie uprawnień do interfejsów API firmy Microsoft, takich jak Microsoft Graph.
    * **Interfejsy API stosowane przez moją organizację**. Umożliwia wybranie uprawnień do interfejsów API udostępnianych przez organizację lub interfejsów API, z którymi organizacja jest zintegrowana.
    * **Moje interfejsy API**. Umożliwia wybranie uprawnień dla udostępnianych interfejsów API.

1. Po wybraniu interfejsów API zobaczysz stronę **Żądanie uprawnień interfejsu API**. Jeśli interfejs API udostępnia uprawnienia delegowane i aplikacji, wybierz typ uprawnień, którego potrzebuje Twoja aplikacja.
1. Po zakończeniu wybierz pozycję **Dodaj uprawnienia**.

Powrócisz do strony **uprawnień interfejsu API** . Uprawnienia zostały zapisane i dodane do tabeli.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Informacje o uprawnieniach interfejsu API i interfejsie użytkownika zgody administratora

### <a name="configured-permissions"></a>Skonfigurowane uprawnienia

W tej sekcji przedstawiono uprawnienia, które zostały jawnie skonfigurowane dla obiektu aplikacji. Te uprawnienia są częścią wymaganej listy dostępu do zasobów aplikacji. Możesz dodawać lub usuwać uprawnienia z tej tabeli. Jako administrator możesz również udzielić lub odwołać zgodę administratora na zestaw uprawnień lub poszczególnych uprawnień interfejsu API.

### <a name="other-permissions-granted"></a>Inne uprawnienia przyznane

Jeśli aplikacja jest zarejestrowana w dzierżawie, może zostać wyświetlona dodatkowa sekcja zatytułowana **inne uprawnienia udzielone dzierżawcy**. W tej sekcji przedstawiono uprawnienia przyznane dzierżawcy, które nie zostały jawnie skonfigurowane dla obiektu aplikacji. Te uprawnienia są dynamicznie wymagane i wyrażane. Ta sekcja pojawia się tylko wtedy, gdy istnieje co najmniej jedno uprawnienie, które ma zastosowanie.

Można dodać zestaw uprawnień interfejsu API lub poszczególnych uprawnień, które są wyświetlane w tej sekcji, do sekcji **skonfigurowane uprawnienia** . Jako administrator możesz również odwołać zgodę administratora dla poszczególnych interfejsów API lub uprawnień w tej sekcji.

### <a name="admin-consent-button"></a>Przycisk zgody administratora

Jeśli aplikacja jest zarejestrowana w dzierżawie, zobaczysz przycisk **Udziel zgody administratora dla dzierżawy** . Ta wartość jest wyłączona, jeśli nie jesteś administratorem lub nie skonfigurowano żadnych uprawnień dla aplikacji.
Ten przycisk umożliwia administratorowi udzielenie zgody administratora na uprawnienia skonfigurowane dla aplikacji. Kliknięcie przycisku zgody administratora powoduje uruchomienie nowego okna z monitem o wyrażenie zgody, na którym są wyświetlane wszystkie skonfigurowane uprawnienia.

> [!NOTE]
> Istnieje opóźnienie między skonfigurowanymi uprawnieniami aplikacji i wyświetlanymi w monicie o zgodę. Jeśli nie widzisz wszystkich skonfigurowanych uprawnień w monicie o zgodę, zamknij ją i uruchom ponownie.

Jeśli masz uprawnienia, które zostały przyznane, ale nie skonfigurowano, przycisk zgody administratora poprosi o obsługę tych uprawnień. Można je dodać do skonfigurowanych uprawnień lub można je usunąć.

Monit o zgodę zapewnia opcję **akceptacji** lub **anulowania**. Wybierz pozycję **Akceptuj** , aby udzielić zgody administratora. Jeśli wybierzesz pozycję **Anuluj**, zgoda administratora nie zostanie udzielona. Zostanie wyświetlony komunikat o błędzie informujący o odrzuceniu zgody.

> [!NOTE]
> Istnieje opóźnienie między udzieleniem zgody administratora przez wybranie pozycji **Akceptuj** w monicie o zgodę, a stanem zgody administratora na odzwierciedlenie w portalu.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak uwidocznić interfejsy API sieci Web.
> [!div class="nextstepaction"]
> [Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md)

* Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

* Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).

* [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)

* [Szybki Start: modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)

* [Szybki Start: Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md)
