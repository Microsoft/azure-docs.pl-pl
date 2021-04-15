---
title: Rozwiązywanie problemów dotyczących logowania jednokrotnego opartego na protokole SAML w usłudze Azure Active Directory
description: Rozwiązywanie problemów z aplikacją usługi Azure AD skonfigurowaną na potrzeby logowania pojedynczego opartego na forsłudze SAML.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.openlocfilehash: c4a4f7bfad4254e7c3fa5851e62532ed427ced8b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376430"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Rozwiązywanie problemów dotyczących logowania jednokrotnego opartego na protokole SAML w usłudze Azure Active Directory
Jeśli wystąpi problem podczas konfigurowania aplikacji. Sprawdź, czy zostały już wszystkie kroki opisane w samouczku dla aplikacji. W konfiguracji aplikacji masz w tekście dokumentację dotyczącą sposobu konfigurowania aplikacji. Ponadto możesz uzyskać dostęp do listy samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą [Azure Active Directory,](../saas-apps/tutorial-list.md) aby uzyskać szczegółowe wskazówki krok po kroku.

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać kolejnego wystąpienia aplikacji
Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:
-   Skonfiguruj unikatowy identyfikator dla drugiego wystąpienia. Nie będzie można skonfigurować tego samego identyfikatora, który był używany dla pierwszego wystąpienia.
-   Skonfiguruj inny certyfikat niż używany dla pierwszego wystąpienia.

Jeśli aplikacja nie obsługuje żadnego z powyższych. Następnie nie będzie można skonfigurować drugiego wystąpienia.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nie można dodać identyfikatora ani adresu URL odpowiedzi
Jeśli nie możesz skonfigurować identyfikatora lub adresu URL odpowiedzi, potwierdź, że wartości Identyfikator i Adres URL odpowiedzi są zgodne z wzorcami wstępnie skonfigurowanymi dla aplikacji.

Aby poznać wzorce wstępnie skonfigurowane dla aplikacji:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministracyjnym.** Przejdź do kroku 7. Jeśli jesteś już w bloku konfiguracji aplikacji w usłudze Azure AD.
2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.
3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.
4. Kliknij **pozycję Aplikacje dla** przedsiębiorstw Azure Active Directory menu nawigacji po lewej stronie.
5. Kliknij **pozycję Wszystkie** aplikacje, aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz tutaj aplikacji, którą chcesz  wyświetlić, użyj kontrolki  Filtr w górnej części listy Wszystkie aplikacje i ustaw opcję Pokaż na wartość **Wszystkie aplikacje.** 
6. Wybierz aplikację, dla której chcesz skonfigurować logowanie pojedyncze.
7. Po zakończeniu ładowania aplikacji kliknij **pozycję Logowanie** pojedyncze w menu nawigacji po lewej stronie aplikacji.
8. Wybierz **pozycję Logowanie oparte na saml z** listy **rozwijanej** Tryb.
9. Przejdź do pola **tekstowego Identyfikator** lub **Adres URL** odpowiedzi w sekcji Domena i **adresy URL.**
10. Istnieją trzy sposoby, aby poznać obsługiwane wzorce dla aplikacji:
    * W polu tekstowym zobaczysz obsługiwane wzorce jako symbol zastępczy *Przykład:* <https://contoso.com> .
    * Jeśli wzorzec nie jest obsługiwany, podczas próby wprowadzenia wartości w polu tekstowym zobaczysz czerwony wykrzyknik. Jeśli najedziesz kursorem myszy na czerwony wykrzyknik, zobaczysz obsługiwane wzorce.
    * W samouczku dla aplikacji można również uzyskać informacje o obsługiwanych wzorcach. W sekcji **Konfigurowanie logowania pojedynczego usługi Azure AD.** Przejdź do kroku konfigurowania wartości w sekcji **Domena i adresy** URL.

Jeśli wartości nie są zgodne z wzorcami wstępnie skonfigurowanymi w usłudze Azure AD. Oto co możesz zrobić:
-   We współpracy z dostawcą aplikacji uzyskaj wartości zgodne ze wzorcem wstępnie skonfigurowanym w usłudze Azure AD
-   Możesz też skontaktować się z zespołem usługi Azure AD pod adresem lub pozostawić komentarz w samouczku, aby zażądać aktualizacji obsługiwanych <aadapprequest@microsoft.com> wzorców dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie mogę ustawić format EntityID (identyfikator użytkownika)
Nie będzie można wybrać formatu EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w żądanym formacie SAML AuthRequest. Aby uzyskać więcej informacji, zobacz artykuł Single Sign-On SAML protocol (Pojedynczy protokół [SAML)](../develop/single-sign-on-saml-protocol.md#authnrequest) w sekcji NameIDPolicy.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nie można znaleźć metadanych usługi Azure AD w celu ukończenia konfiguracji za pomocą aplikacji
Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministracyjnym.**
2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.
3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.
4. Kliknij **pozycję Aplikacje dla** przedsiębiorstw Azure Active Directory menu nawigacji po lewej stronie.
5. Kliknij **pozycję Wszystkie** aplikacje, aby wyświetlić listę wszystkich aplikacji.
   * Jeśli w tym miejscu nie widzisz aplikacji,  którą chcesz wyświetlić,  użyj kontrolki Filtr  w górnej części listy Wszystkie aplikacje i ustaw opcję Pokaż na **wartość Wszystkie aplikacje.**
6. Wybierz aplikację, dla których skonfigurowano logowanie pojedyncze.
7. Po zakończeniu ładowania aplikacji kliknij **pozycję Logowanie pojedyncze** w menu nawigacji po lewej stronie aplikacji.
8. Przejdź do **sekcji Certyfikat podpisywania SAML,** a następnie kliknij **pozycję Pobierz wartość** kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania pojedynczego, zobaczysz opcję pobrania pliku XML metadanych lub certyfikatu.

Usługa Azure AD nie dostarcza adresu URL służącego do uzyskania metadanych. Metadane można pobrać tylko jako plik XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłanych do aplikacji
Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowania](../develop/active-directory-claims-mapping.md) oświadczeń w Azure Active Directory uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)