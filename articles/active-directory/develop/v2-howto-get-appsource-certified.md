---
title: Uzyskaj certyfikat AppSource Azure Active Directory | Microsoft Docs
description: Zapoznaj się ze szczegółami dotyczącymi sposobu uzyskania certyfikatu AppSource przez aplikację w celu uzyskania Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647510"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Uzyskaj certyfikat AppSource Certified for Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) to miejsce, w którym użytkownicy biznesowi mogą odnajdywać i podejmować aplikacje SaaS firmy (autonomiczne SaaS i dodatki do istniejących produktów SaaS firmy Microsoft) i zarządzać nimi.

Aby wyświetlić autonomiczną aplikację SaaS na AppSource, aplikacja musi akceptować Logowanie jednokrotne z kont służbowych z dowolnej firmy lub organizacji, która ma Azure Active Directory (Azure AD). Proces logowania musi korzystać z protokołów [OpenID Connect Connect](v2-protocols-oidc.md) lub [OAuth 2,0](v2-oauth2-auth-code-flow.md) . Integracja SAML nie jest akceptowana w przypadku certyfikacji AppSource.

## <a name="multi-tenant-applications"></a>Aplikacje wielodostępne

*Aplikacja z wieloma dzierżawcami* to aplikacja akceptująca logowania od użytkowników z dowolnej firmy lub organizacji, która ma usługę Azure AD bez konieczności oddzielnego wystąpienia, konfiguracji lub wdrożenia. AppSource zaleca, aby aplikacje implementujące wiele dzierżawców mogły korzystać z bezpłatnej wersji próbnej *pojedynczego kliknięcia* .

Aby włączyć obsługę wielu dzierżawców w aplikacji, wykonaj następujące kroki:
1. Ustaw `Multi-Tenanted` Właściwość na `Yes` Informacje o rejestracji aplikacji w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Domyślnie aplikacje utworzone w Azure Portal są konfigurowane jako *[pojedyncze dzierżawy](#single-tenant-applications)*.
1. Zaktualizuj swój kod, aby wysyłać żądania do `common` punktu końcowego. Aby to zrobić, zaktualizuj punkt końcowy z `https://login.microsoftonline.com/{yourtenant}` do `https://login.microsoftonline.com/common*` .
1. W przypadku niektórych platform, takich jak ASP.NET, konieczne jest również zaktualizowanie kodu w celu zaakceptowania wielu wystawców.

Aby uzyskać więcej informacji na temat wielu dzierżawców, zobacz [Jak zalogować się do każdego użytkownika usługi Azure Active Directory (Azure AD) przy użyciu wzorca aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplikacje z jedną dzierżawą

*Aplikacja z jedną dzierżawą* jest aplikacją, która akceptuje tylko logowania użytkowników ze zdefiniowanego wystąpienia usługi Azure AD. Użytkownicy zewnętrzni (włącznie z kontami służbowymi z innych organizacji lub kontami osobistymi) mogą zalogować się do aplikacji z jedną dzierżawą po dodaniu każdego użytkownika jako konta gościa do wystąpienia usługi Azure AD, że aplikacja jest zarejestrowana. 

Użytkowników jako konta gościa można dodawać do usługi Azure AD za pomocą [funkcji współpracy B2B usługi Azure AD](../external-identities/what-is-b2b.md) . można to zrobić [programowo](../../active-directory-b2c/code-samples.md). W przypadku korzystania z B2B użytkownicy mogą utworzyć Portal samoobsługowy, który nie wymaga zaproszenia do logowania. Aby uzyskać więcej informacji, zobacz [Portal samoobsługowy na potrzeby rejestracji w ramach współpracy B2B w usłudze Azure AD](../external-identities/self-service-portal.md).

Aplikacje z jedną dzierżawą mogą umożliwić *kontakt ze mną* , ale jeśli chcesz włączyć jedno kliknięcie/bezpłatne środowisko próbne, które AppSource zalecane, zamiast tego Włącz obsługę wielu dzierżawców w aplikacji.

## <a name="appsource-trial-experiences"></a>Środowiska próbne AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezpłatna wersja próbna (środowisko próbne klienta)

Wersja próbna podwyższenia poziomu klienta to środowisko, które AppSource zalecane, ponieważ oferuje on dostęp jednego kliknięcia do aplikacji. Poniższy przykład pokazuje, jak wygląda to środowisko:

1.  Użytkownik znajdzie aplikację w witrynie sieci Web AppSource, a następnie wybierze opcję **bezpłatna wersja próbna** .

    ![Pokazuje bezpłatną wersję próbną dla środowiska próbnego dla klienta](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource przekierowuje użytkownika do adresu URL w witrynie sieci Web. Witryna sieci Web uruchamia proces *logowania* jednokrotnego automatycznie (przy ładowaniu strony).

    ![Pokazuje, w jaki sposób użytkownik jest przekierowywany do adresu URL w witrynie sieci Web](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  Użytkownik zostanie przekierowany do strony logowania firmy Microsoft, a użytkownik podaje poświadczenia, aby się zalogować.

    ![Pokazuje stronę logowania firmy Microsoft](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. Użytkownik wyrazi zgodę na aplikację.

    ![Przykład: Strona zgody dla aplikacji](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  Logowanie kończy się i użytkownik zostanie przekierowany z powrotem do witryny sieci Web.  Użytkownik uruchamia bezpłatną wersję próbną.

    ![Pokazuje środowisko, którego użytkownik widzi po przekierowaniu do lokacji](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Skontaktuj się z nami (środowisko próbne dla partnerów)

Możesz skorzystać z wersji próbnej partnera, gdy konieczne jest wykonanie ręcznej lub długoterminowej operacji w celu aprowizacji użytkownika/firmy — na przykład aplikacja musi udostępniać maszyny wirtualne, wystąpienia bazy danych lub operacje, które wymagają dużo czasu. W takim przypadku, gdy użytkownik wybierze przycisk **Żądaj wersji próbnej** i wypełni formularz, AppSource wyśle informacje kontaktowe użytkownika. Po otrzymaniu tych informacji możesz zainicjować obsługę środowiska i wysłać instrukcje do użytkownika w celu uzyskania dostępu do środowiska próbnego:<br/><br/>

1. Użytkownik odnajduje aplikację w witrynie sieci Web AppSource, a następnie wybiera **kontakt ze mną**.

    ![Wyświetlaj kontakt ze mną w celu korzystania z wersji próbnej dla partnera](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. Użytkownik wypełnia formularz informacjami kontaktowymi.

    ![Pokazuje przykładową formę z informacjami kontaktowymi](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Otrzymasz informacje o użytkowniku, skonfigurujesz wystąpienie próbne i wyślesz hiperlink w celu uzyskania dostępu do aplikacji.

    ![Pokazuje symbol zastępczy informacji o użytkowniku](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. Użytkownik uzyskuje dostęp do aplikacji i kończy proces logowania jednokrotnego.

    ![Pokazuje ekran logowania aplikacji](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. Użytkownik wyrazi zgodę na aplikację.

    ![Pokazuje przykładową stronę zgody dla aplikacji](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. Logowanie kończy się i użytkownik zostanie przekierowany z powrotem do witryny sieci Web. Użytkownik uruchamia bezpłatną wersję próbną.

    ![Pokazuje środowisko, którego użytkownik widzi po przekierowaniu do lokacji](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat korzystania z wersji próbnej usługi AppSource, zobacz [ten film wideo](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

W przypadku integracji z usługą Azure AD firma Microsoft zaleca, aby zapewnić pomoc techniczną [&A](/answers/topics/azure-active-directory.html) ze społecznością.

Zdecydowanie zalecamy, aby zadać pytania dotyczące [firmy Microsoft&pytań](/answers/topics/azure-active-directory.html) i przeszukiwać istniejące problemy, aby zobaczyć, czy ktoś zażądał wcześniej pytania. Upewnij się, że Twoje pytania lub komentarze są oznaczone przy użyciu [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Skorzystaj z poniższej sekcji komentarzy, aby przekazać Opinie i popomóc nam udoskonalić zawartość i kształtować ją.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia aplikacji obsługujących logowania do usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](authentication-flows-app-scenarios.md).
- Aby uzyskać informacje na temat sposobu wyświetlania listy aplikacji SaaS w programie AppSource, zobacz temat [Informacje o partnerze AppSource](https://appsource.microsoft.com/partners)