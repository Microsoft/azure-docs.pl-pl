---
title: Zmień ustawienia zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania — Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia informacji o zatwierdzeniu i żądającym dla pakietu dostępu w Azure Active Directory zarządzania prawami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591308"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmiana ustawień zatwierdzania i informacji osoby żądającej (wersja zapoznawcza) dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Jako Menedżer pakietów programu Access można w dowolnym momencie zmienić ustawienia zatwierdzania i informacji osoby żądającej, edytując istniejące zasady lub dodając nowe zasady.

W tym artykule opisano sposób zmiany ustawień zatwierdzania i informacji osoby żądającej dla istniejącego pakietu dostępu.

## <a name="approval"></a>Zatwierdzenie

W sekcji zatwierdzenie należy określić, czy zatwierdzenie ma być wymagane, jeśli użytkownicy zażądają tego pakietu dostępu. Ustawienia zatwierdzania działają w następujący sposób:

- Tylko jeden z wybranych osób zatwierdzających lub zatwierdzeń powrotu musi zatwierdzić żądanie zatwierdzenia pojedynczego etapu. 
- Tylko jedna z wybranych osób zatwierdzających z poszczególnych etapów musi zatwierdzić żądanie zatwierdzenia 2-etapowe.
- Osoba zatwierdzająca może być kierownikiem, wewnętrznym sponsorem lub zewnętrznym sponsorem w zależności od tego, kto ma dostęp do zasad.
- Zatwierdzenie od każdej wybranej osoby zatwierdzającej nie jest wymagane w przypadku zatwierdzania jednego lub 2-etapowego.
- Decyzja o zatwierdzeniu zależy od osoby zatwierdzającej, która najpierw przegląda żądanie.

Aby zapoznać się z pokazem dodawania osób zatwierdzających do zasad żądań, Obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Aby zapoznać się z przykładem dodawania wieloetapowego zatwierdzania do zasad żądania, Obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>Zmień ustawienia zatwierdzania istniejącego pakietu dostępu

Wykonaj następujące kroki, aby określić ustawienia zatwierdzania dla żądań pakietu dostępu:

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Wybierz zasady, aby edytować lub dodać nowe zasady do pakietu dostępu
    1. Kliknij pozycję **zasady** , a następnie **Dodaj zasady** , jeśli chcesz utworzyć nowe zasady.
    1. Kliknij zasady, które chcesz edytować, a następnie kliknij przycisk **Edytuj**.

1. Przejdź do karty **żądanie** .

1. Aby wymagać zatwierdzenia dla żądań od wybranych użytkowników, ustaw przełącznik **Wymagaj zatwierdzenia** na **wartość tak**. Lub, aby żądania były automatycznie zatwierdzane, ustaw przełącznik na wartość **nie**.

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, ustaw przełącznik Żądaj **uzasadnienia żądającego** na **wartość tak**.
    
1. Teraz Ustal, czy żądania będą wymagały zatwierdzenia pojedynczego lub 2-etapowego. Ustaw liczbę przełączeń między **etapami** na **1** w celu zatwierdzenia pojedynczego etapu lub ustaw przełącznik na **2** na potrzeby zatwierdzania dwuetapowego.

    ![Dostęp do pakietów — ustawienia zatwierdzenia](./media/entitlement-management-access-package-approval-policy/approval.png)


Aby dodać osoby zatwierdzające, należy wykonać następujące czynności: 

### <a name="single-stage-approval"></a>Zatwierdzenie na jednym etapie

1. Dodaj **pierwszą osobę zatwierdzającą**:
    
    Jeśli zasady są skonfigurowane do zarządzania dostępem dla użytkowników w katalogu, można wybrać pozycję **Menedżer jako osoba zatwierdzająca**. Lub Dodaj określonego użytkownika, klikając przycisk **Dodaj osoby zatwierdzające** po wybraniu opcji wybierz określonych osoby zatwierdzające z menu rozwijanego.
    
    ![Dostęp do pakietów — dla użytkowników w katalogu — w pierwszej kolejności](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Jeśli te zasady mają ustawiony dostęp do zarządzania użytkownikami spoza katalogu, możesz wybrać **zewnętrznego sponsora** lub **wewnętrznego sponsora**. Lub Dodaj określonego użytkownika, klikając pozycję **Dodaj osoby zatwierdzające** lub grupy w obszarze Wybierz określone osoby zatwierdzające.
    
    ![Dostęp do pakietów żądań — dla użytkowników poza katalogiem — pierwszej osoby zatwierdzającej](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. W przypadku wybrania opcji **Menedżer** jako pierwszej osoby zatwierdzającej kliknij pozycję **Dodaj rezerwę** , aby wybrać co najmniej jednego użytkownika lub grupę w katalogu jako osobę zatwierdzającą. Osoby zatwierdzające rezerwy otrzymują żądanie, jeśli Zarządzanie uprawnieniami nie może znaleźć Menedżera dla użytkownika żądającego dostępu.

    Menedżer został znaleziony przez Zarządzanie uprawnieniami przy użyciu atrybutu **Menedżera** . Ten atrybut znajduje się w profilu użytkownika w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. W przypadku wybrania **opcji wybierz określone osoby zatwierdzające** kliknij przycisk **Dodaj osoby zatwierdzające** , aby wybrać co najmniej jednego użytkownika lub grupę w katalogu, który ma zostać zatwierdzony.

1. W polu w obszarze **decyzja musi być podejmowana w ciągu kilku dni?** Określ liczbę dni, przez które osoba zatwierdzająca musi przejrzeć żądanie tego pakietu dostępu.

    Jeśli żądanie nie zostanie zatwierdzone w tym okresie, zostanie automatycznie odrzucone. Użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu.

1. Aby wymagać, aby osoby zatwierdzające zapewnią uzasadnienie swojej decyzji, należy ustawić opcję Wymagaj uzasadnienia osoby zatwierdzającej na **wartość tak**.

    Uzasadnienie jest widoczne dla innych osób zatwierdzających i osoby zgłaszającej żądanie.

### <a name="2-stage-approval"></a>2 — zatwierdzenie na etapie

W przypadku wybrania 2-etapowego zatwierdzania należy dodać drugą osobę zatwierdzającą.

1. Dodaj **drugą osobę zatwierdzającą**: 
    
    Jeśli użytkownicy znajdują się w katalogu, należy dodać określonego użytkownika jako drugiej osoby zatwierdzającej, klikając przycisk **Dodaj osoby zatwierdzające** w obszarze Wybieranie określonych osób zatwierdzających.

    ![Dostęp do pakietu-żądania — dla użytkowników w katalogu sekundy osoby zatwierdzającej](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Jeśli użytkownicy nie znajdują się w katalogu, wybierz **wewnętrznej sponsora** lub **zewnętrzny sponsor** jako drugą osobę zatwierdzającą. Po wybraniu osoby zatwierdzającej Dodaj osoby zatwierdzające rezerw.

    ![Dostęp do pakietu-żądania — dla użytkowników poza katalogiem w drugiej osoby zatwierdzającej](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Określ liczbę dni, przez którą druga osoba zatwierdzająca musi zatwierdzić żądanie w polu objętym **decyzją musi być podjęta w ciągu kilku dni?**. 

1. Ustaw opcję Wymagaj uzasadnienia osoby zatwierdzającej na **wartość tak** lub **nie**.

### <a name="alternate-approvers"></a>Alternatywne osoby zatwierdzające

Można określić alternatywne osoby zatwierdzające podobne do określenia pierwszego i drugiego zatwierdzenia, które mogą zatwierdzać żądania. Posiadanie zastępców osób zatwierdzających zagwarantuje, że żądania są zatwierdzane lub odrzucane przed wygaśnięciem (limit czasu). Można wyświetlić listę zastępczych osób zatwierdzających pierwszej osoby zatwierdzającej i drugiej osoby zatwierdzającej na potrzeby zatwierdzania dwuetapowego. 

Określając alternatywne osoby zatwierdzające, w przypadku, gdy pierwszej lub drugiej osoby zatwierdzającej nie mogły zatwierdzić lub odrzucić żądania, oczekujące żądanie zostanie przekazane do alternatywnej osoby zatwierdzającej zgodnie z harmonogramem przekazywania określonym podczas konfigurowania zasad. Otrzymają wiadomość e-mail z prośbą o zatwierdzenie lub odrzucenie oczekującego żądania.

Po przejściu żądania do zastępców osoby zatwierdzające mogą nadal zatwierdzać lub odrzucać żądania. Alternatywne osoby zatwierdzające używają tej samej witryny z dostępem w celu zatwierdzenia lub odrzucenia oczekującego żądania.

Możemy wyświetlić listę osób lub grup osób, które mają być osobami zatwierdzającymi i alternatywnymi osobami zatwierdzającymi. Upewnij się, że podajesz listę różnych zestawów osób jako pierwszej, drugiej i alternatywnej osoby zatwierdzającej.
Na przykład, jeśli w pierwszej osoby zatwierdzającej wystawiłeś Alicja i Robert, lista Karoliny i Dave jako alternatywne osoby zatwierdzające. Wykonaj następujące kroki, aby dodać alternatywne osoby zatwierdzające do pakietu dostępu:

1. W obszarze pierwszej osoby zatwierdzającej, drugiej osoby zatwierdzającej lub obu, kliknij przycisk **Pokaż zaawansowane ustawienia żądania**.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Dostęp do pakietu-zasady-Pokaż zaawansowane ustawienia żądania":::

1. Ustaw **, jeśli nie wykonano żadnej akcji, przechodź do innej osoby zatwierdzającej?** Przełącz na **wartość tak**.

1. Kliknij przycisk **Dodaj alternatywne osoby zatwierdzające** i wybierz z listy alternatywne osoby zatwierdzające.

    ![Dostęp do pakietu — zasady Dodaj alternatywne osoby zatwierdzające](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Jeśli wybierzesz pozycję Menedżer jako osoba zatwierdzająca dla pierwszej osoby zatwierdzającej, będzie dostępna dodatkowa opcja, **Menedżer drugiego poziomu jako alternatywna osoba zatwierdzająca**, dostępną do wyboru w polu alternatywna osoba zatwierdzająca. W przypadku wybrania tej opcji należy dodać osobę zatwierdzającą rezerwę do przesyłania żądania do programu w przypadku, gdy system nie może odnaleźć Menedżera drugiego poziomu.

1. W polu **Przekaż do alternatywnych osób zatwierdzających po** liczbie dni wprowadź liczbę dni, przez które osoby zatwierdzające muszą zatwierdzić lub odrzucić żądanie. Jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania przed upływem czasu żądania, żądanie wygasa (timeout), a użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu. 

    Żądania mogą być przekazywane do alternatywnych osób zatwierdzających dziennie po upływie czasu trwania żądania, a decyzja głównych osób zatwierdzających musi przekroczyć limit czasu po upływie co najmniej 4 dni. Jeśli limit czasu żądania jest mniejszy lub równy 3, nie jest wystarczająco dużo czasu na przesłanie żądania do alternatywnej osoby zatwierdzającej. W tym przykładzie czas trwania żądania wynosi 14 dni. W związku z tym czas trwania żądania osiągnie połowę okresu ważności w dniu 7. Dlatego nie można przesłać dalej żądania przed dniem 8. Ponadto żądania nie mogą być przekazywane w ostatnim dniu czasu trwania żądania. W tym przykładzie najnowsze żądanie może być przekazywane z dniem 13.

## <a name="enable-requests"></a>Włącz żądania

1. Jeśli chcesz, aby pakiet dostępu był natychmiast dostępny dla użytkowników w zasadach żądania, przesuń przełącznik Enable na **wartość tak**.

    Zawsze możesz włączyć ją w przyszłości po zakończeniu tworzenia pakietu dostępu.

    W przypadku wybrania opcji **Brak (tylko przypisania bezpośrednie do administratorów)** i skonfigurowania opcji Włącz na **nie** Administratorzy nie mogą bezpośrednio przypisywać tego pakietu dostępu.

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Kliknij przycisk **Dalej**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Zbierz dodatkowe informacje o żądającym zatwierdzenia (wersja zapoznawcza)

Aby upewnić się, że użytkownicy uzyskują dostęp do odpowiednich pakietów dostępu, można wymagać od osoby żądającej odpowiedzi na niestandardowe pole tekstowe lub wiele pytań w momencie żądania. Istnieje limit 20 pytań na zasady oraz limit 25 odpowiedzi na wiele pytań. Pytania będą następnie widoczne dla osób zatwierdzających, aby ułatwić im podejmowanie decyzji.

1. Przejdź do karty **Informacje o żądającym** i kliknij kartę **pytania** podrzędne.
 
1. Wpisz tekst, w którym chcesz zadać żądanie, znanego również jako ciąg wyświetlania, dla pytania w polu **pytania** .

    ![Dostęp — ustawienie informacji na temat żądania włączenia zasad](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Jeśli społeczność użytkowników, którzy będą potrzebować dostępu do pakietu dostępu, nie ma wspólnego preferowanego języka, możesz ulepszyć środowisko dla użytkowników żądających dostępu do usługi myaccess.microsoft.com. Aby ulepszyć środowisko pracy, możesz zapewnić alternatywne ciągi wyświetlania dla różnych języków. Na przykład jeśli w przeglądarce sieci Web użytkownika jest ustawiona wartość hiszpański, a w polu zainstalowano hiszpańskie ciągi wyświetlania, te ciągi będą wyświetlane dla żądającego użytkownika. Aby skonfigurować lokalizację dla żądań, kliknij pozycję **Dodaj lokalizację**.
    1. W okienku **Dodaj lokalizacje dla pytania** wybierz **kod języka** dla języka, w którym ma zostać zlokalizowane pytanie.
    1. W skonfigurowanym języku wpisz pytanie w **zlokalizowanym polu tekstowym** .
    1. Po dodaniu wszystkich wymaganych lokalizacji kliknij przycisk **Zapisz**.

    ![Dostęp do pakietu — zasady — Konfigurowanie zlokalizowanego tekstu](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Wybierz **format odpowiedzi** , w którym chcesz odpowiedzieć żądającym. Formaty odpowiedzi obejmują: *krótki tekst*, *wielokrotne wybór* i *długi tekst*.
 
    ![Dostęp do pakietu — zasady — wybierz pozycję Wyświetl i Edytuj format odpowiedzi z wieloma opcjami](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. W przypadku wybrania wielu opcji kliknij przycisk **Wyświetl i edytuj** , aby skonfigurować opcje odpowiedzi.
    1. Po wybraniu opcji Wyświetl i edytuj okienko **pytania Wyświetl/Edytuj** zostanie otwarte.
    1. Wpisz opcje odpowiedzi, które chcesz nadać żądającemu w odpowiedzi na pytanie w polach **wartości odpowiedzi** .
    1. Wpisz tyle odpowiedzi, ile potrzebujesz, a następnie kliknij przycisk **Zapisz**.
    
    ![Dostęp do pakietu — zasady — wprowadzanie wielu opcji wyboru](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Aby wymagać od zleceniodawców odpowiedzi na to pytanie podczas żądania dostępu do pakietu dostępu, kliknij pole wyboru w obszarze **wymagane**.

1. Wypełnij pozostałe karty (np. cykl życia) zależnie od potrzeb.

Po skonfigurowaniu informacji o żądającym w zasadach pakietu dostępu program może wyświetlić odpowiedzi na pytania dotyczące osoby żądającej. Aby uzyskać wskazówki dotyczące wyświetlania informacji o żądającym, zobacz [Wyświetlanie odpowiedzi żądającej na pytania (wersja zapoznawcza)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Następne kroki
- [Zmień ustawienia cyklu życia pakietu dostępu](entitlement-management-access-package-lifecycle-policy.md)
- [Wyświetl żądania dla pakietu dostępu](entitlement-management-access-package-requests.md)
