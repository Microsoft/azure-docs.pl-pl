---
title: 'Dostrajanie wydajności: burza, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Poznaj wskazówki dotyczące dostrajania wydajności topologii burzy Azure w klastrze usługi Azure HDInsight i Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912794"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: burza, & usługi HDInsight Azure Data Lake Storage Gen2

Informacje o czynnikach, które należy wziąć pod uwagę podczas dostrajania wydajności topologii burzowej platformy Azure. Na przykład ważne jest zapoznanie się z charakterystyką pracy wykonywanej przez elementy Spout i Piorunów (czy liczba operacji we/wy lub intensywnym wykorzystaniu pamięci). W tym artykule opisano zakres wytycznych dotyczących dostrajania wydajności, w tym Rozwiązywanie typowych problemów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu na potrzeby analizy analitycznej](../common/storage-account-create.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Uruchamianie klastra burzy w Data Lake Storage Gen2**. Aby uzyskać więcej informacji, zobacz [burza w usłudze HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Dostrajanie równoległości topologii

Może być możliwe zwiększenie wydajności przez zwiększenie współbieżności operacji we/wy do i z Data Lake Storage Gen2. Topologia burzy ma zestaw konfiguracji, które określają równoległość:
* Liczba procesów roboczych (procesy robocze są równomiernie rozłożone na maszyny wirtualne).
* Liczba wystąpień programu wykonującego elementu Spout.
* Liczba wystąpień programu wykonującego.
* Liczba zadań elementu Spout.
* Liczba zadań obiektu piorun.

Na przykład w klastrze z 4 maszynami wirtualnymi i 4 procesami roboczymi 32, elementu Spout wykonawcy i 32 elementu Spout zadania i 256ją zadania 512 i elementy wykonujące testy, należy wziąć pod uwagę następujące kwestie:

Każdy kierownik, który jest węzłem procesu roboczego, ma jeden proces roboczy maszyny wirtualnej Java (JVM). Ten proces JVM służy do zarządzania 4 wątkami elementu Spout i 64 wątków. W każdym wątku zadania są uruchamiane sekwencyjnie. W powyższej konfiguracji każdy wątek elementu Spout ma 1 zadanie, a każdy wątek pioruna ma 2 zadania.

W obszarze burzy są dostępne różne składniki oraz ich wpływ na poziom równoległości:
* Węzeł główny (o nazwie Nimbus w obszarze burza) służy do przesyłania zadań i zarządzania nimi. Te węzły nie mają wpływu na stopień równoległości.
* Węzły nadzoru. W usłudze HDInsight odnosi się to do maszyny wirtualnej platformy Azure z węzłem roboczym.
* Zadania procesu roboczego są procesami burzy uruchomionymi na maszynach wirtualnych. Każde zadanie procesu roboczego odpowiada wystąpieniu JVM. Burza dystrybuuje liczbę procesów roboczych określonych dla węzłów procesu roboczego tak, jak to możliwe.
* Wystąpienia modułów wykonujących elementu Spout i piorunów. Każde wystąpienie programu wykonującego odpowiada wątkowi uruchomionemu w ramach procesów roboczych (JVMs).
* Zadania burzowe. Są to zadania logiczne wykonywane przez każdy z tych wątków. Nie powoduje to zmiany poziomu równoległości, dlatego należy oszacować, czy potrzebujesz wielu zadań na wykonawcę.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Uzyskaj najlepszą wydajność z Data Lake Storage Gen2

Podczas pracy z Data Lake Storage Gen2 można uzyskać najlepszą wydajność, jeśli wykonasz następujące czynności:
* Łączenie małych dołączeń w większe rozmiary.
* Wykonaj dowolną liczbę równoczesnych żądań. Ponieważ każdy wątek pioruna wykonuje odczyty blokujące, warto umieścić w zakresie 8-12 wątków na rdzeń. Dzięki temu karta sieciowa i użycie procesora CPU są ciągle wykorzystywane. Większa maszyna wirtualna umożliwia wykonywanie większej liczby równoczesnych żądań.  

### <a name="example-topology"></a>Przykładowa topologia

Załóżmy, że masz 8-węzłowy klaster roboczy z maszyną wirtualną platformy Azure D13v2. Ta maszyna wirtualna ma 8 rdzeni, więc między 8 węzłami roboczymi jest 64 całkowitej liczby rdzeni.

Załóżmy, że 8 wątków piorunów na rdzeń. Podano 64 rdzeni, co oznacza, że chcemy 512 łączne wystąpienia modułu wykonującego (czyli wątki). W tym przypadku Załóżmy, że zaczynamy od jednego JVM na maszynę wirtualną, a głównie używają współbieżności wątków w JVM, aby osiągnąć współbieżność. Oznacza to, że potrzebujemy 8 zadań roboczych (jednej na maszynę wirtualną na platformie Azure) i modułów wykonujących 512. Zgodnie z tą konfiguracją, burza próbuje równomiernie rozpowszechnić pracowników w węzłach procesu roboczego (nazywanych również węzłami nadzorującymi), dając każdy węzeł procesu roboczego 1 JVM. Teraz w ramach nadzorcy, burza próbuje rozpowszechnić wykonawców jednocześnie między osobami nadzorującymi, zapewniając każdemu opiekunowi (czyli JVM) 8 wątków każdego z nich.

## <a name="tune-additional-parameters"></a>Dostosuj dodatkowe parametry
Po utworzeniu topologii podstawowej można rozważyć, czy chcesz dostosować dowolny z parametrów:
* **Liczba JVMs na węzeł procesu roboczego.** Jeśli masz dużą strukturę danych (na przykład tabelę odnośników), która jest hostowana w pamięci, każda JVM wymaga oddzielnej kopii. Alternatywnie możesz użyć struktury danych w wielu wątkach, jeśli masz mniej JVMs. W przypadku operacji we/wy pioruna liczba JVMs nie jest tak duża jak liczba wątków dodanych w ramach tych JVMs. Dla uproszczenia dobrym pomysłem jest posiadanie jednego JVM na proces roboczy. W zależności od tego, co działa lub jakie jest wymagane przetwarzanie aplikacji, może być konieczna zmiana tej liczby.
* **Liczba modułów wykonujących elementu Spout.** Ponieważ w powyższym przykładzie są wykorzystywane pioruny do zapisu w Data Lake Storage Gen2, liczba elementy Spout nie jest bezpośrednio istotna dla wydajności obiektu. Jednakże, w zależności od ilości przetwarzania lub operacji we/wy w elementu Spout, dobrym pomysłem jest dostrojenie elementy Spout w celu uzyskania najlepszej wydajności. Upewnij się, że masz wystarczająco dużo elementy Spout, aby zapewnić, że pioruny są zajęte. Stawki danych wyjściowych elementy Spout powinny odpowiadać przepływności piorunów. Rzeczywista konfiguracja zależy od elementu Spout.
* **Liczba zadań.** Każdy obiekt piorun działa jako pojedynczy wątek. Dodatkowe zadania na piorun nie zapewniają żadnych dodatkowych współbieżności. Jedyną zaletą jest to, że proces potwierdzania spójnej kolekcji zajmuje dużą część czasu wykonywania obiektu. Dobrym pomysłem jest Grupowanie wielu spójnych kolekcji przed wysłaniem potwierdzenia z pioruna. Dlatego w większości przypadków wiele zadań nie zapewnia dodatkowej korzyści.
* **Grupowanie lokalne lub losowe.** Gdy to ustawienie jest włączone, krotki są wysyłane do piorunów w ramach tego samego procesu roboczego. Powoduje to zmniejszenie komunikacji między procesami i połączenia sieciowe. Jest to zalecane w przypadku większości topologii.

Ten podstawowy scenariusz jest dobrym punktem początkowym. Przetestuj swoje własne dane, aby dostosować poprzednie parametry w celu uzyskania optymalnej wydajności.

## <a name="tune-the-spout"></a>Dostosuj elementu Spout

Możesz zmodyfikować następujące ustawienia, aby dostroić elementu Spout.

- **Limit czasu krotki: Topology. Message. Timeout. s**. To ustawienie określa czas, przez jaki komunikat jest potrzebny do ukończenia i otrzymuje potwierdzenie, zanim zostanie uznane za niepowodzenie.

- **Maksymalna ilość pamięci na proces roboczy: Worker. childopts**. To ustawienie umożliwia określenie dodatkowych parametrów wiersza polecenia dla procesów roboczych języka Java. Najczęściej używane ustawienie to XmX, które określa maksymalną ilość pamięci przydzieloną do sterty JVM.

- **Maksymalna liczba elementu Spout oczekujących: Topology. max. elementu Spout. Pending**. To ustawienie określa liczbę spójnych krotek (nie jest jeszcze potwierdzone we wszystkich węzłach w topologii) na wątek elementu Spout w dowolnym momencie.

  Dobrym obliczeniem jest oszacowanie rozmiaru poszczególnych krotek. Następnie ustal, ile pamięci ma jeden wątek elementu Spout. Łączna ilość pamięci przydzielonej do wątku, podzielona przez tę wartość, powinna stanowić górną granicę dla maksymalnego parametru oczekującego elementu Spout.

Domyślny Data Lake Storage Gen2 burzy, który ma parametr zasad synchronizacji rozmiaru (fileBufferSize), który może służyć do dostrajania tego parametru.

W topologiach intensywnie korzystających z operacji we/wy dobrym pomysłem jest zapisanie każdego wątku piorunu do własnego pliku oraz ustawianie zasad rotacji plików (fileRotationSize). Gdy plik osiągnie określony rozmiar, strumień jest automatycznie opróżniany i nowy plik jest zapisywana w. Zalecany rozmiar pliku do rotacji to 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorowanie topologii w burzach  
Gdy topologia jest uruchomiona, można monitorować ją w interfejsie użytkownika burzy. Oto główne parametry do przeszukiwania:

* **Całkowite opóźnienie wykonania procesu.** Jest to średni czas, przez który jedna krotka będzie emitować przez elementu Spout, przetworzona przez pioruna i potwierdzona.

* **Całkowite opóźnienie procesu pioruna.** Jest to średni czas spędzony przez krotkę w piorunze do momentu otrzymania potwierdzenia.

* **Całkowite opóźnienie wykonania pioruna.** Jest to średni czas spędzony przez piorun w metodzie Execute.

* **Liczba niepowodzeń.** Odnosi się to do liczby krotek, które nie zostały w pełni przetworzone przed upływem limitu czasu.

* **Pojemności.** Jest to miara, w jaki sposób jest zajęty system. Jeśli ta liczba wynosi 1, pioruny pracują tak szybko, jak to możliwe. Jeśli wartość jest mniejsza niż 1, zwiększ równoległość. Jeśli wartość jest większa niż 1, Zmniejsz równoległość.

## <a name="troubleshoot-common-problems"></a>Rozwiązywanie typowych problemów
Poniżej przedstawiono kilka typowych scenariuszy rozwiązywania problemów.
* **Wiele krotek ma limit czasu.** Zapoznaj się z każdym węzłem w topologii, aby określić, gdzie wąskie gardło. Najbardziej typową przyczyną jest to, że pioruny nie mogą utrzymywać się w elementy Spout. Prowadzi to do krotek clogging bufory wewnętrzne podczas oczekiwania na przetworzenie. Należy rozważyć zwiększenie wartości limitu czasu lub zmniejszenie maksymalnej liczby oczekujących elementu Spout.

* **Istnieje wysokie całkowite opóźnienie wykonania procesu, ale opóźnienie niskiego procesu.** W takim przypadku istnieje możliwość, że krotki nie są potwierdzane wystarczająco szybko. Sprawdź, czy istnieje wystarczająca liczba potwierdzeń. Kolejną możliwością jest to, że oczekuje w kolejce przez zbyt długo przed rozpoczęciem przetwarzania przez piorunów. Zmniejsz maksymalną liczbę oczekujących elementu Spout.

* **Istnieje duże opóźnienie wykonania.** Oznacza to, że metoda Execute () obiektu piorun trwa zbyt długo. Zoptymalizuj kod lub zapoznaj się z rozmiarem zapisu i zachowaniem opróżniania.

### <a name="data-lake-storage-gen2-throttling"></a>Ograniczanie Data Lake Storage Gen2
W przypadku osiągnięcia limitów przepustowości zapewnianej przez Data Lake Storage Gen2 mogą pojawić się błędy zadań. Sprawdź dzienniki zadań pod kątem błędów ograniczania. Równoległość można zmniejszyć przez zwiększenie rozmiaru kontenera.    

Aby sprawdzić, czy masz ograniczone ograniczenia, Włącz rejestrowanie debugowania po stronie klienta:

1. W obszarze **Ambari**  >  **burzy**  >  **config**  >  **Advanced burzy-Worker-Log4J**, Zmień **&lt; poziom główny = &gt; "info"** na **&lt; root Level = &gt; "debug"**. Uruchom ponownie wszystkie węzły/usługi, aby konfiguracja zaczęła obowiązywać.
2. Monitoruj dzienniki topologii burzy w węzłach procesu roboczego (w obszarze/var/log/Storm/Worker-Artifacts/ &lt; topologyname &gt; / &lt; port &gt; /Worker.log), aby uzyskać Data Lake Storage Gen2 wyjątków ograniczania.

## <a name="next-steps"></a>Następne kroki
W [tym blogu](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)można odwoływać się do dodatkowej dostrajania wydajności dla burzy.

Aby zapoznać się z dodatkowym przykładem, zobacz [ten temat w witrynie GitHub](https://github.com/hdinsight/storm-performance-automation).