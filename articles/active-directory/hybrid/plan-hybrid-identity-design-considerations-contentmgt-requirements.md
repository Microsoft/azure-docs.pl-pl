---
title: Projekt tożsamości hybrydowej — wymagania dotyczące zarządzania zawartością Azure | Microsoft Docs
description: Zawiera szczegółowe informacje na temat sposobu określania wymagań związanych z zarządzaniem zawartością Twojej firmy. Zwykle gdy użytkownik ma własne urządzenie, może mieć także wiele poświadczeń, które będą się zmieniać w zależności od używanej aplikacji. Ważne jest, aby odróżnić zawartość, która została utworzona przy użyciu poświadczeń osobistych, a także tych, które zostały utworzone przy użyciu poświadczeń firmowych. Rozwiązanie do obsługi tożsamości powinno być w stanie współdziałać z usługami w chmurze, aby zapewnić użytkownikom końcowym bezproblemowe środowisko i zapewnić ich prywatność oraz zwiększyć ochronę przed wyciekami danych.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408536"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących zarządzania zawartością dla rozwiązania do tworzenia tożsamości hybrydowej
Zrozumienie wymagań związanych z zarządzaniem zawartością dla Twojej firmy może bezpośrednio wpłynąć na decyzję, na której będzie używane rozwiązanie do obsługi tożsamości hybrydowej. W przypadku rozprzestrzeniania się wielu urządzeń i możliwości użytkowników do przynoszenia własnych urządzeń ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)) firma musi chronić własne dane, ale również musi utrzymywać prywatność użytkownika bez zmian. Zwykle gdy użytkownik ma własne urządzenie, może mieć także wiele poświadczeń, które będą się zmieniać w zależności od używanej aplikacji. Ważne jest, aby odróżnić zawartość, która została utworzona przy użyciu poświadczeń osobistych, a także tych, które zostały utworzone przy użyciu poświadczeń firmowych. Rozwiązanie do obsługi tożsamości powinno być w stanie współdziałać z usługami w chmurze, aby zapewnić użytkownikom końcowym bezproblemowe środowisko i zapewnić ich prywatność oraz zwiększyć ochronę przed wyciekami danych. 

Twoje rozwiązanie do obsługi tożsamości będzie używane przez różne kontrole techniczne w celu zapewnienia zarządzania zawartością, jak pokazano na poniższej ilustracji:

![kontrolki zabezpieczeń](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Kontrole zabezpieczeń, które będą korzystać z systemu zarządzania tożsamościami**

Ogólnie rzecz biorąc, wymagania dotyczące zarządzania zawartością będą korzystać z systemu zarządzania tożsamościami w następujących obszarach:

* Prywatność: identyfikowanie użytkownika, który jest właścicielem zasobu i stosowanie odpowiednich kontroli w celu zachowania integralności.
* Klasyfikacja danych: Zidentyfikuj użytkownika lub grupę i poziom dostępu do obiektu zgodnie z jego klasyfikacją. 
* Ochrona przed wyciekami danych: mechanizmy kontroli zabezpieczeń odpowiedzialne za ochronę danych w celu uniknięcia wycieków muszą współdziałać z systemem tożsamości w celu zweryfikowania tożsamości użytkownika. Jest to również ważne dla celów inspekcji.

> [!NOTE]
> Zapoznaj się z [klasyfikacją danych dla gotowości chmury](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) , aby uzyskać więcej informacji o najlepszych rozwiązaniach i wytycznych dotyczących klasyfikacji danych.
> 
> 

Planując rozwiązanie do obsługi tożsamości hybrydowej, upewnij się, że odpowiedzi na następujące pytania są zgodne z wymaganiami organizacji:

* Czy w firmie istnieją mechanizmy kontroli zabezpieczeń w celu wymuszenia poufności danych?
  * Jeśli tak, czy kontrola zabezpieczeń będzie w stanie zintegrować z rozwiązaniem tożsamości hybrydowej, które ma zostać przyjęte?
* Czy Twoja firma używa klasyfikacji danych?
  * Jeśli tak, czy bieżące rozwiązanie umożliwia integrację z rozwiązaniem tożsamości hybrydowej, które ma zostać przyjęte?
* Czy Twoja firma ma obecnie jakieś rozwiązanie do wycieku danych? 
  * Jeśli tak, czy bieżące rozwiązanie umożliwia integrację z rozwiązaniem tożsamości hybrydowej, które ma zostać przyjęte?
* Czy firma musi przeprowadzać inspekcję dostępu do zasobów?
  * Jeśli tak, jakiego typu zasobów?
  * Jeśli tak, jakiego poziomu informacji jest konieczne?
  * Jeśli tak, gdzie musi znajdować się dziennik inspekcji? Lokalna lub w chmurze?
* Czy firma musi zaszyfrować wszystkie wiadomości e-mail zawierające dane poufne (SSNs, numery kart kredytowych itp.)?
* Czy firma musi szyfrować wszystkie dokumenty/zawartość udostępnione zewnętrznym partnerom biznesowym?
* Czy firma musi wymuszać zasady firmowe na niektórych rodzajach wiadomości e-mail (nie należy odpowiedzać wszystkiego, nie przekazuj dalej)?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) spowoduje przejście do opcji dostępne i zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania należy wybrać opcję, która najlepiej odpowiada potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)