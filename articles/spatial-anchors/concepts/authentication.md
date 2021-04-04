---
title: Uwierzytelnianie i autoryzacja
description: Dowiedz się więcej na temat różnych sposobów uwierzytelniania aplikacji lub usługi w kotwicach przestrzennych platformy Azure, a także poziomów kontroli, które mają być bramą dostępu do kotwic przestrzennych.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722967"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Uwierzytelnianie i autoryzacja w kotwicach przestrzennych platformy Azure

W tym artykule przedstawiono różne sposoby uwierzytelniania w zakotwiczeniach przestrzennych platformy Azure z poziomu aplikacji lub usługi sieci Web. Dowiesz się również, jak korzystać z kontroli dostępu opartej na rolach (Azure RBAC) w usłudze Azure Active Directory (Azure AD) w celu kontrolowania dostępu do kont zakotwiczeń przestrzennych.

## <a name="overview"></a>Omówienie

![Diagram przedstawiający Omówienie uwierzytelniania w zakotwiczeniach przestrzennych platformy Azure.](./media/spatial-anchors-authentication-overview.png)

Aby uzyskać dostęp do danego konta zakotwiczeń przestrzennych platformy Azure, klienci muszą najpierw uzyskać token dostępu z usługi Azure Mixed Reality Security Token Service (STS). Tokeny uzyskane z usługi STS mają okres istnienia wynoszący 24 godziny. Zawierają one informacje, które są używane przez usługi zakotwiczenia przestrzennego do podejmowania decyzji dotyczących autoryzacji na koncie i zapewniają, że tylko autoryzowane podmioty zabezpieczeń mogą uzyskać dostęp do konta.

Tokeny dostępu można uzyskać w programie Exchange dla kluczy konta lub tokenów wystawionych przez usługę Azure AD.

Klucze kont umożliwiają szybkie rozpoczynanie pracy z użyciem usługi kotwic przestrzennych platformy Azure. Jednak przed wdrożeniem aplikacji w środowisku produkcyjnym Zalecamy zaktualizowanie aplikacji w taki sposób, aby korzystała z uwierzytelniania usługi Azure AD.

Tokeny uwierzytelniania usługi Azure AD można uzyskać na dwa sposoby:

- Jeśli tworzysz aplikację dla przedsiębiorstw, a Twoja firma korzysta z usługi Azure AD jako systemu tożsamości, możesz użyć uwierzytelniania opartego na użytkownikach usługi Azure AD w aplikacji. Następnie Udziel dostępu do kont kotwicy przestrzennej przy użyciu istniejących grup zabezpieczeń usługi Azure AD. Możesz również udzielić dostępu bezpośrednio użytkownikom w organizacji.
- W przeciwnym razie zalecamy uzyskanie tokenów usługi Azure AD z usług sieci Web, które obsługują aplikację. Zalecamy zastosowanie tej metody dla aplikacji produkcyjnych, ponieważ pozwala to uniknąć osadzania poświadczeń w celu uzyskania dostępu do kotwic w aplikacji klienckiej platformy Azure.

## <a name="account-keys"></a>Klucze konta

Najprostszym sposobem na rozpoczęcie pracy jest użycie kluczy konta w celu uzyskania dostępu do konta zakotwiczeń przestrzennych platformy Azure. Klucze kont można pobrać na Azure Portal. Przejdź do swojego konta i wybierz kartę **klucze** :

![Zrzut ekranu pokazujący kartę klucze z przyciskiem Kopiuj dla wyróżnionego klucza podstawowego.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Dostępne są dwa klucze. Oba jednocześnie są prawidłowe w przypadku dostępu do konta kotwice przestrzenne. Zalecamy regularne aktualizowanie klucza używanego do uzyskiwania dostępu do konta. Posiadanie dwóch oddzielnych prawidłowych kluczy pozwala na te aktualizacje bez przestojów. Wystarczy tylko zaktualizować klucz podstawowy i klucz pomocniczy.

Zestaw SDK ma wbudowaną obsługę uwierzytelniania za pośrednictwem kluczy konta. Wystarczy ustawić `AccountKey` Właściwość `cloudSession` obiektu:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Po ustawieniu tej właściwości zestaw SDK będzie obsługiwał wymianę klucza konta dla tokenu dostępu i wymagane buforowanie tokenów dla aplikacji.

> [!WARNING]
> Zalecamy używanie kluczy konta do szybkiego dołączania, ale tylko podczas tworzenia/prototypowania. Nie zalecamy dostarczania aplikacji do środowiska produkcyjnego z osadzonym kluczem konta. Zamiast tego należy skorzystać z opisanych dalej metod uwierzytelniania usługi Azure AD opartych na użytkowniku lub usłudze.

## <a name="azure-ad-user-authentication"></a>Uwierzytelnianie użytkowników w usłudze Azure AD

W przypadku aplikacji przeznaczonych dla użytkowników Azure Active Directory użytkownicy zalecamy używanie tokenu usługi Azure AD dla użytkownika. Ten token można uzyskać za pomocą [MSAL](../../active-directory/develop/msal-overview.md). Wykonaj kroki opisane w [przewodniku szybki start dotyczącym rejestrowania aplikacji, w](../../active-directory/develop/quickstart-register-app.md)tym:

**W witrynie Azure Portal**
1.    Zarejestruj swoją aplikację w usłudze Azure AD jako aplikację natywną. W ramach rejestracji należy określić, czy aplikacja powinna być wielodostępna. Należy również podać adresy URL przekierowania dozwolone dla aplikacji.
1.  Przejdź do karty **uprawnienia interfejsu API** .
2.  Wybierz pozycję **Dodaj uprawnienie**.
    1.  Wybierz pozycję **dostawca zasobów rzeczywistości mieszanej** w obszarze **interfejsy API Moja organizacja używa** karty.
    2.  Wybierz pozycję **uprawnienia delegowane**.
    3.  Wybierz pozycję **mixedreality. Signer** w obszarze **mixedreality**.
    4.  Wybierz pozycję **Dodaj uprawnienia**.
3.  Wybierz pozycję **Udziel zgody administratora**.

2. Udziel aplikacji lub użytkownikom dostępu do zasobu:
   1.    Przejdź do zasobu zakotwiczeń przestrzennych w Azure Portal.
   2.    Przejdź do karty **Kontrola dostępu (IAM)** .
   3.    Wybierz pozycję **Dodaj przypisanie roli**.
   1.    [Wybierz rolę](#azure-role-based-access-control).
   2.    W polu **Wybierz** wprowadź nazwy użytkowników, grup i/lub aplikacji, do których chcesz przypisać dostęp.
   3.    Wybierz pozycję **Zapisz**.

**W kodzie**
1.    Upewnij się, że używasz identyfikatora aplikacji i identyfikatora URI przekierowania własnej aplikacji usługi Azure AD dla **identyfikatora klienta** i parametrów **RedirectUri** w MSAL.
2.    Ustaw informacje o dzierżawie:
        1.    Jeśli aplikacja obsługuje **tylko moją organizację**, Zamień tę wartość na **Identyfikator dzierżawy** lub **nazwę dzierżawy**. Na przykład contoso.microsoft.com.
        2.    Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość **organizacją**.
        3.    Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, Zastąp tę wartość **wspólną**.
3.    Na żądanie tokenu Ustaw **zakres** na **" `https://sts.<account-domain>//.default` "**, gdzie `<account-domain>` jest zastępowana **domeną konta** dla konta zakotwiczeń przestrzennych platformy Azure. Przykładowym zakresem dla konta zakotwiczeń przestrzennych platformy Azure w domenie konta Wschodnie stany USA 2 jest **" `https://sts.mixedreality.azure.com//.default` "**. Ten zakres wskaże usługę Azure AD, że aplikacja żąda tokenu dla usługi tokenu zabezpieczeń rzeczywistości mieszanej (STS).

Po wykonaniu tych kroków aplikacja powinna mieć możliwość uzyskania od MSAL tokenu usługi Azure AD. Możesz ustawić ten token usługi Azure AD jako `authenticationToken` obiekt konfiguracji sesji w chmurze:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Uwierzytelnianie usługi Azure AD

W celu wdrożenia aplikacji, które korzystają z kotwic przestrzennych platformy Azure do środowiska produkcyjnego, zalecamy użycie usługi zaplecza, która będzie przetwarzać żądania uwierzytelniania brokera. Poniżej przedstawiono omówienie procesu:

![Diagram, który zawiera omówienie uwierzytelniania w zakotwiczeniach przestrzennych platformy Azure.](./media/spatial-anchors-aad-authentication.png)

W tym miejscu zakłada się, że aplikacja używa własnego mechanizmu do uwierzytelniania w usłudze zaplecza. (Na przykład konto Microsoft, PlayFab, Facebook, identyfikator Google lub niestandardową nazwę użytkownika i hasło).  Po uwierzytelnieniu użytkowników w usłudze zaplecza usługa ta może pobrać token usługi Azure AD, wymienić go na token dostępu dla kotwic przestrzennych platformy Azure i przywrócić go do aplikacji klienckiej.

Token dostępu usługi Azure AD jest pobierany za pośrednictwem [MSAL](../../active-directory/develop/msal-overview.md). Wykonaj kroki opisane w sekcji [Rejestrowanie przewodnika Szybki Start](../../active-directory/develop/quickstart-register-app.md)dotyczącego aplikacji, w tym:

**W witrynie Azure Portal**
1.    Zarejestruj swoją aplikację w usłudze Azure AD:
        1.    W Azure Portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.
        2.    Wybierz pozycję **Nowa rejestracja**.
        3.    Wprowadź nazwę aplikacji, wybierz pozycję **Web App/API** jako typ aplikacji, a następnie wprowadź adres URL uwierzytelniania dla usługi. Wybierz przycisk **Utwórz**.
2.    W aplikacji wybierz pozycję **Ustawienia**, a następnie wybierz kartę **Certyfikaty i wpisy tajne** . Utwórz nowy klucz tajny klienta, wybierz czas trwania, a następnie wybierz pozycję **Dodaj**. Pamiętaj, aby zapisać wartość klucza tajnego. Musisz dołączyć go do kodu usługi sieci Web.
3.    Przyznaj aplikacji i/lub użytkownikom dostęp do zasobu:
        1.    Przejdź do zasobu zakotwiczeń przestrzennych w Azure Portal.
        2.    Przejdź do karty **Kontrola dostępu (IAM)** .
        3.    Wybierz pozycję **Dodaj przypisanie roli**.
        4.    [Wybierz rolę](#azure-role-based-access-control).
        5.    W polu **Wybierz** wprowadź nazwę lub nazwy aplikacji, do których chcesz przypisać dostęp. Jeśli chcesz, aby użytkownicy Twojej aplikacji mieli różne role względem konta zakotwiczeń przestrzennych, zarejestruj wiele aplikacji w usłudze Azure AD i przypisz osobną rolę do każdej z nich. Następnie Zaimplementuj logikę autoryzacji, aby korzystać z odpowiedniej roli dla użytkowników.

              > [!NOTE]
              > W okienku **Dodaj przypisanie roli** w obszarze **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.

        6.    Wybierz pozycję **Zapisz**.

**W kodzie**

>[!NOTE]
> Możesz użyć [przykładu usługi](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) , która jest dostępna w ramach [przykładowych aplikacji kotwicowych](https://github.com/Azure/azure-spatial-anchors-samples).

1.    Upewnij się, że używasz identyfikatora aplikacji, klucza tajnego aplikacji i identyfikatora URI przekierowania dla własnej aplikacji usługi Azure AD jako parametry **identyfikatora klienta**, **klucza tajnego** i **RedirectUri** w MSAL.
2.    W parametrze **urzędu** w MSAL należy ustawić identyfikator dzierżawy na własny identyfikator dzierżawy usługi Azure AD.
3.    Na żądanie tokenu Ustaw **zakres** na **" `https://sts.<account-domain>//.default` "**, gdzie `<account-domain>` jest zastępowana **domeną konta** dla konta zakotwiczeń przestrzennych platformy Azure. Przykładowym zakresem dla konta zakotwiczeń przestrzennych platformy Azure w domenie konta Wschodnie stany USA 2 jest **" `https://sts.mixedreality.azure.com//.default` "**.

Po wykonaniu tych kroków usługa zaplecza może pobrać token usługi Azure AD. Następnie może go wymienić na token MR, który zwróci zwrot do klienta. Użycie tokenu usługi Azure AD w celu pobrania tokenu MR odbywa się za pośrednictwem wywołania REST. Oto przykładowe wywołanie:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Nagłówek autoryzacji jest sformatowany w następujący sposób: `Bearer <Azure_AD_token>`

Odpowiedź zawiera token MR w postaci zwykłego tekstu.

Token MR jest następnie zwracany do klienta. Aplikacja kliencka może następnie ustawić ją jako token dostępu w konfiguracji sesji w chmurze:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Aby ułatwić kontrolę poziomu dostępu udzielonego aplikacjom, usługom lub użytkownikom usługi Azure AD w usłudze, możesz przypisać te istniejące role zgodnie z potrzebami dotyczącymi kont zakotwiczeń przestrzennych platformy Azure:

- **Właściciel konta zakotwiczeń przestrzennych**. Aplikacje lub Użytkownicy, którzy mają tę rolę, mogą tworzyć kotwice przestrzenne, wysyłać do nich zapytania i usuwać je. W przypadku uwierzytelniania na koncie przy użyciu kluczy konta rola właściciela konta kotwicy przestrzennej jest przypisana do uwierzytelnionego podmiotu zabezpieczeń.
- **Współautor konta kotwic przestrzennych**. Aplikacje lub Użytkownicy, którzy mają tę rolę mogą tworzyć kotwice przestrzenne i wykonywać dla nich zapytania, ale nie mogą ich usuwać.
- **Czytnik konta zakotwiczeń przestrzennych**. Aplikacje lub Użytkownicy, którzy mają tę rolę mogą wykonywać zapytania tylko dla kotwic przestrzennych. Nie mogą tworzyć nowych, usuwać istniejących lub aktualizować metadanych. Ta rola jest zwykle używana w przypadku aplikacji, w których niektórzy użytkownicy nadzorują środowisko, ale inne mogą odwoływać kotwice wcześniej umieszczone w środowisku.

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację z zakotwiczeniami przestrzennymi platformy Azure:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
