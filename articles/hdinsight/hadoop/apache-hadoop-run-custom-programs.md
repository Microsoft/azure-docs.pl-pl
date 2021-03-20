---
title: Uruchamianie niestandardowych programów MapReduce — Azure HDInsight
description: Kiedy i jak uruchamiać niestandardowe programy Apache MapReduce w klastrach usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0f7a3676db14dbf61d3976690c8f9c71a660f889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943242"
---
# <a name="run-custom-mapreduce-programs"></a>Uruchamianie niestandardowych programów MapReduce

Oparte na Apache Hadoop systemy danych Big Data, takie jak HDInsight, umożliwiają przetwarzanie danych przy użyciu szerokiej gamy narzędzi i technologii. W poniższej tabeli opisano główne zalety i zagadnienia dotyczące każdego z nich.

| Mechanizm zapytania | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- |
| **Apache Hive przy użyciu HiveQL** | <ul><li>Doskonałe rozwiązanie do przetwarzania wsadowego i analizy dużych ilości niezmiennych danych, podsumowania danych i wykonywania zapytań na żądanie. Używa znanej składni podobnej do języka SQL.</li><li>Może służyć do tworzenia trwałych tabel danych, które można łatwo podzielić na partycje i zindeksować.</li><li>Dla tych samych danych można tworzyć wiele tabel i widoków zewnętrznych.</li><li>Obsługuje ona prostą implementację magazynu danych, która zapewnia ogromne możliwości skalowania i odporności na uszkodzenia na potrzeby przechowywania i przetwarzania danych.</li></ul> | <ul><li>Wymaga, aby dane źródłowe miały co najmniej pewną rozpoznawalną strukturę.</li><li>Nie jest to odpowiednie dla zapytań w czasie rzeczywistym i aktualizacji na poziomie wiersza. Najlepiej jest używać do zadań wsadowych w dużych zestawach danych.</li><li>Może nie być możliwe przeprowadzenie niektórych typów złożonych zadań przetwarzania.</li></ul> |
| **Apache świnie z użyciem wieprzowiny** | <ul><li>Doskonałe rozwiązanie do manipulowania danymi jako zestawy, scalanie i filtrowanie zestawów danych, stosowanie funkcji do rekordów lub grup rekordów oraz na potrzeby restrukturyzacji danych przez definiowanie kolumn, grupowanie wartości lub konwersja kolumn do wierszy.</li><li>Można użyć podejścia opartego na przepływie pracy jako sekwencji operacji na danych.</li></ul> | <ul><li>Użytkownicy SQL mogą znaleźć mniej znane i trudniejsze na korzystanie z HiveQL.</li><li>Domyślnym wyjściem jest zwykle plik tekstowy, więc może być trudniejszy do użycia z narzędziami do wizualizacji, takimi jak program Excel. Zazwyczaj warstwy Hive zostaną przełączone na dane wyjściowe.</li></ul> |
| **Mapa niestandardowa/Zmniejsz** | <ul><li>Zapewnia pełną kontrolę nad mapą i zmniejsza fazy i wykonywanie.</li><li>Pozwala to na zoptymalizowanie zapytań w celu osiągnięcia maksymalnej wydajności z klastra lub zminimalizowania obciążenia serwerów i sieci.</li><li>Składniki mogą być zapisywane w zakresie dobrze znanych języków.</li></ul> | <ul><li>Jest to trudniejsze niż używanie świni lub Hive, ponieważ należy utworzyć własne mapy i zredukować składniki.</li><li>Procesy, które wymagają przyłączenia zestawów danych, są trudniejsze do zaimplementowania.</li><li>Mimo że istnieją platformy testowe dostępne, kod debugowania jest bardziej skomplikowany niż zwykła aplikacja, ponieważ kod jest uruchamiany jako zadanie wsadowe pod kontrolą harmonogramu zadań usługi Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Jest to abstrakcyjne szczegóły ścieżki magazynu, ułatwiając administrację i eliminując konieczność, aby użytkownicy wiedzieli, gdzie są przechowywane dane.</li><li>Umożliwia ona powiadamianie o zdarzeniach, takich jak dostępność danych, pozwalając innym narzędziom, takim jak Oozie, wykrywanie czasu wystąpienia operacji.</li><li>Udostępnia on relacyjny widok danych, w tym partycjonowanie według klucza, i ułatwia dostęp do danych.</li></ul> | <ul><li>Obsługuje format plików RCFile, CSV, tekstu JSON, SequenceFile i ORC, ale może być konieczne napisanie niestandardowego elementu SERDE dla innych formatów.</li><li>HCatalog nie jest bezpieczny wątkowo.</li><li>Istnieją pewne ograniczenia dotyczące typów danych dla kolumn podczas korzystania z modułu ładującego HCatalog w skryptach wieprzowych. Aby uzyskać więcej informacji, zobacz [HCatLoader typy danych](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) w dokumentacji Apache HCatalog.</li></ul> |

Zazwyczaj należy używać najprostszych metod, które mogą zapewnić wymagane wyniki. Na przykład możesz mieć możliwość osiągnięcia takich wyników przy użyciu tylko usługi Hive, ale w przypadku bardziej złożonych scenariuszy może być konieczne użycie świni, a nawet napisać własne mapowanie i zredukować składniki. Możesz również zdecydować, że po eksperymentie z gałęzią lub świnią, że mapa niestandardowa i zmniejszenie składników mogą zapewnić lepszą wydajność, umożliwiając precyzyjne dostosowanie i zoptymalizowanie przetwarzania.

## <a name="custom-mapreduce-components"></a>Mapa niestandardowa/Zmniejsz składniki

Mapowanie/zmniejszanie kodu składa się z dwóch oddzielnych funkcji wdrożonych jako **Mapa** i **Zmniejsz** składniki. Składnik **mapy** jest uruchamiany równolegle na wielu węzłach klastra, a każdy węzeł stosuje mapowanie do własnego podzestawu danych węzła. Składnik **redukcji** sortuje i podsumowuje wyniki ze wszystkich funkcji map. Aby uzyskać więcej informacji na temat tych dwóch składników, zobacz [Korzystanie z MapReduce w usłudze Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).

W większości scenariuszy przetwarzania usługi HDInsight jest to prostsze i bardziej wydajne, aby można było używać abstrakcji wyższego poziomu, takich jak świnie lub Hive. Możesz również utworzyć mapę niestandardową i zredukować składniki do użycia w skryptach Hive w celu przeprowadzenia bardziej zaawansowanego przetwarzania.

Mapa niestandardowa/Zmniejsz składniki są zwykle zapisywane w języku Java. Usługa Hadoop udostępnia interfejs przesyłania strumieniowego, który umożliwia także używanie składników, które są opracowywane w innych językach, takich jak C#, F #, Visual Basic, Python i JavaScript.

* Aby zapoznać się z przewodnikiem dotyczącym tworzenia niestandardowych programów MapReduce języka Java, zobacz [opracowywanie programów Java MapReduce dla platformy Hadoop w usłudze HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Należy rozważyć utworzenie własnej mapy i zmniejszenie składników pod kątem następujących warunków:

* Należy przetwarzać dane, które są całkowicie niestrukturalne, przez analizowanie danych i używanie logiki niestandardowej do uzyskiwania informacji ze strukturą.
* Chcesz wykonywać złożone zadania, które są trudne (lub niemożliwe) do wyrażania w ramach świni lub Hive, bez konieczności tworzenia plików UDF. Na przykład może być konieczne użycie zewnętrznej usługi geokodowania, aby przekonwertować współrzędne geograficzne lub adresy IP w danych źródłowych na nazwy lokalizacji geograficznej.
* Chcesz ponownie użyć istniejącego kodu .NET, Python lub JavaScript w programie map/Zmniejsz składniki przy użyciu interfejsu usługi przesyłania strumieniowego Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Przekazywanie i uruchamianie niestandardowego programu MapReduce

Najpopularniejsze programy MapReduce są zapisywane w języku Java i kompilowane do pliku JAR.

1. Po opracowaniu, skompilowaniu i przetestowaniu programu MapReduce Użyj polecenia, `scp` Aby przekazać plik jar do węzła głównego.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zastąp wartość CLUSTERname nazwą klastra. Jeśli do zabezpieczenia konta SSH użyto hasła, zostanie wyświetlony monit o wprowadzenie hasła. Jeśli użyto certyfikatu, może być konieczne użycie `-i` parametru w celu określenia pliku klucza prywatnego.

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH wykonaj swój program MapReduce za pośrednictwem PRZĘDZy.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    To polecenie przesyła zadanie MapReduce do PRZĘDZenia. Plik wejściowy jest `/example/data/sample.log` i katalog wyjściowy `/example/data/logoutput` . Plik wejściowy i wszystkie pliki wyjściowe są przechowywane w domyślnym magazynie klastra.

## <a name="next-steps"></a>Następne kroki

* [Używanie języka C# z MapReduce przesyłania strumieniowego na Apache Hadoop w usłudze HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Używanie funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej w usłudze HDInsight](python-udf-hdinsight.md)
