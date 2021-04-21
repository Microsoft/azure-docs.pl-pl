---
title: Omówienie stref i rekordów DNS — Azure DNS
description: Omówienie obsługi hostowania stref i rekordów DNS w Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816967"
---
# <a name="overview-of-dns-zones-and-records"></a>Omówienie stref i rekordów DNS

W tym artykule wyjaśniono kluczowe pojęcia dotyczące domen, stref DNS, rekordów DNS i zestawów rekordów. Dowiesz się, jak jest ona obsługiwana w Azure DNS.

## <a name="domain-names"></a>Nazwy domen

System nazw domen (DNS, Domain Name System) jest hierarchią domen. Hierarchia rozpoczyna się od domeny głównej, której nazwa to po prostu „**.**”.  Poniżej są domeny najwyższego poziomu, takie jak „com”, „net”, „org”, „uk” lub „jp”.  Poniżej domen najwyższego poziomu znajdują się domeny drugiego poziomu, takie jak "org.uk" lub "co.jp". Domeny w hierarchii DNS są dystrybuowane globalnie, hostowane przez serwery nazw DNS na całym świecie.

Rejestrator nazw domen to organizacja, która umożliwia zakup nazwy domeny, takiej jak `contoso.com` .  Zakup nazwy domeny daje prawo do kontrolowania hierarchii DNS pod nazwą, na przykład umożliwiając skierowanie nazwy do `www.contoso.com` firmowej witryny internetowej. Rejestrator może hostować domenę na własnych serwerach nazw w Twoim imieniu lub umożliwiać określenie alternatywnych serwerów nazw.

Azure DNS udostępnia globalnie rozproszoną infrastrukturę serwera nazw o wysokiej dostępności, która umożliwia hostować domenę. Hostowanie domen w usłudze Azure DNS umożliwia zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi, rozliczeń i pomocy technicznej, co w przypadku innych usług platformy Azure.

Azure DNS obecnie nie obsługuje kupowania nazw domen. Jeśli chcesz kupić nazwę domeny, musisz użyć zewnętrznego rejestratora nazw domen. Rejestrator zazwyczaj nalicza niewielką roczną opłatę. Domeny mogą być następnie hostowane w Azure DNS do zarządzania rekordami DNS. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Strefy DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Rekordy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Czas na żywo

Czas wygaśnięcia (TTL) określa, jak długo każdy rekord jest buforowany przez klientów przed ponownym zapytaniem. W powyższym przykładzie czas wygaśnięcia wynosi 3600 sekund lub 1 godzinę.

W Azure DNS czas wygaśnięcia jest określany dla zestawu rekordów, a nie dla każdego rekordu, więc ta sama wartość jest używana dla wszystkich rekordów w tym zestawie rekordów.  Możesz określić dowolną wartość czasu wygaśnięcia z 1 do 2 147 483 647 sekund.

### <a name="wildcard-records"></a>Rekordy z symbolami wieloznacznymi

Usługa DNS platformy Azure obsługuje [rekordy z użyciem symboli wieloznacznych](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Rekordy z symbolami wieloznacznymi są zwracane w odpowiedzi na dowolne zapytanie o zgodnej nazwie, chyba że istnieje bliższe dopasowanie z zestawu rekordów bez symboli wieloznacznych. Azure DNS obsługuje zestawy rekordów z symbolami wieloznacznymi dla wszystkich typów rekordów z wyjątkiem NS i SOA.

Aby utworzyć zestaw rekordów z symbolami wieloznacznymi, użyj nazwy zestawu rekordów ' \* '. Możesz również użyć nazwy z " " jako etykietą najlewszą po lewej stronie, na \* przykład \* "foo".

### <a name="caa-records"></a>Rekordy CAA

Rekordy CAA umożliwiają właścicielom domeny określenie, które urzędy certyfikacji (CA) są autoryzowane do wydawania certyfikatów dla ich domeny. Ten rekord umożliwia urzędu certyfikacji uniknięcie błędnego wystawiania certyfikatów w pewnych okolicznościach. Rekordy CAA mają trzy właściwości:
* **Flagi:** to pole jest liczbą całkowitą z zakresu od 0 do 255, używaną do reprezentowania flagi krytycznej, która ma specjalne znaczenie dla specyfikacji [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag:** ciąg ASCII, który może być jednym z następujących:
    * **problem:** jeśli chcesz określić certyfikaty, które mogą wystawiać certyfikaty (wszystkie typy)
    * **issuewild:** jeśli chcesz określić certyfikaty, które mogą wystawiać certyfikaty (tylko certyfikaty z symbolami wieloznacznymi)
    * **iodef:** określ adres e-mail lub nazwę hosta, do których adresy certyfikacji mogą powiadamiać o nieautoryzowanych żądaniach wystawienia certyfikatu
* **Wartość:** wartość wybranego konkretnego tagu

### <a name="cname-records"></a>Rekordy CNAME

Zestawy rekordów CNAME nie mogą współistnieć z innymi zestawami rekordów o tej samej nazwie. Nie można na przykład utworzyć zestawu rekordów CNAME o nazwie względnej "www" i rekordu A o nazwie względnej "www".

Ponieważ wierzchołek strefy (nazwa = ' ') zawsze będzie zawierać zestawy rekordów NS i SOA podczas tworzenia strefy, nie można utworzyć zestawu rekordów CNAME w wierzchołku \@ strefy.

Te ograniczenia wynikają ze standardów dotyczących serwerów DNS i nie stanowią ograniczeń usługi DNS platformy Azure.

### <a name="ns-records"></a>Rekordy NS

Zestaw rekordów NS w wierzchołku strefy (nazwa ' ') jest tworzony automatycznie dla każdej strefy DNS i usuwany automatycznie po \@ usunięciu strefy. Nie można go usunąć oddzielnie.

Ten zestaw rekordów zawiera nazwy serwerów nazw Azure DNS przypisanych do strefy. Do tego zestawu rekordów NS można dodać więcej serwerów nazw, aby obsługiwać domeny współhostowania z więcej niż jednym dostawcą DNS. Możesz również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Jednak usuwanie lub modyfikowanie wstępnie wypełnionych serwerów nazw Azure DNS jest niedozwolone. 

To ograniczenie dotyczy tylko zestawu rekordów NS w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) można tworzyć, modyfikować i usuwać bez ograniczeń.

### <a name="soa-records"></a>Rekordy SOA

Zestaw rekordów SOA jest tworzony automatycznie w wierzchołku każdej strefy (nazwa = ' ') i jest automatycznie usuwany po \@ usunięciu strefy.  Rekordów SOA nie można tworzyć ani usuwać oddzielnie.

Można modyfikować wszystkie właściwości rekordu SOA z wyjątkiem właściwości "host". Ta właściwość jest wstępnie skonfigurowana tak, aby odwołyła się do nazwy serwera nazw podstawowych podanej przez Azure DNS.

Numer seryjny strefy w rekordzie SOA nie jest aktualizowany automatycznie, gdy zmiany w rekordach w strefie. W razie potrzeby można zaktualizować go ręcznie, edytując rekord SOA.

### <a name="spf-records"></a>Rekordy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Rekordy SRV

[Rekordy SRV](https://en.wikipedia.org/wiki/SRV_record) są używane przez różne usługi do określania lokalizacji serwera. Podczas określania rekordu SRV w Azure DNS:

* Usługa *i* *protokół muszą* być określone jako część nazwy zestawu rekordów z prefiksem podkreślenia.  Na przykład ' \_ sip. \_ tcp.name".  W przypadku rekordu w wierzchołku strefy nie trzeba określać "" w nazwie rekordu. Wystarczy użyć usługi i \@ protokołu, na przykład \_ "sip". \_ tcp".
* *Priorytet,* *waga,* *port* i element *docelowy* są określane jako parametry każdego rekordu w zestawie rekordów.

### <a name="txt-records"></a>Rekordy TXT

Rekordy TXT są używane do mapowania nazw domen na dowolne ciągi tekstowe. Są one używane w wielu aplikacjach, w szczególności związanych z konfiguracją poczty e-mail, takich jak [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) i [DomainKeys Identified Mail (DKIM).](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

Standardy DNS zezwalają na jeden rekord TXT zawiera wiele ciągów, z których każdy może mieć do 254 znaków. Jeśli jest używanych wiele ciągów, są one concatenated przez klientów i traktowane jako jeden ciąg.

Podczas wywoływania Azure DNS API REST należy osobno określić każdy ciąg TXT.  W przypadku korzystania Azure Portal, programu PowerShell lub interfejsu wiersza polecenia należy określić pojedynczy ciąg na rekord, który w razie potrzeby jest automatycznie dzielony na 254-znakowe segmenty.

Wielu ciągów w rekordzie DNS nie należy mylić z wieloma rekordami TXT w zestawie rekordów TXT.  Zestaw rekordów TXT może zawierać wiele rekordów, *z których każdy może* zawierać wiele ciągów.  Azure DNS obsługuje łączną długość ciągu do 1024 znaków w każdym zestawie rekordów TXT (dla wszystkich połączonych rekordów).

## <a name="tags-and-metadata"></a>Tagi i metadane

### <a name="tags"></a>Tagi

Tagi są listą par nazwa-wartość i są używane przez Azure Resource Manager do oznaczania etykietami zasobów. Azure Resource Manager używa tagów do włączania filtrowanych widoków rachunku za platformę Azure, a także umożliwia ustawienie zasad dla niektórych tagów. Aby uzyskać więcej informacji na temat tagów, zobacz [Using tags to organize your Azure resources](../azure-resource-manager/management/tag-resources.md) (Porządkowanie zasobów na platformie Azure za pomocą tagów).

Azure DNS obsługuje używanie Azure Resource Manager tagów w zasobach strefy DNS.  Nie obsługuje tagów w zestawach rekordów DNS, chociaż jako alternatywa "metadane" jest obsługiwana w zestawach rekordów DNS, jak wyjaśniono poniżej.

### <a name="metadata"></a>Metadane

Jako alternatywę dla tagów zestawu rekordów Azure DNS adnotacje zestawów rekordów przy użyciu "metadanych".  Podobnie jak w przypadku tagów, metadane umożliwiają skojarzenie par nazwa-wartość z każdym zestawem rekordów.  Ta funkcja może być przydatna, na przykład do rekordu przeznaczenia każdego zestawu rekordów.  W przeciwieństwie do tagów, metadanych nie można używać do zapewnienia filtrowanych widoków rachunku za platformę Azure i nie można ich określić w Azure Resource Manager zasad.

## <a name="etags"></a>Etagi

Załóżmy, że dwie osoby lub dwa procesy próbują zmodyfikować rekord DNS w tym samym czasie. Która z nich wygrywa? A czy zwyciężczyni wie, że nadpisała zmiany utworzone przez kogoś innego?

Azure DNS używa tagów Etag do bezpiecznej obsługi współbieżnych zmian w tym samym zasobie. Tagi Etag są oddzielone [od Azure Resource Manager tagów](#tags). Z każdym zasobem DNS (strefą lub zestawem rekordów) jest skojarzony element Etag. Za każdym razem, gdy zasób jest pobierany, pobierany jest również jego tag Etag. Podczas aktualizowania zasobu możesz przekazać z powrotem tag Etag, aby Azure DNS sprawdzić, czy tag Etag na serwerze jest odpowiedni. Ponieważ każda aktualizacja zasobu powoduje ponowne wygenerowanie tagu Etag, niezgodność tagu Etag wskazuje, że nastąpiła współbieżna zmiana. Tagów Etag można również używać podczas tworzenia nowego zasobu, aby upewnić się, że zasób jeszcze nie istnieje.

Domyślnie program Azure DNS PowerShell używa tagów Etag do blokowania współbieżnych zmian stref i zestawów rekordów. Opcjonalny przełącznik *-Overwrite* może służyć do pomijania kontroli tagów Etag. W takim przypadku wszelkie współbieżne zmiany, które wystąpiły, są zastępowane.

Na poziomie interfejsu API REST Azure DNS Etag są określane przy użyciu nagłówków HTTP.  Ich zachowanie znajduje się w poniższej tabeli:

| Nagłówek | Zachowanie |
| --- | --- |
| Brak |Put zawsze się powiedzie (bez sprawdzania tagów Etag) |
| Dopasowanie if-match \<etag> |Put powiedzie się tylko wtedy, gdy zasób istnieje i Etag dopasowuje |
| If-match * |Put powiedzie się tylko wtedy, gdy zasób istnieje |
| If-none-match * |Put kończy się powodzeniem tylko wtedy, gdy zasób nie istnieje |


## <a name="limits"></a>Limity

W przypadku korzystania z usługi Azure DNS obowiązują następujące limity Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć korzystanie Azure DNS, dowiedz się, jak [utworzyć strefę DNS](./dns-getstarted-portal.md) i [utworzyć rekordy DNS.](./dns-getstarted-portal.md)
* Aby przeprowadzić migrację istniejącej strefy DNS, dowiedz się, jak zaimportować i [wyeksportować plik strefy DNS.](dns-import-export.md)