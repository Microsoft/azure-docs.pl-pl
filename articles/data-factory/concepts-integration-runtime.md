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
ms.date: 03/26/2020
ms.openlocfilehash: ffa348c796a4d9d4e3bdb8e7ce18ba0eb82e17ad
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418390"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastruktura Integration Runtime w usłudze Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory do zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przepływ danych:** Wykonywanie [przepływu danych](concepts-data-flow-overview.md) w zarządzanym środowisku obliczeniowym platformy Azure.  
- **Przenoszenie danych**: Kopiowanie danych w magazynach danych w sieci publicznej i magazynach danych w sieci prywatnej (lokalnej lub wirtualnej sieci prywatnej). Zapewnia obsługę wbudowanych łączników, konwersji formatów i mapowania kolumn oraz wydajne i skalowalne przenoszenie danych.
- **Wysyłka działań:** Wysyłanie i monitorowanie działań transformacji uruchomionych w różnych usługach obliczeniowych, takich jak Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i inne.
- **Wykonanie pakietów SSIS**: natywne wykonywanie pakietów SQL Server Integration Services (SSIS) w zarządzanym środowisku obliczeniowym platformy Azure.

W usłudze Data Factory działanie definiuje akcję do wykonania. Połączona usługa definiuje docelowy magazyn danych lub usługę obliczeniową. Infrastruktura Integration Runtime zapewnia połączenie między działaniem i połączonymi usługami.  Odwołuje się do połączonej usługi lub działania i zapewnia środowisko obliczeniowe, w którym działanie działa włączone lub zostanie wysłane. Dzięki temu działanie można wykonać w regionie najbliższym docelowemu magazynowi danych lub usłudze obliczeniowej, w sposób najbardziej wydajny, jednocześnie spełniając wymagania dotyczące zabezpieczeń i zgodności.

## <a name="integration-runtime-types"></a>Typy infrastruktury Integration Runtime

Usługa Data Factory oferuje trzy typy środowiska wykonawczego integracji (IR) i należy wybrać typ, który najlepiej służy możliwościom integracji danych i potrzebom środowiska sieciowego, którego szukasz.  Te trzy typy to:

- Azure
- Samodzielny hosting
- Azure-SSIS

W poniższej tabeli opisano możliwości i obsługę sieci dla każdego typu infrastruktury Integration Runtime:

Typ IR | Sieć publiczna | Sieć prywatna
------- | -------------- | ---------------
Azure | Przepływ danych<br/>Przenoszenie danych<br/>Wysyłanie działania | &nbsp;
Samodzielny hosting | Przenoszenie danych<br/>Wysyłanie działania | Przenoszenie danych<br/>Wysyłanie działania
Azure-SSIS | Wykonanie pakietu SSIS | Wykonanie pakietu SSIS

Na poniższym diagramie przedstawiono, jak różnych infrastruktur Integration Runtime można użyć w sposób połączony, aby zapewnić bogate możliwości integracji danych i obsługę sieci:

![Różne typy infrastruktur Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure

Środowisko wykonawcze integracji platformy Azure może:

- Uruchamianie przepływów danych na platformie Azure 
- Uruchamianie działania kopiowania między magazynami danych w chmurze
- Wywoływanie następujących działań transformacji w sieci publicznej: Działanie Databricks Notebook/ Jar/ Python, Aktywność hive HDInsight, Aktywność HSPI, Aktywność HDInsight MapReduce, działanie HDInsight Spark, aktywność HDInsight Streaming, działanie wsadowe uczenia maszynowego, działania aktualizacji zasobów uczenia maszynowego, działanie procedury przechowywanej, aktywność U-SQL analizy Data Lake Analytics, działanie niestandardowe .NET, aktywność sieci Web, działanie wyszukiwania i działanie Pobierz metadane.

### <a name="azure-ir-network-environment"></a>Środowisko sieciowe IR Azure

Środowisko uruchomieniowe integracji platformy Azure obsługuje łączenie się z magazynami danych i oblicza usługi z publicznie dostępnymi punktami końcowymi. Zastosowanie infrastruktury Integration Runtime (Self-hosted) w środowisku sieci wirtualnej Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure
Infrastruktura Integration Runtime zapewnia w pełni zarządzane obliczenia bez serwera na platformie Azure.  Nie musisz się martwić o aprowianie infrastruktury, instalację oprogramowania, poprawki lub skalowanie pojemności.  Dodatkowo płacisz tylko za czas rzeczywistego wykorzystania.

Produkt Azure Integration Runtime zapewnia natywne możliwości obliczeniowe przenoszenia danych między magazynami danych w chmurze w sposób bezpieczny, niezawodny i wydajny.  Możesz określić liczbę jednostek integracji danych używanych w działaniu kopiowania, a rozmiar obliczeniowy infrastruktury Azure IR jest skalowany elastycznie bez konieczności jawnego regulowania rozmiaru infrastruktury Azure Integration Runtime. 

Wysyłka działania jest lekką operacją do kierowania działania do docelowej usługi obliczeniowej, więc nie trzeba skalować w górę rozmiaru obliczeń dla tego scenariusza.

Aby uzyskać informacje dotyczące tworzenia i konfigurowania usługi Azure IR, odc. jak utworzyć i skonfigurować usługę Azure IR w obszarze poradniki. 

> [!NOTE] 
> Środowisko uruchomieniowe integracji platformy Azure ma właściwości związane ze środowiska wykonawczego przepływu danych, który definiuje podstawowej infrastruktury obliczeniowej, które będą używane do uruchamiania przepływów danych na. 

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)

Infrastruktura IR (Self-hosted) oferuje następujące możliwości:

- Uruchamianie działania kopiowania między magazynami danych w chmurze i magazynem danych w sieci prywatnej.
- Wywoływanie następujących działań przekształcania względem zasobów obliczeniowych w lokalnej lub wirtualnej sieci Azure: działanie hive HDInsight (BYOC-Bring Your Own Cluster), działanie HDInsight Pig (BYOC), działanie HDInsight MapReduce (BYOC), działanie HDInsight Spark (BYOC), działanie HDInsight Streaming (BYOC), działanie wsadowe uczenia maszynowego, działania aktualizacji zasobów uczenia maszynowego, działanie procedury przechowywanej, działanie U-SQL usługi Data Analytics Lake, działanie niestandardowe (działa na platformie Azure) , Działanie odnośnika i Pobierz aktywność metadanych.

> [!NOTE] 
> Użyj środowiska uruchomieniowego integracji hostowanego samodzielnie do obsługi magazynów danych, który wymaga sterownika bring-your-own, takiego jak SAP Hana, MySQL itp.  Aby uzyskać więcej informacji, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Środowisko wykonawcze Java (JRE) jest zależnością samodzielnego hostowanego podczerwionego środowiska. Upewnij się, że masz zainstalowany środowisko JRE na tym samym hoście.

### <a name="self-hosted-ir-network-environment"></a>Własne środowisko sieciowe IR

Jeśli chcesz bezpiecznie przeprowadzić integrację danych w środowisku sieci prywatnej, które nie ma bezpośredniego zasięgu wzroku ze środowiska chmury publicznej, możesz zainstalować samodzielnie hostowane środowisko podczerwone w środowisku lokalnym za zaporą firmową lub wewnątrz wirtualnej sieci prywatnej.  Infrastruktura Integration Runtime (Self-hosted) wykonuje tylko wychodzące połączenia HTTP do otwartej sieci Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie własnego środowiska IR

Zainstaluj samodzielnie hostowane podczerwone środowisko IR na komputerze lokalnym lub na maszynie wirtualnej w sieci prywatnej. Aktualnie obsługujemy uruchamianie środowisk IR (Self-hosted) w systemie operacyjnym Windows.  

W celu zapewnienia wysokiej dostępności i skalowalności można zmienić skalowanie środowiska IR (Self-hosted), łącząc wystąpienie logiczne z wieloma maszynami lokalnymi w trybie aktywny-aktywny.  Aby uzyskać więcej informacji, zobacz jak [utworzyć i skonfigurować samodzielnie hostowane podczerwona](create-self-hosted-integration-runtime.md) artykuł w obszarze jak przewodniki po szczegóły.

## <a name="azure-ssis-integration-runtime"></a>Infrastruktura Azure SSIS Integration Runtime

Aby zmniejszyć i przenieść obecne obciążenie SSIS, można utworzyć środowisko IR Azure-SSIS w celu natywnego wykonywania pakietów SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Środowisko sieciowe IR Azure-SSIS

Środowisko IR Azure-SSIS można udostępnić w sieci publicznej lub prywatnej.  Dostęp do danych lokalnych jest obsługiwany przez połączenie środowiska IR Azure-SSIS z siecią wirtualną połączoną z siecią lokalną.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure-SSIS

Azure-SSIS IR to w pełni zarządzany klaster maszyn wirtualnych platformy Azure, których przeznaczeniem jest uruchamianie pakietów SSIS. Można przynieść własne usługi Azure SQL Database lub serwer wystąpienia zarządzanego do obsługi katalogu projektów/pakietów SSIS (SSISDB), który będzie dołączony do niego. Możesz skalować moc obliczeniową, określając rozmiar węzłów, a także liczbę węzłów w klastrze. Kosztem pracy produktu Azure-SSIS Integration Runtime można zarządzać, uruchamiając go i zatrzymując zgodnie z potrzebami.

Więcej informacji można znaleźć w artykule przewodnika How to create and configure Azure-SSIS IR (Jak utworzyć i skonfigurować środowisko Azure-SSIS IR).  Po utworzeniu możesz wdrażać istniejące pakiety SSIS i zarządzać nimi praktycznie bez zmian, przy użyciu znanych narzędzi, takich jak SQL Server Data Tools (SSDT) i SQL Server Management Studio (SSMS), co przypomina lokalne zastosowanie SSIS.

Aby uzyskać więcej informacji o środowisku uruchomieniowym Azure-SSIS, zobacz następujące artykuły: 

- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera instrukcje krok po kroku, aby utworzyć usługę Azure-SSIS IR i używa usługi Azure SQL Database do hostowania katalogu SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozwija się w samouczku i zawiera instrukcje dotyczące korzystania z wystąpienia zarządzanego bazy danych SQL azure i dołączania do podczerwenia do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 

## <a name="integration-runtime-location"></a>Lokalizacja środowiska Integration Runtime

Lokalizacja usługi Data Factory to miejsce, w którym przechowywane są metadane fabryki danych i gdzie inicjowane jest wyzwalanie potoku. Jednak fabryka danych może mieć dostęp do magazynów danych i usług obliczeniowych w innych regionach świadczenia usługi Azure, aby przenosić dane między magazynami danych lub przetwarzać dane przy użyciu usług obliczeniowych. To zachowanie jest wykonywane przez [dostępne globalnie środowisko IR](https://azure.microsoft.com/global-infrastructure/services/), co zapewnia zgodność danych, wydajność i niższe koszty wyjścia z sieci.

Lokalizacja IR definiuje lokalizację zaplecza obliczeniowego i w praktyce lokalizację, w której wykonywane jest przenoszenie danych, wysyłanie działania i wykonywanie pakietu SSIS. Lokalizacja IR może różnić się od lokalizacji fabryki danych, do której należy. 

### <a name="azure-ir-location"></a>Lokalizacja środowiska IR Azure

- W przypadku działania kopiowania podajnik ADF dołoży wszelkich starań, aby automatycznie wykryć lokalizację magazynu danych ujścia, a następnie użyj podczerwenia w tym samym regionie, jeśli jest dostępny, lub najbliższego w tej samej lokalizacji geograficznej; Jeśli region magazynu danych ujścia nie jest wykrywalny, używana jest podczerwenie w regionie fabryki danych jako alternatywa.

  Na przykład fabryka została utworzona we wschodnich stanach 
  
  - Podczas kopiowania danych do obiektu Blob platformy Azure w zachodnie stany USA, jeśli usługa ADF pomyślnie wykryła, że obiekt Blob znajduje się w zachodnie stany USA, działanie kopiowania jest wykonywane na podczerweniu IR w zachodnie stany USA; Jeśli wykrywanie regionu nie powiedzie się, działanie kopiowania jest wykonywane na podczerwony w wschodnich stanach USA.
  - Podczas kopiowania danych do salesforce, którego region nie jest wykrywalny, działanie kopiowania jest wykonywane na ir we wschodnich stanach USA.

- W przypadku działania kopiowania usługa ADF dokłada wszelkich starań, aby automatycznie wykryć magazyn danych ujścia i źródła, aby wybrać najlepszą lokalizację w tym samym regionie (jeśli jest dostępna) lub najbliższą w tej samej lokalizacji geograficznej lub jeśli nie można go wykryć, aby użyć regionu fabryki danych jako alternatywy.

- W przypadku wykonywania działań odnośników/getmetadata/usuń (nazywanych działaniami potoku), wysyłania działań transformacji (nazywanych również działaniami zewnętrznymi) i operacji tworzenia (połączenie testowe, przeglądanie listy folderów i listy tabel, dane w wersji zapoznawczej), usługa ADF używa podczerwenia w regionie fabryki danych.

- W przypadku przepływu danych podań ADF używa podczerwieni w regionie fabryki danych. 

  > [!TIP] 
  > Dobrą praktyką byłoby zapewnienie przepływu danych działa w tym samym regionie, co odpowiednie magazyny danych (jeśli to możliwe). Można to osiągnąć poprzez automatyczne rozpoznawanie usługi Azure IR (jeśli lokalizacja magazynu danych jest taka sama jak lokalizacja fabryki danych) lub tworząc nowe wystąpienie usługi Azure IR w tym samym regionie co magazyny danych, a następnie wykonać przepływ danych na nim. 

Istnieje możliwość monitorowania, która lokalizacja IR jest używana podczas wykonywania działania w widoku monitorowania działania potoku w interfejsie użytkownika lub w ładunku monitorowania działania.

### <a name="self-hosted-ir-location"></a>Lokalizacja własnego środowiska IR

Środowisko IR (Self-hosted) jest rejestrowane logicznie w usłudze Data Factory, a zasoby obliczeniowe wspierające jego funkcjonalność zapewnia użytkownik. W związku z tym nie istnieje wyraźna właściwość lokalizacji środowiska IR (Self-hosted). 

W przypadku zastosowania do wykonania przenoszenia danych, środowisko IR (Self-hosted) pobiera dane ze źródła i zapisuje je w miejscu docelowym.

### <a name="azure-ssis-ir-location"></a>Lokalizacja środowiska IR Azure-SSIS

Wybór odpowiedniej lokalizacji dla środowiska IR Azure-SSIS jest kluczowy dla osiągnięcia wysokiej wydajności obciążeń wyodrębnianie-przekształcanie-ładowanie (ETL).

- Lokalizacja usługi Azure-SSIS IR nie musi być taka sama jak lokalizacja fabryki danych, ale powinna być taka sama jak lokalizacja własnej bazy danych SQL platformy Azure lub serwera wystąpienia zarządzanego, na którym ma być hostowana usługa SSISDB. Dzięki temu środowisko IR Azure-SSIS może z łatwością uzyskać dostęp do bazy SSISDB bez wytwarzania dużego ruchu między różnymi lokalizacjami.
- Jeśli nie masz istniejącej bazy danych SQL platformy Azure lub serwera wystąpienia zarządzanego do obsługi SSISDB, ale masz lokalne źródła danych/miejsca docelowe, należy utworzyć nową usługę Azure SQL Database lub serwer wystąpienia zarządzanego w tej samej lokalizacji sieci wirtualnej połączonej z siecią lokalną.  W ten sposób można utworzyć usługę Azure-SSIS IR przy użyciu nowej bazy danych SQL azure lub serwera wystąpienia zarządzanego i dołączyć do tej sieci wirtualnej, wszystkie w tej samej lokalizacji, skutecznie minimalizując przepływy danych w różnych lokalizacjach.
- Jeśli lokalizacja istniejącej bazy danych SQL platformy Azure lub serwera wystąpienia zarządzanego, na którym jest hostowany plik SSISDB, nie jest taka sama jak lokalizacja sieci wirtualnej połączonej z siecią lokalną, najpierw utwórz usługę Azure-SSIS IR przy użyciu istniejącej bazy danych SQL platformy Azure lub serwera wystąpienia zarządzanego i dołączając do innej sieci wirtualnej w tej samej lokalizacji, a następnie skonfiguruj sieć wirtualną do połączenia sieci wirtualnej między różnymi lokalizacjami.

Na poniższym diagramie przedstawiono ustawienia lokalizacji usługi Data Factory i jej czasy uruchomienia integracji:

![Lokalizacja środowiska Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Wybór środowiska IR do użycia

### <a name="copy-activity"></a>Działanie kopiowania

Działanie kopiowania wymaga połączonych usług źródła i ujścia w celu zdefiniowania kierunku przepływu danych. Poniższa logika jest stosowana do określenia, które wystąpienie środowiska IR jest używane do wykonania kopii: 

- **Kopiowanie między dwoma źródłami danych w chmurze:** gdy usługi połączone ze źródłem i ujściem są używane przy użyciu usługi Azure IR, usługa ADF używa regionalnej usługi Azure IR, jeśli określono, lub automatycznie określi lokalizację usługi Azure IR, jeśli wybierzesz autoresolve IR (domyślnie) zgodnie z opisem w sekcji [lokalizacja środowiska wykonawczego integracji.](#integration-runtime-location)
- **Kopiowanie między źródłem danych w chmurze i źródłem danych w sieci prywatnej**: jeśli połączona usługa źródła lub ujścia wskazuje środowisko IR (Self-hosted), działanie kopiowania jest wykonywane w tym środowisku IT (Self-hosted).
- **Kopiowanie między dwoma źródłami danych w sieci prywatnej:** zarówno źródło, jak i usługa połączone z ujściem muszą wskazywać to samo wystąpienie środowiska wykonawczego integracji, a środowisko wykonawcze integracji jest używane do wykonywania działania kopiowania.

### <a name="lookup-and-getmetadata-activity"></a>Działanie wyszukiwania i uzyskiwania metadanych

Działanie wyszukiwania i uzyskiwania metadanych jest wykonywane w środowisku Integration Runtime skojarzonym z połączoną usługą magazynu danych.

### <a name="external-transformation-activity"></a>Zewnętrzne działanie transformacji

Każde działanie transformacji zewnętrznej, która korzysta z zewnętrznego aparatu obliczeniowego ma docelową usługę obliczeniową Linked Service, która wskazuje środowisko uruchomieniowe integracji. To wystąpienie środowiska uruchomieniowego integracji określa lokalizację, z której jest wywoływane to zewnętrzne działanie transformacji zakodowane ręcznie.

### <a name="data-flow-activity"></a>Działanie przepływu danych

Działania przepływu danych są wykonywane w czasie wykonywania integracji platformy Azure skojarzone z nim. Obliczenia platformy Spark używane przez przepływy danych są określane przez właściwości przepływu danych w czasie wykonywania integracji platformy Azure i są w pełni zarządzane przez usługę ADF.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Tworzenie środowiska wykonawczego integracji platformy Azure](create-azure-integration-runtime.md)
- [Tworzenie środowiska wykonawczego integracji hostowanego samodzielnie](create-self-hosted-integration-runtime.md)
- [Utwórz środowisko uruchomieniowe integracji platformy Azure-SSIS](create-azure-ssis-integration-runtime.md). Ten artykuł rozwija się w samouczku i zawiera instrukcje dotyczące korzystania z wystąpienia zarządzanego bazy danych SQL azure i dołączania do podczerwenia do sieci wirtualnej. 
