---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f6bd0c13d5cbad802613e2bdea8fd6002f4deea2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445631"
---
Centrum zdarzeń organizuje sekwencje zdarzeń na co najmniej jedną partycję. Po nadejściu nowszych zdarzeń są one dodawane na końcu tej sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

Partycje przechowują dane zdarzeń zawierające treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości opisujący zdarzenie oraz metadane, takie jak przesunięcie w partycji, jego numer w sekwencji strumienia oraz sygnatura czasowa po stronie usługi, przy której została zaakceptowana.

![Diagram przedstawiający starszą i nowszą sekwencję zdarzeń.](./media/event-hubs-partitions/partition.png)

Event Hubs jest zaprojektowana w celu ułatwienia przetwarzania dużych ilości zdarzeń, a partycjonowanie ułatwia to na dwa sposoby:

Po pierwsze, mimo że Event Hubs jest usługą PaaS, istnieje fizyczna rzeczywistość poniżej i konserwowanie dziennika, który zachowuje porządek zdarzeń, wymaga, aby te zdarzenia były przechowywane razem w magazynie bazowym i jego replikach, co powoduje górny limit przepływności dla tego dziennika. Partycjonowanie umożliwia używanie wielu dzienników równoległych dla tego samego centrum zdarzeń, co w związku z tym mnoży dostępną pierwotną przepływność we/wy.

Po drugie, własne aplikacje muszą być w stanie kontynuować przetwarzanie ilości zdarzeń wysyłanych do centrum zdarzeń. Może to być złożone i wymaga znacznej, skalowanej pojemności przetwarzania równoległego. Uzasadnienie dla partycji jest takie samo jak powyżej: pojemność pojedynczego procesu obsługującego zdarzenia jest ograniczona, w związku z czym potrzebne jest kilka procesów, a partycje to sposób, w jaki Twoje rozwiązanie pobiera strumieniowo te procesy, a mimo to zapewnia, że każde zdarzenie ma zrozumiały właściciel przetwarzania. 

Event Hubs zachowuje zdarzenia dla skonfigurowanego czasu przechowywania stosowanego dla wszystkich partycji. Zdarzenia są usuwane automatycznie po osiągnięciu okresu przechowywania. Jeśli określisz okres przechowywania o jeden dzień, zdarzenie stanie się niedostępne dokładnie przez 24 godziny od momentu jego zaakceptowania. Nie można jawnie usunąć zdarzeń. 

Dozwolony czas przechowywania wynosi do 7 dni dla Event Hubs Standard i do 90 dni dla Event Hubs — warstwa Dedykowana. Jeśli musisz zarchiwizować zdarzenia poza dozwolonym okresem przechowywania, możesz je [automatycznie przechowywać w usłudze Azure Storage lub Azure Data Lake, włączając funkcję przechwytywania Event Hubs](../articles/event-hubs/event-hubs-capture-overview.md), a jeśli chcesz przeszukiwać lub analizować takie głębokie archiwa, możesz je [łatwo zaimportować do usługi Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) lub innych podobnych magazynów i platform analitycznych. 

Powód ograniczenia Event Hubs "przechowywania danych na podstawie czasu polega na tym, że duże ilości danych historycznych klientów są zalewkowane w głębokiej sklepie, która jest indeksowana tylko przez sygnaturę czasową i zezwala na dostęp sekwencyjny. W tym miejscu jest to, że historyczne dane wymagają bardziej zaawansowanego indeksowania i więcej bezpośredniego dostępu niż interfejs obsługi zdarzeń w czasie rzeczywistym, który Event Hubs lub Kafka. Aparaty strumienia zdarzeń nie są dobrze dopasowane do odtwarzania roli jezior danych lub archiwów długoterminowych na potrzeby określania źródła zdarzeń. 

Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością. W Event Hubs, które nie są związane z interwencją administracyjną, tak jak na przykład w Apache Kafka, ale nieparzysty rozkład będzie prowadzić do nierównego obciążenia procesorów zdarzeń podrzędnych.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Liczba partycji jest określana podczas tworzenia i musi zawierać się w przedziale od 1 do 32 w standardzie Event Hubs. Liczba partycji może należeć do 2000 partycji na jednostkę pojemności w Event Hubs — warstwa Dedykowana. 

Zalecamy wybranie co najmniej tylu partycji, które powinny być wymagane w przypadku trwałych [jednostek przepływności (jednostek przepływności)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) podczas szczytowego ładowania aplikacji dla danego centrum zdarzeń. Należy obliczyć z jedną partycją o pojemności 1 jednostek PRZEPŁYWNOŚCI (1 MB w, 2 MB na zewnątrz). Możesz skalować TUs w przestrzeni nazw lub jednostki pojemności klastra niezależnie od liczby partycji. Centrum zdarzeń z partycjami 32 lub centrum zdarzeń z 1 partycją ponosi dokładnie ten sam koszt, gdy przestrzeń nazw jest ustawiona na 1 jednostek PRZEPŁYWNOŚCI pojemność. 

Aplikacje kontrolują mapowanie zdarzeń na partycje na jeden z trzech sposobów:

- Przez określenie klucza partycji, który jest stale mapowany (przy użyciu funkcji skrótu) do jednej z dostępnych partycji. 
- Nie określając klucza partycji, który umożliwia brokerowi losowe Wybieranie partycji dla danego zdarzenia.
- Jawnie wysyłając zdarzenia do określonej partycji.

Określenie klucza partycji umożliwia utrzymywanie powiązanych zdarzeń w tej samej partycji i w kolejności, w jakiej zostały wysłane. Klucz partycji to jakiś ciąg pochodzący z kontekstu aplikacji i identyfikujący wzajemne relacje zdarzeń.

Sekwencja zdarzeń identyfikowanych przez klucz partycji jest *strumieniem*. Partycja to multipleksowy magazyn dzienników dla wielu takich strumieni. 

Liczba partycji centrum zdarzeń w [dedykowanym klastrze Event Hubs](../articles/event-hubs/event-hubs-dedicated-overview.md) można [zwiększyć](../articles/event-hubs/dynamically-add-partitions.md) po utworzeniu centrum zdarzeń, ale Rozkład strumieni między partycjami ulegnie zmianie, gdy zostanie on zmieniony, gdy zostanie zmienione mapowanie kluczy partycji na partycje, dlatego należy spróbować trudno uniknąć takich zmian, jeśli względna kolejność zdarzeń w aplikacji.

Ustawienie liczby partycji na maksymalną dozwoloną wartość jest uciążliwe, ale zawsze należy pamiętać, że strumienie zdarzeń muszą być strukturalne, aby można było korzystać z wielu partycji. Jeśli potrzebujesz bezwzględnie zamówionej kolejności dla wszystkich zdarzeń lub tylko kilku podstrumienia, możesz nie być w stanie korzystać z wielu partycji. Ponadto wiele partycji sprawia, że przetwarzanie jest bardziej skomplikowane. 

Mimo że partycje mogą być wysyłane bezpośrednio, nie jest to zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu wprowadzonych w sekcji [wydawcy zdarzeń](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

