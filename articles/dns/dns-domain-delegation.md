---
title: Azure DNS delegowania danych — omówienie
description: Dowiedz się, jak zmienić delegowanie domeny i korzystać z serwerów nazw usługi Azure DNS do zapewniania hostingu domeny.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/19/2021
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 4753b07cc2f3ccd998c26a3392eb08c8761dd6f7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738850"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegowanie stref DNS za pomocą usługi Azure DNS

Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Aby zapytania DNS dla domeny miały dostęp do usługi Azure DNS, należy delegować domenę do usługi Azure DNS z domeny nadrzędnej. Pamiętaj, Azure DNS nie jest rejestratorem domen. W tym artykule wyjaśniono, jak działa delegowanie domeny i jak delegować domeny do usługi Azure DNS.

## <a name="how-dns-delegation-works"></a>Jak działa delegowanie DNS

### <a name="domains-and-zones"></a>Domeny i strefy

System nazw domen (DNS, Domain Name System) jest hierarchią domen. Hierarchia rozpoczyna się od domeny głównej, której nazwa to po prostu „**.**”.  Poniżej są domeny najwyższego poziomu, takie jak „com”, „net”, „org”, „uk” lub „jp”.  Pod tymi domenami najwyższego poziomu są domeny drugiego poziomu, takie jak „org.uk” lub „co.jp”.  I tak dalej. Domeny w hierarchii DNS są hostowane przy użyciu osobnych stref DNS. Te strefy są globalnie rozproszone, hostowane przez serwery DNS na całym świecie.

**Strefa DNS** — Domena jest unikatową nazwą w systemie nazw domen, na przykład „contoso.com”. Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład domena „contoso.com” może zawierać wiele rekordów DNS, takich jak „mail.contoso.com” (dla serwera poczty) i „www.contoso.com” (dla witryny sieci Web).

**Rejestrator domen** — Rejestrator domen to firma, która może udostępniać nazwy domen internetowych. Weryfikuje ona, czy domena internetowa, której chcesz używać, jest dostępna, i umożliwi jej zakupienie. Po zarejestrowaniu nazwy domeny jesteś jej prawnym właścicielem. Jeśli masz już domenę internetową, użyjesz bieżącego rejestratora domen do delegowania do Azure DNS.

Aby uzyskać więcej informacji o akredytowanych rejestratorach domen, zobacz [ICANN-Akredytowani rejestratorzy](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Rozpoznawanie i delegowanie

Istnieją dwa typy serwerów DNS:

* *Autorytatywny* serwer DNS hostuje strefy DNS. Odpowiada na zapytania DNS dotyczące rekordów tylko w tych strefach.
* Cykliczny *serwer* DNS nie hostuje stref DNS. Odpowiada na zapytania DNS, wywołując autorytatywne serwery DNS w celu zebrania danych, których potrzebuje.

Usługa Azure DNS zapewnia autorytatywną usługę DNS.  Nie zapewnia rekursywnej usługi DNS. Usługa Cloud Services i maszyny wirtualne na platformie Azure są automatycznie skonfigurowane do korzystania z rekursywnych usług DNS udostępnianych oddzielnie w ramach infrastruktury platformy Azure. Aby uzyskać informacje na temat zmiany tych ustawień DNS, zobacz [Name Resolution in Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) (Rozpoznawanie nazw na platformie Azure).

Klienci DNS na komputerach lub urządzeniach przenośnych zwykle wywołują cykliczny serwer DNS, aby wykonać zapytania DNS potrzebne aplikacjom klienckim.

Gdy cykliczny serwer DNS odbiera zapytanie dotyczące rekordu DNS takiego jak „www.contoso.com”, najpierw musi znaleźć serwer nazw hostujący strefę dla domeny „contoso.com”. W celu znalezienia serwera nazw rozpoczyna od serwerów nazw głównych i wyszukuje serwery nazw hostujące strefę „com”. Następnie wykonuje zapytanie względem serwerów nazw „com”, aby znaleźć serwery nazw hostujące strefę „contoso.com”.  Na koniec jest w stanie odpytować te serwery nazw o "www.contoso.com".

Ta procedura jest nazywana rozpoznawaniem nazwy DNS. Mówiąc ściślej, rozpoznawanie nazw DNS obejmuje więcej kroków, takich jak następujące rekordy C CNAME, ale nie jest to ważne, aby zrozumieć, jak działa delegowanie DNS.

W jaki sposób strefa nadrzędna wskazuje serwery nazw dla strefy podrzędnej? Robi to przy użyciu specjalnego rekordu DNS nazywanego rekordem NS (angielski akronim „NS” oznacza „name server”, czyli „serwer nazw”). Na przykład strefa główna zawiera rekordy NS dla strefy „com” i pokazuje serwery nazw strefy „com”. Z kolei strefa „com” zawiera rekordy NS dla strefy „contoso.com”, która wyświetla serwery nazw dla strefy „contoso.com”. Konfigurowanie rekordów NS dla strefy podrzędnej w strefie nadrzędnej nazywa się delegowaniem domeny.

Na poniższej ilustracji przedstawiono przykładowe zapytanie DNS. Contoso.net i partners.contoso.net są strefami DNS na platformie Azure.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. Klient żąda adresu `www.partners.contoso.net` z lokalnego serwera DNS.
2. Lokalny serwer DNS nie ma rekordu, dlatego wykonuje żądanie do głównego serwera nazw.
3. Główny serwer nazw nie ma rekordu, ale zna adres serwera nazw, udostępnia ten adres `.net` serwerowi DNS
4. Lokalny serwer DNS wysyła żądanie do `.net` serwera nazw.
5. Serwer `.net` nazw nie ma rekordu, ale zna adres serwera `contoso.net` nazw. W takim przypadku odpowiada przy użyciu adresu serwera nazw dla strefy DNS hostowanej w Azure DNS.
6. Lokalny serwer DNS wysyła żądanie do serwera nazw dla strefy `contoso.net` hostowanej w Azure DNS.
7. Strefa nie ma rekordu, ale zna serwer nazw i `contoso.net` `partners.contoso.net` odpowiada za pomocą adresu. W tym przypadku jest to strefa DNS hostowana w Azure DNS.
8. Lokalny serwer DNS wysyła żądanie do serwera nazw dla `partners.contoso.net` strefy.
9. Strefa `partners.contoso.net` ma rekord A i odpowiada adresem IP.
10. Lokalny serwer DNS udostępnia klientowi adres IP
11. Klient łączy się z witryną sieci Web `www.partners.contoso.net`.

Każde delegowanie faktycznie zawiera dwie kopie rekordów NS — jedną w strefie nadrzędnej wskazującej strefę podrzędną i drugą w samej strefie podrzędnej. Strefa „contoso.net” zawiera rekordy NS dla strefy „contoso.net” (oprócz rekordów NS w strefie „net”). Są to tak zwane autorytatywne rekordy NS i znajdują się na wierzchołku strefy podrzędnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [delegować domenę do usługi Azure DNS](dns-delegate-domain-azure-dns.md).
