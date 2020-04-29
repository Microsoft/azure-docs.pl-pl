---
title: Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci Web — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak skonfigurować aplikację tak, aby uwidocznić nowe uprawnienie/zakres i rolę umożliwiające udostępnienie aplikacji dla aplikacji klienckich.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: e005ba9c5458849863bd4668ffde1e0f6fb4bf91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76704225"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web

Opracowane internetowe interfejsy API można udostępniać aplikacjom klienckim przez uwidocznienie [uprawnień/zakresów](developer-glossary.md#scopes) i [ról](developer-glossary.md#roles). Prawidłowo skonfigurowany internetowy interfejs API jest udostępniany podobnie jak inne internetowe interfejsy API firmy Microsoft, w tym interfejs API programu Graph i interfejsy API usługi Office 365.

Z tego przewodnika Szybki start dowiesz się, jak skonfigurować aplikację tak, aby uwidocznić nowy zakres w celu udostępnienia go aplikacjom klienckim.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy upewnij się, że są spełnione następujące wymagania wstępne:

* Znasz obsługiwane [uprawnienia i zgody](v2-permissions-and-consent.md), ponieważ ich znajomość jest istotna podczas tworzenia aplikacji, które mają być używane przez innych użytkowników lub aplikacje.
* Masz dzierżawę z zarejestrowanymi w niej aplikacjami.
  * Jeśli nie masz zarejestrowanych aplikacji, [dowiedz się, jak zarejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

Przed skonfigurowaniem aplikacji wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji lub główną stronę rejestracji.
1. Wybierz metodę do użycia (interfejs użytkownika lub manifest aplikacji) w celu uwidocznienia nowego zakresu:
    * [Uwidacznianie nowego zakresu za pomocą interfejsu użytkownika](#expose-a-new-scope-through-the-ui)
    * [Uwidacznianie nowego zakresu lub roli za pomocą manifestu aplikacji](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Uwidacznianie nowego zakresu za pomocą interfejsu użytkownika

[![Pokazuje, jak uwidocznić interfejs API przy użyciu interfejsu użytkownika](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Aby uwidocznić nowy zakres za pomocą interfejsu użytkownika:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwidocznij interfejs API**.

1. Wybierz polecenie **Dodaj zakres**.

1. Jeśli nie ustawisz pozycji **Identyfikator URI identyfikatora aplikacji**, zostanie wyświetlony monit o jej określenie. Podaj identyfikator URI identyfikatora aplikacji lub użyj udostępnionego identyfikatora, a następnie wybierz pozycję **Zapisz i kontynuuj**.

1. Gdy zostanie wyświetlona strona **Dodawanie zakresu**, podaj informacje o Twoim zakresie:

    | Pole | Opis |
    |-------|-------------|
    | **Nazwa zakresu** | Podaj znaczącą nazwę zakresu.<br><br>Na przykład `Employees.Read.All`. |
    | **Kto może wyrazić zgodę** | Wybierz, czy użytkownicy mogą wyrazić zgodę na ten zakres, czy też jest wymagana zgoda administratora. Wybierz pozycję **Tylko administratorzy** dla uprawnień dających duże przywileje. |
    | **Nazwa wyświetlana zgody administratora** | Podaj znaczący opis zakresu, który będzie widoczny dla administratorów.<br><br>Na przykład: `Read-only access to Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez administratora** | Podaj znaczący opis zakresu, który będzie widoczny dla administratorów.<br><br>Na przykład: `Allow the application to have read-only access to all Employee data.` |

    Jeśli użytkownicy mogą wyrazić zgodę na zakres, dodaj także wartości dla następujących pól:

    | Pole | Opis |
    |-------|-------------|
    | **Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika** | Podaj znaczącą nazwę dla zakresu, która będzie widoczna dla użytkowników.<br><br>Na przykład: `Read-only access to your Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez użytkownika** | Podaj znaczący opis zakresu, który będzie widoczny dla użytkowników.<br><br>Na przykład: `Allow the application to have read-only access to your Employee data.` |

1. Ustaw pozycję **Stan** i wybierz polecenie **Dodaj zakres** po zakończeniu.

1. Wykonaj kroki, aby [zweryfikować, czy internetowy interfejs API został uwidoczniony dla innych aplikacji](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Uwidacznianie nowego zakresu lub roli za pomocą manifestu aplikacji

[![Uwidacznianie nowego zakresu przy użyciu kolekcji oauth2Permissions w manifeście](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Aby uwidocznić nowy zakres za pomocą manifestu aplikacji:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty internetowy edytor manifestu umożliwiający **edycję** manifest w obrębie portalu. Opcjonalnie możesz wybrać pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go dla aplikacji.
    
    Następujący przykład pokazuje, jak uwidocznić nowy zakres o nazwie `Employees.Read.All` w zasobie/interfejsie API przez dodanie następującego element JSON do kolekcji `oauth2Permissions`.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

   > [!NOTE]
   > Wartość `id` musi zostać wygenerowana programowo lub za pomocą narzędzia do generowania identyfikatora GUID, takiego jak [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Wartość `id` reprezentuje unikatowy identyfikator dla zakresu w formie uwidocznionej przez internetowy interfejs API. Po odpowiednim skonfigurowaniu klienta za pomocą uprawnień dostępu do internetowego interfejsu API usługa Azure AD wystawi token dostępu OAuth 2.0. Gdy klient wywołuje internetowy interfejs API, prezentuje on token dostępu, który ma oświadczenie zakresu (scp) ustawione na uprawnienia żądane w jego rejestracji aplikacji.
   >
   > Jeśli jest to konieczne, później można uwidocznić dodatkowe zakresy. Należy pamiętać, że internetowy interfejs API może uwidoczniać wiele zakresów powiązanych z różnymi funkcjami. Zasób może kontrolować dostęp do internetowego interfejsu API w czasie wykonywania, oceniając oświadczenia zakresu (`scp`) w odebranym tokenie dostępu OAuth 2.0.

1. Po skończeniu kliknij przycisk **Zapisz**. Teraz internetowy interfejs API jest skonfigurowany do użycia przez inne aplikacje w katalogu.
1. Wykonaj kroki, aby [zweryfikować, czy internetowy interfejs API został uwidoczniony dla innych aplikacji](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Weryfikowanie, czy internetowy interfejs API został uwidoczniony dla innych aplikacji

1. Wróć do dzierżawy usługi Azure AD, wybierz pozycję **Rejestracje aplikacji**, a następnie znajdź i wybierz aplikację kliencką, którą chcesz skonfigurować.
1. Powtórz kroki opisane w temacie [Konfigurowanie aplikacji klienckiej do uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md).
1. Po przekroczeniu kroku, aby [wybrać interfejs API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis
), wybierz zasób. Powinien zostać wyświetlony nowy zakres dostępny dla żądań uprawnień klienta.

## <a name="more-on-the-application-manifest"></a>Więcej informacji na temat manifestu aplikacji

Manifest aplikacji pełni rolę mechanizmu aktualizowania jednostki Application, która definiuje wszystkie atrybuty konfiguracji tożsamości aplikacji usługi Azure AD. Aby uzyskać więcej informacji na temat jednostki Application i jej schematu, zobacz [dokumentację jednostki Application interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ten artykuł zawiera wyczerpujące informacje na temat elementów członkowskich jednostki Application używanych do określania uprawnień dla interfejsu API, w tym następujących elementów:  

* Element członkowski appRoles, który jest kolekcją jednostek [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type). Jest on używany do definiowania [uprawnień aplikacji](developer-glossary.md#permissions) dla internetowego interfejsu API.
* Element członkowski oauth2Permissions, który jest kolekcją jednostek [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type). Jest on używany do definiowania [uprawnień delegowanych](developer-glossary.md#permissions) dla internetowego interfejsu API.

Aby uzyskać więcej informacji na temat ogólnych pojęć dotyczących manifestu aplikacji, zobacz [Omówienie manifestu aplikacji usługi Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych powiązanych przewodników Szybki start dotyczących zarządzania aplikacjami:

* [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md)
* [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
* [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
* [Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md)

Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).
