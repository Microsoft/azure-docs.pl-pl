---
title: Korzystanie z wykrywania ryzyka przy Azure Active Directory dostępu warunkowego
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować zasady dostępu warunkowego Azure Active Directory (Azure AD) w celu blokowania logowania na podstawie ryzyka związanego z sesją.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77186612"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Szybki Start: Blokuj dostęp w przypadku wykrycia ryzyka sesji przy użyciu Azure Active Directory dostępu warunkowego  

Aby zachować ochronę środowiska, można zablokować podejrzanych użytkowników przed zalogowaniem się. [Usługa Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analizuje poszczególne logowania i oblicza prawdopodobieństwo, że próba logowania nie została wykonana przez uprawnionego właściciela konta użytkownika. Prawdopodobieństwo (niski, średni, wysoki) jest wskazywane w postaci wartości obliczanej o nazwie [poziomy ryzyka logowania](concept-conditional-access-conditions.md#sign-in-risk). Ustawiając warunek ryzyka związanego z logowaniem, można skonfigurować zasady dostępu warunkowego w celu reagowania na określone poziomy ryzyka związanego z logowaniem.

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) , które blokują logowanie, gdy wykryto skonfigurowany poziom ryzyka związanego z logowaniem.

![Tworzenie zasad](./media/app-sign-in-risk/1000.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza zaprezentowanego w tym samouczku potrzebne są następujące elementy:

- **Dostęp do Azure AD — wersja Premium P2 wersja** — podczas gdy dostęp warunkowy jest Azure AD — wersja Premium P1, potrzebna jest wersja P2, ponieważ scenariusz w tym przewodniku Szybki Start wymaga ochrony tożsamości.
- **Ochrona tożsamości** — scenariusz w tym przewodniku Szybki Start wymaga włączenia ochrony tożsamości. Jeśli nie wiesz, jak włączyć ochronę tożsamości, zobacz [włączanie Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).
- **Przeglądarka sieci Tor** — [przeglądarka tor](https://www.torproject.org/projects/torbrowser.html.en) została zaprojektowana tak, aby pomóc zachować prywatność w trybie online. Ochrona tożsamości wykrywa logowanie z przeglądarki tor jako logowania z anonimowych adresów IP, które mają poziom ryzyka średniego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory wykrywania zagrożeń](../reports-monitoring/concept-risk-events.md).  
- **Konto testowe o nazwie Alain Charon** — Jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie użytkowników opartych na chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest upewnienie się, że konto testowe może uzyskać dostęp do dzierżawy przy użyciu przeglądarki sieci Tor.

**Aby przetestować Logowanie:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako **Alain Charon**.
1. Wyloguj się.

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Scenariusz w tym przewodniku szybki start używa logowania z przeglądarki Tor w celu wygenerowania wykrytych logowań **z anonimowych adresów IP** . Poziom ryzyka tego wykrywania ryzyka to średni. Aby odpowiedzieć na wykrycie tego ryzyka, należy ustawić wartość średni warunek ryzyka logowania. W środowisku produkcyjnym należy określić warunek ryzyka logowania na wartość wysoki lub średni i wysoki.

W tej sekcji przedstawiono sposób tworzenia wymaganych zasad dostępu warunkowego. W zasadach ustaw następujące ustawienia:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Alain Charon  |
| Aplikacje w chmurze | Wszystkie aplikacje w chmurze |
| Ryzyko związane z logowaniem | Medium |
| Uprawnienie | Blokowanie dostępu |

![Tworzenie zasad](./media/app-sign-in-risk/130.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. W Azure Portal na lewym pasku nawigacyjnym kliknij pozycję **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na stronie **Azure Active Directory** w sekcji **zabezpieczenia** kliknij pozycję **dostęp warunkowy**.

   ![Dostęp warunkowy](./media/app-sign-in-risk/03.png)

1. Na stronie **dostęp warunkowy** na pasku narzędzi u góry kliknij przycisk **Dodaj**.

   ![Nazwa](./media/app-sign-in-risk/108.png)

1. Na **nowej** stronie w polu tekstowym **Nazwa** wpisz **blok dostęp dla poziomu średniego ryzyka**.

   ![Nazwa](./media/app-sign-in-risk/104.png)

1. W sekcji **przypisanie** kliknij pozycję **Użytkownicy i grupy**.

   ![Użytkownicy i grupy](./media/app-sign-in-risk/06.png)

1. Na stronie **Użytkownicy i grupy** :

   ![Dostęp warunkowy](./media/app-sign-in-risk/107.png)

   1. Kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Użytkownicy i grupy**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Alain Charon**, a następnie kliknij pozycję **Wybierz**.
   1. Na stronie **Użytkownicy i grupy** kliknij przycisk **gotowe**.
1. Kliknij pozycję **aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/app-sign-in-risk/08.png)

1. Na stronie **aplikacje w chmurze** :

   ![Dostęp warunkowy](./media/app-sign-in-risk/109.png)

   1. Kliknij pozycję **wszystkie aplikacje w chmurze**.
   1. Kliknij pozycję **Gotowe**.
1. Kliknij pozycję **warunki**.

   ![Kontrole dostępu](./media/app-sign-in-risk/19.png)

1. Na stronie **warunki** :

   ![Poziom ryzyka dotyczący logowania](./media/app-sign-in-risk/21.png)

   1. Kliknij pozycję **ryzyko związane z logowaniem**.
   1. W obszarze **Konfiguracja**kliknij przycisk **tak**.
   1. Jako poziom ryzyka związanego z logowaniem wybierz pozycję **Średni**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **warunki** kliknij przycisk **gotowe**.
1. W sekcji **kontrole dostępu** kliknij pozycję **Udziel**.

   ![Kontrole dostępu](./media/app-sign-in-risk/10.png)

1. Na stronie **Grant** :

   ![Dostęp warunkowy](./media/app-sign-in-risk/105.png)

   1. Wybierz pozycję **Blokuj dostęp**.
   1. Kliknij pozycję **Wybierz**.
1. W sekcji **Włączanie zasad** **kliknij pozycję włączone.**

   ![Włącz zasady](./media/app-sign-in-risk/18.png)

1. Kliknij przycisk **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowanie

Teraz, po skonfigurowaniu zasad dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. Pierwszym krokiem jest użycie **Narzędzia do działania** warunkowego, co pozwala na symulowanie logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Po uruchomieniu w tym scenariuszu narzędzia do wykonywania działań w **przypadku** **zablokowania dostęp dla poziomu średniego ryzyka** powinien zostać wyświetlony w obszarze **zasady, które mają zastosowanie**.

![Użytkownik](./media/app-sign-in-risk/117.png)

**Aby oszacować zasady dostępu warunkowego:**

1. Na stronie [zasady dostępu warunkowego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij pozycję **What If**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Kliknij pozycję **użytkownik**, wybierz pozycję **Artur Charon** na stronie **Użytkownicy** , a następnie kliknij pozycję **Wybierz**.

   ![Użytkownik](./media/app-sign-in-risk/116.png)

1. Jako **ryzyko związane z logowaniem**wybierz pozycję **Średni**.

   ![Użytkownik](./media/app-sign-in-risk/119.png)

1. Kliknij **What If**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji wiesz już, jak oszacować symulowaną logowanie. Oprócz symulacji należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby przetestować zasady, spróbuj zalogować się do [Azure Portal](https://portal.azure.com) jako **Artur Charon** przy użyciu przeglądarki sieci Tor. Próba logowania powinna być blokowana przez zasady dostępu warunkowego.

![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń użytkownika testowego, przeglądarkę sieci Tor i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usuwanie użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

   ![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/33.png)

- Aby uzyskać instrukcje dotyczące usuwania przeglądarki tor, zobacz [Odinstalowywanie](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymagaj zaakceptowania](require-tou.md)
> warunków użytkowania[Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji](app-based-mfa.md)
