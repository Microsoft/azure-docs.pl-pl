---
title: Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji
description: Przewodnik dla architektów i administratorów IT przy użyciu serwera proxy aplikacji w celu odpornego dostępu do aplikacji lokalnych
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724664"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji

Serwer proxy aplikacji to funkcja usługi Azure AD, która umożliwia użytkownikom dostęp do lokalnych aplikacji sieci Web z klienta zdalnego. Serwer proxy aplikacji obejmuje zarówno usługę serwera proxy aplikacji w chmurze, jak i łączniki serwera proxy aplikacji, które są uruchamiane na serwerze lokalnym. 

Użytkownicy uzyskują dostęp do zasobów lokalnych za pośrednictwem adresu URL opublikowanego za pośrednictwem serwera proxy aplikacji. Nastąpi przekierowanie do strony logowania do usługi Azure AD. Usługa serwera proxy aplikacji w usłudze Azure AD wysyła token do łącznika serwera proxy aplikacji w sieci firmowej, który przekazuje token do lokalnego Active Directory uwierzytelniony użytkownik może uzyskać dostęp do zasobu lokalnego. Na poniższym diagramie [Łączniki](../manage-apps/application-proxy-connectors.md) są wyświetlane w [grupie łączników](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Podczas publikowania aplikacji za pośrednictwem serwera proxy aplikacji należy zaimplementować [Planowanie pojemności i odpowiednią nadmiarowość dla łączników serwera proxy aplikacji](../manage-apps/application-proxy-connectors.md#capacity-planning).

![Diagram architektury aplikacji y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Jak mogę zaimplementować serwer proxy aplikacji?

Aby zaimplementować dostęp zdalny za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD, zobacz następujące zasoby.

* [Planowanie wdrożenia serwera proxy aplikacji](../manage-apps/application-proxy-deployment-plan.md)

* [Najlepsze rozwiązania dotyczące wysokiej dostępności i równoważenia obciążenia](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Konfigurowanie serwerów proxy](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Projektowanie odpornej strategii kontroli dostępu](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)