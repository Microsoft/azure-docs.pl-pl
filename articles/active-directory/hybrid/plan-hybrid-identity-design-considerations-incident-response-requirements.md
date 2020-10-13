---
title: Projektowanie tożsamości hybrydowej — wymagania dotyczące odpowiedzi na zdarzenia Azure | Microsoft Docs
description: Określanie możliwości monitorowania i raportowania dla hybrydowego rozwiązania do obsługi tożsamości, które może zostać użyte przez niego do podjęcia działań w celu zidentyfikowania potencjalnych zagrożeń i ich ograniczenia
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
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
ms.openlocfilehash: 8c68be4e46693621f77cb25dd8f34f8e0b7d75dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278415"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących odpowiedzi na zdarzenia dla rozwiązania do tworzenia tożsamości hybrydowej
Duże lub średnie organizacje najprawdopodobniej będą miały [odpowiedź na zdarzenia związane z bezpieczeństwem](/previous-versions/tn-archive/cc700825(v=technet.10)) w celu ułatwienia działania odpowiednio do poziomu incydentu. System zarządzania tożsamościami jest ważnym składnikiem procesu reagowania na zdarzenia, ponieważ może służyć do identyfikowania, kto wykonał konkretną akcję względem celu. Rozwiązanie do obsługi tożsamości hybrydowej musi być w stanie zapewnić możliwości monitorowania i raportowania, które mogą być używane przez niego do działania w celu zidentyfikowania potencjalnego zagrożenia i rozwiązania tego problemu. W przypadku typowego planu reagowania na zdarzenia w ramach planu będą dostępne następujące fazy:

1. Wstępna ocena.
2. Komunikacja przy zdarzeniu.
3. Kontrola szkód i zmniejszenie ryzyka.
4. Identyfikacja tego, co zostało naruszone i ważności.
5. Zachowywanie zeznań.
6. Powiadomienie do odpowiednich stron.
7. Odzyskiwanie systemu.
8. Łączoną.
9. Uszkodzenie i ocena kosztów.
10. Przetwarzanie i planowanie korekty.

Podczas identyfikacji tego, co zostało naruszone, i fazy ważności, konieczne będzie zidentyfikowanie systemów, których zabezpieczenia zostały naruszone, plików, do których uzyskano dostęp, i ustalenie czułości tych plików. System tożsamości hybrydowej powinien być w stanie spełnić te wymagania, aby ułatwić identyfikację użytkownika, który wprowadził te zmiany. 

## <a name="monitoring-and-reporting"></a>Monitorowanie i raportowanie
Wiele razy system tożsamości może pomóc w fazie oceny początkowej głównie w przypadku, gdy system ma wbudowane funkcje inspekcji i raportowania. Podczas wstępnej oceny administrator IT musi mieć możliwość zidentyfikowania podejrzanych działań lub system powinien być w stanie wyzwolić go automatycznie na podstawie wstępnie skonfigurowanego zadania. Wiele działań może wskazywać na możliwy atak, jednak w innych przypadkach nieprawidłowo skonfigurowany system może prowadzić do liczby fałszywych dodatnich w systemie wykrywania wtargnięcia. 

System zarządzania tożsamościami powinien pomóc administratorom IT w identyfikowaniu i raportowaniu tych podejrzanych działań. Zwykle te wymagania techniczne mogą być spełnione przez monitorowanie wszystkich systemów i posiadanie możliwości raportowania, które mogą wyróżnić potencjalne zagrożenia. Skorzystaj z poniższych pytań, aby zaprojektować rozwiązanie do tworzenia tożsamości hybrydowej, biorąc pod uwagę wymagania dotyczące odpowiedzi na zdarzenia:

* Czy firma ma odpowiedź na zdarzenia związane z bezpieczeństwem?
  * Jeśli tak, czy obecny system zarządzania tożsamościami jest używany w ramach procesu?
* Czy firma musi identyfikować podejrzane próby logowania od użytkowników na różnych urządzeniach?
* Czy firma musi wykryć potencjalne naruszenia poświadczeń użytkownika?
* Czy firma musi prowadzić inspekcję dostępu użytkownika i akcji?
* Czy firma musi wiedzieć, kiedy użytkownik resetuje swoje hasło?

## <a name="policy-enforcement"></a>Wymuszanie zasad
W przypadku nieuszkodzenia kontroli i zmniejszania ryzyka ważne jest, aby szybko ograniczyć rzeczywiste i potencjalne skutki ataku. Ta akcja, która zostanie podjęta w tym momencie, będzie mogła wprowadzić różnicę między małoletnim a głównym. Dokładna odpowiedź będzie zależeć od organizacji i rodzaju ataku. Jeśli wstępna ocena stwierdziła, że konto zostało naruszone, należy wymusić zasady, aby zablokować to konto. Jest to tylko jeden przykład, w którym zostanie wykorzystany system zarządzania tożsamościami. Skorzystaj z poniższych pytań, aby zaprojektować rozwiązanie do tworzenia tożsamości hybrydowych, biorąc pod uwagę, w jaki sposób zasady zostaną wymuszone w celu reagowania na bieżące zdarzenie:

* Czy w firmie istnieją zasady umożliwiające zablokowanie użytkownikom dostępu do sieci w razie potrzeby?
  * Jeśli tak, czy bieżące rozwiązanie jest integrowane z systemem hybrydowego zarządzania tożsamościami, które ma zostać przyjęte?
* Czy firma musi wymusić dostęp warunkowy dla użytkowników znajdujących się w kwarantannie? 

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) spowoduje przejście do opcji dostępne i zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania należy wybrać opcję, która najlepiej odpowiada potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)