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
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93311849"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z usługi Azure Blob Storage

Proces nauki danych zespołu wymaga pozyskania lub załadowania danych do różnych środowisk magazynu, które mają być przetwarzane lub analizowane w najbardziej odpowiednim przypadku na każdym etapie procesu.

## <a name="different-technologies-for-moving-data"></a>Różne technologie do przemieszczania danych

W poniższych artykułach opisano sposób przenoszenia danych do i z usługi Azure Blob Storage przy użyciu różnych technologii.

* [Azure Storage — Eksplorator](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSI](move-data-to-azure-blob-using-ssis.md)

Która metoda jest Najlepsza dla Ciebie, zależy od danego scenariusza. [Scenariusze dotyczące zaawansowanej analizy w Azure Machine Learning](plan-sample-scenarios.md) artykule ułatwiają określenie potrzebnych zasobów dla różnych przepływów pracy analizy danych używanych w procesie zaawansowanego analizowania.

> [!NOTE]
> Pełny Wprowadzenie do usługi Azure Blob Storage można znaleźć w [temacie Podstawowe informacje](../../storage/blobs/storage-quickstart-blobs-dotnet.md) dotyczące usługi Azure BLOB i [usłudze Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).
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
* Instrukcje dotyczące tworzenia konta magazynu i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).