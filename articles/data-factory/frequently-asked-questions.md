---
title: 'Usługa Azure Data Factory: często zadawane pytania '
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412945"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory — często zadawane pytania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Czym jest usługa Azure Data Factory? 
Fabryka danych to w pełni zarządzana, oparta na chmurze usługa ETL integracji danych, która automatyzuje przenoszenie i przekształcanie danych. Podobnie jak fabryka, która uruchamia sprzęt do przekształcania surowców w gotowe towary, usługa Azure Data Factory organizuje istniejące usługi, które zbierają nieprzetworzone dane i przekształcają je w gotowe do użycia informacje. 

Korzystając z usługi Azure Data Factory, można tworzyć przepływy pracy oparte na danych, aby przenosić dane między magazynami danych w chmurze i w chmurze. Można też przetwarzać i przekształcać dane za pomocą przepływów danych. Usługa ADF obsługuje również zewnętrzne aparaty obliczeniowe dla przekształceń ręcznie kodowanych przy użyciu usług obliczeniowych, takich jak Usługi Azure HDInsight, Azure Databricks i środowisko uruchomieniowe integracji usług SQL Server Integration Services (SSIS). 

Za pomocą usługi Data Factory można wykonać przetwarzanie danych w usłudze w chmurze opartej na platformie Azure lub we własnym środowisku obliczeniowym hostowanym, takim jak SSIS, SQL Server lub Oracle. Po utworzeniu potoku, który wykonuje potrzebną akcję, można zaplanować jego uruchamianie okresowo (na przykład co godzinę, codziennie lub co tydzień), planowanie przedziału czasu lub wyzwalanie potoku z wystąpienia zdarzenia. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Sterowanie przepływami i skalą 
Aby obsługiwać różnorodne przepływy integracji i wzorce w nowoczesnym magazynie danych, usługa Data Factory umożliwia elastyczne modelowanie potoku danych. Pociąga to za sobą pełne sterowanie przepływem paradygmatów, które obejmują wykonanie warunkowe, rozgałęzianie w potokach danych i możliwość jawnego przekazywania parametrów wewnątrz i między tymi przepływami. Przepływ sterowania obejmuje również przekształcanie danych za pośrednictwem wysyłania działań do zewnętrznych aparatów wykonywania i możliwości przepływu danych, w tym przenoszenia danych na dużą skalę, za pośrednictwem działania Kopiowanie.

Usługa Data Factory zapewnia swobodę modelowania dowolnego stylu przepływu, który jest wymagany do integracji danych i który może być wysyłany na żądanie lub wielokrotnie zgodnie z harmonogramem. Kilka typowych przepływów, które umożliwia ten model to:   

- Przepływy kontrolne:
    - Działania mogą być połączone ze sobą w sekwencji w potoku.
    - Działania mogą być rozgałęzione w ramach potoku.
    - Parametry:
        - Parametry mogą być definiowane na poziomie potoku i argumenty mogą być przekazywane podczas wywoływania potoku na żądanie lub z wyzwalacza.
        - Działania mogą wykorzystywać argumenty przekazywane do potoku.
    - Niestandardowe przekazywanie stanu:
        - Dane wyjściowe działania, w tym stan, mogą być używane przez kolejne działanie w potoku.
    - Zapętlone pojemniki:
        - Foreach działania będzie iterować w ramach określonej kolekcji działań w pętli. 
- Przepływy oparte na wyzwalaczach:
    - Potoki mogą być wyzwalane na żądanie lub przez czas zegara ściennego.
- Przepływy delta:
    - Parametry mogą służyć do definiowania znaku wysokiej wody dla kopii delta podczas przenoszenia wymiarów lub tabel odniesienia z magazynu relacyjnego, lokalnie lub w chmurze, aby załadować dane do jeziora. 

Aby uzyskać więcej informacji, zobacz [Samouczek: Sterowanie przepływami](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dane przekształcane na dużą skalę za pomocą potoków bez kodu
Nowe środowisko narzędzi oparte na przeglądarce zapewnia tworzenie i wdrażanie potoku bez kodu z nowoczesnym, interaktywnym doświadczeniem opartym na sieci Web.

Dla deweloperów danych wizualnych i inżynierów danych interfejs użytkownika sieci web usługi Data Factory jest środowiskiem projektowania bez kodu, które będzie używane do tworzenia potoków. Jest w pełni zintegrowany z visual studio online Git i zapewnia integrację dla ciągłego wdrażania/ciągłego wdrażania i rozwoju iteracyjnego z opcjami debugowania.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Bogate, wieloplatformowe sdeksy dla zaawansowanych użytkowników
Usługa Data Factory V2 zapewnia bogaty zestaw zestawów SDK, które mogą służyć do tworzenia potoków, zarządzania nimi i monitorowania przy użyciu ulubionego środowiska IDE, w tym:
* Zestaw SDK dla języka Python
* Interfejsu wiersza polecenia programu PowerShell
* Zestaw SDK języka C#

Użytkownicy mogą również używać udokumentowanych interfejsów API REST do interfejsu z fabryką danych v2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteracyjny rozwój i debugowanie przy użyciu narzędzi wizualnych
Narzędzia wizualne usługi Azure Data Factory umożliwiają iteracyjne tworzenie i debugowanie. Można utworzyć potoki i wykonać testy przy użyciu możliwości **debugowania** w kanwie potoku bez pisania jednego wiersza kodu. Można wyświetlić wyniki przebiegów testowych w oknie **Dane wyjściowe** kanwy potoku. Po pomyślnym uruchomieniu testu można dodać więcej działań do potoku i kontynuować debugowanie w sposób iteracyjny. Można również anulować przebiegi testowe po ich zakończeniu. 

Przed wybraniem opcji **Debug**nie jest wymagane publikowanie zmian w usłudze fabryki danych. Jest to przydatne w scenariuszach, w których chcesz upewnić się, że nowe dodatki lub zmiany będą działać zgodnie z oczekiwaniami przed zaktualizowaniem przepływów pracy fabryki danych w środowiskach deweloperskich, testowych lub produkcyjnych. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Możliwość wdrażania pakietów SSIS na platformie Azure 
Jeśli chcesz przenieść swoje obciążenia SSIS, można utworzyć fabrykę danych i aprowizować środowisko uruchomieniowe integracji platformy Azure-SSIS. Środowisko uruchomieniowe integracji platformy Azure-SSIS to w pełni zarządzany klaster maszyn wirtualnych (węzłów) platformy Azure, które są przeznaczone do uruchamiania pakietów SSIS w chmurze. Aby uzyskać instrukcje krok po kroku, zobacz [wdrażanie pakietów SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) samouczek. 
 
### <a name="sdks"></a>Zestawy SDK
Jeśli jesteś zaawansowanym użytkownikiem i szukasz interfejsu programowego, usługa Data Factory udostępnia bogaty zestaw zestawów SDK, których można używać do tworzenia potoków, zarządzania nimi lub monitorowania przy użyciu ulubionego środowiska IDE. Obsługa języka obejmuje .NET, PowerShell, Python i REST.

### <a name="monitoring"></a>Monitorowanie
Fabryki danych można monitorować za pośrednictwem programu PowerShell, zestawu SDK lub narzędzi do monitorowania wizualnego w interfejsie użytkownika przeglądarki. Można monitorować i zarządzać przepływami niestandardowymi na żądanie, wyzwalaczami i zegarem w sposób wydajny i skuteczny. Anuluj istniejące zadania, zobacz błędy na pierwszy rzut oka, przejdź do szczegółów, aby uzyskać szczegółowe komunikaty o błędach, i debuguj problemy, a wszystko to z jednego okienka szkła bez przełączania kontekstu lub poruszania się między ekranami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nowe funkcje SSIS w fabryce danych
Od czasu pierwszej publicznej wersji zapoznawczej w 2017 r. usługa Data Factory dodała następujące funkcje dla SSIS:

-    Obsługa trzech kolejnych konfiguracji/wariantów usługi Azure SQL Database do obsługi bazy danych SSIS (SSISDB) projektów/pakietów:
-    Baza danych SQL z punktami końcowymi usługi sieci wirtualnej
-    Wystąpienie zarządzane
-    Elastyczna pula
-    Obsługa sieci wirtualnej usługi Azure Resource Manager na klasycznej sieci wirtualnej, która ma być przestarzała w przyszłości, co umożliwia wstrzyknięcie/dołączenie środowiska wykonawczego integracji platformy Azure-SSIS do sieci wirtualnej skonfigurowanej dla bazy danych SQL z dostępem do danych usługi sieci wirtualnej/MI/lokalnie. Aby uzyskać więcej informacji, zobacz też [Dołączanie środowiska wykonawczego integracji platformy Azure-SSIS do sieci wirtualnej.](join-azure-ssis-integration-runtime-virtual-network.md)
-    Obsługa uwierzytelniania usługi Azure Active Directory (Azure AD) i uwierzytelniania SQL w celu nawiązania połączenia z usługą SSISDB, umożliwiając uwierzytelnianie usługi Azure AD przy użyciu tożsamości zarządzanej przez usługę Data Factory dla zasobów platformy Azure
-    Obsługa lokalnej licencji programu SQL Server w celu uzyskania znacznych oszczędności dzięki opcji korzyści hybrydowej platformy Azure
-    Obsługa enterprise edition środowiska wykonawczego integracji Azure-SSIS, który umożliwia korzystanie z zaawansowanych/premium funkcji, niestandardowy interfejs konfiguracji, aby zainstalować dodatkowe składniki/rozszerzenia i ekosystem partnerów. Aby uzyskać więcej informacji, zobacz też [Enterprise Edition, Custom Setup i 3rd Party Extensibility for SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Głębsza integracja SSIS w fabryce danych, która umożliwia wywoływanie/wyzwalanie działań pakietu SSIS pierwszej klasy w potokach usługi Data Factory i planowanie ich za pośrednictwem usługi SSMS. Aby uzyskać więcej informacji, zobacz też [Modernizowanie i rozszerzanie przepływów pracy ETL/ELT o działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co to jest środowisko uruchomieniowe integracji?
Środowisko wykonawcze integracji to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przenoszenie danych**: W przypadku przenoszenia danych środowisko wykonawcze integracji przenosi dane między źródłowymi i docelowymi magazynami danych, zapewniając jednocześnie obsługę wbudowanych łączników, konwersji formatu, mapowania kolumn oraz wydajnego i skalowalnego transferu danych.
- **Działania wysyłki:** W przypadku transformacji środowisko wykonawcze integracji zapewnia możliwość natywnego wykonywania pakietów SSIS.
- **Wykonywanie pakietów SSIS:** Środowisko wykonawcze integracji natywnie wykonuje pakiety SSIS w zarządzanym środowisku obliczeniowym platformy Azure. Środowisko wykonawcze integracji obsługuje również działania związane z wysyłką i monitorowaniem transformacji uruchomione w różnych usługach obliczeniowych, takich jak Usługi Azure HDInsight, usługa Azure Machine Learning, baza danych SQL i program SQL Server.

Można wdrożyć jedno lub wiele wystąpień środowiska wykonawczego integracji zgodnie z wymaganiami do przenoszenia i przekształcania danych. Środowisko wykonawcze integracji można uruchomić w sieci publicznej platformy Azure lub w sieci prywatnej (lokalnie, Azure Virtual Network lub Amazon Web Services wirtualnej chmury prywatnej [VPC]). 

Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaki jest limit liczby uruchomień integracji?
Nie ma twardego limitu liczby wystąpień środowiska uruchomieniowego integracji, które można mieć w fabryce danych. Istnieje jednak limit liczby rdzeni maszyn wirtualnych, których środowisko wykonawcze integracji może używać na subskrypcję dla wykonywania pakietu SSIS. Aby uzyskać więcej informacji, zobacz [Limity fabryki danych](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jakie są pojęcia najwyższego poziomu usługi Azure Data Factory?
Subskrypcja platformy Azure może zawierać jedno lub więcej wystąpień usługi Azure Data Factory (lub fabryk danych). Usługa Azure Data Factory zawiera cztery kluczowe składniki, które współpracują ze sobą jako platforma, na której można redagować przepływy pracy oparte na danych z krokami przenoszenia i przekształcania danych.

### <a name="pipelines"></a>Potoki
Fabryka danych może obejmować jeden lub wiele potoków. Potok jest logiczną grupą działań do wykonywania jednostki pracy. które umożliwiają wykonanie zadania. Na przykład potok może zawierać grupę działań, które pozyskiwania danych z obiektu blob platformy Azure, a następnie uruchomić kwerendę hive w klastrze HDInsight do partycjonowania danych. Zaletą jest to, że można użyć potoku do zarządzania działaniami jako zestaw, a nie konieczności zarządzania każdym działaniem indywidualnie. Można połączyć ze sobą działania w potoku, aby obsługiwać je sekwencyjnie lub można obsługiwać je niezależnie, równolegle.

### <a name="data-flows"></a>Przepływy danych
Przepływy danych to obiekty, które są budowane wizualnie w fabryce danych, które przekształcają dane na dużą skalę w usługach platformy Spark wewnętrznej bazy danych. Nie trzeba rozumieć programowania lub spark wewnętrznych. Wystarczy zaprojektować zamiar transformacji danych przy użyciu wykresów (Mapowanie) lub arkuszy kalkulacyjnych (Wrangling).

### <a name="activities"></a>Działania
Działania reprezentują krok przetwarzania w potoku. Na przykład można użyć działania kopiowania do kopiowania danych z jednego magazynu danych do innego magazynu danych. Podobnie można użyć działania hive, który uruchamia kwerendę hive w klastrze usługi Azure HDInsight do przekształcania lub analizowania danych. Usługa Data Factory obsługuje trzy typy działań: działania przenoszenia danych, działania przekształcania danych i działania sterowania.

### <a name="datasets"></a>Zestawy danych
Zestawy danych reprezentują struktury w magazynach danych. Struktury te po prostu wskazują na dane, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. 

### <a name="linked-services"></a>Połączone usługi
Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Pomyśl o tym w ten sposób: połączona usługa definiuje połączenie ze źródłem danych, a zestaw danych reprezentuje strukturę danych. Na przykład usługa połączona usługi Azure Storage określa parametry połączenia, aby połączyć się z kontem usługi Azure Storage. Zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder zawierający dane.

Połączone usługi mają dwa cele w fabryce danych:

- Do reprezentowania *magazynu danych,* który zawiera, ale nie jest ograniczona do lokalnego wystąpienia programu SQL Server, wystąpienie bazy danych Oracle, udział plików lub konto magazynu obiektów Blob platformy Azure. Aby uzyskać listę obsługiwanych magazynów danych, zobacz [Kopiowanie aktywności w usłudze Azure Data Factory](copy-activity-overview.md).
- Reprezentowanie *zasobu obliczeniowego*, który może hostować wykonywanie działania. Na przykład działanie hive hdinsight działa w klastrze HDInsight Hadoop. Aby uzyskać listę działań transformacji i obsługiwanych środowisk obliczeniowych, zobacz [Przekształcanie danych w usłudze Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Wyzwalacze
Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy rozpocznie się wykonywanie potoku. Istnieją różne typy wyzwalaczy dla różnych typów zdarzeń. 

### <a name="pipeline-runs"></a>Uruchomienia potoków
Przebieg potoku jest wystąpieniem wykonania potoku. Zwykle wystąpienia potoku uruchamiane przez przekazywanie argumentów do parametrów, które są zdefiniowane w potoku. Argumenty można przekazać ręcznie lub w definicji wyzwalacza.

### <a name="parameters"></a>Parametry
Parametry są parami klucz-wartość w konfiguracji tylko do odczytu.Definiujesz parametry w potoku i przekazujesz argumenty dla zdefiniowanych parametrów podczas wykonywania z kontekstu uruchomienia. Kontekst uruchamiania jest tworzony przez wyzwalacz lub potok, który można wykonać ręcznie. Działania w ramach potoku wykorzystują wartości parametrów.

Zestaw danych jest silnie wpisanym parametrem i jednostką, której można użyć ponownie lub odwołać się. Działanie może odwoływać się do zestawów danych i może korzystać z właściwości, które są zdefiniowane w definicji zestawu danych.

Połączona usługa jest również silnie wpisanym parametrem, który zawiera informacje o połączeniu z magazynem danych lub środowiskiem obliczeniowym. Jest to również encja, której można użyć ponownie lub odwołać się.

### <a name="control-flows"></a>Przepływy kontrolne
Przepływy sterowania organizuje działania potoku, które obejmują działania łańcucha w sekwencji, rozgałęzienia, parametry zdefiniowane na poziomie potoku i argumenty, które są przekazywanie podczas wywoływania potoku na żądanie lub z wyzwalacza. Przepływy sterowania obejmują również niestandardowe przekazywanie stanu i zapętlanie kontenerów (czyli foreach iteratorów).


Aby uzyskać więcej informacji o pojęciach związanych z usługą Data Factory, zobacz następujące artykuły:

- [Dataset and linked services (Zestaw danych i połączone usługi)](concepts-datasets-linked-services.md)
- [Potoki i działania](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Jaki jest model cenowy usługi Data Factory?
Aby uzyskać szczegółowe informacje o cenach usługi Azure Data Factory, zobacz [Szczegóły cen usługi Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak mogę być na bieżąco z informacjami o fabryce danych?
Aby uzyskać najbardziej aktualne informacje o usłudze Azure Data Factory, przejdź do następujących witryn:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Strona główna dokumentacji](/azure/data-factory)
- [Strona główna produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Głębokie nurkowanie techniczne 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak zaplanować potok? 
Można użyć wyzwalacza harmonogramu lub wyzwalacza przedziału czasu, aby zaplanować potok. Wyzwalacz używa harmonogramu kalendarza zegara ściennego, który może okresowo lub w przypadku powtarzających się wzorców opartych na kalendarzu (na przykład w poniedziałki o 18:00 i czwartki o 21:00). Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Czy mogę przekazać parametry do uruchomienia potoku?
Tak, parametry są pierwszorzędną koncepcją najwyższego poziomu w fabryce danych. Można zdefiniować parametry na poziomie potoku i przekazać argumenty podczas wykonywania potoku uruchamianego na żądanie lub przy użyciu wyzwalacza.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Czy można zdefiniować wartości domyślne dla parametrów potoku? 
Tak. Można zdefiniować wartości domyślne dla parametrów w potokach. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Czy działanie w potoku zużywają argumenty, które są przekazywane do uruchomienia potoku? 
Tak. Każde działanie w potoku może zużywać wartość parametru, który `@parameter` jest przekazywany do potoku i uruchamiany z konstrukcją. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Czy właściwość wyjścia działania może być zużywana w innym działaniu? 
Tak. Dane wyjściowe działania mogą być używane `@activity` w kolejnych działań z konstrukcji.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak bezpiecznie obsługiwać wartości null w danych wyjściowych działania? 
Można użyć `@coalesce` konstrukcji w wyrażeniach do obsługi wartości null bezpiecznie. 

## <a name="mapping-data-flows"></a>Przepływy danych mapowania

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potrzebuję pomocy w rozwiązywaniu problemów z logiką przepływu danych. Jakie informacje muszę podać, aby uzyskać pomoc?

Gdy firma Microsoft udziela pomocy lub rozwiązywania problemów z przepływami danych, podaj skrypt przepływu danych. Jest to skrypt związany z kodem z wykresu przepływu danych. W interfejsie użytkownika ADF otwórz przepływ danych, a następnie kliknij przycisk "Skrypt" w prawym górnym rogu. Skopiuj i wklej ten skrypt lub zapisz go w pliku tekstowym.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Jak uzyskać dostęp do danych przy użyciu pozostałych 90 typów zestawów danych w fabryce danych?

Funkcja przepływu danych mapowania obecnie umożliwia usługi Azure SQL Database, usługi Azure SQL Data Warehouse, rozdzielane pliki tekstowe z magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Storage Gen2 i pliki parkietu z magazynu obiektów Blob lub Magazynu danych Lake Gen2 natywnie dla źródła i ujścia. 

Użyj copy działania do etapu danych z dowolnego z innych łączników, a następnie wykonać działanie przepływu danych do przekształcania danych po jego przesuń. Na przykład potok najpierw skopiuje się do magazynu obiektów Blob, a następnie działanie przepływu danych użyje zestawu danych w źródle do przekształcenia tych danych.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Czy środowisko uruchomieniowe integracji hostowanego samodzielnie jest dostępne dla przepływów danych?

Self-hosted IR to konstrukcja potoku ADF, której można używać z działaniem kopiowania do pozyskiwania lub przenoszenia danych do i z źródeł danych opartych na prem lub maszynach wirtualnych i pochłaniaczy. Etap danych najpierw z Kopiuj, a następnie przepływ danych do transformacji, a następnie kolejne kopii, jeśli trzeba przenieść, że przekształcone dane z powrotem do magazynu on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Czy aparat obliczeniowy przepływu danych obsługuje wielu dzierżawców?
Klastry nigdy nie są współużytkowane. Gwarantujemy izolację dla każdego zadania uruchamiane w przebiegach produkcyjnych. W przypadku scenariusza debugowania jedna osoba pobiera jeden klaster, a wszystkie debugowania zostaną udostępnione do tego klastra, które są inicjowane przez tego użytkownika.

## <a name="wrangling-data-flows"></a>Rozłączanie przepływów danych

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Jakie są obsługiwane regiony do wrangling przepływu danych?

Przepływ danych wrangling jest obecnie obsługiwany w fabrykach danych utworzonych w następujących regionach:

* Australia Wschodnia
* Kanada Środkowa
* Indie Środkowe
* Wschodnie stany USA
* Wschodnie stany USA 2
* Japonia Wschodnia
* Europa Północna
* Azja Południowo-Wschodnia
* Południowo-środkowe stany USA
* Południowe Zjednoczone Królestwo
* Zachodnio-środkowe stany USA
* Europa Zachodnia
* Zachodnie stany USA
* Zachodnie stany USA 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Jakie są ograniczenia i ograniczenia związane z przepływem danych wrangling?

Nazwy zestawów danych mogą zawierać tylko znaki alfanumeryczne. Obsługiwane są następujące magazyny danych:

* Zestaw danych DelimitedText w usłudze Azure Blob Storage przy użyciu uwierzytelniania klucza konta
* Zestaw danych DelimitedText w usłudze Azure Data Lake Storage gen2 przy użyciu uwierzytelniania klucza konta lub jednostki usługi
* Zestaw danych DelimitedText w usłudze Azure Data Lake Storage gen1 przy użyciu uwierzytelniania głównego usługi
* Usługa Azure SQL Database and Data Warehouse przy użyciu uwierzytelniania SQL. Zobacz obsługiwane typy SQL poniżej. Nie ma polybase lub przemieszczania obsługi magazynu danych.

W tej chwili integracja usługi połączonej usługi Key Vault nie jest obsługiwana w przepływach danych.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Jaka jest różnica między mapowaniem a wrangling przepływu danych?

Mapowanie przepływów danych umożliwiają przekształcanie danych na dużą skalę bez konieczności kodowania. Zadanie przekształcania danych można zaprojektować w kanwie przepływu danych, konstruując serię przekształceń. Zacznij od dowolnej liczby przekształceń źródłowych, a następnie kroków przekształcania danych. Uzupełnij przepływ danych za pomocą zlewu, aby wylądować w miejscu docelowym. Mapowanie przepływu danych jest wielki w mapowaniu i przekształcaniu danych za pomocą znanych i nieznanych schematów w ujściach i źródłach.

Wrangling przepływów danych umożliwiają agile przygotowania danych i eksploracji przy użyciu power query online edytora mashup na dużą skalę za pomocą wykonywania iskrowe. Wraz ze wzrostem jezior danych czasami wystarczy eksplorować zestaw danych lub utworzyć zestaw danych w jeziorze. Nie mapujesz do znanego obiektu docelowego. Przepływy danych wrangling są używane dla mniej formalnych i opartych na modelu scenariuszy analizy.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Jaka jest różnica między przepływami danych platformy power platform i przepływami danych?

Przepływy danych platformy Power Platform umożliwiają użytkownikom importowanie i przekształcanie danych z szerokiej gamy źródeł danych do wspólnej usługi danych i usługi Azure Data Lake w celu tworzenia aplikacji usługi PowerApps, raportów usługi Power BI lub automatyzacji przepływu. Przepływy danych platformy Power Platform korzystają z ustalonych środowisk przygotowywania danych dodatku Power Query, podobnych do usług Power BI i Excel. Przepływy danych platformy power platform umożliwiają również łatwe ponowne użycie w organizacji i automatyczne obsługę aranżacji (np. automatyczne odświeżanie przepływów danych, które zależą od innego przepływu danych po odświeżeniu poprzedniego).

Usługa Azure Data Factory (ADF) to usługa integracji danych zarządzanych, która umożliwia inżynierom danych i integratorowi danych obywatelskich tworzenie złożonych hybrydowych przepływów pracy (ETL) i wyodrębniania i przekształcania obciążenia (ELT). Wrangling przepływu danych w ADF umożliwia użytkownikom bez kodu, bezserwerowe środowisko, które upraszcza przygotowanie danych w chmurze i skaluje do dowolnego rozmiaru danych bez konieczności zarządzania infrastrukturą. Używa technologii przygotowywania danych dodatku Power Query (używanej również w przepływach danych platformy Power Platform, Excel, Power BI) do przygotowania i kształtowania danych. Stworzony do obsługi wszystkich zawiłości i wyzwań związanych z integracją dużych zbiorów danych, wrangling przepływów danych pozwala użytkownikom szybko przygotować dane na dużą skalę poprzez wykonanie iskrowe. Użytkownicy mogą tworzyć potoki danych odporne w dostępnym środowisku wizualnym za pomocą naszego interfejsu opartego na przeglądarce i pozwolić ADF obsługiwać złożoność wykonywania platformy Spark. Twórz harmonogramy dla potoków i monitoruj wykonywanie przepływów danych z portalu monitorowania usługi ADF. Łatwe zarządzanie łat. dostępności danych dzięki zaawansowanemu monitorowaniu dostępności i alertom usługi ADF oraz korzystanie z wbudowanych funkcji ciągłej integracji i wdrażania w celu zapisywania przepływów i zarządzania nimi w środowisku zarządzanym. Ustanawiaj alerty i zobacz plany wykonania, aby sprawdzić, czy logika działa zgodnie z planem podczas dostrajania przepływów danych.

### <a name="supported-sql-types"></a>Obsługiwane typy SQL

Wrangling przepływ danych obsługuje następujące typy danych w języku SQL. Zostanie wyświetlony błąd sprawdzania poprawności przy użyciu typu danych, który nie jest obsługiwany.

* short
* double
* rzeczywiste
* float
* char
* nchar
* varchar
* nvarchar
* liczba całkowita
* int
* bit
* wartość logiczna
* smallint
* tinyint
* bigint
* long
* tekst
* date
* datetime
* datetime2
* smalldatetime
* sygnatura czasowa
* uniqueidentifier
* xml

Inne typy danych będą obsługiwane w przyszłości.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia fabryki danych, zobacz następujące samouczki:

- [Szybki start: tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md)
- [Samouczek: Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
