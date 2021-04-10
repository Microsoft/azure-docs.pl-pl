---
title: Współpracuj z innymi — LUIS
titleSuffix: Azure Cognitive Services
description: Właściciel aplikacji może dodawać współautorów do zasobu tworzenia. Współautorzy mogą modyfikować model, uczenie i publikować aplikację.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101095190"
---
# <a name="add-contributors-to-your-app"></a>Dodawanie współautorów do aplikacji

Właściciel aplikacji może dodawać współautorów do aplikacji. Współautorzy mogą modyfikować model, uczenie i publikować aplikację. Po przeprowadzeniu [migracji](luis-migration-authoring.md) konta _Współautorzy_ są zarządzani w Azure Portal dla zasobu tworzenia przy użyciu strony **Kontrola dostępu (IAM)** . Dodaj użytkownika przy użyciu adresu e-mail współpracownika i roli _współautor_ .

## <a name="add-contributor-to-azure-authoring-resource"></a>Dodaj współautor do zasobu tworzenia platformy Azure

Przeprowadzono migrację, jeśli środowisko tworzenia LUIS jest powiązane z zasobem tworzenia na stronie **Zarządzanie zasobami usługi Azure >** w portalu Luis.

1. W Azure Portal Znajdź zasób tworzenia Language Understanding (LUIS). Ma typ `LUIS.Authoring` .
1. Na stronie Access Control tego zasobu **(IAM)** wybierz pozycję **+ Dodaj** , a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![W Azure Portal Dodaj przypisanie roli do zasobu tworzenia.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. W oknie **Dodawanie przypisania roli** wybierz **rolę** współautor. W opcji **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**. W opcji **Wybierz** wprowadź adres e-mail użytkownika. Jeśli użytkownik jest znany przez więcej niż 1 adres e-mail dla tej samej domeny, upewnij się, że wprowadzono _podstawowe_ konto e-mail.

    ![Dodawanie wiadomości e-mail użytkownika do roli współautor dla usługi Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Po znalezieniu adresu e-mail użytkownika wybierz konto i wybierz pozycję **Zapisz**.

    Jeśli masz problemy z tym przypisaniem roli, przejrzyj temat [Przypisywanie ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) i [Rozwiązywanie problemów z usługą Azure Access Control](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Wyświetlanie aplikacji jako współautora

Po dodaniu roli jako współautor [Zaloguj się do portalu Luis](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Użytkownicy z wieloma wiadomościami e-mail

W przypadku dodawania współautorów do aplikacji LUIS należy określić dokładny adres e-mail. Gdy usługa Azure Active Directory (Azure AD) umożliwia jednemu użytkownikowi używanie więcej niż jednego konta e-mail, LUIS wymaga, aby użytkownik mógł zalogować się przy użyciu adresu e-mail określonego podczas dodawania współautora.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Zasoby Azure Active Directory

Jeśli używasz usługi [Azure Active Directory](../../active-directory/index.yml) (Azure AD) w organizacji, language UNDERSTANDING (Luis) potrzebuje uprawnień do informacji o dostępie użytkowników, gdy chcą korzystać z Luis. Zasoby, które LUIS wymagają, są minimalne.

Szczegółowy opis jest wyświetlany podczas próby zarejestrowania się przy użyciu konta, które ma zgodę administratora lub nie wymaga zgody administratora, na przykład zgody administratora:

* Umożliwia zalogowanie się do aplikacji przy użyciu konta organizacji i umożliwienie aplikacji odczytania Twojego profilu. Umożliwia również aplikacji odczytywanie podstawowych informacji o firmie. Daje to LUIS uprawnienia do odczytu podstawowych danych profilowych, takich jak identyfikator użytkownika, adres e-mail, nazwa
* Zezwala aplikacji na wyświetlanie i aktualizowanie danych, nawet jeśli nie są aktualnie używane. Uprawnienie jest wymagane do odświeżenia tokenu dostępu użytkownika.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory użytkownika dzierżawy

LUIS używa przepływu zgody standardowego Azure Active Directory (Azure AD).

Administrator dzierżawy powinien współpracować bezpośrednio z użytkownikiem, który wymaga dostępu do LUIS w usłudze Azure AD.

* Najpierw użytkownik loguje się do LUIS i widzi okno podręczne, które wymaga zatwierdzenia przez administratora. Użytkownik kontaktuje się z administratorem dzierżawy przed kontynuowaniem.
* Następnie Administrator dzierżawy loguje się do LUIS i widzi wyskakujące okno dialogowe przepływu zgody. To okno dialogowe, w którym administrator musi udzielić uprawnienia użytkownikowi. Po zaakceptowaniu przez administratora uprawnienia użytkownik może kontynuować LUIS. Jeśli administrator dzierżawy nie zaloguje się do usługi LUIS, administrator będzie mógł uzyskać [zgodę](https://account.activedirectory.windowsazure.com/r#/applications) na Luis. Na tej stronie można filtrować listę do elementów, które zawierają nazwę `LUIS` .

Jeśli administrator dzierżawy chce, aby niektórzy użytkownicy korzystali z LUIS, istnieje kilka możliwych rozwiązań:
* Podawanie "zgody administratora" (wyrażanie zgody wszystkim użytkownikom usługi Azure AD), a następnie ustawienie opcji "tak" jako "wymagane przypisanie użytkownika" we właściwościach aplikacji dla przedsiębiorstw i przypisanie/dodanie tylko odpowiednich użytkowników do aplikacji. W przypadku tej metody administrator nadal zapewnia dostęp do aplikacji "Zgoda na administrowanie", jednak można kontrolować użytkowników, którzy mają do nich dostępu.
* Drugim rozwiązaniem jest użycie [interfejsu API zarządzania tożsamościami i dostępem usługi Azure AD w Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) , aby zapewnić zgodę na poszczególnych użytkowników.

Dowiedz się więcej na temat użytkowników i zgody usługi Azure Active Directory:
* [Ograniczanie aplikacji](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) do zestawu użytkowników

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak za pomocą wersji](luis-how-to-manage-versions.md) sterować cyklem życia aplikacji.
* Zapoznaj się z pojęciami dotyczącymi [zasobów tworzenia](luis-how-to-azure-subscription.md#authoring-key) i [współautorów](luis-how-to-azure-subscription.md#contributions-from-other-authors) tego zasobu.
* Dowiedz się [, jak tworzyć zasoby dotyczące](luis-how-to-azure-subscription.md) tworzenia i wykonywania
* Migrowanie do nowego [zasobu tworzenia](luis-migration-authoring.md)