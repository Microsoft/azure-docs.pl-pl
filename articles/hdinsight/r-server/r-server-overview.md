---
title: Wprowadzenie do usługi ML w usłudze Azure HDInsight
description: Dowiedz się, jak używać usług ML w usłudze HDInsight do tworzenia aplikacji do analizy danych Big Data.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943928"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co to jest usługa ML w usłudze Azure HDInsight

Microsoft Machine Learning Server jest dostępna jako opcja wdrażania podczas tworzenia klastrów usługi HDInsight na platformie Azure. Typ klastra, który zapewnia tę opcję, nosi nazwę **usługi ml**. Ta funkcja zapewnia dostęp na żądanie do dostosowywalnych, dystrybuowanych metod analizy w usłudze HDInsight.

Usługi ML w usłudze HDInsight zapewniają najnowsze możliwości analizy opartej na języku R na zestawach danych praktycznie dowolnym rozmiarze. Zestawy danych mogą być ładowane do usługi Azure Blob lub magazynu Data Lake. Aplikacje bazujące na języku R mogą korzystać z pakietów języka R o 8000 + Open Source. Dostępne są również procedury w ramach skalowania, czyli pakiet do analizy danych Big Data firmy Microsoft.

Węzeł brzegowy zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. Węzeł brzegowy umożliwia uruchamianie równoległych funkcji skalowania w obrębie rdzeni serwera. Można je również uruchamiać w węzłach klastra przy użyciu mapy usługi Hadoop skalowania w poziomie. Można również użyć kontekstów obliczeniowych Apache Spark.

Modele lub przewidywania powstałe w wyniku analizy mogą być pobierane do użytku lokalnego. Mogą one również znajdować się `operationalized` w innym miejscu na platformie Azure. W szczególności za poorednictwem [Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net)i [usługi sieci Web](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Wprowadzenie do usługi l Services w usłudze HDInsight

Aby utworzyć klaster usługi ML w usłudze HDInsight, wybierz typ klastra **usługi ml** . Typ klastra usługi ML zawiera ML Server w węzłach danych i węźle brzegowym. Węzeł brzegowy służy jako strefa wypełniania dla analiz opartych na usługach ML. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal,](../hdinsight-hadoop-create-linux-clusters-portal.md) aby zapoznać się z przewodnikiem dotyczącym tworzenia klastra.

## <a name="why-choose-ml-services-in-hdinsight"></a>Dlaczego warto wybrać usługi ML w usłudze HDInsight?

Usługi ML w usłudze HDInsight zapewniają następujące korzyści:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innowacje AI firmy Microsoft i Open-Source

  Usługi ML obejmują wysoce dostosowywalny, rozproszony zestaw algorytmów, takich jak [kolekcję funkcji revoscaler](/machine-learning-server/r-reference/revoscaler/revoscaler), [biblioteki revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)i [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Algorytmy te mogą korzystać z rozmiarów danych większych niż rozmiar pamięci fizycznej. Są one również uruchamiane na wielu platformach w sposób rozproszony. Dowiedz się więcej na temat kolekcji niestandardowych [pakietów języka R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) firmy Microsoft i [pakietów Python](/machine-learning-server/python-reference/introducing-python-package-reference) zawartych w produkcie.
  
  Usługi ML mostkuje te innowacje i wkłady firmy Microsoft pochodzące z społeczności typu Open Source (zestawu narzędzi języka R, Python i AI). Wszystko to na jednej platformie klasy korporacyjnej. Każdy pakiet uczenia maszynowego "open source" w języku R lub Python może współpracować ze wszystkimi zastrzeżonymi innowacyjnymi firmy Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Proste, bezpieczne i wysoce skalowane operacjonalizacji i administracja

  Przedsiębiorstwa opierają się na tradycyjnych odmianach i środowiskach inwestycji dużo czasu i wysiłku na operacjonalizacji. Ta akcja skutkuje niepełnymi kosztami i opóźnieniami, w tym czasem tłumaczenia dla: modele, iteracje, aby zachować ich ważność i bieżące, zatwierdzanie prawne i zarządzanie uprawnieniami.

  Usługi ML oferują [operacjonalizacji](/machine-learning-server/what-is-operationalization)klasy korporacyjnej. Po zakończeniu działania modelu uczenia maszynowego program klika kilka kliknięć, aby generować interfejsy API usług sieci Web. Te [usługi sieci Web](/machine-learning-server/operationalize/concept-what-are-web-services) są hostowane w sieci na serwerze w chmurze i mogą być zintegrowane z aplikacjami biznesowymi. Możliwość wdrażania w elastycznej siatce umożliwia bezproblemowe skalowanie z potrzebami firmy zarówno w przypadku partii, jak i w czasie rzeczywistym. Aby uzyskać instrukcje, zobacz [OPERACJONALIZOWAĆ ml Services w usłudze HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ klastra usług ML w usłudze HDInsight jest obsługiwany tylko w usłudze HDInsight 3,6. Zaplanowano wycofanie usługi HDInsight 3,6 w dniu 31 grudnia 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Najważniejsze funkcje usługi ML Services w usłudze HDInsight

Poniższe funkcje są zawarte w usłudze ML w usłudze HDInsight.

| Kategoria funkcji | Opis |
|------------------|-------------|
| Język R — włączony | [Pakiety języka r](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) dla rozwiązań utworzonych w języku r, z dystrybucją języka r typu open source i infrastruktury czasu wykonywania na potrzeby wykonywania skryptów. |
| Python — włączono | [Moduły języka Python](/machine-learning-server/python-reference/introducing-python-package-reference) dla rozwiązań utworzonych w języku Python, z dystrybucją typu "open source" w języku Python i infrastruktury środowiska uruchomieniowego na potrzeby wykonywania skryptów.
| [Modele wstępnie nauczone](/machine-learning-server/install/microsoftml-install-pretrained-models) | Na potrzeby analizy wizualizacji i tekstu tonacji można przystąpić do oceny danych, które zapewniasz. |
| [Wdrażanie i korzystanie z](r-server-operationalize.md) | `Operationalize` Twój serwer i Wdróż rozwiązania jako usługę sieci Web. |
| [Zdalne wykonywanie](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Uruchom sesje zdalne w klastrze usługi ML w sieci z poziomu stacji roboczej klienta. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opcje przechowywania danych dla usług ML w usłudze HDInsight

Domyślnym magazynem systemu plików HDFS może być konto usługi Azure Storage lub Azure Data Lake Storage. Przekazane dane do magazynu klastra podczas analizy są trwałe. Dane są dostępne nawet po usunięciu klastra. Różne narzędzia mogą obsługiwać transfer danych do magazynu. Narzędzia te obejmują funkcję przekazywania opartą na portalu dla konta magazynu i narzędzia AzCopy.

Podczas tworzenia klastra można włączyć dostęp do dodatkowych obiektów blob i magazynów Data Lake. Nie są one ograniczone przez opcję magazynu podstawowego w użyciu.  Aby dowiedzieć się więcej o używaniu wielu kont magazynu, zobacz temat [Opcje usługi Azure Storage dla usług ml w usłudze HDInsight](./r-server-storage.md) .

Możesz również użyć Azure Files jako opcji magazynu do użycia w węźle brzegowym. Azure Files umożliwia udziały plików utworzone w usłudze Azure Storage w systemie plików Linux. Aby uzyskać więcej informacji, zobacz temat [Azure Storage options for ml Services w usłudze HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Węzeł brzegowy usługi Access Services

Można nawiązać połączenie z usługą Microsoft ML Server w węźle brzegowym przy użyciu przeglądarki lub protokołu SSH/wylogowania. Konsola języka R jest instalowana domyślnie podczas tworzenia klastra.  

## <a name="develop-and-run-r-scripts"></a>Opracowywanie i uruchamianie skryptów języka R

Skrypty języka R mogą korzystać z dowolnego z pakietów języka R typu "open source" w liczbie 8000. Można również użyć równoległych i rozproszonych procedur z biblioteki skalowania. Skrypty uruchamiane w węźle brzegowym działają w obrębie interpretera języka R w tym węźle. Z wyjątkiem kroków, które są wywoływane w funkcjach skalowania przy użyciu kontekstu obliczeniowego ograniczenia mapy (RxHadoopMR) lub Spark (obliczeniowego rxspark). Funkcje są uruchamiane w sposób rozproszony w węzłach danych, które są skojarzone z danymi. Aby uzyskać więcej informacji na temat opcji kontekstu, zobacz temat [Opcje kontekstu obliczeniowego dla usług w usłudze ml w usłudze HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` Model

Po zakończeniu modelowania danych `operationalize` model do prognozowania dla nowych danych na platformie Azure lub w środowisku lokalnym. Ten proces jest znany jako ocenianie. Ocenianie może odbywać się w usłudze HDInsight, Azure Machine Learning lub lokalnie.

### <a name="score-in-hdinsight"></a>Wyniki w usłudze HDInsight

Aby wypróbować w usłudze HDInsight, napisz funkcję języka R. Funkcja wywołuje model, aby dokonać prognoz dla nowego pliku danych, który został załadowany do konta magazynu. Następnie Zapisz przewidywania z powrotem na koncie magazynu. Tę procedurę można uruchomić na żądanie na węźle brzegowym klastra lub przy użyciu zaplanowanego zadania.

### <a name="score-in-azure-machine-learning-aml"></a>Wynik w Azure Machine Learning (AML)

Aby wypróbować Azure Machine Learning, Użyj pakietu języka R "Open Azure Machine Learning source", który jest znany jako [Azure](https://cran.r-project.org/src/contrib/Archive/AzureML/) , aby opublikować model jako usługę sieci Web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowany w węźle brzegowym. Następnie użyj funkcji w Azure Machine Learning, aby utworzyć interfejs użytkownika dla usługi sieci Web, a następnie Wywołaj usługę sieci Web zgodnie z wymaganiami dla oceny. Następnie przekonwertuj obiekty modelu skalowania na równoważne obiekty modelu "open source", które mają być używane z usługą sieci Web. W przypadku tej konwersji Użyj funkcji przekształcania skalowania, na przykład `as.randomForest()` dla modeli opartych na modelu.

### <a name="score-on-premises"></a>Ocena w środowisku lokalnym

Aby wypróbować lokalnie po utworzeniu modelu: serializować model w języku R, Pobierz go, a następnie użyj go do oceniania nowych danych. Nowe dane można wystawić przy użyciu opisanego wcześniej podejścia do oceny usługi HDInsight lub [usług sieci Web](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Instalowanie i konserwowanie pakietów języka R

Większość używanych pakietów języka R jest wymagana w węźle brzegowym, ponieważ większość kroków skryptów języka R jest w tej chwili uruchamiana. Aby zainstalować dodatkowe pakiety języka R w węźle brzegowym, można użyć `install.packages()` metody w języku R.

Jeśli używasz tylko procedur bibliotek skalowania, zazwyczaj nie potrzebujesz dodatkowych pakietów R. W węzłach danych mogą być potrzebne dodatkowe pakiety do wykonania **rxExec** lub **RxDataStep** .

Dodatkowe pakiety można zainstalować z akcją skryptu utworzoną po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługami ml w klastrze usługi HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Zmień ustawienia pamięci Apache Hadoop MapReduce

Po uruchomieniu zadania MapReduce można modyfikować dostępną pamięć do usługi ML. Aby zmodyfikować klaster, należy użyć interfejsu użytkownika Apache Ambari dla klastra. Instrukcje dotyczące interfejsu użytkownika Ambari znajdują się w temacie [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Dostępną pamięć do usługi ML można zmienić przy użyciu przełączników Hadoop w wywołaniu **RxHadoopMR**:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejący klaster usług ML w usłudze HDInsight można skalować w górę lub w dół w portalu. Skalowanie w górę pozwala uzyskać dodatkową pojemność w przypadku większych zadań przetwarzania. Możesz skalować ponownie klaster, gdy jest on bezczynny. Aby uzyskać instrukcje dotyczące skalowania klastra, zobacz [Zarządzanie klastrami usługi HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Konserwacja systemu operacyjnego odbywa się na podstawowych maszynach wirtualnych z systemem Linux w klastrze usługi HDInsight w ciągu kilku godzin. Zwykle konserwacja odbywa się o godzinie 3:30 (czas lokalny maszyny wirtualnej) co poniedziałek i czwartek. Aktualizacje nie wpływają na więcej niż czwarty klaster w danym momencie.

Uruchomione zadania mogą spowalniać pracę w trakcie konserwacji. Jednak powinny one nadal działać. Wszelkie niestandardowe oprogramowanie lub dane lokalne, które zostały zachowane w tych zdarzeniach konserwacyjnych, chyba że wystąpi błąd krytyczny, który wymaga ponownego skompilowania klastra.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opcje środowiska IDE dla usług ML w usłudze HDInsight

Węzeł brzegowy systemu Linux klastra usługi HDInsight jest strefą docelową analizy na podstawie języka R. Najnowsze wersje usługi HDInsight zapewniają oparty na przeglądarce środowisko IDE serwera RStudio na węźle brzegowym. Serwer RStudio jest bardziej wydajny niż konsola języka R do tworzenia i wykonywania.

Środowisko IDE pulpitu może uzyskać dostęp do klastra za pomocą zdalnego kontekstu obliczeniowego MapReduce lub Spark. Dostępne opcje to: Microsoft [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio i Walware.

Aby uzyskać dostęp do konsoli języka R w węźle brzegowym, wpisz **R** w wierszu polecenia. Korzystając z interfejsu konsoli, wygodnie jest utworzyć skrypt języka R w edytorze tekstu. Następnie należy wyciąć i wkleić sekcje skryptu do konsoli języka R, zgodnie z potrzebami.

## <a name="pricing"></a>Ceny

Ceny skojarzone z klastrem usługi HDInsight usług w usłudze ML mają strukturę podobną do innych typów klastrów w usłudze HDInsight. Są one oparte na wymiarze rozmiarów bazowych maszyn wirtualnych w obrębie nazwy, danych i węzłów brzegowych. Również przyrosty godzin. Aby uzyskać więcej informacji, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z usług ML w klastrach usługi HDInsight, zobacz następujące artykuły:

* [Wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Opcje magazynu dla klastra usług ML w usłudze HDInsight](r-server-storage.md)