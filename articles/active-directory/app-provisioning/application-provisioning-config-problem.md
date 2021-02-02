---
title: Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD
description: Jak rozwiązywać typowe problemy związane z konfigurowaniem aprowizacji użytkowników w aplikacji już wymienionej w galerii aplikacji usługi Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 651bc199a43881add7faa00a9d1cf78000de6944
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256276"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD

Skonfigurowanie [automatycznej aprowizacji użytkowników](user-provisioning.md) dla aplikacji (jeśli jest obsługiwana), wymaga wprowadzenia określonych instrukcji w celu przygotowania aplikacji do automatycznej aprowizacji. Następnie można użyć Azure Portal, aby skonfigurować usługę aprowizacji do synchronizowania kont użytkowników z aplikacją.

Należy zawsze zacząć od znalezienia samouczka dotyczącego konfiguracji w celu skonfigurowania aprowizacji aplikacji. Następnie wykonaj te kroki, aby skonfigurować aplikację i usługę Azure AD w celu utworzenia połączenia aprowizacji. Listę samouczków dotyczących aplikacji można znaleźć na [liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak sprawdzić, czy obsługa administracyjna działa 

Po skonfigurowaniu usługi większość szczegółowych informacji o działaniu usługi może być narysowana z dwóch miejsc:

-   **Dzienniki aprowizacji (wersja zapoznawcza)** — [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) rejestrują wszystkie operacje wykonywane przez usługę aprowizacji, w tym kwerendy usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji. Zbadaj aplikację docelową pod kątem istnienia tych użytkowników, porównując obiekty użytkownika między systemem. Następnie Dodaj, zaktualizuj lub Wyłącz konto użytkownika w systemie docelowym na podstawie porównania. Możesz uzyskać dostęp do dzienników aprowizacji w Azure Portal, wybierając pozycję **Azure Active Directory** &gt; dzienniki aprowizacji **aplikacji w przedsiębiorstwie** &gt; **(wersja zapoznawcza)** w sekcji **działanie** .

-   **Bieżący stan —** Podsumowanie ostatniego przebiegu aprowizacji dla danej aplikacji można znaleźć w sekcji **Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[ \] &gt;** , w dolnej części ekranu w obszarze Ustawienia usługi programu. Bieżąca sekcja stanu pokazuje, czy cykl aprowizacji rozpoczął Inicjowanie obsługi kont użytkowników. Postęp cyklu można obejrzeć, sprawdzić, ilu użytkowników i grup została zainicjowana, i zobaczyć, ile ról zostało utworzonych. Jeśli występują błędy, szczegółowe informacje można znaleźć w temacie [dzienniki aprowizacji (... /Reports-monitoring/Concept-Provisioning-logs.MD? Context = Azure/Active-Directory/Manage-Apps/Context/Manage-Apps-Context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemów z obsługą administracyjną, które należy wziąć pod uwagę

Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść do szczegółów, jeśli masz pomysł, gdzie zacząć.

* [Nie można uruchomić usługi aprowizacji](#provisioning-service-does-not-appear-to-start)
* Nie można zapisać konfiguracji, ponieważ poświadczenia aplikacji nie działają
* [Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Nie można uruchomić usługi aprowizacji

W przypadku ustawienia **stanu aprowizacji** na **włączony** w sekcji **Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[ \] &gt;** w Azure Portal. Na tej stronie nie są jednak wyświetlane żadne inne szczegóły stanu po kolejnych ponownych ładowaniach. Prawdopodobnie usługa jest uruchomiona, ale nie ukończyła jeszcze cyklu początkowego. Sprawdź **dzienniki aprowizacji** opisane powyżej, aby określić operacje wykonywane przez usługę, a także błędy.

>[!NOTE]
>Cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, co poprawia wydajność kolejnych synchronizacji.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nie można zapisać konfiguracji, ponieważ poświadczenia aplikacji nie działają

Aby można było zainicjować obsługę administracyjną, usługa Azure AD wymaga poprawnych poświadczeń, które umożliwiają nawiązywanie połączenia z interfejsem API zarządzania użytkownikami udostępnionym przez tę aplikację. Jeśli te poświadczenia nie działają lub nie wiesz, co się dzieje, zapoznaj się z samouczkiem dotyczącym konfigurowania tej aplikacji, opisanej wcześniej.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani

Gdy użytkownik wyświetla jako "pominięte" w dziennikach aprowizacji, bardzo ważne jest odczytywanie szczegółowych informacji w komunikacie dziennika w celu ustalenia przyczyny. Poniżej przedstawiono typowe przyczyny i rozwiązania:

- **Skonfigurowano filtr określania zakresu** **, który umożliwia filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Użytkownik jest "nieefektywnie uprawniony".** Jeśli zobaczysz ten konkretny komunikat o błędzie, przyczyną jest problem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z aplikacji i ponownie przypisz go. Aby uzyskać więcej informacji, zobacz [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

- **Brak wymaganego atrybutu lub nie został on wypełniony dla użytkownika.** Ważną kwestią do uwzględnienia podczas konfigurowania aprowizacji jest sprawdzenie i skonfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Obejmuje to ustawienie "dopasowania właściwości", który służy do jednoznacznego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowania atrybutu aprowizacji użytkowników](../app-provisioning/customize-application-attributes.md).

  * **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". Może to być nazwa wyświetlana lub alias adresu e-mail, ponieważ Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość odpowiadająca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu Azure Active Directory](user-provisioning.md)
