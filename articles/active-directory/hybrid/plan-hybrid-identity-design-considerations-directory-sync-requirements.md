---
title: Projekt tożsamości hybrydowej — wymagania dotyczące synchronizacji katalogów Azure | Microsoft Docs
description: Określ, jakie wymagania są wymagane do synchronizowania wszystkich użytkowników między programem = premises a chmurą dla przedsiębiorstwa.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278398"
---
# <a name="determine-directory-synchronization-requirements"></a>Określanie wymagań dotyczących synchronizacji katalogów
Każda synchronizacja polega na umożliwieniu użytkownikom tożsamości w chmurze w oparciu o tożsamość lokalną. Bez względu na to, czy użytkownicy będą używać konta zsynchronizowanego do uwierzytelniania, czy uwierzytelniania federacyjnego, nadal będą musieli mieć tożsamość w chmurze.  Ta tożsamość będzie musiała być okresowo utrzymywana i aktualizowana.  Aktualizacje mogą mieć wiele form, od zmian tytułu zmiany hasła.  

Zacznij od oceny lokalnego rozwiązania do tworzenia tożsamości i wymagań użytkownika. Ta ocena jest ważna, aby zdefiniować wymagania techniczne dotyczące tworzenia i utrzymywania tożsamości użytkowników w chmurze.  W przypadku większości organizacji Active Directory jest lokalna i będzie katalogiem lokalnym, z którego użytkownicy będą synchronizowani, ale w niektórych przypadkach nie będzie to miało miejsca.  

Upewnij się, że odpowiadają na następujące pytania:

* Czy istnieje jeden las usługi AD, wiele czy nie?
  
  * Ile katalogów usługi Azure AD będzie synchronizowanych?
    
    1. Czy używasz filtrowania?
    2. Czy jest planowane wiele Azure AD Connect serwerów?
* Czy obecnie masz narzędzie do synchronizacji w środowisku lokalnym?
  
  * Jeśli tak, czy użytkownicy będą mieć katalog wirtualny/integrację tożsamości?
* Czy masz inny katalog lokalny, który chcesz synchronizować (np. katalog LDAP, baza danych kadr itp.)?
  * Czy chcesz wykonać jakiekolwiek GALSync?
  * Jaki jest bieżący stan głównych nazw UPN w organizacji? 
  * Czy masz inny katalog, dla którego użytkownicy są uwierzytelniani?
  * Czy firma korzysta z programu Microsoft Exchange?
    * Czy planujesz posiadanie hybrydowego wdrożenia programu Exchange?

Teraz, gdy masz pomysł na wymagania dotyczące synchronizacji, musisz określić, które narzędzie jest poprawnym, aby spełnić te wymagania.  Firma Microsoft udostępnia kilka narzędzi do wykonywania integracji i synchronizacji katalogów.  Aby uzyskać więcej informacji, zobacz [tabelę porównawczą narzędzi integracji katalogu tożsamości hybrydowej](plan-hybrid-identity-design-considerations-tools-comparison.md) . 

Teraz, gdy masz wymagane wymagania dotyczące synchronizacji i narzędzia, które będą wykonywane w firmie, musisz oszacować aplikacje korzystające z tych usług katalogowych. Ta ocena jest ważna, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje z chmurą. Upewnij się, że odpowiadają na następujące pytania:

* Czy te aplikacje będą przenoszone do chmury i korzystać z katalogu?
* Czy istnieją specjalne atrybuty, które muszą zostać zsynchronizowane z chmurą, aby te aplikacje mogły ich używać pomyślnie?
* Czy te aplikacje należy ponownie napisać, aby korzystać z uwierzytelniania w chmurze?
* Czy te aplikacje będą nadal aktywne w środowisku lokalnym, a użytkownicy uzyskują do nich dostęp przy użyciu tożsamości w chmurze?

Należy również określić wymagania dotyczące zabezpieczeń i ograniczenia synchronizacji katalogów. Ta ocena jest ważna, aby uzyskać listę wymagań, które będą potrzebne do tworzenia i obsługi tożsamości użytkowników w chmurze. Upewnij się, że odpowiadają na następujące pytania:

* Gdzie będzie zlokalizowany serwer synchronizacji?
* Czy zostanie przyłączona do domeny?
* Czy serwer będzie znajdował się w sieci z ograniczeniami za zaporą, na przykład strefą DMZ?
  * Czy będzie można otworzyć wymagane porty zapory do obsługi synchronizacji?
* Czy istnieje plan odzyskiwania po awarii dla serwera synchronizacji?
* Czy masz konto z odpowiednimi uprawnieniami dla wszystkich lasów, z którymi chcesz się zsynchronizować?
  * Jeśli firma nie wie odpowiedzi na to pytanie, zapoznaj się z sekcją "uprawnienia do synchronizacji haseł" w artykule [Instalowanie usługi synchronizacji Azure Active Directory](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) i określanie, czy masz już konto z tymi uprawnieniami, czy też musisz je utworzyć.
* W przypadku synchronizacji odpowiednie-Forest serwer synchronizacji może uzyskać dostęp do każdego lasu?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określenie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) spowoduje przejście do opcji dostępnych. Po udzieleniu odpowiedzi na te pytania należy wybrać opcję, która najlepiej odpowiada potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)