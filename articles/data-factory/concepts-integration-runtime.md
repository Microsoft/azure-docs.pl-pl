---
title: Integration Runtime
description: Więcej informacji na temat infrastruktury Integration Runtime w usłudze Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: d5e20b1fc0ce32eae8dc2888fdda982f0de95d90
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636650"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastruktura Integration Runtime w usłudze Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory do zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przepływ danych** : wykonywanie [przepływu danych](concepts-data-flow-overview.md) w zarządzanym środowisku obliczeniowym platformy Azure.  
- **Przenoszenie danych** : Skopiuj dane między magazynami danych w sieci publicznej i magazynami danych w sieci prywatnej (lokalnej lub wirtualnej sieci prywatnej). Zapewnia obsługę wbudowanych łączników, konwersji formatów i mapowania kolumn oraz wydajne i skalowalne przenoszenie danych.
- **Wysyłanie działań** : wysyłanie i monitorowanie działań przekształcania działających na różnych usługach obliczeniowych, takich jak Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i innych.
- **Wykonanie pakietów SSIS** : natywne wykonywanie pakietów SQL Server Integration Services (SSIS) w zarządzanym środowisku obliczeniowym platformy Azure.

W usłudze Data Factory działanie definiuje akcję do wykonania. Połączona usługa definiuje docelowy magazyn danych lub usługę obliczeniową. Infrastruktura Integration Runtime zapewnia połączenie między działaniem i połączonymi usługami.  Jest on przywoływany przez połączoną usługę lub działanie i udostępnia środowisko obliczeniowe, w którym działanie jest uruchamiane lub wysyłane z programu. Dzięki temu działanie można wykonać w regionie najbliższym docelowemu magazynowi danych lub usłudze obliczeniowej, w sposób najbardziej wydajny, jednocześnie spełniając wymagania dotyczące zabezpieczeń i zgodności.

Środowisko Integration Runtimes można utworzyć w interfejsie użytkownika Azure Data Factory za pośrednictwem [centrum zarządzania](author-management-hub.md) oraz wszelkich działań, zestawów danych lub przepływów, które odwołują się do nich.

## <a name="integration-runtime-types"></a>Typy infrastruktury Integration Runtime

Data Factory oferuje trzy typy Integration Runtime (IR) i należy wybrać typ, który najlepiej spełnia możliwości integracji danych i wymagania dotyczące środowiska sieciowego.  Te trzy typy to:

- Azure
- Samodzielny hosting
- Azure-SSIS

W poniższej tabeli opisano możliwości i obsługę sieci dla każdego typu infrastruktury Integration Runtime:

Typ IR | Sieć publiczna | Sieć prywatna
------- | -------------- | ---------------
Azure | Przepływ danych<br/>Przenoszenie danych<br/>Wysyłanie działania | Przepływ danych<br/>Przenoszenie danych<br/>Wysyłanie działania
Samodzielny hosting | Przenoszenie danych<br/>Wysyłanie działania | Przenoszenie danych<br/>Wysyłanie działania
Azure-SSIS | Wykonanie pakietu SSIS | Wykonanie pakietu SSIS


## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure

Środowisko Azure Integration Runtime może:

- Uruchamianie przepływów danych na platformie Azure 
- Uruchamianie działania kopiowania między magazynami danych w chmurze
- Wyślij następujące działania przekształcania w sieci publicznej: operacje dotyczące notesu/jar/Python, działania programu Hive w usłudze HDInsight, działania dotyczącego usługi HDInsight, działania MapReduce, działania usługi HDInsight Spark, działania dotyczącego przesyłania strumieniowego usługi HDInsight, działania dotyczącego wykonywania Azure Machine Learning Studio wsadowego (klasycznego) partii, Data Lake Analytics Azure Machine Learning Studio działania w sieci Web, działania dotyczącego wyszukiwania oraz działania pobierania metadanych.

### <a name="azure-ir-network-environment"></a>Środowisko sieciowe IR Azure

Azure Integration Runtime obsługuje łączenie z magazynami danych i usługi obliczeniowe z dostępnymi publicznymi punktami końcowymi. Włączenie zarządzanych Virtual Network, Azure Integration Runtime obsługuje łączenie z magazynami danych przy użyciu usługi link prywatny w środowisku sieci prywatnej.

### <a name="azure-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure
Infrastruktura Integration Runtime zapewnia w pełni zarządzane obliczenia bez serwera na platformie Azure.  Nie musisz martwić się o obsługę infrastruktury, instalację oprogramowania, poprawki lub skalowanie pojemności.  Dodatkowo płacisz tylko za czas rzeczywistego wykorzystania.

Produkt Azure Integration Runtime zapewnia natywne możliwości obliczeniowe przenoszenia danych między magazynami danych w chmurze w sposób bezpieczny, niezawodny i wydajny.  Możesz określić liczbę jednostek integracji danych używanych w działaniu kopiowania, a rozmiar obliczeniowy infrastruktury Azure IR jest skalowany elastycznie bez konieczności jawnego regulowania rozmiaru infrastruktury Azure Integration Runtime. 

Wysyłanie działań jest lekkim operacją służącą do kierowania aktywności do docelowej usługi obliczeniowej, dlatego nie ma potrzeby skalowania w górę rozmiaru obliczeń dla tego scenariusza.

Aby uzyskać informacje na temat tworzenia i konfigurowania Azure IR, zobacz [jak utworzyć i skonfigurować Azure Integration Runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> Środowisko Azure Integration Runtime ma właściwości dotyczące środowiska uruchomieniowego przepływu danych, które definiuje podstawową infrastrukturę obliczeniową, która będzie używana do uruchamiania przepływów danych. 

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)

Infrastruktura IR (Self-hosted) oferuje następujące możliwości:

- Uruchamianie działania kopiowania między magazynami danych w chmurze i magazynem danych w sieci prywatnej.
- Wysyłanie następujących działań transformacji do zasobów obliczeniowych w środowisku lokalnym lub Virtual Network platformy Azure: działanie programu Hive w usłudze HDInsight (BYOC — Tworzenie własnego klastra), działanie usługi HDInsight (BYOC), Usługa HDInsight MapReduce (BYOC), działanie usługi HDInsight Spark (BYOC), działanie usługi HDInsight streaming (BYOC), działanie przetwarzania wsadowego usługi (klasyczny), działanie Azure Machine Learning Studio (klasyczne) Azure Machine Learning Studio aktualizowanie działań zasobów, działanie procedury składowanej, Data Lake Analytics działania U-SQL , Działanie niestandardowe (działa w Azure Batch), działanie Lookup i działanie Get Metadata.

> [!NOTE] 
> Korzystaj z własnego środowiska Integration Runtime, aby obsługiwać magazyny danych, które wymagają sterownika przystosowanego do oprogramowania, takiego jak SAP HANA, MySQL itp.  Aby uzyskać więcej informacji, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) to zależność środowiska IR obsługiwanego przez siebie. Upewnij się, że środowisko JRE jest zainstalowane na tym samym hoście.

### <a name="self-hosted-ir-network-environment"></a>Własne środowisko sieciowe IR

Jeśli chcesz bezpiecznie przeprowadzić integrację danych w środowisku sieci prywatnej, które nie ma bezpośredniego wglądu w środowisko chmury publicznej, możesz zainstalować własne środowisko IR w środowisku lokalnym za zaporą firmową lub w wirtualnej sieci prywatnej.  Infrastruktura Integration Runtime (Self-hosted) wykonuje tylko wychodzące połączenia HTTP do otwartej sieci Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie własnego środowiska IR

Zainstaluj własne środowisko IR na maszynie lokalnej lub maszynie wirtualnej w sieci prywatnej. Aktualnie obsługujemy uruchamianie środowisk IR (Self-hosted) w systemie operacyjnym Windows.  

W celu zapewnienia wysokiej dostępności i skalowalności można zmienić skalowanie środowiska IR (Self-hosted), łącząc wystąpienie logiczne z wieloma maszynami lokalnymi w trybie aktywny-aktywny.  Aby uzyskać więcej informacji, zobacz artykuł jak [utworzyć i skonfigurować samoobsługowe środowisko IR](create-self-hosted-integration-runtime.md) w sekcji jak przewodniki dotyczące szczegółów.

## <a name="azure-ssis-integration-runtime"></a>Infrastruktura Azure SSIS Integration Runtime

Aby zmniejszyć i przenieść obecne obciążenie SSIS, można utworzyć środowisko IR Azure-SSIS w celu natywnego wykonywania pakietów SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Środowisko sieciowe IR Azure-SSIS

Środowisko IR Azure-SSIS można udostępnić w sieci publicznej lub prywatnej.  Dostęp do danych lokalnych jest obsługiwany przez połączenie środowiska IR Azure-SSIS z siecią wirtualną połączoną z siecią lokalną.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure-SSIS

Azure-SSIS IR to w pełni zarządzany klaster maszyn wirtualnych platformy Azure, których przeznaczeniem jest uruchamianie pakietów SSIS. Możesz przenieść swoje własne Azure SQL Database lub wystąpienie zarządzane SQL dla katalogu projektów SSIS/pakietów (SSISDB). Możesz skalować moc obliczeniową, określając rozmiar węzłów, a także liczbę węzłów w klastrze. Kosztem pracy produktu Azure-SSIS Integration Runtime można zarządzać, uruchamiając go i zatrzymując zgodnie z potrzebami.

Więcej informacji można znaleźć w artykule przewodnika How to create and configure Azure-SSIS IR (Jak utworzyć i skonfigurować środowisko Azure-SSIS IR).  Po utworzeniu możesz wdrażać istniejące pakiety SSIS i zarządzać nimi praktycznie bez zmian, przy użyciu znanych narzędzi, takich jak SQL Server Data Tools (SSDT) i SQL Server Management Studio (SSMS), co przypomina lokalne zastosowanie SSIS.

Aby uzyskać więcej informacji o środowisku uruchomieniowym Azure-SSIS, zobacz następujące artykuły: 

- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](./tutorial-deploy-ssis-packages-azure.md). Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR i używania Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego SQL i przyłączania środowiska IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 

## <a name="integration-runtime-location"></a>Lokalizacja środowiska Integration Runtime

### <a name="relationship-between-factory-location-and-ir-location"></a>Relacja między lokalizacją fabryki i lokalizacją podczerwieni

Gdy klient tworzy wystąpienie usługi Data Factory, muszą określić lokalizację fabryki danych. Lokalizacja usługi Data Factory to miejsce, w którym przechowywane są metadane fabryki danych i gdzie inicjowane jest wyzwalanie potoku. Metadane dla fabryki są przechowywane tylko w wybranym regionie klienta i nie będą przechowywane w innych regionach.

Jednak fabryka danych może mieć dostęp do magazynów danych i usług obliczeniowych w innych regionach świadczenia usługi Azure, aby przenosić dane między magazynami danych lub przetwarzać dane przy użyciu usług obliczeniowych. To zachowanie jest wykonywane przez [dostępne globalnie środowisko IR](https://azure.microsoft.com/global-infrastructure/services/), co zapewnia zgodność danych, wydajność i niższe koszty wyjścia z sieci.

Lokalizacja IR definiuje lokalizację zaplecza obliczeniowego i w praktyce lokalizację, w której wykonywane jest przenoszenie danych, wysyłanie działania i wykonywanie pakietu SSIS. Lokalizacja IR może różnić się od lokalizacji fabryki danych, do której należy. 

### <a name="azure-ir-location"></a>Lokalizacja środowiska IR Azure

Można ustawić określoną lokalizację Azure IR, w którym wystąpiło wykonanie działania lub wysyłka w tym konkretnym regionie.

Jeśli zdecydujesz się używać Azure IR automatycznie Rozwiązuj w sieci publicznej, co jest ustawieniem domyślnym,

- W przypadku działania kopiowania funkcja ADF będzie najlepszym rozwiązaniem w celu automatycznego wykrycia lokalizacji magazynu danych ujścia, a następnie użycia środowiska IR w tym samym regionie, jeśli jest dostępny, lub najbliżej siebie w tej samej lokalizacji geograficznej. Jeśli nie można wykryć regionu magazynu danych ujścia, w regionie fabryki danych jest używane środowisko IR jako alternatywa.

  Na przykład Twoja fabryka została utworzona w regionie Wschodnie stany USA, 
  
  - W przypadku kopiowania danych do usługi Azure BLOB w regionie zachodnie stany USA, jeśli usługa ADF pomyślnie wykryła, że obiekt BLOB znajduje się w regionie zachodnie stany USA, działanie Copy jest wykonywane w środowisku IR w regionie zachodnie w przypadku niepowodzenia wykrywania regionu działanie Copy jest wykonywane w środowisku IR w regionie Wschodnie stany USA.
  - Podczas kopiowania danych do usługi Salesforce, do której nie można wykryć regionu, działanie Copy jest wykonywane w środowisku IR w regionie Wschodnie stany USA.

  >[!TIP] 
  >Jeśli obowiązują rygorystyczne wymagania dotyczące zgodności danych i konieczne jest zapewnienie, że dane nie opuszczą konkretnego obszaru geograficznego, można jawnie utworzyć środowisko IR Azure w konkretnym regionie i wskazać to środowisko IR dla połączonej usługi przy użyciu właściwości ConnectVia. Na przykład, jeśli chcesz skopiować dane z obiektu BLOB w Południowe Zjednoczone Królestwo do usługi Azure Synapse Analytics w Południowe Zjednoczone Królestwo i chcesz upewnić się, że dane nie opuszczają Wielkiej Brytanii, Utwórz Azure IR w Południowe Zjednoczone Królestwo i Połącz obie połączone usługi z tym środowiskiem IR.

- W przypadku wyszukiwania/GetMetadata/usuwania działania (znanych także jako działania potoku), wysyłania działań transformacji (znanych także jako działania zewnętrzne) i operacji tworzenia (połączenie testowe, lista folderów przeglądania i lista tabel, Podgląd danych), ADF używa środowiska IR w regionie usługi Data Factory.

- W przypadku przepływu danych funkcja ADF używa środowiska IR w regionie fabryki danych. 

  > [!TIP] 
  > Dobrym sposobem zapewnienia przepływu danych w tym samym regionie co odpowiednie magazyny danych (jeśli to możliwe). Można to osiągnąć przez automatyczne rozwiązanie Azure IR (Jeśli lokalizacja magazynu danych jest taka sama jak lokalizacja Data Factory) lub przez utworzenie nowego wystąpienia Azure IR w tym samym regionie, w którym znajdują się magazyny danych, a następnie wykonanie na nim przepływu danych. 

W przypadku włączenia Virtual Network zarządzanych dla Azure IR automatycznego rozwiązywania APD funkcja ADF używa środowiska IR w regionie fabryki danych. 

Istnieje możliwość monitorowania, która lokalizacja IR jest używana podczas wykonywania działania w widoku monitorowania działania potoku w interfejsie użytkownika lub w ładunku monitorowania działania.

### <a name="self-hosted-ir-location"></a>Lokalizacja własnego środowiska IR

Środowisko IR (Self-hosted) jest rejestrowane logicznie w usłudze Data Factory, a zasoby obliczeniowe wspierające jego funkcjonalność zapewnia użytkownik. W związku z tym nie istnieje wyraźna właściwość lokalizacji środowiska IR (Self-hosted). 

W przypadku zastosowania do wykonania przenoszenia danych, środowisko IR (Self-hosted) pobiera dane ze źródła i zapisuje je w miejscu docelowym.

### <a name="azure-ssis-ir-location"></a>Lokalizacja środowiska IR Azure-SSIS

Wybór odpowiedniej lokalizacji dla środowiska IR Azure-SSIS jest kluczowy dla osiągnięcia wysokiej wydajności obciążeń wyodrębnianie-przekształcanie-ładowanie (ETL).

- Lokalizacja Azure-SSIS IR nie musi być taka sama jak lokalizacja fabryki danych, ale powinna być taka sama jak lokalizacja własnego Azure SQL Database lub wystąpienia zarządzanego SQL, gdzie SSISDB. Dzięki temu środowisko IR Azure-SSIS może z łatwością uzyskać dostęp do bazy SSISDB bez wytwarzania dużego ruchu między różnymi lokalizacjami.
- Jeśli nie masz istniejącego SQL Database lub wystąpienia zarządzanego SQL, ale masz lokalne źródła danych/miejsca docelowe, należy utworzyć nowe Azure SQL Database lub wystąpienie zarządzane SQL w tej samej lokalizacji sieci wirtualnej podłączonej do sieci lokalnej.  W ten sposób można utworzyć Azure-SSIS IR przy użyciu nowego Azure SQL Database lub wystąpienia zarządzanego SQL i dołączenia do tej sieci wirtualnej, a wszystko to w tej samej lokalizacji, co skutecznie minimalizuje ruchy danych w różnych lokalizacjach.
- Jeśli lokalizacja istniejącego Azure SQL Database lub wystąpienia zarządzanego SQL nie jest taka sama jak lokalizacja sieci wirtualnej podłączonej do sieci lokalnej, najpierw utwórz Azure-SSIS IR przy użyciu istniejącego Azure SQL Database lub wystąpienia zarządzanego SQL i dołączenia do innej sieci wirtualnej w tej samej lokalizacji, a następnie skonfiguruj sieć wirtualną do połączenia sieci wirtualnej między różnymi lokalizacjami.

Na poniższym diagramie przedstawiono ustawienia lokalizacji usługi Data Factory i jej czasy uruchomienia integracji:

![Lokalizacja środowiska Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Wybór środowiska IR do użycia

### <a name="copy-activity"></a>Działanie kopiowania

Działanie kopiowania wymaga połączonych usług źródła i ujścia w celu zdefiniowania kierunku przepływu danych. Poniższa logika jest stosowana do określenia, które wystąpienie środowiska IR jest używane do wykonania kopii: 

- **Kopiowanie między dwoma źródłami danych w chmurze** : gdy są Azure IR używane połączone usługi źródłowe i ujścia, funkcja ADF używa Azure IR regionalnych, jeśli została określona, lub automatycznie określa lokalizację Azure IR w przypadku wybrania opcji automatycznego rozwiązywania problemów ze środowiskiem IR (domyślnie), zgodnie z opisem w sekcji [Lokalizacja Integration Runtime](#integration-runtime-location) .
- **Kopiowanie między źródłem danych w chmurze i źródłem danych w sieci prywatnej** : jeśli połączona usługa źródła lub ujścia wskazuje środowisko IR (Self-hosted), działanie kopiowania jest wykonywane w tym środowisku IT (Self-hosted).
- **Kopiowanie między dwoma źródłami danych w sieci prywatnej** : usługa połączona źródłowa i ujścia musi wskazywać na to samo wystąpienie środowiska Integration Runtime i czy środowisko Integration Runtime jest używane do wykonania działania kopiowania.

### <a name="lookup-and-getmetadata-activity"></a>Działanie wyszukiwania i uzyskiwania metadanych

Działanie wyszukiwania i uzyskiwania metadanych jest wykonywane w środowisku Integration Runtime skojarzonym z połączoną usługą magazynu danych.

### <a name="external-transformation-activity"></a>Zewnętrzne działanie transformacji

Każde działanie zewnętrznego przekształcenia, które wykorzystuje zewnętrzny aparat obliczeniowy, ma docelową połączoną usługę obliczeniową, która wskazuje na środowisko Integration Runtime. To wystąpienie środowiska Integration Runtime określa lokalizację, z której wysyłane jest zewnętrzne działanie transformacji kodowanej ręcznie.

### <a name="data-flow-activity"></a>Działanie przepływu danych

Działania przepływu danych są wykonywane w skojarzonym z nim środowisku Azure Integration Runtime. Obliczenia platformy Spark wykorzystywane przez przepływy danych są określane przez właściwości przepływu danych w Azure Integration Runtime i są w pełni zarządzane przez moduł ADF.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Tworzenie środowiska Azure Integration Runtime](create-azure-integration-runtime.md)
- [Tworzenie własnego środowiska Integration Runtime](create-self-hosted-integration-runtime.md)
- [Utwórz środowisko Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego SQL i przyłączania środowiska IR do sieci wirtualnej.