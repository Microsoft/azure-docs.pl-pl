---
title: 'Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft | Azure'
description: W tym przewodniku szybki start dowiesz się, jak zarejestrować aplikację za pomocą platformy tożsamości firmy Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4e9e6c9179c7fbf405cf8fc661a209da3c9d3b61
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031270"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft

W tym przewodniku szybki start zarejestrujesz aplikację w Azure Portal, dzięki czemu platforma tożsamości firmy Microsoft może udostępniać usługi uwierzytelniania i autoryzacji dla aplikacji i jej użytkowników.

Każda aplikacja, która ma zostać zarejestrowana przez platformę tożsamości firmy Microsoft do zarządzania tożsamościami i dostępem (IAM). Bez względu na to, czy jest to aplikacja kliencka, jak aplikacja sieci Web, czy też jest to internetowy interfejs API, który wykonuje kopię zapasową aplikacji klienckiej, rejestrując ją ustanawia relację zaufania między aplikacją i dostawcą tożsamości, platformą tożsamości firmy Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją — [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Kończenie [przewodnika Szybki Start: Konfigurowanie dzierżawy](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Zarejestrowanie aplikacji ustanawia relację zaufania między aplikacją i platformą tożsamości firmy Microsoft. Zaufanie jest jednokierunkowe: Twoja aplikacja ufa platformie tożsamości firmy Microsoft, a nie odwrotnie.

Wykonaj następujące kroki, aby utworzyć rejestrację aplikacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. Określ, kto może korzystać z aplikacji, czasami nazywaną *odbiorcami logowania*.

    | Obsługiwane typy kont | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli tworzysz aplikację do użycia tylko przez użytkowników (lub Gości *) w* dzierżawie.<br><br>Często nazywana jest aplikacją biznesową (LOB, *line-of-Business* ), jest to aplikacja o **pojedynczej dzierżawie** na platformie tożsamości firmy Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli chcesz, aby użytkownicy w *dowolnej* dzierżawie usługi Azure AD mogli korzystać z aplikacji. Ta opcja jest odpowiednia, jeśli na przykład tworzysz aplikację "oprogramowanie jako usługa" (SaaS), którą zamierzasz udostępnić w wielu organizacjach.<br><br>Jest to tzw. aplikacja **wielodostępna** na platformie tożsamości firmy Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Po wybraniu tej opcji rejestrujesz aplikację **wielodostępną** , która może również obsługiwać użytkowników z osobistymi **kontami Microsoft** (MSA). |
    | **Osobiste konta Microsoft** | Wybierz tę opcję, jeśli tworzysz aplikację do użycia tylko przez użytkowników z osobistymi kontami Microsoft. Osobiste konta Microsoft to konta Skype, Xbox, Live i hotmail. |

1. Nie wprowadzaj niczego dla **identyfikatora URI przekierowania (opcjonalnie)** skonfigurujesz go w następnej sekcji.
1. Wybierz pozycję **zarejestruj** , aby ukończyć początkową rejestrację aplikacji.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Zrzut ekranu przedstawiający Azure Portal w przeglądarce internetowej pokazujący okienko Zarejestruj aplikację.":::

Po zakończeniu rejestracji Azure Portal wyświetla okienko **Przegląd** rejestracji aplikacji, w tym jego **Identyfikator aplikacji (klienta)**. Ta wartość jest również określana jako tylko *Identyfikator klienta*, a ta wartości jednoznacznie identyfikuje aplikację na platformie tożsamości firmy Microsoft.

Kod aplikacji lub zwykle Biblioteka uwierzytelniania użyta w aplikacji również używa identyfikatora klienta jako jednego aspektu podczas weryfikowania tokenów zabezpieczających odbieranych z platformy tożsamości.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Zrzut ekranu przedstawiający Azure Portal w przeglądarce internetowej z okienkiem przegląd rejestracji aplikacji.":::

## <a name="add-a-redirect-uri"></a>Dodaj identyfikator URI przekierowania

Identyfikator URI przekierowania to lokalizacja, w której platforma tożsamości firmy Microsoft przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.

Na przykład w produkcyjnej aplikacji sieci Web identyfikator URI przekierowania jest często publicznym punktem końcowym, w którym działa aplikacja `https://contoso.com/auth-response` . Podczas opracowywania warto również dodać punkt końcowy, w którym uruchamiasz aplikację lokalnie, na przykład `https://127.0.0.1/auth-response` lub `http://localhost/auth-response` .

Aby dodać i zmodyfikować identyfikatory URI przekierowania dla zarejestrowanych aplikacji, należy skonfigurować [Ustawienia platformy](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurowanie ustawień platformy

Ustawienia dla każdego typu aplikacji, w tym identyfikatory URI przekierowania, są konfigurowane w **konfiguracjach platformy** w Azure Portal. Niektóre platformy, takie jak aplikacje **sieci Web** i **jednostronicowe**, wymagają ręcznego określenia identyfikatora URI przekierowania. W przypadku innych platform, takich jak Mobile i Desktop, można wybierać spośród identyfikatorów URI przekierowania generowanych dla Ciebie podczas konfigurowania innych ustawień.

Aby skonfigurować ustawienia aplikacji na podstawie docelowej platformy lub urządzenia:

1. Wybierz aplikację w **rejestracje aplikacji** w Azure Portal.
1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Konfiguracja platformy** wybierz pozycję **Dodaj platformę**.
1. Na stronie **Konfigurowanie platform** wybierz kafelek dla typu aplikacji (platformy), aby skonfigurować jego ustawienia.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Zrzut ekranu przedstawiający okienko Konfiguracja platformy w Azure Portal" border="false":::

    | Platforma | Ustawienia konfiguracji |
    | -------- | ---------------------- |
    | **Sieć Web** | Wprowadź **Identyfikator URI przekierowania** dla aplikacji, lokalizację, w której platforma Microsoft Identity przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.<br/><br/>Wybierz tę platformę dla standardowych aplikacji sieci Web, które są uruchomione na serwerze. |
    | **Aplikacja jednostronicowa** | Wprowadź **Identyfikator URI przekierowania** dla aplikacji, lokalizację, w której platforma Microsoft Identity przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.<br/><br/>Wybierz tę platformę, jeśli tworzysz aplikację sieci Web po stronie klienta w języku JavaScript lub przy użyciu struktury, takiej jak kątowy, Vue.js, React.js lub Blazor webassembly. |
    | **iOS/macOS** | Wprowadź **Identyfikator pakietu** aplikacji, który znajduje się w Xcode w *info. plist* lub Build Settings.<br/><br/>Podczas określania identyfikatora pakietu zostanie wygenerowany identyfikator URI przekierowania. |
    | **Android** | Wprowadź **nazwę pakietu** aplikacji, którą można znaleźć w pliku *AndroidManifest.xml* i wygenerować i wprowadzić **skrót sygnatury**.<br/><br/>Po określeniu tych ustawień zostanie wygenerowany identyfikator URI przekierowania. |
    | **Aplikacje mobilne i klasyczne** | Wybierz jeden z **sugerowanych identyfikatorów URI przekierowania** lub Określ **niestandardowy identyfikator URI przekierowania**.<br/>W przypadku aplikacji klasycznych zalecamy:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Wybierz tę platformę dla aplikacji mobilnych, które nie używają najnowszej biblioteki uwierzytelniania firmy Microsoft (MSAL) lub nie korzystają z brokera. Należy również wybrać tę platformę dla aplikacji klasycznych. |
1. Wybierz pozycję **Konfiguruj** , aby zakończyć konfigurację platformy.

### <a name="redirect-uri-restrictions"></a>Ograniczenia identyfikatorów URI przekierowania

Istnieją pewne ograniczenia dotyczące formatu identyfikatorów URI przekierowania dodawanych do rejestracji aplikacji. Aby uzyskać szczegółowe informacje o tych ograniczeniach, zobacz [ograniczenia i ograniczenia dotyczące URI przekierowania (adres URL odpowiedzi)](reply-url.md).

## <a name="add-credentials"></a>Dodawanie poświadczeń

Poświadczenia są używane przez poufne aplikacje klienckie, które uzyskują dostęp do internetowego interfejsu API. Przykładami poufnych klientów są aplikacje sieci Web, inne interfejsy API sieci Web lub aplikacje typu Service-and-daemon. Poświadczenia umożliwiają aplikacji uwierzytelnianie jako same, co nie wymaga interakcji z użytkownikiem w czasie wykonywania.

Do rejestracji poufnej aplikacji klienckiej można dodać zarówno certyfikaty, jak i klucze tajne klienta (ciąg) jako poświadczenia.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania certyfikatów i wpisów tajnych w rejestracji aplikacji":::

### <a name="add-a-certificate"></a>Dodawanie certyfikatu

Czasami nazywa się *kluczem publicznym*, certyfikat jest zalecanym typem poświadczeń, ponieważ zapewnia wyższy poziom pewności niż klucz tajny klienta.

1. Wybierz aplikację w **rejestracje aplikacji** w Azure Portal.
1. Wybierz pozycję **Certificates &** Secret  >  **upload Certificate**.
1. Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
1. Wybierz pozycję **Dodaj**.

### <a name="add-a-client-secret"></a>Dodawanie klucza tajnego klienta

Klucz tajny klienta, znany również jako *hasło aplikacji*, to wartość ciągu, która może być używana przez aplikację zamiast certyfikatu do samodzielnej tożsamości. Jest to łatwiejsze w użyciu dwa typy poświadczeń, które są często używane podczas opracowywania, ale są uznawane za mniej bezpieczne niż certyfikat. Certyfikaty należy używać w aplikacjach działających w środowisku produkcyjnym.

1. Wybierz aplikację w **rejestracje aplikacji** w Azure Portal.
1. Wybierz pozycję **Certyfikaty & klucz** tajny  >   **nowy klucz tajny klienta**.
1. Dodaj opis wpisu tajnego klienta.
1. Wybierz czas trwania.
1. Wybierz pozycję **Dodaj**.
1. **Zapisz wartość wpisu tajnego** do użycia w kodzie aplikacji klienta — *nigdy nie jest ona wyświetlana ponownie* po opuszczeniu tej strony.

## <a name="next-steps"></a>Następne kroki

Aplikacje klienckie zazwyczaj muszą uzyskiwać dostęp do zasobów w internetowym interfejsie API. Oprócz ochrony aplikacji klienckiej za pomocą platformy tożsamości firmy Microsoft można używać platformy do autoryzowania zakresu, opartego na uprawnieniach dostępu do internetowego interfejsu API.

Przejdź do następnego przewodnika Szybki Start w serii, aby utworzyć kolejną rejestrację aplikacji dla internetowego interfejsu API i uwidocznić jej zakresy.

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji w celu udostępnienia internetowego interfejsu API](quickstart-configure-app-expose-web-apis.md)
