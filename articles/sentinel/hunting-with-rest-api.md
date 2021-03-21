---
title: Zarządzanie wytransmisję strumieniowąami i zapytaniami w usłudze Azure wskaźnikiem przy użyciu interfejsu API REST | Microsoft Docs
description: W tym artykule opisano sposób, w jaki funkcje łowiectwa wskaźnikowego platformy Azure umożliwiają skorzystanie z Log Analytics interfejsu API REST w celu zarządzania wydziałami i transmisję strumieniową zapytań.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795698"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Zarządzanie zapytaniami dotyczącymi wyszukiwania zagrożeń i transmisji strumieniowej na żywo w usłudze Azure Sentinel przy użyciu interfejsu API REST

Punkty kontrolne platformy Azure, które są wbudowane w składniku Azure Monitor Log Analytics, umożliwiają korzystanie z interfejsu API REST Log Analytics w celu zarządzania wytransmisję strumieniowąmi i zapytaniami. W tym dokumencie przedstawiono sposób tworzenia zapytań polowania i zarządzania nimi za pomocą interfejsu API REST.  Zapytania utworzone w ten sposób będą wyświetlane w interfejsie użytkownika usługi Azure wskaźnikowej.

Aby uzyskać więcej informacji na temat [interfejsu API zapisane wyszukiwania](/rest/api/loganalytics/savedsearches), zobacz ostateczne informacje o interfejsie API REST.

## <a name="api-examples"></a>Przykłady dotyczące interfejsu API

W poniższych przykładach Zamień te symbole zastępcze na zamiennik określony w poniższej tabeli:

| Symbol zastępczy | Zamień na |
|-|-|
| **Identyfikator** | Nazwa subskrypcji, do której stosuje się zapytanie polowające lub transmisję strumieniową. |
| **ResourceGroupName** | Nazwa grupy zasobów, do której stosuje się zapytanie polowające lub transmisję strumieniową. |
| **{savedSearchId}** | unikatowy identyfikator (GUID) dla każdego zapytania polowania. |
| **WorkspaceName** | Nazwa obszaru roboczego Log Analytics, który jest elementem docelowym zapytania. |
| **Nazwa** | wyświetlana nazwa zapytania. |
| **Zharmonizowan** | Opis zapytania o łowiectwo lub transmisję strumieniową. |
| **Taktykę** | odpowiednie MITRE ATT&taktykę, które mają zastosowanie do zapytania. |
| **Dotyczących** | wyrażenie zapytania dotyczące zapytania. |
|  

### <a name="example-1"></a>Przykład 1

Ten przykład pokazuje, jak utworzyć lub zaktualizować zapytanie polowania dla danego obszaru roboczego wskaźnikowego platformy Azure.  W przypadku zapytania transmisję strumieniową Zastąp ciąg *"Category": "zapytania łowieckie"* " *Category": "transmisję strumieniową Query"* w **treści żądania**: 

#### <a name="request-header"></a>Nagłówek żądania

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Treść żądania

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Przykład 2

W tym przykładzie pokazano, jak usunąć zapytanie polowające lub transmisję strumieniową dla danego obszaru roboczego wskaźnikowego platformy Azure:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Przykład 3

W tym przykładzie pokazano, jak pobrać zapytanie o łowiectwo lub transmisję strumieniową dla danego obszaru roboczego:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zarządzania polowaniemi i transmisję strumieniową zapytań na platformie Azure — za pomocą interfejsu API Log Analytics. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z notesów do uruchamiania zautomatyzowanych kampanii łowieckich](notebooks.md)