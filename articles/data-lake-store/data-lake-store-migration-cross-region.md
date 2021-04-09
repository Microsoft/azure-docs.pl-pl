---
title: Azure Data Lake Storage Gen1 migracji między regionami | Microsoft Docs
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania i dokończyć migrację do Azure Data Lake Storage Gen1, ponieważ staną się dostępne w nowych regionach.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723810"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrowanie Azure Data Lake Storage Gen1 między regionami

Ponieważ Azure Data Lake Storage Gen1 staną się dostępne w nowych regionach, można dokonać jednorazowej migracji, aby skorzystać z nowego regionu. Dowiedz się, co należy wziąć pod uwagę podczas planowania i dokończyć migrację.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie bezpłatnego konta platformy Azure już dzisiaj](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1 w dwóch różnych regionach**. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Najpierw Zidentyfikuj strategię migracji, która najlepiej sprawdza się w przypadku aplikacji, która zapisuje, odczytuje lub przetwarza dane w Data Lake Storage Gen1. Po wybraniu strategii należy wziąć pod uwagę wymagania dotyczące dostępności aplikacji oraz przestoje występujące podczas migracji. Na przykład najprostszym podejściem może być użycie modelu migracji chmurowej "Unieś-and-Shift". W ramach tej metody wstrzymasz aplikację w istniejącym regionie, podczas gdy wszystkie dane są kopiowane do nowego regionu. Po zakończeniu procesu kopiowania Wznów swoją aplikację w nowym regionie, a następnie usuń stare konto Data Lake Storage Gen1. Wymagana jest przestój podczas migracji.

Aby zmniejszyć czas przestoju, możesz od razu rozpocząć pozyskiwanie nowych danych w nowym regionie. Jeśli potrzebujesz minimalnych danych, uruchom aplikację w nowym regionie. W tle Kontynuuj kopiowanie starszych danych z istniejącego konta Data Lake Storage Gen1 do nowego konta Data Lake Storage Gen1 w nowym regionie. Korzystając z tej metody, można przełączać się do nowego regionu z małym przestojem. Gdy wszystkie starsze dane zostały skopiowane, usuń stare konto Data Lake Storage Gen1.

Inne ważne informacje, które należy wziąć pod uwagę podczas planowania migracji:

* **Wolumin danych**. Ilość danych (w gigabajtach, liczba plików i folderów itd.) wpływa na czas i zasoby, które są potrzebne podczas migracji.

* **Nazwa konta Data Lake Storage Gen1**. Nowa nazwa konta w nowym regionie musi być globalnie unikatowa. Na przykład nazwa starego konta Data Lake Storage Gen1 w regionie Wschodnie stany USA 2 może być contosoeastus2.azuredatalakestore.net. Możesz nazwać nowe konto Data Lake Storage Gen1 w regionie EU Północna contosonortheu.azuredatalakestore.net.

* **Narzędzia**. Zaleca się, aby do kopiowania plików Data Lake Storage Gen1 użyć [działania kopiowania Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) . Data Factory obsługuje przenoszenie danych z wysoką wydajnością i niezawodnością. Należy pamiętać, że Data Factory kopiuje tylko hierarchię folderów i zawartość plików. Należy ręcznie zastosować wszystkie listy kontroli dostępu (ACL), które są używane w starym koncie do nowego konta. Aby uzyskać więcej informacji, w tym o docelowych wydajności dla scenariuszy najlepszego przypadku, zobacz temat [wydajność i dostrajanie działania kopiowania](../data-factory/copy-activity-performance.md). Jeśli chcesz, aby dane były kopiowane szybciej, może być konieczne użycie dodatkowych jednostek przenoszenia danych w chmurze. Inne narzędzia, takie jak AdlCopy, nie obsługują kopiowania danych między regionami.  

* **Opłaty za przepustowość**. [Opłaty za przepustowość](https://azure.microsoft.com/pricing/details/bandwidth/) są stosowane, ponieważ dane są przesyłane z regionu platformy Azure.

* **Listy ACL danych**. Zabezpiecz dane w nowym regionie, stosując listy ACL do plików i folderów. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Zalecamy użycie migracji w celu zaktualizowania i dostosowania list kontroli dostępu. Możesz chcieć użyć ustawień podobnych do Twoich bieżących ustawień. Listy ACL, które są stosowane do każdego pliku, można wyświetlić za pomocą Azure Portal, [poleceń cmdlet programu PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)lub zestawów SDK.  

* **Lokalizacja usług analitycznych**. Aby uzyskać najlepszą wydajność, usługi analizy, takie jak Azure Data Lake Analytics lub Azure HDInsight, powinny znajdować się w tym samym regionie co dane.  

## <a name="next-steps"></a>Następne kroki
* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
