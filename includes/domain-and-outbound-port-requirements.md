---
title: dołączanie pliku
description: dołączanie pliku
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74559300"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez własne środowisko Integration Runtime do łączenia się z usługami przenoszenia danych w Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. Jeśli została wyłączona funkcja autoaktualizowania, możesz pominąć konfigurowanie tej domeny. |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji [kopiowania etapowego](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Wymagane tylko w przypadku kopiowania z lub do Azure SQL Database lub Azure SQL Data Warehouse i opcjonalnych w inny sposób. Funkcja przygotowana-Copy służy do kopiowania danych do SQL Database lub SQL Data Warehouse bez otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko w przypadku kopiowania z lub do Azure Data Lake Store i opcjonalnych w inny sposób. |
