---
title: Opis konfiguracji okresowej kopii zapasowej
description: Użyj funkcji okresowej kopii zapasowej i przywracania Service Fabric, aby skonfigurować okresową kopię zapasową wiarygodnych usług stanowych lub Reliable Actors.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018889"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Informacje o konfiguracji okresowej kopii zapasowej na platformie Azure Service Fabric

Konfigurowanie okresowej kopii zapasowej wiarygodnych usług stanowych lub Reliable Actors obejmuje następujące kroki:

1. **Tworzenie zasad kopii zapasowych**: w tym kroku co najmniej jedna zasada tworzenia kopii zapasowej jest tworzona w zależności od wymagań.

2. **Włączanie kopii zapasowej**: w tym kroku należy skojarzyć zasady tworzenia kopii zapasowych utworzone w **kroku 1** z wymaganymi jednostkami, _aplikacją_, _usługą_ lub _partycją_.

## <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych składają się z następujących konfiguracji:

* **Automatyczne przywracanie po utracie danych**: określa, czy przywracanie ma być wyzwalane automatycznie przy użyciu najnowszej dostępnej kopii zapasowej na wypadek, gdyby partycja napotyka zdarzenie utraty danych.
> [!NOTE]
> Zaleca się, aby nie ustawiać autoprzywracania w klastrach produkcyjnych
>

* **Maksymalna liczba przyrostowych kopii zapasowych**: określa maksymalną liczbę przyrostowych kopii zapasowych do wykonania między dwoma pełnymi kopiami zapasowymi. Maksymalna liczba przyrostowych kopii zapasowych określa górny limit. Pełną kopię zapasową można wykonać, zanim określona liczba przyrostowych kopii zapasowych zostanie ukończona w jednym z następujących warunków

    1. Replika nigdy nie zajęła pełnej kopii zapasowej, ponieważ stała się podstawowa.

    2. Niektóre rekordy dziennika od momentu ostatniej kopii zapasowej zostały obcięte.

    3. Replika przekazała limit MaxAccumulatedBackupLogSizeInMB.

* **Harmonogram tworzenia kopii zapasowych**: czas lub częstotliwość okresowych kopii zapasowych. Jeden może zaplanować cykliczne wykonywanie kopii zapasowych w określonym interwale lub w ustalonym czasie codziennie/co tydzień.

    1. **Harmonogram tworzenia kopii zapasowych oparty na częstotliwości**: ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonanie kopii zapasowej danych w ustalonych odstępach czasu. Żądany przedział czasu między dwoma kolejnymi kopiami zapasowymi jest definiowany przy użyciu formatu ISO8601. Harmonogram tworzenia kopii zapasowych oparty na częstotliwościach obsługuje rozdzielczość interwału do minuty.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Harmonogram tworzenia kopii zapasowych na podstawie czasu**: ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonanie kopii zapasowej danych w określonych porach dnia lub tygodnia. Typ częstotliwości harmonogramu może być codzienny lub co tydzień.
        1. **_Dzienny_ harmonogram tworzenia kopii zapasowych**: ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonanie kopii zapasowej danych w określonych porach dnia. Aby określić tę wartość, ustaw `ScheduleFrequencyType` na _codziennie_ i ustaw `RunTimes` na listę żądany czas w dniu w formacie ISO8601, a Data określona wraz z godziną zostanie zignorowana. Na przykład program `0001-01-01T18:00:00` przedstawia _6:00 PM_ codziennie, ignorując część daty _0001-01-01_. Poniższy przykład ilustruje konfigurację wyzwalającą codzienne kopie zapasowe o godzinie _9:00 am_ i _6:00 PM_ .

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Tygodniowy_ harmonogram tworzenia kopii zapasowych**: ten typ harmonogramu powinien być używany, jeśli konieczne jest wykonanie kopii zapasowej danych w określonych porach dnia. Aby to określić, ustaw `ScheduleFrequencyType` na _tydzień_; Ustaw `RunDays` na listę dni w tygodniu, gdy kopia zapasowa ma zostać wyzwolona i ustawiona `RunTimes` na listę żądanego czasu w ciągu dnia w formacie ISO8601, Data określona wraz z godziną zostanie zignorowana. Lista dni tygodnia, w których ma być wyzwalana okresowa kopia zapasowa. Poniższy przykład ilustruje konfigurację, aby wyzwolić codzienne kopie zapasowe o godzinie _9:00 am_ i _6:00 PM_ w poniedziałek do piątku.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Magazyn kopii zapasowych**: określa lokalizację, w której mają zostać przekazane kopie zapasowe. Magazynem może być magazyn obiektów blob platformy Azure lub udział plików.
    1. **Magazyn obiektów blob platformy Azure**: ten typ magazynu powinien być wybrany, jeśli potrzeba przechowywania wygenerowanych kopii zapasowych na platformie Azure. Zarówno w przypadku klastrów _autonomicznych_ , jak i _opartych na platformie Azure_ można używać tego typu magazynu. Opis tego typu magazynu wymaga parametrów połączenia i nazwy kontenera, w którym należy przekazać kopie zapasowe. Jeśli kontener o określonej nazwie jest niedostępny, zostanie on utworzony podczas przekazywania kopii zapasowej.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Usługa przywracania kopii zapasowych nie działa z usługą Azure Storage w wersji 1
        >

    2. **Udział plików**: ten typ magazynu powinien być wybrany dla klastrów _autonomicznych_ , gdy konieczne jest przechowywanie kopii zapasowych danych lokalnie. Opis tego typu magazynu wymaga ścieżki udziału plików, w której należy przekazać kopie zapasowe. Dostęp do udziału plików można skonfigurować przy użyciu jednej z następujących opcji.
        1. _Zintegrowane uwierzytelnianie systemu Windows_, w którym dostęp do udziału plików jest dostarczany wszystkim komputerom należącym do klastra Service Fabric. W takim przypadku ustaw następujące pola, aby skonfigurować magazyn kopii zapasowych na podstawie _udziałów plików_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Ochrona udziału plików przy użyciu nazwy użytkownika i hasła_, gdzie dostęp do udziału plików jest dostarczany dla określonych użytkowników. Specyfikacja magazynu udziałów plików oferuje również możliwość określenia dodatkowej nazwy użytkownika i hasła pomocniczego w celu dostarczenia poświadczeń zwrotnych w przypadku niepowodzenia uwierzytelniania z użyciem podstawowej nazwy użytkownika i hasła podstawowego. W takim przypadku ustaw następujące pola, aby skonfigurować magazyn kopii zapasowych na podstawie _udziałów plików_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Upewnij się, że niezawodność magazynu spełnia lub przekracza wymagania dotyczące niezawodności danych kopii zapasowej.
>

* **Zasady przechowywania**: określa zasady zachowywania kopii zapasowych w skonfigurowanym magazynie. Obsługiwane są tylko podstawowe zasady przechowywania.
    1. **Podstawowe zasady przechowywania**: te zasady przechowywania umożliwiają zapewnienie optymalnego wykorzystania magazynu przez usunięcie plików kopii zapasowej, które nie są już wymagane. `RetentionDuration` można określić, aby ustawić przedział czasu, dla którego kopie zapasowe muszą być przechowywane w magazynie. `MinimumNumberOfBackups` jest opcjonalnym parametrem, który może być określony w celu upewnienia się, że określona liczba kopii zapasowych jest zawsze zachowywana niezależnie od `RetentionDuration` . Poniższy przykład ilustruje konfigurację, aby zachować kopie zapasowe przez _10_ dni i nie zezwalać na przekroczenie liczby kopii zapasowych poniżej _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Włącz okresowe wykonywanie kopii zapasowej
Po zdefiniowaniu zasad tworzenia kopii zapasowych w celu spełnienia wymagań dotyczących tworzenia kopii zapasowych należy odpowiednio powiązać zasady kopii zapasowej z _aplikacją_ lub _usługą_ albo _partycją_.

> [!NOTE]
> Przed włączeniem kopii zapasowej upewnij się, że nie ma żadnych uaktualnień aplikacji
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchiczna Propagacja zasad kopii zapasowych
W Service Fabric relacja między aplikacją, usługą i partycjami jest hierarchiczna, jak wyjaśniono w [modelu aplikacji](./service-fabric-application-model.md). Zasady tworzenia kopii zapasowych można kojarzyć z _aplikacją_, _usługą_ lub _partycją_ w hierarchii. Zasady tworzenia kopii zapasowej propagują hierarchicznie do następnego poziomu. Zakładając, że istnieje tylko jedna zasada tworzenia kopii zapasowych i skojarzona z _aplikacją_, wszystkie partycje stanowe należące do wszystkich _niezawodnych usług stanowych_ i _Reliable Actors_ _aplikacji_ zostaną wykonane przy użyciu zasad tworzenia kopii zapasowych. Lub jeśli zasady tworzenia kopii zapasowej są skojarzone z _niezawodną usługą stanową_, zostanie utworzona kopia zapasowa wszystkich jej partycji przy użyciu zasad tworzenia kopii zapasowych.

### <a name="overriding-backup-policy"></a>Zastępowanie zasad kopii zapasowych
Może istnieć scenariusz, w którym dla wszystkich usług aplikacji jest wymagana kopia zapasowa danych z tym samym harmonogramem tworzenia kopii zapasowych, z wyjątkiem określonych usług, które wymagają tworzenia kopii zapasowych danych przy użyciu harmonogramu wyższej częstotliwości lub tworzenia kopii zapasowej na innym koncie magazynu lub w udziale. Aby rozwiązać takie scenariusze, usługa przywracania kopii zapasowych udostępnia funkcję przesłonięcia propagowanych zasad w zakresie usług i partycji. Gdy zasady tworzenia kopii zapasowej są skojarzone z _usługą_ lub _partycją_, zastępuje ona propagowane zasady tworzenia kopii zapasowych (jeśli istnieją).

### <a name="example"></a>Przykład

W tym przykładzie używa się Instalatora z dwiema aplikacjami, _MyApp_A_ i _MyApp_B_. Aplikacja _MyApp_A_ zawiera dwie niezawodne usługi stanowe, _SvcA1_  &  _SvcA3_ i jedną niezawodną usługę aktora, _ActorA2_. _SvcA1_ zawiera trzy partycje, podczas gdy _ActorA2_ i _SvcA3_ zawierają dwie partycje.  Aplikacja _MyApp_B_ zawiera trzy niezawodne usługi stanowe, _SvcB1_, _SvcB2_ i _SvcB3_. _SvcB1_ i _SvcB2_ zawierają dwie partycje, podczas gdy _SvcB3_ zawiera trzy partycje.

Załóżmy, że wymagania dotyczące tworzenia kopii zapasowych tych aplikacji są następujące:

1. MyApp_A
    1. Twórz codzienne kopie zapasowe danych dla wszystkich partycji wszystkich _niezawodnych usług stanowych_ i _Reliable Actors_ należące do aplikacji. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Jedna z usług, _SvcA3_, wymaga wykonywania kopii zapasowych co godzinę.

    3. Rozmiar danych w partycji _SvcA1_P2_ jest większy niż oczekiwano, a jego dane kopii zapasowej powinny być przechowywane w innej lokalizacji magazynu _BackupStore2_.

2. MyApp_B
    1. Utwórz kopię zapasową danych w każdej niedzielę o godzinie 8:00 dla wszystkich partycji usługi _SvcB1_ . Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

    2. Utwórz kopię zapasową danych codziennie o 8:00 AM dla partycji _SvcB2_P1_. Przekaż dane kopii zapasowej do lokalizacji _BackupStore1_.

Aby rozwiązać te wymagania dotyczące kopii zapasowych danych, tworzone są zasady tworzenia kopii zapasowych BP_1 do BP_5, a kopie zapasowe są włączone w następujący sposób.
1. MyApp_A
    1. Utwórz zasady tworzenia kopii zapasowych, _BP_1_ z harmonogramem tworzenia kopii zapasowych opartego na częstotliwości, gdzie częstotliwość jest ustawiona na 24 godziny. i magazyn kopii zapasowych skonfigurowany do korzystania z lokalizacji magazynu _BackupStore1_. Włącz te zasady dla _MyApp_A_ aplikacji przy użyciu interfejsu API [włączania kopii zapasowej aplikacji](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) . Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_1_ dla wszystkich partycji _niezawodnych usług stanowych_ i _Reliable Actors_ należących do _MyApp_A_ aplikacji.

    2. Utwórz zasady tworzenia kopii zapasowych, _BP_2_ z harmonogramem tworzenia kopii zapasowych opartego na częstotliwości, gdzie częstotliwość jest ustawiona na 1 godz. i magazyn kopii zapasowych skonfigurowany do korzystania z lokalizacji magazynu _BackupStore1_. Włącz tę zasadę dla usługi _SvcA3_ za pomocą interfejsu API [włączania kopii zapasowych usługi](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Ta akcja przesłania propagowane zasady _BP_1_ przez jawne włączenie zasad tworzenia kopii zapasowych _BP_2_ dla wszystkich partycji usługi _SvcA3_ , co prowadzi do tworzenia kopii zapasowych danych przy użyciu _BP_2_ zasad tworzenia kopii zapasowych dla tych partycji.

    3. Utwórz zasady tworzenia kopii zapasowych, _BP_3_ z harmonogramem tworzenia kopii zapasowych opartego na częstotliwości, gdzie częstotliwość jest ustawiona na 24 godziny. i magazyn kopii zapasowych skonfigurowany do korzystania z lokalizacji magazynu _BackupStore2_. Włącz tę zasadę dla _SvcA1_P2_ partycji przy użyciu interfejsu API [włączania kopii zapasowej partycji](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Ta akcja przesłania propagowane _BP_1_ zasad przez jawne włączenie zasad tworzenia kopii zapasowych _BP_3_ dla partycji _SvcA1_P2_.

2. MyApp_B
    1. Utwórz zasady tworzenia kopii zapasowych, _BP_4_, z harmonogramem tworzenia kopii zapasowych opartego na czasie, w którym ustawiono typ częstotliwości harmonogramu co tydzień, dni uruchamiania to niedziela, a czas wykonywania jest ustawiony na 8:00 am. Magazyn kopii zapasowych skonfigurowany do korzystania z lokalizacji magazynu _BackupStore1_. Włącz tę zasadę dla usługi _SvcB1_ za pomocą interfejsu API [włączania kopii zapasowych usługi](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_4_ dla wszystkich partycji usługi _SvcB1_.

    2. Utwórz zasady tworzenia kopii zapasowych, _BP_5_, z harmonogramem tworzenia kopii zapasowych opartego na czasie, w którym ustawiono typ częstotliwości harmonogramu dziennie, a czas wykonywania jest ustawiony na 8:00 am. Magazyn kopii zapasowych skonfigurowany do korzystania z lokalizacji magazynu _BackupStore1_. Włącz tę zasadę dla _SvcB2_P1_ partycji przy użyciu interfejsu API [włączania kopii zapasowej partycji](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Ta akcja umożliwia tworzenie kopii zapasowych danych przy użyciu zasad tworzenia kopii zapasowych _BP_5_ dla _SvcB2_P1_ partycji.

Na poniższym diagramie przedstawiono jawnie włączone zasady tworzenia kopii zapasowych i propagowane zasady tworzenia kopii zapasowych.

![Service Fabric hierarchia aplikacji][0]

## <a name="disable-backup"></a>Wyłączanie kopii zapasowej
Zasady tworzenia kopii zapasowych można wyłączyć, jeśli nie ma potrzeby tworzenia kopii zapasowych danych. Zasady tworzenia kopii zapasowych włączone w _aplikacji_ można wyłączyć tylko w tej samej _aplikacji_ przy użyciu opcji Wyłącz interfejs API [tworzenia kopii](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) zapasowej aplikacji, a zasady tworzenia kopii zapasowych włączone w _usłudze_ _mogą być_ wyłączone _w tej samej_ _usłudze_ przy [użyciu interfejsu API](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) [usługi Disable Backup](/rest/api/servicefabric/sfclient-api-disableservicebackup) .

* Wyłączenie zasad tworzenia kopii zapasowych dla _aplikacji_ powoduje zatrzymanie wszystkich okresowych kopii zapasowych danych w wyniku propagacji zasad tworzenia kopii zapasowych na niezawodne partycje usługi stanowej lub niezawodne partycje aktora.

* Wyłączenie zasad tworzenia kopii zapasowych dla _usługi_ powoduje zatrzymanie wszystkich okresowych kopii zapasowych danych w wyniku propagacji tych zasad kopii zapasowych na partycje _usługi_.

* Wyłączenie zasad tworzenia kopii zapasowej dla _partycji_ powoduje zatrzymanie wszystkich okresowych kopii zapasowych danych z powodu zasad tworzenia kopii zapasowych na partycji.

* Podczas wyłączania kopii zapasowej dla jednostki (aplikacji/usługi/partycji) `CleanBackup` można ustawić _wartość true_ , aby usunąć wszystkie kopie zapasowe w skonfigurowanym magazynie.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Przed wyłączeniem kopii zapasowej upewnij się, że nie ma żadnych uaktualnień aplikacji
>

## <a name="suspend--resume-backup"></a>Wstrzymywanie & Wznów tworzenie kopii zapasowej
Niektóre sytuacje mogą wymagać tymczasowego zawieszania okresowej kopii zapasowej danych. W takiej sytuacji, w zależności od wymagań, w _aplikacji_, _usłudze_ lub _partycji_ może być używany interfejs API zawieszania kopii zapasowych. Okresowe zawieszenie kopii zapasowej jest przechodnie w porównaniu z poddrzewem hierarchii aplikacji od punktu, w którym jest stosowany. 

* Po zastosowaniu zawieszenia w _aplikacji_ przy użyciu interfejsu API [wstrzymania tworzenia kopii zapasowej aplikacji](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) wszystkie usługi i partycje w tej aplikacji są zawieszane w celu okresowego wykonywania kopii zapasowych danych.

* Po zastosowaniu zawieszenia w _usłudze_ za pomocą interfejsu API [usługi zawieszania kopii zapasowych](/rest/api/servicefabric/sfclient-api-suspendservicebackup) wszystkie partycje w ramach tej usługi są zawieszane w celu okresowego wykonywania kopii zapasowych danych.

* Po zastosowaniu zawieszenia na _partycji_ przy użyciu interfejsu API [wstrzymania kopii zapasowej](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) zawieszania partycji w ramach tej usługi zawiesza się okresowe wykonywanie kopii zapasowych danych.

Gdy zachodzi potrzeba zawieszenia, okresowe wykonywanie kopii zapasowych danych można przywrócić przy użyciu odpowiedniego, wznawiając interfejs API tworzenia kopii zapasowych. Okresowe wykonywanie kopii zapasowej należy wznowić w tej samej _aplikacji_, _usłudze_ lub _partycji_ , w której zostało wstrzymane.

* Jeśli zawieszanie zostało zastosowane w _aplikacji_, należy ją wznowić przy użyciu interfejsu API [przywracania kopii zapasowej aplikacji](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) . 

* Jeśli zawieszanie zostało zastosowane w _usłudze_, należy ją wznowić przy użyciu interfejsu API [przywracania kopii zapasowej usługi](/rest/api/servicefabric/sfclient-api-resumeservicebackup) .

* Jeśli zawieszanie zostało zastosowane w _partycji_, należy je wznowić przy użyciu interfejsu API [wznawiania tworzenia kopii zapasowej partycji](/rest/api/servicefabric/sfclient-api-resumepartitionbackup) .

### <a name="difference-between-suspend-and-disable-backups"></a>Różnica między wstrzymywaniem i wyłączaniem kopii zapasowych
Jeśli kopie zapasowe nie są już wymagane dla konkretnej aplikacji, usługi lub partycji, należy wyłączyć opcję Utwórz kopię zapasową. Jeden może wywołać Wyłączanie żądania kopii zapasowej wraz z parametrem czyste kopie zapasowe w celu uzyskania wartości true, co oznacza, że wszystkie istniejące kopie zapasowe również zostaną usunięte. Jednakże wstrzymanie ma być używane w scenariuszach, w których jeden z nich chce wyłączyć kopie zapasowe tymczasowo, jak w przypadku zapełnienia dysku lokalnego lub przekazanie kopii zapasowej kończy się niepowodzeniem ze względu na znany problem z siecią. 

Wyłączenie może być wywoływane tylko na poziomie, który został wcześniej włączony do tworzenia kopii zapasowej, ale zawieszanie można zastosować na dowolnym poziomie, który jest obecnie włączony do tworzenia kopii zapasowej bezpośrednio lub przez dziedziczenie/hierarchię. Na przykład, jeśli kopia zapasowa jest włączona na poziomie aplikacji, jeden może wywoływać wyłączenie tylko na poziomie aplikacji, jednak wstrzymanie może być wywoływane w aplikacji, dowolnej usłudze lub partycji w ramach tej aplikacji. 

## <a name="auto-restore-on-data-loss"></a>Autoprzywracanie po utracie danych
Partycja usługi może utracić dane z powodu nieoczekiwanych błędów. Na przykład dysk dla dwóch z trzech replik partycji (łącznie z repliką podstawową) jest uszkodzony lub wyczyszczony.

Gdy Service Fabric wykryje, że partycja jest w utracie danych, wywołuje `OnDataLossAsync` metodę interfejsu na partycji i oczekuje, że partycja może wykonać wymaganą akcję, aby wyczerpać utratę danych. W takiej sytuacji, jeśli obowiązujące zasady tworzenia kopii zapasowych na partycji mają `AutoRestoreOnDataLoss` ustawioną flagę `true` , przywracanie zostanie wyzwolone automatycznie przy użyciu najnowszej dostępnej kopii zapasowej dla tej partycji.

> [!NOTE]
> Zaleca się, aby nie ustawiać autoprzywracania w klastrach produkcyjnych
>

## <a name="get-backup-configuration"></a>Pobierz konfigurację kopii zapasowej
Dostępne są oddzielne interfejsy API do uzyskiwania informacji o konfiguracji kopii zapasowych w ramach _aplikacji_, _usługi_ i zakresu _partycji_ . [Pobierz informacje o konfiguracji kopii](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)zapasowej aplikacji, [Pobierz informacje o konfiguracji kopii zapasowej usługi](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)i [Pobierz informacje o konfiguracji kopii zapasowej partycji](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) są odpowiednio opisane. Głównie te interfejsy API zwracają odpowiednie zasady tworzenia kopii zapasowych, zakres, w którym są stosowane zasady tworzenia kopii zapasowych, oraz szczegóły zawieszenia kopii zapasowej. Poniżej znajduje się krótki opis zwracanych wyników tych interfejsów API.

- Informacje o konfiguracji kopii zapasowej aplikacji: zawierają szczegóły zasad tworzenia kopii zapasowych stosowane w aplikacji oraz wszystkie zasady nadmiernej ridden w usługach i partycjach należących do aplikacji. Zawiera również informacje o zawieszeniu dla aplikacji i usług IT oraz partycje.

- Informacje o konfiguracji kopii zapasowej usługi: zawierają szczegóły obowiązujących zasad tworzenia kopii zapasowych w usłudze oraz zakres, w którym te zasady zostały zastosowane i wszystkie zasady nadmiernej ridden na swoich partycjach. Zawiera również informacje o zawieszeniu dotyczące usługi i jej partycji.

- Informacje o konfiguracji kopii zapasowej partycji: zawiera szczegóły obowiązujących zasad tworzenia kopii zapasowych na partycji oraz zakres, w którym te zasady zostały zastosowane. Zawiera również informacje o zawieszeniu dla partycji.

## <a name="list-available-backups"></a>Wyświetl dostępne kopie zapasowe

Dostępne kopie zapasowe można wyświetlić za pomocą interfejsu API pobierania listy kopii zapasowych. Wynik wywołania interfejsu API zawiera elementy informacji o kopii zapasowej powiązane ze wszystkimi kopiami zapasowymi dostępnymi w magazynie kopii zapasowych skonfigurowanym w odpowiednich zasadach tworzenia kopii zapasowych. Różne warianty tego interfejsu API są udostępniane w celu wyświetlenia listy dostępnych kopii zapasowych należących do aplikacji, usługi lub partycji. Te interfejsy API obsługują pobieranie _najnowszej_ dostępnej kopii zapasowej wszystkich odpowiednich partycji lub filtrowanie kopii zapasowych na podstawie _daty rozpoczęcia_ i _daty zakończenia_.

Te interfejsy API obsługują również stronicowanie wyników, gdy parametr _MaxResults_ jest ustawiony na wartość niezerową dodatnią liczbę całkowitą, a następnie interfejs API zwraca maksymalne elementy informacji o kopii zapasowej _MaxResults_ . W przypadku więcej elementów informacji o kopii zapasowej dostępnych niż wartość _MaxResults_ , zwracany jest token kontynuacji. Aby uzyskać następny zestaw wyników, można użyć prawidłowego parametru tokenu kontynuacji. Gdy prawidłowa wartość tokenu kontynuacji jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli zostaną zwrócone wszystkie dostępne wyniki, w odpowiedzi nie jest dostępny żaden token kontynuacji.

Poniżej przedstawiono krótkie informacje dotyczące obsługiwanych wariantów.

- [Pobierz listę kopii zapasowych aplikacji](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): zwraca listę kopii zapasowych dostępnych dla każdej partycji należącej do danej Service Fabric aplikacji.

- [Pobierz listę kopii zapasowych usługi](/rest/api/servicefabric/sfclient-api-getservicebackuplist): zwraca listę kopii zapasowych dostępnych dla każdej partycji należącej do danej usługi Service Fabric.
 
- [Pobierz listę kopii zapasowych partycji](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): zwraca listę kopii zapasowych dostępnych dla określonej partycji.

## <a name="next-steps"></a>Następne kroki
- [Dokumentacja interfejsu API REST przywracania kopii zapasowych](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
