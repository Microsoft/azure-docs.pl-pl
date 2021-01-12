---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121679"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji kopiowania etapowego. |
| `*.database.windows.net`      | 1433           | Wymagane tylko w przypadku kopiowania z lub do Azure SQL Database lub do usługi Azure Synapse Analytics i opcjonalnych w inny sposób. Funkcja przygotowana-Copy służy do kopiowania danych do SQL Database lub usługi Azure Synapse Analytics bez konieczności otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko w przypadku kopiowania z lub do Azure Data Lake Store i opcjonalnych w inny sposób. |
