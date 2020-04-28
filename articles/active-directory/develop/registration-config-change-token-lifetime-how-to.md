---
title: Zmień ustawienia domyślne okresu istnienia tokenu dla niestandardowych aplikacji usługi Azure AD
description: Jak zaktualizować zasady okresu istnienia tokenu dla aplikacji opracowywanej w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883357"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić wartości domyślne okresu istnienia tokenu dla aplikacji opracowanej niestandardowo

W tym artykule pokazano, jak ustawić zasady okresu istnienia tokenu przy użyciu programu Azure AD PowerShell. Azure AD — wersja Premium umożliwia deweloperom aplikacji i administratorom dzierżawy skonfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane dla całej dzierżawy lub do zasobów, do których uzyskuje się dostęp.

1. Aby ustawić zasady istnienia tokenu, należy pobrać [moduł programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie **Connect-AzureAD-Confirm** .

    Poniżej przedstawiono przykładowe zasady ustawiające maksymalny wiek tokenu odświeżania jednego czynnika. Utwórz zasady:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Następne kroki

* Zobacz [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) , aby dowiedzieć się, jak skonfigurować okresy istnienia tokenów wystawionych przez usługę Azure AD, w tym informacje o sposobie ustawiania okresów istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawami lub dla określonej jednostki usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
