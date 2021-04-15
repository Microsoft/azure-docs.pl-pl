---
title: Komunikat o błędzie jest wyświetlany na stronie aplikacji po zalogowaniu | Microsoft Docs
description: Jak rozwiązywać problemy z logowaniem do usługi Azure AD, gdy aplikacja zwraca komunikat o błędzie.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379302"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Po zalogowaniu się użytkownika na stronie aplikacji jest wyświetlany komunikat o błędzie

W tym scenariuszu Azure Active Directory użytkownika (Azure AD). Jednak aplikacja wyświetla komunikat o błędzie i nie pozwala użytkownikowi ukończyć przepływu logowania. Problem polega na tym, że aplikacja nie zaakceptowała odpowiedzi wystawionej przez usługę Azure AD.

Istnieje kilka możliwych przyczyn, dla których aplikacja nie zaakceptowała odpowiedzi z usługi Azure AD. Jeśli komunikat o błędzie nie identyfikuje wyraźnie tego, czego brakuje w odpowiedzi, spróbuj wykonać następujące czynności:

-   Jeśli aplikacja jest galerią usługi Azure AD, sprawdź, czy instrukcje z tematu Jak debugować logowanie pojedynczej oparte na saml do aplikacji w [usłudze Azure AD.](./debug-saml-sso-issues.md)

-   Użyj narzędzia takiego jak [Fiddler,](https://www.telerik.com/fiddler) aby przechwycić żądanie SAML, odpowiedź i token.

-   Wyślij odpowiedź SAML do dostawcy aplikacji i zapytaj go, czego brakuje.

## <a name="attributes-are-missing-from-the-saml-response"></a>Brak atrybutów w odpowiedzi SAML

Aby dodać atrybut w konfiguracji usługi Azure AD, który zostanie wysłany w odpowiedzi usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministracyjnym.

2. W górnej części okienka nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.

4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie** aplikacje, aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz chcieć aplikacji, użyj kontrolki **Filtr** w górnej części **listy Wszystkie aplikacje.** Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania pojedynczego.

7. Po zakończeniu ładowania aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji.

8. W sekcji **Atrybuty użytkownika** wybierz pozycję Wyświetl i edytuj wszystkie inne **atrybuty użytkownika.** W tym miejscu możesz zmienić atrybuty do wysłania do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Wybierz **pozycję Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   1.  Wybierz pozycję **Zapisz**. Nowy atrybut zostanie wyświetlony w tabeli.

9. Zapisz konfigurację.

   Następnym razem, gdy użytkownik się do aplikacji zaimknie, usługa Azure AD wyśle nowy atrybut w odpowiedzi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikacja nie identyfikuje użytkownika

Logowanie do aplikacji kończy się niepowodzeniem, ponieważ w odpowiedzi SAML brakuje atrybutu, takiego jak rola. Lub kończy się niepowodzeniem, ponieważ aplikacja oczekuje innego formatu lub wartości **atrybutu NameID** (identyfikator użytkownika).

Jeśli do tworzenia, obsługi i usuwania użytkowników w aplikacji używasz automatycznej aprowności użytkowników usługi [Azure AD,](../app-provisioning/user-provisioning.md) sprawdź, czy użytkownik został aprowowany w aplikacji SaaS. Aby uzyskać więcej informacji, zobacz No users are are being provisioned to an Azure AD Gallery application (Brak użytkowników [aprowowanych w aplikacji z galerii usługi Azure AD).](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Dodawanie atrybutu do konfiguracji aplikacji usługi Azure AD

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współzałożycieli.

2. Wybierz **pozycję Wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.

4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz chcieć aplikacji, użyj kontrolki **Filtr** w górnej części **listy Wszystkie aplikacje.** Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

7. Po ładowaniu aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji.

8. W **obszarze Atrybuty użytkownika** wybierz unikatowy identyfikator użytkownika z **listy** rozwijanej Identyfikator użytkownika.

## <a name="change-the-nameid-format"></a>Zmienianie formatu NameID

Jeśli aplikacja oczekuje innego formatu **atrybutu NameID** (identyfikator użytkownika), zobacz [Edytowanie identyfikatora nameID,](../develop/active-directory-saml-claims-customization.md#editing-nameid) aby zmienić format NameID.

Usługa Azure AD wybiera format atrybutu **NameID** (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w żądanym formacie SAML AuthRequest. Aby uzyskać więcej informacji, zobacz sekcję "NameIDPolicy" w sekcji [Single sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md#nameidpolicy)(Protokół SAML logowania pojedynczego).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje innej metody podpisu dla odpowiedzi SAML

Aby zmienić części tokenu SAML podpisane cyfrowo przez usługę Azure AD, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministracyjnym.

2. Wybierz **pozycję Wszystkie** usługi w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.

4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie** aplikacje, aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz chcieć aplikacji, użyj kontrolki **Filtr** w górnej części **listy Wszystkie aplikacje.** Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania pojedynczego.

7. Po ładowaniu aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji.

8. W **obszarze Certyfikat podpisywania SAML** wybierz pozycję **Pokaż zaawansowane ustawienia podpisywania certyfikatów.**

9. Wybierz opcję **podpisywania oczekiwaną** przez aplikację spośród tych opcji:

   * **Podpisz odpowiedź SAML**
   * **Podpisywanie odpowiedzi i asercji SAML**
   * **Podpisywanie asercji SAML**

   Następnym razem, gdy użytkownik zaloguje się do aplikacji, usługa Azure AD podpisze wybraną część odpowiedzi SAML.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikacja oczekuje algorytmu podpisywania SHA-1

Domyślnie usługa Azure AD podpisuje token SAML przy użyciu najbezpieczniejszego algorytmu. Zalecamy, aby nie zmieniać algorytmu podpisywania na *SHA-1,* chyba że aplikacja wymaga algorytmu SHA-1.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministracyjnym.

2. Wybierz **pozycję Wszystkie** usługi w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.

4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie** aplikacje, aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz chcieć aplikacji, użyj kontrolki **Filtr** w górnej części **listy Wszystkie aplikacje.** Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania pojedynczego.

7. Po zakończeniu ładowania aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji po lewej stronie aplikacji.

8. W **obszarze Certyfikat podpisywania SAML** wybierz pozycję **Pokaż zaawansowane ustawienia podpisywania certyfikatów.**

9. Wybierz **sha-1** jako **algorytm podpisywania.**

   Następnym razem, gdy użytkownik zaloguje się do aplikacji, usługa Azure AD podpisze token SAML przy użyciu algorytmu SHA-1.

## <a name="next-steps"></a>Następne kroki
[How to debug SAML-based single sign-on to applications in Azure AD (Jak debugować logowanie](./debug-saml-sso-issues.md)pojedyncze oparte na saml do aplikacji w usłudze Azure AD).