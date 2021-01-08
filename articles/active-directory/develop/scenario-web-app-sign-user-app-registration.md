---
title: Rejestrowanie aplikacji sieci Web, która loguje się do użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zarejestrować aplikację sieci Web, która loguje się do użytkowników
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7ca294d3d7764737d4e2be55aae25bc8ff08a8d1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011530"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplikacja sieci Web, która loguje się do użytkowników: Rejestracja aplikacji

W tym artykule opisano specyficzne dla rejestracji aplikacji aplikacje sieci Web, które logują się w użytkownikach.

Aby zarejestrować aplikację, możesz użyć:

- [Aplikacja sieci Web — szybki start](#register-an-app-by-using-the-quickstarts). Oprócz tworzenia aplikacji, Przewodniki Szybki Start w Azure Portal zawierają przycisk o nazwie **Utwórz tę zmianę dla mnie**. Ten przycisk służy do ustawiania właściwości, które są potrzebne, nawet w przypadku istniejącej aplikacji. Należy dostosować wartości tych właściwości do własnego przypadku. W szczególności adres URL internetowego interfejsu API dla aplikacji prawdopodobnie będzie inny niż proponowana wartość domyślna, co wpłynie również na identyfikator URI wylogowania.
- Azure Portal, aby [ręcznie zarejestrować aplikację](#register-an-app-by-using-the-azure-portal).
- Program PowerShell i narzędzia wiersza polecenia.

## <a name="register-an-app-by-using-the-quickstarts"></a>Rejestrowanie aplikacji przy użyciu przewodników Szybki Start

Możesz użyć tych linków do uruchomienia uruchamiania aplikacji sieci Web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Rejestrowanie aplikacji przy użyciu Azure Portal

> [!NOTE]
> Portal, który ma być używany, różni się w zależności od tego, czy aplikacja działa w Microsoft Azureej chmurze publicznej czy w chmurze krajowej lub suwerennej. Aby uzyskać więcej informacji, zobacz [chmury narodowe](./authentication-national-cloud.md#app-registration-endpoints).


1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. 
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wprowadź **nazwę** aplikacji, na przykład `AspNetCore-WebApp` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
   1. Wybierz obsługiwane typy kont dla swojej aplikacji. (Zobacz [obsługiwane typy kont](./v2-supported-account-types.md)).
   1. Dla **identyfikatora URI przekierowania** Dodaj typ aplikacji i miejsce docelowe identyfikatora URI, które będą akceptować zwrócone odpowiedzi na tokeny po pomyślnym uwierzytelnieniu. Na przykład wprowadź `https://localhost:44321`.
   1. Wybierz pozycję **Zarejestruj**.
1. W obszarze **Zarządzanie** wybierz pozycję **uwierzytelnianie** , a następnie Dodaj następujące informacje:
   1. W sekcji **sieci Web** Dodaj `https://localhost:44321/signin-oidc` jako **Identyfikator URI przekierowania**.
   1. Dodaj `https://localhost:44321/signout-oidc` jako **adres URL wylogowania**.
   1. W obszarze **Niejawne przyznanie** wybierz pozycję **Tokeny identyfikatorów**.
   1. Wybierz pozycję **Zapisz**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji:
   1. Wprowadź **nazwę** aplikacji, na przykład `MailApp-openidconnect-v2` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
   1. Wybierz obsługiwane typy kont dla swojej aplikacji. (Zobacz [obsługiwane typy kont](./v2-supported-account-types.md)).
   1. W sekcji **Identyfikator URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujący identyfikator URI przekierowania: **https://localhost:44326/** .
   1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W sekcji **niejawne przyznanie** wybierz pozycję **identyfikatory tokenów**. Ten przykład wymaga włączenia [niejawnego przepływu przydzielenia](v2-oauth2-implicit-grant-flow.md) w celu zalogowania użytkownika.
1. Wybierz pozycję **Zapisz**.

# <a name="java"></a>[Java](#tab/java)

1. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji: 
    1. Wprowadź **nazwę** aplikacji, na przykład `java-webapp` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić. 
    1. Wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)**.
    1. Wybierz pozycję **zarejestruj** , aby zarejestrować aplikację.
1. W obszarze **Zarządzanie** wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**.
1. Wybierz pozycję **Sieć Web**.
1. W polu **Identyfikator URI przekierowania** wprowadź ten sam Host i numer portu, a następnie pozycję `/msal4jsample/secure/aad` dla strony logowania. 
1. Wybierz pozycję **Konfiguruj**.
1. W sekcji **sieci Web** Użyj hosta i numeru portu, a następnie **/msal4jsample/Graph/Me** jako **Identyfikator URI przekierowania** dla strony informacje o użytkowniku.
Domyślnie użycie przykładu:
   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

1. Wybierz pozycję **Zapisz**.
1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**.
1. W sekcji wpisy **tajne klienta** wybierz pozycję **nowy klucz tajny klienta**, a następnie:

   1. Wprowadź opis klucza.
   1. Wybierz czas trwania klucza **w 1 roku**.
   1. Wybierz pozycję **Dodaj**.
   1. Gdy zostanie wyświetlona wartość klucza, skopiuj ją do nowszej wersji. Ta wartość nie będzie ponownie wyświetlana ani nie można jej pobrać w inny sposób.

# <a name="python"></a>[Python](#tab/python)

1. Gdy zostanie wyświetlona **Strona Zarejestruj aplikację** , wprowadź informacje o rejestracji aplikacji:
   1. Wprowadź **nazwę** aplikacji, na przykład `python-webapp` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
   1. Zmień **obsługiwane typy kont** na **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)**.
   1. W sekcji **Identyfikator URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujący identyfikator URI przekierowania: **http://localhost:5000/getAToken** .
   1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Będzie ona potrzebna do skonfigurowania pliku konfiguracji programu Visual Studio dla tego projektu.
1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**.
1. W sekcji wpisy **tajne klienta** wybierz pozycję **nowy klucz tajny klienta**, a następnie:
   1. Wprowadź opis klucza.
   1. Wybierz kluczowy czas trwania wynoszący **1 rok**.
   1. Wybierz pozycję **Dodaj**.
   1. Gdy zostanie wyświetlona wartość klucza, skopiuj ją. Będzie on potrzebny później.
---

## <a name="register-an-app-by-using-powershell"></a>Rejestrowanie aplikacji przy użyciu programu PowerShell

> [!NOTE]
> Obecnie program Azure AD PowerShell tworzy aplikacje zawierające tylko następujące obsługiwane typy kont:
>
> - MyOrg (konta tylko w tym katalogu organizacji)
> - AnyOrg (konta w dowolnym katalogu organizacyjnym)
>
> Możesz utworzyć aplikację, która loguje się do użytkowników przy użyciu osobistych kont Microsoft (na przykład Skype, Xbox lub Outlook.com). Najpierw Utwórz aplikację wielodostępną. Obsługiwane typy kont to konta w dowolnym katalogu organizacyjnym. Następnie zmień [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) Właściwość na wartość **2** i [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) Właściwość na `AzureADandPersonalMicrosoftAccount` w [manifeście aplikacji](./reference-app-manifest.md) z Azure Portal. Aby uzyskać więcej informacji, zobacz [krok 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) w samouczku ASP.NET Core. Możesz uogólnić ten krok do aplikacji sieci Web w dowolnym języku.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Konfiguracja kodu aplikacji](scenario-web-app-sign-user-app-configuration.md).
