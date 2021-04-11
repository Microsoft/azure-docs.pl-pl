---
title: Aplikacje wieloznaczne w serwer proxy aplikacji usługi Azure AD platformy Azure
description: Dowiedz się, jak używać wieloznacznych aplikacji na serwerze proxy aplikacji Azure Active Directory.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a532ae9485efa9571137130d32ba0827728e8094
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166874"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikacje wieloznaczne w Azure Active Directory serwerze proxy aplikacji

W Azure Active Directory (Azure AD) skonfigurowanie dużej liczby aplikacji lokalnych może szybko stać się w stanie niezarządzanym i wprowadza niepotrzebne zagrożenia dla błędów konfiguracji, jeśli wiele z nich wymaga tych samych ustawień. Za pomocą [usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md)można rozwiązać ten problem, używając wieloznacznego publikowania aplikacji w celu publikowania wielu aplikacji jednocześnie i zarządzania nimi. Jest to rozwiązanie, które umożliwia:

- Uprość narzuty administracyjne
- Zmniejsz liczbę błędów konfiguracji potencjalnych
- Umożliwienie użytkownikom bezpiecznego dostępu do większej liczby zasobów

Ten artykuł zawiera informacje potrzebne do skonfigurowania wieloznacznego publikowania aplikacji w środowisku.

## <a name="create-a-wildcard-application"></a>Tworzenie aplikacji wieloznacznej

Możesz utworzyć symbol wieloznaczny (*), jeśli masz grupę aplikacji z taką samą konfiguracją. Potencjalnymi kandydatami dla aplikacji wieloznacznej są aplikacje, które udostępniają następujące ustawienia:

- Grupa użytkowników, którzy mają do nich dostęp
- Metoda logowania jednokrotnego
- Protokół dostępu (http, https)

Możesz publikować aplikacje z symbolami wieloznacznymi, jeśli oba, wewnętrzne i zewnętrzne adresy URL mają następujący format:

> http (s)://*.\<domain\>

Na przykład: `http(s)://*.adventure-works.com`.

Ponieważ wewnętrzne i zewnętrzne adresy URL mogą korzystać z różnych domen, najlepszym rozwiązaniem powinna być taka sama. Podczas publikowania aplikacji zostanie wyświetlony komunikat o błędzie, jeśli jeden z adresów URL nie ma symbolu wieloznacznego.

Tworzenie aplikacji wieloznacznej bazuje na tym samym [przepływie publikowania aplikacji](application-proxy-add-on-premises-application.md) , który jest dostępny dla wszystkich innych aplikacji. Jedyną różnicą jest to, że w adresach URL jest uwzględniany symbol wieloznaczny, a potencjalnie konfiguracja logowania jednokrotnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, upewnij się, że zostały spełnione te wymagania.

### <a name="custom-domains"></a>Niestandardowe domeny

Chociaż [domeny niestandardowe](application-proxy-configure-custom-domain.md) są opcjonalne dla wszystkich innych aplikacji, są one wymagane w przypadku aplikacji wieloznacznych. Tworzenie domen niestandardowych wymaga:

1. Utwórz zweryfikowaną domenę na platformie Azure.
1. Przekaż certyfikat TLS/SSL w formacie PFX do serwera proxy aplikacji.

Należy rozważyć użycie certyfikatu wieloznacznego w celu dopasowania do aplikacji, którą zamierzasz utworzyć. 

Ze względów bezpieczeństwa jest to wymaganie twarde i nie będzie on obsługiwał symboli wieloznacznych dla aplikacji, które nie mogą używać domeny niestandardowej dla zewnętrznego adresu URL.

### <a name="dns-updates"></a>Aktualizacje DNS

W przypadku korzystania z domen niestandardowych należy utworzyć wpis DNS z rekordem CNAME dla zewnętrznego adresu URL (na przykład  `*.adventure-works.com` ) wskazującego zewnętrzny adres URL punktu końcowego serwera proxy aplikacji. W przypadku aplikacji z symbolami wieloznacznymi rekord CNAME musi wskazywać odpowiedni zewnętrzny adres URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Aby upewnić się, że rekord CNAME został prawidłowo skonfigurowany, można użyć [polecenia nslookup](/windows-server/administration/windows-commands/nslookup) na jednym z docelowych punktów końcowych, na przykład `expenses.adventure-works.com` .  Odpowiedź powinna zawierać już wymieniony alias ( `<yourAADTenantId>.tenant.runtime.msappproxy.net` ).

### <a name="using-connector-groups-assigned-to-an-app-proxy-cloud-service-region-other-than-the-default-region"></a>Używanie grup łączników przypisanych do regionu usługi w chmurze serwera proxy aplikacji innego niż domyślny region
Jeśli łączniki są zainstalowane w regionach innych niż domyślny region dzierżawy, może być korzystne, aby zmienić region, dla którego Twoja grupa łączników została zoptymalizowana, aby zwiększyć wydajność dostępu do tych aplikacji. Aby dowiedzieć się więcej, zobacz [Optymalizacja grup łączników do używania najbliższej usługi proxy aplikacji w chmurze](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).
 
Jeśli grupa łączników przypisana do aplikacji wieloznacznej używa regionu **innego niż domyślny**, należy zaktualizować rekord CNAME, aby wskazywał na adres URL określony w regionie. Skorzystaj z poniższej tabeli, aby określić odpowiedni adres URL:

| Region przypisany do łącznika | Zewnętrzny adres URL |
| ---   | ---         |
| Azja | `<yourAADTenantId>.asia.tenant.runtime.msappproxy.net`|
| Australia  | `<yourAADTenantId>.aus.tenant.runtime.msappproxy.net` |
| Europa  | `<yourAADTenantId>.eur.tenant.runtime.msappproxy.net`|
| Ameryka Północna  | `<yourAADTenantId>.nam.tenant.runtime.msappproxy.net` |

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę w przypadku aplikacji wieloznacznych.

### <a name="accepted-formats"></a>Akceptowane formaty

W przypadku aplikacji z symbolami wieloznacznymi **wewnętrzny adres URL** musi być sformatowany jako `http(s)://*.<domain>` .

![W przypadku wewnętrznego adresu URL Użyj formatu http (s)://*. \<> domeny](./media/application-proxy-wildcard/22.png)

W przypadku konfigurowania **zewnętrznego adresu URL** należy użyć następującego formatu: `https://*.<custom domain>`

![W przypadku zewnętrznego adresu URL Użyj formatu https://*. \<> domeny niestandardowej](./media/application-proxy-wildcard/21.png)

Inne pozycje symboli wieloznacznych, wielu symboli wieloznacznych lub innych ciągów wyrażeń regularnych nie są obsługiwane i powodują błędy.

### <a name="excluding-applications-from-the-wildcard"></a>Wykluczanie aplikacji z symboli wieloznacznych

Możesz wykluczyć aplikację z wieloznacznej aplikacji przez

- Publikowanie aplikacji wyjątku jako zwykłej aplikacji
- Włączanie symbolu wieloznacznego tylko dla określonych aplikacji za pomocą ustawień DNS

Publikowanie aplikacji jako zwykłej aplikacji jest preferowaną metodą wykluczenia aplikacji z symbolu wieloznacznego. Wykluczone aplikacje należy opublikować przed wieloznacznymi aplikacjami, aby upewnić się, że wyjątki są wymuszane od początku. Najbardziej konkretna aplikacja zawsze będzie mieć pierwszeństwo — aplikacja opublikowana jako `budgets.finance.adventure-works.com` ma pierwszeństwo przed aplikacją `*.finance.adventure-works.com` , która z kolei ma pierwszeństwo przed aplikacją `*.adventure-works.com` .

Możesz również ograniczyć symbol wieloznaczny, aby działał tylko dla określonych aplikacji za pomocą zarządzania DNS. Najlepszym rozwiązaniem jest utworzenie wpisu CNAME zawierającego symbol wieloznaczny i zgodny z formatem skonfigurowanego zewnętrznego adresu URL. Można jednak wskazywać konkretne adresy URL aplikacji w symbolach wieloznacznych. Na przykład zamiast `*.adventure-works.com` , Point `hr.adventure-works.com` , `expenses.adventure-works.com` i `travel.adventure-works.com individually` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` .

Jeśli użyjesz tej opcji, potrzebujesz także innego wpisu CNAME dla wartości `AppId.domain` , na przykład, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com` , również wskazują tę samą lokalizację. **Identyfikator appid** można znaleźć na stronie właściwości aplikacji symboli wieloznacznych:

![Znajdź identyfikator aplikacji na stronie właściwości aplikacji](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ustawianie adresu URL strony głównej dla panelu Moje aplikacje

Aplikacja z symbolami wieloznacznymi jest reprezentowana przy użyciu tylko jednego kafelka w [panelu Moje aplikacje](https://myapps.microsoft.com). Domyślnie ten kafelek jest ukryty. Aby wyświetlić kafelek i uzyskać użytkownikom na określonej stronie:

1. Postępuj zgodnie z wytycznymi dotyczącymi [ustawiania adresu URL strony głównej](application-proxy-configure-custom-home-page.md).
1. Na stronie właściwości aplikacji ustaw dla opcji **Pokaż aplikację** **wartość true** .

### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos

W przypadku aplikacji korzystających z [ograniczonego delegowania protokołu Kerberos (KCD) jako metody logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)Nazwa SPN określona dla metody SSO może również wymagać symbolu wieloznacznego. Na przykład może to być nazwa SPN: `HTTP/*.adventure-works.com` . Nadal musisz mieć skonfigurowane indywidualne nazwy SPN na serwerach zaplecza (na przykład `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com` ).

## <a name="scenario-1-general-wildcard-application"></a>Scenariusz 1: ogólna aplikacja symboli wieloznacznych

W tym scenariuszu masz trzy różne aplikacje, które chcesz opublikować:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Wszystkie trzy aplikacje:

- Są używane przez wszystkich użytkowników
- Użyj *zintegrowanego uwierzytelniania systemu Windows*
- Mieć te same właściwości

Można opublikować wieloznaczną aplikację, wykonując czynności opisane w temacie [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). W tym scenariuszu przyjęto założenie:

- Dzierżawy z następującym IDENTYFIKATORem: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Została skonfigurowana zweryfikowana domena o nazwie `adventure-works.com` .
- Wpis **CNAME** wskazujący, że został `*.adventure-works.com` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` utworzony.

Postępując zgodnie z [opisanymi krokami](application-proxy-add-on-premises-application.md), utworzysz nową aplikację serwera proxy aplikacji w dzierżawie. W tym przykładzie symbol wieloznaczny znajduje się w następujących polach:

- Wewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w wewnętrznym adresie URL](./media/application-proxy-wildcard/42.png)

- Zewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w zewnętrznym adresie URL](./media/application-proxy-wildcard/43.png)

- Wewnętrzna Nazwa SPN aplikacji:

    ![Przykład: symbol wieloznaczny w konfiguracji nazwy SPN](./media/application-proxy-wildcard/44.png)

Publikując wieloznaczną aplikację, możesz teraz uzyskać dostęp do swoich trzech aplikacji, przechodząc do adresów URL, które są używane do (na przykład `travel.adventure-works.com` ).

Konfiguracja implementuje następującą strukturę:

![Pokazuje strukturę zaimplementowaną przez przykładową konfigurację](./media/application-proxy-wildcard/05.png)

| Kolor | Opis |
| ---   | ---         |
| Blue (Niebieski)  | Aplikacje jawnie opublikowane i widoczne w Azure Portal. |
| Szary  | Aplikacje, które można uzyskać za pomocą aplikacji nadrzędnej. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenariusz 2: ogólna aplikacja wieloznaczna z wyjątkiem

W tym scenariuszu oprócz trzech ogólnych aplikacji jest inna aplikacja, `finance.adventure-works.com` która powinna być dostępna tylko przez dział finansów. W przypadku bieżącej struktury aplikacji aplikacja finansowa byłaby dostępna za pomocą aplikacji wieloznacznych i wszystkich pracowników. Aby to zmienić, należy wykluczyć aplikację z symbolu wieloznacznego, konfigurując finanse jako oddzielną aplikację z bardziej restrykcyjnymi uprawnieniami.

Należy upewnić się, że istnieją rekordy CNAME wskazujące `finance.adventure-works.com` punkt końcowy określony dla aplikacji, określony na stronie serwer proxy aplikacji. W tym scenariuszu `finance.adventure-works.com` wskazuje na `https://finance-awcycles.msappproxy.net/` .

Zgodnie z [opisanymi krokami](application-proxy-add-on-premises-application.md)ten scenariusz wymaga następujących ustawień:

- W **wewnętrznym adresie URL** ustawiasz opcję **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw wartość finanse zamiast symbolu wieloznacznego w wewnętrznym adresie URL](./media/application-proxy-wildcard/52.png)

- W **zewnętrznym adresie URL** ustawiasz opcję **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw finanse zamiast symbolu wieloznacznego w zewnętrznym adresie URL](./media/application-proxy-wildcard/53.png)

- Wewnętrzna Nazwa SPN aplikacji wewnętrznej ustawiana jako **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw finanse zamiast symbolu wieloznacznego w konfiguracji nazwy SPN](./media/application-proxy-wildcard/54.png)

Ta konfiguracja implementuje następujący scenariusz:

![Pokazuje konfigurację zaimplementowaną w przykładowym scenariuszu](./media/application-proxy-wildcard/09.png)

Ponieważ `finance.adventure-works.com` jest bardziej szczegółowym adresem URL niż `*.adventure-works.com` , ma pierwszeństwo. Użytkownicy przechodzenia do programu `finance.adventure-works.com` mają doświadczenie określone w aplikacji finanse. W takim przypadku tylko pracownicy finanse mogą uzyskać dostęp `finance.adventure-works.com` .

Jeśli masz wiele aplikacji opublikowanych na potrzeby finansów i masz `finance.adventure-works.com` zweryfikowaną domenę, możesz opublikować kolejną aplikację z symbolami wieloznacznymi `*.finance.adventure-works.com` . Ponieważ jest to bardziej szczegółowe niż ogólne `*.adventure-works.com` , ma pierwszeństwo w sytuacji, gdy użytkownik uzyskuje dostęp do aplikacji w domenie finansów.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o **domenach niestandardowych**, zobacz [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).
- Aby dowiedzieć się więcej o **publikowaniu aplikacji**, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
