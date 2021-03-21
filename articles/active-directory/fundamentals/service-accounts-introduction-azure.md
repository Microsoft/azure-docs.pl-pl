---
title: Wprowadzenie do zabezpieczania Azure Active Directory kont usług
description: Wyjaśnienie typów kont usług dostępnych w Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27353e6086085aadae622e007a38f97e15cbcb7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587869"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Wprowadzenie do zabezpieczania kont usług platformy Azure

Istnieją trzy typy kont usługi natywnych do Azure Active Directory: tożsamości zarządzane, nazwy główne usług i konta usług oparte na użytkownikach. Konta usług są specjalnym typem konta, które jest przeznaczone do reprezentowania nieczłowieka, takiego jak aplikacja, interfejs API lub inna usługa. Te jednostki działają w kontekście zabezpieczeń dostarczonym przez konto usługi. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Typy kont usługi Azure Active Directory

W przypadku usług hostowanych na platformie Azure zalecamy użycie tożsamości zarządzanej, o ile jest to możliwe, oraz nazwy głównej usługi, jeśli nie. Tożsamości zarządzane nie mogą być używane dla usług hostowanych poza platformą Azure. W takim przypadku zalecamy użycie nazwy głównej usługi. Jeśli możesz użyć tożsamości zarządzanej lub nazwy głównej usługi, zrób to. Zalecamy, aby nie używać konta użytkownika Azure Active Directory jako nazwy głównej usługi. Zapoznaj się z poniższą tabelą.
 

| Hosting usług| Tożsamość zarządzana| Jednostka usługi| Konto użytkownika platformy Azure |
| - | - | - | - |
|Usługa jest hostowana na platformie Azure.| Tak. <br>Zalecane, jeśli usługa <br>obsługuje tożsamość zarządzaną.| Tak.| Niezalecane. |
| Usługa nie jest hostowana na platformie Azure.| Nie| Tak. Rekomendowane.| Niezalecane. |
| Usługa jest wielodostępna| Nie| Tak. Rekomendowane.| Nie. |


## <a name="managed-identities"></a>Tożsamości zarządzane

Tożsamości zarządzane są bezpiecznymi tożsamościami Azure Active Directory (Azure AD) utworzonymi w celu dostarczania tożsamości dla zasobów platformy Azure. Istnieją [dwa typy zarządzanych tożsamości](../managed-identities-azure-resources/overview.md#managed-identity-types): 
 
* Zarządzane tożsamości przypisane do systemu można przypisywać bezpośrednio do wystąpienia usługi. 

* Tożsamości zarządzane przypisane przez użytkownika można utworzyć jako zasób autonomiczny. 

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie tożsamości zarządzanych](service-accounts-managed-identities.md). Aby uzyskać ogólne informacje o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md)

## <a name="service-principals"></a>Jednostki usługi

Jeśli nie możesz użyć tożsamości zarządzanej do reprezentowania aplikacji, użyj nazwy głównej usługi. Jednostki usługi mogą być używane zarówno w przypadku aplikacji z jedną dzierżawą, jak i z wieloma dzierżawcami. 

Nazwa główna usługi to lokalna reprezentacja obiektu aplikacji w jednej dzierżawie usługi Azure AD. Działa jako tożsamość wystąpienia aplikacji, definiuje, kto może uzyskiwać dostęp do aplikacji i jakie zasoby mogą uzyskać dostęp do aplikacji. Jednostka usługi jest tworzona w programie (lokalna dla) każdej dzierżawy, w której aplikacja jest używana, i odwołuje się do globalnie unikatowego obiektu aplikacji. Dzierżawca zabezpiecza logowanie jednostki usługi i dostęp do zasobów.

Istnieją dwa mechanizmy uwierzytelniania przy użyciu jednostek usługi — certyfikaty klienta i wpisy tajne klienta. Certyfikaty są bezpieczniejsze: Użyj certyfikatów klienta, jeśli jest to możliwe. W przeciwieństwie do wpisów tajnych klienta certyfikaty klienta nie mogą być przypadkowo osadzone w kodzie.

Aby uzyskać informacje na temat zabezpieczania jednostek usługi, zobacz Zabezpieczanie podmiotów usługi.

 
## <a name="next-steps"></a>Następne kroki


Aby uzyskać więcej informacji na temat zabezpieczania kont usług platformy Azure, zobacz:

[Zabezpieczanie tożsamości zarządzanych](service-accounts-managed-identities.md)

[Zabezpieczanie podmiotów usługi](service-accounts-principal.md)

[Zarządzanie kontami usług platformy Azure](service-accounts-governing-azure.md)