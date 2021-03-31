---
title: Przewodnik rozwiązywania problemów — Azure DNS
description: W tej ścieżce szkoleniowej Rozpocznij Rozwiązywanie typowych problemów dotyczących Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: fae63c61949302e25c9dee2899577fa4f0d2a975
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965582"
---
# <a name="azure-dns-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów Azure DNS

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących typowych pytań Azure DNS.

Jeśli te kroki nie rozwiążą problemu, możesz również wyszukać lub opublikować swój problem na naszej [stronie pytania firmy&Microsoft dotyczącej wsparcia dla społeczności](/answers/topics/azure-virtual-network.html). Możesz też otworzyć żądanie pomocy technicznej platformy Azure.


## <a name="i-cant-create-a-dns-zone"></a>Nie mogę utworzyć strefy DNS

Aby rozwiązać typowe problemy, spróbuj wykonać następujące kroki:

1.  Zapoznaj się z dziennikami inspekcji Azure DNS, aby ustalić przyczynę niepowodzenia.
2.  Każda nazwa strefy DNS musi być unikatowa w obrębie swojej grupy zasobów. Oznacza to, że dwie strefy DNS o tej samej nazwie nie mogą współużytkować grupy zasobów. Spróbuj użyć innej nazwy strefy lub innej grupy zasobów.
3.  Możesz zobaczyć błąd „Osiągnięto lub przekroczono maksymalną liczbę stref w subskrypcji {identyfikator subskrypcji}”. Użyj innej subskrypcji platformy Azure, usuń kilka stref lub skontaktuj się z pomocą techniczną platformy Azure w celu podniesienia limitu dla subskrypcji.
4.  Możesz zobaczyć błąd „Strefa {nazwa strefy} jest niedostępna”. Ten błąd oznacza, że usługa DNS platformy Azure nie mogła przydzielić serwerów nazw dla tej strefy DNS. Spróbuj użyć innej nazwy strefy. Lub, jeśli jesteś właścicielem nazwy domeny, możesz skontaktować się z pomocą techniczną platformy Azure, aby przydzielić serwery nazw.


### <a name="recommended-articles"></a>Zalecane artykuły

* [Strefy i rekordy DNS](dns-zones-records.md)
* [Tworzenie strefy DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nie można utworzyć nowego rekordu DNS

Aby rozwiązać typowe problemy, spróbuj wykonać następujące kroki:

1.  Zapoznaj się z dziennikami inspekcji Azure DNS, aby ustalić przyczynę niepowodzenia.
2.  Czy zestaw rekordów już istnieje?  Usługa DNS platformy Azure zarządza rekordami za pomocą *zestawów*, czyli kolekcji rekordów o tej samej nazwie i typie. Jeśli już istnieje rekord o tej danej nazwie i typie, to w celu dodania kolejnego takiego rekordu trzeba poddać edycji istniejący zestaw rekordów.
3.  Czy próbujesz utworzyć rekord w wierzchołku strefy DNS (w „katalogu głównym” strefy)? Jeśli tak, to konwencja systemu DNS wymaga użycia znaku „@” jako nazwy rekordu. Należy również zauważyć, że standardy DNS nie zezwalają na rekordy CNAME w wierzchołku strefy.
4.  Czy występuje konflikt dotyczący rekordu CNAME?  Standardy DNS nie zezwalają na rekordy CNAME o takiej samej nazwie jak rekord dowolnego innego typu. Jeśli istnieje już rekord CNAME, utworzenie innego typu rekordu o takiej samej nazwie zakończy się niepowodzeniem.  Analogicznie, utworzenie rekordu CNAME zakończy się niepowodzeniem, jeśli jego nazwa jest taka sama jak nazwa istniejącego rekordu innego typu. Rozwiąż ten konflikt, usuwając drugi rekord lub wybierając inną nazwę rekordu.
5.  Czy osiągnięto limit liczby zestawów rekordów w strefie DNS? Bieżąca liczba zestawów rekordów oraz najwyższa możliwa liczba zestawów rekordów są pokazane w witrynie Azure Portal w obszarze „Właściwości” danej strefy. Jeśli osiągnięto ten limit, usuń niektóre zestawy rekordów lub skontaktuj się z pomocą techniczną platformy Azure, aby zgłosić limit zestawu rekordów dla tej strefy, a następnie spróbuj ponownie. 


### <a name="recommended-articles"></a>Zalecane artykuły

* [Strefy i rekordy DNS](dns-zones-records.md)
* [Tworzenie strefy DNS](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nie mogę rozpoznać mojego rekordu DNS

Rozpoznawanie nazw DNS to wieloetapowy proces, który może się nie powieść z wielu powodów. Następujące kroki zawierają informacje pomocne w badaniu przyczyn nieudanego rozpoznawania nazw DNS w przypadku rekordu DNS w strefie hostowanej w usłudze DNS platformy Azure.

1.  Sprawdź, czy rekordy DNS zostały prawidłowo skonfigurowane w usłudze DNS platformy Azure. Przejrzyj rekordy DNS w witrynie Azure Portal, sprawdzając, czy nazwa strefy, nazwa rekordu i typ rekordu są prawidłowe.
2.  Sprawdź, czy rekordy DNS są rozpoznawane prawidłowo na serwerach nazw usługi DNS platformy Azure.
    - Jeśli zapytania DNS są wysyłane z komputera lokalnego, mogą być wyświetlane wyniki z pamięci podręcznej. Nie odzwierciedlają one aktualnego stanu serwerów nazw.  Ponadto w sieciach firmowych często są używane serwery proxy usługi DNS, które uniemożliwiają kierowanie zapytań DNS do określonych serwerów nazw.  Aby uniknąć tych problemów, skorzystaj z usługi rozpoznawania nazw opartej na sieci Web, np. [digwebinterface](https://digwebinterface.com).
    - Pamiętaj, aby określić poprawne serwery nazw dla strefy DNS zgodnie z informacjami w witrynie Azure Portal.
    - Sprawdź, czy nazwa DNS jest poprawna (należy określić w pełni kwalifikowaną nazwę, z uwzględnieniem nazwy strefy) i czy typ rekordu jest poprawny
3.  Zobaczy, czy nazwa domeny DNS została prawidłowo [oddelegowana na serwery nazw usługi DNS platformy Azure](dns-domain-delegation.md). Istnieje [wiele witryn innych firm umożliwiających weryfikowanie delegowania DNS](https://www.bing.com/search?q=dns+check+tool). Ten test jest testem delegowania *strefy*, więc należy podać tylko nazwę strefy DNS, a nie w pełni kwalifikowaną nazwę rekordu.
4.  Po ukończeniu powyższych działań rozpoznawanie rekordu DNS powinno już przebiegać prawidłowo. Do weryfikacji można ponownie skorzystać z usługi [digwebinterface](https://digwebinterface.com), tym razem stosując domyślne ustawienia serwera nazw.


### <a name="recommended-articles"></a>Zalecane artykuły

* [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Jak określić „usługę” i „protokół” dla rekordu SRV?

Usługa DNS platformy Azure zarządza rekordami DBS za pomocą zestawów, czyli kolekcji rekordów o tej samej nazwie i typie. W przypadku zestawu rekordów SRV „usługa” i „protokół” muszą być określone jako część nazwy zestawu rekordów. Inne parametry rekordów SRV („priorytet”, „waga”, „port” i „miejsce docelowe”) są określane osobno dla każdego rekordu w zestawie rekordów.

Przykładowe nazwy rekordów SRV (nazwa usługi „sip”, protokół „tcp”):

- \_sip.\_tcp (tworzy zestaw rekordów w wierzchołku strefy)
- \_sip.\_tcp.sipservice (tworzy zestaw rekordów o nazwie „sipservice”)

### <a name="recommended-articles"></a>Zalecane artykuły

* [Strefy i rekordy DNS](dns-zones-records.md)
* [Create DNS record sets and records by using the Azure portal (Tworzenie zestawów rekordów i rekordów DNS przy użyciu witryny Azure Portal)](./dns-getstarted-portal.md)
* [Typ rekordu SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Następne kroki

* Informacje o [Azure DNS strefach i rekordach](dns-zones-records.md)
* Aby rozpocząć korzystanie z Azure DNS, Dowiedz się, jak [utworzyć strefę DNS](./dns-getstarted-portal.md) i [utworzyć rekordy DNS](./dns-getstarted-portal.md).
* Aby przeprowadzić migrację istniejącej strefy DNS, Dowiedz się, jak [importować i eksportować plik strefy DNS](dns-import-export.md).