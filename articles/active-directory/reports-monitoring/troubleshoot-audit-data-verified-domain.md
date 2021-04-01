---
title: Rozwiązywanie problemów z danymi inspekcji zweryfikowanej zmiany domeny | Microsoft Docs
description: Zawiera informacje, które będą widoczne w dziennikach aktywności Azure Active Directory w przypadku zmiany domeny zweryfikowanej przez użytkowników.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87117436"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Rozwiązywanie problemów: Inspekcja danych na zweryfikowanej zmianie domeny 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Mam wiele zmian w swoich użytkownikach i nie mam pewności, co jest przyczyną.

### <a name="symptoms"></a>Objawy

Sprawdź dzienniki inspekcji usługi Azure AD i Zobacz wiele aktualizacji użytkowników w mojej dzierżawie usługi Azure AD. Te **aktualizacje zdarzeń użytkownika** nie wyświetlają informacji o **aktorze** , co sprawia, że niepewność w przypadku, gdy/kto wyzwolił zmiany masy dla użytkowników. 

### <a name="cause"></a>Przyczyna

 Typowym powodem związanym ze zmianami obiektu grupowego jest niesynchroniczna operacja zaplecza o nazwie **ProxyCalc**.  **ProxyCalc** to logika, która określa odpowiednie adresy **userPrincipalName** i **proxy**, które są aktualizowane w usłudze Azure AD users, grupy lub kontakty. Projekt związany z **ProxyCalc** polega na tym, że wszystkie adresy **userPrincipalName** i **proxy** są spójne w usłudze Azure AD w dowolnym momencie. **ProxyCalc** musi być wyzwalana przez jawną zmianę, podobną do zweryfikowanej domeny, i nie jest bezterminowo uruchamiany w tle jako zadanie. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Co oznacza spójność UserPrincipalName? 

W przypadku użytkowników tylko w chmurze spójność oznacza, że element **userPrincipalName** jest ustawiony na zweryfikowany sufiks domeny. Po przetworzeniu niespójnego elementu **userPrincipalName** **ProxyCalc** przekonwertuje go na domyślny sufiks onmicrosoft.com, na przykład: username@Contoso.onmicrosoft.com 

W przypadku zsynchronizowanych użytkowników spójność oznacza, że element **userPrincipalName** jest ustawiony na zweryfikowany sufiks domeny i jest zgodny z lokalną wartością **userPrincipalName** (ShadowUserPrincipalName). W przypadku przetworzenia niespójnego elementu **userPrincipalName** **ProxyCalc** powróci do tej samej wartości co **ShadowUserPrincipalName** lub, w przypadku usunięcia sufiksu domeny z dzierżawy, przekonwertuje ją na domyślny sufiks domeny *. onmicrosoft.com. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Co oznacza spójność adresów serwera proxy? 

W przypadku użytkowników tylko w chmurze spójność oznacza, że adresy serwerów proxy są zgodne z zweryfikowanym sufiksem domeny. Gdy zostanie przetworzony niespójny adres serwera proxy, **ProxyCalc** przekonwertuje go na domyślny sufiks domeny *. onmicrosoft.com, na przykład: SMTP:username@Contoso.onmicrosoft.com 

W przypadku zsynchronizowanych użytkowników spójność oznacza, że adresy serwerów proxy są zgodne z wartościami lokalnych adresów serwera proxy (tj. ShadowProxyAddresses). **ProxyAddresses** powinny być zsynchronizowane z **ShadowProxyAddresses**. Jeśli zsynchronizowany użytkownik ma przypisaną licencję programu Exchange, adresy serwerów proxy muszą być zgodne z wartościami lokalnych adresów serwera proxy i muszą być zgodne z zweryfikowanym sufiksem domeny. W tym scenariuszu **ProxyCalc** będzie oczyszczał niespójny adres serwera proxy z niezweryfikowanym sufiksem domeny i zostanie usunięty z obiektu w usłudze Azure AD. W przypadku zweryfikowania niezweryfikowanej domeny w późniejszym czasie program **ProxyCalc** będzie ponownie obliczał i dodać adres serwera proxy z **ShadowProxyAddresses** z powrotem do obiektu w usłudze Azure AD.  

> [!NOTE]
> W przypadku zsynchronizowanych obiektów, aby uniknąć **ProxyCalc** logiki obliczeń nieoczekiwanych wyników, najlepszym rozwiązaniem jest ustawienie adresów serwera proxy na domenę zweryfikowaną usługi Azure AD w obiekcie lokalnym.  

  
Jednym z zadań administracyjnych, które mogą wyzwolić **ProxyCalc** , jest zawsze, że zweryfikowano zmianę domeny. To zadanie jest wykonywane za każdym razem, gdy zweryfikowana domena zostanie dodana/usunięta z dzierżawy usługi Azure AD, która wewnętrznie wyzwala **ProxyCalc**.  

Jeśli na przykład dodasz zweryfikowaną domenę Fabrikam.com do dzierżawy Contoso.onmicrosoft.com, ta akcja spowoduje wyzwolenie operacji ProxyCalc na wszystkich obiektach w dzierżawie. To zdarzenie zostanie przechwycone w dziennikach inspekcji usługi Azure AD w miarę **aktualizowania zdarzeń użytkowników** poprzedzonych przez **dodanie zweryfikowanej domeny** zdarzenia. Z drugiej strony, jeśli Fabrikam.com został usunięty z dzierżawy Contoso.onmicrosoft.com, wszystkie zdarzenia **użytkownika aktualizacji** będą poprzedzane zdarzeniem **usuwania zweryfikowanej domeny** .   

#### <a name="additional-notes"></a>Dodatkowe uwagi:

ProxyCalc nie powoduje zmian w określonych obiektach, które: 

- nie masz aktywnej licencji programu Exchange 
- Ustaw **MSExchRemoteRecipientType** na wartość null 
- nie są uważane za zasób udostępniony. Zasób udostępniony jest, gdy **CloudMSExchRecipientDisplayType** zawiera jedną z następujących wartości: **MailboxUser (Shared)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Skrzynka pocztowa grupy**, **zaplanowana Skrzynka pocztowa**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 Aby zbudować więcej korelacji między tymi dwoma zdarzeniami, firma Microsoft pracuje nad aktualizacją informacji **aktora** w dziennikach inspekcji, aby zidentyfikować te zmiany jako wyzwolone przez zweryfikowaną zmianę domeny. Ta akcja pomoże sprawdzić, kiedy zdarzenie zmiany zweryfikowanej domeny zostało wykonane i rozpoczęte w celu pogrupowania aktualizacji obiektów w swojej dzierżawie. 

Ponadto w większości przypadków nie wprowadzono żadnych zmian dla użytkowników, ponieważ ich adresy **userPrincipalName** i **proxy** są spójne, więc pracujemy nad wyświetlaniem w dziennikach inspekcji tylko tych aktualizacji, które spowodowały rzeczywistą zmianę obiektu. Ta akcja uniemożliwi zakłócenia w dziennikach inspekcji i pomaga administratorom skorelować pozostałe zmiany wprowadzone przez użytkownika do zweryfikowanego zdarzenia zmiany domeny, jak wyjaśniono powyżej. 

## <a name="next-steps"></a>Następne kroki

[Atrybuty cienia usługi synchronizacji Azure AD Connect](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
