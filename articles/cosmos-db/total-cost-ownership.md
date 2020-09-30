---
title: Łączny koszt posiadania (TCO) z Azure Cosmos DB
description: W tym artykule porównano całkowity koszt posiadania Azure Cosmos DB z IaaS i lokalnymi bazami danych
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 1e6d0343a68d502fb567f6010b8c8b3043e282a8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569488"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Łączny koszt posiadania (TCO) z Azure Cosmos DB

Usługa Azure Cosmos DB została zaprojektowana z myślą o precyzyjnej obsłudze wielu dzierżawców i zarządzania zasobami. Dzięki takiej konstrukcji korzystanie z usługi Azure Cosmos DB jest znacznie tańsze, a użytkownicy mogą zaoszczędzić pieniądze. Obecnie usługa Azure Cosmos DB obsługuje ponad 280 obciążeń klientów na pojedynczej maszynie z ciągle rosnącą gęstością oraz tysiące obciążeń klientów w ramach klastra. Równoważy ona repliki obciążeń klientów na różnych maszynach w klastrze oraz na wielu klastrach w centrum danych. Aby dowiedzieć się więcej, zobacz [Azure Cosmos DB: wypychanie granicy dystrybuowanych globalnie baz danych](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Ze względu na współdziałanie z usługą Resource-ładu, Wielodostępność i natywną integrację z resztą infrastruktury platformy Azure, Azure Cosmos DB jest średnio od 4 do 6 razy tańsze niż MongoDB, Cassandra lub inne NoSQL OSS uruchomione w IaaS i nawet 10 razy tańsze niż aparaty bazy danych działające lokalnie. Zobacz dokument dotyczący [łącznego kosztu (nie) własności usługi NoSQL Database w chmurze](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

W przypadku rozwiązań baz danych OSS NoSQL, takich jak Apache Cassandra, MongoDB, HBase, aparaty zostały zaprojektowane do użytku lokalnego. Oferowana jako usługa zarządzana jest równoważna z szablonem Menedżer zasobów z bazą danych dzierżawy do zarządzania klastrami i obsługą monitorowania. Architektury OSS NoSQL wymagają znaczących obciążeń operacyjnych, a wiedza może być trudna i kosztowna do znalezienia. Z drugiej strony Azure Cosmos DB to w pełni zarządzana usługa w chmurze, która umożliwia deweloperom skoncentrowanie się na innowacyjności biznesowej, a nie na zarządzaniu infrastrukturą bazy danych i jej utrzymywaniu.

W przeciwieństwie do natywnej Azure Cosmos DB usługi bazy danych w chmurze, aparaty bazy danych OSS NoSQL nie zostały zaprojektowane i skompilowane przy użyciu zarządzania zasobami lub z szczegółowym użyciem wielu dzierżawców jako podstawowych zasad architektury. Aparaty bazy danych OSS NoSQL, takie jak Cassandra i MongoDB, stanowią podstawowe założenie, że wszystkie zasoby maszyny wirtualnej, na których są uruchomione, są dostępne do użycia. Wiele z tych aparatów baz danych nie może działać, jeśli ilość zasobów spadnie poniżej określonego progu. Na przykład w przypadku małych wystąpień maszyn wirtualnych i są one dostępne z konfiguracjami zalecanymi przez dostawcę, które sugerują zwykle maszyny wirtualne o dużej skali z wyższym kosztem. W związku z tym nie można hostować usługi OSS NoSQL ani innego lokalnego aparatu bazy danych i udostępnić go przy użyciu modelu ładowania opartego na zużyciu, takiego jak żądania na sekundę lub zużyty magazyn.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Łączny koszt posiadania Azure Cosmos DB

Model aprowizacji bezserwerowej Azure Cosmos DB eliminuje konieczność przekroczenia obsługi administracyjnej infrastruktury baz danych. Zasoby Azure Cosmos DB są udostępniane bez potrzeby wyspecjalizowanych konfiguracji ani licencjonowania. W efekcie aplikacje z obsługą kopii zapasowej Azure Cosmos DB mogą działać z użyciem nawet 70% całkowitego kosztu oszczędności dla własności w porównaniu z bazami danych OSS NoSQL. Aby zapoznać się z niektórymi przykładami w czasie rzeczywistym, zobacz [przypadki użycia klientów](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Inne korzyści wynikające z modelu cen Azure Cosmos DB obejmują:

* **Doskonały koszt:** Analitycy rynkowi, klienci i partnerzy potwierdzą większą wartość wszystkich funkcji, które Azure Cosmos DB oferują znacznie niższą cenę w porównaniu z tym, co klienci mogą uzyskać podczas wdrażania tych rozwiązań samodzielnie lub przez innych dostawców. Baza danych oferuje takie funkcje, jak globalna dystrybucja, zapisy wieloregionowe, dobrze zdefiniowane i intuicyjne modele spójności, Automatyczne indeksowanie jest znacznie uproszczone dzięki Azure Cosmos DB bez żadnej złożoności, nakładu pracy ani przestojów.

* **NoSQL DevOps nie jest wymagana:** W przypadku Azure Cosmos DB jeden nie musi wykorzystywać DevOps do zarządzania wdrożeniami, wykonywania czynności konserwacyjnych, skalowania i poprawiania. Można wykonać wszystkie obciążenia, które można wykonać przy użyciu klastra OSS NoSQL hostowanego lokalnie lub w infrastrukturze chmurowej.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Koszt posiadania Azure Cosmos DB" border="false":::

* **Możliwość elastycznego skalowania:** Azure Cosmos DB przepływności można skalować w górę i w dół, co pozwala zmniejszyć koszty użytkowania w czasie poza szczytem. Klastry OSS NoSQL wdrożone w infrastrukturze chmurowej zapewniają ograniczoną elastyczność, a lokalne wdrożenia nie są elastyczne według definicji. W Azure Cosmos DB, jeśli zapewniasz większą przepływność, przepustowość jest gwarantowana w skali liniowej. Ta gwarancja została utworzona przez umowy SLA finansowy i w 99 percentylu w dowolnej skali.

* **Oszczędności skali:** Usługa zarządzana, taka jak Azure Cosmos DB, działa z dużą liczbą węzłów, zintegrowany natywnie z obsługą sieci, magazynu i obliczeń. Ze względu na dużą skalę Azure Cosmos DB standaryzacji można zaoszczędzić koszty.

* **Optymalizacja pod kątem chmury:** Azure Cosmos DB jest zaprojektowana od podstaw dzięki szczegółowej izolacji wielu dzierżawców i wydajności. Pozwala to na optymalne umieszczanie, wykonywanie i równoważenie tysięcy dzierżawców i ich obciążeń między klastrami i centrami danych. Z kolei bieżąca generacja baz danych OSS NoSQL działa lokalnie z całą maszyną wirtualną przyjętą do uruchomienia obciążenia jednej dzierżawy. Te bazy danych nie są również zaprojektowane w celu wykorzystania infrastruktury i sprzętu dostawcy chmury w pełnym zakresie. Na przykład aparat bazy danych OSS NoSQL nie ma informacji o różnicach między maszyną wirtualną a uaktualnieniem obrazu procedury lub faktem, że dysk w warstwie Premium ma już trzy metody replikacji. Nie mogą oni korzystać z tych korzyści i uzyskiwać korzyści i oszczędności dla klientów.

* **Płacisz za godzinę:** W przypadku obciążeń o dużej skali, które muszą być skalowane w dowolnym momencie, opłaty są naliczane tylko za godzinę. Obciążenia w aplikacji zwykle różnią się w różnych godzinach roku oraz przez dane, które są wysyłane do zapytania. Dzięki Azure Cosmos DB można skalować w górę lub w dół w miarę potrzeb i płacisz tylko za to, czego potrzebujesz. W systemach hostowanych lokalnie lub IaaS nie można dopasować tego modelu, ponieważ nie ma możliwości zlikwidowania sprzętu co godzinę. W takich przypadkach można potencjalnie zaoszczędzić od 10 do 14 razy na średnim Azure Cosmos DB.

* **Bezpłatnie otrzymujesz wiele funkcji:** W Azure Cosmos DB obciążenia zapisu są znacznie tańsze w porównaniu z alternatywnymi usługami baz danych. Ponadto Azure Cosmos DB oferuje funkcje takie jak [Automatyczne indeksowanie](indexing-policies.md), [czas wygaśnięcia (TTL)](time-to-live.md), [Źródło zmian](change-feed.md) i inne osoby bez dodatkowych opłat, co oznacza, że inne usługi bazy danych zazwyczaj naliczają opłaty.

* **Używa ujednoliconej waluty dla różnorodnych obciążeń:** W przeciwieństwie do alternatywnych ofert, w Azure Cosmos DB nie ma potrzeby segmentacji obciążeń, na przykład do odczytu i zapisu. Lub zainicjuj przepływność dla każdego typu obciążenia, który ma przepływność odczytu a przepływność zapisu. W Azure Cosmos DB, zainicjowana przepływność jest zarezerwowana przy użyciu ujednoliconej i znormalizowanej waluty w warunkach jednostek żądań lub RU/s. Azure Cosmos DB nie zmusza do przypisywania priorytetów do obciążeń, wykonywania operacji planowania wydajności lub płacenia dla każdego typu pojemności osobno. Takie podejście pozwala łatwo rozróżnić te same RU/s między różnymi operacjami i typami obciążeń.

* **Nie wymaga, aby maszyny wirtualne aprowizacji były skalowane:** Większość operacyjnych baz danych wymaga pracy z dużymi maszynami wirtualnymi, aby uniknąć zakłócenia i nieluźnego zarządzania zasobami, jeśli chcesz skalować. Powoduje to obciążenie i zobowiązanie z góry kosztu dla klientów. Za pomocą Azure Cosmos DB można bezproblemowo zacząć i zwiększać rozmiar obciążeń o dużej skali bez przestojów ani wpływu na dostępność danych.

* **Zainicjowaną przepływność można wykorzystać do maksymalnego limitu:** Ze względu na podstawowe metody multipleksowania w Azure Cosmos DB można przywyższyć stopień nasycenia przepływności do większej wartości niż w przypadku opcji hostowanych IaaS lub ofert innych firm. Ta metoda zapisuje wiele więcej niż alternatywne rozwiązania.

* **Ścisła integracja Azure Cosmos DB z innymi usługami platformy Azure.** Azure Cosmos DB ma natywną integrację z sieciami, obliczeniami, Azure Functions (bezserwerowe), usługą Azure IoT i innymi usługami platformy Azure. Dzięki tej integracji uzyskasz najlepszą wydajność, szybkość replikacji danych na całym świecie z niezawodnymi gwarancjami. Rozwiązania innych firm nie będą w stanie dopasować lub zwykle naliczać Premium w celu zaoferowania takich funkcji.

* **Automatycznie uzyskujesz wysoką dostępność, domyślnie z co najmniej 10-20 domenami błędów:** Azure Cosmos DB obsługuje dystrybucję obciążeń między domenami błędów, funkcja, która ma kluczowe znaczenie dla wysokiej dostępności. Oferuje 99,999 wysoką dostępność dla operacji odczytu i zapisu w 99 percentylu w dowolnym miejscu na świecie. Koszt wdrożenia tego elementu na własne lub przez rozwiązanie innych firm będzie wysoki.

* **Wszystkie funkcje przedsiębiorstwa są automatycznie dostępne bez dodatkowych kosztów.** Azure Cosmos DB oferuje najbardziej kompleksowy zestaw certyfikatów zgodności, zabezpieczeń i szyfrowania w czasie spoczynku i ruchu bez dodatkowych kosztów (w porównaniu z naszą konkurencją). Dostępność regionalna jest automatycznie dostępna w dowolnym miejscu na świecie. Bazę danych można rozciągnąć na dowolną liczbę regionów świadczenia usługi Azure i dodać lub usunąć regiony w dowolnym momencie.

* **Możesz zaoszczędzić do 65% kosztów z zarezerwowaną pojemnością:** Azure Cosmos DB [zarezerwowana pojemność](cosmos-db-reserved-capacity.md) pomaga zaoszczędzić pieniądze dzięki przedpłaceniu za zasoby Azure Cosmos DB przez jeden rok lub trzy lata. Możesz znacząco obniżyć koszty dzięki rocznym lub trzecim zobowiązaniom z góry, a następnie zaoszczędzić od 20-65% rabatów w porównaniu z regularnymi cenami. W przypadku obciążeń o kluczowym znaczeniu można uzyskać lepszy umowy SLA w zakresie możliwości aprowizacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, [jak Azure Cosmos DB model cenowy jest ekonomiczny dla klientów](total-cost-ownership.md)
* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont Cosmos](optimize-cost-regions.md)
* Dowiedz się więcej o [łącznym koszcie (nie) własności usługi NoSQL Database w chmurze](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
