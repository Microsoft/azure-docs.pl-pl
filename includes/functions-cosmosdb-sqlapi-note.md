---
title: dołączanie pliku
description: dołączanie pliku
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279620"
---
Użycie powiązań usługi Azure Cosmos DB jest obsługiwane tylko w przypadku interfejsu API SQL. W przypadku wszystkich innych Azure Cosmos DB interfejsów API należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API, w tym [interfejsu api Azure Cosmos DB dla MongoDB](../articles/cosmos-db/mongodb-introduction.md), [interfejs API Cassandra](../articles/cosmos-db/cassandra-introduction.md), [interfejsu API Gremlin](../articles/cosmos-db/graph-introduction.md)i [interfejs API tabel](../articles/cosmos-db/table-introduction.md).
