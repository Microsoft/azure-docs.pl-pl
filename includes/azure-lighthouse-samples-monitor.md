---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356275"
---
Te przykłady pokazują, jak za pomocą usługi Azure Monitor tworzyć alerty dla subskrypcji, które zostały dołączone na potrzeby zarządzania zasobami delegowanymi na platformie Azure.

| **Szablon** | **Opis** |
|---------|---------|
| [monitorowanie–delegowanie–zmiany](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Wysyła zapytanie o ostatni 1 dzień działania w ramach zarządzania dzierżawą i [zgłasza wszelkie dodane lub usunięte delegowania](../articles/lighthouse/how-to/monitor-delegation-changes.md) (lub nieudane próby).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Ten szablon tworzy alert platformy Azure i nawiązuje połączenie z istniejącą grupą akcji.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Tworzy wiele alertów dziennika opartych na zapytaniach języka Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Wdraża alert w dzierżawie, gdy użytkownik deleguje subskrypcję do dzierżawy zarządzającej.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Wyświetla dzienniki aktywności platformy Azure w ramach subskrypcji z opcją filtrowania ich według nazwy domeny. |
