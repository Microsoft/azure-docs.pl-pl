---
title: Włącz Azure AD B2C domen niestandardowych
titleSuffix: Azure AD B2C
description: Informacje o sposobie włączania domen niestandardowych w adresach URL przekierowania dla Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580168"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Włącz domeny niestandardowe dla Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

W tym artykule opisano sposób włączania domen niestandardowych w adresach URL przekierowania dla Azure Active Directory B2C (Azure AD B2C). Używanie domeny niestandardowej z aplikacją zapewnia bezproblemowe środowisko użytkownika. Z punktu widzenia użytkownika pozostają w domenie podczas procesu logowania, a nie przekierowywanie do Azure AD B2C domyślnej domeny *<dzierżawcy>. b2clogin.com*.

![Środowisko użytkownika domeny niestandardowej](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Przegląd domeny niestandardowej

Domeny niestandardowe można włączyć dla Azure AD B2C przy użyciu usług [Azure front-drzwi](https://azure.microsoft.com/services/frontdoor/). Drzwi frontonu platformy Azure to globalny, skalowalny punkt wejścia, który używa sieci Microsoft Global Edge do tworzenia szybkich, bezpiecznych i skalowalnych aplikacji sieci Web. Możesz renderować Azure AD B2C zawartość za drzwiami frontonu platformy Azure, a następnie skonfigurować opcję dla drzwi frontonu platformy Azure, aby dostarczyć zawartość za pośrednictwem domeny niestandardowej w adresie URL aplikacji.

Na poniższym diagramie przedstawiono integrację z usługą Azure front-drzwi:

1. W aplikacji użytkownik klika przycisk Zaloguj, co spowoduje przejście do strony logowania Azure AD B2C. Ta strona określa niestandardową nazwę domeny.
1. Przeglądarka sieci Web rozpoznaje niestandardową nazwę domeny w adresie IP usługi Azure front-drzwi. Podczas rozpoznawania nazw DNS rekord nazwy kanonicznej (CNAME) z niestandardową nazwą domeny wskazuje na domyślny Host frontonu (na przykład `contoso.azurefd.net` ). 
1. Ruch skierowany do domeny niestandardowej (na przykład `login.contoso.com` ) jest kierowany do określonego domyślnego hosta frontonu ( `contoso.azurefd.net` ).
1. Drzwi frontonu platformy Azure wywoła zawartość Azure AD B2C przy użyciu `<tenant-name>.b2clogin.com` domeny domyślnej Azure AD B2C. Żądanie do punktu końcowego Azure AD B2C zawiera niestandardowy nagłówek HTTP, który zawiera oryginalną niestandardową nazwę domeny.
1. Azure AD B2C odpowiada na żądanie, wyświetlając odpowiednią zawartość i oryginalną domenę niestandardową.

![Diagram sieci niestandardowych domen](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> Połączenie z przeglądarki do frontonu platformy Azure powinno zawsze korzystać z protokołu IPv4 zamiast IPv6.

W przypadku korzystania z domen niestandardowych należy wziąć pod uwagę następujące kwestie:

- Można skonfigurować wiele domen niestandardowych. Maksymalna liczba obsługiwanych domen niestandardowych znajduje się w temacie [limity i ograniczenia usługi Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md) dotyczące Azure AD B2C oraz [limitów subskrypcji i usług platformy Azure, przydziałów i ograniczeń](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) dotyczących drzwi platformy Azure.
- Drzwi frontonu platformy Azure to oddzielna usługa platformy Azure, dzięki czemu będą naliczane dodatkowe opłaty. Aby uzyskać więcej informacji, zobacz [Cennik przed drzwiami](https://azure.microsoft.com/pricing/details/frontdoor).
- Aby korzystać z [zapory aplikacji sieci Web](../web-application-firewall/afds/afds-overview.md)platformy Azure, należy potwierdzić, że konfiguracja zapory i reguły działają poprawnie z Azure AD B2C przepływów użytkownika.
- Po skonfigurowaniu domen niestandardowych użytkownicy nadal będą mogli uzyskiwać dostęp do Azure AD B2C domyślnej nazwy domeny *<dzierżawcy>. b2clogin.com* (chyba że używasz zasad niestandardowych i [blokujesz dostęp](#block-access-to-the-default-domain-name).
- Jeśli masz wiele aplikacji, Przeprowadź migrację ich wszystkich do domeny niestandardowej, ponieważ w przeglądarce jest przechowywana sesja Azure AD B2C w ramach aktualnie używanej nazwy domeny.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Dodaj niestandardową nazwę domeny do dzierżawy

Postępuj zgodnie ze wskazówkami dotyczącymi [dodawania i weryfikowania domeny niestandardowej w usłudze Azure AD](../active-directory/fundamentals/add-custom-domain.md). Po zweryfikowaniu domeny usuń utworzony rekord TXT DNS.

> [!IMPORTANT]
> Aby wykonać te kroki, należy zalogować się do dzierżawy usługi **Azure AD B2C** i wybrać usługę **Azure Active Directory** .

Sprawdź wszystkie poddomeny, których planujesz użyć. Weryfikowanie tylko domeny najwyższego poziomu jest niewystarczające. Na przykład aby można było zalogować się przy użyciu *login.contoso.com* i *account.contoso.com*, należy zweryfikować obie poddomeny, a nie tylko domenę najwyższego poziomu *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Utwórz nowe wystąpienie drzwi platformy Azure

Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia drzwi czołowych dla aplikacji](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) przy użyciu ustawień domyślnych dla hosta frontonu i reguł routingu. 

> [!IMPORTANT]
> Aby wykonać te kroki, po zalogowaniu się do Azure Portal w kroku 1 Wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający subskrypcję platformy Azure, której chcesz użyć dla drzwi frontonu platformy Azure. *Nie* powinna to być katalog zawierający dzierżawcę Azure AD B2C. 

W kroku **Dodawanie zaplecza** Użyj następujących ustawień:

* W obszarze **typ hosta zaplecza** wybierz pozycję **host niestandardowy**.  
* W polu **Nazwa hosta zaplecza** wybierz nazwę hosta dla punktu końcowego Azure AD B2C, <nazwę dzierżawy>. b2clogin.com. Na przykład contoso.b2clogin.com. 
* W polu **nagłówek hosta zaplecza** wybierz tę samą wartość wybraną dla **nazwy hosta zaplecza**.

![Dodawanie zaplecza](./media/custom-domain/add-a-backend.png)

Po dodaniu **zaplecza** do **puli zaplecza** należy wyłączyć **sondy kondycji**.

![Dodawanie puli zaplecza](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Skonfiguruj domenę niestandardową na platformie Azure front-drzwi

Postępuj zgodnie z instrukcjami, aby [dodać domenę niestandardową do swoich drzwi](../frontdoor/front-door-custom-domain.md). Podczas tworzenia `CNAME` rekordu dla domeny niestandardowej Użyj niestandardowej nazwy domeny, która została zweryfikowana wcześniej w kroku [Dodaj niestandardową nazwę domeny do usługi Azure AD](#add-a-custom-domain-name-to-your-tenant) . 

Po zweryfikowaniu nazwy domeny niestandardowej wybierz pozycję **niestandardowa nazwa domeny https**. Następnie w obszarze **Typ zarządzania certyfikatami** wybierz pozycję [Zarządzanie drzwiami przednimi](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)lub [Użyj własnego certyfikatu](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Poniższy zrzut ekranu pokazuje, jak dodać domenę niestandardową i włączyć protokół HTTPS przy użyciu certyfikatu usługi Azure front-drzwi.

![Konfigurowanie domeny niestandardowej drzwi platformy Azure](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS

W przypadku [dostosowywania interfejsu użytkownika Azure AD B2C](customize-ui-with-html.md) przy użyciu szablonu HTML należy [skonfigurować mechanizm CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) z domeną niestandardową.

Skonfiguruj usługę Azure Blob Storage na potrzeby udostępniania zasobów między źródłami, wykonując następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu.
1. Z menu wybierz pozycję **CORS**.
1. Dla **dozwolonych źródeł** wprowadź `https://your-domain-name` . Zamień `your-domain-name` na nazwę domeny. Na przykład `https://login.contoso.com`. W przypadku wprowadzania nazwy dzierżawy używaj wszystkich małych liter.
1. W przypadku **dozwolonych metod** zaznacz opcję oba `GET` i `OPTIONS` .
1. Dla **dozwolonych nagłówków** Wprowadź gwiazdkę (*).
1. W przypadku **widocznych nagłówków** Wprowadź gwiazdkę (*).
1. W obszarze **Maksymalny wiek** wprowadź 200.
1. Wybierz pozycję **Zapisz**.

## <a name="configure-your-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Gdy użytkownik zdecyduje się na zalogowanie się za pomocą dostawcy tożsamości społecznościowej, Azure AD B2C inicjuje żądanie autoryzacji i przybierze użytkownika do wybranego dostawcy tożsamości w celu ukończenia procesu logowania. Żądanie autoryzacji określa `redirect_uri` domyślną nazwę domeny Azure AD B2C: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Jeśli zasady zostały skonfigurowane w taki sposób, aby zezwalały na logowanie się z zewnętrznym dostawcą tożsamości, zaktualizuj identyfikatory URI przekierowań OAuth przy użyciu domeny niestandardowej. Większość dostawców tożsamości umożliwia rejestrowanie wielu identyfikatorów URI przekierowania. Zalecamy dodanie identyfikatorów URI przekierowania zamiast zastępowania ich, aby można było przetestować zasady niestandardowe bez wpływu na aplikacje, które używają domyślnej nazwy domeny Azure AD B2C. 

W poniższym identyfikatorze URI przekierowania:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Zastąp **<Custom-domain-name>nazwą** domeny niestandardowej.
- Zastąp **<nazwę dzierżawcy>** nazwą dzierżawy lub identyfikatorem dzierżawy.

Poniższy przykład pokazuje prawidłowy identyfikator URI przekierowania OAuth:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Jeśli zdecydujesz się na użycie [identyfikatora dzierżawy](#optional-use-tenant-id), prawidłowy identyfikator URI przekierowania OAuth będzie wyglądać następująco:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Metadane [dostawców tożsamości SAML](saml-identity-provider-technical-profile.md) będą wyglądać następująco:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Przetestuj domenę niestandardową

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij przycisk **Kopiuj do schowka**.

    ![Kopiowanie identyfikatora URI żądania autoryzacji](./media/custom-domain/user-flow-run-now.png)

1. W adresie URL **punktu końcowego przebiegu przepływu użytkownika** Zastąp domenę Azure AD B2Cową (<dzierżawy>. b2clogin.com) swoją domeną niestandardową.  
    Na przykład zamiast:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    używanych

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Wybierz pozycję **Uruchom przepływ użytkownika**. Zasady Azure AD B2C powinny zostać załadowane.
1. Zaloguj się przy użyciu kont lokalnych i społecznościowych.
1. Powtórz test z pozostałymi zasadami.

## <a name="configure-your-application"></a>Konfigurowanie aplikacji 

Po skonfigurowaniu i przetestowaniu domeny niestandardowej możesz zaktualizować swoje aplikacje w celu załadowania adresu URL, który określa domenę niestandardową jako nazwę hosta zamiast domeny Azure AD B2C. 

Integracja domen niestandardowych dotyczy punktów końcowych uwierzytelniania, które używają zasad Azure AD B2C (przepływy użytkownika lub zasady niestandardowe) do uwierzytelniania użytkowników. Te punkty końcowe mogą wyglądać następująco:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Zastąp:
- **domena** niestandardowa z domeną niestandardową
- **Nazwa** dzierżawy z nazwą dzierżawy lub identyfikatorem dzierżawy
- **Nazwa zasad** z nazwą zasad. [Dowiedz się więcej na temat zasad Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Metadane [dostawcy usługi SAML](./saml-service-provider.md) mogą wyglądać następująco: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Obowiązkowe Użyj identyfikatora dzierżawy

Nazwę dzierżawy B2C w adresie URL można zastąpić identyfikatorem GUID identyfikatora dzierżawy, aby usunąć wszystkie odwołania do "B2C" w adresie URL. Identyfikator GUID identyfikatora dzierżawy można znaleźć na stronie przeglądu B2C w Azure Portal.
Na przykład zmień `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` na `https://account.contosobank.co.uk/<tenant ID GUID>/`

Jeśli zdecydujesz się użyć identyfikatora dzierżawy zamiast nazwy dzierżawy, pamiętaj, aby odpowiednio zaktualizować **identyfikatory URI przekierowania uwierzytelniania OAuth** dostawcy tożsamości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dostawcy tożsamości](#configure-your-identity-provider).

### <a name="token-issuance"></a>Wystawianie tokenów

Nazwa wystawcy tokenu (ISS) zmienia się w zależności od używanej domeny niestandardowej. Na przykład:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Blokuj dostęp do domyślnej nazwy domeny

Po dodaniu domeny niestandardowej i skonfigurowaniu aplikacji użytkownicy nadal będą mogli uzyskać dostęp do domeny <dzierżawcy>. b2clogin.com. Aby uniemożliwić dostęp, można skonfigurować zasady, aby sprawdzać żądanie autoryzacji "Nazwa hosta" na liście dozwolonych domen. Nazwa hosta to nazwa domeny, która pojawia się w adresie URL. Nazwa hosta jest dostępna za pomocą `{Context:HostName}` [resolverów roszczeń](claim-resolver-overview.md). Następnie można przedstawić niestandardowy komunikat o błędzie. 

1. Zapoznaj się z przykładem zasad dostępu warunkowego, które sprawdzają nazwę hosta z usługi [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .
1. Przekaż pliki zasad w następującej kolejności: `B2C_1A_TrustFrameworkExtensions_HostName.xml` a następnie `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C zwraca błąd nieznalezionej strony

- **Objaw** — po skonfigurowaniu domeny niestandardowej podczas próby zalogowania się przy użyciu domeny niestandardowej zostanie wyświetlony komunikat o błędzie HTTP 404.
- **Możliwe przyczyny** — ten problem może być związany z konfiguracją DNS lub konfiguracją wewnętrznej bazy danych platformy Azure. 
- **Rozwiązanie**:  
    1. Upewnij się, że domena niestandardowa jest [zarejestrowana i pomyślnie zweryfikowana](#add-a-custom-domain-name-to-your-tenant) w dzierżawie Azure AD B2C.
    1. Upewnij się, że [domena niestandardowa](../frontdoor/front-door-custom-domain.md) jest prawidłowo skonfigurowana. `CNAME`Rekord domeny niestandardowej musi wskazywać na domyślnego hosta frontonu platformy Azure (na przykład contoso.azurefd.NET).
    1. Upewnij się, że [Konfiguracja puli zaplecza systemu Azure z przodu](#set-up-your-custom-domain-on-azure-front-door) jest wskazywana dzierżawcy, w której skonfigurowano niestandardową nazwę domeny, oraz miejsce przechowywania przepływu użytkownika lub zasad niestandardowych.

### <a name="identify-provider-returns-an-error"></a>Zidentyfikuj dostawcę zwraca błąd

- **Objaw** — po skonfigurowaniu domeny niestandardowej możesz zalogować się przy użyciu kont lokalnych. Jednak po zalogowaniu się przy użyciu poświadczeń z zewnętrznych [dostawców tożsamości społecznościowych lub firmowych](add-identity-provider.md)dostawcy tożsamości przedstawiają komunikat o błędzie.
- **Możliwe przyczyny** — Jeśli Azure AD B2C użytkownik może zalogować się przy użyciu federacyjnego dostawcy tożsamości, określa identyfikator URI przekierowania. Identyfikator URI przekierowania jest punktem końcowym, do którego dostawca tożsamości zwraca token. Identyfikator URI przekierowania jest tą samą domeną używaną przez aplikację z żądaniem autoryzacji. Jeśli identyfikator URI przekierowania nie został jeszcze zarejestrowany w dostawcy tożsamości, może nie ufać nowemu identyfikatorowi URI przekierowania, który powoduje komunikat o błędzie. 
- **Rozwiązanie** — wykonaj kroki opisane w sekcji [Konfigurowanie dostawcy tożsamości](#configure-your-identity-provider) w celu dodania nowego identyfikatora URI przekierowania. 


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Czy mogę użyć zaawansowanej konfiguracji usługi Azure Front drzwiczk, takiej jak *reguły zapory aplikacji sieci Web*? 
  
Zaawansowane ustawienia konfiguracji usług Azure Front drzwiczk nie są oficjalnie obsługiwane, ale można ich używać na własne ryzyko. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Po użyciu Uruchom teraz, aby spróbować uruchomić moje zasady, dlaczego nie widzę domeny niestandardowej?

Skopiuj adres URL, Zmień nazwę domeny ręcznie, a następnie wklej ją z powrotem do przeglądarki.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Który adres IP jest prezentowany Azure AD B2C? Adres IP użytkownika lub adres IP usługi Azure front-drzwi?

Drzwi frontonu platformy Azure przechodzą oryginalny adres IP użytkownika. Jest to adres IP, który będzie widoczny w raportach inspekcji lub w zasadach niestandardowych.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Czy mogę użyć zapory aplikacji sieci Web innej firmy (WAF) z B2C?

Aby korzystać z własnej zapory aplikacji sieci Web przed drzwiami platformy Azure, musisz skonfigurować i sprawdzić, czy wszystko działa prawidłowo w ramach przepływów pracy użytkownika Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Informacje o [żądaniach autoryzacji OAuth](protocols-overview.md).