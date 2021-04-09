---
title: Zarządzanie kontami administratorów dostępu awaryjnego — Azure AD
description: W tym artykule opisano sposób korzystania z kont dostępu awaryjnego w celu zapobiegania niezamierzonemu blokowaniu organizacji usługi Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6a1e4b3b44004ec6d03c293bbd10617b3d3af69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740826"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD

Ważne jest, aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure Active Directory (Azure AD), ponieważ nie można zalogować się ani aktywować konta innego użytkownika jako administrator. Można ograniczyć wpływ przypadkowego braku dostępu administracyjnego przez utworzenie dwóch lub więcej *kont dostępu awaryjnego* w organizacji.

Konta dostępu awaryjnego są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane. Zalecamy, aby zachować cel ograniczenia użycia konta awaryjnego tylko do czasu, gdy jest to absolutnie konieczne.

Ten artykuł zawiera wskazówki dotyczące zarządzania kontami dostępu awaryjnego w usłudze Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Dlaczego warto używać konta dostępu awaryjnego

Organizacja może potrzebować używania konta dostępu awaryjnego w następujących sytuacjach:

- Konta użytkowników są federacyjne, a Federacja jest obecnie niedostępna z powodu przerwy w działaniu sieci lub dostawcy tożsamości. Na przykład jeśli Host dostawcy tożsamości w Twoim środowisku został wyłączony, użytkownicy mogą nie być w stanie zalogować się, gdy usługa Azure AD przekieruje do swojego dostawcy tożsamości.
- Administratorzy są rejestrowani za pomocą usługi Azure AD Multi-Factor Authentication i wszystkie ich poszczególne urządzenia są niedostępne lub usługa jest niedostępna. Aby aktywować rolę, użytkownicy mogą nie być w stanie wykonać Multi-Factor Authentication. Na przykład awaria sieci w komórce uniemożliwia im odpowiadanie na rozmowy telefoniczne lub odbieranie wiadomości tekstowych, ale tylko dwa mechanizmy uwierzytelniania zarejestrowane na urządzeniu.
- Osoba z najnowszym dostępem administratora globalnego opuściła organizację. Usługa Azure AD zapobiega usuwaniu ostatniego konta administratora globalnego, ale nie zapobiega usuwaniu ani wyłączaniu konta w środowisku lokalnym. W każdej sytuacji organizacja nie może odzyskać konta.
- Nieprzewidziane okoliczności, takie jak klęski żywiołowej katastrofy, podczas których może być niedostępny telefon komórkowy lub inne sieci. 

## <a name="create-emergency-access-accounts"></a>Tworzenie kont dostępu awaryjnego

Utwórz dwa lub więcej kont dostępu awaryjnego. Konta te powinny być kontami tylko w chmurze, które korzystają z \* domeny. onmicrosoft.com, które nie są federacyjne ani synchronizowane z poziomu środowiska lokalnego.

Podczas konfigurowania tych kont muszą zostać spełnione następujące wymagania:

- Konta dostępu awaryjnego nie powinny być skojarzone z żadnym użytkownikiem w organizacji. Upewnij się, że Twoje konta nie są połączone z telefonami przenośnymi dostarczonymi przez pracownika, tokenami sprzętowymi, które podróżują z poszczególnymi pracownikami lub innymi poświadczeniami specyficznymi dla pracownika. To zabezpieczenie obejmuje sytuacje, w których pojedynczy pracownik jest nieosiągalny, gdy jest wymagana poświadczenie. Ważne jest, aby upewnić się, że wszystkie zarejestrowane urządzenia są przechowywane w znanej, zabezpieczonej lokalizacji z wieloma środkami komunikacji z usługą Azure AD.
- Mechanizm uwierzytelniania używany na potrzeby konta dostępu awaryjnego powinien być różny od tego, który jest używany przez inne konta administracyjne, w tym inne konta dostępu awaryjnego.  Jeśli na przykład normalne logowanie administratora jest realizowane za pośrednictwem lokalnej usługi MFA, usługa Azure AD MFA będzie innym mechanizmem.  Jeśli jednak usługa Azure AD MFA jest podstawową częścią uwierzytelniania dla kont administracyjnych, należy wziąć pod uwagę inne podejście, takie jak używanie dostępu warunkowego z zewnętrznym dostawcą usług MFA za pomocą niestandardowych kontrolek.
- Urządzenie lub poświadczenia nie mogą wygasnąć lub być w zakresie automatycznego oczyszczania z powodu braku użycia.  
- Należy zmienić przypisanie roli administratora globalnego na stałe dla kont dostępu awaryjnego. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Wyklucz co najmniej jedno konto z usługi uwierzytelniania wieloskładnikowego opartego na telefonie

Aby zmniejszyć ryzyko ataku wynikającego z złamanego hasła, usługa Azure AD zaleca, aby wymagać uwierzytelniania wieloskładnikowego dla wszystkich poszczególnych użytkowników. Ta grupa zawiera administratorów i wszystkich innych użytkowników (na przykład funkcjonariuszy finansów), których naruszone konto miałoby znaczący wpływ.

Co więcej, co najmniej jeden z kont dostępu awaryjnego nie powinien mieć tego samego mechanizmu usługi uwierzytelniania wieloskładnikowego co inne konta niekrytyczne. Obejmuje to rozwiązania do uwierzytelniania wieloskładnikowego innych firm. Jeśli masz zasady dostępu warunkowego, aby wymagać [uwierzytelniania wieloskładnikowego dla każdego administratora](../authentication/howto-mfa-userstates.md) usługi Azure AD i innych aplikacji połączonych oprogramowania jako usługi (SaaS), należy wykluczyć z tego wymagania konta dostępu awaryjnego i skonfigurować inny mechanizm. Ponadto należy się upewnić, że konta nie mają zasad uwierzytelniania wieloskładnikowego dla poszczególnych użytkowników.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Wyklucz co najmniej jedno konto z zasad dostępu warunkowego

W sytuacji awaryjnej nie ma możliwości, aby zasady nie blokowały dostępu do rozwiązania problemu. Co najmniej jedno konto dostępu awaryjnego powinno być wykluczone ze wszystkich zasad dostępu warunkowego.

## <a name="federation-guidance"></a>Wskazówki dotyczące Federacji

Niektóre organizacje wykorzystują usługi domenowe AD i usług AD FS oraz podobny dostawca tożsamości do sfederować usługi Azure AD. [Nie powinno być kont lokalnych z uprawnieniami administracyjnymi](../fundamentals/protect-m365-from-on-premises-attacks.md). Uwierzytelnianie główne i pozyskiwanie dla kont z uprawnieniami administracyjnymi poza usługą Azure AD dodaje niepotrzebne ryzyko w przypadku awarii lub naruszenia tych systemów.

## <a name="store-account-credentials-safely"></a>Bezpieczne przechowywanie poświadczeń konta

Organizacje muszą mieć pewność, że poświadczenia kont dostępu awaryjnego są bezpieczne i znane tylko osobom uprawnionym do ich używania. Niektórzy klienci używają kart inteligentnych i innych użytkowników używają haseł. Hasło do konta dostępu awaryjnego jest zwykle podzielone na dwie lub trzy części, zapisane w oddzielnych kawałkach papieru i przechowywane w bezpiecznym, bezpiecznym miejscu, w którym znajdują się bezpieczne, osobne lokalizacje.

W przypadku używania haseł upewnij się, że konta mają silne hasła, które nie wygasają hasła. W idealnym przypadku hasła powinny składać się z co najmniej 16 znaków i losowo generować.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorowanie dzienników logowania i inspekcji

Organizacje powinny monitorować aktywność logowania i rejestrowania inspekcji z kont awaryjnych i wyzwalać powiadomienia dla innych administratorów. Gdy monitorujesz działanie na kontach z systemem Break, możesz sprawdzić, czy te konta są używane tylko na potrzeby testowania i faktycznych zagrożeń. Możesz użyć usługi Azure Log Analytics do monitorowania dzienników logowania i wyzwalania alertów e-mail i wiadomości SMS dla administratorów po każdym zalogowaniu się z kont szklanych.

### <a name="prerequisites"></a>Wymagania wstępne

1. [Wyślij dzienniki logowania usługi Azure AD](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) do Azure monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Uzyskaj identyfikatory obiektów dla kont szklanych

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta przypisanego do roli administratora użytkownika.
1. Wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**.
1. Wyszukaj konto ze szkła Break i wybierz nazwę użytkownika.
1. Skopiuj i Zapisz atrybut identyfikatora obiektu, aby można było go później użyć.
1. Powtórz poprzednie kroki dla drugiego konta ze szlifem.

### <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta przypisanego do roli współautor monitorowania w programie Azure monitor.
1. Wybierz pozycję **wszystkie usługi**, wprowadź ciąg "log Analytics" w obszarze wyszukiwania, a następnie wybierz pozycję **log Analytics obszary robocze**.
1. Wybierz obszar roboczy.
1. W obszarze roboczym wybierz pozycję **alerty**  >  **Nowa reguła alertu**.
    1. W obszarze **zasób** Sprawdź, czy subskrypcja jest tą, z którą chcesz skojarzyć regułę alertu.
    1. W obszarze **warunek** wybierz pozycję **Dodaj**.
    1. Wybierz pozycję **niestandardowe wyszukiwanie w dzienniku** w obszarze **Nazwa sygnału**.
    1. W obszarze **zapytanie wyszukiwania** wprowadź następujące zapytanie, wstawiając identyfikatory obiektów dwóch kont szklanych.
        > [!NOTE]
        > Dla każdego dodatkowego konta ze szlifem, które chcesz dołączyć, Dodaj kolejną "lub UserId = =" ObjectGuid "" do zapytania.

        ![Dodawanie identyfikatorów obiektów ze szkła podziału do reguły alertu](./media/security-emergency-access/query-image1.png)

    1. W obszarze **logika alertu** wpisz następujące polecenie:

        - Na podstawie: liczba wyników
        - Operator: większe niż
        - Wartość progowa: 0

    1. W obszarze **obliczane na podstawie** wybierz **okres (w minutach) okresu** działania zapytania oraz **częstotliwość (w minutach)** , z jaką ma być uruchamiana kwerenda. Częstotliwość powinna być mniejsza lub równa okresowi.

        ![Logika alertu](./media/security-emergency-access/alert-image2.png)

    1. Kliknij **Gotowe**. Teraz możesz wyświetlić szacowany koszt miesięczny tego alertu.
1. Wybierz grupę akcji użytkowników, którzy będą powiadamiani o alertach. Jeśli chcesz go utworzyć, zobacz [Tworzenie grupy akcji](#create-an-action-group).
1. Aby dostosować powiadomienia e-mail wysyłane do członków grupy akcji, wybierz akcje w obszarze **Dostosuj akcje**.
1. W obszarze **szczegóły alertu** Określ nazwę reguły alertu i Dodaj opcjonalny opis.
1. Ustaw **poziom ważności** zdarzenia. Zalecamy, aby ustawić ją na wartość **krytyczną (ważność 0)**.
1. W obszarze **Włącz regułę przy tworzeniu** pozostaw wartość **tak**.
1. Aby wyłączyć alerty przez pewien czas, zaznacz pole wyboru **Pomiń alerty** , a następnie wprowadź czas oczekiwania przed ponownym wysłaniem alertu, a następnie wybierz pozycję **Zapisz**.
1. Kliknij pozycję **Utwórz regułę alertu**.

### <a name="create-an-action-group"></a>Tworzenie grupy akcji

1. Wybierz pozycję **Utwórz grupę akcji**.

    ![Tworzenie grupy akcji dla akcji powiadomień](./media/security-emergency-access/action-group-image3.png)

1. Wprowadź nazwę grupy akcji i krótką nazwę.
1. Sprawdź subskrypcję i grupę zasobów.
1. W obszarze Typ akcji wybierz pozycję **poczta e-mail/SMS/wypychanie/głos**.
1. Wprowadź nazwę akcji, na przykład **Powiadom administratora globalnego**.
1. Wybierz **Typ akcji** jako **adres e-mail/SMS/wypychanie/głos**.
1. Wybierz pozycję **Edytuj szczegóły** , aby wybrać metody powiadamiania, które chcesz skonfigurować, i wprowadź wymagane informacje kontaktowe, a następnie wybierz przycisk **OK** , aby zapisać szczegóły.
1. Dodaj wszelkie dodatkowe akcje, które mają zostać wyzwolone.
1. Wybierz przycisk **OK**.

## <a name="validate-accounts-regularly"></a>Regularnie Weryfikuj konta

W przypadku uczenia członków personelu do korzystania z kont dostępu awaryjnego i weryfikowania kont dostępu awaryjnego należy co najmniej wykonać następujące czynności w regularnych odstępach czasu:

- Upewnij się, że pracownicy monitorujący zabezpieczenia są świadomi działania sprawdzania konta.
- Upewnij się, że proces szkła przerwania awaryjnego do korzystania z tych kont jest udokumentowany i aktualny.
- Upewnij się, że administratorzy i funkcjonariusze zabezpieczeń, którzy będą musieli wykonać te czynności w trakcie nagłego postępowania są przeszkolone na tym etapie.
- Zaktualizuj poświadczenia konta, w szczególności hasła, dla kont dostępu awaryjnego, a następnie sprawdź, czy konta dostępu awaryjnego mogą się zalogować i wykonywać zadania administracyjne.
- Upewnij się, że użytkownicy nie zarejestrowali Multi-Factor Authentication lub samoobsługowego resetowania hasła (SSPR) na dowolne urządzenie użytkownika lub szczegółowe dane osobowe. 
- Jeśli konta są zarejestrowane dla Multi-Factor Authentication na urządzeniu, do użycia podczas przeprowadzania logowania lub uaktywniania roli, należy się upewnić, że urządzenie jest dostępne dla wszystkich administratorów, którzy mogą potrzebować go w sytuacji awaryjnej. Sprawdź również, czy urządzenie może komunikować się za pomocą co najmniej dwóch ścieżek sieciowych, które nie mają wspólnego trybu awarii. Na przykład urządzenie może komunikować się z Internetem za pomocą sieci bezprzewodowej i sieci dostawcy komórkowej.

Te kroki należy wykonać w regularnych odstępach czasu i w przypadku kluczowych zmian:

- Co najmniej co 90 dni
- W przypadku niedawnej zmiany pracowników działu IT, takich jak zmiana zadania, wyjście lub nowe zatrudnienie
- Gdy subskrypcje usługi Azure AD w organizacji zostały zmienione

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](security-planning.md)
- [Dodawanie użytkowników przy użyciu usługi Azure AD](../fundamentals/add-users-azure-active-directory.md) i [przypisywanie nowego użytkownika do roli administratora globalnego](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Utwórz konto w usłudze Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md), jeśli jeszcze nie masz konta
- [Jak wymagać weryfikacji dwuetapowej dla użytkownika](../authentication/howto-mfa-userstates.md)
- [Skonfiguruj dodatkowe zabezpieczenia dla administratorów globalnych w Microsoft 365](/office365/enterprise/protect-your-global-administrator-accounts), jeśli używasz Microsoft 365
- [Rozpocznij przegląd dostępu administratorów globalnych](../privileged-identity-management/pim-how-to-start-security-review.md) i [Przechodzenie do istniejących administratorów globalnych, aby uzyskać bardziej szczegółowe role](permissions-reference.md) administratorów