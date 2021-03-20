---
title: Najlepsze rozwiązania i wskazówki dotyczące wydajności — Azure SQL Edge
description: Informacje o najlepszych rozwiązaniach dotyczących wydajności i wskazówkach dotyczących konfiguracji w usłudze Azure SQL Edge
keywords: SQL Edge, przechowywanie danych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392014"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Najlepsze rozwiązania w zakresie wydajności i wskazówki dotyczące konfiguracji

Usługa Azure SQL Edge oferuje kilka funkcji i możliwości, których można użyć w celu zwiększenia wydajności wdrożenia programu SQL Edge. W tym artykule przedstawiono niektóre najlepsze rozwiązania i zalecenia dotyczące maksymalizowania wydajności. 

## <a name="best-practices"></a>Najlepsze rozwiązania 

### <a name="configure-multiple-tempdb-data-files"></a>Konfigurowanie wielu plików danych tempdb

Usługa Azure SQL Edge domyślnie tworzy tylko jeden plik danych tempdb w ramach inicjowania kontenera. Zalecamy rozważenie utworzenia wielu plików danych tempdb po wdrożeniu. Aby uzyskać więcej informacji, zapoznaj się ze wskazówkami zawartymi w artykule [zalecenia dotyczące zmniejszenia rywalizacji o alokacje w bazie danych SQL Server tempdb](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Użyj klastrowanych indeksów magazynu kolumn tam, gdzie jest to możliwe

Urządzenia IoT i Edge zapewnią generowanie dużej ilości danych, które zwykle są agregowane w pewnym przedziale czasu na potrzeby analizy. Pojedyncze wiersze danych są rzadko używane do analizy. Indeksy magazynu kolumn są idealne do przechowywania i wykonywania zapytań dotyczących dużych zestawów danych. Ten indeks korzysta z magazynu danych opartego na kolumnach i przetwarzania zapytań, aby uzyskać zyski do 10 razy większą wydajność zapytań niż w przypadku tradycyjnych magazynów zorientowanych na wiersze. Możesz również uzyskać zyski do 10 razy kompresji danych przez rozmiar nieskompresowanych danych. Aby uzyskać więcej informacji, zobacz [indeksy magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview)

Ponadto inne funkcje usługi Azure SQL Edge, takie jak przesyłanie strumieniowe danych i przechowywanie danych, korzystają z optymalizacji magazynu kolumn podczas wstawiania i usuwania danych. 

### <a name="simple-recovery-model"></a>Model odzyskiwania prostego

Ponieważ magazyn może być ograniczony na urządzeniach brzegowych, wszystkie bazy danych użytkowników w usłudze Azure SQL Edge domyślnie korzystają z modelu odzyskiwania prostego. Model odzyskiwania prostego automatycznie ponownie przejmuje miejsce w dzienniku, aby zachować małe wymagania dotyczące miejsca, co eliminuje konieczność zarządzania przestrzenią dziennika transakcji. Urządzenia brzegowe z ograniczoną ilością dostępnego miejsca do magazynowania mogą być przydatne. Aby uzyskać więcej informacji na temat prostego modelu odzyskiwania i innych dostępnych modeli odzyskiwania, zobacz [modele odzyskiwania](/sql/relational-databases/backup-restore/recovery-models-sql-server) .

Operacje, takie jak wysyłanie dziennika i przywracanie do punktu w czasie, które wymagają tworzenia kopii zapasowych dziennika transakcji, nie są obsługiwane przez model odzyskiwania prostego.  

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana 

### <a name="setting-memory-limits"></a>Ustawianie limitów pamięci

Usługa Azure SQL Edge obsługuje do 64 GB pamięci dla puli buforów, a dodatkowa pamięć może być wymagana przez procesy satelitarne działające w ramach kontenera usługi SQL Edge. Na mniejszych urządzeniach brzegowych z ograniczoną ilością pamięci zaleca się ograniczenie dostępnej pamięci do kontenerów programu SQL Edge w taki sposób, że host platformy Docker i inne procesy lub moduły graniczne mogą działać prawidłowo. Całkowita ilość dostępnej pamięci dla programu SQL Edge może być kontrolowana przy użyciu następujących mechanizmów. 

- Ograniczanie ilości pamięci dostępnej dla kontenerów programu SQL Edge: całkowita ilość pamięci dostępnej dla kontenera programu SQL Edge może być ograniczona przy użyciu opcji konfiguracji środowiska uruchomieniowego kontenera `--memory` . Aby uzyskać więcej informacji na temat ograniczania pamięci dostępnej dla kontenera usługi SQL Edge, zobacz [Opcje środowiska uruchomieniowego z pamięcią, procesorami i procesorami GPU](https://docs.docker.com/config/containers/resource_constraints/).

- Ograniczanie ilości pamięci dostępnej dla procesu SQL w kontenerze: Domyślnie proces SQL w kontenerze używa tylko 80% dostępnej fizycznej pamięci RAM. W przypadku większości wdrożeń konfiguracja domyślna będzie dobra. Mogą jednak wystąpić sytuacje, w których dodatkowa pamięć może być wymagana do przesyłania strumieniowego danych i procesów ONNX działających wewnątrz kontenerów programu SQL Edge. W takich scenariuszach pamięć dostępna dla procesu SQL może być kontrolowana przy użyciu `memory.memorylimitmb` Ustawienia w pliku MSSQL-conf. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przy użyciu pliku MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

Podczas ustawiania limitów pamięci należy zachować ostrożność, aby nie ustawiać tej wartości za niską. Jeśli nie ustawisz wystarczającej ilości pamięci dla procesu SQL, może to mieć istotny wpływ na wydajność bazy danych SQL.

### <a name="delayed-durability"></a>Opóźniona trwałość

Transakcje w usłudze Azure SQL Edge mogą być w pełni trwałe, SQL Server domyślne lub opóźnione trwałe (znane także jako zatwierdzanie z opóźnieniem).

W pełni trwałe zatwierdzenia transakcji są synchroniczne i raportują zatwierdzenie jako pomyślne i zwrotne do klienta, dopiero po zapisaniu rekordów dziennika transakcji na dysku. Opóźnione zatwierdzanie transakcji trwałych jest asynchroniczne i Zgłoś zatwierdzenie jako pomyślne przed zapisaniem rekordów dziennika transakcji na dysku. Zapisanie wpisów dziennika transakcji na dysku jest wymagane, aby transakcja była trwała. Opóźnione transakcje trwałe stają się trwałe, gdy wpisy dziennika transakcji są opróżniane na dysk. 

W przypadku wdrożeń, w których **niektóre utraty danych** mogą być tolerowane lub na urządzeniach brzegowych z wolnym magazynem, można użyć opóźnionej trwałości do zoptymalizowania pozyskiwania danych i czyszczenia danych. Aby uzyskać więcej informacji, zobacz [Kontrola trwałości transakcji](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Konfiguracje systemu operacyjnego Linux 

Należy rozważyć użycie następujących ustawień [konfiguracji systemu operacyjnego Linux](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) w celu uzyskania najlepszej wydajności instalacji SQL.