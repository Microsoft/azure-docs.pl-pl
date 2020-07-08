---
title: Problemy z konfigurowaniem federacyjnego logowania jednokrotnego dla aplikacji w galerii usługi Azure AD
description: Rozwiązywanie niektórych typowych problemów, które mogą wystąpić podczas konfigurowania federacyjnego logowania jednokrotnego przy użyciu języka SAML dla aplikacji, które są wymienione w galerii aplikacji usługi Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 858e61c6371aac28d709a9016e4b9cbed6befe3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763605"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Wystąpił problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji w galerii usługi Azure AD

Jeśli wystąpi problem podczas konfigurowania aplikacji. Upewnij się, że wykonano wszystkie kroki samouczka dotyczące aplikacji. W konfiguracji aplikacji masz wbudowaną dokumentację dotyczącą konfigurowania aplikacji. Ponadto możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) , aby uzyskać szczegółowe wskazówki krok po kroku.

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

Jeśli wartości nie są zgodne ze wzorcami wstępnie skonfigurowanymi w usłudze Azure AD. Dostępne możliwości:

-   Współpraca z dostawcą aplikacji w celu uzyskania wartości zgodnych ze wzorcem wstępnie skonfigurowanym w usłudze Azure AD

-   Możesz też skontaktować się z zespołem usługi Azure AD lub <aadapprequest@microsoft.com> pozostawić komentarz w samouczku, aby zażądać aktualizacji obsługiwanych wzorców dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format EntityID (identyfikator użytkownika)

Nie będzie można wybrać formatu EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne logowania jednokrotnego [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.

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

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiadomo, jak dostosować oświadczenia SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
