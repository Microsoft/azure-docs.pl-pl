---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 626074940ced57bdb1ae93f494b7a3847ef83a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96445008"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez własne środowisko Integration Runtime do interaktywnego tworzenia. |
| `{datafactory}.{region}.datafactory.azure.net`<br> oraz `*.frontend.clouddatahub.net` | 443            | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. <br>W przypadku nowych utworzonych Data Factory Znajdź nazwę FQDN z własnego klucza Integration Runtime, który jest w formacie {DataFactory}. {Region}. DataFactory. Azure. NET. W przypadku starej fabryki danych, jeśli nie widzisz nazwy FQDN w kluczu integracji samodzielnej, zamiast tego należy użyć *. frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. Jeśli została wyłączona funkcja autoaktualizowania, możesz pominąć konfigurowanie tej domeny. |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji [kopiowania etapowego](../articles/data-factory/copy-activity-performance.md#staged-copy) . |
| `*.database.windows.net`      | 1433           | Wymagane tylko w przypadku kopiowania z lub do Azure SQL Database lub do usługi Azure Synapse Analytics i opcjonalnych w inny sposób. Funkcja przygotowana-Copy służy do kopiowania danych do SQL Database lub usługi Azure Synapse Analytics bez konieczności otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko w przypadku kopiowania z lub do Azure Data Lake Store i opcjonalnych w inny sposób. |
