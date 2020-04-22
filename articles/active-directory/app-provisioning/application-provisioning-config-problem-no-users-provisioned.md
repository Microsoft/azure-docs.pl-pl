---
title: Użytkownicy nie są aprowizacji w mojej aplikacji
description: Jak rozwiązywać typowe problemy napotykane, gdy nie widzisz użytkowników pojawiających się w aplikacji galerii usługi Azure AD skonfigurowanej do inicjowania obsługi administracyjnej przez użytkowników za pomocą usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac6d4f24d3b6c21828ccb11785005df736c6b070
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680350"
---
# <a name="no-users-are-being-provisioned"></a>Żadni użytkownicy nie są aprowizowani 
>[!NOTE]
>Począwszy od 16.04.2020 zmieniliśmy zachowanie użytkowników przypisanych domyślnej roli dostępu. Szczegółowe informacje można znaleźć w poniższej sekcji. 
>
Po skonfigurowaniu automatycznego inicjowania obsługi administracyjnej dla aplikacji (w tym sprawdzenia, czy poświadczenia aplikacji dostarczone do usługi Azure AD w celu nawiązania połączenia z aplikacją są prawidłowe), użytkownicy i/lub grupy są aprowizowani aplikacji. Inicjowanie obsługi administracyjnej zależy od następujących elementów:

-   Którzy użytkownicy i grupy zostały **przypisane** do aplikacji. Należy zauważyć, że inicjowanie obsługi administracyjnej grup zagnieżdżonych lub grup usługi Office 365 nie jest obsługiwane. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizowania prawidłowych atrybutów z usługi Azure AD do aplikacji. Aby uzyskać więcej informacji na temat mapowania atrybutów, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej użytkowników dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
-   Niezależnie od tego, czy istnieje **filtr zakresu,** który filtruje użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Jeśli zauważysz, że użytkownicy nie są aprowizacji, zapoznaj się z [dzienników inicjowania obsługi administracyjnej (w wersji zapoznawczej)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Dzienniki inicjowania obsługi administracyjnej można uzyskać w witrynie Azure Portal, wybierając **dzienniki inicjowania obsługi administracyjnej** aplikacji **usługi Azure Active Directory** &gt; **Enterprise Apps** &gt; (wersja zapoznawcza) w sekcji **Działanie.** Można przeszukiwać dane inicjowania obsługi administracyjnej na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub systemie docelowym. Aby uzyskać szczegółowe informacje, zobacz [Inicjowanie obsługi administracyjnej dzienników (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki inicjowania obsługi administracyjnej rejestrują wszystkie operacje wykonywane przez usługę inicjowania obsługi administracyjnej, w tym wykonywanie zapytań usługi Azure AD dla przypisanych użytkowników, którzy są w zakresie inicjowania obsługi administracyjnej, wykonywanie zapytań do aplikacji docelowej pod kątem istnienia tych użytkowników, porównywanie obiektów użytkownika między systemem. Następnie dodaj, zaktualizuj lub wyłącz konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemowe z inicjowania obsługi administracyjnej do rozważenia
Poniżej znajduje się lista ogólnych obszarów problemowych, które można drążyć, jeśli masz pomysł, od czego zacząć.

- [Usługa inicjowania obsługi administracyjnej nie uruchamia się](#provisioning-service-does-not-appear-to-start)
- [Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są pomijani i nie są aprowizacji, nawet jeśli są one przypisane](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa inicjowania obsługi administracyjnej nie uruchamia się
Jeśli ustawisz **stan inicjowania obsługi administracyjnej** **w** sekcji **\] &gt;Inicjowanie obsługi administracyjnej aplikacji aplikacji usługi Azure Active Directory &gt; Enterprise Apps &gt; \[** w portalu Azure. Jednak żadne inne szczegóły stanu są wyświetlane na tej stronie po kolejnych przeładowaniach, jest prawdopodobne, że usługa jest uruchomiona, ale nie ukończyła jeszcze początkowego cyklu. Sprawdź **dzienniki inicjowania obsługi administracyjnej (wersja zapoznawcza)** opisane powyżej, aby określić, jakie operacje wykonuje usługa i czy występują błędy.

>[!NOTE]
>Początkowy cykl może trwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa inicjowania obsługi administracyjnej przechowuje znaki wodne reprezentujące stan obu systemów po cyklu początkowym. Początkowy cykl zwiększa wydajność kolejnych synchronizacji.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są pomijani i nie są aprowiowane, nawet jeśli są przypisane

Gdy użytkownik pojawi się jako "pominięte" w dziennikach inicjowania obsługi administracyjnej, ważne jest, aby przejrzeć **kroki** kartę dziennika, aby ustalić przyczynę. Poniżej znajdują się typowe powody i rozwiązania:

- **Skonfigurowano filtr zakresu,** **który filtruje użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [filtry zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik "nie jest skutecznie uprawniony".** Jeśli zostanie wyświetlony ten konkretny komunikat o błędzie, jest to spowodowane problemem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, co likwiduje przypisanie użytkownika (lub grupy) z aplikacji i przypisz go ponownie. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie dostępu użytkownika lub grupy](../manage-apps/assign-user-or-group-access-portal.md).
- **Brak wymaganego atrybutu lub nie jest wypełniany dla użytkownika.** Ważną rzeczą, którą należy wziąć pod uwagę podczas konfigurowania inicjowania obsługi administracyjnej jest przeglądanie i konfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) przepływają z usługi Azure AD do aplikacji. Ta konfiguracja obejmuje ustawienie "właściwości pasującej", która jest używana do jednoznacznej identyfikacji i dopasowania użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowań atrybutów inicjowania obsługi administracyjnej dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazwy grupy i szczegółów grupy, oprócz członków, jeśli są obsługiwane dla niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **mapowanie** obiektów grupy wyświetlanych na karcie **Inicjowanie obsługi administracyjnej.** Jeśli grupy inicjowania obsługi administracyjnej jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "pasujące identyfikator". Pasującym identyfikatorem może być wyświetlana nazwa lub alias e-mail. Grupa i jej członkowie nie są aprowiowane, jeśli pasująca właściwość jest pusta lub nie wypełniona dla grupy w usłudze Azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Inicjowanie obsługi administracyjnej użytkowników przypisanych do domyślnej roli dostępu
Domyślna rola aplikacji z galerii jest nazywana rolą "dostępu domyślnego". Historycznie użytkownicy przypisani do tej roli nie są aprowizacji i są oznaczone jako pominięte w [dzienników inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) ze względu na "nie skutecznie uprawniony." 

**Zachowanie dla inicjowania obsługi administracyjnej konfiguracji utworzonych po 16.04.2020:** Użytkownicy przypisani do domyślnej roli dostępu będą oceniani tak samo jak wszystkie inne role. Użytkownik, który ma przypisany dostęp domyślny, nie zostanie pominięty jako "nie skutecznie uprawniony". 

**Zachowanie dla inicjowania obsługi administracyjnej konfiguracji utworzonych przed 16.04.2020:** Przez następne 3 miesiące zachowanie będzie kontynuowane tak, jak jest dzisiaj. Użytkownicy z domyślną rolą dostępu zostaną pominięci jako nieuwzwiązania. Po lipcu 2020 r. zachowanie będzie jednolite dla wszystkich wniosków. Nie pominiemy inicjowania obsługi administracyjnej użytkowników z domyślną rolą dostępu ze względu na to, że "nie jest skutecznie uprawniony". Ta zmiana zostanie wywłyszona przez firmę Microsoft, bez potrzeby działania klienta. Jeśli chcesz upewnić się, że ci użytkownicy nadal są pomijane, nawet po tej zmianie, należy zastosować odpowiednie filtry zakresu lub odsebić użytkownika z aplikacji, aby upewnić się, że są one poza zakresem.  

W przypadku pytań dotyczących tych zmian prosimy o kontakt zprovisioningfeedback@microsoft.com
## <a name="next-steps"></a>Następne kroki

[Synchronizacja usługi Azure AD Connect: opis aprowizacji deklaratywnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
