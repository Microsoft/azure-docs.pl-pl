---
title: Aplikacje wieloznaczne w serwerze proxy aplikacji usługi Azure AD
description: Dowiedz się, jak korzystać z aplikacji wieloznacznych w serwerze proxy aplikacji usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e17cc4bf6f70901e3bb5d250f0a55f073ad8d99e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406728"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikacje wieloznaczne w serwerze proxy aplikacji usługi Azure Active Directory

W usłudze Azure Active Directory (Azure AD) konfigurowanie dużej liczby aplikacji lokalnych może szybko stać się nie do opanowania i wprowadza niepotrzebne ryzyko błędów konfiguracji, jeśli wiele z nich wymaga tych samych ustawień. Za [pomocą usługi Azure AD Application Proxy](application-proxy.md)można rozwiązać ten problem za pomocą publikowania symboli wieloznacznych aplikacji do publikowania wielu aplikacji naraz i zarządzania nimi. Jest to rozwiązanie, które pozwala na:

- Uproszczenie obciążenia administracyjnego
- Zmniejszenie liczby potencjalnych błędów konfiguracji
- Umożliwienie użytkownikom bezpiecznego dostępu do większej liczby zasobów

Ten artykuł zawiera informacje potrzebne do skonfigurowania publikowania aplikacji z symbolami wieloznacznych w twoim środowisku.

## <a name="create-a-wildcard-application"></a>Tworzenie aplikacji wieloznacznych

Można utworzyć aplikację z symbolami wieloznacznych (*), jeśli masz grupę aplikacji o tej samej konfiguracji. Potencjalnymi kandydatami do aplikacji z symbolami wieloznaczowymi są aplikacje z następującymi ustawieniami:

- Grupa użytkowników mających do nich dostęp
- Metoda SSO
- Protokół dostępu (http, https)

Aplikacje z symbolami wieloznaczymi można publikować, jeśli zarówno wewnętrzne, jak i zewnętrzne adresy URL są w następującym formacie:

> http(s)://*. \<domena\>

Na przykład: `http(s)://*.adventure-works.com`.

Wewnętrzne i zewnętrzne adresy URL mogą korzystać z różnych domen, ale powinny być takie same. Podczas publikowania aplikacji pojawia się błąd, jeśli jeden z adresów URL nie ma symbolu wieloznacznego.

Jeśli masz dodatkowe aplikacje z różnymi ustawieniami konfiguracji, należy opublikować te wyjątki jako oddzielne aplikacje, aby zastąpić domyślne ustawienia dla symbolu wieloznacznego. Aplikacje bez symbolu wieloznacznego zawsze mają pierwszeństwo przed aplikacjami wieloznacznym. Z punktu widzenia konfiguracji są to "tylko" zwykłe aplikacje.

Tworzenie aplikacji wieloznacznych opiera się na tym samym [przepływie publikowania aplikacji,](application-proxy-add-on-premises-application.md) który jest dostępny dla wszystkich innych aplikacji. Jedyną różnicą jest to, że należy dołączyć symbol wieloznaczny w adresach URL i potencjalnie konfiguracji jedno i zalogowania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, upewnij się, że spełniasz te wymagania.

### <a name="custom-domains"></a>Domeny niestandardowe

[Domeny niestandardowe](application-proxy-configure-custom-domain.md) są opcjonalne dla wszystkich innych aplikacji, ale są warunkiem wstępnym dla aplikacji wieloznacznych. Tworzenie domen niestandardowych wymaga:

1. Utwórz zweryfikowaną domenę na platformie Azure.
1. Przekaż certyfikat TLS/SSL w formacie PFX do serwera proxy aplikacji.

Należy rozważyć użycie certyfikatu symboli wieloznacznych, aby dopasować aplikację, którą zamierzasz utworzyć. Alternatywnie można również użyć certyfikatu, który zawiera tylko określone aplikacje. W takim przypadku tylko aplikacje wymienione w certyfikacie będą dostępne za pośrednictwem tej aplikacji wieloznacznego.

Ze względów bezpieczeństwa jest to trudne wymaganie i nie będziemy obsługiwać symboli wieloznacznych dla aplikacji, które nie mogą używać domeny niestandardowej dla zewnętrznego adresu URL.

### <a name="dns-updates"></a>Aktualizacje DNS

Podczas korzystania z domen niestandardowych należy utworzyć wpis DNS z rekordem CNAME `*.adventure-works.com`dla zewnętrznego adresu URL (na przykład ) wskazujący zewnętrzny adres URL punktu końcowego serwera proxy aplikacji. W przypadku aplikacji wieloznacznych rekord CNAME musi wskazywać odpowiednie zewnętrzne adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Aby potwierdzić, że cname został poprawnie skonfigurowany, można użyć [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) w jednym `expenses.adventure-works.com`z docelowych punktów końcowych, na przykład .  Twoja odpowiedź powinna zawierać wspomniany`<yourAADTenantId>.tenant.runtime.msappproxy.net`już alias ( ).

## <a name="considerations"></a>Zagadnienia do rozważenia

Oto kilka kwestii, które należy wziąć pod uwagę w przypadku aplikacji wieloznacznych.

### <a name="accepted-formats"></a>Akceptowane formaty

W przypadku aplikacji wieloznacznych wewnętrzny adres `http(s)://*.<domain>` **URL** musi być sformatowany jako .

![W przypadku wewnętrznego adresu URL użyj formatu http(s)://*. \<> domen](./media/application-proxy-wildcard/22.png)

Podczas konfigurowania **zewnętrznego adresu URL**należy użyć następującego formatu:`https://*.<custom domain>`

![W przypadku zewnętrznego adresu URL użyj formatu https://*. \<> domeny niestandardowej](./media/application-proxy-wildcard/21.png)

Inne pozycje symboli wieloznacznych, wiele symboli wieloznacznych lub innych ciągów odzwania nie są obsługiwane i powodują błędy.

### <a name="excluding-applications-from-the-wildcard"></a>Wykluczanie aplikacji z symbolu wieloznacznego

Aplikację można wykluczyć z aplikacji z symbolami wieloznacznych,

- Publikowanie aplikacji wyjątku jako zwykłej aplikacji
- Włączanie symbolu wieloznacznego tylko dla określonych aplikacji za pomocą ustawień DNS

Publikowanie aplikacji jako zwykłej aplikacji jest preferowaną metodą wykluczania aplikacji z symbolu wieloznacznego. Należy opublikować wykluczone aplikacje przed aplikacjami wieloznacznymi, aby upewnić się, że wyjątki są wymuszane od początku. Najbardziej specyficzna aplikacja zawsze będzie mieć `budgets.finance.adventure-works.com` pierwszeństwo – aplikacja `*.finance.adventure-works.com`opublikowana jako ma pierwszeństwo `*.adventure-works.com`przed aplikacją, co z kolei ma pierwszeństwo przed aplikacją.

Można również ograniczyć symbol wieloznaczny do pracy tylko dla określonych aplikacji za pośrednictwem zarządzania dns. Najlepszym rozwiązaniem jest utworzenie wpisu CNAME zawierającego symbol wieloznaczny i odpowiadającego formatowi skonfigurowanego zewnętrznego adresu URL. Można jednak zamiast tego wskazać określone adresy URL aplikacji na symbole wieloznaczne. Na przykład zamiast `*.adventure-works.com`, `hr.adventure-works.com`punkt `expenses.adventure-works.com` `travel.adventure-works.com individually` i `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`do .

Jeśli używasz tej opcji, potrzebujesz również innego wpisu CNAME dla wartości `AppId.domain`, na przykład, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`również wskazując tę samą lokalizację. **Identyfikator AppId** można znaleźć na stronie właściwości aplikacji aplikacji wieloznacznej:

![Znajdź identyfikator aplikacji na stronie właściwości aplikacji](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ustawianie adresu URL strony głównej w panelu MyApps

Aplikacja wieloznaczna jest reprezentowana tylko z jednym kafelkiem w [panelu MyApps](https://myapps.microsoft.com). Domyślnie ten kafelek jest ukryty. Aby wyświetlić kafelek i mieć użytkowników wylądować na określonej stronie:

1. Postępuj zgodnie ze wskazówkami dotyczącymi [ustawiania adresu URL strony głównej](application-proxy-configure-custom-home-page.md).
1. Ustaw **pokaż aplikację** na **true** na stronie właściwości aplikacji.

### <a name="kerberos-constrained-delegation"></a>Delegowanie z ograniczeniami protokołu Kerberos

W przypadku aplikacji korzystających z [delegowania z ograniczeniami kerberos (KCD) jako metody SSO](application-proxy-configure-single-sign-on-with-kcd.md)nazwa SPN wymieniona dla metody SSO może również wymagać symbolu wieloznacznego. Na przykład nazwa SPN `HTTP/*.adventure-works.com`może być: . Nadal musisz mieć skonfigurowane indywidualne nazwy SPN na serwerach `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`wewnętrznej bazy danych (na przykład ).

## <a name="scenario-1-general-wildcard-application"></a>Scenariusz 1: Ogólne symbole wieloznaczne

W tym scenariuszu masz trzy różne aplikacje, które chcesz opublikować:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Wszystkie trzy aplikacje:

- Są używane przez wszystkich użytkowników
- Korzystanie ze *zintegrowanego uwierzytelniania systemu Windows*
- Mają te same właściwości

Aplikację wieloznacznych można opublikować, wykonując kroki opisane w [obszarze Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). W tym scenariuszu przyjęto:

- Dzierżawca o następującym identyfikatorze:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Skonfigurowano zweryfikowaną `adventure-works.com` domenę o nazwie.
- Utworzono wpis **CNAME,** który `*.adventure-works.com` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` wskazuje.

Wykonując [opisane kroki,](application-proxy-add-on-premises-application.md)należy utworzyć nową aplikację proxy aplikacji w dzierżawie. W tym przykładzie symbol wieloznaczny znajduje się w następujących polach:

- Wewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w wewnętrznym adresie URL](./media/application-proxy-wildcard/42.png)

- Zewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w zewnętrznym adresie URL](./media/application-proxy-wildcard/43.png)

- Aplikacja wewnętrzna SPN:

    ![Przykład: symbol wieloznaczny w konfiguracji SPN](./media/application-proxy-wildcard/44.png)

Publikując aplikację z symbolami wieloznaczymi, można teraz uzyskać dostęp do trzech aplikacji, `travel.adventure-works.com`przechodząc do adresów URL, do których jesteś przyzwyczajony (na przykład ).

Konfiguracja implementuje następującą strukturę:

![Pokazuje strukturę zaimplementowana przez przykładową konfigurację](./media/application-proxy-wildcard/05.png)

| Kolor | Opis |
| ---   | ---         |
| Blue  | Aplikacje jawnie opublikowane i widoczne w witrynie Azure portal. |
| Szary  | Aplikacje, które można udostępnić za pośrednictwem aplikacji nadrzędnej. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenariusz 2: Ogólne symbole wieloznaczne z wyjątkiem

W tym scenariuszu masz oprócz trzech ogólnych aplikacji `finance.adventure-works.com`innej aplikacji, która powinna być dostępna tylko przez dział finanse. Przy bieżącej strukturze aplikacji aplikacja finansowa będzie dostępna za pośrednictwem aplikacji wieloznacznych i przez wszystkich pracowników. Aby to zmienić, należy wykluczyć aplikację z symbolu wieloznacznego, konfigurując program Finance jako osobną aplikację z bardziej restrykcyjnymi uprawnieniami.

Należy upewnić się, że istnieją rekordy `finance.adventure-works.com` CNAME, który wskazuje punkt końcowy określonej aplikacji, określone na serwerze proxy aplikacji strony dla aplikacji. W tym `finance.adventure-works.com` scenariuszu `https://finance-awcycles.msappproxy.net/`wskazuje na .

Wykonując [opisane kroki,](application-proxy-add-on-premises-application.md)ten scenariusz wymaga następujących ustawień:

- W **wewnętrznym adresie URL** **ustawisz finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw finanse zamiast symbolu wieloznacznego w wewnętrznym adresie URL](./media/application-proxy-wildcard/52.png)

- W **zewnętrznym adresie URL**można ustawić **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustawianie finansowania zamiast symbolu wieloznacznego w zewnętrznym adresie URL](./media/application-proxy-wildcard/53.png)

- Nazwa SPN aplikacji wewnętrznej **jest** ustawiana zamiast symbolu wieloznacznego.

    ![Przykład: Ustawianie finansowania zamiast symbolu wieloznacznego w konfiguracji nazwy SPN](./media/application-proxy-wildcard/54.png)

Ta konfiguracja implementuje następujący scenariusz:

![Pokazuje konfigurację zaimplementowana przez przykładowy scenariusz](./media/application-proxy-wildcard/09.png)

Ponieważ `finance.adventure-works.com` jest bardziej specyficzny `*.adventure-works.com`adres URL niż , ma pierwszeństwo. Użytkownicy nawigujący, aby `finance.adventure-works.com` mieć środowisko określone w aplikacji Zasoby finansowe. W takim przypadku tylko pracownicy finansowi `finance.adventure-works.com`mają dostęp do pliku .

Jeśli masz wiele aplikacji opublikowanych dla `finance.adventure-works.com` finansów i masz jako zweryfikowaną domenę, możesz opublikować inną aplikację `*.finance.adventure-works.com`z symbolami wieloznacznymi . Ponieważ jest to bardziej `*.adventure-works.com`szczegółowe niż rodzajowy , ma pierwszeństwo, jeśli użytkownik uzyskuje dostęp do aplikacji w domenie finansowej.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o **domenach niestandardowych,** zobacz [Praca z domenami niestandardowymi w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Aby dowiedzieć się więcej o **publikowaniu aplikacji,** zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
