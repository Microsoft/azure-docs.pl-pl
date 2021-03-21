---
title: Najlepsze rozwiązania dotyczące korzystania z Azure Data Lake Storage Gen2 | Microsoft Docs
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi pozyskiwania danych, bezpieczeństwa i wydajności związanych z używaniem Azure Data Lake Storage Gen2 (wcześniej znanych jako Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 0efcdfd1b14479edf84dc1892e7e1d9afabd5a81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95913559"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Najlepsze rozwiązania dotyczące korzystania z Azure Data Lake Storage Gen2

Ten artykuł zawiera informacje o najlepszych rozwiązaniach i kwestiach związanych z pracą z usługą Azure Data Lake Storage Gen2. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajności, odporności i monitorowania Data Lake Storage Gen2. Przed Data Lake Storage Gen2, praca z naprawdę dużą ilością danych w usługach, takich jak usługa Azure HDInsight, została złożona. Fragmentu dane na wielu kontach magazynu obiektów blob, dzięki czemu można osiągnąć magazyn petabajtów i optymalną wydajność w tej skali. Data Lake Storage Gen2 obsługuje pojedyncze rozmiary plików, tak jakby 5TB i większość stałych limitów wydajności został usunięty. Jednak nadal istnieją pewne zagadnienia, które opisano w tym artykule, aby uzyskać najlepszą wydajność dzięki Data Lake Storage Gen2.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Azure Data Lake Storage Gen2 oferuje funkcje kontroli dostępu w systemie POSIX dla użytkowników, grup Azure Active Directory (Azure AD) i jednostek usługi. Te kontrolki dostępu można ustawić na istniejące pliki i katalogi. Kontroli dostępu można także użyć do tworzenia uprawnień domyślnych, które mogą być automatycznie stosowane do nowych plików lub katalogów. Więcej szczegółów na temat list ACL Data Lake Storage Gen2 są dostępne w [programie w programie Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Korzystanie z grup zabezpieczeń i poszczególnych użytkowników

Podczas pracy z danymi Big Data w Data Lake Storage Gen2 jest możliwe, że jednostka usługi jest używana do zezwalania usługom, takim jak usługa Azure HDInsight, na pracę z danymi. Mogą jednak wystąpić sytuacje, w których indywidualni użytkownicy potrzebują dostępu do danych. We wszystkich przypadkach zdecydowanie należy rozważyć użycie [grup zabezpieczeń](../common/storage-auth-aad.md) Azure Active Directory zamiast przypisywania poszczególnych użytkowników do katalogów i plików.

Gdy grupa zabezpieczeń ma przypisane uprawnienia, Dodawanie lub usuwanie użytkowników z grupy nie wymaga żadnych aktualizacji do Data Lake Storage Gen2. Pozwala to również uniknąć przekroczenia maksymalnej liczby wpisów kontroli dostępu dla listy kontroli dostępu (ACL). Obecnie ten numer to 32 (w tym cztery listy ACL w stylu POSIX, które są zawsze skojarzone z każdym plikiem i katalogiem): Użytkownik będący właścicielem, grupa będąca właścicielem, maska i inne. Każdy katalog może mieć dwa typy list ACL, listy ACL dostępu i domyślnej listy ACL, dla łącznej liczby wpisów kontroli dostępu 64. Aby uzyskać więcej informacji na temat tych list ACL, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Gdy użytkownik lub użytkownicy będą musieli uzyskać dostęp do danych na koncie magazynu z włączoną hierarchiczną przestrzenią nazw, najlepiej jest używać Azure Active Directory grup zabezpieczeń. Niektóre zalecane grupy do rozpoczęcia z programu to **ReadOnlyUsers**, **WriteAccessUsers** i **FullAccessUsers** dla katalogu głównego kontenera, a nawet oddzielne dla podkatalogów kluczy. Jeśli istnieją inne przewidywane grupy użytkowników, które mogą zostać później dodane, ale nie zostały jeszcze zidentyfikowane, warto rozważyć utworzenie fikcyjnych grup zabezpieczeń, które mają dostęp do niektórych folderów. Dzięki użyciu grupy zabezpieczeń można uniknąć długiego czasu przetwarzania podczas przypisywania nowych uprawnień do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla podmiotów usługi

Nazwy główne usług Azure Active Directory są zwykle używane przez usługi, takie jak Azure Databricks, aby uzyskać dostęp do danych w Data Lake Storage Gen2. W przypadku wielu klientów jedna jednostka usługi Azure Active Directory może być odpowiednia i może mieć pełne uprawnienia w katalogu głównym kontenera Data Lake Storage Gen2. Inni klienci mogą wymagać wielu klastrów z różnymi jednostkami usługi, w których jeden klaster ma pełny dostęp do danych, i inny klaster z dostępem tylko do odczytu. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Włączanie zapory Data Lake Storage Gen2 z dostępem do usługi platformy Azure

Data Lake Storage Gen2 obsługuje opcję włączania zapory i ograniczania dostępu tylko do usług platformy Azure, co jest zalecane do ograniczenia wektora ataków zewnętrznych. Zaporę można włączyć na koncie magazynu w Azure Portal za pomocą **zapory**  >  **Włącz zaporę (włączona)**  >  **Zezwalaj na dostęp do opcji usług platformy Azure** .

Aby uzyskać dostęp do konta magazynu z Azure Databricks, wdróż Azure Databricks w sieci wirtualnej, a następnie Dodaj tę sieć wirtualną do zapory. Zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Podczas tworzenia architektury systemu przy użyciu Data Lake Storage Gen2 lub dowolnej usługi w chmurze należy wziąć pod uwagę wymagania dotyczące dostępności i sposób reagowania na potencjalne przerwy w świadczeniu usługi. Problem może być zlokalizowany w konkretnym wystąpieniu lub nawet w całym regionie, więc należy mieć plan dla obu tych wystąpień. W zależności od celu czasu odzyskiwania i celu punktu odzyskiwania umowy SLA obciążenia można wybrać bardziej lub mniej agresywną strategię w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoką dostępność (HA) i odzyskiwanie po awarii (DR) może być czasami połączone ze sobą, Chociaż każda z nich ma nieco inną strategię, szczególnie w przypadku, gdy zawiera dane. Data Lake Storage Gen2 już obsługuje replikację 3. pod okapem, aby chronić przed zlokalizowanymi awariami sprzętowymi. Ponadto inne opcje replikacji, takie jak ZRS lub GZRS, wzbogacają HA, a GRS & RA-GRS ulepszają program DR. Podczas kompilowania planu o wysokiej dostępności w przypadku przerwania działania usługi obciążenie musi mieć dostęp do najnowszych danych tak szybko, jak to możliwe, przez przełączenie na lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii, aby przygotować się do mało prawdopodobnego uszkodzenia regionu, ważne jest również, aby dane były replikowane do innego regionu przy użyciu replikacji GRS lub RA-GRS. Należy również wziąć pod uwagę wymagania dotyczące przypadków granicznych, takich jak uszkodzenie danych, w przypadku których warto utworzyć okresowe migawki, aby wrócić do programu. W zależności od ważności i rozmiaru danych należy rozważyć stopniowe przyrostowe migawki z 1-, 6-i 24-godzinnego okresu, zgodnie z tolerancją ryzyka.

W celu odporności danych z Data Lake Storage Gen2 zaleca się geograficzną replikację danych za pośrednictwem GRS lub RA-GRS, które spełniają wymagania dotyczące HA/DR. Ponadto należy rozważyć sposób, w jaki aplikacja używa Data Lake Storage Gen2 do automatycznego przełączenia w tryb failover do regionu pomocniczego przez wyzwalacze monitorowania lub długość nieudanych prób lub co najmniej wysyła powiadomienie do administratorów w celu ręcznej interwencji. Należy pamiętać, że istnieje kompromis między przechodzeniem w tryb failover a oczekiwaniem na powrót usługi do trybu online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Użyj pomocą distcp do przenoszenia danych między dwiema lokalizacjami

Krótkie dla kopii rozproszonej, pomocą distcp to narzędzie wiersza polecenia systemu Linux, które jest dostarczane z usługą Hadoop i zapewnia rozproszone przenoszenie danych między dwiema lokalizacjami. Te dwie lokalizacje mogą być Data Lake Storage Gen2, HDFS lub S3. To narzędzie używa zadań MapReduce w klastrze Hadoop (na przykład HDInsight) do skalowania w poziomie na wszystkich węzłach. Pomocą distcp jest uznawany za najszybszy sposób przenoszenia danych Big Data bez specjalnych urządzeń do kompresji sieciowej. Pomocą distcp udostępnia również opcję aktualizowania różnic między dwiema lokalizacjami, obsługuje automatyczne ponawianie prób, a także dynamiczne skalowanie obliczeń. Takie podejście jest niezwykle wydajne, gdy chodzi o replikację elementów, takich jak tabele Hive/Spark, które mogą mieć wiele dużych plików w jednym katalogu i chcesz skopiować tylko zmodyfikowane dane. Z tego względu pomocą distcp jest najbardziej Zalecanym narzędziem do kopiowania danych między magazynami danych Big Data.

Zadania kopiowania mogą być wyzwalane przez przepływy pracy Apache Oozie korzystające z częstotliwości lub wyzwalaczy danych, a także zadań z systemem Linux. W przypadku zadań związanych z replikacją intensywną zaleca się sporządzenie oddzielnego klastra usługi HDInsight Hadoop, który można dostrajać i skalować specjalnie dla zadań kopiowania. Dzięki temu zadania kopiowania nie zakłócają zadań krytycznych. W przypadku przeprowadzania replikacji na poziomie wystarczającej częstotliwości klaster może nawet zostać przełączony między zadaniami. W przypadku przełączenia w tryb failover do regionu pomocniczego upewnij się, że inny klaster jest również w regionie pomocniczym, aby replikować nowe dane z powrotem do głównego konta Data Lake Storage Gen2 po utworzeniu kopii zapasowej. Aby zapoznać się z przykładami używania pomocą distcp, zobacz [Używanie pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Planowanie zadań kopiowania przy użyciu Azure Data Factory

Azure Data Factory można również użyć do zaplanowania zadań kopiowania przy użyciu działania kopiowania i nawet można je skonfigurować na częstotliwości za pośrednictwem Kreatora kopiowania. Pamiętaj, że Azure Data Factory ma limit jednostek przenoszenia danych w chmurze (DMUs), a ostatecznie Wykorzystaj przepływność/obliczenia dla obciążeń dużych ilości danych. Ponadto Azure Data Factory obecnie nie oferuje aktualizacji różnicowych między kontami Data Lake Storage Gen2, dlatego katalogi takie jak tabele Hive wymagają pełnej kopii do replikacji. Więcej informacji na temat kopiowania przy użyciu Data Factory można znaleźć w artykule dotyczącym usługi [Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) .

## <a name="monitoring-considerations"></a>Zagadnienia dotyczące monitorowania

Data Lake Storage Gen2 dostarcza metryki w Azure Portal w ramach konta Data Lake Storage Gen2 i w Azure Monitor. W Azure Portal zostanie wyświetlona dostępność Data Lake Storage Gen2. Aby uzyskać aktualną dostępność konta Data Lake Storage Gen2, musisz uruchomić własne testy syntetyczne w celu sprawdzenia dostępności. Inne metryki, takie jak łączne wykorzystanie magazynu, żądania odczytu/zapisu oraz dane wejściowe/wyjściowe są dostępne do wykorzystania przez monitorowanie aplikacji i mogą wyzwalać alerty w przypadku przekroczenia progów (na przykład Średnie opóźnienie lub liczba błędów na minutę).

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogu

Podczas wypełniania danych do programu Data Lake należy wstępnie zaplanować strukturę danych, dzięki czemu można efektywnie wykorzystać zabezpieczenia, partycjonowanie i przetwarzanie. Wiele z poniższych zaleceń dotyczy wszystkich obciążeń związanych z danymi Big Data. Każde obciążenie ma inne wymagania dotyczące sposobu, w jaki dane są używane, ale poniżej przedstawiono kilka typowych układów, które należy wziąć pod uwagę podczas pracy z scenariuszami IoT i Batch.

### <a name="iot-structure"></a>Struktura IoT

W przypadku obciążeń IoT może istnieć duża ilość danych przechowywanych w magazynie danych obejmujących wiele produktów, urządzeń, organizacji i klientów. Ważne jest wstępne zaplanowanie układu katalogu dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla odbiorców w dół. Ogólny szablon, który ma zostać rozważenia, może być następujący:

*{Region}/{SubjectMatter (s)}/{yyyy}/{mm}/{dd}/{hh}/*

Na przykład dane telemetryczne dla aparatu samolotowego w Wielkiej Brytanii mogą wyglądać następująco:

*Zjednoczone Królestwo/płaszczyzny/BA1293/Engine1/2017/08/11/12/*

Istnieje istotny powód, aby umieścić datę na końcu struktury katalogów. Jeśli chcesz zablokować określone regiony lub zagadnienia dotyczące użytkowników/grup, możesz łatwo to zrobić przy użyciu uprawnień POSIX. W przeciwnym razie, jeśli istnieje potrzeba ograniczenia pewnej grupy zabezpieczeń do wyświetlania tylko danych z Wielkiej Brytanii lub niektórych płaszczyzn, w przypadku dużej ilości katalogów w każdej godzinie należy wymagać oddzielnego uprawnienia. Ponadto, jeśli ze strukturą daty na początku zwiększy się liczbę katalogów jako czas, w którym zakończył się.

### <a name="batch-jobs-structure"></a>Struktura zadań wsadowych

Na wysokim poziomie często stosowane podejście do przetwarzania wsadowego polega na wykorzystaniu danych w katalogu "in". Następnie, po przetworzeniu danych, należy umieścić nowe dane w katalogu "out" dla procesów podrzędnych do użycia. Ta struktura katalogów jest czasami widoczna dla zadań wymagających przetwarzania poszczególnych plików i może nie wymagać masowego przetwarzania równoległego w dużych zestawach danych. Podobnie jak w przypadku zalecanej struktury IoT, dobrą strukturą katalogów są katalogi poziomu nadrzędnego dla elementów, takich jak region i zagadnienia dotyczące tematu (na przykład organizacja, produkt/producent). Ta struktura ułatwia zabezpieczanie danych w organizacji i lepsze zarządzanie danymi w obciążeniach. Ponadto należy rozważyć datę i godzinę w strukturze, aby umożliwić lepszą organizację, wyszukiwanie filtrowane, zabezpieczenia i automatyzację w przetwarzaniu. Poziom szczegółowości dla struktury daty zależy od interwału, w którym dane są przekazywane lub przetwarzane, na przykład co godzinę, codziennie lub nawet miesięcznie.

Czasami przetwarzanie plików nie powiedzie się z powodu uszkodzenia danych lub nieoczekiwanych formatów. W takich przypadkach struktura katalogów może korzystać z folderu **/Bad** , aby przenieść pliki do dalszej inspekcji. Zadanie usługi Batch może także obsłużyć raportowanie lub powiadomienie o tych *nieprawidłowych* plikach w ramach interwencji ręcznej. Weź pod uwagę następującą strukturę szablonów:

*{Region}/{SubjectMatter (s)}/in/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter (s)}/out/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter (s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

Na przykład firma marketingowa otrzymuje codzienne wyodrębnianie danych aktualizacji klientów od klientów w Ameryka Północna. Może wyglądać podobnie do poniższego fragmentu kodu przed i po jego przetworzeniu:

*//Wyodrębnianie/ACMEPaperCo/w/2017/08/14/updates_08142017.csv*\
*//Wyodrębnianie/ACMEPaperCo/out/2017/08/14/processed_updates_08142017.csv*

W przypadku częstego przetwarzania danych wsadowych bezpośrednio do baz danych, takich jak Hive lub tradycyjne bazy danych SQL, nie istnieje potrzeba **/in** lub **/out** folderu, ponieważ dane wyjściowe znajdują się już w oddzielnym folderze tabeli programu Hive lub zewnętrznej bazy danych. Na przykład codzienne wyodrębnienia od klientów będzie miało miejsce w odpowiednich folderach, a aranżacja według takich elementów, jak Azure Data Factory, Apache Oozie lub Apache Flow, wywoła codzienne zadanie Hive lub Spark w celu przetworzenia i zapisania danych w tabeli programu Hive.