---
title: Konfigurowanie aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD | Microsoft Docs
description: Kroki konfigurowania aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 32f79f24df6fe705146b39750c710450ef8f1f7b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735943"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurowanie aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces dodawania aplikacji uwierzytelniania OpenID z galerii

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz pozycję **Nowa aplikacja** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz nazwę aplikacji. Wybierz żądaną aplikację z panelu wyników i zarejestruj ją.

    ![Pozycja Openid na liście wyników](common/search-new-app.png)


1. Na stronie Nazwa aplikacji kliknij przycisk **Utwórz konto** .

    ![Przycisk dodawania](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > W tym miejscu administrator dzierżawy powinien wybrać przycisk rejestrowania i wyrazić zgodę na aplikację. Aplikacja zostanie wtedy dodana do dzierżawy klienta, gdzie można ją skonfigurować. Jawne dodawanie aplikacji nie jest potrzebne.

5. Nastąpi przekierowanie do strony logowania aplikacji lub usługi Azure Active Directory (Azure AD) w celu uzyskania poświadczeń logowania.

6. Po pomyślnym uwierzytelnieniu należy zaakceptować zgodę na stronie zgody. Aplikacja zostanie wtedy wyświetlona na stronie głównej.

    > [!NOTE]
    > Istnieje możliwość dodania tylko jednego wystąpienia aplikacji. Jeśli wystąpienie zostało już dodane, to próba ponownego wyrażenia zgody nie spowoduje ponownego dodania aplikacji do dzierżawy. Dlatego logicznie można używać tylko jednego wystąpienia aplikacji w dzierżawie.

1. Aby dodać aplikację OpenID Connect z galerii, postępuj zgodnie z poniższym wideo.
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania w przypadku protokołu OpenID Connect

Najbardziej podstawowy przepływ logowania zawiera następujące kroki:

![Przepływ uwierzytelniania w przypadku protokołu OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplikacja wielodostępna
Aplikacje wielodostępne są przeznaczona do użytku w wielu (a nie tylko jednej) organizacjach. Są to zazwyczaj aplikacje typu SaaS (software-as-a-service) napisane przez niezależnych dostawców oprogramowania (ISV).

Aplikację wielodostępną należy aprowizować w każdym katalogu, w którym będzie używana. Ich zarejestrowanie wymaga zgody użytkownika lub administratora. Proces wyrażania zgody rozpoczyna się po zarejestrowaniu aplikacji w katalogu i udzieleniu jej dostępu do interfejsu API programu Graph lub innego internetowego interfejsu API. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, zostanie wyświetlone okno dialogowe z uprawnieniami wymaganymi przez aplikację.

Użytkownik lub administrator może wtedy wyrazić zgodę na aplikację. Zgoda umożliwia aplikacji dostęp do określonych danych oraz ostatecznie rejestruje aplikację w katalogu.

> [!NOTE]
> Jeśli udostępnisz aplikację użytkownikom w wielu katalogach, potrzebujesz mechanizmu, aby określić dzierżawcę, w którym się znajdują. Aplikacja jednodostępna musi szukać użytkownika tylko w swoim własnym katalogu. Aplikacja wielodostępna musi zidentyfikować określonego użytkownika na podstawie wszystkich katalogów w usłudze Azure AD.
>
> Aby to umożliwić, usługa Azure AD udostępnia wspólny punkt końcowy uwierzytelniania (zamiast punktów końcowych specyficznych dla dzierżawy), gdzie dowolna aplikacja wielodostępna może kierować żądania logowania. Ten punkt końcowy to `https://login.microsoftonline.com/common` dla wszystkich katalogów w usłudze Azure AD. Punkt końcowy specyficzny dla dzierżawy to na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com`.
>
> Wspólny punkt końcowy to ważne zagadnienie do uwzględnienia podczas opracowywania aplikacji. Potrzebna jest logika niezbędna do obsługi wielu dzierżawców podczas logowania, wylogowywania i weryfikacji tokenu.

Domyślnie usługa Azure AD promuje aplikacje wielodostępne. Łatwo jest uzyskać do nich dostęp z wielu organizacji i używać po zaakceptowaniu zgody.

## <a name="consent-framework"></a>Platforma wyrażania zgody

Struktura wyrażania zgody w usłudze Azure AD umożliwia opracowywanie wielodostępnych aplikacji internetowych i natywnych aplikacji klienckich. Te aplikacje umożliwiają logowanie za pomocą kont użytkownika z dzierżawy usługi Azure AD innej niż dzierżawa, w której aplikacja jest zarejestrowana. Mogą ona także potrzebować dostępu do internetowych interfejsów API, takich jak:
- Interfejs API Microsoft Graph umożliwia dostęp do usług Azure AD, Intune i Services w programie Microsoft 365.
- Inne interfejsy API usług firmy Microsoft.
- Twoje własne internetowe interfejsy API.

Struktura opiera się na zgodzie wyrażanej przez użytkownika lub administratora w odpowiedzi na żądanie zarejestrowania w katalogu kierowane przez aplikację. Rejestracja może obejmować uzyskiwanie dostępu do danych katalogu. Po wyrażeniu zgody aplikacja kliencka może wywoływać interfejs API programu Microsoft Graph w imieniu użytkownika i używać informacji zgodnie z potrzebami.

[Interfejs API Microsoft Graph](https://developer.microsoft.com/graph/) zapewnia dostęp do danych w Microsoft 365, takich jak:

- Kalendarze i komunikaty z programu Exchange
- Witryny i listy z programu SharePoint
- Dokumenty z usługi OneDrive
- Notesy z programu OneNote
- Zadania z programu Planner
- Skoroszyty z programu Excel

Interfejs API programu Graph umożliwia również dostęp do użytkowników i grup z usługi Azure AD oraz innych obiektów danych z różnych usług w chmurze firmy Microsoft.

Poniższe kroki pokazują, jak środowisko wyrażania zgody działa dla deweloperów aplikacji i użytkownika:

1. Załóżmy, że masz internetową aplikację kliencką, który wymaga zażądania konkretnego uprawnienia na potrzeby dostępu do zasobu lub interfejsu API. Witryna Azure Portal jest używana do deklarowania żądań dotyczących uprawnień podczas konfigurowania. Podobnie jak w przypadku innych ustawień konfiguracji, stają się one częścią rejestracji usługi Azure AD aplikacji. W celu uzyskania ścieżki żądania uprawnień należy wykonać poniższe czynności:

    a. Kliknij **rejestracje aplikacji** z lewej strony menu i Otwórz aplikację, wpisując nazwę aplikacji w polu wyszukiwania.

    ![Zrzut ekranu pokazujący, że wybrano "Rejestracje aplikacji" z menu po lewej stronie i wyróżniono pole wyszukiwania "aplikacja I D".](./media/openidoauth-tutorial/application.png)

    b. Kliknij pozycję **Wyświetl uprawnienia interfejsu API**.

    ![Zrzut ekranu przedstawiający stronę "Call A P" z wybranym przyciskiem "Wyświetl A P A".](./media/openidoauth-tutorial/api-permission.png)

    c. Kliknij pozycję **Dodaj uprawnienie**.

    ![Zrzut ekranu przedstawiający sekcję "uprawnienia P I" z wybranym przyciskiem "Dodaj uprawnienie".](./media/openidoauth-tutorial/add-permission.png)

    d. Kliknij **Microsoft Graph**.

    ![Zrzut ekranu przedstawiający stronę "Żądaj uprawnień P I" z wybraną pozycją "Microsoft A P i" i "Microsoft Graph".](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Wybierz wymagane opcje z uprawnień **delegowanych** i **uprawnień aplikacji**.

    ![Interfejs API programu Graph](./media/openidoauth-tutorial/graphapi.png)

2. Należy wziąć pod uwagę, że uprawnienia aplikacji zostały zaktualizowane. Aplikacja jest uruchamiana i użytkownik użyje jej po raz pierwszy. Najpierw aplikacja musi pobrać kod autoryzacji z punktu końcowego autoryzacji / usługi Azure AD. Może wtedy użyć kodu autoryzacji do uzyskania nowego tokenu dostępu i odświeżania.

3. Jeśli użytkownik nie jest jeszcze uwierzytelniony, punkt końcowy autoryzacji / usługa Azure AD wyświetli monit logowania.

    ![Zrzut ekranu przedstawiający monit logowania dla konta](./media/openidoauth-tutorial/authentication.png)

4. Gdy użytkownik jest już zalogowany, usługa Azure AD określa, czy należy mu pokazać stronę wyrażenia zgody. To ustalenie zależy od tego, czy użytkownik (lub administrator organizacji) już udzielił zgody aplikacji.

   Jeśli nie wyrażono zgody, usługa Azure AD monituje o nią użytkownika i wyświetla uprawnienia wymagane przez aplikację do działania. Uprawnienia wyświetlane w oknie dialogowym zgody odpowiadają uprawnieniom wybranym w ramach delegowanych uprawnień w witrynie Azure Portal.

    ![Strona zgody](./media/openidoauth-tutorial/consentpage.png)

Zwykły użytkownik może wyrazić zgodę na niektóre uprawnienia. Inne uprawnienia wymagają zgody administratora dzierżawy.

## <a name="difference-between-admin-consent-and-user-consent"></a>Różnica między zgodą administratora i zgodą użytkownika

Jako administrator możesz także wyrazić zgodę na uprawnienia delegowane aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgoda administratora zapobiega wyświetlaniu okna dialogowego zgody dla każdego użytkownika w dzierżawie. Użytkownicy z rolą administratora mogą wyrazić zgodę w witrynie Azure Portal. Na stronie **Ustawienia** aplikacji wybierz pozycję **wymagane uprawnienia**  >  **Udziel zgody administratora**.

![Przycisk Udziel uprawnień](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udzielanie wyraźnej zgody przy użyciu przycisku **Udziel zgody administratora** jest teraz wymagane dla aplikacji jednostronicowych (aplikacji jednostronicowych), które używają ADAL.js. W przeciwnym przypadku wystąpi błąd aplikacji przy żądaniu tokenu dostępu.

Uprawnienia tylko do aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnienia dotyczącego tylko aplikacji, a użytkownik spróbuje zalogować się do aplikacji, zostanie wyświetlony komunikat o błędzie. Komunikat informujący o tym, że użytkownik nie jest w stanie wyrazić zgody.

Jeśli aplikacja używa uprawnień wymagających zgody administratora, potrzebujesz elementu takiego jak przycisk lub link, za pomocą którego administrator może uruchomić akcję. Żądanie wysyłane przez aplikację na potrzeby tej akcji to zwykłe żądanie autoryzacji OAuth2/OpenID Connect. To żądanie zawiera parametr ciągu zapytania *prompt=admin_consent*.

Po tym, jak administrator wyraził zgodę, a jednostka usługi jest tworzona w dzierżawie klienta, późniejsze żądania logowania nie potrzebują parametru *Prompt = admin_consent* . Ponieważ administrator zdecydował, że żądane uprawnienia są akceptowalne, od tego momentu żadni inni użytkownicy w dzierżawie nie będą monitowani o zgodę.

Administrator dzierżawy może wyłączyć możliwość wyrażania zgody na aplikacje przez zwykłych użytkowników. Jeśli ta funkcja jest wyłączona, zgoda administratora jest zawsze wymagana do używania aplikacji w dzierżawie. Jeśli chcesz przetestować aplikację z wyłączoną zgodą użytkownika końcowego, możesz znaleźć odpowiedni przełącznik konfiguracji w [witrynie Azure Portal](https://portal.azure.com/). Znajduje się on w sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze **Aplikacje dla przedsiębiorstw**.

Parametru *prompt=admin_consent* mogą też używać aplikacje wymagające uprawnień, dla których zgoda administratora nie jest konieczna. Przykładem jest aplikacja, która wymaga środowiska, w którym Administrator dzierżawy "rejestruje się" jeden raz, a inni użytkownicy nie otrzymują monitu o zgodę od tego momentu.

Wyobraź sobie sytuację, w której aplikacja wymaga zgody administratora i administrator loguje się bez wysyłania parametru *prompt=admin_consent*. Gdy administrator pomyślnie wyrazi zgodę na aplikację, będzie ona mieć zastosowanie tylko dla jego konta użytkownika. Zwykli użytkownicy nadal nie będą mogli zalogować się ani wyrazić zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz dać administratorowi dzierżawy możliwość przeanalizowania aplikacji przed zezwoleniem na dostęp innych użytkowników.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie logowania jednokrotnego opartego na usłudze OIDC (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal-setup-oidc-sso.md)