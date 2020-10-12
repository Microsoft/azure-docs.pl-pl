---
title: Jak uzyskać certyfikat AppSource dla Azure Active Directory | Microsoft Docs
description: Szczegółowe informacje na temat sposobu uzyskania certyfikatu AppSource przez aplikację w celu Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 85c4488c4e8e1e2f9591b7d734c522c34ea4ba2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116329"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak uzyskać certyfikat AppSource dla Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) to miejsce, w którym użytkownicy biznesowi mogą odnajdywać i podejmować aplikacje SaaS firmy (autonomiczne SaaS i dodatki do istniejących produktów SaaS firmy Microsoft) i zarządzać nimi.

Aby wyświetlić autonomiczną aplikację SaaS na AppSource, aplikacja musi akceptować Logowanie jednokrotne z kont służbowych z dowolnej firmy lub organizacji, która ma Azure Active Directory (Azure AD). Proces logowania musi korzystać z protokołów [OpenID Connect Connect](v1-protocols-openid-connect-code.md) lub [OAuth 2,0](v1-protocols-oauth-code.md) . Integracja SAML nie jest akceptowana w przypadku certyfikacji AppSource.

## <a name="guides-and-code-samples"></a>Przewodniki i przykłady kodu

Jeśli chcesz dowiedzieć się, jak zintegrować aplikację z usługą Azure AD przy użyciu funkcji Open ID Connect, postępuj zgodnie z naszymi przewodnikami i przykładami kodu w [przewodniku dewelopera Azure Active Directory](v1-overview.md#get-started "Wprowadzenie do usługi Azure AD dla deweloperów").

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

*Aplikacja z wieloma dzierżawcami* to aplikacja akceptująca logowania od użytkowników z dowolnej firmy lub organizacji, która ma usługę Azure AD bez konieczności oddzielnego wystąpienia, konfiguracji lub wdrożenia. AppSource zaleca, aby aplikacje implementujące wiele dzierżawców mogły korzystać z bezpłatnej wersji próbnej *pojedynczego kliknięcia* .

Aby włączyć obsługę wielu dzierżawców w aplikacji, wykonaj następujące kroki:
1. Ustaw `Multi-Tenanted` Właściwość na `Yes` Informacje o rejestracji aplikacji w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Domyślnie aplikacje utworzone w Azure Portal są konfigurowane jako *[pojedyncze dzierżawy](#single-tenant-applications)*.
1. Zaktualizuj swój kod, aby wysyłać żądania do `common` punktu końcowego. Aby to zrobić, zaktualizuj punkt końcowy z `https://login.microsoftonline.com/{yourtenant}` do `https://login.microsoftonline.com/common*` .
1. W przypadku niektórych platform, takich jak ASP .NET, należy również zaktualizować kod w celu zaakceptowania wielu wystawców.

Aby uzyskać więcej informacji na temat wielu dzierżawców, zobacz [Jak zalogować się do każdego użytkownika usługi Azure Active Directory (Azure AD) przy użyciu wzorca aplikacji wielodostępnych](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Aplikacje z jedną dzierżawą

*Aplikacja z jedną dzierżawą* jest aplikacją, która akceptuje tylko logowania użytkowników ze zdefiniowanego wystąpienia usługi Azure AD. Użytkownicy zewnętrzni (włącznie z kontami służbowymi z innych organizacji lub kontami osobistymi) mogą zalogować się do aplikacji z jedną dzierżawą po dodaniu każdego użytkownika jako konta gościa do wystąpienia usługi Azure AD, że aplikacja jest zarejestrowana. 

Użytkowników jako konta gościa można dodawać do usługi Azure AD za pomocą [funkcji współpracy B2B usługi Azure AD](../external-identities/what-is-b2b.md) . można to zrobić [programowo](../../active-directory-b2c/code-samples.md). W przypadku korzystania z B2B użytkownicy mogą utworzyć Portal samoobsługowy, który nie wymaga zaproszenia do logowania. Aby uzyskać więcej informacji, zobacz [Portal samoobsługowy na potrzeby rejestracji w ramach współpracy B2B w usłudze Azure AD](../external-identities/self-service-portal.md).

Aplikacje z jedną dzierżawą mogą umożliwić *kontakt ze mną* , ale jeśli chcesz włączyć jedno kliknięcie/bezpłatne środowisko próbne, które AppSource zalecane, zamiast tego Włącz obsługę wielu dzierżawców w aplikacji.

## <a name="appsource-trial-experiences"></a>Środowiska próbne AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (środowisko próbne klienta)

Wersja próbna podwyższenia poziomu klienta to środowisko, które AppSource zalecane, ponieważ oferuje on dostęp jednego kliknięcia do aplikacji. Poniższy przykład pokazuje, jak wygląda to środowisko:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>Użytkownik odnajduje aplikację w witrynie sieci Web AppSource</li><li>Wybiera opcję "bezpłatna wersja próbna"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource przekierowuje użytkownika do adresu URL w witrynie sieci Web</li><li>Witryna sieci Web uruchamia proces <i>logowania</i> jednokrotnego automatycznie (przy ładowaniu strony)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>Użytkownik zostanie przekierowany do strony logowania firmy Microsoft</li><li>Użytkownik podaje poświadczenia do logowania</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>Użytkownik wyrazi zgodę na aplikację</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>Logowanie kończy się i użytkownik zostaje przekierowany z powrotem do witryny sieci Web</li><li>Użytkownik uruchamia bezpłatną wersję próbną</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Skontaktuj się z nami (środowisko próbne dla partnerów)

Możesz skorzystać z wersji próbnej partnera, gdy konieczne jest wykonanie ręcznej lub długoterminowej operacji w celu aprowizacji użytkownika/firmy — na przykład aplikacja musi udostępniać maszyny wirtualne, wystąpienia bazy danych lub operacje, które wymagają dużo czasu. W takim przypadku, gdy użytkownik wybierze przycisk **Żądaj wersji próbnej** i wypełni formularz, AppSource wyśle informacje kontaktowe użytkownika. Po otrzymaniu tych informacji możesz zainicjować obsługę środowiska i wysłać instrukcje do użytkownika w celu uzyskania dostępu do środowiska próbnego:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>Użytkownik odnajduje aplikację w witrynie sieci Web AppSource</li><li>Wybiera opcję "Skontaktuj się z nami"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Wypełnia formularz informacjami kontaktowymi</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Otrzymujesz informacje o użytkowniku</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Środowisko konfiguracji</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Skontaktuj się z użytkownikiem z informacjami dotyczącymi wersji próbnej</td>
        </tr>
        </table><br/><br/>
        <ul><li>Uzyskasz informacje o użytkowniku i wystąpienie wersji próbnej instalacji</li><li>Wysyłasz hiperłącze, aby uzyskać dostęp do aplikacji użytkownikowi</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>Użytkownik uzyskuje dostęp do aplikacji i kończy proces logowania jednokrotnego</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>Użytkownik wyrazi zgodę na aplikację</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>Logowanie kończy się i użytkownik zostaje przekierowany z powrotem do witryny sieci Web</li><li>Użytkownik uruchamia bezpłatną wersję próbną</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat korzystania z wersji próbnej usługi AppSource, zobacz [ten film wideo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia aplikacji obsługujących logowania do usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](./v1-authentication-scenarios.md).
- Aby uzyskać informacje na temat sposobu wyświetlania listy aplikacji SaaS w programie AppSource, zobacz temat [Informacje o partnerze AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

W przypadku integracji z usługą Azure AD w celu zapewnienia pomocy technicznej firma Microsoft używa [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) ze społecznością.

Zdecydowanie zalecamy, aby zadać pytania Stack Overflow pierwsze i przeglądać istniejące problemy, aby zobaczyć, czy ktoś zażądał wcześniej pytania. Upewnij się, że Twoje pytania lub komentarze są otagowane przy użyciu [ `[azure-active-directory]` i `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Skorzystaj z poniższej sekcji komentarzy, aby przekazać Opinie i popomóc nam udoskonalić zawartość i kształtować ją.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->