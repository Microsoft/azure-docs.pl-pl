---
title: 'ML Studio (klasyczny): Zarządzanie cyklem życia aplikacji — Azure'
description: Zastosuj najlepsze rozwiązania dotyczące zarządzania cyklem życia aplikacji w Azure Machine Learning Studio (klasyczny)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518406"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Zarządzanie cyklem życia aplikacji w Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (klasyczny) to narzędzie służące do opracowywania eksperymentów uczenia maszynowego, które są operacyjne na platformie Azure w chmurze. Podobnie jak w przypadku środowiska IDE programu Visual Studio i skalowalnej usługi w chmurze Scalonej z jedną platformą. Możesz uwzględnić standardowe zasady zarządzania cyklem życia aplikacji (ALM), aby korzystać z różnych zasobów do automatycznego wykonywania i wdrażania, w Azure Machine Learning Studio (klasyczne). W tym artykule omówiono niektóre opcje i metody.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Eksperymentowanie wersji
Istnieją dwa zalecane sposoby przetworzenia wersji eksperymentów. Możesz polegać na wbudowanej historii uruchamiania lub wyeksportować eksperyment w formacie JSON, aby zarządzać nim zewnętrznie. Każde podejście ma swoje zalety i wady.

### <a name="experiment-snapshots-using-run-history"></a>Tworzenie eksperymentów migawek przy użyciu historii uruchamiania
W modelu wykonywania eksperymentu szkoleniowego Azure Machine Learning Studio (klasyczny) niezmienna migawka eksperymentu jest przesyłana do harmonogramu zadań po każdym kliknięciu przycisku **Uruchom** w edytorze eksperymentów. Aby wyświetlić tę listę migawek, kliknij pozycję **historia uruchamiania** na pasku poleceń w widoku edytora eksperymentów.

![Przycisk Historia uruchamiania](./media/version-control/runhistory.png)

Następnie można otworzyć migawkę w trybie zablokowanym, klikając nazwę eksperymentu w chwili, gdy eksperyment został przesłany do uruchomienia, a migawka została wykonana. Zauważ, że tylko pierwszy element na liście, który reprezentuje bieżący eksperyment, jest w stanie umożliwiającym edycję. Zauważ również, że każda migawka może być również w różnych stanach, w tym zakończone (częściowe uruchomienie), zakończone niepowodzeniem, Niepowodzenie (częściowe uruchomienie) lub wersja robocza.

![Lista historii uruchamiania](./media/version-control/runhistorylist.png)

Po jego otwarciu można zapisać eksperyment tworzenia migawek jako nowy eksperyment, a następnie go zmodyfikować. Jeśli migawka eksperymentu zawiera zasoby, takie jak modele przeszkolone, przekształcenia lub zestawy danych, które mają zaktualizowane wersje, migawka zachowuje odwołania do oryginalnej wersji podczas tworzenia migawki. W przypadku zapisania zablokowanej migawki jako nowego eksperymentu Azure Machine Learning Studio (klasyczny) wykryje istnienie nowszej wersji tych zasobów i automatycznie zaktualizuje je w nowym eksperymentie.

Po usunięciu eksperymentu wszystkie migawki tego eksperymentu zostaną usunięte.

### <a name="exportimport-experiment-in-json-format"></a>Eksperyment eksportu/importu w formacie JSON
Migawki uruchamiania migawek przechowują niezawodną wersję eksperymentu w Azure Machine Learning Studio (klasyczny) za każdym razem, gdy zostanie przesłany do uruchomienia. Możesz również zapisać lokalną kopię eksperymentu i zaewidencjonować ją do swojego ulubionego systemu kontroli źródła, takiego jak Team Foundation Server, i później na potrzeby ponownego tworzenia eksperymentu z tego pliku lokalnego. Do osiągnięcia tego celu można użyć [Azure Machine Learning PowerShell](https://aka.ms/amlps) polecenia cmdlet [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) .

Plik JSON to tekstowa reprezentacja grafu eksperymentu, która może zawierać odwołanie do zasobów w obszarze roboczym, takim jak zestaw danych lub model szkolony. Nie zawiera serializowanej wersji elementu zawartości. W przypadku próby zaimportowania dokumentu JSON z powrotem do obszaru roboczego, przywoływane zasoby muszą już istnieć z tymi samymi identyfikatorami zasobów, do których odwołuje się eksperyment. W przeciwnym razie nie możesz uzyskać dostępu do zaimportowanego eksperymentu.

## <a name="versioning-trained-model"></a>Model przeszkolonej wersji
Model przeszkolony w Azure Machine Learning Studio (klasyczny) jest serializowany do formatu znanego jako plik iLearner ( `.iLearner` ) i jest przechowywany na koncie usługi Azure Blob Storage skojarzonym z obszarem roboczym. Jednym ze sposobów uzyskania kopii pliku iLearner jest przechodzenie przez interfejs API ponownego uczenia. W [tym artykule](./retrain-machine-learning-model.md) wyjaśniono, jak działa interfejs API ponownego uczenia. Ogólne czynności:

1. Skonfiguruj eksperyment szkoleniowy.
2. Dodaj port wyjściowy usługi sieci Web do modułu uczenia modelu lub moduł, który produkuje szkolony model, taki jak dostrajanie modelu lub tworzenie modelu języka R.
3. Uruchom eksperyment szkoleniowy, a następnie wdróż go jako usługę sieci Web szkoleń modeli.
4. Wywołaj punkt końcowy BES usługi sieci Web szkoleń i określ odpowiednią nazwę pliku iLearner i lokalizację konta usługi BLOB Storage, w której będzie ona przechowywana.
5. Zbieraj wygenerowany plik iLearner po zakończeniu wywołania BES.

Innym sposobem pobrania pliku iLearner jest użycie narzędzia PowerShell polecenia cmdlet [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Może to być łatwiejsze, jeśli chcesz uzyskać kopię pliku iLearner bez konieczności samodzielnego uczenia modelu.

Po utworzeniu pliku iLearner zawierającego przeszkolony model możesz zastosować własną strategię obsługi wersji. Strategia może być równie prosta, jak stosowanie wstępnej/przyrostowej konwencji nazewnictwa i po prostu opuszczenie pliku iLearner w usłudze BLOB Storage lub skopiowanie/zaimportowanie go do systemu kontroli wersji.

Zapisany plik iLearner może być następnie używany do oceniania za pomocą wdrożonych usług sieci Web.

## <a name="versioning-web-service"></a>Wersja usługi sieci Web
Możesz wdrożyć dwa typy usług sieci Web z poziomu eksperymentu Azure Machine Learning Studio (klasycznego). Klasyczna usługa sieci Web jest ściśle połączona z eksperymentem, a także z obszarem roboczym. Nowa usługa sieci Web używa platformy Azure Resource Manager i nie jest już połączona z pierwotnym eksperymentem lub obszarem roboczym.

### <a name="classic-web-service"></a>Klasyczna usługa sieci Web
Aby skorzystać z wersji klasycznej usługi sieci Web, można skorzystać z konstrukcji punktu końcowego usługi sieci Web. Oto typowy przepływ:

1. Z eksperymentu predykcyjnego wdrażana jest nowa klasyczna usługa sieci Web, która zawiera domyślny punkt końcowy.
2. Tworzysz nowy punkt końcowy o nazwie EP2, który ujawnia bieżącą wersję eksperymentu/przeszkolonego modelu.
3. Możesz wrócić i zaktualizować eksperyment predykcyjny i przeszkolony model.
4. Należy ponownie wdrożyć eksperyment predykcyjny, który zaktualizuje domyślny punkt końcowy. Nie spowoduje to jednak zmiany EP2.
5. Tworzysz dodatkowy punkt końcowy o nazwie EP3, który uwidacznia nową wersję eksperymentu i szkolony model.
6. W razie potrzeby Wróć do kroku 3.

W miarę upływu czasu może istnieć wiele punktów końcowych utworzonych w tej samej usłudze sieci Web. Każdy punkt końcowy reprezentuje kopię eksperymentu do punktu w czasie obejmującą przeszkolony model do punktu w czasie. Następnie można użyć zewnętrznej logiki, aby określić, który punkt końcowy jest wywoływany, co efektywnie oznacza wybranie wersji modelu przeszkolonego dla przebiegu oceniania.

Możesz również utworzyć wiele identycznych punktów końcowych usługi sieci Web, a następnie poprawić różne wersje pliku iLearner do punktu końcowego, aby osiągnąć podobny efekt. W [tym artykule](create-models-and-endpoints-with-powershell.md) wyjaśniono bardziej szczegółowo, jak to zrobić.

### <a name="new-web-service"></a>Nowa usługa sieci Web
W przypadku tworzenia nowej usługi sieci Web opartej na Azure Resource Manager konstrukcja punktu końcowego nie jest już dostępna. Zamiast tego można generować pliki definicji usługi sieci Web (WSD) w formacie JSON, od eksperymentu predykcyjnego za pomocą polecenia [AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) programu PowerShell polecenia cmdlet lub przy użyciu programu PowerShell [*Export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) z wdrożonej usługi sieci web opartej na Menedżer zasobów.

Po uzyskaniu przez niego wyeksportowanego pliku WSD i kontroli wersji można także wdrożyć usługi WSD jako nową usługę sieci Web w innym planie usługi sieci Web w innym regionie platformy Azure. Wystarczy upewnić się, że podano odpowiednią konfigurację konta magazynu, a także nowy identyfikator planu usługi sieci Web. Aby wykonać poprawkę w różnych plikach iLearner, można zmodyfikować plik WSD i zaktualizować odwołanie do lokalizacji dla przeszkolonego modelu i wdrożyć je jako nową usługę sieci Web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatyzowanie wykonywania eksperymentów i wdrażania
Ważnym elementem ALM jest możliwość automatyzowania procesu wykonywania i wdrażania aplikacji. W Azure Machine Learning Studio (klasyczny) można to zrobić przy użyciu [modułu programu PowerShell](https://aka.ms/amlps). Oto przykład kompleksowych kroków, które są istotne dla standardowego procesu automatycznego wykonywania/wdrażania ALM, przy użyciu [modułu PowerShell Azure Machine Learning Studio (klasycznego)](https://aka.ms/amlps). Każdy krok jest połączony z co najmniej jednym polecenia cmdletem programu PowerShell, którego można użyć do wykonania tego kroku.

1. [Przekaż zestaw danych](https://github.com/hning86/azuremlps#upload-amldataset).
2. Skopiuj eksperyment szkoleniowy do obszaru roboczego z [obszaru roboczego](https://github.com/hning86/azuremlps#copy-amlexperiment) lub z [galerii](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)lub [zaimportuj](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [wyeksportowany](https://github.com/hning86/azuremlps#export-amlexperimentgraph) eksperyment z dysku lokalnego.
3. [Zaktualizuj zestaw danych](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w doświadczeniu szkoleniowym.
4. [Uruchom eksperyment szkoleniowy](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Podwyższ poziom nauczonego modelu](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Skopiuj eksperyment predykcyjny](https://github.com/hning86/azuremlps#copy-amlexperiment) do obszaru roboczego.
7. [Aktualizowanie przeszkolonego modelu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) w ramach eksperymentu predykcyjnego.
8. [Uruchom eksperyment predykcyjny](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Wdróż usługę sieci Web](https://github.com/hning86/azuremlps#new-amlwebservice) na podstawie eksperymentu predykcyjnego.
10. Testowanie [zasobów](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) usługi sieci Web lub punktu końcowego [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) .

## <a name="next-steps"></a>Następne kroki
* Pobierz moduł [programu PowerShell dla Azure Machine Learning Studio (klasyczny)](https://aka.ms/amlps) i Rozpocznij automatyzację zadań Alm.
* Dowiedz się, jak [utworzyć dużą liczbę modeli ml i zarządzać nimi za pomocą pojedynczego eksperymentu](create-models-and-endpoints-with-powershell.md) za pomocą programu PowerShell i środowiska API ponownego uczenia.
* Dowiedz się więcej na temat [wdrażania usług sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md).