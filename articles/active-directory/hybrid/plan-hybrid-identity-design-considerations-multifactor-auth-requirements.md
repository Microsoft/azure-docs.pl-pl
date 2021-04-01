---
title: Projekt tożsamości hybrydowej — wymagania dotyczące uwierzytelniania wieloskładnikowego Azure | Microsoft Docs
description: Przy użyciu kontroli dostępu warunkowego usługa Azure AD weryfikuje określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
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
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976046"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego dla rozwiązania do obsługi tożsamości hybrydowej
W tym świecie mobilności użytkownicy uzyskujący dostęp do danych i aplikacji w chmurze oraz z dowolnego urządzenia mogą zabezpieczyć te informacje.  Codziennie istnieje nowy nagłówek o naruszeniu zabezpieczeń.  Chociaż nie ma gwarancji na takie naruszenia, usługa uwierzytelniania wieloskładnikowego zapewnia dodatkową warstwę zabezpieczeń, która pomaga zapobiegać naruszeniu tych naruszeń.
Zacznij od oceny wymagań organizacji dotyczących uwierzytelniania wieloskładnikowego. Oznacza to, co to jest organizacja, która podjęła próbę zabezpieczenia.  Ta ocena jest ważna, aby zdefiniować wymagania techniczne dotyczące konfigurowania i włączania uwierzytelniania wieloskładnikowego dla organizacji.

Zwróć uwagę na następujące pytania:

* Czy Twoja firma próbuje zabezpieczyć aplikacje firmy Microsoft? 
* Jak są publikowane te aplikacje?
* Czy firma zapewnia dostęp zdalny, aby umożliwić pracownikom dostęp do aplikacji lokalnych?

Jeśli tak, jakiego typu dostępu zdalnego? Należy również sprawdzić, gdzie znajdują się użytkownicy, którzy uzyskują dostęp do tych aplikacji. Ta ocena jest kolejnym ważnym krokiem w celu zdefiniowania odpowiedniej strategii uwierzytelniania wieloskładnikowego. Upewnij się, że odpowiadają na następujące pytania:

* Gdzie znajdują się użytkownicy, którzy mają się znajdować?
* Czy mogą znajdować się w dowolnym miejscu?
* Czy firma chce ustalić ograniczenia zgodnie z lokalizacją użytkownika?

Po zrozumieniu tych wymagań należy również oszacować wymagania użytkownika dotyczące uwierzytelniania wieloskładnikowego. Ta ocena jest ważna, ponieważ określi wymagania dotyczące wdrażania uwierzytelniania wieloskładnikowego. Upewnij się, że odpowiadają na następujące pytania:

* Czy użytkownicy znają uwierzytelnianie wieloskładnikowe?
* Czy niektóre zastosowania będą wymagane w celu zapewnienia dodatkowego uwierzytelniania?  
  * Jeśli tak, cały czas, gdy pochodzą z sieci zewnętrznych lub uzyskują dostęp do określonych aplikacji lub w innych warunkach?
* Czy użytkownicy będą musieli przeprowadzić szkolenia w zakresie instalacji i implementacji usługi uwierzytelniania wieloskładnikowego?
* Jakie są kluczowe scenariusze, w których firma chce włączyć uwierzytelnianie wieloskładnikowe dla swoich użytkowników?

Po udzieleniu odpowiedzi na poprzednie pytania można zrozumieć, czy uwierzytelnianie wieloskładnikowe jest już zaimplementowane w środowisku lokalnym. Ta ocena jest ważna, aby zdefiniować wymagania techniczne dotyczące konfigurowania i włączania uwierzytelniania wieloskładnikowego dla organizacji. Upewnij się, że odpowiadają na następujące pytania:

* Czy firma musi chronić konta uprzywilejowane za pomocą usługi MFA?
* Czy firma musi włączyć uwierzytelnianie wieloskładnikowe dla pewnych aplikacji ze względu na zgodność?
* Czy firma musi włączyć usługę MFA dla wszystkich uprawnionych użytkowników tej aplikacji lub tylko administratorów?
* Czy usługi MFA muszą być zawsze włączone lub tylko wtedy, gdy użytkownicy są zalogowani poza siecią firmową?

## <a name="next-steps"></a>Następne kroki
[Definiowanie hybrydowej strategii wdrażania tożsamości](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

