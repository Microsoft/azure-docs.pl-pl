---
title: Szablony portalu dla deweloperów platformy Azure API Management | Microsoft Docs
description: Dowiedz się, jak dostosować zawartość stron portalu dla deweloperów przy użyciu zestawu szablonów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9bbb7ef46fbde881c8968128870d24a03b046c23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73176791"
---
# <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów

Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu dla deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Korzystając z składni [DotLiquid](http://dotliquidmarkup.org/) i wybranego edytora, takiego jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i dostępnego zestawu zlokalizowanych [zasobów ciągów](api-management-template-resources.md#strings), [zasobów glifów](api-management-template-resources.md#glyphs)i [kontrolek stron](api-management-page-controls.md), masz doskonałą elastyczność konfigurowania zawartości stron w miarę, w jakiej są one widoczne przy użyciu tych szablonów.  
  
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="developer-portal-templates"></a><a name="DeveloperPortalTemplates"></a> Szablony portalu dla deweloperów  
  
-   [Interfejsy API](api-management-api-templates.md)  
    -   [Lista interfejsów API](api-management-api-templates.md#APIList)  
    -   [Operacja](api-management-api-templates.md#Product)  
    -   [Przykłady kodu](api-management-api-templates.md#CodeSamples)  
        -   [Odsłon](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Cel C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Produkty](api-management-product-templates.md)  
    -   [Lista produktów](api-management-product-templates.md#ProductList)  
    -   [Product](api-management-product-templates.md#Product)  
-   [Aplikacje](api-management-application-templates.md)  
    -   [Lista aplikacji](api-management-application-templates.md#ProductList)  
    -   [Aplikacja](api-management-application-templates.md#Application)  
-   [Problemy](api-management-issue-templates.md)  
    -   [Lista problemów](api-management-issue-templates.md#IssueList)  
-   [Profil użytkownika](api-management-user-profile-templates.md)  
    -   [Profil](api-management-user-profile-templates.md#Profile)  
    -   [Subskrypcje](api-management-user-profile-templates.md#Subscriptions)  
    -   [Aplikacje](api-management-user-profile-templates.md#Applications)  
    -   [Aktualizowanie informacji o koncie](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [Pages](api-management-page-templates.md)  
    -   [Zaloguj się](api-management-page-templates.md#SignIn)  
    -   [Zarejestruj się](api-management-page-templates.md#SignUp)  
    -   [Nie znaleziono strony](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Następne kroki  

+ [Dokumentacja dotycząca szablonów](api-management-developer-portal-templates-reference.md)  
+ [Dokumentacja dotycząca modeli obiektów](api-management-template-data-model-reference.md)  
+ [Kontrolki strony](api-management-page-controls.md)  
+ [Zasoby szablonu](api-management-template-resources.md)