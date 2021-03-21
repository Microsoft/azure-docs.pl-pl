---
title: Rozwiązywanie problemów dotyczących logowania jednokrotnego opartego na protokole SAML w usłudze Azure Active Directory
description: Rozwiązywanie problemów z aplikacją usługi Azure AD, która jest skonfigurowana do logowania jednokrotnego opartego na protokole SAML.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257530"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Rozwiązywanie problemów dotyczących logowania jednokrotnego opartego na protokole SAML w usłudze Azure Active Directory
Jeśli wystąpi problem podczas konfigurowania aplikacji. Upewnij się, że wykonano wszystkie kroki samouczka dotyczące aplikacji. W konfiguracji aplikacji masz wbudowaną dokumentację dotyczącą konfigurowania aplikacji. Ponadto możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md) , aby uzyskać szczegółowe wskazówki krok po kroku.

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać innego wystąpienia aplikacji
Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:
-   Skonfiguruj unikatowy identyfikator drugiego wystąpienia. Nie będzie można skonfigurować tego samego identyfikatora, który jest używany dla pierwszego wystąpienia.
-   Skonfiguruj inny certyfikat niż użyty dla pierwszego wystąpienia.

Jeśli aplikacja nie obsługuje żadnego z powyższych. Następnie nie będzie można skonfigurować drugiego wystąpienia.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nie można dodać identyfikatora lub adresu URL odpowiedzi
Jeśli nie możesz skonfigurować identyfikatora lub adresu URL odpowiedzi, potwierdź, że wartości identyfikatorów i adresów URL odpowiedzi są zgodne ze wzorcem wstępnie skonfigurowanym dla aplikacji.

Aby poznać wzorce, które zostały wstępnie skonfigurowane dla aplikacji:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.** Przejdź do kroku 7. Jeśli jesteś już w bloku konfiguracja aplikacji w usłudze Azure AD.
2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.
5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.
7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.
8. Wybierz pozycję **Logowanie oparte** na protokole SAML z listy rozwijanej **tryb** .
9. Przejdź do pola tekstowego **Identyfikator** lub **adres URL odpowiedzi** w **sekcji domena i adresy URL.**
10. Istnieją trzy sposoby, aby poznać obsługiwane wzorce dla aplikacji:
    * W polu tekstowym widzisz obsługiwane wzorce jako przykład zastępczy *:* <https://contoso.com> .
    * Jeśli wzorzec nie jest obsługiwany, zobaczysz czerwony wykrzyknik przy próbie wprowadzenia wartości w polu tekstowym. Jeśli umieścisz wskaźnik myszy nad czerwonym wykrzyknikiem, zobaczysz obsługiwane wzorce.
    * W samouczku dotyczącym aplikacji można także uzyskać informacje o obsługiwanych wzorcach. W sekcji Konfigurowanie logowania jednokrotnego w **usłudze Azure AD** . Przejdź do kroku, aby skonfigurować wartości w sekcji **domen i adresów URL** .

Jeśli wartości nie są zgodne ze wzorcami wstępnie skonfigurowanymi w usłudze Azure AD. Oto co możesz zrobić:
-   Współpraca z dostawcą aplikacji w celu uzyskania wartości zgodnych ze wzorcem wstępnie skonfigurowanym w usłudze Azure AD
-   Możesz też skontaktować się z zespołem usługi Azure AD lub <aadapprequest@microsoft.com> pozostawić komentarz w samouczku, aby zażądać aktualizacji obsługiwanych wzorców dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format EntityID (identyfikator użytkownika)
Nie będzie można wybrać formatu EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Single Sign-On protokołu SAML](../develop/single-sign-on-saml-protocol.md#authnrequest) w sekcji NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nie można znaleźć metadanych usługi Azure AD w celu ukończenia konfiguracji w aplikacji
Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**
2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.
5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.
7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.
8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłanych do aplikacji
Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](../develop/active-directory-claims-mapping.md) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)