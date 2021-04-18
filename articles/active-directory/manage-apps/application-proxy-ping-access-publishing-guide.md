---
title: Uwierzytelnianie oparte na nagłówku za pomocą usługi PingAccess dla usługi Azure AD serwer proxy aplikacji
description: Publikowanie aplikacji przy użyciu funkcji PingAccess i serwera proxy aplikacji w celu obsługi uwierzytelniania opartego na nagłówku.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600084"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Logowanie jednokrotne z uwierzytelnianiem na podstawie nagłówka dzięki funkcji serwera proxy aplikacji i narzędziu PingAccess

Azure Active Directory (Azure AD) serwer proxy aplikacji z usługą PingAccess, dzięki czemu klienci usługi Azure AD mogą uzyskać dostęp do większej liczby twoich aplikacji. PingAccess to kolejna opcja poza zintegrowanym logowaniem pojedynczym opartym [na nagłówku.](application-proxy-configure-single-sign-on-with-headers.md)

## <a name="whats-pingaccess-for-azure-ad"></a>Co to jest usługa PingAccess dla usługi Azure AD?

Usługa PingAccess dla usługi Azure AD umożliwia użytkownikom dostęp i logowanie jednokrotne do aplikacji, które używają nagłówków do uwierzytelniania. serwer proxy aplikacji traktuje te aplikacje jak inne, używając usługi Azure AD do uwierzytelniania dostępu, a następnie przekazywania ruchu przez usługę łącznika. Usługa PingAccess znajduje się przed aplikacjami i tłumaczy token dostępu z usługi Azure AD na nagłówek. Następnie aplikacja otrzymuje uwierzytelnianie w formacie, który może odczytać.

Użytkownicy nie zauważą niczego innego, gdy logują się w celu korzystania z aplikacji firmowych. Nadal mogą pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwer proxy aplikacji kierują ruch zdalny do wszystkich aplikacji bez względu na ich typ uwierzytelniania, dzięki czemu nadal będą równoważyć obciążenia automatycznie.

## <a name="how-do-i-get-access"></a>Jak mogę uzyskać dostęp?

Ponieważ ten scenariusz pochodzi ze partnerstwa między usługami Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak Azure Active Directory — wersja Premium obejmują podstawową licencję usługi PingAccess, która obejmuje maksymalnie 20 aplikacji. Jeśli musisz opublikować więcej niż 20 aplikacji opartych na nagłówkach, możesz kupić dodatkową licencję z usługi PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikowanie aplikacji na platformie Azure

Ten artykuł jest dla osób, które po raz pierwszy publikują aplikację z tym scenariuszem. Oprócz szczegółów kroków publikowania opisano w nim wprowadzenie do funkcji serwer proxy aplikacji i PingAccess. Jeśli obie usługi zostały już skonfigurowane, ale chcesz odświeżyć kroki publikowania, przejdź do sekcji Dodawanie aplikacji do usługi [Azure AD](#add-your-application-to-azure-ad-with-application-proxy) za pomocą serwer proxy aplikacji publikowania.

> [!NOTE]
> Ponieważ ten scenariusz jest partnerstwom między usługą Azure AD i usługą PingAccess, niektóre instrukcje znajdują się w witrynie tożsamości ping.

### <a name="install-an-application-proxy-connector"></a>Instalowanie łącznika serwer proxy aplikacji łącznika

Jeśli włączono już serwer proxy aplikacji łącznik, możesz pominąć tę sekcję i przejść do sekcji Dodawanie aplikacji do usługi [Azure AD](#add-your-application-to-azure-ad-with-application-proxy)za pomocą serwer proxy aplikacji .

Łącznik serwer proxy aplikacji to usługa systemu Windows Server, która kieruje ruch od pracowników zdalnych do opublikowanych aplikacji. Aby uzyskać bardziej szczegółowe instrukcje instalacji, zobacz [Samouczek: dodawanie](application-proxy-add-on-premises-application.md)aplikacji lokalnej na temat dostępu zdalnego za pośrednictwem serwer proxy aplikacji w Azure Active Directory .

1. Zaloguj się do [portalu Azure Active Directory jako](https://aad.portal.azure.com/) administrator aplikacji. Zostanie **Azure Active Directory centrum administracyjnego.**
1. Wybierz **Azure Active Directory**  >  **serwer proxy aplikacji** Pobierz  >  **usługę łącznika**. Zostanie **serwer proxy aplikacji Pobieranie łącznika** aplikacji.

   ![Pobieranie łącznika serwera proxy aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postępuj zgodnie z instrukcjami instalacji.

Pobranie łącznika powinno automatycznie serwer proxy aplikacji dla katalogu, ale jeśli tak nie jest, możesz wybrać pozycję **Włącz** serwer proxy aplikacji .

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Dodawanie aplikacji do usługi Azure AD za pomocą serwer proxy aplikacji

Istnieją dwie akcje, które należy podjąć w Azure Portal. Najpierw musisz opublikować aplikację za pomocą serwer proxy aplikacji. Następnie należy zebrać pewne informacje o aplikacji, których można użyć podczas kroków funkcji PingAccess.

#### <a name="publish-your-application"></a>Publikowanie aplikacji

Najpierw musisz opublikować aplikację. Ta akcja obejmuje:

- Dodawanie aplikacji lokalnej do usługi Azure AD
- Przypisywanie użytkownika do testowania aplikacji i wybieranie logowania jednokrotnego opartego na nagłówku
- Konfigurowanie adresu URL przekierowania aplikacji
- Udzielanie użytkownikom i innym aplikacjom uprawnień do korzystania z aplikacji lokalnej

Aby opublikować własną aplikację lokalną:

1. Jeśli nie po raz pierwszy w ostatniej sekcji, zaloguj się do portalu [Azure Active Directory jako](https://aad.portal.azure.com/) administrator aplikacji.
1. Wybierz **pozycję Aplikacje dla przedsiębiorstw** Nowa  >  **aplikacja** Dodaj aplikację  >  **lokalną.** Zostanie **wyświetlona strona Dodaj własną aplikację lokalną.**

   ![Dodawanie własnej aplikacji lokalnej](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Wypełnij wymagane pola informacjami o nowej aplikacji. Użyj poniższych wskazówek dotyczących ustawień.

   > [!NOTE]
   > Aby uzyskać bardziej szczegółowy przewodnik po tym kroku, zobacz Dodawanie aplikacji lokalnej [do usługi Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **Wewnętrzny adres URL:** zwykle podano adres URL, który umożliwia dostęp do strony logowania aplikacji w sieci firmowej. W tym scenariuszu łącznik musi traktować serwer proxy PingAccess jako stronę główną aplikacji. Użyj tego formatu: `https://<host name of your PingAccess server>:<port>` . Domyślny port to 3000, ale można go skonfigurować w funkcji PingAccess.

      > [!WARNING]
      > W przypadku tego typu logowania pojedynczego wewnętrzny adres URL musi używać adresu i `https` nie może używać adresu `http` . Ponadto podczas konfigurowania aplikacji istnieje ograniczenie, że żadna z dwóch aplikacji nie powinna mieć tego samego wewnętrznego adresu URL, ponieważ pozwala to serwerowi proxy aplikacji zachować rozróżnienie między aplikacjami.

   1. **Metoda uwierzytelniania wstępnego:** wybierz **Azure Active Directory**.
   1. **Przetłumacz adres URL w nagłówkach:** wybierz **pozycję Nie.**

   > [!NOTE]
   > Jeśli jest to Twoja pierwsza aplikacja, użyj portu 3000, aby rozpocząć i wrócić, aby zaktualizować to ustawienie w przypadku zmiany konfiguracji usługi PingAccess. W przypadku kolejnych aplikacji port musi być zgodne z odbiornikiem skonfigurowanym w funkcji PingAccess. Dowiedz się więcej o [odbiornikach w funkcji PingAccess.](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)

1. Wybierz pozycję **Dodaj**. Zostanie wyświetlona strona przeglądu nowej aplikacji.

Teraz przypisz użytkownika do testowania aplikacji i wybierz logowanie jednorazowe oparte na nagłówku:

1. Na pasku bocznym aplikacji wybierz pozycję **Użytkownicy i grupy** Dodaj  >  **użytkowników** i  >  **grupy \<Number> (wybrane).** Zostanie wyświetlona lista użytkowników i grup do wyboru.

   ![Przedstawia listę użytkowników i grup](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Wybierz użytkownika do testowania aplikacji, a następnie wybierz **pozycję Wybierz**. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej.
1. Wybierz opcję **Przypisz**.
1. Na pasku bocznym aplikacji wybierz **pozycję Nagłówek logowania pojedynczego.**  >  

   > [!TIP]
   > Jeśli po raz pierwszy używasz logowania pojedynczego opartego na nagłówku, musisz zainstalować usługę PingAccess. Aby upewnić się, że subskrypcja platformy Azure jest automatycznie skojarzona z instalacją funkcji PingAccess, użyj linku na tej stronie logowania pojedynczego, aby pobrać usługę PingAccess. Możesz teraz otworzyć witrynę pobierania lub wrócić do tej strony później.

   ![Wyświetla ekran logowania oparty na nagłówku i dostęp PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Wybierz pozycję **Zapisz**.

Następnie upewnij się, że adres URL przekierowania jest ustawiony na zewnętrzny adres URL:

1. Na pasku **bocznym Azure Active Directory administracyjnym** wybierz pozycję **Azure Active Directory**  >  **Rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Wybierz link obok opcji **Przekieruj interfejsy URI,** pokazujący liczbę przekierowanych interfejsów URI, które są ustawione dla klientów internetowych i publicznych. Zostanie **\<application name> wyświetlona strona —** Uwierzytelnianie.
1. Sprawdź, czy zewnętrzny adres URL, który został wcześniej przypisany do aplikacji, znajduje się na liście **URIs przekierowania.** Jeśli tak nie jest, dodaj teraz zewnętrzny adres URL, używając typu przekierowania URI **Internet,** a następnie wybierz pozycję **Zapisz**.

Oprócz zewnętrznego adresu URL do listy URI przekierowania należy dodać punkt końcowy autoryzacji Azure Active Directory na zewnętrznym adresie URL.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Na koniec skonfiguruj aplikację lokalną, aby użytkownicy mieli dostęp do odczytu, a inne aplikacje — dostęp do odczytu/zapisu:

1. Na pasku **Rejestracje aplikacji** aplikacji wybierz pozycję Uprawnienia interfejsu API Dodaj uprawnienie Interfejsy **API**  >    >  **firmy Microsoft**  >  **Microsoft Graph**. Zostanie **wyświetlona strona** Żądanie uprawnień **interfejsu** API dla Microsoft Graph zawierająca interfejsy API dla systemu Windows Azure Active Directory.

   ![Wyświetla stronę Żądanie uprawnień interfejsu API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Wybierz **pozycję Delegowane uprawnienia**  >    >  **Użytkownik.Odczyt**.
1. Wybierz **pozycję Uprawnienia aplikacji**  >  **Application**  >  **Application.ReadWrite.All.**
1. Wybierz **pozycję Dodaj uprawnienia.**
1. Na stronie **Uprawnienia interfejsu API** wybierz pozycję **Przyznaj zgodę administratora dla pozycji \<your directory name>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Zbieranie informacji dotyczących kroków funkcji PingAccess

Musisz zebrać te trzy informacje (wszystkie identyfikatory GUID), aby skonfigurować aplikację przy użyciu funkcji PingAccess:

| Nazwa pola usługi Azure AD | Nazwa pola PingAccess | Format danych |
| --- | --- | --- |
| **Identyfikator aplikacji (klienta)** | **Identyfikator klienta** | GUID |
| **Identyfikator katalogu (dzierżawcy)** | **Wystawca** | GUID |
| `PingAccess key` | **Klucz tajny klienta** | Ciąg losowy |

Aby zebrać te informacje:

1. Na pasku **bocznym Azure Active Directory administracyjnym** wybierz pozycję **Azure Active Directory**  >  **Rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację. Zostanie **Rejestracje aplikacji** strona aplikacji.

   ![Przegląd rejestracji aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Obok wartości **Identyfikator aplikacji (klienta)** wybierz ikonę Kopiuj do **schowka,** a następnie skopiuj ją i zapisz. Tę wartość określisz później jako identyfikator klienta usługi PingAccess.
1. Następnie w wartości Identyfikator katalogu **(dzierżawy)** wybierz również pozycję Kopiuj do **schowka,** a następnie skopiuj i zapisz. Tę wartość określisz później jako wystawcę funkcji PingAccess.
1. Na pasku bocznym **Rejestracje aplikacji** aplikacji wybierz pozycję Certyfikaty i wpisy **tajne** Nowy  >  **klucz tajny klienta.** Zostanie **wyświetlona strona Dodaj klucz tajny** klienta.

   ![Wyświetla stronę Dodawanie tajnego klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. W **polu Opis** wpisz `PingAccess key` .
1. W **obszarze Wygasa** wybierz sposób ustawienia klucza PingAccess: Za **1** rok, Za **2 lata** lub **Nigdy.**
1. Wybierz pozycję **Dodaj**. Klucz PingAccess pojawia się w tabeli wpisów tajnych klienta z losowym ciągiem, który jest automatycznie wypełniany w **polu VALUE.**
1. Obok pola VALUE klucza  PingAccess wybierz ikonę Kopiuj do **schowka,** a następnie skopiuj i zapisz go. Tę wartość określisz później jako klucz tajny klienta usługi PingAccess.

**Zaktualizuj `acceptMappedClaims` pole:**

1. Zaloguj się do [portalu Azure Active Directory jako](https://aad.portal.azure.com/) administrator aplikacji.
1. Wybierz **Azure Active Directory**  >  **Rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Na pasku bocznym **Rejestracje aplikacji** aplikacji wybierz pozycję **Manifest**. Zostanie wyświetlony kod JSON manifestu dla rejestracji aplikacji.
1. Wyszukaj pole `acceptMappedClaims` i zmień wartość na `True` .
1. Wybierz pozycję **Zapisz**.

### <a name="use-of-optional-claims-optional"></a>Korzystanie z opcjonalnych oświadczeń (opcjonalnie)

Oświadczenia opcjonalne umożliwiają dodawanie oświadczeń standardowych, ale nie uwzględnianych domyślnie, które mają wszyscy użytkownik i dzierżawca. Możesz skonfigurować opcjonalne oświadczenia dla aplikacji, modyfikując manifest aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł Understanding the Azure AD application manifest (Opis manifestu aplikacji usługi Azure AD)](../develop/reference-app-manifest.md)

Przykład dołączania adresu e-mail do access_token, który będzie zużywany przez usługę PingAccess:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Korzystanie z zasad mapowania oświadczeń (opcjonalnie)

[Zasady mapowania oświadczeń (wersja zapoznawcza)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) dla atrybutów, które nie istnieją w usłudze AzureAD. Mapowanie oświadczeń umożliwia migrowanie starych aplikacji internetowych do chmury przez dodanie dodatkowych oświadczeń niestandardowych, które są oparte na usługach AD FS lub obiektach użytkowników

Aby aplikacja korzystała z oświadczenia niestandardowego i zawierała dodatkowe pola, upewnij się, że utworzono również zasady mapowania oświadczeń niestandardowych i przypisano je [do aplikacji](../develop/active-directory-claims-mapping.md).

> [!NOTE]
> Aby można było użyć oświadczenia niestandardowego, należy także zdefiniować zasady niestandardowe i przypisać je do aplikacji. Te zasady powinny zawierać wszystkie wymagane atrybuty niestandardowe.
>
> Definicję i przypisanie zasad można wykonać za pomocą programu PowerShell lub Microsoft Graph. Jeśli je robisz w programie PowerShell, może być konieczne najpierw użycie polecenia , a następnie przypisanie go `New-AzureADPolicy` do aplikacji za pomocą polecenia `Add-AzureADServicePrincipalPolicy` . Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad mapowania oświadczeń](../develop/active-directory-claims-mapping.md).

Przykład:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Włączanie funkcji PingAccess w celu używania oświadczeń niestandardowych

Włączenie funkcji PingAccess do używania oświadczeń niestandardowych jest opcjonalne, ale wymagane, jeśli oczekujesz, że aplikacja będzie korzystać z dodatkowych oświadczeń.

Podczas konfigurowania usługi PingAccess w następnym kroku utworzysz sesję sieci Web (Settings->Access-> Web Sessions)  musi mieć wybrany profil żądania i odśwież atrybuty użytkownika ustawione na **Nie**

## <a name="download-pingaccess-and-configure-your-application"></a>Pobieranie funkcji PingAccess i konfigurowanie aplikacji

Po ukończeniu wszystkich kroków konfiguracji Azure Active Directory można przejść do konfigurowania funkcji PingAccess.

Szczegółowe kroki dotyczące części tego scenariusza dotyczącej funkcji PingAccess są nadal dostępne w dokumentacji tożsamości ping. Postępuj zgodnie z instrukcjami w tesłudze Configure [PingAccess for Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) to protect applications published using Microsoft Azure AD serwer proxy aplikacji on the Ping Identity web site (Konfigurowanie funkcji PingAccess dla usługi Azure AD w celu ochrony aplikacji opublikowanych przy użyciu usługi Microsoft Azure AD serwer proxy aplikacji w witrynie internetowej usługi Ping Identity i pobierz najnowszą wersję [funkcji PingAccess.](https://www.pingidentity.com/en/lp/azure-download.html?)

Te kroki ułatwiają zainstalowanie funkcji PingAccess i skonfigurowanie konta pingaccess (jeśli jeszcze go nie masz). Następnie, aby utworzyć połączenie usługi Azure AD OpenID Connect (OIDC), należy skonfigurować dostawcę tokenów z wartością identyfikatora katalogu **(dzierżawy)** skopiowaną z portalu usługi Azure AD. Następnie, aby utworzyć sesję sieci Web dla funkcji PingAccess, użyj identyfikatora **aplikacji (klienta) i** `PingAccess key` wartości. Następnie możesz skonfigurować mapowanie tożsamości i utworzyć wirtualnego hosta, lokację i aplikację.

### <a name="test-your-application"></a>Testowanie aplikacji

Po ukończeniu wszystkich tych kroków aplikacja powinna być uruchomiona. Aby ją przetestować, otwórz przeglądarkę i przejdź do zewnętrznego adresu URL utworzonego podczas publikacji aplikacji na platformie Azure. Zaloguj się przy użyciu konta testowego przypisanego do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie funkcji PingAccess dla usługi Azure AD w celu ochrony aplikacji publikowanych przy użyciu Microsoft Azure AD serwer proxy aplikacji](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach](application-proxy-troubleshoot.md)