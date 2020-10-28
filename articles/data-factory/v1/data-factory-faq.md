---
title: Azure Data Factory — często zadawane pytania
description: Często zadawane pytania dotyczące Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de4661bcfd7106d515480e39dc4660f44f469a3d
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631703"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory — często zadawane pytania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [często zadawane pytania lub Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-data-factory"></a>Co to jest usługa Azure Data Factory?
Data Factory to oparta na chmurze usługa integracji danych, która **automatyzuje przenoszenie i transformację danych** . Tak jak w przypadku fabryki, która uruchamia sprzęt, aby pobierać surowce i przekształcić je w gotowe towary, Data Factory organizować istniejące usługi, które zbierają dane pierwotne i przekształcają je w gotowe do użycia informacje.

Data Factory umożliwia tworzenie przepływów pracy opartych na danych w celu przenoszenia danych między magazynami danych zarówno lokalnymi, jak i w chmurze, a także przetwarzaniem/przekształcaniem danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight i Azure Data Lake Analytics. Po utworzeniu potoku, który wykonuje potrzebną akcję, można zaplanować okresowe uruchamianie (co godzinę, codziennie, co tydzień itd.).   

Aby uzyskać więcej informacji, zobacz [omówienie & kluczowe pojęcia](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Gdzie można znaleźć szczegóły cennika Azure Data Factory?
Szczegóły cennika Azure Data Factory można znaleźć na [stronie Szczegóły cennika Data Factory][adf-pricing-details] .  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak mogę rozpocząć pracę z usługą Azure Data Factory?
* Aby zapoznać się z omówieniem Azure Data Factory, zobacz [wprowadzenie do Azure Data Factory](data-factory-introduction.md).
* Aby zapoznać się z samouczkiem dotyczącym **kopiowania/przenoszenia danych** za pomocą działania kopiowania, zobacz [Kopiowanie danych z platformy Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Aby zapoznać się z samouczkiem dotyczącym **przekształcania danych** przy użyciu działania programu Hive w usłudze HDInsight. Zobacz [przetwarzanie danych przez uruchomienie skryptu programu Hive w klastrze usługi Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Co to jest dostępność regionu Data Factory?
Data Factory jest dostępny w regionie **zachodnie stany USA** i **Europa Północna** . Usługi obliczeniowe i magazynowe używane przez fabrykę danych mogą znajdować się w innych regionach. Zobacz [Obsługiwane regiony](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jakie są limity dotyczące liczby fabryk danych/potoków/działań/zestawów DataSet?
Zapoznaj się z sekcją **limity Azure Data Factory** w artykule dotyczącym [limitów subskrypcji i usług, przydziałów i ograniczeń usługi](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co to jest środowisko tworzenia i programowania za pomocą usługi Azure Data Factory?
Fabryki danych można tworzyć/utworzyć przy użyciu jednego z następujących narzędzi/zestawów SDK:

* **Program Visual Studio** Aby utworzyć fabrykę danych Azure, można użyć programu Visual Studio. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie pierwszego potoku danych przy użyciu programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell** Zobacz [Tworzenie i monitorowanie Azure Data Factory przy użyciu Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) samouczka/wskazówki dotyczące tworzenia fabryki danych przy użyciu programu PowerShell. Aby uzyskać kompleksową dokumentację Data Factory poleceń cmdlet, zobacz artykuł [Dokumentacja dokumentacji dotyczącej poleceń cmdlet Data Factory][adf-powershell-reference] w bibliotece MSDN.
* **Biblioteka klas .NET** Fabryki danych można programistycznie tworzyć za pomocą Data Factory .NET SDK. Zobacz [Tworzenie i monitorowanie fabryk danych oraz zarządzanie nimi przy użyciu zestawu .NET SDK](data-factory-create-data-factories-programmatically.md) , aby zapoznać się z przewodnikiem tworzenia fabryki danych przy użyciu zestawu SDK platformy .NET. Aby uzyskać kompleksową dokumentację Data Factory zestawu .NET SDK, zobacz [Data Factory Dokumentacja biblioteki klas][msdn-class-library-reference] .
* **interfejs API REST** Można również użyć interfejsu API REST uwidocznionego przez usługę Azure Data Factory, aby tworzyć i wdrażać fabryki danych. Aby uzyskać kompleksową dokumentację Data Factory interfejsu API REST, zobacz [Data Factory Dokumentacja interfejsu API REST][msdn-rest-api-reference] .
* **Szablon Azure Resource Manager** Zobacz [Samouczek: Tworzenie pierwszej fabryki danych Azure przy użyciu Azure Resource Manager szablonu](data-factory-build-your-first-pipeline-using-arm.md) szczegółowego.

### <a name="can-i-rename-a-data-factory"></a>Czy mogę zmienić nazwę fabryki danych?
Nie. Podobnie jak w przypadku innych zasobów platformy Azure, nie można zmienić nazwy fabryki danych platformy Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Czy mogę przenieść fabrykę danych z jednej subskrypcji platformy Azure do innej?
Tak. Użyj przycisku **Przenieś** w bloku Fabryka danych, jak pokazano na poniższym diagramie:

![Przenoszenie fabryki danych](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Jakie środowiska obliczeniowe są obsługiwane przez Data Factory?
Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez Data Factory oraz działania, które można na nich uruchamiać.

| Środowisko obliczeniowe | activities |
| --- | --- |
| [Klaster HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) lub [własny klaster usługi HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[Dotnet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [świnie](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), usługa [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Usługa Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasyczny)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Działania programu Studio (klasyczne): wykonywanie wsadowe i aktualizowanie zasobu](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics (dawniej SQL Data Warehouse)](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedura składowana](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak Azure Data Factory porównać z usługą SQL Server Integration Services (SSIS)? 
Zapoznaj się z prezentacją [Azure Data Factory a SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) z jednej z naszych MVP (najbardziej cenionymi profesjonalistami): Reza rad. Niektóre ostatnie zmiany w Data Factory mogą nie być wymienione na slajdzie. Nieustannie dodajemy więcej możliwości do Azure Data Factory. Nieustannie dodajemy więcej możliwości do Azure Data Factory. Te aktualizacje zostaną dołączone do porównania technologii integracji danych firmy Microsoft w dalszej części tego roku.   

## <a name="activities---faq"></a>Działania — często zadawane pytania
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jakie są różne rodzaje działań, których można użyć w potoku Data Factory?
* [Działania przenoszenia danych](data-factory-data-movement-activities.md) w celu przenoszenia danych.
* [Działania przekształcania danych](data-factory-data-transformation-activities.md) w celu przetwarzania/przekształcania danych.

### <a name="when-does-an-activity-run"></a>Kiedy działa działanie?
Ustawienie konfiguracji **dostępności** w tabeli danych wyjściowych określa, kiedy działanie jest uruchamiane. Jeśli są określone wejściowe zestawy danych, działanie sprawdza, czy przed uruchomieniem systemu są spełnione wszystkie zależności z danymi wejściowymi (czyli stan **gotowości** ).

## <a name="copy-activity---faq"></a>Działanie Copy (kopiowanie) — często zadawane pytania
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Czy lepszym rozwiązaniem może być potok z wieloma działaniami lub oddzielny potok dla każdego działania?
Potoki powinny być powiązane z powiązanymi działaniami. Jeśli zestawy danych, które je łączą, nie są używane przez żadne inne działania poza potokiem, można zachować działania w jednym potoku. W ten sposób nie trzeba przeciągać aktywnych okresów potoku, aby były wyrównane ze sobą. Ponadto integralność danych w tabelach wewnętrznych dla potoku jest lepiej zachowywana podczas aktualizowania potoku. Aktualizacja potoku zasadniczo powoduje zatrzymanie wszystkich działań w potoku, usunięcie ich i ponowne utworzenie. W perspektywie tworzenia warto również łatwiej zobaczyć przepływ danych w ramach powiązanych działań w jednym pliku JSON dla potoku.

### <a name="what-are-the-supported-data-stores"></a>Jakie są obsługiwane magazyny danych?
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jakie są obsługiwane formaty plików?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Gdzie jest wykonywana operacja kopiowania?
Aby uzyskać szczegółowe informacje, zobacz sekcję [przenoszenie danych dostępnych globalnie](data-factory-data-movement-activities.md#global) . W krótkim czasie, gdy jest objęty lokalnym magazynem danych, operacja kopiowania jest wykonywana przez bramę Zarządzanie danymi w środowisku lokalnym. A w przypadku przenoszenia danych między dwoma magazynami w chmurze operacja kopiowania jest wykonywana w regionie najbliższym lokalizacji ujścia w tym samym obszarze geograficznym.

## <a name="hdinsight-activity---faq"></a>Działanie usługi HDInsight — często zadawane pytania
### <a name="what-regions-are-supported-by-hdinsight"></a>Jakie regiony są obsługiwane przez usługi HDInsight?
Zobacz sekcję Dostępność geograficzna w następującym artykule: lub [szczegóły cennika usługi HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Który region jest używany przez klaster usługi HDInsight na żądanie?
Klaster usługi HDInsight na żądanie jest tworzony w tym samym regionie, w którym znajduje się określony magazyn, który ma być używany z klastrem.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak skojarzyć dodatkowe konta magazynu z klastrem usługi HDInsight?
Jeśli używasz własnego klastra usługi HDInsight (BYOC), zobacz następujące tematy:

* [Korzystanie z klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami][hdinsight-alternate-storage]
* [Używanie dodatkowych kont magazynu z usługą HDInsight Hive][hdinsight-alternate-storage-2]

Jeśli używasz klastra na żądanie utworzonego za pomocą usługi Data Factory, określ dodatkowe konta magazynu dla połączonej usługi HDInsight, aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. W definicji JSON połączonej usługi na żądanie Użyj właściwości **additionalLinkedServiceNames** , aby określić alternatywne konta magazynu, jak pokazano w poniższym FRAGMENCIE kodu JSON:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
W powyższym przykładzie otherLinkedServiceName1 i otherLinkedServiceName2 reprezentują połączone usługi, których definicje zawierają poświadczenia wymagane przez klaster usługi HDInsight do uzyskiwania dostępu do alternatywnych kont magazynu.

## <a name="slices---faq"></a>Wycinków — często zadawane pytania
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Dlaczego moje wycinki wejściowe nie są w stanie gotowe?
Typowy błąd nie powoduje ustawienia właściwości **zewnętrznej** na **wartość true** w wejściowym zestawie danych, gdy dane wejściowe są zewnętrzne względem fabryki danych (nie są tworzone przez fabrykę danych).

W poniższym przykładzie wystarczy ustawić **zewnętrzny** na true w **pozycję DataSet1** .  

**DataFactory1** Potok 1: pozycję DataSet1-> zakończeniu-> DataSet2-> Activity2-> dataset3 potok 2: dataset3-> działanie activity3-> dataset4

Jeśli masz inną fabrykę danych z potokiem, który pobiera dataset4 (wyprodukowany przez potok 2 w usłudze Data Factory 1), Oznacz dataset4 jako zewnętrzny zestaw danych, ponieważ zestaw danych jest tworzony przez inną fabrykę danych (DataFactory1, a nie DataFactory2).  

**DataFactory2**    
Potok 1: dataset4->activity4->dataset5

Jeśli właściwość zewnętrzna jest prawidłowo ustawiona, sprawdź, czy dane wejściowe istnieją w lokalizacji określonej w definicji wejściowego zestawu danych.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak uruchomić wycinek w innym czasie niż północ, gdy wycinek jest tworzony codziennie?
Użyj właściwości **przesunięcia** , aby określić czas, w którym ma zostać utworzony wycinek. Zobacz sekcję [dostępność zestawu danych](data-factory-create-datasets.md#dataset-availability) , aby uzyskać szczegółowe informacje o tej właściwości. Oto krótki przykład:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dzienne wycinki zaczynają się od **6 am** zamiast domyślnej północy.     

### <a name="how-can-i-rerun-a-slice"></a>Jak mogę ponownie uruchomić wycinek?
Możesz ponownie uruchomić wycinek w jeden z następujących sposobów:

* Użyj aplikacji monitorowanie i zarządzanie, aby ponownie uruchomić okno działania lub wycinek. Aby uzyskać instrukcje, zobacz Ponowne uruchamianie [wybranych okien działania](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Kliknij przycisk **Uruchom** na pasku poleceń w bloku **wycinka danych** dla wycinka w Azure Portal.
* Uruchom polecenie cmdlet **Set-AzDataFactorySliceStatus** z ustawionym stanem " **oczekiwanie** na wycinek".   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Aby uzyskać szczegółowe informacje na temat polecenia cmdlet, zobacz [Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] .

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak długo trwa przetwarzanie wycinka?
Użyj Eksploratora okien działania w monitorze & zarządzanie aplikacją, aby dowiedzieć się, jak długo zajęło przetworzenie wycinka danych. Szczegóły można znaleźć w [Eksploratorze okna działania](data-factory-monitor-manage-app.md#activity-window-explorer) .

W Azure Portal można również wykonać następujące czynności:  

1. Kliknij pozycję **zestawy** danych kafelek w bloku **fabryka dane** dla fabryki danych.
2. Kliknij konkretny zestaw danych w bloku **zestawy danych** .
3. Wybierz wycinek, który Cię interesuje z listy **ostatnie wycinki** w bloku **tabeli** .
4. Kliknij przebieg działania z listy **uruchomienia działania** w bloku **wycinek danych** .
5. Kliknij kafelek **Właściwości** w bloku **szczegóły uruchomienia działania** .
6. Powinno zostać wyświetlone pole **Duration** z wartością. Ta wartość to czas potrzebny do przetworzenia wycinka.   

### <a name="how-to-stop-a-running-slice"></a>Jak zatrzymać działanie wycinka?
Jeśli zachodzi potrzeba zatrzymania potoku, można użyć polecenia cmdlet [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) . Obecnie wstrzymywanie potoków nie zatrzymuje wykonywania wycinków, które są w toku. Po zakończeniu wykonywania w toku żaden dodatkowy plasterek nie zostanie pobrany.

Jeśli na pewno chcesz natychmiast przerwać wykonywanie wszystkich wykonań, jedynym sposobem usunięcia potoku jest jego usunięcie. Jeśli zdecydujesz się usunąć potok, nie musisz usuwać tabel i połączonych usług używanych przez potok.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive