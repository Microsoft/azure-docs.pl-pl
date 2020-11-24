---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fcfe05db6a9be1049ca5da06985f31135ac79f3b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555239"
---
Opłaty za usługę Azure Storage są naliczane w oparciu o użycie konta magazynu. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. 

Koszty magazynu są obliczane na podstawie następujących czynników: 

* **Region** odnosi się do regionu geograficznego, w którym bazuje Twoje konto.
* **Typ konta** odnosi się do typu konta magazynu, którego używasz. 
* **Warstwa dostępu** odnosi się do wzorca użycia danych określonego dla konta ogólnego przeznaczenia w wersji 2 lub BLOB Storage.
* **Pojemność** magazynu to część przydziału konta magazynu używanego do przechowywania danych.
* **Replikacja** określa liczbę kopii danych, które są przechowywane jednocześnie, oraz lokalizacje.
* **Transakcje** odnoszą się do wszystkich operacji odczytu i zapisu w usłudze Azure Storage.
* **Dane** wychodzące odnoszą się do wszystkich danych transferowanych z regionu platformy Azure. Po uzyskaniu dostępu do danych na koncie magazynu przez aplikację, która nie działa w tym samym regionie, opłaty są naliczone za wychodzące dane. Aby uzyskać informacje o korzystaniu z grup zasobów w celu grupowania danych i usług w tym samym regionie w celu ograniczenia opłat za ruch wychodzący, zobacz [co to jest Grupa zasobów platformy Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Strona [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) zawiera szczegółowe informacje o cenach w zależności od typu konta, pojemności magazynu, replikacji i transakcji. Artykuł [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/) zawiera szczegółowe informacje o cenach za wyjście danych. Artykuł [Kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) zawiera informacje, które ułatwią szacowanie kosztów.