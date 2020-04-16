---
title: Wprowadzenie do usługi Azure Data Factory
description: Dowiedz się więcej o usłudze Azure Data Factory, czyli usłudze integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 327aadbc2996e91ef0f7639da86a647ddd42247c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410939"
---
# <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W świecie danych big data dane nieprzetworzone i niezorganizowane często są przechowywane w relacyjnych, nierelacyjnych i innych systemach magazynowania. Jednak same nieprzetworzone dane nie mają prawidłowego kontekstu ani znaczenia umożliwiającego zapewnienie istotnych informacji analitykom, specjalistom z zakresu danych i osobom podejmującym decyzje biznesowe. 

Dane big data wymagają usługi, która umożliwia organizację i operacjonalizację procesów mających na celu przekształcenie tych ogromnych zbiorów nieprzetworzonych danych w praktyczną analizę biznesową. Azure Data Factory to zarządzana usługa w chmurze, stworzona z myślą o tych kompleksowych, hybrydowych projektach typu wyodrębnianie-transformacja-ładowanie (ETL), wyodrębnianie-ładowanie-transformacja (ELT) i integracji danych.

Na przykład wyobraź sobie producenta gier, który zbiera petabajty dzienników gier tworzonych przez gry w chmurze. Firma chce analizować te dzienniki w celu uzyskania szczegółowych informacji dotyczących preferencji klientów, danych demograficznych oraz zachowania użytkowników podczas korzystania z gier. Dzięki temu firma będzie mogła identyfikować możliwości sprzedaży droższych produktów i sprzedaży wiązanej, opracowywać nowe atrakcyjne funkcje pozytywnie wpływające na rozwój biznesowy oraz oferować klientom udoskonalone środowisko gier.

Do analizy tych dzienników firma musi użyć danych referencyjnych, np. informacji o kliencie, grze i kampanii marketingowej, które znajdują się w lokalnym magazynie danych. Firma chce korzystać z tych danych z lokalnego magazynu danych, łącząc je z dodatkowymi danymi dzienników znajdującymi się w magazynie danych w chmurze. 

Aby wyodrębnić szczegółowe informacje, chce przetworzyć połączone dane przy użyciu klastra Spark w chmurze (Azure HDInsight), a następnie opublikować przekształcone dane w magazynie danych w chmurze, takim jak Azure SQL Data Warehouse, co ułatwi tworzenie raportów na ich podstawie. Firma zamierza zautomatyzować przepływ pracy, a także monitorować go i zarządzać nim w cyklu dziennym. Poza tym chce wykonywać przepływ pracy, gdy pliki znajdą się w kontenerze usługi Blob Storage.

Azure Data Factory to platforma, która umożliwia pracę z takimi scenariuszami danych. Jest to *oparta na chmurze usługa integracji ETL i integracji danych, która umożliwia tworzenie opartych na danych przepływów pracy w celu organizowania przenoszenia danych i przekształcania danych na dużą skalę.* Za pomocą usługi Azure Data Factory można tworzyć oparte na danych przepływy pracy (nazywane potokami) i ustalać ich harmonogram. Można tworzyć złożone procesy ETL, które przekształcają dane wizualnie za pomocą przepływów danych lub przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop, Azure Databricks i Azure SQL Database. 

Ponadto można opublikować przekształcone dane do magazynów danych, takich jak usługi Azure SQL Data Warehouse dla aplikacji analizy biznesowej (BI) do wykorzystania. Ostatecznie dzięki usłudze Azure Data Factory nieprzetworzone dane mogą być organizowane w praktyczne magazyny i usługi data lake ułatwiające podejmowanie właściwych decyzji biznesowych.

![Widok najwyższego poziomu usługi Data Factory](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Jak to działa?

Fabryka danych zawiera serię połączonych ze sobą systemów, które zapewniają kompletną platformę end-to-end dla inżynierów danych.

### <a name="connect-and-collect"></a>Łączenie i zbieranie

Przedsiębiorstwa dysponują danymi różnych typów, znajdującymi się w różnych magazynach lokalnych i w chmurze, ze strukturą pełną i częściową, przychodzącymi w różnych interwałach i w różnym tempie. 

Pierwszy krok tworzenia systemu uzyskiwania informacji polega na połączeniu wszystkich wymaganych źródeł danych i systemów przetwarzania, takich jak usługi SaaS, bazy danych, udziały plików oraz internetowe usługi FTP. Następnym krokiem jest przeniesienie danych w miarę potrzeb do centralnej lokalizacji w celu ich dalszego przetwarzania.

Firmy, które nie korzystają z usługi Data Factory, muszą tworzyć niestandardowe składniki umożliwiające przepływ danych lub projektować własne usługi, aby zintegrować źródła danych i systemy przetwarzania. Takie podejście jest kosztowne, a integracja i utrzymanie systemów stwarza trudności. Ponadto rozwiązanie to rzadko zapewnia mechanizmy kontrolne oraz funkcje monitorowania i wysyłania alertów na poziomie korporacyjnym. Możliwości te są natomiast dostępne w przypadku w pełni zarządzanej usługi.

Usługa Data Factory udostępnia [działanie kopiowania](copy-activity-overview.md) w potoku danych, które pozwala przenosić dane z lokalnych magazynów danych i źródeł danych w chmurze do centralnego magazynu danych w chmurze w celu przeprowadzenia kolejnych etapów analizy. Na przykład można zbierać dane w usłudze Azure Data Lake Storage i przekształcać dane później przy użyciu usługi obliczeniowej usługi Azure Data Lake Analytics. Można też pobrać dane z usługi Azure Blob Storage, aby przekształcić je później przy użyciu klastra usługi Azure HDInsight na platformie Hadoop.

### <a name="transform-and-enrich"></a>Przekształcanie i wzbogacanie
Po obecności danych w scentralizowanym magazynie danych w chmurze, przetwarzaj lub przekształcaj zebrane dane przy użyciu przepływów danych mapowania ADF. Przepływy danych umożliwiają inżynierom danych tworzenie i obsługę wykresów transformacji danych, które są wykonywane w programie Spark bez konieczności zrozumienia klastrów platformy Spark lub programowania platformy Spark.

Jeśli wolisz przekształcenia kodu ręcznie, ADF obsługuje działania zewnętrzne do wykonywania przekształceń w usługach obliczeniowych, takich jak HDInsight Hadoop, Spark, Data Lake Analytics i machine learning.

### <a name="cicd-and-publish"></a>CI/CD i publikować
Usługa Data Factory oferuje pełną obsługę ciągłej integracji/ciągłego wdrażania potoków danych przy użyciu usług Azure DevOps i GitHub. Dzięki temu można stopniowo rozwijać i dostarczać procesy ETL przed opublikowaniem gotowego produktu. Po przekształceniu danych pierwotnych do postaci gotowej dla działań biznesowych należy załadować je do magazynu Azure Data Warehouse, bazy danych Azure SQL Database, bazy danych Azure CosmosDB lub dowolnego aparatu analizy, z którego mogą korzystać użytkownicy biznesowi przy użyciu swoich narzędzi do analizy biznesowej.

### <a name="monitor"></a>Monitorowanie
Po pomyślnym utworzeniu i wdrożeniu potoku integracji danych, zapewniającego wartość biznesową danym przetworzonym, należy monitorować zaplanowane działania i potoki pod kątem współczynników powodzenia i niepowodzenia. Usługa Azure Data Factory dysponuje wbudowaną obsługą monitorowania potoków przy użyciu narzędzi Azure Monitor, API, PowerShell, dzienników usługi Azure Monitor i paneli kondycji w witrynie Azure Portal.

## <a name="top-level-concepts"></a>Koncepcje najwyższego poziomu
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Usługa Azure Data Factory składa się z czterech głównych składników. Ich współdziałanie pozwala udostępnić platformę umożliwiającą tworzenie opartych na danych przepływów pracy wraz z etapami służącymi do przenoszenia i przekształcania danych.

### <a name="pipeline"></a>Potok
Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują pracę. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pozwalają pozyskać dane z obiektu blob platformy Azure, a następnie uruchomić zapytanie programu Hive w klastrze usługi HDInsight w celu podzielenia danych. 

Zaletą korzystania z potoku jest możliwość zarządzania zestawem działań, a nie pojedynczymi zadaniami. Działania w potoku można ze sobą połączyć w sposób sekwencyjny, mogą też działać niezależnie i równolegle.

### <a name="mapping-data-flows"></a>Przepływy danych mapowania
Tworzenie wykresów logiki przekształcania danych i zarządzanie nimi, których można użyć do przekształcania danych o dowolnym rozmiarze. Można utworzyć bibliotekę wielokrotnego wykorzystania procedur przekształcania danych i wykonać te procesy w sposób skalowany w poziomie z potoków usługi ADF. Fabryka danych wykona logikę w klastrze platformy Spark, który obraca się w górę i obraca się w dół, gdy jest to potrzebne. Nigdy nie będziesz musiał zarządzać klastrami ani utrzymywać ich.

### <a name="activity"></a>Działanie
Działania reprezentują krok przetwarzania w potoku. Można na przykład użyć działania kopiowania w celu skopiowania danych z jednego magazynu danych do drugiego. Podobnie można użyć działania programu Hive, które uruchomi zapytanie programu Hive w klastrze usługi Azure HDInsight, aby przekształcić lub przeanalizować dane. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm ten działa następująco: połączona usługa zawiera definicję połączenia ze źródłem danych, a zestaw danych reprezentuje strukturę danych. Na przykład połączona usługa Azure Storage określa parametry połączenia, które umożliwiają połączenie z kontem usługi Azure Storage. Natomiast zestaw danych usługi Azure Blob określa kontener obiektów blob oraz folder, który zawiera dane.

Połączone usługi w usłudze Fabryka danych służą do dwóch celów:

- Reprezentowanie **magazynu danych**, w tym między innymi bazy danych na lokalnym serwerze SQL Server, bazy danych Oracle, udziału plików lub konta usługi Azure Blob Storage. Listę obsługiwanych magazynów danych zamieszczono w artykule na temat [działania kopiowania](copy-activity-overview.md).

- Reprezentowanie **zasobu obliczeniowego**, który może hostować wykonywanie działania. Na przykład działanie HDInsightHive jest wykonywane w klastrze HDInsight na platformie Hadoop. Listę działań przekształcania i obsługiwanych środowisk obliczeniowych zamieszczono w artykule dotyczącym [przekształcania danych](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy należy rozpocząć wykonanie potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń.

### <a name="pipeline-runs"></a>Uruchomienia potoków
Uruchomienie potoku to wystąpienie wykonania potoku. Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów zdefiniowanych w potokach. Argumenty można przekazać ręcznie lub w ramach definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry to pary klucz-wartość w konfiguracji tylko do odczytu.Parametry są definiowane w potoku. Argumenty dla zdefiniowanych parametrów są przekazywane w trakcie wykonania z kontekstu uruchomienia utworzonego przez wyzwalacz lub potok wykonany ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych to silnie typizowany parametr oraz obiekt wielokrotnego użytku, do którego można się odwoływać. Działanie może odwoływać się do zestawów danych oraz wykorzystywać właściwości określone w definicji zestawu danych.

Połączona usługa to również silnie typizowany parametr zawierający informacje o połączeniu z magazynem danych lub środowiskiem obliczeniowym. Również jest to obiekt wielokrotnego użytku, do którego można się odwoływać.

### <a name="control-flow"></a>Przepływ sterowania
Przepływ sterowania to inaczej organizacja działań potoku, która obejmuje działania połączone w sekwencję, rozgałęzienia, parametry zdefiniowane na poziomie potoku oraz argumenty przekazywane podczas wywoływania potoku na żądanie lub przy użyciu wyzwalacza. Zawiera również kontenery przekazywania stanów niestandardowych i pętli, czyli iteratory For-each.

### <a name="variables"></a>Zmienne
Zmienne mogą być używane wewnątrz potoków do przechowywania wartości tymczasowych i mogą być również używane w połączeniu z parametrami, aby umożliwić przekazywanie wartości między potokami, przepływami danych i innymi działaniami.

## <a name="next-steps"></a>Następne kroki
Oto ważne dokumenty następnego kroku do zbadania:

- [Dataset and linked services (Zestaw danych i połączone usługi)](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)
- [Przepływy danych mapowania](concepts-data-flow-overview.md)
- [Interfejs użytkownika usługi Data Factory w witrynie Azure Portal](quickstart-create-data-factory-portal.md)
- [Narzędzie do kopiowania danych w witrynie Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Szablon usługi Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
