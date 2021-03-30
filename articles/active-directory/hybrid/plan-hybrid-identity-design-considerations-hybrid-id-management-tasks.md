---
title: Projektowanie tożsamości hybrydowej — zadania zarządzania Azure | Microsoft Docs
description: Usługa Azure AD sprawdza określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji przy użyciu kontroli dostępu warunkowego.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986525"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planowanie cyklu życia tożsamości hybrydowej
Tożsamość stanowi jedną z podstaw strategii dostępu do pakietu Enterprise Mobility i aplikacji. Bez względu na to, czy logujesz się na urządzeniu przenośnym, czy w aplikacji SaaS, Twoja tożsamość jest kluczem do uzyskania dostępu do wszystkiego. Rozwiązanie do zarządzania tożsamościami na najwyższym poziomie obejmuje ujednolicenie i synchronizację między repozytoriami tożsamości, które obejmują automatyzację i scentralizowanie procesu aprowizacji zasobów. Rozwiązanie tożsamości powinno być scentralizowaną tożsamością w środowisku lokalnym i w chmurze, a także używać jakiejś tożsamości Federacji do obsługi scentralizowanego uwierzytelniania i bezpiecznego udostępniania i współpracy z użytkownikami zewnętrznymi i firmami. Zakres zasobów od systemów operacyjnych i aplikacji do osób w organizacji lub stowarzyszonych z nią. Strukturę organizacyjną można zmienić w celu uwzględnienia zasad i procedur aprowizacji.

Ważne jest również, aby mieć rozwiązanie do obsługi tożsamości w celu zapewnienia użytkownikom możliwości samoobsługowego korzystania z nich w celu zapewnienia produktywności. Rozwiązanie tożsamości jest bardziej niezawodne, jeśli umożliwia logowanie jednokrotne dla użytkowników we wszystkich zasobach, do których potrzebują dostępu. Administratorzy na wszystkich poziomach mogą używać standardowych procedur do zarządzania poświadczeniami użytkowników. Niektóre poziomy administracji można zmniejszyć lub wyeliminować, w zależności od zakresu rozwiązania do zarządzania aprowizacji. Ponadto można bezpiecznie rozpowszechniać możliwości administracyjne, ręcznie lub automatycznie, między różnymi organizacjami. Na przykład administrator domeny może obsłużyć tylko osoby i zasoby w tej domenie. Ten użytkownik może wykonywać zadania administracyjne i aprowizacji, ale nie jest autoryzowany do wykonywania zadań konfiguracyjnych, takich jak tworzenie przepływów pracy.

## <a name="determine-hybrid-identity-management-tasks"></a>Określanie zadań hybrydowego zarządzania tożsamościami
Dystrybucja zadań administracyjnych w organizacji zwiększa dokładność i efektywność administrowania oraz zwiększa balans obciążeń w organizacji. Poniżej znajdują się przestawki, które definiują niezawodny system zarządzania tożsamościami.

 ![zagadnienia dotyczące zarządzania tożsamościami](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Aby zdefiniować zadania hybrydowego zarządzania tożsamościami, należy poznać pewne istotne cechy organizacji, które będą przyjmować tożsamość hybrydową. Ważne jest, aby zrozumieć bieżące repozytoria używane dla źródeł tożsamości. Znając te podstawowe elementy, będziesz mieć podstawą podstawy i na podstawie tego, że będzie konieczne zaproszenie bardziej szczegółowych pytań, które pozwolą na lepszą decyzję projektową dotyczącą rozwiązania tożsamości.  

Podczas definiowania tych wymagań upewnij się, że udzielono odpowiedzi na co najmniej następujące pytania

* Opcje aprowizacji: 
  
  * Czy rozwiązanie tożsamości hybrydowej obsługuje niezawodne zarządzanie dostępem do kont i system aprowizacji?
  * Jak użytkownicy, grupy i hasła mają być zarządzane?
  * Czy zarządzanie cyklem życia tożsamości jest odpowiedzią? 
    * Jak długo trwa aktualizacja hasła konta?
* Zarządzanie licencjami: 
  
  * Czy rozwiązanie do obsługi tożsamości hybrydowej obsługuje zarządzanie licencjami?
    * Jeśli tak, jakie funkcje są dostępne?
  * Czy rozwiązanie obsługuje zarządzanie licencjami oparte na grupach? 
  
    * Jeśli tak, czy można przypisać do niej grupę zabezpieczeń? 
    * Jeśli tak, czy katalog w chmurze automatycznie przypisze licencje wszystkim członkom grupy? 
    * Co się stanie, jeśli użytkownik zostanie później dodany do grupy lub usunięty z niej, czy licencja zostanie automatycznie przypisana czy usunięta? 
* Integracja z innymi dostawcami tożsamości innych firm:
  * Czy to rozwiązanie hybrydowe można zintegrować z dostawcami tożsamości innych firm, aby zaimplementować Logowanie jednokrotne?
  * Czy jest możliwe ujednolicenie różnych dostawców tożsamości w spójny system tożsamości?
  * Jeśli tak, jak i jakie są i jakie funkcje są dostępne?

## <a name="synchronization-management"></a>Zarządzanie synchronizacją
Jednym z celów programu Identity Manager jest możliwość przeniesienia wszystkich dostawców tożsamości i ich zsynchronizowania. Synchronizujesz dane na podstawie autorytatywnego dostawcy tożsamości. W scenariuszu tożsamości hybrydowej z zsynchronizowanym modelem zarządzania można zarządzać wszystkimi tożsamościami użytkowników i urządzeń na serwerze lokalnym oraz synchronizować konta i opcjonalnie hasła do chmury. Użytkownik wprowadza to samo hasło lokalnie, jak w chmurze, a przy logowaniu hasło jest weryfikowane przez rozwiązanie tożsamości. Ten model używa narzędzia do synchronizacji katalogów.

![Synchronizacja katalogu ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) z odpowiednim projektem synchronizacja rozwiązania do obsługi tożsamości hybrydowej zapewnia, że odpowiedzi na następujące pytania:
*    Jakie rozwiązania synchronizacji są dostępne dla rozwiązania tożsamości hybrydowej?
*    Jakie są dostępne funkcje logowania jednokrotnego?
*    Jakie są opcje federacji tożsamości między B2B i B2C?

## <a name="next-steps"></a>Następne kroki
[Określ strategię wdrażania hybrydowego zarządzania tożsamościami](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

