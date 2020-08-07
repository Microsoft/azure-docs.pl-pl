---
title: 'Szybki Start: Konfigurowanie aplikacji w celu udostępnienia internetowego interfejsu API | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować aplikację, aby uwidocznić nowe uprawnienia/zakres i rolę w celu udostępnienia aplikacji aplikacjom klienckim.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830295"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Szybki Start: Konfigurowanie aplikacji do udostępniania internetowego interfejsu API

Opracowane internetowe interfejsy API można udostępniać aplikacjom klienckim przez uwidocznienie [uprawnień/zakresów](developer-glossary.md#scopes) i [ról](developer-glossary.md#roles). Prawidłowo skonfigurowany internetowy interfejs API jest udostępniany podobnie jak inne internetowe interfejsy API firmy Microsoft, w tym interfejs API programu Graph i interfejsy API usługi Office 365.

W tym przewodniku szybki start dowiesz się, jak skonfigurować aplikację, aby uwidocznić nowy zakres w celu udostępnienia aplikacji klienckich.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Kończenie [przewodnika Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

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
    | **Nazwa wyświetlana zgody administratora** | Podaj znaczący opis zakresu, który będzie widoczny dla administratorów.<br><br>Na przykład `Read-only access to Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez administratora** | Podaj znaczący opis zakresu, który będzie widoczny dla administratorów.<br><br>Na przykład `Allow the application to have read-only access to all Employee data.` |

    Jeśli użytkownicy mogą wyrazić zgodę na zakres, dodaj także wartości dla następujących pól:

    | Pole | Opis |
    |-------|-------------|
    | **Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika** | Podaj znaczącą nazwę dla zakresu, która będzie widoczna dla użytkowników.<br><br>Na przykład `Read-only access to your Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez użytkownika** | Podaj znaczący opis zakresu, który będzie widoczny dla użytkowników.<br><br>Na przykład `Allow the application to have read-only access to your Employee data.` |

1. Ustaw pozycję **Stan** i wybierz polecenie **Dodaj zakres** po zakończeniu.

1. Obowiązkowe Aby pominąć monitowanie o zgodę użytkowników aplikacji na zdefiniowane zakresy, możesz "wstępnie autoryzować" aplikację kliencką, aby uzyskać dostęp do internetowego interfejsu API. Należy wstępnie autoryzować *tylko* te aplikacje klienckie, które są zaufane, ponieważ użytkownicy nie będą mogli zrezygnować z zgody.
    1. W obszarze **autoryzowane aplikacje klienckie**wybierz pozycję **Dodaj aplikację kliencką** .
    1. Wprowadź **Identyfikator aplikacji** klienckiej, która ma zostać wstępnie autoryzowana. Na przykład, że wcześniej zarejestrowano aplikację sieci Web.
    1. W obszarze **autoryzowane zakresy**wybierz zakresy, dla których chcesz pominąć monitowanie o zgodę, a następnie wybierz pozycję **Dodaj aplikację**.

    Aplikacja kliencka jest teraz wstępnie autoryzowaną aplikacją klienta (PPW), a użytkownicy nie będą monitowani o zgodę na zalogowanie się do niego.

1. Wykonaj kroki, aby [zweryfikować, czy internetowy interfejs API został uwidoczniony dla innych aplikacji](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Uwidacznianie nowego zakresu lub roli za pomocą manifestu aplikacji

Manifest aplikacji służy jako mechanizm aktualizowania jednostki aplikacji, która definiuje atrybuty rejestracji aplikacji usługi Azure AD.

[![Uwidacznianie nowego zakresu przy użyciu kolekcji oauth2Permissions w manifeście](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Aby uwidocznić nowy zakres przez edycję manifestu aplikacji:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty internetowy edytor manifestu umożliwiający **edycję** manifest w obrębie portalu. Opcjonalnie możesz wybrać pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go dla aplikacji.

    Następujący przykład pokazuje, jak uwidocznić nowy zakres o nazwie `Employees.Read.All` w zasobie/interfejsie API przez dodanie następującego element JSON do kolekcji `oauth2Permissions`.

    Wygeneruj `id` wartość programowo lub za pomocą narzędzia generowania identyfikatora GUID, takiego jak [Guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

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

1. Po skończeniu kliknij przycisk **Zapisz**. Teraz internetowy interfejs API jest skonfigurowany do użycia przez inne aplikacje w katalogu.
1. Wykonaj kroki, aby [zweryfikować, czy internetowy interfejs API został uwidoczniony dla innych aplikacji](#verify-the-web-api-is-exposed-to-other-applications).

Aby uzyskać więcej informacji na temat jednostki aplikacji i jej schematu, zobacz dokumentację referencyjną typu zasobów [aplikacji][ms-graph-application] Microsoft Graph.

Aby uzyskać więcej informacji na temat manifestu aplikacji, w tym jego odwołania do schematu, zobacz [Opis manifestu aplikacji usługi Azure AD](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Weryfikowanie, czy internetowy interfejs API został uwidoczniony dla innych aplikacji

1. Wróć do dzierżawy usługi Azure AD, wybierz pozycję **rejestracje aplikacji**, a następnie Znajdź i wybierz aplikację kliencką, którą chcesz skonfigurować.
1. Powtórz kroki opisane w temacie [Konfigurowanie aplikacji klienckiej do uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md).
1. Po przekroczeniu kroku, aby [wybrać interfejs API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis), wybierz zasób (Rejestracja aplikacji interfejsu API sieci Web).
    * Jeśli została utworzona Rejestracja aplikacji interfejsu API sieci Web przy użyciu Azure Portal, zasób interfejsu API zostanie wyświetlony na karcie **Moje interfejsy API** .
    * Jeśli zezwolisz programowi Visual Studio na utworzenie rejestracji aplikacji internetowego interfejsu API podczas tworzenia projektu, zasób interfejsu API zostanie wyświetlony na karcie **interfejsy API używane przez moją organizację** .

Po wybraniu zasobu internetowego interfejsu API powinien zostać wyświetlony nowy zakres dla żądań uprawnień klienta.

## <a name="using-the-exposed-scopes"></a>Używanie uwidocznionych zakresów

Po odpowiednim skonfigurowaniu klienta z uprawnieniami dostępu do internetowego interfejsu API można wystawić token dostępu OAuth 2,0 za pomocą usługi Azure AD. Gdy klient wywołuje internetowy interfejs API, przedstawia token dostępu, który ma ustawiony zakres ( `scp` ) na uprawnienia wymagane w rejestracji aplikacji.

Jeśli jest to konieczne, później można uwidocznić dodatkowe zakresy. Należy pamiętać, że internetowy interfejs API może uwidoczniać wiele zakresów powiązanych z różnymi funkcjami. Zasób może kontrolować dostęp do internetowego interfejsu API w czasie wykonywania, oceniając oświadczenia zakresu (`scp`) w odebranym tokenie dostępu OAuth 2.0.

W aplikacjach pełna wartość zakresu jest połączeniem **identyfikatora aplikacji** internetowego interfejsu API (zasobu) i **nazwy zakresu**.

Na przykład, jeśli identyfikator URI aplikacji internetowego interfejsu API to `https://contoso.com/api` i nazwa zakresu to `Employees.Read.All` , pełny zakres to:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy interfejs API sieci Web został uwidoczniony przez skonfigurowanie jego zakresów, skonfiguruj rejestrację aplikacji klienta z uprawnieniami dostępu do tych zakresów.

> [!div class="nextstepaction"]
> [Skonfiguruj rejestrację aplikacji na potrzeby dostępu do interfejsu API sieci Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
