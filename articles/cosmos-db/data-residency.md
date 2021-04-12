---
title: Jak spełnić wymagania dotyczące miejsca zamieszkania danych w Azure Cosmos DB
description: Dowiedz się, jak spełnić wymagania dotyczące miejsca zamieszkania danych w Azure Cosmos DB, aby dane i kopie zapasowe pozostawały w jednym regionie.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491869"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Jak spełnić wymagania dotyczące miejsca zamieszkania danych w Azure Cosmos DB

W Azure Cosmos DB można skonfigurować dane i kopie zapasowe tak, aby pozostały w jednym regionie w celu spełnienia[ wymagań dotyczących miejsca zamieszkania.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Wymagania dotyczące danych w miejscu zamieszkania

W Azure Cosmos DB należy jawnie skonfigurować replikację danych między regionami. Dowiedz się, jak skonfigurować replikację geograficzną przy użyciu [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account), [interfejsu wiersza polecenia platformy Azure](scripts/cli/common/regions.md). Aby spełnić wymagania dotyczące miejsca zamieszkania danych, można utworzyć zasady platformy Azure, które umożliwiają niektórym regionom zapobieganie replikacji danych do niechcianych regionów.

## <a name="residency-requirements-for-backups"></a>Wymagania dotyczące kopii zapasowych miejsca zamieszkania

**Kopie zapasowe w trybie ciągłym**: te kopie zapasowe są domyślnie rezydentne, ponieważ są przechowywane w magazynie lokalnie nadmiarowym lub magazynu strefowo nadmiarowym. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [ciągłej kopii zapasowej](continuous-backup-restore-portal.md) .

**Kopie zapasowe w trybie okresowym**: domyślnie kopie zapasowe kont trybu okresowego będą przechowywane w magazynie geograficznie nadmiarowym. W przypadku okresowych trybów tworzenia kopii zapasowej można skonfigurować nadmiarowość danych na poziomie konta. Istnieją trzy opcje nadmiarowości dla magazynu kopii zapasowych. Są one lokalną nadmiarowością, nadmiarowością stref lub nadmiarowością geograficzną. Aby dowiedzieć się więcej, zobacz jak [skonfigurować nadmiarowość kopii zapasowej](configure-periodic-backup-restore.md#configure-backup-interval-retention) za pomocą portalu.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Użyj Azure Policy, aby wymusić wymagania dotyczące miejsca zamieszkania

Jeśli masz wymagania dotyczące danych z miejsca zamieszkania, które wymagają zachowania wszystkich danych w jednym regionie platformy Azure, możesz wymusić nadmiarowe lub lokalnie nadmiarowe kopie zapasowe dla konta za pomocą Azure Policy.  Możesz również wymusić zasady, że konta Azure Cosmos DB nie są replikowane geograficznie do innych regionów.

Azure Policy to usługa, za pomocą której można tworzyć i przypisywać zasady stosujące reguły do zasobów platformy Azure oraz zarządzać nimi. Azure Policy pomaga zachować zgodność tych zasobów ze standardami firmy i umowami dotyczącymi poziomu usług. Aby uzyskać więcej informacji, zobacz jak używać [Azure Policy](policy.md) do implementowania ładu i kontroli dla Azure Cosmos DB zasobów

## <a name="next-steps"></a>Następne kroki

* Konfigurowanie okresowych kopii zapasowych i zarządzanie nimi przy użyciu [Azure Portal](configure-periodic-backup-restore.md)
* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
