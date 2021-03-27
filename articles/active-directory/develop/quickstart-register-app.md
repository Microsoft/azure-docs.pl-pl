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
ms.openlocfilehash: fa5ecd19863e8a37efdf533e68aeaed053c552f8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625683"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft

W tym przewodniku szybki start zarejestrujesz aplikację w Azure Portal, dzięki czemu platforma tożsamości firmy Microsoft może udostępniać usługi uwierzytelniania i autoryzacji dla aplikacji i jej użytkowników.

Platforma tożsamości firmy Microsoft przeprowadza Zarządzanie tożsamościami i dostępem tylko dla zarejestrowanych aplikacji. Bez względu na to, czy jest to aplikacja kliencka, jak aplikacja sieci Web, czy też jest to internetowy interfejs API, który wykonuje kopię zapasową aplikacji klienckiej, rejestrując ją ustanawia relację zaufania między aplikacją i dostawcą tożsamości, platformą tożsamości firmy Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zakończenie [konfigurowania dzierżawy](quickstart-create-new-tenant.md) — Szybki Start.

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Zarejestrowanie aplikacji ustanawia relację zaufania między aplikacją i platformą tożsamości firmy Microsoft. Zaufanie jest jednokierunkowe: Twoja aplikacja ufa platformie tożsamości firmy Microsoft, a nie odwrotnie.

Wykonaj następujące kroki, aby utworzyć rejestrację aplikacji:

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, w górnym menu Użyj filtru **katalogów i subskrypcji** , :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: Aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** wyświetlaną aplikacji. Użytkownicy Twojej aplikacji mogą zobaczyć nazwę wyświetlaną, gdy używają aplikacji, na przykład podczas logowania.
    Możesz zmienić nazwę wyświetlaną w dowolnym momencie, a wiele rejestracji aplikacji może współużytkować tę samą nazwę. Identyfikator aplikacji (klienta) automatycznie wygenerowany przez rejestrację aplikacji, a nie jej nazwa wyświetlana, jednoznacznie identyfikuje aplikację na platformie tożsamości.
1. Określ, kto może korzystać z aplikacji, czasami nazywanych *odbiorcami logowania*.

    | Obsługiwane typy kont | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli tworzysz aplikację do użycia tylko przez użytkowników (lub Gości *) w* dzierżawie.<br><br>Ta aplikacja jest często nazywana aplikacją biznesową (LOB, *line-of-Business* ), która jest aplikacją *jednodostępną* na platformie tożsamości firmy Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli chcesz, aby użytkownicy w *dowolnej* dzierżawie usługi Azure Active Directory (Azure AD) mogli korzystać z aplikacji. Ta opcja jest odpowiednia, jeśli na przykład tworzysz aplikację "oprogramowanie jako usługa" (SaaS), którą zamierzasz udostępnić w wielu organizacjach.<br><br>Ten typ aplikacji jest znany jako aplikacja *wielodostępna* na platformie tożsamości firmy Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Po wybraniu tej opcji rejestrujesz aplikację *wielodostępną* , która może również obsługiwać użytkowników, którzy mają osobiste *konta Microsoft*. |
    | **Osobiste konta Microsoft** | Wybierz tę opcję, jeśli tworzysz aplikację tylko dla użytkowników, którzy mają osobiste konta Microsoft. Osobiste konta Microsoft to konta Skype, Xbox, Live i hotmail. |

1. Nie wprowadzaj niczego dla **identyfikatora URI przekierowania (opcjonalne)**. W następnej sekcji skonfigurujesz identyfikator URI przekierowania.
1. Wybierz pozycję **zarejestruj** , aby ukończyć początkową rejestrację aplikacji.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Zrzut ekranu przedstawiający Azure Portal w przeglądarce internetowej, pokazujący okienko Zarejestruj aplikację.":::

Po zakończeniu rejestracji Azure Portal wyświetla okienko **Przegląd** rejestracji aplikacji. Zobaczysz **Identyfikator aplikacji (klienta)**. Ta wartość jest nazywana również *identyfikatorem klienta*, a ta wartością jednoznacznie identyfikuje aplikację na platformie tożsamości firmy Microsoft. 

> [!IMPORTANT]
> Nowe rejestracje aplikacji są domyślnie ukryte dla użytkowników. Gdy wszystko będzie gotowe do wyświetlania aplikacji na [stronie Moje aplikacje](../user-help/my-apps-portal-end-user-access.md) , możesz ją włączyć. Aby włączyć aplikację, w Azure Portal przejdź do aplikacji **Azure Active Directory**  >  **Enterprise** i wybierz aplikację. Następnie na stronie **Właściwości** przełączenie jest **widoczne dla użytkowników?**

Kod aplikacji lub zwykle Biblioteka uwierzytelniania używana w aplikacji używa również identyfikatora klienta. Identyfikator jest używany w ramach walidacji tokenów zabezpieczających odbieranych z platformy tożsamości.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Zrzut ekranu przedstawiający Azure Portal w przeglądarce internetowej z okienkiem przegląd rejestracji aplikacji.":::

## <a name="add-a-redirect-uri"></a>Dodaj identyfikator URI przekierowania

*Identyfikator URI przekierowania* to lokalizacja, w której platforma tożsamości firmy Microsoft przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.

Na przykład w produkcyjnej aplikacji sieci Web identyfikator URI przekierowania jest często publicznym punktem końcowym, w którym działa aplikacja `https://contoso.com/auth-response` . Podczas opracowywania warto również dodać punkt końcowy, w którym uruchamiasz aplikację lokalnie, na przykład `https://127.0.0.1/auth-response` lub `http://localhost/auth-response` .

Aby dodać i zmodyfikować identyfikatory URI przekierowania dla zarejestrowanych aplikacji, należy skonfigurować [Ustawienia platformy](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurowanie ustawień platformy

Ustawienia dla każdego typu aplikacji, w tym identyfikatory URI przekierowania, są konfigurowane w **konfiguracjach platformy** w Azure Portal. Niektóre platformy, takie jak aplikacje **sieci Web** i **jednostronicowe**, wymagają ręcznego określenia identyfikatora URI przekierowania. W przypadku innych platform, takich jak Mobile i Desktop, można wybierać spośród identyfikatorów URI przekierowania generowanych dla Ciebie podczas konfigurowania innych ustawień.

Aby skonfigurować ustawienia aplikacji na podstawie docelowej platformy lub urządzenia:

1. W Azure Portal, w **rejestracje aplikacji**, wybierz aplikację.
1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Konfiguracja platformy** wybierz pozycję **Dodaj platformę**.
1. W obszarze **Konfigurowanie platform** wybierz kafelek dla typu aplikacji (platformy), aby skonfigurować jego ustawienia.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Zrzut ekranu przedstawiający okienko Konfiguracja platformy w Azure Portal." border="false":::

    | Platforma | Ustawienia konfiguracji |
    | -------- | ---------------------- |
    | **Sieć Web** | Wprowadź **Identyfikator URI przekierowania** dla aplikacji. Ten identyfikator URI jest lokalizacją, w której platforma tożsamości firmy Microsoft przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.<br/><br/>Wybierz tę platformę dla standardowych aplikacji sieci Web, które są uruchomione na serwerze. |
    | **Aplikacja jednostronicowa** | Wprowadź **Identyfikator URI przekierowania** dla aplikacji. Ten identyfikator URI jest lokalizacją, w której platforma tożsamości firmy Microsoft przekierowuje klienta użytkownika i wysyła tokeny zabezpieczające po uwierzytelnieniu.<br/><br/>Wybierz tę platformę, jeśli tworzysz aplikację sieci Web po stronie klienta za pomocą języka JavaScript lub struktury, takiej jak kątowy, Vue.js, React.js lub Blazor webassembly. |
    | **iOS/macOS** | Wprowadź **Identyfikator pakietu** aplikacji. Znajdź je w **ustawieniach kompilacji** lub w Xcode w *info. plist*.<br/><br/>Podczas określania **identyfikatora pakietu** zostanie wygenerowany identyfikator URI przekierowania. |
    | **Android** | Wprowadź **nazwę pakietu** aplikacji. Znajdź ją w pliku *AndroidManifest.xml* . Wygeneruj również i wprowadź **skrót sygnatury**.<br/><br/>Po określeniu tych ustawień zostanie wygenerowany identyfikator URI przekierowania. |
    | **Aplikacje mobilne i klasyczne** | Wybierz jeden z **sugerowanych identyfikatorów URI przekierowania**. Lub Określ **niestandardowy identyfikator URI przekierowania**.<br/><br/>W przypadku aplikacji klasycznych przy użyciu osadzonej przeglądarki zalecamy<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>W przypadku aplikacji klasycznych przy użyciu przeglądarki systemowej zalecamy<br/>`http://localhost`<br/><br/>Wybierz tę platformę dla aplikacji mobilnych, które nie używają najnowszej biblioteki uwierzytelniania firmy Microsoft (MSAL) lub nie korzystasz z brokera. Należy również wybrać tę platformę dla aplikacji klasycznych. |
1. Wybierz pozycję **Konfiguruj** , aby zakończyć konfigurację platformy.

### <a name="redirect-uri-restrictions"></a>Ograniczenia identyfikatorów URI przekierowania

Istnieją pewne ograniczenia dotyczące formatu identyfikatorów URI przekierowania dodawanych do rejestracji aplikacji. Aby uzyskać szczegółowe informacje o tych ograniczeniach, zobacz [ograniczenia i ograniczenia dotyczące URI przekierowania (adres URL odpowiedzi)](reply-url.md).

## <a name="add-credentials"></a>Dodawanie poświadczeń

Poświadczenia są używane przez [poufne aplikacje klienckie](msal-client-applications.md) , które uzyskują dostęp do internetowego interfejsu API. Przykładami poufnych klientów są [aplikacje sieci Web](scenario-web-app-call-api-overview.md), inne [interfejsy API sieci Web](scenario-protected-web-api-overview.md)lub [aplikacje typu typ usługi i demon](scenario-daemon-overview.md). Poświadczenia umożliwiają aplikacji uwierzytelnianie jako same, co nie wymaga interakcji z użytkownikiem w czasie wykonywania. 

Do rejestracji poufnej aplikacji klienckiej można dodać zarówno certyfikaty, jak i klucze tajne klienta (ciąg) jako poświadczenia.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym wyświetlane są okienka certyfikaty i wpisy tajne w rejestracji aplikacji.":::

### <a name="add-a-certificate"></a>Dodawanie certyfikatu

Typ certyfikatu jest czasami nazywany *kluczem publicznym*. Zapewnia większą gwarancję niż klucz tajny klienta. Aby uzyskać więcej informacji na temat używania certyfikatu jako metody uwierzytelniania w aplikacji, zobacz [Microsoft Identity platform application Authentication Certificates](active-directory-certificate-credentials.md).

1. W Azure Portal, w **rejestracje aplikacji**, wybierz aplikację.
1. Wybierz pozycję **Certificates &** Secret  >  **upload Certificate**.
1. Wybierz plik, który chcesz przekazać. Musi to być jeden z następujących typów plików: *CER*, *PEM*, *CRT*.
1. Wybierz pozycję **Dodaj**.

### <a name="add-a-client-secret"></a>Dodawanie klucza tajnego klienta

Klucz tajny klienta jest również znany jako *hasło aplikacji*. Jest to wartość ciągu, która może być używana przez aplikację zamiast certyfikatu do samodzielnej tożsamości. Klucz tajny klienta to łatwiejszy z dwóch typów poświadczeń do użycia. Jest on często używany podczas opracowywania, ale jest traktowany jako mniej bezpieczny niż certyfikat. Używaj certyfikatów w aplikacjach, które działają w środowisku produkcyjnym. 

Aby uzyskać więcej informacji na temat zaleceń dotyczących zabezpieczeń aplikacji, zobacz [najlepsze rozwiązania i zalecenia dotyczące platformy tożsamości firmy Microsoft](identity-platform-integration-checklist.md#security).

1. W Azure Portal, w **rejestracje aplikacji**, wybierz aplikację.
1. Wybierz pozycję **Certyfikaty & klucz** tajny  >   **nowy klucz tajny klienta**.
1. Dodaj opis wpisu tajnego klienta.
1. Wybierz czas trwania.
1. Wybierz pozycję **Dodaj**.
1. *Zapisz wartość wpisu tajnego* do użycia w kodzie aplikacji klienta. Ta wartość klucza tajnego *nigdy nie jest ponownie wyświetlana* po opuszczeniu tej strony.


## <a name="next-steps"></a>Następne kroki

Aplikacje klienckie zazwyczaj muszą uzyskiwać dostęp do zasobów w internetowym interfejsie API. Aplikację kliencką można chronić przy użyciu platformy tożsamości firmy Microsoft. Możesz również użyć platformy do autoryzowania zakresu, dostęp oparty na uprawnieniach do internetowego interfejsu API.

Przejdź do następnego przewodnika Szybki Start w serii, aby utworzyć kolejną rejestrację aplikacji dla internetowego interfejsu API i uwidocznić jej zakresy.

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji w celu udostępnienia internetowego interfejsu API](quickstart-configure-app-expose-web-apis.md)
