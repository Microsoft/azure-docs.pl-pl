---
title: Użytkownicy nie są obsługiwani w mojej aplikacji
description: Jak rozwiązywać typowe problemy związane z niewidocznymi użytkownikami w aplikacji galerii usługi Azure AD skonfigurowanym do aprowizacji użytkowników w usłudze Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 21f16a0b9c8b3b603735520d5dcdbf61601e1480
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994829"
---
# <a name="no-users-are-being-provisioned"></a>Żadni użytkownicy nie są aprowizowani 
>[!NOTE]
>Rozpoczęcie 04/16/2020 zmieniono zachowanie dla użytkowników, którym przypisano domyślną rolę dostępu. Szczegóły można znaleźć w sekcji poniżej. 
>
Po skonfigurowaniu automatycznej aprowizacji dla aplikacji (w tym sprawdzić, czy poświadczenia aplikacji podane w usłudze Azure AD w celu nawiązania połączenia z aplikacją są prawidłowe), użytkownicy i/lub grupy są udostępniane aplikacji. Obsługa administracyjna jest określana na podstawie następujących elementów:

-   Użytkowników i grup **przypisanych** do aplikacji. Należy zauważyć, że alokacja zagnieżdżonych grup lub grup Microsoft 365 nie jest obsługiwana. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizowania prawidłowych atrybutów z usługi Azure AD z aplikacją. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
-   Czy istnieje **Filtr zakresu** , który umożliwia filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Jeśli zauważysz, że nie zainicjowano obsługi administracyjnej użytkowników, zapoznaj się z [dziennikami aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Możesz uzyskać dostęp do dzienników aprowizacji w Azure Portal, wybierając pozycję **Azure Active Directory** &gt; dzienniki aprowizacji **aplikacji w przedsiębiorstwie** &gt; **(wersja zapoznawcza)** w sekcji **działanie** . Możesz przeszukiwać dane aprowizacji na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub docelowym. Aby uzyskać szczegółowe informacje, zobacz [dzienniki aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki aprowizacji rejestrują wszystkie operacje wykonywane przez usługę aprowizacji, w tym Wysyłanie zapytań do usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji, wykonywanie zapytań względem aplikacji docelowej pod kątem istnienia tych użytkowników, porównując obiekty użytkownika między systemem. Następnie Dodaj, zaktualizuj lub Wyłącz konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemów z obsługą administracyjną, które należy wziąć pod uwagę
Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść do szczegółów, jeśli masz pomysł, gdzie zacząć.

- [Nie można uruchomić usługi aprowizacji](#provisioning-service-does-not-appear-to-start)
- [Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Nie można uruchomić usługi aprowizacji
W przypadku ustawienia **stanu aprowizacji** na **włączony** w sekcji **Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[ \] &gt;** w Azure Portal. Jednak na tej stronie nie są wyświetlane żadne inne szczegóły stanu po kolejnych ponownych ładowaniach, prawdopodobnie usługa jest uruchomiona, ale nie ukończyła jeszcze cyklu początkowego. Sprawdź **dzienniki aprowizacji (wersja zapoznawcza)** opisane powyżej, aby określić, jakie operacje usługa działa, i wystąpiły błędy.

>[!NOTE]
>Cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym. Cykl początkowy pozwala zwiększyć wydajność kolejnych synchronizacji.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani

Jeśli użytkownik jest wyświetlany jako "pominięty" w dziennikach aprowizacji, ważne jest, aby sprawdzić, czy jest to karta **kroki** w dzienniku. Poniżej przedstawiono typowe przyczyny i rozwiązania:

- **Skonfigurowano filtr określania zakresu** **, który umożliwia filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [Określanie zakresu filtrów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik jest "nieefektywnie uprawniony".** Jeśli zobaczysz ten konkretny komunikat o błędzie, przyczyną jest problem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z aplikacji i ponownie przypisz go. Aby uzyskać więcej informacji na temat przypisywania, zobacz [przypisywanie użytkowników lub grup dostępu](../manage-apps/assign-user-or-group-access-portal.md).
- **Brak wymaganego atrybutu lub nie został on wypełniony dla użytkownika.** Ważną kwestią do uwzględnienia podczas konfigurowania aprowizacji jest przejrzenie i skonfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Ta konfiguracja obejmuje ustawienie "dopasowania właściwości" służącego do unikatowego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". IDENTYFIKATORem zgodnym może być nazwa wyświetlana lub alias adresu e-mail. Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość pasująca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Inicjowanie obsługi administracyjnej użytkowników przypisanych do domyślnej roli dostępu
Domyślna rola w aplikacji z galerii jest nazywana rolą "domyślny dostęp". W przeszłości Użytkownicy przypisani do tej roli nie są obsługiwani i są oznaczeni jako pominięci w [dziennikach aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , z powodu "nie jest to efektywnie uprawniony". 

**Zachowanie dla konfiguracji aprowizacji utworzonych po 04/16/2020:** Użytkownicy przypisani do domyślnej roli dostępu zostaną ocenione tak samo, jak wszystkie inne role. Użytkownik, któremu przypisano domyślny dostęp nie zostanie pominięty jako "nie jako skuteczny do upoważnienia". 

**Zachowanie dla konfiguracji aprowizacji utworzonych przed 04/16/2020:** W ciągu następnych 3 miesięcy zachowanie będzie kontynuowane, tak jak dzisiaj. Użytkownicy z domyślną rolą dostępu zostaną pominięci jako nieskutecznie uprawniony. Po lipcu 2020 zachowanie będzie jednorodne dla wszystkich aplikacji. Nie będziemy pomijać użytkowników aprowizacji z domyślną rolą dostępu z powodu "nie jest to efektywnie uprawniony". Ta zmiana zostanie wprowadzona przez firmę Microsoft i nie jest wymagana żadna akcja dla klienta. Aby upewnić się, że Ci użytkownicy nadal są pomijani, nawet po tej zmianie, należy zastosować odpowiednie filtry określania zakresu lub cofnąć przypisanie użytkownika z aplikacji, aby upewnić się, że są poza zakresem.  

Aby uzyskać odpowiedzi na pytania dotyczące tych zmian, skontaktuj się z provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Następne kroki

[Azure AD Connect Sync: wyjaśnienie aprowizacji deklaracyjnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)