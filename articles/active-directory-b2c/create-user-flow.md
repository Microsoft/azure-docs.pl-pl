---
title: Tworzenie przepływu użytkownika — Azure Active Directory B2C
description: Dowiedz się, jak tworzyć przepływy użytkowników w Azure Portal, aby włączyć funkcję tworzenia konta, logowania i edytowania profilów użytkowników w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556491"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Tworzenie przepływu użytkownika w Azure Active Directory B2C

Możesz tworzyć [przepływy użytkowników](user-flow-overview.md) różnych typów w dzierżawie Azure Active Directory B2C (Azure AD B2C) i używać ich w aplikacjach zgodnie z potrzebami. Przepływy użytkowników mogą być ponownie używane między aplikacjami.

> [!IMPORTANT]
> Zmieniliśmy sposób odwoływania się do wersji przepływów użytkownika. Wcześniej oferowaliśmy wersje V1 (gotowe do zastosowań produkcyjnych) oraz wersje V1.1 i V2 (wersje zapoznawcze). Teraz zostały skonsolidowane przepływy użytkowników w **zalecane** wersje (wersja zapoznawcza nowej generacji) i **standardowe** (ogólnie dostępne). Wszystkie przepływy użytkowników starszej wersji programu V 1.1 i V2 są w ścieżce do wycofania od **1 sierpnia 2021**. Aby uzyskać szczegółowe informacje, zobacz [wersje przepływu użytkowników w Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- **Zarejestruj aplikację** , której chcesz użyć do przetestowania nowego przepływu użytkownika. Aby zapoznać się z przykładem, zobacz [Samouczek: rejestrowanie aplikacji sieci Web w Azure AD B2C](tutorial-register-applications.md).
- **Dodaj zewnętrznych dostawców tożsamości** , jeśli chcesz włączyć Logowanie użytkowników z dostawcami, takimi jak Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft lub Twitter. Zobacz [Dodawanie dostawców tożsamości do aplikacji w Azure AD B2C](add-identity-provider.md).
- **Skonfiguruj dostawcę tożsamości konta lokalnego** , aby określić typy tożsamości (adres e-mail, nazwa użytkownika, numer telefonu), które mają być obsługiwane dla kont lokalnych w dzierżawie. Następnie można wybrać spośród tych obsługiwanych typów tożsamości podczas tworzenia poszczególnych przepływów użytkowników. Po zakończeniu przepływu użytkownika w katalogu Azure AD B2C jest tworzone konto lokalne, a dostawca tożsamości **konta lokalnego** uwierzytelnia informacje o użytkowniku. Skonfiguruj dostawcę tożsamości konta lokalnego dzierżawy, wykonując następujące kroki:

   1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). 
   2. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
   3. Na pasku wyszukiwania u góry Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
   4. W obszarze **Zarządzaj** wybierz pozycję **dostawcy tożsamości**.
   5. Na liście dostawca tożsamości wybierz pozycję **konto lokalne**.
   6. Na stronie **Konfiguruj lokalną dostawcy tożsamości** wybierz wszystkie typy tożsamości, które mają być obsługiwane. Wybranie opcji w tym miejscu sprawia, że są one dostępne dla przepływów użytkownika utworzonych później:
      - **Telefon** (wersja zapoznawcza): umożliwia użytkownikowi wprowadzanie numeru telefonu, który jest sprawdzany przy rejestrowaniu i zmienia swój identyfikator użytkownika.
      - **Adres e-mail** (domyślnie): umożliwia użytkownikowi wprowadzanie adresu e-mail, który jest weryfikowany podczas rejestracji i zmienia swój identyfikator użytkownika.
      - **Nazwa użytkownika**: umożliwia użytkownikowi utworzenie własnego unikatowego identyfikatora użytkownika. Adres e-mail jest zbierany od użytkownika i weryfikowany.
    7. Wybierz pozycję **Zapisz**.

## <a name="create-a-user-flow"></a>Tworzenie przepływu użytkownika

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

    ![B2C dzierżawy, katalogu i subskrypcji, Azure Portal](./media/create-user-flow/directory-subscription-pane.png)

3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
4. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona przepływy użytkownika w portalu z wyróżnionym przyciskiem nowy przepływ użytkownika](./media/create-user-flow/signup-signin-user-flow.png)

5. Na stronie **Tworzenie przepływu użytkownika** wybierz typ przepływu użytkownika, który chcesz utworzyć (zobacz temat [przepływy użytkownika w Azure AD B2C](user-flow-overview.md) , aby zapoznać się z omówieniem).

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Rejestracja i przepływ logowania](./media/create-user-flow/select-user-flow-type.png)

6. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).

    ![Utwórz stronę przepływu użytkownika w Azure Portal z wyróżnionymi właściwościami](./media/create-user-flow/select-version.png)

7. Wprowadź **nazwę** przepływu użytkownika (na przykład *signupsignin1*, *profileediting1*, *passwordreset1*).
8. W obszarze **dostawcy tożsamości** wybierz opcje w zależności od typu tworzonego przepływu użytkownika:

   - **Konto lokalne**. Jeśli chcesz zezwolić użytkownikom na tworzenie kont lokalnych w dzierżawie Azure AD B2C, wybierz Typ identyfikatora, który ma być używany (na przykład adres e-mail, identyfikator użytkownika lub telefon). Wyświetlane są tylko te typy tożsamości, które są skonfigurowane w ustawieniach [dostawcy tożsamości konta lokalnego](#before-you-begin) .

   - **Dostawcy tożsamości społecznościowej**. Jeśli chcesz zezwolić użytkownikom na logowanie za pomocą [dostawców tożsamości społecznościowych, które zostały dodane](add-identity-provider.md), takich jak Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft lub Twitter, wybierz dostawców z listy.

9. W polu **atrybuty użytkownika i oświadczenia** wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Wybierz pozycję **Pokaż więcej**. Wybierz atrybuty i oświadczenia, a następnie wybierz przycisk **OK**.

    ![Strona wyboru atrybutów i oświadczeń z trzema wybranymi oświadczeniami](./media/create-user-flow/signup-signin-attributes.png)

10. Wybierz pozycję **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz pozycję **zasady**  >  **przepływy użytkownika**, a następnie wybierz utworzony przepływ użytkownika. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web, która została zarejestrowana w kroku 1. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
2. W zależności od typu testowanego przepływu użytkownika Zarejestruj się przy użyciu prawidłowego adresu e-mail i postępuj zgodnie z przepływem rejestracji lub Zaloguj się przy użyciu wcześniej utworzonego konta.

    ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Postępuj zgodnie z monitami przepływu użytkownika. Po zakończeniu przepływu użytkownika token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

> [!NOTE]
> Środowisko "Uruchamianie przepływu użytkownika" nie jest obecnie zgodne z typem adresu URL odpowiedzi SPA przy użyciu przepływu kodu autoryzacji. Aby użyć środowiska "Uruchamianie przepływu użytkownika" z tego rodzaju aplikacjami, należy zarejestrować adres URL odpowiedzi typu "Web" i włączyć przepływ niejawny zgodnie z opisem w [tym miejscu](tutorial-register-spa.md).

## <a name="next-steps"></a>Następne kroki

- [Dodawanie dostępu warunkowego do Azure AD B2C przepływów użytkownika](conditional-access-user-flow.md)
- [Dostosowywanie interfejsu użytkownika w przepływie użytkownika Azure AD B2C](customize-ui-with-html.md)
