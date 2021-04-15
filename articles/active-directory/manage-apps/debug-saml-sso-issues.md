---
title: Debugowanie logowania pojedynczego opartego na saml — Azure Active Directory
description: Debugowanie logowania pojedynczego opartego na saml w aplikacjach w Azure Active Directory.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374322"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowanie logowania jednokrotnego opartego na protokole SAML w aplikacjach w usłudze Azure Active Directory

Dowiedz się, jak [](what-is-single-sign-on.md) znaleźć i rozwiązać problemy z logowaniem pojedynczym dla aplikacji w usłudze Azure Active Directory (Azure AD), które korzystają z logowania pojedynczego opartego na saml. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zalecamy zainstalowanie rozszerzenia [Moje aplikacje bezpiecznego logowania.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) To rozszerzenie przeglądarki ułatwia zbieranie informacji o żądaniu SAML i odpowiedzi SAML, które są potrzebne do rozwiązywania problemów z logowaniem pojedynczym. Jeśli nie możesz zainstalować rozszerzenia, w tym artykule pokazano, jak rozwiązywać problemy z zainstalowanym rozszerzeniem i bez niego.

Aby pobrać i zainstalować Moje aplikacje bezpiecznego logowania, użyj jednego z poniższych linków.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testowanie logowania pojedynczego opartego na saml

Aby przetestować logowanie pojedyncze oparte na saml między usługą Azure AD i aplikacją docelową:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny lub inny administrator z uprawnieniami do zarządzania aplikacjami.
1. W bloku po lewej stronie wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję Aplikacje **dla przedsiębiorstw.** 
1. Z listy aplikacji dla przedsiębiorstw wybierz aplikację, dla której chcesz przetestować logowanie pojedyncze, a następnie z opcji po lewej stronie wybierz pozycję **Logowanie pojedyncze.**
1. Aby otworzyć środowisko testowania logowania pojedynczego opartego na saml, przejdź do **tematu Test single sign-on** (Krok 5). Jeśli **przycisk Testuj** jest wyszrzony, należy najpierw wypełnić i zapisać wymagane atrybuty w sekcji **Podstawowa konfiguracja saml.**
1. W bloku **Testowanie logowania pojedynczego** użyj poświadczeń firmowych, aby zalogować się do aplikacji docelowej. Możesz zalogować się jako bieżący użytkownik lub inny użytkownik. Jeśli zalogujesz się jako inny użytkownik, zostanie wyświetlony monit o uwierzytelnienie.

    ![Zrzut ekranu przedstawiający testową stronę logowania jednokrotnego SAML](./media/debug-saml-sso-issues/test-single-sign-on.png)

Jeśli pomyślnie się zalogowano, test zakończył się pomyślnie. W tym przypadku usługa Azure AD wystawiła token odpowiedzi SAML dla aplikacji. Do pomyślnego zalogowania się aplikacja użyła tokenu SAML.

Jeśli wystąpi błąd na stronie logowania firmy lub na stronie aplikacji, użyj jednej z następnych sekcji, aby rozwiązać ten problem.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Rozwiązywanie błędu logowania na stronie logowania firmy

Podczas próby zalogowania się na stronie logowania firmy może zostać wyświetlony błąd podobny do poniższego przykładu.

![Przykład przedstawiający błąd na stronie logowania firmy](./media/debug-saml-sso-issues/error.png)

Do debugowania tego błędu potrzebny jest komunikat o błędzie i żądanie SAML. Rozszerzenie Moje aplikacje secure sign-in automatycznie zbiera te informacje i wyświetla wskazówki dotyczące rozwiązywania problemów w usłudze Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Aby rozwiązać problem z błędem logowania Moje aplikacje zainstalowanym rozszerzeniem bezpiecznego logowania

1. W przypadku wystąpienia błędu rozszerzenie przekierowuje Cię z powrotem do bloku logowania pojedynczego usługi Azure AD **Test.**
1. W bloku **Test logowania pojedynczego wybierz** pozycję Pobierz żądanie **SAML.**
1. Na podstawie błędu i wartości w żądaniu SAML powinny zostać podane konkretne wskazówki dotyczące rozwiązywania problemów.
1. Zostanie wyświetlony przycisk **Napraw,** aby automatycznie zaktualizować konfigurację w usłudze Azure AD w celu rozwiązania problemu. Jeśli nie widzisz tego przycisku, problem z logowaniem nie jest spowodowany błędną konfiguracją w usłudze Azure AD.

Jeśli w przypadku błędu logowania nie zostanie podane żadne rozwiązanie, zalecamy użycie pola tekstowego opinii w celu poinformowania nas.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Aby rozwiązać ten problem bez instalowania Moje aplikacje bezpiecznego logowania

1. Skopiuj komunikat o błędzie w prawym dolnym rogu strony. Komunikat o błędzie zawiera:
    - Identyfikator korelacji i znacznik czasu. Te wartości są ważne podczas tworzenia sprawy pomocy technicznej w firmie Microsoft, ponieważ ułatwiają inżynierom zidentyfikowanie problemu i dokładne rozwiązanie problemu.
    - Instrukcja identyfikująca główną przyczynę problemu.
1. Wstecz do usługi Azure AD i znajdź **blok Test logowania pojedynczego.**
1. W polu tekstowym powyżej pola **Uzyskaj wskazówki dotyczące rozwiązywania** wklej komunikat o błędzie.
1. Kliknij **pozycję Uzyskaj wskazówki dotyczące rozwiązywania,** aby wyświetlić kroki rozwiązywania problemu. Wskazówki mogą wymagać informacji z żądania SAML lub odpowiedzi SAML. Jeśli nie używasz rozszerzenia bezpiecznego logowania Moje aplikacje, do pobrania żądania i odpowiedzi SAML może być potrzebne narzędzie, takie jak [Fiddler.](https://www.telerik.com/fiddler)
1. Sprawdź, czy miejsce docelowe w żądaniu SAML odpowiada adresowi URL usługi saml single Sign-On Service uzyskaną z usługi Azure AD.
1. Sprawdź, czy wystawca w żądaniu SAML jest tym samym identyfikatorem, który został skonfigurowany dla aplikacji w usłudze Azure AD. Usługa Azure AD używa wystawcy do znalezienia aplikacji w katalogu.
1. Sprawdź, czy adres AssertionConsumerServiceURL to miejsce, w którym aplikacja oczekuje odbierania tokenu SAML z usługi Azure AD. Tę wartość można skonfigurować w usłudze Azure AD, ale nie jest ona obowiązkowa, jeśli jest częścią żądania SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Rozwiązywanie błędu logowania na stronie aplikacji

Możesz zalogować się pomyślnie, a następnie zobaczyć błąd na stronie aplikacji. Dzieje się tak, gdy usługa Azure AD wystawiła token aplikacji, ale aplikacja nie akceptuje odpowiedzi.

Aby usunąć ten błąd, wykonaj następujące kroki lub obejrzyj to krótkie wideo dotyczące rozwiązywania problemów z logowaniem [jednokrotnym SAML](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)przy użyciu usługi Azure AD:

1. Jeśli aplikacja znajduje się w galerii usługi Azure AD, sprawdź, czy zostały podjęte wszystkie kroki integracji aplikacji z usługą Azure AD. Aby znaleźć instrukcje integracji dla aplikacji, zobacz [listę samouczków](../saas-apps/tutorial-list.md)integracji aplikacji SaaS .
1. Pobierz odpowiedź SAML.
    - Jeśli zainstalowano Moje aplikacje bezpiecznego logowania, w bloku **Test** logowania pojedynczego kliknij pozycję Pobierz odpowiedź **SAML.**
    - Jeśli rozszerzenie nie jest zainstalowane, użyj narzędzia takiego jak [Fiddler,](https://www.telerik.com/fiddler) aby pobrać odpowiedź SAML.
1. Zwróć uwagę na następujące elementy w tokenie odpowiedzi SAML:
   - Unikatowy identyfikator użytkownika wartości i formatu NameID
   - Oświadczenia wystawione w tokenie
   - Certyfikat używany do podpisywania tokenu.

     Aby uzyskać więcej informacji na temat odpowiedzi SAML, zobacz [Single Sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)(Protokół SAML logowania pojedynczego).

1. Teraz, po przejrzeniu odpowiedzi SAML, zobacz Błąd na stronie aplikacji po zalogowaniu się, aby uzyskać wskazówki dotyczące sposobu rozwiązania problemu. [](application-sign-in-problem-application-error.md) 
1. Jeśli nadal nie możesz się pomyślnie zalogować, możesz poprosić dostawcę aplikacji, czego brakuje w odpowiedzi SAML.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy logowanie pojedyncze działa w aplikacji, możesz zautomatyzować aprowizowanie i [coprowizowanie](../app-provisioning/user-provisioning.md) użytkowników w aplikacjach SaaS lub rozpocząć pracę z [dostępem warunkowym.](../conditional-access/app-based-conditional-access.md)
