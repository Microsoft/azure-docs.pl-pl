---
title: Opcje obliczeniowe i magazynowe — Azure Database for PostgreSQL — serwer elastyczny
description: W tym artykule opisano opcje obliczeń i magazynu na serwerze elastycznym Azure Database for PostgreSQL.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: b76b6ff788d3d7f44db33af96944d528282f0ac7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712219"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Opcje obliczeń i magazynowania w Azure Database for PostgreSQL-elastycznym serwerze

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer Azure Database for PostgreSQL można utworzyć w jednej z trzech różnych warstw cenowych: możliwe do przetworzenia, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości obliczeń w rdzeni wirtualnych, które mogą być inicjowane, pamięć na rdzeń wirtualny i technologia magazynowania służąca do przechowywania danych. Wszystkie zasoby są obsługiwane na poziomie serwera PostgreSQL. Serwer może mieć jedną lub wiele baz danych.

| Zasób/warstwa | **Z możliwością przenoszenia** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Rdzeni wirtualnych | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Pamięć na rdzeń wirtualny | Zmienna | 4 GB | 6,75 do 8 GB |
| Rozmiar magazynu | 32 GB do 16 TB | 32 GB do 16 TB | 32 GB do 16 TB |
| Okres przechowywania kopii zapasowej bazy danych | od 7 do 35 dni | od 7 do 35 dni | od 7 do 35 dni |

Aby wybrać warstwę cenową, należy użyć poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Z możliwością przenoszenia | Najlepsze w przypadku obciążeń, które nie potrzebują pełnego procesora CPU w sposób ciągły. |
| Ogólnego przeznaczenia | Większość obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy. Są to na przykład serwery do hostowania aplikacji internetowych i mobilnych oraz inne aplikacje dla przedsiębiorstw.|
| Optymalizacja pod kątem pamięci | Obciążenia baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności. Są to na przykład serwery przetwarzające dane w czasie rzeczywistym oraz wysokowydajne aplikacje transakcyjne i analityczne.|

Po utworzeniu serwera można zmienić warstwę obliczeniową, liczbę rdzeni wirtualnych i rozmiar magazynu w ciągu kilku sekund. Można też niezależnie dostosować okres przechowywania kopii zapasowej w górę lub w dół. Aby uzyskać więcej informacji, zobacz sekcję [skalowanie zasobów](#scale-resources) .

## <a name="compute-tiers-vcores-and-server-types"></a>Warstwy obliczeń, rdzeni wirtualnych i typy serwerów

Zasoby obliczeniowe mogą być wybierane na podstawie warstwy, rdzeni wirtualnych i rozmiaru pamięci. Rdzeni wirtualnych reprezentuje logiczny procesor CPU bazowego sprzętu.

Szczegółowe specyfikacje dostępnych typów serwerów są następujące:

| Nazwa jednostki SKU             | Rdzeni wirtualnych | Rozmiar pamięci | Maksymalna obsługiwana liczba IOPS | Maksymalna obsługiwana przepustowość we/wy |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Z możliwością przenoszenia**        |        |             |                    |                             |
| B1MS                 | 1      | 2 GiB       | 640                | 15 MiB/s                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/s                  |
| **Ogólnego przeznaczenia**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/s                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/s                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/s                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/s                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/s                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/s                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/s                 |
| **Zoptymalizowane pod kątem pamięci** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/s                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/s                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/s                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/s                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/s                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/s                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/s                 |

## <a name="storage"></a>Storage

Zapewniana ilość miejsca w magazynie to pojemność magazynu dostępna dla serwera Azure Database for PostgreSQL. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera PostgreSQL. Całkowita ilość dostępnego miejsca w magazynie określa również wydajność we/wy dostępną dla serwera.

Magazyn jest dostępny w następujących stałych rozmiarach:

| Rozmiar dysku | Liczba operacji we/wy na sekundę |
|:---|:---|
| 32 GiB | Zainicjowano 120 do 3 500 |
| 64 GiB | Zainicjowano 240 do 3 500 |
| 128 GiB | Zainicjowano 500 do 3 500 |
| 256 GiB | Zainicjowano 1100 do 3 500 |
| 512 GiB | Zainicjowano 2300 do 3 500 |
| 1 TiB | 5000 |
| 2 TiB | 7500 |
| 4 TiB | 7500 |
| 8 TiB | 16 000 |
| 16 TiB | 18 000 |

Należy zauważyć, że operacje we/wy są również ograniczone przez typ maszyny wirtualnej. Mimo że można wybrać dowolny rozmiar magazynu niezależny od typu serwera, może nie być możliwe użycie wszystkich operacji we/wy, które zapewnia magazyn, szczególnie w przypadku wybrania serwera z niewielką liczbą rdzeni wirtualnych.

Podczas tworzenia serwera można dodać dodatkową pojemność magazynu.

>[!NOTE]
> Magazyn można skalować w górę, nie w dół.

Możesz monitorować użycie we/wy w Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [Limit magazynu, procent magazynu, użycie magazynu i procent operacji we/wy](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Maksymalna liczba operacji we/wy dla konfiguracji

|Nazwa jednostki SKU            |Rozmiar magazynu w GiB                       |32 |64 |128 |256 |512  |1 024|2048|4 096|8192 |16 384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maksymalna liczba IOPS                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Z możliwością przenoszenia**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1MS                |LICZBA OPERACJI WE/WY 640                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |LICZBA OPERACJI WE/WY 1280                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Ogólnego przeznaczenia** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |LICZBA OPERACJI WE/WY 3200                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |LICZBA OPERACJI WE/WY 6 400                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |LICZBA OPERACJI WE/WY 12 800                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|D16s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Zoptymalizowane pod kątem pamięci**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |LICZBA OPERACJI WE/WY 3200                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |LICZBA OPERACJI WE/WY 6 400                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |LICZBA OPERACJI WE/WY 12 800                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|E16s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |LICZBA OPERACJI WE/WY 18 000                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

W przypadku zaznaczenia z liczbą operacji we \* /wy są ograniczone przez wybrany typ maszyny wirtualnej. W przeciwnym razie operacje we/wy są ograniczone przez wybrany rozmiar magazynu.

>[!NOTE]
> W metrykach może być widocznych więcej operacji we/wy z powodu dużego poziomu dysku. Więcej informacji można znaleźć w [dokumentacji](https://docs.microsoft.com/azure/virtual-machines/disk-bursting#disk-level-bursting) . 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maksymalna przepustowość operacji we/wy (MiB/s) dla konfiguracji

|Nazwa jednostki SKU            |Rozmiar magazynu, GiB                             |32 |64 |128 |256 |512  |1 024|2048|4 096|8192 |16 384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Przepustowość magazynu, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Z możliwością przenoszenia**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1MS                |10 MiB/s                                    |dziesięć|dziesięć|dziesięć |dziesięć |dziesięć  |dziesięć  |dziesięć  |dziesięć  |dziesięć   |dziesięć   |
|B2s                 |15 MiB/s                                    |15000|15000|15000 |15000 |15000  |15000  |15000  |15000  |15000   |15000   |
|**Ogólnego przeznaczenia** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Zoptymalizowane pod kątem pamięci**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Po oznaczeniu z opcją \* przepustowość we/wy jest ograniczona przez wybrany typ maszyny wirtualnej. W przeciwnym razie przepustowość we/wy jest ograniczona przez wybrany rozmiar magazynu.

### <a name="reaching-the-storage-limit"></a>Osiągnięcie limitu magazynu

Po osiągnięciu limitu magazynu serwer zacznie zwracać błędy i uniemożliwia dalsze modyfikacje. Może to również spowodować problemy z innymi działaniami operacyjnymi, takimi jak kopie zapasowe i archiwum WAL.

Aby uniknąć tej sytuacji, gdy użycie magazynu osiągnie 95% lub dostępna pojemność jest mniejsza niż 5 GiB, serwer zostanie automatycznie przełączony do **trybu tylko do odczytu**.

Zalecamy, aby aktywnie monitorować miejsce na dysku, które jest używane, i zwiększyć rozmiar dysku przed sytuacją braku miejsca w magazynie. Możesz skonfigurować alert, aby powiadomić Cię, gdy magazyn serwera zbliża się do dysku, aby uniknąć problemów z uruchamianiem dysku. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [sposobu konfigurowania alertu](howto-alert-on-metrics.md).


### <a name="storage-auto-grow"></a>Autouzupełnianie magazynu

Funkcja autozwiększania magazynu nie jest jeszcze dostępna dla serwera elastycznego.

## <a name="backup"></a>Backup

Usługa automatycznie pobiera kopie zapasowe serwera. Możesz wybrać okres przechowywania z zakresu od 7 do 35 dni. Dowiedz się więcej o kopiach zapasowych w [artykule pojęcia](concepts-backup-restore.md).

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rdzeni wirtualnych, warstwę obliczeń, ilość miejsca do magazynowania i okres przechowywania kopii zapasowych. Liczbę rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Rozmiar magazynu można zwiększyć tylko. Skalowanie zasobów można przeprowadzić za pomocą portalu lub interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Rozmiar magazynu można zwiększyć tylko. Po zwiększeniu nie można wrócić do mniejszego rozmiaru magazynu.

W przypadku zmiany liczby rdzeni wirtualnych lub warstwy obliczeniowej serwer zostanie uruchomiony ponownie, aby nowy typ serwera został uwzględniony. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Długość tego okresu może być różna, lecz w większości przypadków jest to mniej niż minuta. Skalowanie magazynu działa tak samo, jak również wymaga ponownego uruchomienia.

Zmiana okresu przechowywania kopii zapasowej jest operacją online.

## <a name="pricing"></a>Ceny

Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/PostgreSQL/)usługi. Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) przedstawia miesięczny koszt na karcie **warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** i wybierz **Azure Database for PostgreSQL** , aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak utworzyć serwer PostgreSQL w portalu](how-to-manage-server-portal.md).
- Dowiedz się więcej o [limitach usługi](concepts-limits.md).
