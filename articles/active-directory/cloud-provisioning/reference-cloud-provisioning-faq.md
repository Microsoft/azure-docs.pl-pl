---
title: Często zadawane pytania dotyczące aprowizacji w chmurze programu Azure AD Connect
description: Ten dokument zawiera opis często zadawanych pytań dotyczących aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28fb9da04c9c9d3e98b5226e1aee4cf5dde7183b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628676"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Azure Active Directory Connect — często zadawane pytania dotyczące aprowizacji w chmurze

Zapoznaj się z często zadawanymi pytaniami dotyczącymi usługi Azure Active Directory (Azure AD) łączenie z obsługą chmury.

## <a name="general-installation"></a>Instalacja ogólna

**P: jak często jest przeprowadzane Inicjowanie obsługi w chmurze?**

Zaplanowano uruchomienie aprowizacji w chmurze co 2 minuty. Co 2 minuty wszystkie zmiany skrótu użytkownika, grupy i hasła będą obsługiwane w usłudze Azure AD.

**P: widzi błędy synchronizacji skrótów haseł podczas pierwszego uruchomienia. Zalet?**

Jest to oczekiwane zachowanie. Błędy są spowodowane nieobecnością obiektu użytkownika w usłudze Azure AD. Gdy użytkownik zostanie zainicjowany do usługi Azure AD, skróty haseł powinny być inicjowane w kolejnym przebiegu. Zaczekaj na kilka przebiegów i upewnij się, że synchronizacja skrótów haseł nie ma już błędów.

**P: co się stanie, jeśli wystąpienie Active Directory ma atrybuty, które nie są obsługiwane przez obsługę administracyjną w chmurze (na przykład rozszerzenia katalogów)?**

Zainicjowanie obsługi administracyjnej usług w chmurze i udostępnienie obsługiwanych atrybutów. Nieobsługiwane atrybuty nie zostaną zainicjowane do usługi Azure AD. Przejrzyj rozszerzenia katalogu w Active Directory i upewnij się, że te atrybuty nie są potrzebne do przepływu do usługi Azure AD. Jeśli wymagane jest co najmniej jeden atrybut, należy rozważyć użycie Azure AD Connect synchronizacji lub przeniesienie wymaganych informacji do jednego z obsługiwanych atrybutów (na przykład atrybuty rozszerzenia 1-15).

**P: Jaka jest różnica między synchronizacją Azure AD Connect a obsługą chmury?**

W przypadku synchronizacji Azure AD Connect obsługa jest uruchamiana na lokalnym serwerze synchronizacji. Konfiguracja jest przechowywana na lokalnym serwerze synchronizacji. W przypadku Azure AD Connect aprowizacji w chmurze konfiguracja aprowizacji jest przechowywana w chmurze i uruchamiana w chmurze w ramach usługi Azure AD Provisioning. 

**P: Czy można użyć aprowizacji w chmurze do synchronizowania z wielu Active Directory lasów?**

Tak. Inicjowanie obsługi administracyjnej w chmurze może służyć do synchronizowania z wieloma lasami Active Directory. W środowisku z obsługą kilku lasów wszystkie odwołania (np. Menedżer) muszą znajdować się w domenie.  

**P: jak jest aktualizowany Agent?**

Agenci są autouaktualniani przez firmę Microsoft. W przypadku zespołu IT zmniejsza to obciążenie związane z koniecznością testowania i weryfikowania nowych wersji agentów. 

**P: Czy można wyłączyć Autouaktualnianie?**

Nie ma żadnego obsługiwanego sposobu wyłączenia autouaktualniania.

**P: Czy można zmienić kotwicę źródłową na potrzeby aprowizacji w chmurze?**

Domyślnie Inicjowanie obsługi w chmurze używa metody MS-ds-Consistency-GUID z alternatywą dla ObjectGUID jako kotwicą źródła. Nie ma żadnego obsługiwanego sposobu zmiany kotwicy źródłowej.

**P: widzę nowe jednostki usługi z nazwami domen usługi AD podczas korzystania z aprowizacji w chmurze. Czy jest to oczekiwane?**

Tak, Inicjowanie obsługi w chmurze tworzy jednostkę usługi dla konfiguracji aprowizacji z nazwą domeny jako główną nazwą usługi. Nie wprowadzaj żadnych zmian w konfiguracji głównej usługi.

**P: co się stanie, gdy synchronizowany użytkownik musi zmienić hasło przy następnym logowaniu?**

Jeśli synchronizacja skrótów haseł jest włączona w ramach aprowizacji w chmurze, a synchronizowany użytkownik musi zmienić hasło przy następnym logowaniu w lokalnej usłudze AD, Inicjowanie obsługi administracyjnej w chmurze nie powoduje udostępnienia skrótu hasła "do-zmiana" w usłudze Azure AD. Gdy użytkownik zmieni hasło, skrót hasła użytkownika jest inicjowany z usługi AD do usługi Azure AD.

**P: czy usługa Cloud Provisioning obsługuje zapisywanie zwrotne MS-ds-consistencyGUID dla dowolnego obiektu?**

Nie, Inicjowanie obsługi administracyjnej nie obsługuje zapisywania zwrotnego MS-ds-consistencyGUID dla żadnego obiektu (w tym obiektów użytkownika). 

**Pytanie: jestem użytkownikom aprowizacji przy użyciu aprowizacji w chmurze. Usunięto konfigurację. Dlaczego nadal widzę stare zsynchronizowane obiekty w usłudze Azure AD?** 

Po usunięciu konfiguracji Inicjowanie obsługi administracyjnej nie usuwa automatycznie zsynchronizowanych obiektów w usłudze Azure AD. Aby upewnić się, że nie masz starych obiektów, Zmień zakres konfiguracji na pustą grupę lub jednostki organizacyjne. Po uruchomieniu aprowizacji i oczyszczeniu obiektów należy wyłączyć i usunąć konfigurację. 

**P: co oznacza, że hybrydowe różnice między programem Exchange nie są obsługiwane?**

Funkcja wdrażania hybrydowego programu Exchange umożliwia współistnienie skrzynek pocztowych programu Exchange zarówno lokalnie, jak i w Microsoft 365. Program Azure AD Connect synchronizuje określony zbiór atrybutów z usługi Azure AD z katalogiem lokalnym.  Agent aprowizacji w chmurze obecnie nie synchronizuje tych atrybutów z powrotem w katalogu lokalnym, dlatego nie jest on obsługiwany jako zamiennik Azure AD Connect.

**P: Czy można zainstalować agenta aprowizacji w chmurze w systemie Windows Server Core?**

Nie, Instalacja agenta na serwerze Core nie jest obsługiwana.

**P: Czy można użyć serwera przemieszczania z agentem aprowizacji w chmurze?**

Nie, serwery przejściowe nie są obsługiwane.

**P: Czy można zsynchronizować konta użytkowników-Gości?**

Nie, synchronizowanie kont użytkowników gościa nie jest obsługiwane.

**P: czy po przeniesieniu użytkownika z jednostki organizacyjnej należącej do zakresu aprowizacji w chmurze do jednostki organizacyjnej, która ma zakres Azure AD Connect, co się dzieje?**

Użytkownik zostanie usunięty i utworzony.  Przeniesienie użytkownika z jednostki organizacyjnej należącej do zakresu aprowizacji w chmurze zostanie wyświetlone jako operacja usuwania.  Jeśli użytkownik jest przenoszony do jednostki organizacyjnej, która jest zarządzana przez Azure AD Connect, zostanie ona zainicjowana w usłudze Azure AD i utworzona przez nowego użytkownika.

**P: w przypadku zmiany nazwy lub przeniesienia jednostki organizacyjnej, która znajduje się w zakresie dla filtru aprowizacji w chmurze, co się dzieje z użytkownikiem, który został utworzony w usłudze Azure AD?**

Nic.  Użytkownicy nie zostaną usunięci w przypadku zmiany nazwy lub przeniesienia jednostki organizacyjnej.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
