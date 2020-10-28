---
title: Samouczek — Tworzenie dzierżawy Azure Active Directory B2C
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak przygotować się do rejestracji aplikacji, tworząc dzierżawę Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dce41f979a46ae2bda568b5db79f0e0304705dd8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670181"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C

Aby aplikacje mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz.

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

1. Zaloguj się do [Azure portal](https://portal.azure.com/). Zaloguj się przy użyciu konta platformy Azure, do którego przypisano co najmniej rolę [współautor](../role-based-access-control/built-in-roles.md) w ramach subskrypcji lub grupy zasobów w ramach subskrypcji.

1. Wybierz katalog, który zawiera twoją subskrypcję.

    Na pasku narzędzi Azure Portal wybierz ikonę **katalog i subskrypcja** , a następnie wybierz katalog, który zawiera subskrypcję. Ten katalog różni się od tego, który będzie zawierać dzierżawę Azure AD B2C.

    ![Dzierżawa subskrypcji, katalog + subskrypcja z wybraną dzierżawą subskrypcji](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób** .
1. Wyszukaj **Azure Active Directory B2C** , a następnie wybierz pozycję **Utwórz** .
1. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C** .

    ![Utwórz nową dzierżawę Azure AD B2C wybraną w Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)
    
1. Wprowadź **nazwę organizacji** i **początkową nazwę domeny** . Wybierz **kraj lub region** (nie można go zmienić później), a następnie wybierz pozycję **Utwórz** .

    Nazwa domeny jest używana jako część pełnej nazwy domeny dzierżawy. W tym przykładzie nazwa dzierżawy to *contosob2c.onmicrosoft.com* :

1. Po zakończeniu tworzenia dzierżawy wybierz link **Utwórz nową dzierżawę B2C lub link do istniejącej dzierżawy** w górnej części strony tworzenia dzierżawy.

    ![Łącze do łącza do stron nadrzędnych dzierżawy wyróżnione w Azure Portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Wybierz pozycję **Połącz istniejącą dzierżawę Azure AD B2C z moją subskrypcją platformy Azure** . Musisz zalogować się jako rola właściciela, aby wykonać ten krok.

   ![Połącz istniejący wybór subskrypcji w Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Wybierz utworzoną **Azure AD B2C dzierżawcę** , a następnie wybierz swoją **subskrypcję** .

    W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** . Wprowadź **nazwę** grupy zasobów, która będzie zawierać dzierżawę, wybierz **lokalizację grupy zasobów** , a następnie wybierz pozycję **Utwórz** .

    ![Formularz ustawień subskrypcji linku w Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)
    

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

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. W menu Azure Portal wybierz pozycję **wszystkie usługi** .
1. W polu wyszukiwania **wszystkie usługi** wyszukaj pozycję **Azure AD B2C** , umieść kursor nad wynikiem wyszukiwania, a następnie wybierz ikonę gwiazdki w etykietce narzędzia. **Azure AD B2C** teraz pojawia się w Azure Portal w obszarze **Ulubione** .
1. Jeśli chcesz zmienić położenie nowego elementu ulubionego, przejdź do menu Azure Portal, wybierz pozycję **Azure AD B2C** , a następnie przeciągnij go w górę lub w dół do żądanej pozycji.

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
