---
title: Samouczek — Dodawanie uwierzytelniania do aplikacji sieci Web na Azure App Service | Azure
description: W ramach tego samouczka nauczysz się, jak włączyć uwierzytelnianie i autoryzację dla aplikacji sieci Web działającej na Azure App Service. Ogranicz dostęp do aplikacji sieci Web użytkownikom w organizacji.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a8bd2ef1348692bf57f7e5cb7b6606cfcfd324fe
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905574"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Samouczek: Dodawanie uwierzytelniania do aplikacji sieci Web działającej na Azure App Service

Dowiedz się, jak włączyć uwierzytelnianie dla aplikacji sieci Web działającej na Azure App Service i ograniczyć dostęp do użytkowników w organizacji.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagram przedstawiający Logowanie użytkownika." border="false":::

App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji, dzięki czemu możesz zalogować użytkowników i uzyskać dostęp do danych, pisząc minimalny lub brak kodu w aplikacji sieci Web. Korzystanie z modułu uwierzytelniania App Service/autoryzacji nie jest wymagane, ale pomaga uprościć uwierzytelnianie i autoryzację aplikacji. W tym artykule pokazano, jak zabezpieczyć aplikację sieci Web za pomocą modułu App Service Authentication/Authorization przy użyciu Azure Active Directory (Azure AD) jako dostawcy tożsamości.

Moduł uwierzytelnianie/Autoryzacja jest włączony i konfigurowany za pomocą ustawień Azure Portal i aplikacji. Nie są wymagane żadne zestawy SDK, określone Języki ani zmiany w kodzie aplikacji. Obsługiwane są różne dostawcy tożsamości, w tym usługi Azure AD, konta Microsoft, Facebook, Google i Twitter. Po włączeniu modułu uwierzytelnianie/autoryzacja każde przychodzące żądanie HTTP przechodzi przez niego przed obsługą przez kod aplikacji. Aby dowiedzieć się więcej, zobacz temat [uwierzytelnianie i autoryzacja w Azure App Service](overview-authentication-authorization.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj uwierzytelnianie dla aplikacji sieci Web.
> * Ogranicz dostęp do aplikacji sieci Web użytkownikom w organizacji.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Tworzenie i publikowanie aplikacji sieci Web na App Service

W tym samouczku potrzebna jest aplikacja internetowa wdrożona w celu App Service. Możesz użyć istniejącej aplikacji sieci Web lub wykonać [ASP.NET Core przewodnika Szybki Start](quickstart-dotnetcore.md) , aby utworzyć i opublikować nową aplikację sieci web do App Service.

Bez względu na to, czy używasz istniejącej aplikacji sieci Web, czy utworzyć nową, zanotuj nazwę aplikacji sieci Web i nazwę grupy zasobów, w której wdrożono aplikację sieci Web. Te nazwy są potrzebne w tym samouczku. W tym samouczku przykładowe nazwy w procedurach i zrzuty ekranu zawierają *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Konfigurowanie uwierzytelniania i autoryzacji

Masz teraz aplikację sieci Web działającą na App Service. Następnie należy włączyć uwierzytelnianie i autoryzację dla aplikacji sieci Web. Używasz usługi Azure AD jako dostawcy tożsamości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania usługi Azure AD dla aplikacji App Service](configure-authentication-provider-aad.md).

W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **grupy zasobów** lub Wyszukaj i wybierz pozycję **grupy zasobów** z dowolnej strony.

W obszarze **grupy zasobów** Znajdź i wybierz grupę zasobów. W obszarze **Przegląd** wybierz stronę zarządzania aplikacji.

:::image type="content" alt-text="Zrzut ekranu pokazujący wybór strony zarządzania aplikacji." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

W menu po lewej stronie wybierz pozycję **uwierzytelnianie/autoryzacja**, a następnie włącz uwierzytelnianie App Service, wybierając pozycję **włączone**.

Z listy **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** wybierz pozycję **Zaloguj się za pomocą usługi Azure Active Directory**.

W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Express**, a następnie zaakceptuj ustawienia domyślne, aby utworzyć nową aplikację Active Directory. Wybierz przycisk **OK**.

:::image type="content" alt-text="Zrzut ekranu przedstawiający uwierzytelnianie ekspresowe." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Na stronie **uwierzytelnianie/autoryzacja** wybierz pozycję **Zapisz**.

Gdy zobaczysz powiadomienie z komunikatem `Successfully saved the Auth Settings for <app-name> App` , Odśwież stronę portalu.

Masz teraz aplikację, która jest zabezpieczona przez App Service uwierzytelnianie i autoryzację.

## <a name="verify-limited-access-to-the-web-app"></a>Weryfikowanie ograniczonego dostępu do aplikacji sieci Web

Po włączeniu modułu uwierzytelnianie i autoryzacja App Service Rejestracja aplikacji została utworzona w dzierżawie usługi Azure AD. Rejestracja aplikacji ma taką samą nazwę wyświetlaną jak aplikacja internetowa. Aby sprawdzić ustawienia, wybierz pozycję **Azure Active Directory** z menu Portal i wybierz pozycję **rejestracje aplikacji**. Wybierz utworzoną rejestrację aplikacji. W oknie Przegląd sprawdź, czy **obsługiwane typy kont** są ustawione **tylko na moją organizację**.

:::image type="content" alt-text="Zrzut ekranu pokazujący Weryfikowanie dostępu." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Aby sprawdzić, czy dostęp do aplikacji jest ograniczony do użytkowników w organizacji, uruchom przeglądarkę w trybie incognito lub prywatnym i przejdź do `https://<app-name>.azurewebsites.net` . Należy kierować do zabezpieczonej strony logowania, sprawdzając, czy nieuwierzytelnieni użytkownicy nie mają dostępu do witryny. Zaloguj się jako użytkownik w organizacji, aby uzyskać dostęp do tej witryny. Możesz również uruchomić nową przeglądarkę i spróbować zalogować się przy użyciu konta osobistego, aby sprawdzić, czy użytkownicy spoza organizacji nie mają dostępu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli skończysz pracę z tym samouczkiem i nie potrzebujesz już aplikacji sieci Web ani skojarzonych zasobów, [Wyczyść utworzone zasoby](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj uwierzytelnianie dla aplikacji sieci Web.
> * Ogranicz dostęp do aplikacji sieci Web użytkownikom w organizacji.

> [!div class="nextstepaction"]
> [Usługa App Service uzyskuje dostęp do magazynu](scenario-secure-app-access-storage.md)
