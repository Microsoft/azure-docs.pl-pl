---
title: Konfiguracje węzłów obsługiwane przez usługę Azure HDInsight
description: Poznaj minimalne i zalecane konfiguracje dla węzłów klastra usługi HDInsight.
keywords: rozmiary maszyn wirtualnych, rozmiary klastrów, Konfiguracja klastra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: 0bf5559590b66400fc4fc4dc27ea88c3522effb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83646623"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jakie są domyślne i zalecane konfiguracje węzłów dla usługi Azure HDInsight?

W tym artykule omówiono domyślne i zalecane konfiguracje węzłów dla klastrów usługi Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna i minimalna zalecana konfiguracja węzła oraz rozmiary maszyn wirtualnych dla klastrów

W poniższych tabelach przedstawiono domyślne i zalecane rozmiary maszyn wirtualnych dla klastrów usługi HDInsight.  Te informacje są niezbędne, aby zrozumieć rozmiary maszyn wirtualnych używane podczas tworzenia skryptów programu PowerShell lub interfejsu wiersza polecenia platformy Azure w celu wdrożenia klastrów usługi HDInsight.

Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Jedynymi typami klastrów z dyskami danych są klastry Kafka i HBase z włączoną funkcją przyspieszonego zapisywania. Usługa HDInsight obsługuje rozmiary dysków P30 i S30 w tych scenariuszach. W przypadku wszystkich innych typów klastrów Usługa HDInsight zapewnia zarządzane miejsce na dysku z klastrem. Począwszy od 11/07/2019, rozmiar dysku zarządzanego każdego węzła w nowo utworzonym klastrze wynosi 128 GB. Nie można go zmienić.

W poniższej tabeli zestawiono specyfikacje wszystkich minimalnych zalecanych typów maszyn wirtualnych używanych w tym dokumencie.

| Rozmiar              | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standardowa_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standardowa_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standardowa_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standardowa_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standardowa_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Aby uzyskać więcej informacji na temat specyfikacji poszczególnych typów maszyn wirtualnych, zobacz następujące dokumenty:

* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: Dv2 Series 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci: Dv2 Series 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: Av2 Series 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Wszystkie obsługiwane regiony, z wyjątkiem Brazylii Południowe i Japonia Zachodnia

> [!Note]
> Aby uzyskać identyfikator jednostki SKU do użycia w programie PowerShell i innych skryptach, Dodaj `Standard_` na początku wszystkie jednostki SKU maszyny wirtualnej w poniższych tabelach. Na przykład `D12_v2` `Standard_D12_v2` .

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Nagłówek: domyślny rozmiar maszyny wirtualnej | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Nagłówek: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Proces roboczy: domyślny rozmiar maszyny wirtualnej | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 z 2 dyskami S30 na brokera |
| Proces roboczy: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Dozorcy: domyślny rozmiar maszyny wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| Dozorcy: minimalne zalecane rozmiary maszyn wirtualnych |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Usługi ML: domyślny rozmiar maszyny wirtualnej |  |  |  |  |  | D4_v2 |  |
| Usługi ML: minimalny zalecany rozmiar maszyny wirtualnej |  |  |  |  |  | D4_v2 |  |

\* = Rozmiary maszyn wirtualnych dla klastrów usługi Spark pakiet Enterprise Security (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brazylia Południowa i Japonia Zachodnia

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | Usługi ML |
|---|---|---|---|---|---|---|
| Nagłówek: domyślny rozmiar maszyny wirtualnej | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Nagłówek: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Proces roboczy: domyślny rozmiar maszyny wirtualnej | D4 | D4 | D14 | D3 | D13 | D4 |
| Proces roboczy: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Dozorcy: domyślny rozmiar maszyny wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Dozorcy: minimalne zalecane rozmiary maszyn wirtualnych |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Usługi ML: domyślne rozmiary maszyn wirtualnych |  |  |  |  |  | D4 |
| Usługi ML: minimalne zalecane rozmiary maszyn wirtualnych |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Nagłówek jest znany jako *Nimbus* dla typu klastra burza.
> - Proces roboczy jest znany jako *opiekun* dla typu klastra burza.
> - Proces roboczy jest znany jako *region* dla typu klastra HBase.

## <a name="next-steps"></a>Następne kroki

* [Jakie składniki Apache Hadoop i wersje są dostępne w usłudze HDInsight?](hdinsight-component-versioning.md)
