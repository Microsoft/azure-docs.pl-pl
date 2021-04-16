---
title: Użyj Azure AD Identity Governance, aby przejrzeć i usunąć użytkowników zewnętrznych, którzy nie mają już dostępu do zasobów
description: Korzystanie z przeglądów dostępu w celu rozszerzenia usuwania dostępu członków organizacji partnerskich
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
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532234"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Przeglądanie i usuwanie Azure Active Directory zewnętrznych, którzy nie mają już dostępu do zasobów, za pomocą funkcji zarządzania tożsamościami w usłudze Azure AD

W tym artykule opisano funkcje i metody, które umożliwiają nakieruj i wybierz tożsamości zewnętrzne, aby można było je przeglądać i usuwać z usługi Azure AD, jeśli nie są już potrzebne. Chmura ułatwia niż kiedykolwiek współpracę z użytkownikami wewnętrznymi lub zewnętrznymi. Dzięki usłudze Office 365 organizacje zaczynają dostrzegać rozpowszechnienie tożsamości zewnętrznych (w tym gości), ponieważ użytkownicy współpracują ze sobą nad danymi, dokumentami lub cyfrowymi obszarami roboczymi, takimi jak usługa Teams. Organizacje muszą równoważyć, umożliwiając współpracę oraz spełnianie wymagań dotyczących zabezpieczeń i ładu. Część tych działań powinna obejmować ocenę i wyczyszczenie użytkowników zewnętrznych, którzy zostali zaproszeni do współpracy w dzierżawie, pochodzących z organizacji partnerskich i usunięcie ich z usługi Azure AD, gdy nie będą już potrzebni.

>[!NOTE]
>Do korzystania z przeglądów dostępu w usłudze Azure AD jest wymagana Azure AD — wersja Premium P2, Enterprise Mobility + Security płatna E5 lub licencja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Dlaczego warto przeglądać użytkowników z organizacji zewnętrznych w dzierżawie?

W większości organizacji użytkownicy końcowi inicjują proces zapraszania partnerów biznesowych i dostawców do współpracy. Konieczność współpracy napędza organizacje, aby zapewnić właścicielom zasobów i użytkownikom końcowych możliwość regularnego oceniania i potwierdzania użytkowników zewnętrznych. Często proces dołączania nowych partnerów współpracy jest zaplanowany i uwzględniany, ale w przypadku wielu współpracy nie ma jasnej daty zakończenia, nie zawsze jest oczywiste, gdy użytkownik nie potrzebuje już dostępu. Ponadto zarządzanie cyklem życia tożsamości powoduje, że przedsiębiorstwa muszą utrzymywać usługę Azure AD w czystym stanie i usuwać użytkowników, którzy już nie potrzebują dostępu do zasobów organizacji. Przechowywanie tylko odpowiednich odwołań do tożsamości partnerów i dostawców w katalogu pomaga zmniejszyć ryzyko związane z pracownikami, przypadkowo wybierając użytkowników zewnętrznych, którzy powinni zostać usunięci, i udziela im dostępu. Ten dokument zawiera kilka opcji, od zalecanych proaktywnych sugestii do działań reaktywnych i oczyszczania w celu zarządzania tożsamościami zewnętrznymi.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Przyznawanie i odwoływanie dostępu za pomocą zarządzania uprawnieniami

Funkcje zarządzania uprawnieniami umożliwiają [zautomatyzowany cykl życia tożsamości zewnętrznych](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) z dostępem do zasobów. Ustanowienie procesów i procedur do zarządzania dostępem za pośrednictwem zarządzania uprawnieniami i publikowanie zasobów za pośrednictwem pakietów dostępu, śledzenie dostępu użytkowników zewnętrznych do zasobów staje się znacznie mniej skomplikowanym problemem do rozwiązania. W przypadku zarządzania [dostępem](entitlement-management-overview.md) za pośrednictwem pakietów dostępu do zarządzania uprawnieniami w usłudze Azure AD Organizacja może centralnie definiować dostęp dla użytkowników, a także użytkowników z organizacji partnerskich i zarządzać nimi. Zarządzanie uprawnieniami używa zatwierdzeń i przypisań pakietów dostępu do śledzenia, gdzie użytkownicy zewnętrzni zażądali dostępu i do którego przypisano dostęp. W przypadku utraty wszystkich przypisań przez użytkownika zewnętrznego zarządzanie uprawnieniami może automatycznie usunąć tych użytkowników zewnętrznych z dzierżawy. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Znajdowanie gości, którzy nie zostali zaproszeni za pośrednictwem zarządzania uprawnieniami

Gdy pracownicy są upoważnieni do współpracy z użytkownikami zewnętrznymi, mogą zapraszać dowolną liczbę użytkowników spoza organizacji. Szukanie partnerów zewnętrznych i grupowanie ich w grupy dynamiczne dopasowane do firmy i ich przeglądanie może nie być możliwe, ponieważ może być zbyt wiele różnych poszczególnych firm do przejrzenia lub nie ma właściciela ani sponsora dla organizacji. Firma Microsoft udostępnia przykładowy skrypt programu PowerShell, który może ułatwić analizowanie użycia tożsamości zewnętrznych w dzierżawie. Skrypt wylicza tożsamości zewnętrzne i kategoryzuje je. Skrypt może pomóc w zidentyfikowaniu i oczyszczeniu tożsamości zewnętrznych, które mogą nie być już wymagane. W ramach danych wyjściowych skryptu przykładowy skrypt obsługuje automatyczne tworzenie grup zabezpieczeń zawierających zidentyfikowanych partnerów zewnętrznych bez grupy — do dalszej analizy i używania z przeglądami dostępu usługi Azure AD.
Skrypt jest dostępny w witrynie [GitHub.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) Po zakończeniu działania skryptu generuje on plik wyjściowy HTML, który przedstawia tożsamości zewnętrzne, które:

- Nie ma już członkostwa w grupie w dzierżawie
- Przypisanie roli uprzywilejowanej w dzierżawie
- Przypisanie do aplikacji w dzierżawie

Dane wyjściowe zawierają również poszczególne domeny dla każdej z tych tożsamości zewnętrznych. 

>[!NOTE]
>Skrypt przywołyty powyżej to przykładowy skrypt, który sprawdza członkostwo w grupie, przypisania ról i przypisania aplikacji w usłudze Azure AD. W aplikacjach mogą wystąpić inne przypisania, które użytkownicy zewnętrzni otrzymywali poza usługą Azure AD, takie jak SharePoint (bezpośrednie przypisanie członkostwa) lub RBAC platformy Azure lub Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Przeglądanie zasobów używanych przez tożsamości zewnętrzne

Jeśli masz tożsamości zewnętrzne korzystające z zasobów, takich jak aplikacje Teams lub inne aplikacje, które nie są jeszcze objęte zarządzaniem uprawnieniami, możesz również regularnie przeglądać dostęp do tych zasobów. Przeglądy dostępu [w](create-access-review.md) usłudze Azure AD dają możliwość przeglądania dostępu tożsamości zewnętrznych przez umożliwienie właścicielowi zasobu, samym tożsamościom zewnętrznym lub innej zaufanej osobie delegowaowej poświadczanie, czy nadal wymagany jest dostęp. Przeglądy dostępu są ukierunkowane na zasób i tworzą działanie przeglądu w zakresie wszyscy, którzy mają dostęp do zasobu, lub tylko użytkownicy-goście. Następnie recenzent zobaczy listę użytkowników, których musi przejrzeć — wszyscy użytkownicy, w tym pracownicy organizacji, lub tylko tożsamości zewnętrzne.

![używanie grupy do przeglądania dostępu](media/access-reviews-external-users/group-members.png)

Ustanowienie kultury przeglądu opartego na właścicielu zasobów pomaga zarządzać dostępem dla tożsamości zewnętrznych. Właściciele zasobów, którzy są odpowiedzialne za dostęp, dostępność i bezpieczeństwo posiadanych informacji, są w większości przypadków najlepszymi odbiorcami, którzy będą podejmować decyzje dotyczące dostępu do ich zasobów, i są bliżej użytkowników, którzy do nich dostęp mają, niż centralne zasoby IT lub sponsor, który zarządza wieloma podmiotami zewnętrznymi.

## <a name="create-access-reviews-for-external-identities"></a>Tworzenie przeglądów dostępu dla tożsamości zewnętrznych

Użytkowników, którzy nie mają już dostępu do żadnych zasobów w dzierżawie, można usunąć, jeśli nie pracują już z Twoją organizacją. Przed zablokowaniem i usunięciem tych tożsamości zewnętrznych możesz chcieć sokazać się z tymi użytkownikami zewnętrznymi i upewnić się, że nie przeoczyć projektu ani stałego dostępu, którego nadal potrzebują. Podczas tworzenia grupy zawierającej wszystkie tożsamości zewnętrzne jako elementy członkowskie, które zostały znalezione, nie mają dostępu do żadnych zasobów w dzierżawie, można użyć przeglądów dostępu, aby samodzielnie autotestować wszystkie zewnętrzne osoby, niezależnie od tego, czy nadal potrzebują dostępu, czy też będą nadal potrzebować dostępu w przyszłości. W ramach przeglądu twórca przeglądu w przeglądach  dostępu może użyć funkcji Wymagaj przyczyny zatwierdzenia, aby wymagać od użytkowników zewnętrznych uzasadnienia dalszego dostępu, za pomocą którego można dowiedzieć się, gdzie i w jaki sposób nadal potrzebują dostępu w dzierżawie. Ponadto możesz włączyć  ustawienie Dodatkowa zawartość dla funkcji poczty e-mail recenzenta, aby umożliwić użytkownikom powiadomienie, że utracą dostęp, jeśli nie odpowiedzą, i jeśli nadal będą potrzebować dostępu, wymagane jest uzasadnienie. Jeśli chcesz, aby przeglądy  dostępu wyłączały i usuwały tożsamości zewnętrzne, jeśli nie odpowiadają lub nie podają prawidłowej przyczyny dalszego dostępu, możesz użyć opcji Wyłącz i usuń, zgodnie z opisem w następnej sekcji.

![ograniczenie zakresu przeglądu tylko do użytkowników-gości](media/access-reviews-external-users/guest-users-only.png)

Po zakończeniu przeglądu na **stronie Wyniki** jest przedstawiany przegląd odpowiedzi nadanych przez każdą tożsamość zewnętrzną. Możesz wybrać automatyczne stosowanie wyników i pozwolić na ich wyłączenie i usunięcie przez przeglądy dostępu. Alternatywnie możesz sprawdzić podane odpowiedzi i zdecydować, czy chcesz usunąć dostęp użytkownika, czy wykonać z nim kolejne czynności, a następnie uzyskać dodatkowe informacje przed podjęciem decyzji. Jeśli niektórzy użytkownicy nadal mają dostęp do zasobów, które nie zostały jeszcze przejmowane, możesz skorzystać z przeglądu w ramach odnajdywania i wzbogacić następny przegląd i cykl zaświadczenia.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Wyłączanie i usuwanie tożsamości zewnętrznych za pomocą przeglądów dostępu usługi Azure AD

Oprócz możliwości usunięcia niechcianych tożsamości zewnętrznych z zasobów, takich jak grupy lub aplikacje, przeglądy dostępu usługi Azure AD mogą blokować logowanie się tożsamości zewnętrznych do dzierżawy i usuwanie tożsamości zewnętrznych z dzierżawy po upływie 30 dni. Po wybraniu opcji Blokuj logowanie użytkownika przez **30 dni,** a następnie usunięciu użytkownika z dzierżawy przegląd pozostanie w stanie "stosowanie" przez 30 dni. W tym okresie nie będzie można wyświetlać ani konfigurować ustawień, wyników, recenzentów ani dzienników inspekcji w ramach bieżącego przeglądu. 

![po zakończeniu pracy z ustawieniami](media/access-reviews-external-users/upon-completion-settings.png)

Podczas tworzenia nowego przeglądu dostępu w sekcji "Po  zakończeniu ustawień" dla ustawienia Akcja do zastosowania w przypadku odrzuconych użytkowników możesz zdefiniować opcję Blokuj logowanie użytkowników przez **30 dni,** a następnie usunąć użytkownika z dzierżawy .

To ustawienie umożliwia identyfikowanie, blokowanie i usuwanie tożsamości zewnętrznych z dzierżawy usługi Azure AD. Tożsamości zewnętrzne, które są przeglądane i odmawiają dalszego dostępu recenzenta, będą blokowane i usuwane niezależnie od dostępu do zasobów lub członkostwa w grupie. To ustawienie najlepiej wykorzystać jako ostatni krok po upewnieniu się, że użytkownicy zewnętrzni w przeglądzie nie mają już dostępu do zasobów i można je bezpiecznie usunąć z dzierżawy lub jeśli chcesz upewnić się, że są usunięci, niezależnie od ich stałego dostępu. Funkcja "Wyłącz i usuń" blokuje najpierw użytkownika zewnętrznego, co powoduje wyłączenie możliwości logowania się do dzierżawy i uzyskiwania dostępu do zasobów. Na tym etapie dostęp do zasobów nie jest odwołyowany, a jeśli chcesz ponownie utworzyć konto użytkownika zewnętrznego, jego możliwość logowania może zostać ponownie skonfigurowana. Po zakończeniu dalszych działań zablokowana tożsamość zewnętrzna zostanie usunięta z katalogu po 30 dniach, co spowoduje usunięcie konta oraz dostępu do niego.

## <a name="next-steps"></a>Następne kroki

- [Przeglądy dostępu — interfejs Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Zarządzanie upoważnieniami — interfejs Graph API](/graph/api/resources/entitlementmanagement-root)
