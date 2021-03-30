---
title: Konfigurowanie uwierzytelniania firmy Microsoft
description: Dowiedz się, jak skonfigurować uwierzytelnianie konta Microsoft jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043240"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Skonfiguruj App Service lub aplikację Azure Functions do używania logowania do konta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service lub Azure Functions do korzystania z usługi AAD do obsługi osobistych logowań konto Microsoft.

> [!NOTE]
> Zarówno osobiste konta Microsoft, jak i konta organizacji korzystają z dostawcy tożsamości usługi AAD. W tej chwili nie jest możliwe skonfigurowanie tego dostawcy tożsamości w taki sposób, aby obsługiwał oba typy logowań.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Zarejestruj aplikację na koncie Microsoft

1. Przejdź do [**rejestracje aplikacji**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal. W razie konieczności Zaloguj się przy użyciu konto Microsoft.
1. Wybierz pozycję **Nowa rejestracja**, a następnie wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**
1. W obszarze **identyfikatory URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź `https://<app-domain-name>/.auth/login/aad/callback` . Zamień na *\<app-domain-name>* nazwę domeny aplikacji.  Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Upewnij się, że w adresie URL jest używany schemat HTTPS.

1. Wybierz pozycję **Zarejestruj**.
1. Skopiuj **Identyfikator aplikacji (klienta)**. Będzie on potrzebny później.
1. W lewym okienku wybierz pozycję **Certificates &**  >  **Secret New Client tajny**. Wprowadź opis, wybierz okres ważności, a następnie wybierz pozycję **Dodaj**.
1. Skopiuj wartość, która pojawia się na stronie **certyfikaty & wpisy tajne** . Po opuszczeniu strony nie będzie ona ponownie wyświetlana.

    > [!IMPORTANT]
    > Wartość klucza tajnego klienta (hasło) jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu hasła ani nie rozpowszechniaj go w aplikacji klienckiej.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Dodawanie informacji o koncie Microsoft do aplikacji App Service

1. Przejdź do aplikacji w [Azure Portal].
1. Wybierz pozycję **Ustawienia**  >  **uwierzytelnianie/autoryzacja** i upewnij się, że **uwierzytelnianie App Service** jest **włączone**.
1. W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Zaawansowane** w obszarze **tryb zarządzania**. Wklej do wcześniej uzyskanego identyfikatora aplikacji (klienta) i wpisu tajnego klienta. Użyj **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** w polu **adres URL wystawcy** .
1. Wybierz przycisk **OK**.

   App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.

1. Obowiązkowe Aby ograniczyć dostęp do konto Microsoft użytkowników, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się przy użyciu Azure Active Directory**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione żądania do uwierzytelniania za pomocą usługi AAD. Zwróć uwagę, że ponieważ **adres URL wystawcy** został skonfigurowany tak, aby korzystał z dzierżawy konta Microsoft, tylko konta osobiste będą pomyślnie uwierzytelniane.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (żadna akcja)** może być preferowana, aby aplikacja ręcznie uruchamiała sam uwierzytelnienie. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz pozycję **Zapisz**.

Teraz możesz przystąpić do uwierzytelniania w aplikacji za pomocą konta Microsoft.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Witryna Azure Portal]: https://portal.azure.com/
