---
title: plik dołączany
description: plik dołączany
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596122"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez własne środowisko Integration Runtime do łączenia się z usługami przenoszenia danych w Azure Data Factory. |
| `{datafactory}.{region}.datafactory.azure.net`<br> oraz `*.frontend.clouddatahub.net` | 443            | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. <br>W przypadku nowych utworzonych Data Factory Znajdź nazwę FQDN z własnego klucza Integration Runtime, który jest w formacie {DataFactory}. {Region}. DataFactory. Azure. NET. W przypadku starej fabryki danych, jeśli nie widzisz nazwy FQDN w kluczu integracji samodzielnej, zamiast tego należy użyć *. frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. Jeśli została wyłączona funkcja autoaktualizowania, możesz pominąć konfigurowanie tej domeny. |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji [kopiowania etapowego](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Wymagane tylko w przypadku kopiowania z lub do Azure SQL Database lub do usługi Azure Synapse Analytics i opcjonalnych w inny sposób. Funkcja przygotowana-Copy służy do kopiowania danych do programu SQL Database lub Synapse Analytics bez otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko w przypadku kopiowania z lub do Azure Data Lake Store i opcjonalnych w inny sposób. |
