---
title: Warstwy cenowe — Azure Database for MariaDB
description: Dowiedz się więcej o różnych warstwach cenowych usługi Azure Database dla bazy danych MariaDB, w tym generacjach obliczeniowych, typach magazynu, rozmiarze magazynu, języka wirtualnego, pamięci i okresach przechowywania kopii zapasowych.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9c057bebf94362b3b9e42db9d311e99f1e35c651
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770127"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Warstwy cenowe usługi Azure Database dla mariadb

Można utworzyć usługę Azure Database dla serwera MariaDB w jednej z trzech różnych warstw cenowych: Podstawowe, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane przez ilość obliczeń w rach wirtualnych, które mogą być aprowizowane, pamięci na rzę wirtualną i technologii magazynu używanej do przechowywania danych. Wszystkie zasoby są aprowiedna na poziomie serwera MariaDB. Serwer może mieć jedną lub wiele baz danych.

|    | **Podstawowy** | **Cel ogólny** | **Zoptymalizowana pamięć** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeń | Rdz. |Rdz. | Rdz. |
| Rejy wirtualne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Pamięć na rów wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | Od 5 GB do 1 TB | Od 5 GB do 4 TB | Od 5 GB do 4 TB |
| Okres przechowywania kopii zapasowej bazy danych | 7 do 35 dni | 7 do 35 dni | 7 do 35 dni |

Aby wybrać warstwę cenową, użyj poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowy | Obciążenia, które wymagają lekkich obliczeń i wydajności we/wy. Przykłady obejmują serwery używane do tworzenia lub testowania lub rzadko używanych aplikacji na małą skalę. |
| Ogólnego przeznaczenia | Większość obciążeń biznesowych, które wymagają zrównoważonych obliczeń i pamięci o skalowalnej przepływności we/wy. Przykłady obejmują serwery do obsługi aplikacji internetowych i mobilnych oraz innych aplikacji dla przedsiębiorstw.|
| Optymalizacja pod kątem pamięci | Obciążenia bazy danych o wysokiej wydajności, które wymagają wydajności w pamięci dla szybszego przetwarzania transakcji i wyższej współbieżności. Przykłady obejmują serwery do przetwarzania danych w czasie rzeczywistym oraz aplikacje transakcyjne lub analityczne o wysokiej wydajności.|

Po utworzeniu serwera liczbę podstawowych i warstwy cenowej (z wyjątkiem i z basic) można zmienić w górę lub w dół w ciągu kilku sekund. Można również niezależnie dostosować ilość miejsca w górę i okres przechowywania kopii zapasowej w górę lub w dół bez przestojów aplikacji. Nie można zmienić typu magazynu kopii zapasowej po utworzeniu serwera. Aby uzyskać więcej informacji, zobacz sekcję [Skalowanie zasobów.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Obliczanie pokoleń i corów wirtualnych

Zasoby obliczeniowe są dostarczane jako rdzenie wirtualne, które reprezentują logiczny procesor procesora podstawowego sprzętu. Procesory logiczne gen 5 są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Magazyn

Magazyn, który aprowizować jest ilość pojemności dostępnej dla usługi Azure Database dla serwera MariaDB. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera MariaDB. Całkowita ilość udostępnianego miejsca określa również pojemność we/wy dostępną dla serwera.

|    | **Podstawowy** | **Cel ogólny** | **Zoptymalizowana pamięć** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Podstawowa pamięć masowa | Magazyn ogólnego przeznaczenia | Magazyn ogólnego przeznaczenia |
| Rozmiar magazynu | Od 5 GB do 1 TB | Od 5 GB do 4 TB | Od 5 GB do 4 TB |
| Rozmiar przyrostu pamięci masowej | 1 GB | 1 GB | 1 GB |
| Liczba operacji we/wy na sekundę | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

Można dodać dodatkową pojemność magazynu podczas i po utworzeniu serwera i zezwolić systemowi na automatyczne zwiększanie pamięci masowej na podstawie zużycia magazynu przez obciążenie.

>[!NOTE]
> Magazyn można skalować tylko w górę, a nie w dół.

Warstwa Podstawowa nie zapewnia gwarancji we/wy. W warstwach cenowych ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci skala we/wy z aprowizowanym rozmiarem magazynu w stosunku 3:1.

Zużycie we/wy można monitorować w witrynie Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [limit magazynowania, procent magazynowania, użyty magazyn i procent we/wy.](concepts-monitoring.md)

### <a name="reaching-the-storage-limit"></a>Osiągnięcie limitu miejsca

Serwery o pojemności mniejszej niż 100 GB są oznaczone tylko do odczytu, jeśli wolny magazyn jest mniejszy niż 5% aprowizowanego rozmiaru magazynu. Serwery z aprowizowanym magazynem o rozmiarze większym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GB.

Na przykład jeśli aprowizacja 110 GB miejsca na przestrzeni, a rzeczywiste wykorzystanie wykracza ponad 105 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli aprowizacji 5 GB miejsca, serwer jest oznaczony tylko do odczytu, gdy wolny magazyn osiągnie mniej niż 256 MB.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. Gdy zwiększysz zaaprowizowaną pojemność magazynu, serwer będzie ponownie gotowy do akceptowania transakcji zapisu.

Zaleca się włączenie automatycznego powiększania magazynu lub skonfigurowanie alertu informującego o zbliżaniu się magazynu serwera do progu, dzięki czemu można uniknąć uzyskania dostępu do stanu tylko do odczytu. Aby uzyskać więcej informacji, zobacz dokumentację [dotyczącą konfigurowania alertu](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatyczne powiększanie pamięci masowej

Automatyczne powiększanie pamięci masowej zapobiega wyczerpaniu się pamięci masowej serwera i stanie się tylko do odczytu. Jeśli automatyczne powiększanie magazynu jest włączone, magazyn automatycznie rośnie bez wpływu na obciążenie. W przypadku serwerów o pojemności mniejszej niż 100 GB aprowizowana rozmiar magazynu jest zwiększany o 5 GB, gdy wolny magazyn jest mniejszy niż 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 10 GB rozmiaru aprowizowanego magazynu. Obowiązują maksymalne limity przechowywania, jak określono powyżej.

Na przykład jeśli aprowizacja 1000 GB miejsca na przestrzeni, a rzeczywiste wykorzystanie wykracza ponad 990 GB, rozmiar magazynu serwera zwiększa się do 1050 GB. Alternatywnie, jeśli aprowizacji 10 GB miejsca, rozmiar magazynu jest zwiększona do 15 GB, gdy mniej niż 1 GB miejsca jest bezpłatny.

Należy pamiętać, że magazyn można skalować tylko w górę, a nie w dół.

## <a name="backup"></a>Backup

Usługa automatycznie wykonuje kopie zapasowe serwera. Można wybrać okres przechowywania z zakresu od 7 do 35 dni. Serwery ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci mogą wybrać magazyn geograficznie nadmiarowy dla kopii zapasowych. Dowiedz się więcej o kopiach zapasowych w [artykule pojęcia](concepts-backup.md).

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rów wirtualnych, warstwę cenową (z wyjątkiem i z poziomu podstawowego), ilość miejsca do magazynowania i okres przechowywania kopii zapasowej. Nie można zmienić typu magazynu kopii zapasowej po utworzeniu serwera. Liczbę korów wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół z 7 do 35 dni. Rozmiar magazynu można tylko zwiększyć. Skalowanie zasobów można wykonać za pośrednictwem portalu lub interfejsu wiersza polecenia platformy Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Po zmianie liczby koperów wirtualnych lub warstwy cenowej kopia oryginalnego serwera jest tworzona przy nowej alokacji obliczeń. Gdy nowy serwer zostanie uruchomiony, połączenia zostaną przełączone na nowy serwer. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Długość tego okresu może być różna, lecz w większości przypadków jest to mniej niż minuta.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowej są prawdziwe operacje online. Nie ma żadnych przestojów, a aplikacja nie ma wpływu. W skali We/Wy o rozmiarze aprowizowanego magazynu można zwiększyć liczbę usług We/Wy dostępne dla serwera, skalując magazyn.

## <a name="pricing"></a>Cennik

Aby uzyskać najbardziej aktualne informacje o cenach, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/mariadb/)usług . Aby wyświetlić koszt żądanej konfiguracji, [witryna Azure portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) zawiera miesięczny koszt na karcie **Warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz użyć kalkulatora cen platformy Azure, aby uzyskać szacunkową cenę. W witrynie [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **Bazy danych** i wybierz pozycję Usługa Azure Database **for MariaDB,** aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [ograniczeniach usług](concepts-limits.md).
- Dowiedz się, jak [utworzyć serwer MariaDB w witrynie Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
