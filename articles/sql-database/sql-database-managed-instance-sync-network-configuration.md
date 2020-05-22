---
title: Konfiguracja sieci Azure App Service synchronizacji
description: W tym artykule omówiono sposób synchronizowania konfiguracji sieci w celu Azure App Service planu hostingu.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 0065b5c7ddf8e19897f76aeb49300ec7a6809b30
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772373"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizuj konfigurację sieci dla Azure App Service planu hostingu

Może się tak zdarzyć, że mimo że [aplikacja została zintegrowana z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), nie można nawiązać połączenia z wystąpieniem zarządzanym. Jednym z nich może być próba odświeżenia konfiguracji sieci dla planu usługi.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizuj konfigurację sieci dla planu hostingu App Service

W tym celu wykonaj następujące kroki:  

1. Przejdź do planu App Service aplikacji sieci Web.

   ![plan usługi App Service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kliknij pozycję **Sieć** , a następnie kliknij pozycję **kliknij tutaj, aby zarządzać**.

   ![Zarządzaj planem usługi](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Wybierz sieć **wirtualną** , a następnie kliknij pozycję **Sync Network**.

   ![Synchronizuj sieć](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Poczekaj, aż synchronizacja zostanie ukończona.
  
   ![Synchronizacja ukończona](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teraz możesz spróbować ponownie nawiązać połączenie z wystąpieniem zarządzanym.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Architektura sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md) i [sposób konfigurowania istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md).
