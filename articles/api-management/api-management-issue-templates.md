---
title: Szablony problemów na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak dostosować zawartość stron problemu w portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85846848"
---
# <a name="issue-templates-in-azure-api-management"></a>Szablony problemów na platformie Azure API Management
Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu dla deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Korzystając z składni [DotLiquid](http://dotliquidmarkup.org/) i wybranego edytora, takiego jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i dostępnego zestawu zlokalizowanych [zasobów ciągów](api-management-template-resources.md#strings), [zasobów glifów](api-management-template-resources.md#glyphs)i [kontrolek stron](api-management-page-controls.md), masz doskonałą elastyczność konfigurowania zawartości stron w miarę, w jakiej są one widoczne przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron problemu w portalu dla deweloperów.  
  
-   [Lista problemów](#IssueList)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są zawarte w poniższej dokumentacji, ale mogą ulec zmianie ze względu na ciągłe ulepszenia. Możesz wyświetlić szablony domyślne na żywo w portalu dla deweloperów, przechodząc do żądanych poszczególnych szablonów. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a> Lista problemów  
 Szablon **Lista problemów** umożliwia dostosowanie treści strony Lista problemów w portalu dla deweloperów.  
  
 ![Portal dla deweloperów listy problemów](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portal dla deweloperów listy problemów APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Formanty  
 `Issue list`Szablon może korzystać z następujących [kontrolek strony](api-management-page-controls.md).  
  
-   [Kontrola stronicowania](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Issues`|Kolekcja jednostek [problemu](api-management-template-data-model-reference.md#Issue) .|Problemy widoczne dla bieżącego użytkownika.|  
|`Paging`|Jednostka [stronicowania](api-management-template-data-model-reference.md#Paging) .|Informacje o stronicowaniu dla kolekcji aplikacji.|  
|`IsAuthenticated`|boolean|Czy bieżący użytkownik jest zalogowany do portalu dla deweloperów.|  
|`CanReportIssues`|boolean|Czy bieżący użytkownik ma uprawnienia do pliku problemu.|  
|`Search`|ciąg|Ta właściwość jest przestarzała i nie powinna być używana.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
