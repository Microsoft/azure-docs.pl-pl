---
title: Jak wybrać odpowiedni rozmiar maszyny wirtualnej dla klastra usługi Azure HDInsight
description: Dowiedz się, jak wybrać odpowiedni rozmiar maszyny wirtualnej dla klastra usługi HDInsight.
keywords: rozmiary maszyn wirtualnych, rozmiary klastrów, Konfiguracja klastra
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931776"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Wybieranie odpowiedniego rozmiaru maszyny wirtualnej dla klastra usługi Azure HDInsight

W tym artykule omówiono sposób wybierania odpowiedniego rozmiaru maszyny wirtualnej dla różnych węzłów w klastrze usługi HDInsight. 

Zacznij od ustalenia, jak właściwości maszyny wirtualnej, takie jak przetwarzanie procesora CPU, rozmiar pamięci RAM i opóźnienie sieci, wpłyną na przetwarzanie obciążeń. Następnie zastanów się nad aplikacją i jej dopasowaniem do tego, co są zoptymalizowane pod kątem różnych rodzin maszyn wirtualnych. Upewnij się, że rodzina maszyn wirtualnych, której chcesz użyć, jest zgodna z typem klastra, który ma zostać wdrożony. Listę wszystkich obsługiwanych i zalecanych rozmiarów maszyn wirtualnych dla każdego typu klastra można znaleźć w temacie [obsługiwane konfiguracje węzłów usługi Azure HDInsight](hdinsight-supported-node-configuration.md). Na koniec można użyć procesu testów porównawczych do testowania niektórych przykładowych obciążeń i sprawdzenia, która jednostka SKU w tej rodzinie jest odpowiednia dla Ciebie.

Aby uzyskać więcej informacji na temat planowania innych aspektów klastra, takich jak wybór typu magazynu lub rozmiaru klastra, zobacz [Planowanie pojemności klastrów usługi HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Właściwości maszyny wirtualnej i obciążenia danych Big Data

Rozmiar i typ maszyny wirtualnej są określane przez procesor CPU, rozmiar pamięci RAM i opóźnienie sieci:

- Procesor: rozmiar maszyny wirtualnej określa liczbę rdzeni. Im więcej rdzeni, tym większy stopień równoległych obliczeń każdego węzła. Ponadto niektóre typy maszyn wirtualnych mają szybsze rdzenie.

- Pamięć RAM: rozmiar maszyny wirtualnej określa również ilość pamięci RAM dostępnej w maszynie wirtualnej. W przypadku obciążeń, które przechowują dane w pamięci do przetworzenia, zamiast odczytywania z dysku, upewnij się, że węzły procesu roboczego mają wystarczającą ilość pamięci, aby dopasować dane.

- Sieć: w przypadku większości typów klastrów dane przetwarzane przez klaster nie są na dysku lokalnym, ale raczej w zewnętrznej usłudze magazynu, takiej jak Data Lake Storage lub Azure Storage. Należy wziąć pod uwagę przepustowość sieci i przepływność między węzłową maszyną wirtualną a usługą magazynu. Przepustowość sieci dostępna dla maszyny wirtualnej zwykle wzrasta o większych rozmiarach. Aby uzyskać szczegółowe informacje, zobacz [Omówienie rozmiarów maszyn wirtualnych](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>Omówienie optymalizacji maszyn wirtualnych

Rodziny maszyn wirtualnych na platformie Azure są zoptymalizowane pod kątem zgodności z różnymi przypadkami użycia. W poniższej tabeli znajdują się najbardziej popularne przypadki użycia i rodziny maszyn wirtualnych, które są do nich zgodne.

| Typ                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Poziom wejścia](../virtual-machines/sizes-general.md)          | A, Av2  | Mają wydajność procesora CPU i konfiguracje pamięci, które najlepiej pasują do obciążeń poziomu wejścia, takich jak programowanie i testowanie. Są one ekonomiczne i zapewniają tanią opcję rozpoczęcia pracy z platformą Azure. |
| [Ogólnego przeznaczenia](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Zrównoważony współczynnik mocy procesora CPU w stosunku do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/sizes-compute.md)        | F           | Duży współczynnik mocy procesora CPU w stosunku do pamięci. Dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines/sizes-memory.md)         | Esv3, EV3  | Duży współczynnik pamięci w stosunku do mocy procesora CPU. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |

- Aby uzyskać informacje o cenach dostępnych wystąpień maszyn wirtualnych w regionach obsługiwanych przez usługi HDInsight, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Koszt oszczędności typów maszyn wirtualnych dla obciążeń lekkich

Jeśli masz wymagania dotyczące przetwarzania światła, [Seria F](https://azure.microsoft.com/blog/f-series-vm-size/) może być dobrym wyborem, aby rozpocząć pracę z usługą HDInsight. Oferując niższą cenę za godzinę, seria F zapewnia najlepszy w portfolio platformy Azure stosunek ceny do wydajności, określany na podstawie liczby jednostek ACU (Azure Compute Unit) na procesor wirtualny vCPU.

W poniższej tabeli opisano typy klastrów i typy węzłów, które można tworzyć przy użyciu maszyn wirtualnych z serii Fsv2.

| Typ klastra | Wersja | Węzeł procesu roboczego | Węzeł główny | Węzeł dozorcy |
|---|---|---|---|---|
| platforma Spark | Wszystko | F4 i nowsze | nie | nie |
| Hadoop | Wszystko | F4 i nowsze | nie | nie |
| Kafka | Wszystko | F4 i nowsze | nie | nie |
| HBase | Wszystko | F4 i nowsze | nie | nie |
| LLAP | wyłączone | nie | nie | nie |
| Storm | wyłączone | nie | nie | nie |
| Usługa ML | TYLKO HDI 3,6 | F4 i nowsze | nie | nie |

Aby wyświetlić specyfikacje każdej jednostki SKU serii F, zobacz [rozmiary maszyn wirtualnych z serii f](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Testów porównawczych

Test porównawczy to proces uruchamiania symulowanych obciążeń na różnych maszynach wirtualnych w celu mierzenia, jak będą one działać w przypadku obciążeń produkcyjnych. 

Aby uzyskać więcej informacji na temat tworzenia wzorców dla jednostek SKU maszyn wirtualnych i rozmiarów klastrów, zobacz [Planowanie pojemności klastra w usłudze Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Następne kroki

- [Konfiguracje węzłów obsługiwane przez usługę Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Sizes for Linux virtual machines in Azure](../virtual-machines/sizes.md) (Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure)