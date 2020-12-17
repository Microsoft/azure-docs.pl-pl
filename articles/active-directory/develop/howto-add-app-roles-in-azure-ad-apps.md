---
title: Dodaj role aplikacji i uzyskaj je z tokenu | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dodawać role aplikacji do aplikacji zarejestrowanej w Azure Active Directory, przypisywać użytkownikom i grupom te role i odbierać je w ramach roszczeń "Roles" w tokenie.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bae8f0955ef45e21d38797789bdea4f62bf5ea28
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614935"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie

Kontrola dostępu oparta na rolach (RBAC) to popularny mechanizm wymuszania autoryzacji w aplikacjach. W przypadku korzystania z RBAC administrator przyznaje uprawnienia do ról, a nie do poszczególnych użytkowników lub grup. Administrator może następnie przypisać role do różnych użytkowników i grup w celu kontrolowania, kto ma dostęp do zawartości i funkcjonalności.

Korzystając z funkcji RBAC z rolami aplikacji i oświadczeniami ról, deweloperzy mogą bezpiecznie wymuszać autoryzację w aplikacjach przy mniejszej nakładności.

Innym rozwiązaniem jest użycie grup usługi Azure AD i oświadczeń grup, jak pokazano w przykładowym kodzie [Active-Directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) w witrynie GitHub. Grupy usługi Azure AD i role aplikacji nie wykluczają się wzajemnie. mogą one być używane wspólnie w celu zapewnienia jeszcze bardziej precyzyjnej kontroli dostępu.

## <a name="declare-roles-for-an-application"></a>Deklarowanie ról dla aplikacji

Role aplikacji można definiować przy użyciu [Azure Portal](https://portal.azure.com). Role aplikacji są zwykle definiowane w rejestracji aplikacji reprezentującej usługę, aplikację lub interfejs API. Gdy użytkownik loguje się do aplikacji, usługa Azure AD emituje `roles` rolę dla każdej roli, którą użytkownik lub jednostka usługi otrzymał indywidualnie do użytkownika i od ich przynależności do grupy. Może to służyć do implementowania autoryzacji opartej na żądaniach. Role aplikacji można przypisywać [do użytkownika lub grupy użytkowników](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app). Role aplikacji mogą być również przypisane do jednostki usługi dla innej aplikacji lub [do nazwy głównej usługi dla tożsamości zarządzanej](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md).

> [!IMPORTANT]
> Obecnie Jeśli dodasz nazwę główną usługi do grupy, a następnie przypiszesz rolę aplikacji do tej grupy, usługa Azure AD nie doda tego `roles` żądania do tokenów, które wystawiają problemy.

Istnieją dwa sposoby deklarowania ról aplikacji przy użyciu Azure Portal:

* [Interfejs użytkownika ról aplikacji](#app-roles-ui--preview) | Przeglądania
* [Edytor manifestu aplikacji](#app-manifest-editor)

Liczba dodawanych ról ma wpływ na limity manifestu aplikacji wymuszane przez Azure Active Directory. Aby uzyskać informacje na temat tych limitów, zobacz sekcję  [limity manifestu](./reference-app-manifest.md#manifest-limits) w temacie [informacje dotyczące manifestu aplikacji Azure Active Directory](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Interfejs użytkownika ról aplikacji | Przeglądania

> [!IMPORTANT]
> Funkcja interfejsu użytkownika portalu ról aplikacji [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Aby utworzyć rolę aplikacji przy użyciu interfejsu użytkownika Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz opcję Filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz dzierżawę Azure Active Directory, która zawiera rejestrację aplikacji, do której chcesz dodać rolę aplikacji.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację, w której chcesz zdefiniować role aplikacji.
1. Wybierz **role aplikacji | Wersja zapoznawcza**, a następnie wybierz pozycję **Utwórz rolę aplikacji**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Okienko role aplikacji rejestracji aplikacji w Azure Portal":::
1. W okienku **Tworzenie roli aplikacji** wprowadź ustawienia dla roli. W poniższej tabeli opisano poszczególne ustawienia i ich parametry.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Role aplikacji rejestracji aplikacji tworzą okienko kontekstu w Azure Portal":::

    | Pole | Opis | Przykład |
    |-------|-------------|---------|
    | **Nazwa wyświetlana** | Nazwa wyświetlana roli aplikacji, która pojawia się w obszarze zgody i przypisywania aplikacji przez administratora. Ta wartość może zawierać spacje. | `Survey Writer` |
    | **Dozwolone typy elementów członkowskich** | Określa, czy ta rola aplikacji może być przypisana do użytkowników, aplikacji, czy obu.<br/><br/>Gdy jest to możliwe `applications` , role aplikacji są wyświetlane jako uprawnienia aplikacji w sekcji **Zarządzanie** rejestracją aplikacji > **uprawnienia interfejsu api > Dodawanie uprawnień > moich interfejsów API > wybierz interfejs API > uprawnienia aplikacji**. | `Users/Groups` |
    | **Wartość** | Określa wartość żądania ról, które aplikacja powinna oczekiwać w tokenie. Wartość powinna dokładnie pasować do ciągu, do którego odwołuje się kod aplikacji. Wartość nie może zawierać spacji. | `Survey.Create` |
    | **Opis** | Bardziej szczegółowy opis roli aplikacji wyświetlanej podczas przypisywania i wyrażania zgody aplikacji administratora. | `Writers can create surveys.` |
    | **Czy chcesz włączyć tę rolę aplikacji?** | Określa, czy rola aplikacji jest włączona. Aby usunąć rolę aplikacji, usuń zaznaczenie tego pola wyboru i Zastosuj zmianę przed podjęciem próby usunięcia operacji usuwania. | *Zaznaczono* |

1. Wybierz pozycję **Zastosuj**, aby zapisać zmiany.

### <a name="app-manifest-editor"></a>Edytor manifestu aplikacji

Aby dodać role, edytując manifest bezpośrednio:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz opcję Filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz dzierżawę Azure Active Directory, która zawiera rejestrację aplikacji, do której chcesz dodać rolę aplikacji.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację, w której chcesz zdefiniować role aplikacji.
1. W obszarze **Zarządzanie** wybierz pozycję **manifest**.
1. Edytowanie manifestu aplikacji przez lokalizowanie `appRoles` Ustawienia i Dodawanie ról aplikacji. Można zdefiniować role aplikacji, które są docelowe `users` , `applications` lub obie. Poniższe fragmenty kodu JSON pokazują przykłady obu tych elementów.
1. Zapisz manifest.

Każda definicja roli aplikacji w manifeście musi mieć unikatowy identyfikator GUID dla jego `id` wartości.

`value`Właściwość każdej definicji roli aplikacji powinna dokładnie pasować do ciągów, które są używane w kodzie w aplikacji. `value`Właściwość nie może zawierać spacji. Jeśli tak się stanie, podczas zapisywania manifestu zostanie wyświetlony komunikat o błędzie.

#### <a name="example-user-app-role"></a>Przykład: rola aplikacji użytkownika

Ten przykład definiuje rolę aplikacji o nazwie `Writer` , którą można przypisać do `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Przykład: rola aplikacja aplikacji

Gdy jest to możliwe `applications` , role aplikacji są wyświetlane jako uprawnienia aplikacji w sekcji **Zarządzanie** rejestracją aplikacji > **uprawnienia interfejsu api > Dodawanie uprawnień > moich interfejsów API > wybierz interfejs API > uprawnienia aplikacji**.

Ten przykład pokazuje rolę aplikacji, której celem jest `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Przypisywanie użytkowników i grup do ról

Po dodaniu ról aplikacji w aplikacji można przypisać użytkowników i grupy do ról. Przypisywanie użytkowników i grup do ról może odbywać się za pośrednictwem interfejsu użytkownika portalu lub programowo przy użyciu [Microsoft Graph](/graph/api/user-post-approleassignments). Gdy użytkownicy przypisani do różnych ról aplikacji logują się do aplikacji, ich tokeny będą mieć przypisane role w ramach tego `roles` żądania.

Aby przypisać użytkowników i grupy do ról przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W **Azure Active Directory** wybierz pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie.
1. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji. Jeśli aplikacja nie znajduje się na liście, użyj filtrów w górnej części listy **wszystkie aplikacje** , aby ograniczyć listę, lub przewiń w dół listy, aby zlokalizować aplikację.
1. Wybierz aplikację, do której chcesz przypisać użytkowników lub grupę zabezpieczeń do ról.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , aby otworzyć okienko **Dodaj przypisanie** .
1. Wybierz selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** . Zostanie wyświetlona lista użytkowników i grup zabezpieczeń. Można wyszukać określonego użytkownika lub grupę, a także wybrać wielu użytkowników i grupy, które znajdują się na liście.
1. Po wybraniu opcji użytkownicy i grupy wybierz przycisk **Wybierz** , aby wykonać operację.
1. Wybierz **pozycję Wybierz rolę** w okienku **Dodaj przypisanie** . Zostaną wyświetlone wszystkie role zdefiniowane dla aplikacji.
1. Wybierz rolę i wybierz przycisk **Wybierz** .
1. Wybierz przycisk **Przypisz** , aby zakończyć przypisanie użytkowników i grup do aplikacji.

Upewnij się, że dodani Użytkownicy i grupy są wyświetlane na liście **Użytkownicy i grupy** .

## <a name="assign-app-roles-to-applications"></a>Przypisywanie ról aplikacji do aplikacji

Po dodaniu ról aplikacji w aplikacji można przypisać rolę aplikacji do aplikacji klienckiej przy użyciu Azure Portal lub programowo przy użyciu [Microsoft Graph](/graph/api/user-post-approleassignments).

W przypadku przypisywania ról aplikacji do aplikacji należy utworzyć *uprawnienia aplikacji*. Uprawnienia aplikacji są zwykle używane przez aplikacje demonów lub usługi zaplecza, które muszą uwierzytelniać i wykonywać autoryzowane wywołania interfejsu API jako same, bez interakcji użytkownika.

Aby przypisać role aplikacji do aplikacji przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W **Azure Active Directory** wybierz pozycję **rejestracje aplikacji** w menu nawigacji po lewej stronie.
1. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji. Jeśli aplikacja nie znajduje się na liście, użyj filtrów w górnej części listy **wszystkie aplikacje** , aby ograniczyć listę, lub przewiń w dół listy, aby zlokalizować aplikację.
1. Wybierz aplikację, do której chcesz przypisać rolę aplikacji.
1. Wybierz pozycję **Uprawnienia interfejsu API** > **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** , a następnie wybierz aplikację, dla której zdefiniowano role aplikacji.
1. Wybierz pozycję **Uprawnienia aplikacji**.
1. Wybierz role, które chcesz przypisać.
1. Wybierz przycisk **Dodaj uprawnienia** pełne Dodawanie ról.

Nowo dodane role powinny pojawić się w okienku **uprawnień interfejsu API** rejestracji aplikacji.

#### <a name="grant-admin-consent"></a>Udziel zgody administratora

Ponieważ są to *uprawnienia aplikacji*, a nie delegowane, administrator musi udzielić zgody na korzystanie z ról aplikacji przypisanych do aplikacji.

1. W okienku **uprawnienia interfejsu API** rejestracji aplikacji wybierz pozycję **\<tenant name\> Udziel zgody administratora na**.
1. Wybierz opcję **tak** po wyświetleniu monitu o przyznanie zgody na żądane uprawnienia.

Kolumna **stan** powinna odzwierciedlać, że zgoda została **\<tenant name\> udzielona**.

## <a name="use-app-roles-in-your-web-api"></a>Korzystanie z ról aplikacji w interfejsie API sieci Web

Po zdefiniowaniu ról aplikacji i przypisaniu ich do użytkownika, grupy lub aplikacji, następnym krokiem jest dodanie kodu do internetowego interfejsu API, który wyszukuje te role po wywołaniu interfejsu API. Oznacza to, że po zażądaniu przez aplikację kliencką operacji interfejsu API wymagana jest autoryzacja, kod interfejsu API musi sprawdzić, czy zakresy znajdują się w tokenie dostępu przedstawionym w wywołaniu aplikacji klienta.

Aby dowiedzieć się, jak dodać autoryzację do internetowego interfejsu API, zobacz [Protected Web API: Weryfikuj zakresy i role aplikacji](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Role aplikacji a grupy

Chociaż można używać ról lub grup aplikacji do autoryzacji, kluczowe różnice między nimi mogą mieć wpływ na to, które z nich należy zastosować do danego scenariusza.

| Role aplikacji                                                                          | Grupy                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Są one specyficzne dla aplikacji i są zdefiniowane w rejestracji aplikacji. Są one przenoszone razem z aplikacją. | Nie są one specyficzne dla aplikacji, ale do dzierżawy usługi Azure AD. |
| Role aplikacji są usuwane po usunięciu rejestracji aplikacji.                      | Grupy pozostają nienaruszone, nawet jeśli aplikacja zostanie usunięta.            |
| Podane w ramach tego `roles` żądania.                                                     | Podane w ramach `groups` roszczeń.                                 |

Deweloperzy mogą używać ról aplikacji do kontrolowania, czy użytkownik może zalogować się do aplikacji, czy aplikacja może uzyskać token dostępu dla internetowego interfejsu API. Aby zwiększyć tę kontrolę zabezpieczeń do grup, deweloperzy i Administratorzy mogą również przypisywać grupy zabezpieczeń do ról aplikacji.

Role aplikacji są preferowane przez deweloperów, gdy chcą opisać i kontrolować parametry autoryzacji w samej aplikacji. Na przykład aplikacja używająca grup do autoryzacji zostanie przerwana w następnej dzierżawie, ponieważ identyfikator grupy i nazwa mogą być różne. Aplikacja korzystająca z ról aplikacji pozostaje bezpieczna. W rzeczywistości przypisywanie grup do ról aplikacji jest popularne z aplikacjami SaaS z tego samego powodu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rolach aplikacji o następujących zasobach.

* Przykłady kodu w serwisie GitHub
  * [Dodawanie autoryzacji przy użyciu grup i oświadczeń grup do aplikacji sieci Web ASP.NET Core](https://aka.ms/groupssample)
  * [Pojedyncze jednostronicowe aplikacje (SPA) wywołujące interfejs API sieci Web platformy .NET Core i przy użyciu ról aplikacji i grup zabezpieczeń](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Dokumentacja referencyjna
  * [Manifest aplikacji usługi Azure AD](./reference-app-manifest.md)
  * [Tokeny dostępu usługi Azure AD](access-tokens.md)
  * [Tokeny identyfikatorów usługi Azure AD](id-tokens.md)
  * [Podaj opcjonalne oświadczenia do aplikacji](active-directory-optional-claims.md)
* Wideo: [Implementowanie autoryzacji w aplikacjach przy użyciu platformy tożsamości firmy Microsoft](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
