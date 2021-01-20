---
title: Obsługiwane topologie i scenariusze Azure AD Connect synchronizacji z chmurą
description: Dowiedz się więcej na temat różnych topologii lokalnych i Azure Active Directory (Azure AD), które używają Azure AD Connect synchronizacji z chmurą.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613545"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Obsługiwane topologie i scenariusze Azure AD Connect synchronizacji z chmurą
W tym artykule opisano różne topologie lokalne i Azure Active Directory (Azure AD) używające Azure AD Connect synchronizacji w chmurze. Ten artykuł zawiera tylko obsługiwane konfiguracje i scenariusze.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania Azure AD Connect synchronizacji w chmurze poza konfiguracją lub akcjami, które są formalnie udokumentowane. Każda z tych konfiguracji lub akcji może spowodować niespójny lub nieobsługiwany stan Azure AD Connect synchronizacji z chmurą. W związku z tym firma Microsoft nie może zapewnić pomocy technicznej dotyczącej takich wdrożeń.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Zapamiętaj o wszystkich scenariuszach i topologii
Poniżej znajduje się lista informacji, które należy wziąć pod uwagę podczas wybierania rozwiązania.

- Użytkownicy i grupy muszą być jednoznacznie identyfikowane we wszystkich lasach
- Dopasowanie między lasami nie odbywa się z synchronizacją w chmurze
- Użytkownik lub grupa musi być reprezentowana tylko raz we wszystkich lasach
- Zakotwiczenie źródła dla obiektów jest wybierane automatycznie.  Używa on usługi MS-DS-ConsistencyGuid, jeśli jest obecny, w przeciwnym razie jest używany element ObjectGUID.
- Nie można zmienić atrybutu, który jest używany dla kotwicy źródłowej.

## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedyncza dzierżawa usługi Azure AD
![Diagram przedstawiający topologię pojedynczego lasu i pojedynczej dzierżawy.](media/tutorial-single-forest/diagram-2.png)

Najprostsza topologia to pojedynczy las lokalny, z co najmniej jedną domeną i jedną dzierżawą usługi Azure AD.  Przykład tego scenariusza można znaleźć w [samouczku: pojedynczy las z jedną dzierżawą usługi Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Wiele lasów, pojedyncza dzierżawa usługi Azure AD
![Topologia dla wielu lasów i pojedynczej dzierżawy](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Wspólna topologia to wiele lasów usługi AD, z co najmniej jedną domeną i jedną dzierżawą usługi Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Istniejący Las z Azure AD Connectm, nowym lasem z obsługą chmury
![Diagram przedstawiający topologię istniejącego lasu i nowy las.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Ten scenariusz jest podobny do scenariusza obejmującego wiele lasów, jednak ten element obejmuje istniejące środowisko Azure AD Connect, a następnie umieszcza je w nowym lesie przy użyciu Azure AD Connect synchronizacji w chmurze.  Aby zapoznać się z przykładem tego scenariusza, zobacz [Samouczek: istniejący Las z pojedynczą dzierżawą usługi Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilotażowe Azure AD Connect synchronizacji w chmurze w istniejącym hybrydowym lesie usługi AD
![Topologia jednego lasu i pojedynczej dzierżawy ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) scenariusz pilotażowy obejmuje istnienie zarówno Azure AD Connect, jak i Azure AD Connect synchronizacji w chmurze w tym samym lesie i odpowiednie określanie zakresu dla użytkowników i grup. Uwaga: obiekt powinien znajdować się w zakresie tylko w jednym z narzędzi. 

Aby zapoznać się z przykładem tego scenariusza, zobacz [Samouczek: pilotaż Azure AD Connect Cloud Sync w istniejącym synchronizowanym lesie usługi AD](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)

