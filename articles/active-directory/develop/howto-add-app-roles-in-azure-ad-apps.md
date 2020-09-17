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
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706017"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie

Kontrola dostępu oparta na rolach (RBAC) to popularny mechanizm wymuszania autoryzacji w aplikacjach. W przypadku korzystania z RBAC administrator przyznaje uprawnienia do ról, a nie do poszczególnych użytkowników lub grup. Administrator może następnie przypisać role do różnych użytkowników i grup w celu kontrolowania, kto ma dostęp do zawartości i funkcjonalności.

Korzystając z funkcji RBAC z rolami aplikacji i oświadczeniami ról, deweloperzy mogą bezpiecznie wymuszać autoryzację w swoich aplikacjach, przenosząc im mniej wysiłku.

Innym podejściem jest użycie grup i oświadczeń grup usługi Azure AD, jak pokazano w przykładowym kodzie [webapp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) w witrynie GitHub. Grupy usługi Azure AD i role aplikacji nie wykluczają się wzajemnie. mogą one być używane wspólnie w celu zapewnienia jeszcze bardziej precyzyjnej kontroli dostępu.

## <a name="declare-roles-for-an-application"></a>Deklarowanie ról dla aplikacji

Role aplikacji są zdefiniowane w [Azure Portal](https://portal.azure.com).  Gdy użytkownik loguje się do aplikacji, usługa Azure AD emituje `roles` rolę dla każdej roli, która została udzielona indywidualnie użytkownikowi i od ich przynależności do grupy.  Przypisywanie użytkowników i grup do ról może odbywać się za pośrednictwem interfejsu użytkownika portalu lub programowo przy użyciu [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Aby utworzyć rolę aplikacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz opcję Filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz dzierżawę Azure Active Directory, która zawiera rejestrację aplikacji, do której chcesz dodać rolę aplikacji.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację, w której chcesz zdefiniować role aplikacji.
1. Wybierz **role aplikacji | Wersja zapoznawcza**, a następnie wybierz pozycję **Utwórz rolę aplikacji**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Okienko role aplikacji rejestracji aplikacji w Azure Portal":::
1. W okienku **Tworzenie roli aplikacji** wprowadź ustawienia dla roli. W poniższej tabeli opisano poszczególne ustawienia i ich parametry.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Role aplikacji rejestracji aplikacji tworzą okienko kontekstu w Azure Portal":::

    | Pole | Opis | Przykład |
    |-------|-------------|---------|
    | **Nazwa wyświetlana** | Nazwa wyświetlana roli aplikacji, która pojawia się w obszarze zgody i przypisywania aplikacji przez administratora. Ta wartość może zawierać spacje.  | `Survey Writer` |
    | **Dozwolone typy elementów członkowskich** | Określa, czy ta rola aplikacji może być przypisana do użytkowników, aplikacji, czy obu. | `Users/Groups` |
    | **Wartość** | Określa wartość żądania ról, które aplikacja powinna oczekiwać w tokenie. Wartość powinna dokładnie pasować do ciągu, do którego odwołuje się kod aplikacji. Wartość nie może zawierać spacji. | `Survey.Create` |
    | **Opis** | Bardziej szczegółowy opis roli aplikacji wyświetlanej podczas przypisywania i wyrażania zgody aplikacji administratora. | `Writers can create surveys.` |
    | **Czy chcesz włączyć tę rolę aplikacji?** | Czy rola aplikacji jest włączona. Aby usunąć rolę aplikacji, usuń zaznaczenie tego pola wyboru i Zastosuj zmianę przed podjęciem próby usunięcia operacji usuwania. | *Dane* |

1. Wybierz pozycję **Zastosuj**, aby zapisać zmiany.

> [!NOTE]
> Liczba dodawanych ról jest ograniczona do limitów zdefiniowanych dla manifestu aplikacji. Aby uzyskać informacje na temat tych limitów, zobacz sekcję  [limity manifestu](./reference-app-manifest.md#manifest-limits) w temacie [informacje dotyczące manifestu aplikacji Azure Active Directory](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Przypisywanie użytkowników i grup do ról

Po dodaniu ról aplikacji w aplikacji można przypisać użytkowników i grupy do ról.

1. W **Azure Active Directory** w Azure Portal wybierz pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie.
1. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

     Jeśli aplikacja nie znajduje się na liście, użyj filtrów w górnej części listy **wszystkie aplikacje** , aby ograniczyć listę, lub przewiń w dół listy, aby zlokalizować aplikację.

1. Wybierz aplikację, do której chcesz przypisać użytkowników lub grupę zabezpieczeń do ról.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , aby otworzyć okienko **Dodaj przypisanie** .
1. Wybierz selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń. Można wyszukać określonego użytkownika lub grupę, a także wybrać wielu użytkowników i grupy, które znajdują się na liście.

1. Po wybraniu opcji użytkownicy i grupy wybierz przycisk **Wybierz** , aby wykonać operację.
1. Wybierz pozycję **Wybierz rolę** w okienku **Dodaj przypisanie** . Zostaną wyświetlone wszystkie role zdefiniowane dla aplikacji.
1. Wybierz rolę i wybierz przycisk **Wybierz** .
1. Wybierz przycisk **Przypisz** , aby zakończyć przypisanie użytkowników i grup do aplikacji.
1. Upewnij się, że dodani Użytkownicy i grupy znajdują się na liście **Użytkownicy i grupy** .

## <a name="receive-roles-in-tokens"></a>Odbierz role w tokenach

Gdy użytkownicy przypisani do różnych ról aplikacji logują się do aplikacji, ich tokeny będą mieć przypisane role w ramach tego `roles` żądania.

## <a name="web-api-application-permissions"></a>Uprawnienia aplikacji internetowego interfejsu API

Można zdefiniować role aplikacji, które są przeznaczone dla **użytkowników lub grup**, **aplikacji**lub **obu**. Po wybraniu **aplikacji** lub **obu**tych ról aplikacja zostanie wyświetlona jako uprawnienie aplikacji w **uprawnieniach interfejsu API**aplikacji klienta.

Aby wybrać uprawnienie aplikacji w rejestracji aplikacji klienckiej po zdefiniowaniu roli przeznaczonej dla **aplikacji** lub **obu tych**elementów:

1. W **Azure Active Directory** w Azure Portal wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję Rejestracja aplikacji klienckiej.
1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. Wybierz pozycję **Dodaj uprawnienie**  >  **Moje interfejsy API**.
1. Wybierz aplikację, do której dodano rolę aplikacji, a następnie wybierz pozycję **uprawnienia aplikacji**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rolach aplikacji z następującymi zasobami:

- Przykład kodu: [Dodawanie autoryzacji przy użyciu ról aplikacji & oświadczenia ról do ASP.NET Core aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Wideo: [Implementowanie autoryzacji w aplikacjach przy użyciu platformy tożsamości firmy Microsoft ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifest aplikacji usługi Azure Active Directory](./reference-app-manifest.md)
- [Tokeny dostępu usługi Azure AD](access-tokens.md)
- [Usługa Azure AD `id_tokens`](id-tokens.md)
