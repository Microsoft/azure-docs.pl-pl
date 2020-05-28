---
title: Synchronizuj konfigurację sieci dla Azure App Service
titleSuffix: Azure SQL Managed Instance
description: W tym artykule omówiono sposób synchronizowania konfiguracji sieci w celu Azure App Service planu hostingu z wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 6e4f6789be2faa8d1af0fd75830541f8a9e27a7e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044570"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synchronizuj konfigurację sieci dla Azure App Service planu hostingu przy użyciu wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Może się tak zdarzyć, że mimo że [aplikacja została zintegrowana z usługą Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md), nie można nawiązać połączenia z wystąpieniem zarządzanym SQL. W celu rozwiązania tego problemu należy odświeżyć lub zsynchronizować konfigurację sieci dla planu usługi. 

## <a name="sync-network-configuration"></a>Synchronizacja konfiguracji sieci 

W tym celu wykonaj następujące kroki:  

1. Przejdź do planu App Service aplikacji sieci Web.

   ![plan usługi App Service](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Wybierz pozycję **Sieć** , a następnie wybierz **pozycję kliknij tutaj, aby zarządzać**.

   ![Zarządzaj planem usługi](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Wybierz sieć **wirtualną** , a następnie kliknij pozycję **Sync Network**.

   ![Synchronizuj sieć](./media/azure-app-sync-network-configuration/sync.png)

4. Poczekaj, aż synchronizacja zostanie ukończona.
  
   ![Synchronizacja ukończona](./media/azure-app-sync-network-configuration/sync-done.png)

Teraz możesz spróbować ponownie nawiązać połączenie z wystąpieniem zarządzanym SQL.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego SQL, zobacz [Architektura sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md) i [sposób konfigurowania istniejącej sieci wirtualnej](vnet-existing-add-subnet.md).
