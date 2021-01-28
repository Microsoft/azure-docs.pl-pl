---
title: Głębokie szczegółowe i najlepsze praktyki dotyczące bramy dla Apache Hive w usłudze Azure HDInsight
description: Dowiedz się, jak nawigować po najlepszych rozwiązaniach dotyczących uruchamiania zapytań programu Hive za pośrednictwem bramy usługi Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 63484d882d8ccd387257c6f246c2048a09c77bc8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933117"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Głębokie szczegółowe i najlepsze praktyki dotyczące bramy dla Apache Hive w usłudze Azure HDInsight

Brama usługi Azure HDInsight (brama) to fronton protokołu HTTPS dla klastrów usługi HDInsight. Brama jest odpowiedzialna za: uwierzytelnianie, rozpoznawanie hosta, odnajdowanie usług i inne przydatne funkcje niezbędne do nowoczesnego systemu rozproszonego. Funkcje świadczone przez bramę powodują pewne obciążenie, dla których ten dokument zawiera opis najlepszych rozwiązań do przeprowadzenia nawigacji. Omówiono także techniki rozwiązywania problemów z bramą.

## <a name="the-hdinsight-gateway"></a>Brama usługi HDInsight

Brama usługi HDInsight jest jedyną częścią klastra usługi HDInsight, który jest publicznie dostępny za pośrednictwem Internetu. Dostęp do usługi bramy można uzyskać bez przechodzenia do publicznej sieci Internet przy użyciu `clustername-int.azurehdinsight.net` wewnętrznego punktu końcowego bramy. Wewnętrzny punkt końcowy bramy umożliwia nawiązywanie połączeń z usługą bramy bez opuszczania sieci wirtualnej klastra. Brama obsługuje wszystkie żądania wysyłane do klastra i przekazuje takie żądania do prawidłowych składników i hostów klastra.

Poniższy diagram zawiera przybliżoną ilustrację sposobu, w jaki brama zapewnia streszczenie przed wszystkimi różnymi możliwościami rozpoznawania hosta w usłudze HDInsight.

![Diagram rozpoznawania hosta](./media/gateway-best-practices/host-resolution-diagram.png "Diagram rozpoznawania hosta")

## <a name="motivation"></a>Motywacja

Przed umieszczeniem bramy przed klastrami usługi HDInsight jest zapewnienie interfejsu do odnajdowania usług i uwierzytelniania użytkowników. Mechanizmy uwierzytelniania udostępniane przez bramę są szczególnie przydatne w przypadku klastrów obsługujących ESP.

W przypadku odnajdywania usług korzystanie z bramy polega na tym, że każdy składnik w klastrze jest dostępny jako inny punkt końcowy w ramach witryny sieci Web bramy ( `clustername.azurehdinsight.net/hive2` ), a nie wiele `host:port` par.

W przypadku uwierzytelniania Brama zezwala użytkownikom na uwierzytelnianie za pomocą `username:password` pary poświadczeń. W przypadku klastrów obsługujących ESP to poświadczenie będzie nazwą użytkownika domeny i hasłem. Uwierzytelnianie klastrów usługi HDInsight za pośrednictwem bramy nie wymaga, aby klient uzyskał bilet protokołu Kerberos. Ponieważ Brama akceptuje `username:password` poświadczenia i uzyskuje bilet protokołu Kerberos użytkownika w imieniu użytkownika, można nawiązać bezpieczne połączenia z bramą z dowolnego hosta klienta, w tym klientów przyłączonych do różnych domen AA-DDS niż klaster (ESP).

## <a name="best-practices"></a>Najlepsze rozwiązania

Brama to pojedyncza usługa (równoważna obciążenie na dwóch hostach) odpowiedzialna za przekazywanie żądań i uwierzytelnianie. Brama może stać się wąskim gardła przepływności dla zapytań Hive przekraczających określony rozmiar. Pogorszenie wydajności zapytania może być zaobserwowane, gdy bardzo duże zapytania **SELECT** są wykonywane na bramie za pośrednictwem ODBC lub JDBC. "Bardzo duże" oznacza zapytania, które składają się z więcej niż 5 GB danych w wierszach lub kolumnach. To zapytanie może zawierać długą listę wierszy i lub dużą liczbę kolumn.

Spadek wydajności bramy dla kwerend o dużym rozmiarze wynika z faktu, że dane muszą być transferowane z bazowego magazynu danych (ADLS Gen2) do: serwera Hive programu HDInsight, bramy i na koniec za pośrednictwem sterowników JDBC lub ODBC na hoście klienta.

Na poniższym diagramie przedstawiono kroki, które są uwzględnione w zapytaniu SELECT.

![Diagram wyników](./media/gateway-best-practices/result-retrieval-diagram.png "Diagram wyników")

Apache Hive to relacyjna Abstrakcja na bazie systemu plików zgodnej z systemem HDFS. Streszczenie oznacza, że instrukcje **SELECT** w gałęziach odpowiadają operacjom **odczytu** w systemie plików. Operacje **odczytu** są tłumaczone na odpowiedni schemat przed wysłaniem go do użytkownika. Opóźnienie tego procesu zwiększa się wraz z rozmiarem danych i łącznym przeskokami wymaganym do uzyskania dostępu do użytkownika końcowego.

Podobne zachowanie może wystąpić podczas wykonywania instrukcji **Create** lub **INSERT** w przypadku dużych ilości danych, ponieważ te polecenia będą odpowiadać na operacje **zapisu** w podstawowym systemie plików. Rozważ zapisanie danych, takich jak RAW ORC, do systemu plików/datalake zamiast ładowania go przy użyciu funkcji **INSERT** lub **Load**.

W klastrach z włączonym pakietem zabezpieczeń przedsiębiorstwa wystarczająco złożone zasady Apache Ranger mogą powodować spowolnienie w czasie kompilacji kwerendy, co może prowadzić do przekroczenia limitu czasu bramy. Jeśli w klastrze ESP zauważono limit czasu bramy, należy rozważyć zmniejszenie lub połączenie liczby zasad rangerymi.

## <a name="troubleshooting-techniques"></a>Techniki rozwiązywania problemów

Istnieje wiele miejsc, aby ograniczyć i zrozumieć problemy z wydajnością, które zostały spełnione w ramach powyższego zachowania. Użyj poniższej listy kontrolnej, gdy wystąpi spadek wydajności zapytania w ramach bramy usługi HDInsight:

* Użyj klauzuli **Limit** podczas wykonywania dużych zapytań **SELECT** . Klauzula **Limit** zmniejsza łączną liczbę wierszy raportowanych do hosta klienta. Klauzula **Limit** wpływa tylko na generowanie wyników i nie zmienia planu zapytania. Aby zastosować klauzulę **limitu** do planu zapytania, użyj konfiguracji `hive.limit.optimize.enable` . **Limit** może być połączony z przesunięciem przy użyciu argumentu w postaci **limitu x, y**.

* Nazwij interesujące Cię kolumny podczas uruchamiania **wybranych** zapytań zamiast używania znaku **SELECT \** _. Wybranie mniejszej liczby kolumn spowoduje zmniejszenie ilości odczytanych danych.

_ Spróbuj uruchomić zapytanie o zainteresowanie za pomocą platformy Apache Z usługi Beeline. Jeśli pobieranie wyników za pośrednictwem platformy Apache Z usługi Beeline trwa dłuższy czas, oczekiwanie na opóźnienia podczas pobierania tych samych wyników za pośrednictwem zewnętrznych narzędzi.

* Przetestuj podstawowe zapytanie programu Hive, aby upewnić się, że można nawiązać połączenie z bramą usługi HDInsight. Spróbuj uruchomić podstawowe zapytanie z poziomu dwóch lub więcej zewnętrznych narzędzi, aby upewnić się, że żadne narzędzie nie działa w problemach.

* Przejrzyj wszystkie alerty Ambari Apache, aby upewnić się, że usługi HDInsight są w dobrej kondycji. Alerty Ambari można zintegrować z Azure Monitor na potrzeby raportowania i analizy.

* Jeśli używasz zewnętrznego magazynu metadanych Hive, sprawdź, czy nie osiągnął limitu liczby jednostek DTU usługi Azure SQL DB dla magazynu metadanych Hive. Jeśli wartość DTU zbliża się do limitu, należy zwiększyć rozmiar bazy danych.

* Upewnij się, że wszystkie narzędzia innych firm, takie jak PBI lub Tableau, używają stronicowania do wyświetlania tabel lub baz danych. Zapoznaj się z partnerami pomocy technicznej, aby uzyskać pomoc dotyczącą dzielenia na strony. Głównym narzędziem używanym do stronicowania jest `fetchSize` parametr JDBC. Niewielki rozmiar pobierania może spowodować obniżenie wydajności bramy, ale rozmiar pobieranych za duży może spowodować przekroczenie limitu czasu bramy. Dostrajanie rozmiaru pobierania musi być wykonywane na podstawie obciążenia.

* Jeśli potok danych obejmuje odczytywanie dużej ilości danych z magazynu bazowego klastra usługi HDInsight, należy rozważyć użycie narzędzia bezpośrednio powiązanego z magazynem platformy Azure, takiego jak Azure Data Factory

* Rozważ użycie Apache Hive LLAP podczas uruchamiania interakcyjnych obciążeń, ponieważ LLAP może zapewnić płynny komfort szybkiego zwracania wyników zapytania

* Rozważ zwiększenie liczby wątków dostępnych dla usługi magazynu metadanych Hive przy użyciu programu `hive.server2.thrift.max.worker.threads` . To ustawienie jest szczególnie przydatne, gdy duża liczba równoczesnych użytkowników przesyła zapytania do klastra

* Zmniejsz liczbę ponownych prób używanych do uzyskania dostępu do bramy z dowolnego narzędzia zewnętrznego. W przypadku użycia wielu ponownych prób należy rozważyć zastosowanie zasad ponawiania wycofywania

* Rozważ włączenie gałęzi kompresji przy użyciu konfiguracji `hive.exec.compress.output` i `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Następne kroki

* [Apache Z usługi Beeline w usłudze HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Kroki rozwiązywania problemów z limitem czasu bramy usługi HDInsight](./troubleshoot-gateway-timeout.md)
* [Sieci wirtualne dla usługi HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight z usługą Express Route](../connect-on-premises-network.md)