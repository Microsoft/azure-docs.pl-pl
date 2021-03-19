---
title: Projekt tożsamości hybrydowej — wymagania dotyczące ochrony danych Azure | Microsoft Docs
description: Planując rozwiązanie do tworzenia tożsamości hybrydowej, zidentyfikuj wymagania dotyczące ochrony danych dla Twojej firmy i jakie opcje są dostępne, aby najlepiej spełnić te wymagania.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918775"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planowanie zwiększenia bezpieczeństwa danych za pomocą rozwiązania silnej tożsamości
Pierwszym krokiem w ochronie danych jest określenie, kto może uzyskać dostęp do tych danych. Ponadto należy mieć rozwiązanie do obsługi tożsamości, które można zintegrować z systemem w celu zapewnienia możliwości uwierzytelniania i autoryzacji. Uwierzytelnianie i autoryzacja są często mylone ze sobą i ich role są niezrozumiałe. W rzeczywistości są inne, jak pokazano na poniższym rysunku:

![cykl życia urządzenia przenośnego](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Etapy cyklu życia zarządzania urządzeniami przenośnymi**

Planując rozwiązanie do tworzenia tożsamości hybrydowej, musisz zrozumieć wymagania dotyczące ochrony danych dla Twojej firmy i jakie opcje są dostępne, aby najlepiej spełnić te wymagania.

> [!NOTE]
> Po zakończeniu planowania zabezpieczeń danych zapoznaj się z tematem [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) , aby upewnić się, że decyzje podjęte w tej sekcji nie wpłynęły na wybór wymagań dotyczących uwierzytelniania wieloskładnikowego.
> 
> 

## <a name="determine-data-protection-requirements"></a>Określanie wymagań dotyczących ochrony danych
W wieku mobilności większość firm ma wspólny cel: Umożliwianie użytkownikom wydajnej pracy na swoich urządzeniach przenośnych, lokalnie lub zdalnie z dowolnego miejsca w celu zwiększenia produktywności. Firmy, które mają takie wymagania, będą również zaangażowane w informacje o liczbie zagrożeń, które muszą zostać skorygowane w celu zapewnienia bezpieczeństwa danych firmy i zachowania prywatności użytkowników. Każda firma może mieć w związku z tym inne wymagania. różne reguły zgodności, które różnią się w zależności od tego, który branża działa firma, będzie prowadzić do różnych decyzji projektowych. 

Istnieją jednak pewne aspekty zabezpieczeń, które powinny być zbadane i sprawdzone niezależnie od branży.

## <a name="data-protection-paths"></a>Ścieżki ochrony danych
![ścieżki ochrony danych](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Ścieżki ochrony danych**

Na powyższym diagramie składnik tożsamości będzie pierwszym z nich do zweryfikowania przed uzyskaniem dostępu do danych. Jednak te dane mogą znajdować się w różnych stanach w czasie, w którym uzyskano dostęp. Każda liczba na tym diagramie reprezentuje ścieżkę, w której dane mogą znajdować się w pewnym momencie. Te liczby są wyjaśnione poniżej:

1. Ochrona danych na poziomie urządzenia.
2. Ochrona danych podczas przesyłania.
3. Ochrona danych przechowywanych lokalnie.
4. Ochrona danych w chmurze.

Jest to konieczne, aby rozwiązanie do obsługi tożsamości hybrydowej było w stanie korzystać z zasobów zarządzania tożsamościami lokalnymi i w chmurze w celu zidentyfikowania użytkownika przed udzieleniem mu dostępu do danych. Planując rozwiązanie do obsługi tożsamości hybrydowej, upewnij się, że odpowiedzi na następujące pytania są zgodne z wymaganiami organizacji:

## <a name="data-protection-at-rest"></a>Ochrona danych w czasie spoczynku
Bez względu na to, gdzie dane są przechowywane (na urządzeniach, w chmurze czy lokalnie), ważne jest przeprowadzenie oceny w celu zrozumienia potrzeb organizacji. W przypadku tego obszaru upewnij się, że są zadawane następujące pytania:

* Czy firma musi chronić dane przechowywane w spoczynku?
  * Jeśli tak, czy rozwiązanie do tworzenia tożsamości hybrydowej umożliwia integrację z bieżącą infrastrukturą lokalną?
  * Jeśli tak, czy rozwiązanie do obsługi tożsamości hybrydowej umożliwia integrację z obciążeniami znajdującymi się w chmurze?
* Czy zarządzanie tożsamościami w chmurze jest w stanie chronić poświadczenia użytkownika i inne dane przechowywane w chmurze?

## <a name="data-protection-in-transit"></a>Ochrona danych podczas przesyłania
Dane przesyłane między urządzeniem a centrum danych lub między urządzeniem a chmurą muszą być chronione. Jednak w trakcie tranzytu nie trzeba oznaczać procesu komunikacji ze składnikiem spoza usługi w chmurze. jest on przenoszony wewnętrznie, również na przykład między dwiema sieciami wirtualnymi. W przypadku tego obszaru upewnij się, że są zadawane następujące pytania:

* Czy firma musi chronić dane podczas przesyłania?
  * Jeśli tak, czy rozwiązanie do obsługi tożsamości hybrydowej umożliwia integrację z bezpiecznymi kontrolami, takimi jak SSL/TLS?
* Czy usługa zarządzania tożsamościami w chmurze utrzymuje ruch do i w obrębie magazynu katalogów (w ramach i między centrami danych) podpisanym?

## <a name="compliance"></a>Zgodność
Przepisy, przepisy i wymagania dotyczące zgodności będą się różnić w zależności od branży firmy. Firmy w branżach o wysokiej regulowanej branży muszą rozwiązywać problemy związane z zarządzaniem tożsamościami dotyczące problemów ze zgodnością. Przepisy takie jak Sarbanes-Oxley (SOX), przenośność i odpowiedzialność z tytułu ubezpieczenia kondycji (HIPAA), Bliley Act (GLBA) i Payment Card Industry Data Security Standard (PCI DSS) są rygorystyczne względem tożsamości i dostępu. Rozwiązanie do obsługi tożsamości hybrydowej, które zostanie przyjęte przez firmę, musi mieć podstawowe możliwości, które spełniają wymagania jednego lub kilku z tych rozporządzeń. W przypadku tego obszaru upewnij się, że są zadawane następujące pytania:

* Czy rozwiązanie tożsamości hybrydowej jest zgodne z wymaganiami prawnymi firmy?
* Czy rozwiązanie do tworzenia tożsamości hybrydowej zostało skompilowane 
* w obszarze możliwości, które pozwolą firmie na spełnienie wymagań prawnych? 

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) spowoduje przejście do opcji dostępne i zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania należy wybrać opcję, która najlepiej odpowiada potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
 [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

