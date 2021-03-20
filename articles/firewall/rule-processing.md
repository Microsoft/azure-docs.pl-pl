---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/01/2021
ms.author: victorh
ms.openlocfilehash: bbf838cfa2a6addc665df4b62e2322d056778b49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741365"
---
# <a name="configure-azure-firewall-rules"></a>Konfigurowanie reguł zapory platformy Azure
Reguły translatora adresów sieciowych, reguły sieci i aplikacje można skonfigurować w zaporze platformy Azure. Kolekcje reguł są przetwarzane zgodnie z typem reguły w kolejności priorytetów, niższymi liczbami od 100 do 65 000. Nazwa kolekcji reguł może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. Musi zaczynać się literą lub cyfrą oraz kończyć się literą, cyfrą lub podkreśleniem. Maksymalna długość nazwy to 80 znaków.

Najlepiej wstępnie umieścić numery priorytetów kolekcji reguł w przyrostach 100 (100, 200, 300 itd.), aby w razie potrzeby dodać więcej kolekcji reguł.

> [!NOTE]
> W przypadku włączenia filtrowania opartego na analizie zagrożeń te reguły mają najwyższy priorytet i są zawsze przetwarzane jako pierwsze. Filtrowanie analizy zagrożeń może odmówić ruchu przed przetworzeniem skonfigurowanych reguł. Aby uzyskać więcej informacji, zobacz [filtrowanie oparte na analizie zagrożeń platformy Azure](threat-intel.md).

## <a name="outbound-connectivity"></a>Łączność wychodząca

### <a name="network-rules-and-applications-rules"></a>Reguły sieci i zasady dotyczące aplikacji

W przypadku konfigurowania reguł sieci i reguł aplikacji reguły sieci są stosowane w kolejności priorytetu przed regułami aplikacji. Reguły są przerywane. Dlatego w przypadku znalezienia dopasowania w regule sieciowej nie są przetwarzane żadne inne reguły.  Jeśli nie ma dopasowania reguły sieci, a protokół to HTTP, HTTPS lub MSSQL, pakiet jest następnie oceniany przez reguły aplikacji w kolejności priorytetów. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet jest oceniany względem [kolekcji reguł infrastruktury](infrastructure-fqdns.md). Jeśli nadal nie ma dopasowania, pakiet zostanie domyślnie odrzucony.

#### <a name="network-rule-protocol"></a>Protokół reguły sieci

Reguły sieci można skonfigurować dla **protokołów TCP**, **UDP**, **ICMP** lub **dowolnego** protokołu IP. Każdy protokół IP zawiera wszystkie protokoły IP zgodnie z definicją w dokumencie [numery protokołów IANA (Internet Assigned Numbers Authority)](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) . Jeśli port docelowy jest skonfigurowany jawnie, reguła zostanie przetłumaczona na regułę TCP + UDP.

Przed 9 listopada 2020 **wszystkie** przeznaczone do tego **protokoły TCP**, **UDP** i **ICMP**. Dlatego można skonfigurować regułę przed tą datą przy użyciu protokołu = any i portów docelowych = ' * '. Jeśli nie jest planowane Zezwalanie na żaden protokół IP jako obecnie zdefiniowany, należy zmodyfikować regułę, aby jawnie skonfigurować odpowiednie protokoły (TCP, UDP lub ICMP).

## <a name="inbound-connectivity"></a>Łączność przychodząca

### <a name="nat-rules"></a>Reguły NAT

Przychodzące połączenie internetowe można włączyć przez skonfigurowanie translacji adresów sieciowych (DNAT), zgodnie z opisem w [samouczku: filtrowanie ruchu przychodzącego za pomocą zapory platformy Azure DNAT przy użyciu Azure Portal](tutorial-firewall-dnat.md). Reguły NAT są stosowane w priorytecie przed regułami sieci. Jeśli zostanie znalezione dopasowanie, niejawna odpowiadająca reguła sieci umożliwia dodanie przetłumaczonego ruchu. Ze względów bezpieczeństwa zalecanym rozwiązaniem jest dodanie określonego źródła internetowego w celu umożliwienia DNAT dostępu do sieci i unikania używania symboli wieloznacznych.

Reguły aplikacji nie są stosowane do połączeń przychodzących. Dlatego jeśli chcesz filtrować ruch przychodzący HTTP/S, należy użyć zapory aplikacji sieci Web (WAF). Aby uzyskać więcej informacji, zobacz [co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Przykłady

Poniższe przykłady pokazują wyniki niektórych kombinacji reguł.

### <a name="example-1"></a>Przykład 1

Połączenie z usługą google.com jest dozwolone z powodu zgodnej reguły sieci.

**Reguła sieci**

- Akcja: Zezwalaj


|name  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj — sieć Web     |TCP|Adres IP|*|Adres IP|*|80 443

**Reguła aplikacji**

- Akcja: Odmów

|name  |Typ źródła  |Element źródłowy  |Protokół:port|Docelowe nazwy FQDN|
|---------|---------|---------|---------|----------|----------|
|Odmów — Google     |Adres IP|*|http: 80, https: 443|google.com

**Wynik**

Połączenie z usługą google.com jest dozwolone, ponieważ pakiet jest zgodny z regułą *Zezwalaj-sieci Web* . Przetwarzanie reguły zostało zatrzymane w tym momencie.

### <a name="example-2"></a>Przykład 2

Odmowa ruchu SSH, ponieważ wyższy priorytet *odmówi* kolekcji reguł sieci.

**Kolekcja reguł sieciowych 1**

- Name: Allow-Collection
- Priorytet: 200
- Akcja: Zezwalaj

|name  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Zezwalaj — SSH     |TCP|Adres IP|*|Adres IP|*|22

**Kolekcja reguł sieci 2**

- Nazwa: Odmów — zbieranie
- Priorytet: 100
- Akcja: Odmów

|name  |Protokół  |Typ źródła  |Element źródłowy  |Typ docelowy  |Adres docelowy  |Porty docelowe|
|---------|---------|---------|---------|----------|----------|--------|
|Odmów — SSH     |TCP|Adres IP|*|Adres IP|*|22

**Wynik**

Odmowa połączeń SSH, ponieważ kolekcja reguł sieci o wyższym priorytecie jest blokowana. Przetwarzanie reguły zostało zatrzymane w tym momencie.

## <a name="rule-changes"></a>Zmiany reguły

Jeśli zmienisz regułę tak, aby odmówił wcześniej dozwolony ruch, wszelkie odpowiednie istniejące sesje zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).
