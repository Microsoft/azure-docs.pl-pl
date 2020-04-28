---
title: Co się stało z moim projektem usługi w chmurze?
description: Opisuje, co się dzieje w projekcie usług w chmurze po nawiązaniu połączenia z kontem usługi Azure Storage przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298767"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co się stało z projektem usług Cloud Services (usługa połączona z usługą Visual Studio Azure Storage)?
## <a name="references-added"></a>Dodane odwołania
Pakiet NuGet usługi Azure Storage został dodany do projektu programu Visual Studio.  
Ten pakiet dodaje następujące odwołania platformy .NET:

* **Microsoft. Data. EDM**
* **Microsoft. Data. OData**
* **Microsoft. Data. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System. przestrzenny**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
Elementy zostały utworzone przy użyciu parametrów połączenia wybranego konta magazynu i klucza. Wprowadzono modyfikacje następujących plików:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. local. cscfg**

