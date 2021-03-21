---
title: Przegląd Strefy DNS i rekordów — Azure DNS
description: Omówienie obsługi hostingu stref i rekordów DNS w Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968200"
---
# <a name="overview-of-dns-zones-and-records"></a>Omówienie stref i rekordów DNS

Na tej stronie objaśniono kluczowe pojęcia dotyczące domen, stref DNS i rekordów DNS i zestawów rekordów oraz sposób ich obsługi w Azure DNS.

## <a name="domain-names"></a>Nazwy domen

System nazw domen (DNS, Domain Name System) jest hierarchią domen. Hierarchia rozpoczyna się od domeny głównej, której nazwa to po prostu „**.**”.  Poniżej są domeny najwyższego poziomu, takie jak „com”, „net”, „org”, „uk” lub „jp”.  Pod nimi są domeny drugiego poziomu, takie jak „org.uk” lub „co.jp”. Domeny w hierarchii DNS są dystrybuowane globalnie, hostowane przez serwery nazw DNS na całym świecie.

Rejestrator nazw domen to organizacja, która umożliwia zakupienie nazwy domeny, takiej jak `contoso.com` .  Zakup nazwy domeny daje prawo do kontrolowania hierarchii DNS pod tą nazwą, na przykład w celu skierowania nazwy `www.contoso.com` do firmowej witryny sieci Web. Rejestrator może hostować domenę we własnych serwerach nazw w Twoim imieniu lub zezwolić na Określanie alternatywnych serwerów nazw.

Azure DNS oferuje globalnie dystrybuowaną infrastrukturę serwera nazw o wysokiej dostępności, której można używać do hostowania domeny. Hosting domen w Azure DNS umożliwia zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi, rozliczeń i pomocy technicznej co w przypadku innych usług platformy Azure.

Azure DNS obecnie nie obsługuje kupowania nazw domen. Jeśli chcesz kupić nazwę domeny, musisz użyć rejestratora nazw domen innej firmy. Rejestrator zazwyczaj obciąża małą roczną opłatą. Domeny mogą być następnie hostowane w Azure DNS na potrzeby zarządzania rekordami DNS. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Strefy DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Rekordy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia (TTL) określa, jak długo każdy rekord jest buforowany przez klientów przed ponownym uruchomieniem zapytania. W powyższym przykładzie czas wygaśnięcia to 3600 sekund lub 1 godzina.

W Azure DNS czas TTL jest określany dla zestawu rekordów, a nie dla każdego rekordu, więc ta sama wartość jest używana dla wszystkich rekordów w tym zestawie rekordów.  Można określić dowolną wartość TTL z zakresu od 1 do 2 147 483 647 sekund.

### <a name="wildcard-records"></a>Rekordy symboli wieloznacznych

Usługa DNS platformy Azure obsługuje [rekordy z użyciem symboli wieloznacznych](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Rekordy symboli wieloznacznych są zwracane w odpowiedzi na dowolne zapytanie o pasującej nazwie (chyba że istnieje bliższe dopasowanie z zestawu rekordów innego niż symbol wieloznaczny). Azure DNS obsługuje wieloznaczne zestawy rekordów dla wszystkich typów rekordów, z wyjątkiem NS i SOA.

Aby utworzyć zestaw rekordów z symbolami wieloznacznymi, użyj nazwy zestawu rekordów " \* ". Alternatywnie można również użyć nazwy z " \* " jako jej lewej strony etykiety, na przykład " \* . foo".

### <a name="caa-records"></a>CAA rekordy

Rekordy CAA umożliwiają właścicielom domeny Określanie, które urzędy certyfikacji są autoryzowane do wystawiania certyfikatów dla ich domeny. Dzięki temu urzędy certyfikacji mogą uniknąć nieprawidłowo wystawiania certyfikatów w pewnych okolicznościach. Rekordy CAA mają trzy właściwości:
* **Flagi**: jest to liczba całkowita z zakresu od 0 do 255, używana do reprezentowania flagi krytycznej, która ma specjalne znaczenie dla elementu [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: ciąg ASCII, który może mieć jedną z następujących wartości:
    * **problem**: Użyj tego, jeśli chcesz określić urzędy certyfikacji, które mogą wystawiać certyfikaty (wszystkie typy)
    * **issuewild**: Użyj tego, jeśli chcesz określić urzędy certyfikacji, które mogą wystawiać certyfikaty (tylko Certyfikaty wieloznaczne)
    * **iodef**: Określ adres e-mail lub nazwę hosta, dla którego urzędów certyfikacji mogą powiadamiać o nieautoryzowanych żądaniach wydawania certyfikatów
* **Wartość**: wybrana wartość dla określonego tagu

### <a name="cname-records"></a>Rekordy CNAME

Zestawy rekordów CNAME nie mogą współistnieć z innymi zestawami rekordów o tej samej nazwie. Na przykład nie można utworzyć zestawu rekordów CNAME o nazwie względnej "www" i rekordzie A o nazwie względnej "www" w tym samym czasie.

Ponieważ Apex strefy (nazwa = ' \@ ') zawsze zawiera zestawy rekordów NS i SOA, które zostały utworzone podczas tworzenia strefy, nie można utworzyć zestawu rekordów CNAME w wierzchołku strefy.

Te ograniczenia powstają na podstawie standardów DNS i nie są ograniczeniami Azure DNS.

### <a name="ns-records"></a>Rekordy NS

Rekord NS ustawiony na wierzchołku strefy (nazwa " \@ ") jest tworzony automatycznie przy użyciu każdej strefy DNS i jest automatycznie usuwany po usunięciu strefy (nie można go usunąć oddzielnie).

Ten zestaw rekordów zawiera nazwy Azure DNS serwerów nazw przypisanych do strefy. Do tego zestawu rekordów NS można dodać kolejne serwery nazw, aby obsługiwać domeny współpracujące z więcej niż jednym dostawcą DNS. Możesz również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Nie można jednak usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw Azure DNS. 

Ma to zastosowanie tylko do zestawu rekordów NS w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) mogą być tworzone, modyfikowane i usuwane bez ograniczenia.

### <a name="soa-records"></a>Rekordy SOA

Zestaw rekordów SOA jest tworzony automatycznie w wierzchołku każdej strefy (nazwa = ' \@ ') i jest automatycznie usuwany po usunięciu strefy.  Rekordy SOA nie mogą być tworzone ani usuwane osobno.

Można zmodyfikować wszystkie właściwości rekordu SOA z wyjątkiem właściwości "host", która jest wstępnie skonfigurowana do odwoływania się do nazwy podstawowego serwera nazw dostarczonego przez Azure DNS.

Numer seryjny strefy w rekordzie SOA nie jest aktualizowany automatycznie, gdy zmiany są wprowadzane do rekordów w strefie. Można ją zaktualizować ręcznie, edytując rekord SOA, w razie potrzeby.

### <a name="spf-records"></a>Rekordy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Rekordy SRV

[Rekordy SRV](https://en.wikipedia.org/wiki/SRV_record) są używane przez różne usługi do określania lokalizacji serwera. Podczas określania rekordu SRV w Azure DNS:

* Należy określić *usługę* i *Protokół* jako część nazwy zestawu rekordów poprzedzoną znakami podkreślenia.  Na przykład " \_ SIP. \_ tcp.name '.  W przypadku rekordu w wierzchołku strefy nie ma potrzeby określania elementu " \@ " w nazwie rekordu, po prostu Użyj usługi i protokołu, na przykład " \_ SIP. \_ TCP '.
* *Priorytet*, *waga*, *port* i *cel* są określone jako parametry każdego rekordu w zestawie rekordów.

### <a name="txt-records"></a>Rekordy TXT

Rekordy TXT są używane do mapowania nazw domen na dowolne ciągi tekstowe. Są one używane w wielu aplikacjach, w szczególności w odniesieniu do konfiguracji poczty e-mail, takie jak [platforma zasad nadawcy (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) i [DomainKeys zidentyfikowane wiadomości e-mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy DNS zezwalają na pojedynczy rekord TXT, aby zawierały wiele ciągów, z których każdy może mieć długość do 254 znaków. Gdzie są używane wiele ciągów, są one połączone przez klientów i traktowane jako jeden ciąg.

Podczas wywoływania interfejsu API REST Azure DNS należy osobno określić każdy ciąg TXT.  W przypadku korzystania z Azure Portal, programu PowerShell lub interfejsu wiersza polecenia należy określić pojedynczy ciąg dla każdego rekordu, który jest automatycznie podzielony na segmenty 254-znak, jeśli jest to konieczne.

Nie należy mylić wielu ciągów w rekordzie DNS z wieloma rekordami TXT w zestawie rekordów TXT.  Zestaw rekordów TXT może zawierać wiele rekordów, *z których każdy* może zawierać wiele ciągów.  Azure DNS obsługuje łączną długość ciągu do 1024 znaków w każdym zestawie rekordów TXT (między wszystkimi rekordami połączonymi).

## <a name="tags-and-metadata"></a>Tagi i metadane

### <a name="tags"></a>Tagi

Tagi są listą par nazwa-wartość i są używane przez Azure Resource Manager do etykietowania zasobów.  Azure Resource Manager używa tagów do włączania filtrowanych widoków rachunku na korzystanie z platformy Azure, a także umożliwia ustawienie zasad, na których Tagi są wymagane. Aby uzyskać więcej informacji na temat tagów, zobacz [Using tags to organize your Azure resources](../azure-resource-manager/management/tag-resources.md) (Porządkowanie zasobów na platformie Azure za pomocą tagów).

Azure DNS obsługuje używanie tagów Azure Resource Manager w zasobach strefy DNS.  Nie obsługuje ona tagów w zestawach rekordów DNS, chociaż jako alternatywny element "Metadata" jest obsługiwany w zestawach rekordów DNS, jak wyjaśniono poniżej.

### <a name="metadata"></a>Metadane

Jako alternatywa dla znaczników zestawu rekordów, Azure DNS obsługuje dodawanie adnotacji do zestawów rekordów przy użyciu "Metadata".  Podobnie jak w przypadku tagów, metadane umożliwiają kojarzenie par nazwa-wartość z każdym zestawem rekordów.  Może to być przydatne, na przykład w celu rejestrowania przeznaczenie każdego zestawu rekordów.  W przeciwieństwie do tagów, metadane nie mogą być używane w celu zapewnienia filtrowanego widoku rachunku na korzystanie z platformy Azure i nie mogą być określone w zasadach Azure Resource Manager.

## <a name="etags"></a>Elementów ETag

Załóżmy, że dwa osoby lub dwa procesy próbują w tym samym czasie zmodyfikować rekord DNS. Który serwer WINS? Czy zwycięzca wie, że zastąpiły zmiany wprowadzone przez kogoś innego?

Azure DNS używa elementów ETag do bezpiecznego obsługi równoczesnych zmian w tym samym zasobie. Elementy ETag są oddzielone od [Azure Resource Manager "Tags"](#tags). Do każdego zasobu DNS (strefy lub zestawu rekordów) jest skojarzony element ETag. Za każdym razem, gdy pobierany jest zasób, jest również pobierany jego element ETag. Podczas aktualizowania zasobu można wybrać opcję przekazania elementu ETag, aby Azure DNS mógł sprawdzić, czy element ETag na serwerze jest zgodny. Ponieważ każda aktualizacja zasobu powoduje ponowne wygenerowanie elementu ETag, niezgodność ETag wskazuje, że wystąpiła współbieżna zmiana. Elementy ETag mogą być również używane podczas tworzenia nowego zasobu, aby upewnić się, że zasób jeszcze nie istnieje.

Domyślnie program Azure DNS PowerShell używa elementów ETag do blokowania współbieżnych zmian w strefach i zestawach rekordów. Przełącznik opcjonalnego *zastępowania* może służyć do pomijania testów ETag, w tym przypadku wszystkie współbieżne zmiany, które wystąpiły, zostaną zastąpione.

Na poziomie interfejsu API REST Azure DNS elementy ETag są określane przy użyciu nagłówków HTTP.  Ich zachowanie jest podano w poniższej tabeli:

| Nagłówek | Zachowanie |
| --- | --- |
| Brak |UMIESZCZAj zawsze zakończone powodzeniem (brak testów ETag) |
| If-Match \<etag> |Element PUT kończy się powodzeniem tylko wtedy, gdy zasób istnieje i element ETag pasuje |
| If-Match * |PUT kończy się powodzeniem, jeśli zasób istnieje |
| If-None-Match * |PUT kończy się powodzeniem, jeśli zasób nie istnieje |


## <a name="limits"></a>Limity

W przypadku korzystania z Azure DNS są stosowane następujące domyślne limity:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć korzystanie z Azure DNS, Dowiedz się, jak [utworzyć strefę DNS](./dns-getstarted-portal.md) i [utworzyć rekordy DNS](./dns-getstarted-portal.md).
* Aby przeprowadzić migrację istniejącej strefy DNS, Dowiedz się, jak [importować i eksportować plik strefy DNS](dns-import-export.md).