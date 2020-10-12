---
title: Konfiguracja ustawień HTTP Application Gateway platformy Azure
description: W tym artykule opisano sposób konfigurowania ustawień protokołu HTTP w usłudze Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653021"
---
# <a name="application-gateway-http-settings-configuration"></a>Konfiguracja ustawień protokołu HTTP Application Gateway

Brama aplikacji kieruje ruch do serwerów zaplecza przy użyciu konfiguracji określonej w tym miejscu. Po utworzeniu ustawienia protokołu HTTP należy je skojarzyć z co najmniej jedną regułą routingu żądania.

## <a name="cookie-based-affinity"></a>Koligacja na podstawie pliku cookie

Usługa Azure Application Gateway używa plików cookie zarządzanych przez bramę do obsługi sesji użytkowników. Gdy użytkownik wysyła pierwsze żądanie do Application Gateway, ustawia plik cookie koligacji w odpowiedzi z wartością skrótu, która zawiera szczegóły sesji, dzięki czemu kolejne żądania przenoszące plik cookie koligacji będą kierowane do tego samego serwera wewnętrznej bazy danych w celu utrzymania lepkość. 

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze, a stan sesji jest zapisywany lokalnie na serwerze na potrzeby sesji użytkownika. Jeśli aplikacja nie może obsłużyć koligacji opartej na plikach cookie, nie można użyć tej funkcji. Aby go użyć, należy się upewnić, że klienci obsługują pliki cookie.

[Aktualizacja V80](https://chromiumdash.appspot.com/schedule) w [przeglądarce chrom](https://www.chromium.org/Home) została przypisana, gdzie pliki cookie HTTP bez atrybutu [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) musi być traktowany jako SameSite = swobodny. W przypadku żądań CORS (współużytkowanie zasobów między źródłami), jeśli plik cookie musi zostać wysłany w kontekście innej firmy, musi używać *SameSite = none; Bezpieczne* atrybuty i powinny być wysyłane tylko za pośrednictwem protokołu HTTPS. W przeciwnym razie w scenariuszu tylko HTTP przeglądarka nie wysyła plików cookie w kontekście innej firmy. Celem tej aktualizacji z programu Chrome jest podwyższenie poziomu zabezpieczeń oraz uniknięcie ataków na wiele witryn (CSRF). 

Aby obsłużyć tę zmianę, od lutego 17 2020, Application Gateway (wszystkie typy jednostek SKU) będą wstrzyknąć inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* , a także istniejący plik cookie *ApplicationGatewayAffinity* . Plik cookie *ApplicationGatewayAffinityCORS* ma dwa więcej atrybutów (*"SameSite = none; Secure "*), aby sesja programu Sticky Notes była utrzymywana nawet w przypadku żądań między źródłami.

Należy pamiętać, że domyślna nazwa pliku cookie koligacji to *ApplicationGatewayAffinity* i można ją zmienić. Jeśli używasz niestandardowej nazwy pliku cookie koligacji, dodatkowy plik cookie jest dodawany przy użyciu mechanizmu CORS jako sufiks. Na przykład *CustomCookieNameCORS*.

> [!NOTE]
> Jeśli atrybut *SameSite = none* jest ustawiony, wymagane jest, aby plik cookie zawierał także flagę *Secure* , i musi być wysyłany za pośrednictwem protokołu HTTPS.  Jeśli koligacja sesji jest wymagana za pośrednictwem mechanizmu CORS, należy przeprowadzić migrację obciążenia do protokołu HTTPS. Zapoznaj się z dokumentacją dotyczącą odciążania TLS i kompleksowej usługi TLS dla Application Gateway tym miejscu — [Omówienie](ssl-overview.md), [Konfigurowanie bramy aplikacji z zakończeniem protokołu tls przy użyciu Azure Portal](create-ssl-portal.md), [Konfigurowanie kompleksowej protokołu TLS przy użyciu Application Gateway z portalem](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń pomaga bezpiecznie usunąć członków puli zaplecza podczas aktualizacji planowanych usług. To ustawienie można zastosować do wszystkich elementów członkowskich puli zaplecza przez włączenie opróżniania połączenia w ustawieniu protokołu HTTP. Gwarantuje to, że wszystkie wyrejestrujące się wystąpienia puli zaplecza nadal utrzymują istniejące połączenia i obsługują żądania w celu skonfigurowania limitu czasu i nie otrzymają żadnych nowych żądań ani połączeń. Jedynym wyjątkiem są żądania związane z wyrejestrowywaniem wystąpień z powodu koligacji sesji zarządzanej przez bramę i będzie on nadal przekazywany do wyrejestrowania wystąpień. Opróżnianie połączeń dotyczy wystąpień zaplecza, które są jawnie usuwane z puli zaplecza.

## <a name="protocol"></a>Protokół

Application Gateway obsługuje zarówno protokół HTTP, jak i HTTPS w przypadku żądań routingu do serwerów zaplecza. W przypadku wybrania protokołu HTTP ruch do serwerów zaplecza jest niezaszyfrowany. Jeśli nieszyfrowana komunikacja nie jest akceptowalna, wybierz pozycję HTTPS.

To ustawienie połączone z protokołem HTTPS w odbiorniku obsługuje [kompleksową](ssl-overview.md)transkonfigurację TLS. Dzięki temu można bezpiecznie przesyłać poufne dane zaszyfrowane do zaplecza. Każdy serwer zaplecza w puli zaplecza z włączonym kompleksowym protokołem TLS musi być skonfigurowany przy użyciu certyfikatu w celu umożliwienia bezpiecznej komunikacji.

## <a name="port"></a>Port

To ustawienie określa port, w którym serwery zaplecza nasłuchują ruchu z bramy aplikacji. Można skonfigurować porty z zakresu od 1 do 65535.

## <a name="request-timeout"></a>Limit czasu żądania

To ustawienie określa liczbę sekund oczekiwania przez bramę aplikacji na odebranie odpowiedzi z serwera zaplecza.

## <a name="override-back-end-path"></a>Zastąp ścieżkę zaplecza

To ustawienie umożliwia skonfigurowanie opcjonalnej niestandardowej ścieżki przesyłania dalej do użycia, gdy żądanie jest przekazywane do zaplecza. Dowolna część ścieżki przychodzącej zgodna ze ścieżką niestandardową w polu **Przesłaniaj ścieżkę zaplecza** jest kopiowana do przekazanej ścieżki. W poniższej tabeli przedstawiono sposób działania tej funkcji:

- Gdy ustawienie protokołu HTTP jest dołączone do podstawowej reguły routingu żądania:

  | Oryginalne żądanie  | Zastąp ścieżkę zaplecza | Żądanie przekazane do zaplecza |
  | ----------------- | --------------------- | ---------------------------- |
  | mowa            | opcją/override.            | /override/home/              |
  | /home/secondhome/ | opcją/override.            | /override/home/secondhome/   |

- Po dołączeniu ustawienia protokołu HTTP do reguły routingu żądania opartej na ścieżce:

  | Oryginalne żądanie           | Reguła ścieżki       | Zastąp ścieżkę zaplecza | Żądanie przekazane do zaplecza |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | elemencie pathrule      | opcją/override.            | /override/home/              |
  | /pathrule/home/secondhome/ | elemencie pathrule      | opcją/override.            | /override/home/secondhome/   |
  | mowa                     | elemencie pathrule      | opcją/override.            | /override/home/              |
  | /home/secondhome/          | elemencie pathrule      | opcją/override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | opcją/override.            | opcją/override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | opcją/override.            | /override/secondhome/        |
  | elemencie pathrule                 | elemencie pathrule      | opcją/override.            | opcją/override.                   |

## <a name="use-for-app-service"></a>Korzystanie z usługi App Service

Jest to skrót tylko do interfejsu użytkownika, który wybiera dwa wymagane ustawienia dla Azure App Service zaplecza. Umożliwia **wybranie nazwy hosta z adresu zaplecza**i utworzenie nowej sondy niestandardowej, jeśli nie została ona jeszcze zainstalowana. (Aby uzyskać więcej informacji, zobacz sekcję [Wybieranie nazwy hosta z ustawienia adres zaplecza w](#pick-host-name-from-back-end-address)tym artykule). Zostanie utworzona nowa sonda, a nagłówek sondy jest wybierany z adresu członka zaplecza.

## <a name="use-custom-probe"></a>Użyj sondy niestandardowej

To ustawienie kojarzy [niestandardową sondę](application-gateway-probe-overview.md#custom-health-probe) z USTAWIENIEM protokołu HTTP. Można skojarzyć tylko jedną sondę niestandardową z ustawieniem protokołu HTTP. Jeśli nie utworzysz jawnie niestandardowej sondy, do monitorowania kondycji zaplecza służy [sonda domyślna](application-gateway-probe-overview.md#default-health-probe-settings) . Zalecamy utworzenie niestandardowej sondy do większej kontroli nad monitorowaniem kondycji zaplecza.

> [!NOTE]
> Niestandardowa Sonda nie monitoruje kondycji puli zaplecza, chyba że odpowiednie ustawienie protokołu HTTP jest jawnie skojarzone z odbiornikiem.

## <a name="pick-host-name-from-back-end-address"></a>Wybierz nazwę hosta z adresu zaplecza

Ta funkcja dynamicznie ustawia nagłówek *hosta* w żądaniu na nazwę hosta puli zaplecza. Używa adresu IP lub nazwy FQDN.

Ta funkcja pomaga, gdy nazwa domeny zaplecza różni się od nazwy DNS bramy aplikacji, a zaplecze opiera się na określonym nagłówku hosta, aby rozwiązać prawidłowy punkt końcowy.

Przykładem przypadku są usługi wielodostępne jako zaplecze. Usługa App Service to usługa z wieloma dzierżawami, która używa udostępnionego miejsca z pojedynczym adresem IP. Z tego względu dostęp do usługi App Service można uzyskać tylko za pomocą nazw hostów skonfigurowanych w niestandardowych ustawieniach domeny.

Domyślnie niestandardowa nazwa domeny to *example.azurewebsites.NET*. Aby uzyskać dostęp do usługi App Service przy użyciu bramy aplikacji za pomocą nazwy hosta, która nie jest jawnie zarejestrowana w usłudze App Service lub za pośrednictwem nazwy FQDN bramy aplikacji, należy zastąpić nazwę hosta w oryginalnym żądaniu do nazwy hosta usługi App Service. W tym celu należy włączyć ustawienie **Wybierz nazwę hosta z adresu zaplecza** .

W przypadku domeny niestandardowej, której istniejąca niestandardowa nazwa DNS jest zamapowana na usługę App Service, nie trzeba włączać tego ustawienia.

> [!NOTE]
> To ustawienie nie jest wymagane w przypadku App Service Environment, który jest wdrożeniem dedykowanym.

## <a name="host-name-override"></a>Zastąpienie nazwy hosta

Ta funkcja zastępuje nagłówek *hosta* w żądaniu przychodzącym w bramie aplikacji z określoną nazwą hosta.

Na przykład jeśli *www.contoso.com* jest określony w ustawieniu **Nazwa hosta** , oryginalne żądanie * `https://appgw.eastus.cloudapp.azure.com/path1` zostanie zmienione na *, `https://www.contoso.com/path1` gdy żądanie jest przekazywane do serwera zaplecza.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o puli zaplecza](configuration-overview.md#back-end-pool)