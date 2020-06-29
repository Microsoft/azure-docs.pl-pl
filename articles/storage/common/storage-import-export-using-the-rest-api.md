---
title: Korzystanie z interfejsu API REST usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, gdzie znaleźć zasoby do użycia interfejsu API REST usługi Azure Import/Export, w tym informacje o tym, jak i materiałach referencyjnych.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: ae80c00fd81d0966167d2adf00764a0b1089c163
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514112"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Import/Export

Usługa Microsoft Azure Import/Export udostępnia interfejs API REST umożliwiający programowe sterowanie zadaniami importu/eksportu. Za pomocą interfejsu API REST można wykonać wszystkie operacje importu/eksportu, które można wykonać przy użyciu [Azure Portal](https://portal.azure.com/). Ponadto można użyć interfejsu API REST do wykonywania określonych operacji szczegółowych, takich jak zapytanie o procent wykonania zadania, które nie jest obecnie dostępne w Azure Portal.

Zapoznaj się z tematem [Korzystanie z usługi Microsoft Azure Import/Export do transferowania danych do BLOB Storage](../storage-import-export-service.md) w celu omówienia usługi Import/Export oraz samouczka, w którym pokazano, jak za pomocą portalu tworzyć i zarządzać zadaniami importu i eksportu.

## <a name="service-endpoints"></a>Punkty końcowe usługi

Usługa Azure Import/Export jest dostawcą zasobów dla Azure Resource Manager i udostępnia zestaw interfejsów API REST w następującym punkcie końcowym HTTPS do zarządzania zadaniami importu/eksportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Obsługa wersji

Żądania do usługi Import/Export muszą określać `api-version` parametr i ustawić jego wartość na `2016-11-01` .

## <a name="importexport-service-operations"></a>Operacje usługi Import/Export

[Tworzenie zadania importu](../storage-import-export-creating-an-import-job.md)

[Tworzenie zadania eksportu](../storage-import-export-creating-an-export-job.md)

[Pobieranie informacji o stanie zadania](storage-import-export-retrieving-state-info-for-a-job.md)

[Wyliczanie zadań](../storage-import-export-enumerating-jobs.md)

[Anulowanie i usuwanie zadań](storage-import-export-cancelling-and-deleting-jobs.md)

[Tworzenie kopii zapasowej manifestów dysków](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostyka i odzyskiwanie po błędach zadań usługi Import/Export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Następne kroki

* [REST importowania/eksportowania magazynu](/rest/api/storageimportexport)
