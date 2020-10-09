---
title: Wymagania kontroli dostępu do projektu tożsamości hybrydowej na platformie Azure | Microsoft Docs
description: Obejmuje filary tożsamości i identyfikowanie wymagań dostępu dla zasobów dla użytkowników w środowisku hybrydowym.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "60295147"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Określ wymagania dotyczące kontroli dostępu dla rozwiązania do tworzenia tożsamości hybrydowej
Gdy organizacja opracowuje swoje rozwiązanie do tworzenia tożsamości hybrydowych, może również użyć tej możliwości do sprawdzenia wymagań dostępu dla zasobów, które mają być dostępne dla użytkowników. Dostęp do danych przekracza wszystkie cztery filary tożsamości, które są następujące:

* Administracja
* Authentication
* Autoryzacja
* Inspekcja

Poniższe sekcje dotyczą uwierzytelniania i autoryzacji w celu uzyskania większej ilości szczegółów, administracji i inspekcji są częścią cyklu życia tożsamości hybrydowej. Aby uzyskać więcej informacji o tych funkcjach, przeczytaj artykuł [Określanie hybrydowych zadań związanych z zarządzaniem tożsamościami](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) .

> [!NOTE]
> Zapoznaj [się z czterema filarami zarządzania tożsamościami w wieku hybrydowym](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) , aby uzyskać więcej informacji na temat każdego z tych filarów.
> 
> 

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Istnieją różne scenariusze uwierzytelniania i autoryzacji, te scenariusze będą mieć określone wymagania, które muszą zostać spełnione przez rozwiązanie do tworzenia tożsamości hybrydowej, które firma zamierza zastosować. Scenariusze dotyczące komunikacji między firmami (B2B) mogą dodać dodatkowe wyzwanie dla administratorów IT, ponieważ muszą one zapewnić, że metoda uwierzytelniania i autoryzacji używana przez organizację będzie mogła komunikować się z partnerami biznesowymi. Podczas projektowania procesu wymagania dotyczące uwierzytelniania i autoryzacji upewnij się, że udzielono odpowiedzi na następujące pytania:

* Czy Twoja organizacja będzie uwierzytelniać i autoryzować tylko użytkowników znajdujących się w systemie zarządzania tożsamościami?
  * Czy istnieją plany dotyczące scenariuszy B2B?
  * Jeśli tak, czy już wiesz, które protokoły (SAML, OAuth, Kerberos lub certyfikaty) będą używane do łączenia obu firm?
* Czy rozwiązanie do obsługi tożsamości hybrydowej, które ma zostać przyjęte, obsługuje te protokoły?

Innym ważnym zagadnieniem jest to, że repozytorium uwierzytelniania, które będzie używane przez użytkowników i partnerów, będzie zlokalizowane i używany model administracyjny. Należy wziąć pod uwagę następujące dwie opcje podstawowe:

* Scentralizowane: w tym modelu poświadczenia użytkownika, zasady i administracja mogą być scentralizowane lokalnie lub w chmurze.
* Hybrydowe: w tym modelu poświadczenia użytkownika, zasady i administracja będą scentralizowane lokalnie i zreplikowane w chmurze.

Model, który zostanie przyjęty przez organizację, będzie różny w zależności od wymagań firmy, należy odpowiedzieć na następujące pytania, aby ustalić, gdzie znajduje się system zarządzania tożsamościami i tryb administracyjny do użycia:

* Czy Twoja organizacja ma obecnie lokalne Zarządzanie tożsamościami?
  * Jeśli tak, czy są one planowane, aby je zachować?
  * Czy istnieją jakieś wymagania dotyczące regulacji lub zgodności, które należy wykonać w organizacji, która określa, gdzie powinien znajdować się system zarządzania tożsamościami?
* Czy Twoja organizacja korzysta z logowania jednokrotnego dla aplikacji znajdujących się lokalnie lub w chmurze?
  * Jeśli tak, czy przyjęcie hybrydowego modelu tożsamości ma wpływ na ten proces?

## <a name="access-control"></a>Kontrola dostępu
Chociaż uwierzytelnianie i autoryzacja są elementami podstawowymi, aby umożliwić dostęp do danych firmowych za pomocą weryfikacji użytkownika, ważne jest również, aby kontrolować poziom dostępu, jaki będą mieli użytkownicy, i poziom administratorów dostępu będzie miał za pośrednictwem zarządzanych przez nich zasobów. Twoje rozwiązanie do obsługi tożsamości hybrydowej musi mieć możliwość zapewnienia szczegółowego dostępu do zasobów, delegowania i podstawowej kontroli dostępu. Upewnij się, że odpowiedzi na następujące pytania dotyczą kontroli dostępu:

* Czy firma ma więcej niż jednego użytkownika z podwyższonym poziomem uprawnień do zarządzania systemem tożsamości?
  * Jeśli tak, czy każdy użytkownik musi mieć ten sam poziom dostępu?
* Czy firma musi delegować dostęp do użytkowników w celu zarządzania określonymi zasobami?
  * Jeśli tak, jak często się dzieje?
* Czy firma musi zintegrować możliwości kontroli dostępu między zasobami lokalnymi i w chmurze?
* Czy firma musi ograniczyć dostęp do zasobów zgodnie z pewnymi warunkami?
* Czy firma ma dowolną aplikację, która wymaga dostępu do kontroli niestandardowej do niektórych zasobów?
  * Jeśli tak, gdzie znajdują się te aplikacje (lokalnie lub w chmurze)?
  * Jeśli tak, gdzie znajdują się te zasoby docelowe (lokalnie lub w chmurze)?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) spowoduje przejście do opcji dostępne i zalety/wady każdej opcji.  Odpowiadając na te pytania, wybierz opcję najlepiej pasującą do potrzeb Twojej firmy.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

