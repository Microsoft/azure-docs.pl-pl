---
title: Kompiluj więcej odpornego uwierzytelniania hybrydowego w Azure Active Directory
description: Przewodnik dla architektów i administratorów IT na potrzeby tworzenia odpornej infrastruktury hybrydowej.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724763"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Tworzenie odporności w architekturze hybrydowej

Uwierzytelnianie hybrydowe umożliwia użytkownikom dostęp do zasobów w chmurze przy użyciu ich tożsamości w środowisku lokalnym. Infrastruktura hybrydowa obejmuje zarówno składniki w chmurze, jak i lokalne.

* Składniki w chmurze obejmują usługę Azure AD, zasoby i usługi platformy Azure, aplikacje oparte na chmurze i aplikacje SaaS.

* Lokalne składniki obejmują aplikacje lokalne, zasoby, takie jak bazy danych SQL i dostawcy tożsamości, takie jak Windows Server Active Directory. 

> [!IMPORTANT]
> Zgodnie z planem odporności na infrastrukturę hybrydową, można zminimalizować zależności i pojedyncze punkty awarii. 

Firma Microsoft oferuje trzy mechanizmy uwierzytelniania hybrydowego. Opcje są wymienione w kolejności odporności. Zalecane jest zaimplementowanie synchronizacji skrótów haseł, jeśli jest to możliwe.

* [Synchronizacja skrótów haseł](../hybrid/whatis-phs.md) (PHS) używa Azure AD Connect do synchronizowania tożsamości i skrótu hasła do usługi Azure AD, dzięki czemu użytkownicy mogą logować się do zasobów opartych na chmurze przy użyciu swojego hasła w środowisku lokalnym. PHS ma lokalne zależności tylko do synchronizacji, a nie do uwierzytelniania.

* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md) (PTA) przekierowuje użytkowników do usługi Azure AD w celu zalogowania się. Następnie nazwa użytkownika i hasło są weryfikowane pod kątem Active Directory lokalnych, za pomocą agenta wdrożonego w sieci firmowej. Usługa PTA ma lokalne rozmiary agentów usługi Azure AD PTA, które znajdują się na serwerach lokalnych.

* Klienci [Federacji](../hybrid/whatis-fed.md) wdrażają usługę federacyjną, taką jak AD FS, a następnie usługi Azure AD weryfikują potwierdzenie SAML generowane przez usługę federacyjną. Federacja ma najwyższą zależność od infrastruktury lokalnej i dlatego więcej punktów awarii. 

   
W organizacji mogą być używane co najmniej jedna z tych metod. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla rozwiązania do tworzenia tożsamości hybrydowej usługi Azure AD](../hybrid/choose-ad-authn.md). Ten artykuł zawiera drzewo decyzyjne, które może pomóc w wyborze metody.

## <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł

Najprostsza i najbardziej odporna opcja uwierzytelniania hybrydowego w usłudze Azure AD to [Synchronizacja skrótów haseł](../hybrid/whatis-phs.md) , która nie ma zależności lokalnej infrastruktury tożsamości podczas przetwarzania żądań uwierzytelniania. Po zsynchronizowaniu tożsamości z skrótami haseł do usługi Azure AD użytkownicy mogą uwierzytelniać się w zasobach w chmurze bez żadnej zależności od lokalnych składników tożsamości. 

![Diagram architektury elementu PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

W przypadku wybrania tej opcji uwierzytelniania nie będziesz mieć przerwy w działaniu, gdy lokalne składniki tożsamości staną się niedostępne. Przerwy w działaniu lokalnym mogą wystąpić z wielu powodów, takich jak awaria sprzętu, przerwy w działaniu, klęski żywiołowe i ataki złośliwego oprogramowania. 

### <a name="how-do-i-implement-phs"></a>Jak mogę wdrożyć PHS?

Aby zaimplementować PHS, zobacz następujące zasoby:

* [Implementowanie synchronizacji skrótów haseł przy użyciu Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Włączanie synchronizacji skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)

Jeśli Twoje wymagania są takie same, że nie można używać PHS, Użyj uwierzytelniania przekazywanego.

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

Uwierzytelnianie przekazywane jest zależne od agentów uwierzytelniania znajdujących się lokalnie na serwerach. Istnieje połączenie trwałe lub Usługa Service Bus między usługą Azure AD i lokalnymi agentami PTA. Zapora, serwery obsługujące agentów uwierzytelniania oraz lokalny system Windows Server Active Directory (lub inny dostawca tożsamości) to wszystkie potencjalne punkty awarii. 

![Diagram architektury elementu PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Jak mogę wdrożyć PTA?

Aby zaimplementować uwierzytelnianie przekazywane, zobacz następujące zasoby.

* [Jak działa uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta-how-it-works.md)

* [Szczegółowe omówienie uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Zainstaluj uwierzytelnianie przekazywane usługi Azure AD](../hybrid/how-to-connect-pta-quick-start.md)

* Jeśli używasz PTA, zdefiniuj [topologię o wysokiej dostępności](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Federacja

Federacja obejmuje tworzenie relacji zaufania między usługą Azure AD i usługi federacyjnej, która obejmuje wymianę punktów końcowych, tokeny certyfikatów podpisywania i inne metadane. Gdy żądanie przejdzie do usługi Azure AD, odczytuje konfigurację i przekierowuje użytkownika do skonfigurowanych punktów końcowych. W tym momencie użytkownik współdziała z usługą federacyjną, która wystawia potwierdzenie SAML, które jest sprawdzane przez usługę Azure AD. 

Na poniższym diagramie przedstawiono topologię Active Directory Federation Services w przedsiębiorstwie (AD FS), wdrożenie obejmujące nadmiarowe serwery proxy aplikacji federacyjnych i sieci Web w wielu lokalnych centrach danych. Ta konfiguracja jest oparta na składnikach infrastruktury sieciowej przedsiębiorstwa, takich jak DNS, równoważenie obciążenia sieciowego z możliwościami koligacji geograficznej, zaporami itp. Wszystkie lokalne składniki i połączenia są podatne na awarię. Więcej informacji można znaleźć w dokumentacji dotyczącej [planowania pojemności AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

> [!NOTE]
>  Federacja ma największą liczbę lokalnych zależności i dlatego najbardziej potencjalne punkty awarii. Ten diagram przedstawia AD FS, natomiast inni dostawcy tożsamości lokalnych mogą spełniać podobne zagadnienia dotyczące projektowania, aby zapewnić wysoką dostępność, skalowalność i tryb failover.

![Diagram architektury Federacji](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Jak mogę zaimplementować Federacji?

Jeśli wdrażasz strategię uwierzytelniania federacyjnego lub chcesz bardziej odpornej na błędy, zapoznaj się z poniższymi zasobami.

* [Co to jest uwierzytelnianie federacyjne](../hybrid/whatis-fed.md)

* [Jak działa federacja](../hybrid/how-to-connect-fed-whatis.md)

* [Lista zgodności usługi Azure AD z usługami federacyjnymi](../hybrid/how-to-connect-fed-compatibility.md)

* Wykonaj czynności opisane w dokumentacji dotyczącej [planowania pojemności AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Wdrażanie AD FS na platformie Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Włącz PHS](../hybrid/tutorial-phs-backup.md) wraz z Federacją

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)