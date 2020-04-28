---
title: 'Azure AD Connect: msExchUserHoldPolicies i cloudMsExchUserHoldPolicies | Microsoft Docs'
description: W tym temacie opisano zachowanie atrybutów msExchUserHoldPolicies i cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74213074"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies i cloudMsExchUserHoldPolicies
W poniższym dokumencie referencyjnym opisano te atrybuty, które są używane przez program Exchange i właściwy sposób edytowania domyślnych reguł synchronizacji.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Co to są msExchUserHoldPolicies i cloudMsExchUserHoldPolicies?
Istnieją dwa typy [blokad](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) dostępnych dla serwera programu Exchange: wstrzymanie i przechowywanie w miejscu. Gdy wstrzymanie jest włączone, wszystkie elementy skrzynki pocztowej są umieszczane w blokadzie.  Blokada miejscowa służy do zachowywania tylko tych elementów, które spełniają kryteria zapytania wyszukiwania zdefiniowanego za pomocą narzędzia do zbierania elektronicznych materiałów dowodowych.

Atrybuty MsExchUserHoldPolcies i cloudMsExchUserHoldPolicies umożliwiają dostęp do lokalnych usług AD i Azure AD w celu ustalenia, którzy użytkownicy znajdują się w ramach wstrzymania w zależności od tego, czy korzystają z lokalnego programu Exchange, czy programu Exchange.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>przepływ synchronizacji msExchUserHoldPolicies
Domyślnie MsExchUserHoldPolcies jest synchronizowany przez Azure AD Connect bezpośrednio do atrybutu msExchUserHoldPolicies w obiekcie Metaverse, a następnie do atrybutu msExchUserHoldPolices w usłudze Azure AD

W poniższych tabelach opisano przepływ:

Przychodzące z Active Directory lokalnego:

|Atrybut Active Directory|Nazwa atrybutu|Typ przepływu|Atrybut Metaverse|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Lokalna usługa Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|W programie z usługi AD-User Exchange|

Wychodzące do usługi Azure AD:

|Atrybut Metaverse|Nazwa atrybutu|Typ przepływu|Atrybut usługi Azure AD|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Do usługi AAD — UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>przepływ synchronizacji cloudMsExchUserHoldPolicies
Domyślnie cloudMsExchUserHoldPolicies jest synchronizowany przez Azure AD Connect bezpośrednio do atrybutu cloudMsExchUserHoldPolicies w obiekcie Metaverse. Następnie, jeśli msExchUserHoldPolices nie ma wartości null w obiekcie Metaverse, atrybut w przepływa do Active Directory.

W poniższych tabelach opisano przepływ:

Przychodzące z usługi Azure AD:

|Atrybut Active Directory|Nazwa atrybutu|Typ przepływu|Atrybut Metaverse|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Lokalna usługa Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|W programie z usługi AAD — użytkownik Exchange|

Wychodzące do Active Directory lokalnych:

|Atrybut Metaverse|Nazwa atrybutu|Typ przepływu|Atrybut usługi Azure AD|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Do usługi AD — UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informacje o zachowaniu atrybutu
MsExchangeUserHoldPolicies jest atrybutem pojedynczego urzędu.  Atrybut pojedynczego urzędu można ustawić dla obiektu (w tym przypadku obiektu użytkownika) w katalogu lokalnym lub w katalogu w chmurze.  Reguły uruchamiania określają, że jeśli atrybut jest synchronizowany z lokalnego, usługa Azure AD nie będzie mogła aktualizować tego atrybutu.

Aby umożliwić użytkownikom ustawianie zasad wstrzymania w obiekcie użytkownika w chmurze, używany jest atrybut cloudMSExchangeUserHoldPolicies. Ten atrybut jest używany, ponieważ usługa Azure AD nie może ustawić msExchangeUserHoldPolicies bezpośrednio na podstawie opisanych powyżej reguł.  Ten atrybut zostanie następnie zsynchronizowany z powrotem do katalogu lokalnego, jeśli msExchangeUserHoldPolicies nie ma wartości null i zastąpi bieżącą wartość msExchangeUserHoldPolicies.

W pewnych okolicznościach, na przykład w przypadku zmiany zarówno lokalnie, jak i na platformie Azure, może to spowodować pewne problemy.  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
