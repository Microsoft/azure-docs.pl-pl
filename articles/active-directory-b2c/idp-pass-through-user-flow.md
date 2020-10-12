---
title: Przekazywanie tokenu dostępu za pomocą przepływu użytkownika do aplikacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2,0 jako rolę w przepływie użytkownika w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b834dda926b7da1241a325e1453143eccafaf30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488775"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą przepływu użytkownika do aplikacji w Azure Active Directory B2C

[Przepływ użytkownika](user-flow-overview.md) w Azure Active Directory B2C (Azure AD B2C) zapewnia użytkownikom aplikacji możliwość rejestrowania się lub logowania za pomocą dostawcy tożsamości. Po rozpoczęciu podróży Azure AD B2C otrzymuje [token dostępu](tokens-overview.md) od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. W przepływie użytkownika można włączyć funkcję przekazywania tokenu do aplikacji, które są rejestrowane w Azure AD B2C.

Azure AD B2C obecnie obsługuje tylko przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](authorization-code-flow.md) , w tym [Facebook](identity-provider-facebook.md) i [Google](identity-provider-google.md). W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja musi używać [zalecanego przepływu użytkownika](user-flow-versions.md).
* Przepływ użytkownika jest konfigurowany za pomocą dostawcy tożsamości OAuth 2,0.

## <a name="enable-the-claim"></a>Włączanie tego żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)**, a następnie wybierz swój przepływ użytkownika. Na przykład **B2C_1_signupsignin1**.
5. Wybierz pozycję **Oświadczenia aplikacji**.
6. Włącz funkcję **token dostępu dostawcy tożsamości** .

    ![Włączanie żądania tokenu dostępu dostawcy tożsamości](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknij przycisk **Zapisz** , aby zapisać przepływ użytkownika.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

W przypadku testowania aplikacji w Azure AD B2C może być przydatne, aby token Azure AD B2C mógł `https://jwt.ms` sprawdzić w nim oświadczenia.

1. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms` .
3. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu poświadczeń konta. Powinien zostać wyświetlony token dostępu dostawcy tożsamości w **idp_access_tokenm** .

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Następne kroki

Więcej informacji znajduje się w temacie [Omówienie tokenów Azure AD B2C](tokens-overview.md).
