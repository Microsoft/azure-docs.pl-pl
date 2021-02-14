---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389510"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji kopiowania etapowego. |
| `*.database.windows.net`      | 1433           | Wymagane tylko w przypadku kopiowania z lub do Azure SQL Database lub do usługi Azure Synapse Analytics i opcjonalnych w inny sposób. Funkcja przygotowana-Copy służy do kopiowania danych do SQL Database lub usługi Azure Synapse Analytics bez konieczności otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko w przypadku kopiowania z lub do Azure Data Lake Store i opcjonalnych w inny sposób. |
