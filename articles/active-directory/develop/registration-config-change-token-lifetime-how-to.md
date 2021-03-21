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
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582945"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić wartości domyślne okresu istnienia tokenu dla aplikacji opracowanej niestandardowo

W tym artykule pokazano, jak ustawić zasady czasu istnienia tokenu dostępu przy użyciu programu Azure AD PowerShell. Azure AD — wersja Premium umożliwia deweloperom aplikacji i administratorom dzierżawy skonfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane dla całej dzierżawy lub do zasobów, do których uzyskuje się dostęp.

> [!IMPORTANT]
> Po 2020 maja dzierżawcy nie będą już mogły konfigurować okresów istnienia tokenów odświeżania i tokenu sesji.  Azure Active Directory przestanie przestrzegać istniejących konfiguracji odświeżania i tokenu sesji w zasadach po 30 stycznia 2021. Nadal można skonfigurować okresy istnienia tokenu dostępu po zakończeniu działania. Aby uzyskać więcej informacji, Przeczytaj [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](./active-directory-configurable-token-lifetimes.md).
> Zaimplementowano [funkcje zarządzania sesjami uwierzytelniania](../conditional-access/howto-conditional-access-session-lifetime.md)   w dostępie warunkowym usługi Azure AD. Ta nowa funkcja służy do konfigurowania okresów istnienia tokenu odświeżania przez ustawienie częstotliwości logowania.  

Aby ustawić zasady okresu istnienia tokenu dostępu, Pobierz [moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Uruchom polecenie **Connect-AzureAD-Confirm** .

Poniżej przedstawiono przykładowe zasady, które wymagają, aby użytkownicy uwierzytelniali się częściej w aplikacji sieci Web. Te zasady określają okres istnienia dostępu do nazwy głównej usługi aplikacji sieci Web. Utwórz zasady i przypisz je do nazwy głównej usługi. Należy również uzyskać identyfikator ObjectId nazwy głównej usługi.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Następne kroki

* Zobacz [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](./active-directory-configurable-token-lifetimes.md) , aby dowiedzieć się, jak skonfigurować okresy istnienia tokenów wystawionych przez usługę Azure AD, w tym informacje o sposobie ustawiania okresów istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawami lub dla określonej jednostki usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](./id-tokens.md)
