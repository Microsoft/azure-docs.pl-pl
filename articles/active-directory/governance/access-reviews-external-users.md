---
title: Użyj Azure AD Identity Governance, aby przejrzeć i usunąć użytkowników zewnętrznych, którzy nie mają już dostępu do zasobów
description: Korzystanie z przeglądów dostępu w celu zwiększenia dostępu do usuwania od członków organizacji partnerskich
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176964"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Użyj usługi Azure Active Directory (Azure AD) Zarządzanie tożsamościami, aby przejrzeć i usunąć użytkowników zewnętrznych, którzy nie mają już dostępu do zasobów

W tym artykule opisano funkcje i metody umożliwiające lokalizowanie i wybieranie tożsamości zewnętrznych, dzięki czemu można je przeglądać i usuwać z usługi Azure AD, jeśli nie są już potrzebne. Chmura ułatwia współpracę z użytkownikami wewnętrznymi lub zewnętrznymi. Korzystając z pakietu Office 365, organizacje zaczynają poznać proliferację tożsamości zewnętrznych (w tym Gości), ponieważ użytkownicy pracują razem z danymi, dokumentami lub cyfrowymi obszarami roboczymi, takimi jak zespoły. Organizacje muszą zrównoważyć i zapewnić współpracę oraz spełnić wymagania dotyczące zabezpieczeń i zarządzania. Część tych wysiłków powinna obejmować ocenę i czyszczenie użytkowników zewnętrznych, którzy zostali zaproszeni do współpracy w dzierżawie, którzy pochodzą z organizacji partnerskich i usuwać je z usługi Azure AD, gdy nie są już potrzebne.

>[!NOTE]
>Do korzystania z przeglądów dostępu do usługi Azure AD wymagana jest prawidłowa Azure AD — wersja Premium P2, Enterprise Mobility + Security E5 lub wersja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Dlaczego warto przeglądać użytkowników z organizacji zewnętrznych w Twojej dzierżawie?

W większości organizacji użytkownicy końcowi zainicjują proces zapraszania partnerów i dostawców firmy w celu współpracy. Potrzeba współpracy z organizacjami dysków, aby zapewnić właścicielom zasobów i użytkownikom końcowym możliwość regularnego szacowania i zaświadczania użytkowników zewnętrznych. Często proces dołączania nowych partnerów współpracy jest planowany i uwzględniany w programie, ale w przypadku wielu współpracy nie ma niejasnej daty zakończenia, nie zawsze jest oczywiste, gdy użytkownik nie potrzebuje już dostępu. Ponadto usługi zarządzania cyklem życiowym tożsamości przedsiębiorstwa umożliwiają czyszczenie i usuwanie użytkowników, którzy nie potrzebują już dostępu do zasobów organizacji. Przechowywanie wyłącznie odpowiednich odwołań tożsamości dla partnerów i dostawców w katalogu pozwala zmniejszyć ryzyko dla pracowników, przypadkowo wybierając i przyznając dostęp do użytkowników zewnętrznych, którzy zostali usunięci. W tym dokumencie przedstawiono kilka opcji, które przedziały od zalecanych proaktywnie sugestii, aby reaktywnie i czyścić działania związane z zarządzaniem tożsamościami zewnętrznymi.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Przyznawanie i odwoływanie dostępu przy użyciu zarządzania uprawnieniami

Funkcje zarządzania prawami umożliwiają [Automatyczne cykl życia tożsamości zewnętrznych](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) z dostępem do zasobów. Dzięki ustanowieniu procesów i procedur w celu zarządzania dostępem przez Zarządzanie prawami i publikowania zasobów za pomocą pakietów dostępu śledzenie dostępu użytkowników zewnętrznych do zasobów będzie znacznie mniej skomplikowanym problemem. W przypadku zarządzania dostępem za pośrednictwem [pakietów dostępu do zarządzania prawami](entitlement-management-overview.md) w usłudze Azure AD organizacja może centralnie definiować i zarządzać dostępem użytkowników, a także użytkowników z organizacji partnerskich. Zarządzanie prawami używa zatwierdzeń i przydziałów pakietów dostępu do śledzenia miejsca, w którym zewnętrzni użytkownicy zażądali i zostali przypisani do nich. Jeśli użytkownik zewnętrzny utraci wszystkie swoje przypisania, zarządzanie uprawnieniami może automatycznie usunąć tych użytkowników zewnętrznych z dzierżawy. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Znajdowanie Gości nie zapraszanych przez Zarządzanie uprawnieniami

Gdy pracownicy są uprawnieni do współpracy z użytkownikami zewnętrznymi, mogą zapraszać dowolną liczbę użytkowników spoza organizacji. Wyszukiwanie i grupowanie partnerów zewnętrznych w grupach dynamicznych wyrównanych do firmy i przeglądanie ich może nie być możliwe, ponieważ może istnieć zbyt wiele różnych firm lub nie ma właściciela ani sponsora dla organizacji. Firma Microsoft udostępnia przykładowy skrypt programu PowerShell, który ułatwia analizowanie użycia tożsamości zewnętrznych w dzierżawie. Skrypt wylicza tożsamości zewnętrzne i klasyfikuje je. Skrypt może pomóc identyfikować i czyścić tożsamości zewnętrzne, które mogą nie być już wymagane. W ramach danych wyjściowych skryptu przykład skryptu obsługuje zautomatyzowane tworzenie grup zabezpieczeń, które zawierają zidentyfikowanych partnerów zewnętrznych bez grupy — do dalszej analizy i użycia w ramach przeglądów dostępu do usługi Azure AD.
Skrypt jest dostępny w serwisie [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Po zakończeniu działania skryptu zostanie wygenerowany plik wyjściowy HTML, który zawiera zewnętrzne tożsamości, które są następujące:

- Nie masz już członkostwa w grupie w dzierżawie
- Przypisywanie roli uprzywilejowanej w dzierżawie
- Przypisywanie do aplikacji w dzierżawie

Dane wyjściowe obejmują również poszczególne domeny dla każdej z tych tożsamości zewnętrznych. 

>[!NOTE]
>Skrypt, do którego odwołuje się powyżej, to przykładowy skrypt sprawdzający członkostwo w grupach, przypisania ról i przypisania aplikacji w usłudze Azure AD. Mogą istnieć inne przydziały w aplikacjach, które użytkownicy zewnętrzni otrzymali poza usługę Azure AD, takie jak SharePoint (przypisanie bezpośredniego członkostwa) lub Azure RBAC lub Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Przeglądanie zasobów używanych przez tożsamości zewnętrzne

Jeśli masz tożsamości zewnętrzne korzystające z zasobów, takich jak zespoły lub inne aplikacje, które nie są jeszcze objęte zarządzaniem prawami, możesz chcieć regularnie sprawdzić dostęp do tych zasobów. [Przeglądy dostępu do](create-access-review.md) usługi Azure AD umożliwiają sprawdzenie dostępu tożsamości zewnętrznych przez umożliwienie właścicielowi zasobu, tożsamości zewnętrznych lub innej delegowanej osobie, która ma zaufać, do tego, czy będzie on nadal wymagał dostępu. Przeglądy dostępu są przeznaczone dla zasobu i tworzą działania recenzowania z zakresem wszyscy, którzy mają dostęp tylko do zasobów lub użytkowników-Gości. Recenzent zobaczy listę użytkowników, których potrzebują do przejrzenia — wszyscy użytkownicy, w tym pracownicy organizacji lub tożsamości zewnętrznych.

![używanie grupy do przeglądania dostępu](media/access-reviews-external-users/group-members.png)

Tworzenie kultury przeglądowej opartej na właścicielu zasobów ułatwia zarządzanie dostępem do tożsamości zewnętrznych. Właściciele zasobów, konto do uzyskiwania dostępu, dostępności i bezpieczeństwa posiadanych przez nich informacji, to w większości przypadków najlepszym odbiorcom do podejmowania decyzji dotyczących dostępu do swoich zasobów i są bliżej użytkowników, którzy uzyskują do nich dostęp niż centralny lub sponsor zarządzający wieloma zewnętrznymi.

## <a name="create-access-reviews-for-external-identities"></a>Tworzenie przeglądów dostępu dla tożsamości zewnętrznych

Użytkownicy, którzy nie mają już dostępu do żadnych zasobów w dzierżawie, mogą zostać usunięci, jeśli przestaną działać z Twoją organizacją. Przed zablokowaniem i usunięciem tożsamości zewnętrznych warto skontaktować się z tymi użytkownikami zewnętrznymi i upewnić się, że nie zainstalowano na nich projektu ani stałego dostępu do nich. Podczas tworzenia grupy, która zawiera wszystkie tożsamości zewnętrzne, gdy znalezione elementy członkowskie nie mają dostępu do żadnych zasobów w dzierżawie, można użyć funkcji przeglądy dostępu w celu samodzielnego zaświadczania o tym, czy nadal potrzebują lub masz dostęp — czy nadal będzie potrzebny dostęp w przyszłości. W ramach przeglądu Autor recenzji w przeglądach dostępu może użyć funkcji **Wymagaj przyczyny dotyczącej zatwierdzenia** , aby wymagać od użytkowników zewnętrznych udzielenia uzasadnienia dotyczącego ciągłego dostępu, za pomocą którego można dowiedzieć się, gdzie i jak nadal potrzebują dostępu w dzierżawie. Ponadto można włączyć funkcję ustawienia **dodatkowa zawartość dla programu Reviewer e-mail** , aby poinformować użytkowników o utracie dostępu, jeśli nie odpowiadają, a jeśli nadal będą musieli uzyskać dostęp, jest wymagane uzasadnienie. Jeśli chcesz, aby przeglądy dostępu **wyłączali i usuwali** tożsamości zewnętrzne, jeśli nie odpowiadają lub dostarczą prawidłowe przyczyny ciągłego dostępu, możesz użyć opcji Wyłącz i Usuń, zgodnie z opisem w następnej sekcji.

![Ograniczanie zakresu przeglądu tylko dla użytkowników-Gości](media/access-reviews-external-users/guest-users-only.png)

Po zakończeniu przeglądu na stronie **wyniki** zostanie wyświetlony przegląd odpowiedzi podawanej przez każdą tożsamość zewnętrzną. Możesz zdecydować się na automatyczne stosowanie wyników i uniemożliwić przeglądy dostępu. Alternatywnie możesz przeszukać podaną odpowiedź i zdecydować, czy chcesz usunąć dostęp użytkownika, czy też wykonać dodatkowe informacje przed podjęciem decyzji. Jeśli niektórzy użytkownicy nadal mają dostęp do zasobów, które nie zostały jeszcze przejrzane, możesz użyć przeglądu w ramach odnajdywania i wzbogacić swój kolejny przegląd i cykl zaświadczania.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Wyłączanie i usuwanie tożsamości zewnętrznych przy użyciu przeglądów dostępu do usługi Azure AD

Oprócz opcji usuwania niechcianych tożsamości zewnętrznych z zasobów, takich jak grupy lub aplikacje, przeglądy dostępu do usługi Azure AD mogą blokować tożsamości zewnętrzne przed zalogowaniem się do dzierżawy i usuwać tożsamości zewnętrzne z dzierżawy po 30 dniach. Po wybraniu opcji **Zablokuj użytkownikowi możliwość logowania przez 30 dni, a następnie usuń użytkownika z dzierżawy**, przegląd pozostanie w stanie "stosowanie" przez 30 dni. W tym okresie ustawienia, wyniki, recenzenci lub dzienniki inspekcji w ramach bieżącego przeglądu nie będą widoczne ani konfigurowalne. 

![Po zakończeniu ustawień](media/access-reviews-external-users/upon-completion-settings.png)

Podczas tworzenia nowego przeglądu dostępu w sekcji "po zakończeniu ustawień" dla **akcji do zastosowania w przypadku odrzuconych użytkowników** można zdefiniować **opcję Zablokuj użytkownikom możliwość logowania przez 30 dni, a następnie usunąć użytkownika z dzierżawy**.

To ustawienie pozwala identyfikować, blokować i usuwać tożsamości zewnętrzne z dzierżawy usługi Azure AD. Tożsamości zewnętrzne, które są przeglądane i odrzucane przez recenzenta, zostaną zablokowane i usunięte niezależnie od dostępu do zasobów lub członkostwa w grupie. To ustawienie jest najlepiej używane jako ostatni krok po sprawdzeniu, że użytkownicy zewnętrzni nie przeniesieją już dostępu do zasobów i mogą bezpiecznie zostać usunięci z dzierżawy lub jeśli chcesz, aby upewnić się, że zostały usunięte, niezależnie od ich stałego dostępu. Funkcja "Wyłącz i Usuń" blokuje najpierw użytkownika zewnętrznego, ale nie ma możliwości logowania się do dzierżawy i uzyskiwania dostępu do zasobów. Nie można odwołać dostępu do zasobów na tym etapie, a jeśli chcesz ponownie utworzyć wystąpienie użytkownika zewnętrznego, możliwość zalogowania się może zostać ponownie skonfigurowana. W przypadku braku dalszych działań zablokowana tożsamość zewnętrzna zostanie usunięta z katalogu po upływie 30 dni, usuwając konto oraz dostęp do niego.

## <a name="next-steps"></a>Następne kroki

- [Przeglądy dostępu — interfejs Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Zarządzanie upoważnieniami — interfejs Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
