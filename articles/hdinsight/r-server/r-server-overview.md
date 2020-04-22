---
title: Wprowadzenie do usług ML w usłudze Azure HDInsight
description: Dowiedz się, jak używać usług ML w programie HDInsight do tworzenia aplikacji do analizy dużych zbiorów danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 76fcdb52df88be2c4033140f4bc71b28424d7f38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687797"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co to są usługi ML w usłudze Azure HDInsight

Program Microsoft Machine Learning Server jest dostępny jako opcja wdrażania podczas tworzenia klastrów usługi HDInsight na platformie Azure. Typ klastra, który udostępnia tę opcję, nosi nazwę **USŁUGI ML**. Ta funkcja zapewnia dostęp na żądanie do elastycznych, rozproszonych metod analizy w programie HDInsight.

Usługi ML w programie HDInsight zapewniają najnowsze możliwości analizy opartej na r. zestawów danych o praktycznie dowolnym rozmiarze. Zestawy danych można załadować do magazynu obiektów Blob platformy Azure lub usługi Data Lake. Aplikacje oparte na języku R mogą korzystać z ponad 8000 pakietów języka R typu open source. Procedury w ScaleR, pakiet analizy dużych zbiorów danych firmy Microsoft są również dostępne.

Węzeł krawędzi zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. Węzeł krawędzi umożliwia uruchamianie funkcji rozproszonych z równoległym scalerem między rdzeniami serwera. Można również uruchomić je w węzłach klastra przy użyciu ScaleR's Hadoop Map Reduce. Można również użyć kontekstów obliczeniowych Apache Spark.

Modele lub prognozy, które wynikają z analizy można pobrać do użytku lokalnego. Mogą one `operationalized` również być w innym miejscu na platformie Azure. W szczególności za pośrednictwem [usługi Azure Machine Learning Studio (klasyczna)](https://studio.azureml.net)i usługi sieci [web.](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Wprowadzenie do usług ML w u klienta HDInsight

Aby utworzyć klaster usług ML w umiaśnie HDInsight, wybierz typ klastra **usług ML.** Typ klastra usług ML zawiera serwer ML w węzłach danych i węzeł krawędzi. Węzeł krawędzi służy jako strefa docelowa dla analizy opartej na usługach ML. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure,](../hdinsight-hadoop-create-linux-clusters-portal.md) aby uzyskać instrukcje dotyczące tworzenia klastra.

## <a name="why-choose-ml-services-in-hdinsight"></a>Dlaczego warto wybrać usługi ML w HDInsight?

Usługi ML w hdinsight zapewnia następujące korzyści:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innowacje w zakresie si firmy Microsoft i open source

  Usługi ML obejmują wysoce elastyczny, rozproszony zestaw algorytmów, takich jak [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)i [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Algorytmy te mogą pracować na rozmiary danych większe niż rozmiar pamięci fizycznej. Działają również na różnych platformach w sposób rozproszony. Dowiedz się więcej o kolekcji niestandardowych [pakietów języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) firmy Microsoft i [pakietów Pythona](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dołączonych do produktu.
  
  Usługi ML łączy te innowacje i wkład firmy Microsoft pochodzące ze społeczności open source (R, Python i zestawy narzędzi AI). Wszystko na jednej platformie klasy korporacyjnej. Każdy pakiet uczenia maszynowego typu open source typu R lub Python może współpracować ze swoimi zastrzeżonymi innowacjami firmy Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Prosta, bezpieczna i zakrojona na szeroką skalę operacjonalizacja i administracja

  Przedsiębiorstwa opierające się na tradycyjnych paradygmatach i środowiskach inwestują dużo czasu i wysiłku w kierunku operationalization. Ta akcja powoduje zawyżone koszty i opóźnienia, w tym czas tłumaczenia dla: modeli, iteracji, aby zachować ich prawidłowość i aktualność, zatwierdzenie regulacyjne i zarządzanie uprawnieniami.

  ML Services oferuje operacyjność klasy [korporacyjnej.](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) Po zakończeniu modelu uczenia maszynowego trwa tylko kilka kliknięć, aby wygenerować interfejsy API usług sieci web. Te [usługi sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) są hostowane w siatce serwera w chmurze i mogą być zintegrowane z aplikacjami biznesowymi. Możliwość wdrażania w elastycznej siatce umożliwia bezproblemowe skalowanie zgodnie z potrzebami firmy, zarówno w przypadku oceniania wsadowego, jak i w czasie rzeczywistym. Aby uzyskać instrukcje, zobacz [Operationalize Usługi ML w programie HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ klastra usług ML w programie HDInsight jest obsługiwany tylko w programie HDInsight 3.6. HDInsight 3.6 ma przejść na emeryturę 31 grudnia 2020 roku.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Najważniejsze cechy usług ML w programie HDInsight

Następujące funkcje są zawarte w usługi ML na HDInsight.

| Kategoria funkcji | Opis |
|------------------|-------------|
| Funkcja R | [Pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) dla rozwiązań napisanych w języku R, z dystrybucją języka R typu open source i infrastrukturą wykonawczą do wykonywania skryptów. |
| Python z włączoną obsługą | [Moduły Języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dla rozwiązań napisanych w języku Python, z dystrybucją języka Python typu open source i infrastrukturą wykonawczą do wykonywania skryptów.
| [Wstępnie wyszkolone modele](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Do analizy wizualnej i analizy tonacji tekstu, gotowy do uzyskania danych, które podasz. |
| [Wdrażanie i korzystanie z](r-server-operationalize.md) | `Operationalize`serwera i wdrażaj rozwiązania jako usługę sieci web. |
| [Zdalne wykonanie](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Rozpocznij sesje zdalne w klastrze usług ML w sieci ze stacji roboczej klienta. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opcje przechowywania danych dla usług ML w programie HDInsight

Domyślnym magazynem dla systemu plików HDFS może być konto usługi Azure Storage lub usługa Azure Data Lake Storage. Przekazywane dane do magazynu klastra podczas analizy są trwałe. Dane są dostępne nawet po usunięciu klastra. Różne narzędzia mogą obsługiwać transfer danych do magazynu. Narzędzia obejmują oparty na portalu obiekt przesyłania konta magazynu i narzędzie AzCopy.

Można włączyć dostęp do dodatkowych magazynów obiektów Blob i Data lake podczas tworzenia klastra. Nie są ograniczone przez podstawową opcję magazynu w użyciu.  Zobacz opcje usługi usługi ML usługi [usługi platformy Azure w programie HDInsight,](./r-server-storage.md) aby dowiedzieć się więcej na temat korzystania z wielu kont magazynu.

Można również użyć usługi Azure Files jako opcji magazynu do użycia w węźle krawędzi. Usługa Azure Files umożliwia udziały plików utworzone w usłudze Azure Storage w systemie plików Linux. Aby uzyskać więcej informacji, zobacz [Opcje usługi Azure Storage dla usług ML w programie HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Węzeł brzegowy usług ML programu Access

Można połączyć się z serwerem Microsoft ML Server w węźle brzegowym za pomocą przeglądarki lub SSH/PuTTY. Konsola R jest instalowana domyślnie podczas tworzenia klastra.  

## <a name="develop-and-run-r-scripts"></a>Tworzenie i uruchamianie skryptów języka R

Skrypty języka R mogą używać dowolnego z ponad 8000 pakietów języka R typu open source. Można również użyć równoległych i rozproszonych procedur z biblioteki ScaleR. Skrypty uruchamiane w węźle brzegowej są uruchamiane w interpreterze języka R w tym węźle. Z wyjątkiem kroków, które wywołują funkcje ScaleR z mapą Reduce (RxHadoopMR) lub Spark (RxSpark) kontekstu obliczeniowego. Funkcje są uruchamiane w sposób rozproszony w węzłach danych, które są skojarzone z danymi. Aby uzyskać więcej informacji na temat opcji kontekstu, zobacz [Opcje kontekstu obliczeń dla usług ML w programie HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize`model

Po zakończeniu modelowania danych, `operationalize` aby prognoz dla nowych danych z platformy Azure lub lokalnie. Ten proces jest znany jako punktacji. Ocenianie można wykonać w usłudze HDInsight, usłudze Azure Machine Learning lub lokalnie.

### <a name="score-in-hdinsight"></a>Wynik w HDInsight

Aby uzyskać wynik w HDInsight, napisz funkcję R. Funkcja wywołuje model, aby prognoz dla nowego pliku danych, który został załadowany do konta magazynu. Następnie zapisz prognozy z powrotem na koncie magazynu. Tę procedurę można uruchomić na żądanie w węźle brzegowym klastra lub przy użyciu zaplanowanego zadania.

### <a name="score-in-azure-machine-learning-aml"></a>Wynik w usłudze Azure Machine Learning (AML)

Aby uzyskać wynik przy użyciu usługi Azure Machine Learning, użyj pakietu języka R usługi Azure Azure open source znanego jako [AzureML,](https://cran.r-project.org/src/contrib/Archive/AzureML/) aby opublikować model jako usługę sieci Web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowany w węźle brzegowym. Następnie użyj obiektów w usłudze Azure Machine Learning, aby utworzyć interfejs użytkownika dla usługi sieci web, a następnie wywołać usługę sieci web w razie potrzeby do oceniania. Następnie konwertuj obiekty modelu ScaleR na równoważne obiekty modelu typu open source do użytku z usługą sieci web. Użyj funkcji przymusu ScaleR, takich jak `as.randomForest()` dla modeli opartych na zespole, dla tej konwersji.

### <a name="score-on-premises"></a>Ocena lokalnie

Aby uzyskać wynik lokalnie po utworzeniu modelu: serializować model w R, pobrać go, de-serialize go, a następnie użyć go do oceniania nowych danych. Nowe dane można uzyskać przy użyciu podejścia opisanego wcześniej w Score in HDInsight lub za pomocą [usług internetowych.](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Instalowanie i obsługa pakietów R

Większość pakietów języka R, których używasz, jest wymagana w węźle brzegowej, ponieważ większość kroków skryptów języka R jest tam uruchamiana. Aby zainstalować dodatkowe pakiety R w węźle krawędzi, można użyć `install.packages()` metody w R.

Jeśli po prostu używasz procedur biblioteki ScaleR, zwykle nie potrzebujesz dodatkowych pakietów Języka R. Może być potrzebne dodatkowe pakiety dla **rxExec** lub **RxDataStep** wykonanie w węzłach danych.

Dodatkowe pakiety można zainstalować z akcji skryptu po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługami ML w klastrze HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Zmiana ustawień pamięci Apache Hadoop MapReduce

Dostępna pamięć do usług ML można modyfikować podczas uruchamiania zadania MapReduce. Aby zmodyfikować klaster, użyj interfejsu użytkownika Apache Ambari dla klastra. Aby zapoznać się z instrukcjami interfejsu użytkownika ambari, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Dostępna pamięć do usług ML można zmienić za pomocą przełączników Hadoop w wywołaniu **RxHadoopMR:**

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejący klaster usług ML w programie HDInsight można skalować w górę lub w dół za pośrednictwem portalu. Skalowanie w górę, można uzyskać dodatkową pojemność dla większych zadań przetwarzania. Można skalować z powrotem klastra, gdy jest bezczynny. Aby uzyskać instrukcje dotyczące skalowania klastra, zobacz [Zarządzanie klastrami HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Konserwacja systemu operacyjnego odbywa się na podstawowych maszynach wirtualnych z systemem Linux w klastrze HDInsight poza godzinami pracy. Zazwyczaj konserwacja odbywa się o godzinie 3:30 (czasu lokalnego maszyny Wirtualnej) w każdy poniedziałek i czwartek. Aktualizacje nie mają wpływu na więcej niż jedną czwartą klastra naraz.

Uruchamianie zadań może spowolnić podczas konserwacji. Jednak powinny one nadal działać do końca. Wszelkie niestandardowe oprogramowanie lub dane lokalne, które zostały zachowane w tych zdarzeniach konserwacji, chyba że wystąpi katastrofalny błąd, który wymaga przebudowy klastra.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opcje IDE dla usług ML w programie HDInsight

Węzeł krawędzi systemu Linux klastra HDInsight jest strefą lądowania dla analizy opartej na r. Najnowsze wersje usługi HDInsight zapewniają ideę RStudio Server opartą na przeglądarce w węźle brzegowym. Serwer RStudio jest bardziej wydajny niż konsola R do tworzenia i wykonywania.

Pulpit IDE może uzyskać dostęp do klastra za pośrednictwem zdalnego mapreduce lub spark obliczeń kontekstu. Opcje obejmują: Microsoft [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio i Walware Eclipse oparte StatET.

Dostęp do konsoli R w węźle krawędzi, wpisując **polecenie R** w wierszu polecenia. Podczas korzystania z interfejsu konsoli, jest to wygodne do tworzenia skryptu Języka R w edytorze tekstu. Następnie wytnij i wklej sekcje skryptu do konsoli języka R w razie potrzeby.

## <a name="pricing"></a>Cennik

Ceny skojarzone z klastrem HDInsight usług ML services są skonstruowane podobnie do innych typów klastrów HDInsight. Są one oparte na wielkości podstawowych maszyn wirtualnych w nazwa, dane i węzły krawędzi. Core-godzina podnosi, jak również. Aby uzyskać więcej informacji, zobacz [HDInsight cennik](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z usług ML w klastrach HDInsight, zobacz następujące artykuły:

* [Wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Opcje pamięci masowej klastra usług ML w programie HDInsight](r-server-storage.md)
