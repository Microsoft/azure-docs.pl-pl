---
title: Co się stało z moim projektem ASP.NET 5 (usługi połączone programu Visual Studio)
description: Opisuje, co się stanie po nawiązaniu połączenia z kontem usługi Azure Storage w projekcie programu Visual Studio ASP.NET 5 przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72300004"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co się stało z moim projektem ASP.NET 5 (usługa połączone usługi Visual Studio Azure Storage)?
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

Ponadto został dodany pakiet NuGet **Microsoft. Framework. Configuration. JSON** .

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku config. JSON projektu, element został utworzony przy użyciu wybranych parametrów i kluczy połączenia dla konta magazynu.

Aby uzyskać więcej informacji, zobacz [ASP.NET 5](https://www.asp.net/vnext).

