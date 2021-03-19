---
title: Samouczek — Tworzenie dzierżawy Azure Active Directory B2C
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak przygotować się do rejestracji aplikacji, tworząc dzierżawę Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa91851787bbb0f5570a4f439f794ee352bf0625
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579692"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C

Aby aplikacje mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz. 

> [!NOTE]
> Można utworzyć maksymalnie 20 dzierżawców na subskrypcję. Ten limit pomaga chronić przed zagrożeniami dla zasobów, takich jak ataki typu "odmowa usługi", i jest wymuszany zarówno w Azure Portal, jak i w podstawowym INTERFEJSie tworzenia dzierżawy. Jeśli musisz utworzyć więcej niż 20 dzierżawców, skontaktuj się z [Pomoc techniczna firmy Microsoft](support-options.md).
> 
> Jeśli chcesz ponownie użyć nazwy dzierżawy, która została wcześniej usunięta, ale po wprowadzeniu nazwy domeny zostanie wyświetlony komunikat o błędzie "jest już używany przez inny katalog", musisz [wykonać następujące kroki, aby najpierw usunąć dzierżawcę](./faq.md?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant). Wymagana jest rola co najmniej administratora subskrypcji. Po usunięciu dzierżawy może być również konieczne wylogowanie się i ponowne zalogowanie się, aby można było ponownie użyć nazwy domeny.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako **ulubiony** w Azure Portal

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Zaloguj się przy użyciu konta platformy Azure, do którego przypisano co najmniej rolę [współautor](../role-based-access-control/built-in-roles.md) w ramach subskrypcji lub grupy zasobów w ramach subskrypcji.

1. Wybierz katalog, który zawiera twoją subskrypcję.

    Na pasku narzędzi Azure Portal wybierz ikonę **katalog i subskrypcja** , a następnie wybierz katalog, który zawiera subskrypcję. Ten katalog różni się od tego, który będzie zawierać dzierżawę Azure AD B2C.

    ![Dzierżawa subskrypcji, katalog + subskrypcja z wybraną dzierżawą subskrypcji](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

   ![Wybierz przycisk Utwórz zasób](media/tutorial-create-tenant/create-a-resource.png)

1. Wyszukaj **Azure Active Directory B2C**, a następnie wybierz pozycję **Utwórz**.
2. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C**.

    ![Utwórz nową dzierżawę Azure AD B2C wybraną w Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Na stronie **Tworzenie katalogu** wprowadź następujące elementy:

   - **Nazwa organizacji** — wprowadź nazwę dzierżawy Azure AD B2C.
   - **Początkowa nazwa domeny** — wprowadź nazwę domeny dla dzierżawy Azure AD B2C.
   - **Kraj lub region** — wybierz z listy swój kraj lub region. Tego wyboru nie można później zmienić.
   - **Subskrypcja** — wybierz subskrypcję z listy.
   - **Grupa zasobów** — wybierz lub Wyszukaj grupę zasobów, która będzie zawierać dzierżawę.

    ![Utwórz formularz dzierżawy przy użyciu przykładowych wartości w Azure Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Przejrzyj ustawienia katalogu. Następnie wybierz pozycję **Utwórz**. [Rozwiązywanie problemów z błędami wdrażania](../azure-resource-manager/templates/common-deployment-errors.md).

Możesz połączyć wiele dzierżawców Azure AD B2C z pojedynczą subskrypcją platformy Azure na potrzeby rozliczeń. Aby połączyć dzierżawcę, musisz być administratorem w dzierżawie Azure AD B2C i mieć przypisaną co najmniej rolę współautor w ramach subskrypcji platformy Azure. Aby dowiedzieć się, jak [połączyć dzierżawę Azure AD B2C z subskrypcją](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Wybierz katalog dzierżawy B2C

Aby rozpocząć korzystanie z nowej dzierżawy Azure AD B2C, musisz przełączyć się do katalogu, który zawiera dzierżawcę.

Wybierz filtr **katalogów i subskrypcji** w górnym menu Azure Portal, a następnie wybierz katalog, który zawiera dzierżawę Azure AD B2C.

Jeśli na liście nie zobaczysz nowej dzierżawy usługi Azure B2C, Odśwież okno przeglądarki, a następnie ponownie wybierz filtr **katalog + subskrypcja** w górnym menu.

![B2C dzierżawców — zawierający katalog wybrany w Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Dodaj Azure AD B2C jako ulubiony (opcjonalnie)

Ten opcjonalny krok ułatwia wybranie dzierżawy Azure AD B2C w następujących i kolejnych samouczkach.

Zamiast wyszukiwania *Azure AD B2C* we **wszystkich usługach** za każdym razem, gdy chcesz korzystać z dzierżawy, możesz zamiast tego przystąpić do ulubionego zasobu. Następnie możesz wybrać ją z sekcji **Ulubione** menu portalu, aby szybko przejść do dzierżawy Azure AD B2C.

Wystarczy wykonać tę operację tylko raz. Przed wykonaniem tych kroków upewnij się, że została przełączona do katalogu zawierającego dzierżawę Azure AD B2C, zgodnie z opisem w poprzedniej sekcji, [Wybierz katalog dzierżawy B2C](#select-your-b2c-tenant-directory).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu Azure Portal wybierz pozycję **wszystkie usługi**.
1. W polu wyszukiwania **wszystkie usługi** wyszukaj pozycję **Azure AD B2C**, umieść kursor nad wynikiem wyszukiwania, a następnie wybierz ikonę gwiazdki w etykietce narzędzia. **Azure AD B2C** teraz pojawia się w Azure Portal w obszarze **Ulubione**.
1. Jeśli chcesz zmienić położenie nowego elementu ulubionego, przejdź do menu Azure Portal, wybierz pozycję **Azure AD B2C**, a następnie przeciągnij go w górę lub w dół do żądanej pozycji.

    ![Azure AD B2C, menu Ulubione, Microsoft Azure Portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako **ulubiony** w Azure Portal

Następnie Dowiedz się, jak zarejestrować aplikację sieci Web w nowej dzierżawie.

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji >](tutorial-register-applications.md)