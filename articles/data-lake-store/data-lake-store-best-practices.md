---
title: Najlepsze rozwiązania dotyczące korzystania z Azure Data Lake Storage Gen1 | Microsoft Docs
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi pozyskiwania danych, bezpieczeństwa i wydajności związanych z używaniem Azure Data Lake Storage Gen1 (wcześniej znanych jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452227"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Najlepsze rozwiązania dotyczące korzystania z Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Ten artykuł zawiera informacje o najlepszych rozwiązaniach i kwestiach związanych z pracą z usługą Azure Data Lake Storage Gen1. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajności, odporności i monitorowania Data Lake Storage Gen1. Przed Data Lake Storage Gen1, praca z naprawdę dużą ilością danych w usługach, takich jak usługa Azure HDInsight, została złożona. Fragmentu dane na wielu kontach magazynu obiektów blob, dzięki czemu można osiągnąć magazyn petabajtów i optymalną wydajność w tej skali. W przypadku Data Lake Storage Gen1 większość stałych limitów rozmiaru i wydajności jest usuwana. Jednak nadal istnieją pewne zagadnienia, które opisano w tym artykule, aby uzyskać najlepszą wydajność dzięki Data Lake Storage Gen1.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Azure Data Lake Storage Gen1 oferuje kontrolki dostępu POSIX i szczegółową inspekcję dla Azure Active Directory (Azure AD) użytkowników, grup i jednostek usługi. Te kontrolki dostępu można ustawić na istniejące pliki i foldery. Kontroli dostępu można także użyć do tworzenia wartości domyślnych, które mogą być stosowane do nowych plików lub folderów. Gdy uprawnienia są ustawione na istniejące foldery i obiekty podrzędne, uprawnienia muszą być rekursywnie rozpropagowane dla każdego obiektu. W przypadku dużej liczby plików propagowanie uprawnień może zająć dużo czasu. Czas trwania może mieć zakres od 30-50 do przetworzenia obiektów na sekundę. W związku z tym należy odpowiednio zaplanować strukturę folderów i grupy użytkowników. W przeciwnym razie może to spowodować nieoczekiwane opóźnienia i problemy podczas pracy z danymi.

Załóżmy, że masz folder z 100 000 obiektami podrzędnymi. Jeśli pozostanie Dolna granica 30 obiektów przetworzonych na sekundę, zaktualizowanie uprawnienia dla całego folderu może potrwać godzinę. Więcej szczegółów na temat list ACL Data Lake Storage Gen1 są dostępne w [programie w programie Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Aby zwiększyć wydajność rekursywnie przypisywania list ACL, można użyć narzędzia Command-Line Azure Data Lake. Narzędzie tworzy wiele wątków i cykliczną logikę nawigacji, aby szybko zastosować listy ACL do milionów plików. Narzędzie jest dostępne dla systemów Linux i Windows, a [Dokumentacja](https://github.com/Azure/data-lake-adlstool) i [materiały do pobrania](https://aka.ms/adlstool-download) dla tego narzędzia można znaleźć w witrynie GitHub. Te same ulepszenia wydajności mogą być włączane przez własne narzędzia, które są przeznaczone dla zestawów SDK Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) i [Java](data-lake-store-get-started-java-sdk.md) .

### <a name="use-security-groups-versus-individual-users"></a>Korzystanie z grup zabezpieczeń i poszczególnych użytkowników

Podczas pracy z danymi Big Data w Data Lake Storage Gen1 najprawdopodobniej jednostka usługi jest używana do zezwalania usługom, takim jak usługa Azure HDInsight, na pracę z danymi. Mogą jednak wystąpić sytuacje, w których indywidualni użytkownicy potrzebują dostępu do danych. W takich przypadkach należy używać [grup zabezpieczeń](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) Azure Active Directory zamiast przypisywania poszczególnych użytkowników do folderów i plików.

Gdy grupa zabezpieczeń ma przypisane uprawnienia, Dodawanie lub usuwanie użytkowników z grupy nie wymaga żadnych aktualizacji do Data Lake Storage Gen1. Zapewnia to również, że nie zostanie przekroczony limit [32 dostępu i domyślnych list ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) (obejmuje cztery listy ACL w stylu POSIX, które zawsze są skojarzone z każdym plikiem i folderem: [użytkownik](data-lake-store-access-control.md#the-owning-user)będący właścicielem, [Grupa będąca właścicielem](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask)i inne).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Zgodnie z opisem, gdy użytkownicy potrzebują dostępu do Data Lake Storage Gen1, najlepszym rozwiązaniem jest użycie Azure Active Directory grup zabezpieczeń. Niektóre zalecane grupy do uruchomienia programu mogą mieć wartość **ReadOnlyUsers**, **WriteAccessUsers** i **FullAccessUsers** dla katalogu głównego konta, a nawet oddzielne dla podfolderów kluczy. Jeśli istnieją inne przewidywane grupy użytkowników, które mogą zostać później dodane, ale nie zostały jeszcze zidentyfikowane, warto rozważyć utworzenie fikcyjnych grup zabezpieczeń, które mają dostęp do niektórych folderów. Korzystanie z grupy zabezpieczeń zapewnia, że w przyszłości nie jest potrzebny długi czas przetwarzania do przypisywania nowych uprawnień do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla podmiotów usługi

Nazwy główne usług Azure Active Directory są zwykle używane przez usługi, takie jak usługa Azure HDInsight, aby uzyskiwać dostęp do danych w Data Lake Storage Gen1. W zależności od wymagań dostępu dla wielu obciążeń mogą wystąpić pewne zagadnienia w celu zapewnienia bezpieczeństwa wewnątrz i na zewnątrz organizacji. W przypadku wielu klientów jedna jednostka usługi Azure Active Directory może być odpowiednia i może mieć pełne uprawnienia w katalogu głównym konta Data Lake Storage Gen1. Inni klienci mogą wymagać wielu klastrów z różnymi jednostkami usługi, w których jeden klaster ma pełny dostęp do danych, i inny klaster z dostępem tylko do odczytu. Podobnie jak w przypadku grup zabezpieczeń, można rozważyć utworzenie nazwy głównej usługi dla każdego przewidywanego scenariusza (odczyt, zapis, pełny) po utworzeniu konta Data Lake Storage Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Włączanie zapory Data Lake Storage Gen1 z dostępem do usługi platformy Azure

Data Lake Storage Gen1 obsługuje opcję włączania zapory i ograniczania dostępu tylko do usług platformy Azure, co jest zalecane w przypadku mniejszych wektorów ataków z zewnątrz. Zaporę można włączyć na koncie Data Lake Storage Gen1 w Azure Portal za pomocą **zapory**  >  **Włącz zaporę (włączona)**  >  **Zezwalaj na dostęp do opcji usług platformy Azure** .

![Ustawienia zapory w Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Ustawienia zapory w Data Lake Storage Gen1")

Po włączeniu Zapory tylko usługi platformy Azure, takie jak HDInsight, Data Factory, Azure Synapse Analytics itp., mają dostęp do Data Lake Storage Gen1. Ze względu na wewnętrzne tłumaczenie adresów sieciowych używane przez platformę Azure Zapora Data Lake Storage Gen1 nie obsługuje ograniczania określonych usług według adresu IP i jest przeznaczona tylko do ograniczeń punktów końcowych poza platformą Azure, na przykład lokalnie.

## <a name="performance-and-scale-considerations"></a>Zagadnienia dotyczące wydajności i skalowalności

Jedną z najbardziej zaawansowanych funkcji Data Lake Storage Gen1 jest to, że eliminuje stałe limity przepływności danych. Usunięcie limitów pozwala klientom zwiększyć rozmiar danych i wymagania dotyczące wydajności bez konieczności fragmentu danych. Jednym z najważniejszych zagadnień związanych z optymalizacją Data Lake Storage Gen1 wydajności jest to, że jest to wykonywane optymalnie w przypadku zapewnienia równoległości.

### <a name="improve-throughput-with-parallelism"></a>Zwiększanie przepływności z równoległością

Rozważ udzielenie 8-12 wątków na rdzeń w celu uzyskania optymalnej przepływności odczytu/zapisu. Przyczyną jest blokowanie operacji odczytu/zapisu w pojedynczym wątku, a więcej wątków może pozwolić na większą współbieżność maszyny wirtualnej. Aby zapewnić, że poziomy są prawidłowe i równoległe, należy monitorować wykorzystanie procesora przez maszynę wirtualną.

### <a name="avoid-small-file-sizes"></a>Unikaj małych rozmiarów plików

Uprawnienia i inspekcja w systemie POSIX w Data Lake Storage Gen1 są związane z rozrzutem, który staje się widoczny podczas pracy z wieloma małymi plikami. Najlepszym rozwiązaniem jest przetwarzanie danych wsadowych do większych plików, a także pisanie tysięcy lub milionów małych plików do Data Lake Storage Gen1. Uniknięcie małych rozmiarów plików może mieć wiele korzyści, takich jak:

* Obniżenie kontroli uwierzytelniania w wielu plikach
* Zmniejszone połączenia otwartych plików
* Szybsze kopiowanie/replikacja
* Mniejsza liczba plików do przetworzenia podczas aktualizowania uprawnień Data Lake Storage Gen1 POSIX

W zależności od tego, jakie usługi i obciążenia korzystają z danych, dobrym rozmiarem do uwzględnienia w przypadku plików jest 256 MB lub więcej. Jeśli nie można wykonać operacji wsadowych rozmiarów plików podczas wypełniania w Data Lake Storage Gen1, można mieć oddzielne zadanie kompaktowania, które łączy te pliki w większe. Aby uzyskać więcej informacji i zaleceń dotyczących rozmiarów plików i organizowania danych w Data Lake Storage Gen1, zobacz [Struktura zestawu danych](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Duże rozmiary plików i potencjalny wpływ na wydajność

Mimo że Data Lake Storage Gen1 obsługuje duże pliki o rozmiarze do petabajtów w celu uzyskania optymalnej wydajności i w zależności od procesu odczytującego dane, może nie być idealnym rozwiązaniem niż 2 GB. Na przykład w przypadku korzystania z programu **pomocą distcp** do kopiowania danych między lokalizacjami lub różnymi kontami magazynu, pliki są Finest poziom szczegółowości używany do określania zadań mapy. Tak więc, jeśli kopiujesz 10 plików, które są 1 TB, z których każde zostanie przydzielono maksymalnie 10 mapowania. Ponadto, jeśli masz wiele plików z przypisanymi mapowaniami, początkowo mapowania działają równolegle, aby przenieść duże pliki. Jednak, gdy zadanie zaczyna kończyć się tylko kilkoma mapowaniami pozostanie przydzielone i można je zapamiętać przy użyciu pojedynczego mapowania przypisanego do dużego pliku. Firma Microsoft przesłała udoskonalenia pomocą distcp, aby rozwiązać ten problem w przyszłych wersjach usługi Hadoop.

Innym przykładem, który należy wziąć pod uwagę, jest użycie Azure Data Lake Analytics z Data Lake Storage Gen1. W zależności od przetwarzania wykonywanego przez Ekstraktor, niektóre pliki, których nie można podzielić (na przykład XML, JSON), mogą mieć wpływ na wydajność, gdy jest większy niż 2 GB. W przypadkach, gdy pliki można podzielić przez Ekstraktor (na przykład CSV), preferowane są duże pliki.

### <a name="capacity-plan-for-your-workload"></a>Plan pojemności dla obciążenia

Azure Data Lake Storage Gen1 usuwa limity ograniczania liczby twardych operacji we/wy, które są umieszczane na kontach magazynu obiektów BLOB. Jednak nadal istnieją limity nietrwałe, które należy wziąć pod uwagę. Domyślne limity ograniczenia ruchu przychodzącego/wychodzącego spełniają wymagania większości scenariuszy. Jeśli obciążenie musi mieć zwiększony limit, Pracuj z pomocą techniczną firmy Microsoft. Należy również przyjrzeć się limitom na etapie weryfikacji koncepcji, aby nie trafiać limitów ograniczenia operacji we/wy podczas produkcji. W takim przypadku może zajść konieczność poczekania na ręczne zwiększenie zespołu inżynierów firmy Microsoft. Jeśli nastąpi ograniczenie we/wy, Azure Data Lake Storage Gen1 zwraca kod błędu 429 i najlepszym rozwiązaniem powinna być ponowna próba przy użyciu odpowiednich wykładniczych zasad wycofywania.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optymalizuj "zapisy" przy użyciu buforu sterowników Data Lake Storage Gen1

Aby zoptymalizować wydajność i zmniejszyć liczbę operacji we/wy podczas zapisywania do Data Lake Storage Gen1 z usługi Hadoop, należy wykonać operacje zapisu jak najbliżej rozmiaru buforu sterownika Data Lake Storage Gen1, jak to możliwe. Nie można przekroczyć rozmiaru buforu przed użyciem wartości opróżniania, na przykład podczas przesyłania strumieniowego przy użyciu Apache Storm lub obciążeń przesyłania strumieniowego Spark. Podczas zapisywania do Data Lake Storage Gen1 z usługi HDInsight/Hadoop ważne jest, aby wiedzieć, że Data Lake Storage Gen1 ma sterownik z buforem 4 MB. Podobnie jak w przypadku wielu sterowników systemu plików, bufor ten może być ręcznie opróżniany przed osiągnięciem rozmiaru 4 MB. W przeciwnym razie jest on natychmiast opróżniany do magazynu, jeśli kolejny zapis przekracza maksymalny rozmiar buforu. Tam, gdzie to możliwe, należy unikać przepełnienia lub silnego przynoszącej bufor podczas synchronizowania/opróżniania zasad według liczby lub przedziału czasu.

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Podczas tworzenia architektury systemu przy użyciu Data Lake Storage Gen1 lub dowolnej usługi w chmurze należy wziąć pod uwagę wymagania dotyczące dostępności i sposób reagowania na potencjalne przerwy w świadczeniu usługi. Problem może być zlokalizowany w konkretnym wystąpieniu lub nawet w całym regionie, więc należy mieć plan dla obu tych wystąpień. W zależności od **celu czasu odzyskiwania** i **celu punktu odzyskiwania** umowy SLA obciążenia można wybrać bardziej lub mniej agresywną strategię w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoką dostępność (HA) i odzyskiwanie po awarii (DR) może być czasami połączone ze sobą, Chociaż każda z nich ma nieco inną strategię, szczególnie w przypadku, gdy zawiera dane. Data Lake Storage Gen1 już obsługuje replikację 3. pod okapem, aby chronić przed zlokalizowanymi awariami sprzętowymi. Jednak ponieważ replikacja między regionami nie jest wbudowana, należy ją zarządzać. Podczas kompilowania planu o wysokiej dostępności w przypadku przerwania działania usługi obciążenie musi mieć dostęp do najnowszych danych tak szybko, jak to możliwe, przez przełączenie na lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii, aby przygotować się do mało prawdopodobnego uszkodzenia regionu, ważne jest również, aby dane były replikowane do innego regionu. Te dane mogą początkowo być takie same jak zreplikowane dane HA. Należy jednak wziąć pod uwagę wymagania dotyczące przypadków granicznych, takich jak uszkodzenie danych, w przypadku których warto utworzyć okresowe migawki, aby wrócić do programu. W zależności od ważności i rozmiaru danych należy rozważyć stopniowe przyrostowe migawki z 1-, 6-i 24-godzinnego okresu magazynu lokalnego i/lub pomocniczego, zgodnie z tolerancjami związanymi z ryzykiem.

W celu uzyskania odporności danych z Data Lake Storage Gen1 zaleca się geograficzną replikację danych do oddzielnego regionu z częstotliwością, która spełnia wymagania dotyczące HA/DR, najlepiej co godzinę. Ta częstotliwość replikacji minimalizuje ogromne przepływy danych, które mogą mieć konkurencyjne wymagania dotyczące przepływności w systemie głównym i lepszy cel punktu odzyskiwania (RPO). Ponadto należy rozważyć sposób, w jaki aplikacja używa Data Lake Storage Gen1, aby automatycznie przechodzić do trybu failover na koncie pomocniczym przez wyzwalacze monitorowania lub długość nieudanych prób lub co najmniej wysyłać powiadomienia do administratorów w celu ręcznej interwencji. Należy pamiętać, że istnieje kompromis między przechodzeniem w tryb failover a oczekiwaniem na powrót usługi do trybu online. Jeśli dane nie zakończyły się replikacją, przejście w tryb failover może spowodować utratę danych, niespójność lub złożone scalanie danych.

Poniżej znajdują się trzy najbardziej zalecane opcje organizowania replikacji między kontami Data Lake Storage Gen1 i kluczowe różnice między nimi.

|  |Pomocą distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limity skalowania**     | Ograniczone przez węzły procesu roboczego        | Ograniczone przez maksymalną liczbę jednostek przenoszenia danych w chmurze        | Powiązane przez jednostki analiz        |
|**Obsługuje kopiowanie różnic**     |   Tak      | Nie         | Nie         |
|**Wbudowana aranżacja**     |  Nie (Użyj Oozie Flow lub zadań firmy CRONUS)       | Tak        | Nie (Użyj Azure Automation lub Harmonogram zadań systemu Windows)         |
|**Obsługiwane systemy plików**     | ADL, HDFS, WASB, S3, GS, CFS        |Wiele, zobacz [Łączniki](../data-factory/connector-azure-blob-storage.md).         | ADL do ADL, WASB do ADL (tylko ten sam region)        |
|**Obsługa systemu operacyjnego**     |Dowolny system operacyjny z uruchomioną usługą Hadoop         | Nie dotyczy          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Użyj pomocą distcp do przenoszenia danych między dwiema lokalizacjami

Krótkie dla kopii rozproszonej, pomocą distcp to narzędzie wiersza polecenia systemu Linux, które jest dostarczane z usługą Hadoop i zapewnia rozproszone przenoszenie danych między dwiema lokalizacjami. Te dwie lokalizacje mogą być Data Lake Storage Gen1, HDFS, WASB lub S3. To narzędzie używa zadań MapReduce w klastrze Hadoop (na przykład HDInsight) do skalowania w poziomie na wszystkich węzłach. Pomocą distcp jest uznawany za najszybszy sposób przenoszenia danych Big Data bez specjalnych urządzeń do kompresji sieciowej. Pomocą distcp udostępnia również opcję aktualizowania różnic między dwiema lokalizacjami, obsługuje automatyczne ponawianie prób, a także dynamiczne skalowanie obliczeń. Takie podejście jest niezwykle wydajne, gdy chodzi o replikację elementów, takich jak tabele Hive/Spark, które mogą mieć wiele dużych plików w jednym katalogu i chcesz skopiować tylko zmodyfikowane dane. Z tego względu pomocą distcp jest najbardziej Zalecanym narzędziem do kopiowania danych między magazynami danych Big Data.

Zadania kopiowania mogą być wyzwalane przez przepływy pracy Apache Oozie korzystające z częstotliwości lub wyzwalaczy danych, a także zadań z systemem Linux. W przypadku zadań związanych z replikacją intensywną zaleca się sporządzenie oddzielnego klastra usługi HDInsight Hadoop, który można dostrajać i skalować specjalnie dla zadań kopiowania. Dzięki temu zadania kopiowania nie zakłócają zadań krytycznych. W przypadku przeprowadzania replikacji na poziomie wystarczającej częstotliwości klaster może nawet zostać przełączony między zadaniami. W przypadku przełączenia w tryb failover do regionu pomocniczego upewnij się, że inny klaster jest również w regionie pomocniczym, aby replikować nowe dane z powrotem do głównego konta Data Lake Storage Gen1 po utworzeniu kopii zapasowej. Aby zapoznać się z przykładami używania pomocą distcp, zobacz [Używanie pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Planowanie zadań kopiowania przy użyciu Azure Data Factory

Azure Data Factory można również użyć do zaplanowania zadań kopiowania przy użyciu **działania kopiowania** i nawet można je skonfigurować na częstotliwości za pośrednictwem **Kreatora kopiowania**. Pamiętaj, że Azure Data Factory ma limit jednostek przenoszenia danych w chmurze (DMUs), a ostatecznie Wykorzystaj przepływność/obliczenia dla obciążeń dużych ilości danych. Ponadto Azure Data Factory obecnie nie oferuje aktualizacji różnicowych między kontami Data Lake Storage Gen1, dlatego foldery takie jak tabele Hive wymagają pełnej kopii do replikacji. Więcej informacji na temat kopiowania przy użyciu Data Factory można znaleźć w [przewodniku strojenia działania kopiowania](../data-factory/copy-activity-performance.md) .

### <a name="adlcopy"></a>AdlCopy

AdlCopy to narzędzie wiersza polecenia systemu Windows, które umożliwia kopiowanie danych między dwoma kontami Data Lake Storage Gen1 tylko w tym samym regionie. Narzędzie AdlCopy udostępnia autonomiczną opcję lub opcję użycia konta Azure Data Lake Analytics do uruchomienia zadania kopiowania. Chociaż wcześniej była skompilowana dla kopii na żądanie, a nie do niezawodnej replikacji, udostępnia kolejną opcję kopiowania rozproszonego między kontami Data Lake Storage Gen1 w tym samym regionie. W celu zapewnienia niezawodności zaleca się użycie opcji Premium Data Lake Analytics w przypadku wszystkich obciążeń produkcyjnych. Wersja autonomiczna może zwracać zajęte odpowiedzi i ma ograniczoną skalę i monitorowanie.

Podobnie jak w przypadku pomocą distcp, AdlCopy musi być zorganizowany przez coś, co Azure Automation lub Harmonogram zadań Windows. Podobnie jak w przypadku Data Factory, AdlCopy nie obsługuje kopiowania tylko zaktualizowanych plików, ale kopiuje i zastępuje istniejące pliki. Aby uzyskać więcej informacji i przykłady korzystania z AdlCopy, zobacz [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Zagadnienia dotyczące monitorowania

Data Lake Storage Gen1 udostępnia szczegółowe dzienniki i inspekcje diagnostyczne. Data Lake Storage Gen1 zawiera podstawowe metryki w Azure Portal pod kontem Data Lake Storage Gen1 i w Azure Monitor. W Azure Portal zostanie wyświetlona dostępność Data Lake Storage Gen1. Ta Metryka jest jednak odświeżana co siedem minut i nie można wykonać zapytania za pomocą publicznie uwidocznionego interfejsu API. Aby uzyskać aktualną dostępność konta Data Lake Storage Gen1, musisz uruchomić własne testy syntetyczne w celu sprawdzenia dostępności. Inne metryki, takie jak łączne wykorzystanie magazynu, żądania odczytu/zapisu i ruch przychodzący/wychodzący mogą trwać do 24 godzin. Dlatego bardziej aktualne metryki muszą być obliczane ręcznie za poorednictwem narzędzi wiersza polecenia usługi Hadoop lub agregowania informacji o dziennikach. Najszybszym sposobem na uzyskanie najnowszego wykorzystania magazynu jest uruchomienie tego polecenia HDFS z węzła klastra usługi Hadoop (na przykład węzła głównego):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Eksportowanie Data Lake Storage Gen1 diagnostyki

Jednym z najszybszych metod uzyskiwania dostępu do dzienników z możliwością wyszukiwania z Data Lake Storage Gen1 jest włączenie wysyłania dziennika do **log Analytics** w bloku **diagnostyka** dla konta Data Lake Storage Gen1. Zapewnia to natychmiastowy dostęp do dzienników przychodzących z filtrami czasu i zawartości oraz opcjami alertów (poczta e-mail/webhook) wyzwalanych w ciągu 15 minut. Aby uzyskać instrukcje, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Aby uzyskać więcej alertów w czasie rzeczywistym i więcej kontroli nad miejscem, w którym znajdują się dzienniki, rozważ wyeksportowanie dzienników do usługi Azure EventHub, w której zawartość może być analizowana pojedynczo lub w przedziale czasu, aby przesłać powiadomienia w czasie rzeczywistym do kolejki. Oddzielna aplikacja, taka jak [aplikacja logiki](../connectors/connectors-create-api-azure-event-hubs.md) , może następnie wykorzystać i przekazać alerty do odpowiedniego kanału, a także przesłać metryki do narzędzi monitorowania, takich jak newrelic, usługi Datadog lub AppDynamics. Alternatywnie, jeśli używasz narzędzia innej firmy, takiego jak ElasticSearch, możesz wyeksportować dzienniki, aby Blob Storage i użyć [wtyczki logstash platformy Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) , aby wykorzystać dane w stosie Elasticsearch, Kibana i LOGSTASH (Elk).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Włączanie rejestrowania na poziomie debugowania w usłudze HDInsight

Jeśli wysyłanie dziennika Data Lake Storage Gen1 nie jest włączone, usługa Azure HDInsight umożliwia również włączenie [rejestrowania po stronie klienta dla Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) za pośrednictwem Log4J. Należy ustawić następującą właściwość w konfiguracji   >  **przędzy** Ambari  >    >  **Advanced przędz-Log4J Configurations**:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Po ustawieniu właściwości i ponownym uruchomieniu węzłów Data Lake Storage Gen1 Diagnostyka jest zapisywana w dziennikach PRZĘDZy w węzłach (/tmp/ \<user\> /Yarn.log), a także ważne szczegóły, takie jak błędy lub ograniczanie przepustowości (kod błędu HTTP 429). Te same informacje mogą być również monitorowane w dziennikach Azure Monitor lub wszędzie tam, gdzie dzienniki są wysyłane do programu w bloku [Diagnostyka](data-lake-store-diagnostic-logs.md) konta Data Lake Storage Gen1. Zaleca się włączenie co najmniej rejestrowania po stronie klienta lub użycie opcji wysyłania dziennika przy użyciu Data Lake Storage Gen1 do wglądu operacyjnego i łatwiejszego debugowania.

### <a name="run-synthetic-transactions"></a>Uruchamianie transakcji syntetycznych

Obecnie Metryka dostępności usługi dla Data Lake Storage Gen1 w Azure Portal ma 7-minutowy odświeżanie okna. Ponadto nie można wykonać zapytania przy użyciu publicznie uwidocznionego interfejsu API. W związku z tym zaleca się utworzenie podstawowej aplikacji, która wykonuje transakcje syntetyczne Data Lake Storage Gen1, które mogą zapewnić dostępność na minutę. Przykładem może być utworzenie zadania WebJob, aplikacji logiki lub aplikacja funkcji platformy Azure w celu przeczytania, utworzenia i zaktualizowania Data Lake Storage Gen1 i wysłania wyników do rozwiązania do monitorowania. Operacje można wykonać w folderze tymczasowym, a następnie usunąć po teście, co może być uruchamiane co 30-60 sekund, w zależności od wymagań.

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogu

Podczas wypełniania danych do programu Data Lake należy wstępnie zaplanować strukturę danych, dzięki czemu można efektywnie wykorzystać zabezpieczenia, partycjonowanie i przetwarzanie. Wiele z poniższych zaleceń można użyć niezależnie od tego, czy jest to Azure Data Lake Storage Gen1, Blob Storage czy HDFS. Każde obciążenie ma inne wymagania dotyczące sposobu, w jaki dane są używane, ale poniżej przedstawiono kilka typowych układów, które należy wziąć pod uwagę podczas pracy z scenariuszami IoT i Batch.

### <a name="iot-structure"></a>Struktura IoT

W przypadku obciążeń IoT może istnieć duża ilość danych przechowywanych w magazynie danych obejmujących wiele produktów, urządzeń, organizacji i klientów. Ważne jest wstępne zaplanowanie układu katalogu dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla odbiorców w dół. Ogólny szablon, który ma zostać rozważenia, może być następujący:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Na przykład dane telemetryczne dla aparatu samolotowego w Wielkiej Brytanii mogą wyglądać następująco:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Istnieje istotny powód, aby umieścić datę na końcu struktury folderów. Jeśli chcesz zablokować określone regiony lub zagadnienia dotyczące użytkowników/grup, możesz łatwo to zrobić przy użyciu uprawnień POSIX. W przeciwnym razie, jeśli istnieje potrzeba ograniczenia pewnej grupy zabezpieczeń do wyświetlania tylko danych z Wielkiej Brytanii lub określonych płaszczyzn, w przypadku dużej ilości folderów w każdej godzinie musi być wymagana struktura daty. Ponadto, jeśli struktura daty na początku zwiększy liczbę folderów jako czas oczekiwania.

### <a name="batch-jobs-structure"></a>Struktura zadań wsadowych

Na wysokim poziomie często stosowane podejście do przetwarzania wsadowego polega na wykorzystaniu danych w folderze "in". Następnie po przetworzeniu danych należy umieścić nowe dane w folderze "out", aby można było użyć procesów podrzędnych. Ta struktura katalogów jest czasami widoczna dla zadań wymagających przetwarzania poszczególnych plików i może nie wymagać masowego przetwarzania równoległego w dużych zestawach danych. Podobnie jak struktura IoT zalecana powyżej, dobrą strukturą katalogów są foldery na poziomie nadrzędnym dla elementów, takich jak region i zagadnienia dotyczące tematu (na przykład organizacja, produkt/producent). Ta struktura ułatwia zabezpieczanie danych w organizacji i lepsze zarządzanie danymi w obciążeniach. Ponadto należy rozważyć datę i godzinę w strukturze, aby umożliwić lepszą organizację, wyszukiwanie filtrowane, zabezpieczenia i automatyzację w przetwarzaniu. Poziom szczegółowości dla struktury daty zależy od interwału, w którym dane są przekazywane lub przetwarzane, na przykład co godzinę, codziennie lub nawet miesięcznie.

Czasami przetwarzanie plików nie powiedzie się z powodu uszkodzenia danych lub nieoczekiwanych formatów. W takich przypadkach struktura katalogów może korzystać z folderu **/Bad** , aby przenieść pliki do dalszej inspekcji. Zadanie usługi Batch może także obsłużyć raportowanie lub powiadomienie o tych *nieprawidłowych* plikach w ramach interwencji ręcznej. Weź pod uwagę następującą strukturę szablonów:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Na przykład firma marketingowa otrzymuje codzienne wyodrębnianie danych aktualizacji klientów od klientów w Ameryka Północna. Może wyglądać podobnie do poniższego fragmentu kodu przed i po jego przetworzeniu:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

W przypadku częstego przetwarzania danych wsadowych bezpośrednio do baz danych, takich jak Hive lub tradycyjne bazy danych SQL, nie istnieje potrzeba **/in** lub **/out** folderu, ponieważ dane wyjściowe znajdują się już w oddzielnym folderze tabeli programu Hive lub zewnętrznej bazy danych. Na przykład codzienne wyodrębnienia od klientów będzie miało miejsce w odpowiednich folderach, a aranżacja według takich elementów, jak Azure Data Factory, Apache Oozie lub Apache Flow, wywoła codzienne zadanie Hive lub Spark w celu przetworzenia i zapisania danych w tabeli programu Hive.

## <a name="next-steps"></a>Następne kroki

* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Access Control w Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Zabezpieczenia w Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Dostrajanie Azure Data Lake Storage Gen1 dla wydajności](data-lake-store-performance-tuning-guidance.md)
* [Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z platformy HDInsight Spark z Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Wskazówki dotyczące dostrajania wydajności dotyczące używania gałęzi usługi HDInsight z Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Tworzenie klastrów usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
