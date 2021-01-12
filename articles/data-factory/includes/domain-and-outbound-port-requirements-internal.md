---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121674"
---
| Nazwy domen                                          | Porty wychodzące | Opis                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Chmura publiczna: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> Chinach `*.servicebus.chinacloudapi.cn`   | 443            | Wymagane przez własne środowisko Integration Runtime do interaktywnego tworzenia. |
| Chmura publiczna: `{datafactory}.{region}.datafactory.azure.net`<br> oraz `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> Chinach `{datafactory}.{region}.datafactory.azure.cn` | 443            | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. <br>W przypadku nowych utworzonych Data Factory w chmurze publicznej Znajdź nazwę FQDN z własnego klucza Integration Runtime, który jest w formacie {DataFactory}. {Region}. DataFactory. Azure. NET. W przypadku starej fabryki danych, jeśli nie widzisz nazwy FQDN w kluczu integracji samodzielnej, zamiast tego należy użyć *. frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. Jeśli została wyłączona funkcja autoaktualizowania, możesz pominąć konfigurowanie tej domeny. |
| Adres URL magazynu kluczy | 443           | Wymagane przez Azure Key Vault, jeśli poświadczenia są przechowywane w Key Vault. |
