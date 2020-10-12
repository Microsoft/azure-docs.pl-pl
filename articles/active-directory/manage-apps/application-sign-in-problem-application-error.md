---
title: Na stronie aplikacji jest wyświetlany komunikat o błędzie po zalogowaniu | Microsoft Docs
description: Jak rozwiązywać problemy z logowaniem do usługi Azure AD, gdy aplikacja zwróci komunikat o błędzie.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c826a679c1c64e113beb6b2cc5ffd29f82b55a3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84759542"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Na stronie aplikacji jest wyświetlany komunikat o błędzie po zalogowaniu się użytkownika

W tym scenariuszu Azure Active Directory (Azure AD) podpisuje użytkownika w programie. Jednak aplikacja wyświetla komunikat o błędzie i nie zezwala użytkownikowi na zakończenie przepływu logowania. Problem polega na tym, że aplikacja nie zaakceptował odpowiedzi wydanej przez usługę Azure AD.

Istnieje kilka możliwych przyczyn, dla których aplikacja nie zaakceptował odpowiedzi z usługi Azure AD. Jeśli komunikat o błędzie nie identyfikuje jasno braku odpowiedzi, spróbuj wykonać następujące czynności:

-   Jeśli aplikacja jest galerią usługi Azure AD, upewnij się, że wykonano kroki opisane w temacie jak debugować Logowanie jednokrotne oparte na protokole [SAML do aplikacji w usłudze Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) , aby przechwycić żądanie SAML, odpowiedź i token.

-   Wyślij odpowiedź SAML do dostawcy aplikacji i poproś o ich brak.

## <a name="attributes-are-missing-from-the-saml-response"></a>Brak atrybutów w odpowiedzi SAML

Aby dodać atrybut w konfiguracji usługi Azure AD, który będzie wysyłany w odpowiedzi usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako Administrator globalny lub współadministrator.

2. W górnej części okienka nawigacji po lewej stronie wybierz pozycję **wszystkie usługi** , aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do rejestracji jednokrotnej.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie jednokrotne** w okienku nawigacji.

8. W sekcji **atrybuty użytkownika** wybierz pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika**. W tym miejscu możesz zmienić atrybuty do wysłania do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:

   1. Wybierz pozycję **Dodaj atrybut**. Wprowadź **nazwę**i wybierz **wartość** z listy rozwijanej.

   1.  Wybierz pozycję **Zapisz**. W tabeli zostanie wyświetlony nowy atrybut.

9. Zapisz konfigurację.

   Przy następnym zalogowaniu się użytkownika do aplikacji usługa Azure AD wyśle nowy atrybut do odpowiedzi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikacja nie zidentyfikuje użytkownika

Logowanie do aplikacji nie powiodło się, ponieważ odpowiedź SAML nie zawiera atrybutu, takiego jak rola. Lub nie powiedzie się, ponieważ aplikacja oczekuje innego formatu lub wartości atrybutu **NameID** (identyfikator użytkownika).

Jeśli używasz [zautomatyzowanej aprowizacji użytkowników usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) , aby tworzyć, konserwować i usuwać użytkowników w aplikacji, sprawdź, czy użytkownik został zainicjowany do aplikacji SaaS. Aby uzyskać więcej informacji, zobacz [nie zainicjowano obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Dodawanie atrybutu do konfiguracji aplikacji usługi Azure AD

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako Administrator globalny lub współadministrator.

2. Wybierz pozycję **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **atrybuty użytkownika**wybierz unikatowy identyfikator użytkownika z listy rozwijanej **Identyfikator użytkownika** .

## <a name="change-the-nameid-format"></a>Zmień format NameID

Jeśli aplikacja oczekuje innego formatu atrybutu **NameID** (identyfikator użytkownika), zobacz [Edytowanie NameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) , aby zmienić format NameID.

Usługa Azure AD wybiera format atrybutu **NameID** (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zobacz sekcję "NameIDPolicy" [protokołu SAML logowania](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)jednokrotnego.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje innej metody podpisu dla odpowiedzi SAML

Aby zmienić, które części tokenu SAML są podpisane cyfrowo przez usługę Azure AD, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako Administrator globalny lub współadministrator.

2. Wybierz pozycję **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do rejestracji jednokrotnej.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **certyfikat podpisywania SAML**wybierz pozycję  **Pokaż zaawansowane ustawienia podpisywania certyfikatu**.

9. Wybierz **opcję podpisywania** , której aplikacja oczekuje spośród następujących opcji:

   * **Podpisz odpowiedź SAML**
   * **Podpisz odpowiedź i potwierdzenie SAML**
   * **Podpisz potwierdzenie SAML**

   Przy następnym zalogowaniu się użytkownika do aplikacji usługa Azure AD będzie podpisywać część wybranej odpowiedzi SAML.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikacja oczekuje algorytmu podpisywania SHA-1

Domyślnie usługa Azure AD podpisuje token SAML przy użyciu najbardziej bezpiecznego algorytmu. Zaleca się, aby nie zmieniać algorytmu podpisywania *SHA-1* , chyba że aplikacja wymaga algorytmu SHA-1.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako Administrator globalny lub współadministrator.

2. Wybierz pozycję **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.

4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**. Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do rejestracji jednokrotnej.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne w okienku nawigacji po lewej stronie aplikacji.

8. W obszarze **certyfikat podpisywania SAML**wybierz pozycję **Pokaż zaawansowane ustawienia podpisywania certyfikatu**.

9. Jako **algorytm podpisywania**wybierz opcję **SHA-1** .

   Przy następnym zalogowaniu się użytkownika do aplikacji usługa Azure AD podpisuje token SAML przy użyciu algorytmu SHA-1.

## <a name="next-steps"></a>Następne kroki
[Jak debugować Logowanie jednokrotne oparte na języku SAML do aplikacji w usłudze Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
