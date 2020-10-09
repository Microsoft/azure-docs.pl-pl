---
title: Przenoszenie danych do i z usługi Azure Blob Storage — proces nauki danych zespołu
description: Przenoszenie danych do i z usługi Azure Blob Storage za pomocą Eksplorator usługi Azure Storage, AzCopy, Python i SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76717574"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z usługi Azure Blob Storage

Proces nauki danych zespołu wymaga pozyskania lub załadowania danych do różnych środowisk magazynu, które mają być przetwarzane lub analizowane w najbardziej odpowiednim przypadku na każdym etapie procesu.

## <a name="different-technologies-for-moving-data"></a>Różne technologie do przemieszczania danych

W poniższych artykułach opisano sposób przenoszenia danych do i z usługi Azure Blob Storage przy użyciu różnych technologii.

* [Azure Storage — Eksplorator](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Która metoda jest Najlepsza dla Ciebie, zależy od danego scenariusza. [Scenariusze dotyczące zaawansowanej analizy w Azure Machine Learning](plan-sample-scenarios.md) artykule ułatwiają określenie potrzebnych zasobów dla różnych przepływów pracy analizy danych używanych w procesie zaawansowanego analizowania.

> [!NOTE]
> Pełny Wprowadzenie do usługi Azure Blob Storage można znaleźć w [temacie Podstawowe informacje](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) dotyczące usługi Azure BLOB i [usłudze Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Używanie usługi Azure Data Factory

Alternatywnie można użyć [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do: 

* Tworzenie i planowanie potoku pobierającego dane z usługi Azure Blob Storage 
* Przekaż go do opublikowanej usługi sieci Web Azure Machine Learning. 
* Otrzymuj wyniki analizy predykcyjnej i 
* Przekaż wyniki do magazynu. 

Aby uzyskać więcej informacji, zobacz [Tworzenie potoków predykcyjnych przy użyciu Azure Data Factory i Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz subskrypcję platformy Azure, konto magazynu i odpowiedni klucz magazynu dla tego konta. Przed przekazaniem/pobraniem danych należy znać nazwę konta i klucz konta usługi Azure Storage.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia konta magazynu i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).

