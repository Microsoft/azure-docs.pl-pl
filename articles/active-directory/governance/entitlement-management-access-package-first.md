---
title: Samouczek — Tworzenie pakietu dostępu — Zarządzanie prawami w usłudze Azure AD
description: Samouczek krok po kroku dotyczący sposobu tworzenia pierwszego pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4030dedfb0d7308bb5a0f7a86bc49ee168e6d0c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362811"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Samouczek: Tworzenie pierwszego pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania

Zarządzanie dostępem do wszystkich zasobów wymaganych przez pracowników, takich jak grupy, aplikacje i lokacje, jest ważną funkcją dla organizacji. Aby udzielić pracownikom odpowiedniego poziomu dostępu, muszą one być produktywne i usuwać ich dostęp, gdy nie są już potrzebne.

W tym samouczku będziesz używać banku Woodgrove jako administrator IT. Zażądano utworzenia pakietu zasobów dla kampanii marketingowej, której mogą używać użytkownicy wewnętrzni do żądania samoobsługi. Żądania nie wymagają zatwierdzenia i dostęp użytkownika wygaśnie po upływie 30 dni. W tym samouczku zasoby kampanii marketingowej są tylko członkostwem w pojedynczej grupie, ale może to być Kolekcja grup, aplikacji lub witryn usługi SharePoint Online.

![Diagram przedstawiający Omówienie scenariusza.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie pakietu dostępu z grupą jako zasobem
> * Zezwalaj użytkownikowi w Twoim katalogu na żądanie dostępu
> * Pokazuje, w jaki sposób użytkownik wewnętrzny może zażądać pakietu dostępu

Aby zapoznać się z krok po kroku procesu wdrażania Azure Active Directory Zarządzanie prawami, w tym tworzenia pierwszego pakietu dostępu, zobacz następujące wideo:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

Możesz również programowo utworzyć pakiet dostępu przy użyciu Microsoft Graph. Samouczek pokazujący sposób programowego tworzenia pakietu dostępu można znaleźć w temacie [interfejs API zarządzania uprawnieniami](/graph/tutorial-access-package-api?view=graph-rest-beta).

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z usługi Azure AD do zarządzania prawami, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — wersja Premium P2
- Licencja na usługę Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Krok 1. Konfigurowanie użytkowników i grup

Katalog zasobów zawiera co najmniej jeden zasób do udostępnienia. W tym kroku utworzysz grupę o nazwie **zasoby marketingowe** w katalogu banku Woodgrove Bank, który jest zasobem docelowym dla zarządzania uprawnieniami. Należy również skonfigurować wewnętrzny Obiekt żądający.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

![Tworzenie użytkowników i grup](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny lub administrator użytkowników.  

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. Utwórz lub skonfiguruj następujących dwóch użytkowników. Możesz użyć tych nazw lub różnych nazw. **Admin1** może być zalogowany jako użytkownik.

    | Nazwa | Rola katalogu |
    | --- | --- |
    | **Admin1** | Administrator globalny<br/>-lub-<br/>Administrator użytkowników |
    | **Requestor1** | Użytkownik |

1. Utwórz grupę zabezpieczeń usługi Azure AD o nazwie **zasoby marketingowe** z **przypisanym**typem członkostwa.

    Ta grupa będzie zasobem docelowym dla zarządzania uprawnieniami. Grupa powinna być pusta elementów członkowskich do uruchomienia.

## <a name="step-2-create-an-access-package"></a>Krok 2. Tworzenie pakietu dostępu

*Pakiet dostępu* to zbiór zasobów, których potrzebuje zespół lub projekt i podlega zasadom. Pakiety dostępu są definiowane w kontenerach o nazwie *wykazów*. W tym kroku utworzysz pakiet dostępu do **kampanii marketingowej** w wykazie **ogólnym** .

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-first/elm-access-package.png)

1. W Azure Portal w lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

2. W menu po lewej stronie kliknij pozycję **Zarządzanie tożsamościami** .

3. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.  Jeśli zostanie wyświetlony komunikat **odmowa dostępu**, upewnij się, że w katalogu znajduje się licencja na Azure AD — wersja Premium P2.

4. Kliknij pozycję **nowy pakiet dostępu**.

    ![Zarządzanie prawami w Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

5. Na karcie **podstawowe** wpisz nazwę pakietu dostępu do **kampanii marketingowej** i opis **dostępu do zasobów dla kampanii**.

6. Pozostaw listę rozwijaną **wykazu** ustawioną na **Ogólne**.

    ![Nowy pakiet dostępu — karta podstawowe](./media/entitlement-management-access-package-first/basics.png)

7. Kliknij przycisk **dalej** , aby otworzyć kartę **role zasobów** .

    Na tej karcie należy wybrać zasoby i rolę zasobów do uwzględnienia w pakiecie dostępu.

8. Kliknij pozycję **grupy i zespoły**.

9. W okienku wybierz grupy Znajdź i wybierz utworzoną wcześniej grupę **zasobów marketingowych** .

     Domyślnie są wyświetlane grupy w katalogu ogólnym. Po wybraniu grupy poza katalogiem ogólnym, którą można zobaczyć po zaznaczeniu pola wyboru **Zobacz wszystkie** , zostanie ono dodane do wykazu ogólnego.

    ![Zrzut ekranu przedstawiający kartę "nowy pakiet dostępu — role zasobów" i okno "Wybieranie grup".](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Kliknij pozycję **Wybierz** , aby dodać grupę do listy.

11. Z listy rozwijanej **rola** wybierz **element członkowski**.

    ![Nowy pakiet dostępu — karta role zasobów](./media/entitlement-management-access-package-first/resource-roles.png)
    >[!IMPORTANT]
    >Grupy przypisane do ról dodane do pakietu dostępu będą wskazywane przy użyciu podtypu, który można **przypisać do ról**. Aby uzyskać więcej informacji na temat grup, które można przypisać do ról usługi Azure AD, zapoznaj się z tematem [Tworzenie grupy umożliwiającej przypisanie roli](../roles/groups-create-eligible.md) w Azure Active Directory. Jeśli nie widzisz grupy, którą można przypisać, którą chcesz dodać, lub nie możesz jej dodać, upewnij się, że masz wymaganą rolę usługi Azure AD i rolę zarządzania uprawnieniami, aby wykonać tę operację. Może być konieczne poproszenie kogoś o wymagane role Dodaj zasób do katalogu. Aby uzyskać więcej informacji, zobacz [wymagane role do dodawania zasobów do wykazu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

    >[!NOTE]
    > W przypadku korzystania z [grup dynamicznych](../enterprise-users/groups-create-rule.md) nie będą widoczne żadne inne role, które nie są dostępne poza właścicielem. Jest to celowe.
    > ![Omówienie scenariusza](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Kliknij przycisk **dalej** , aby otworzyć kartę **żądania** .

    Na tej karcie utworzysz zasady żądania. *Zasady* definiują reguły lub guardrails w celu uzyskania dostępu do pakietu dostępu. Tworzysz zasady, które umożliwiają określonemu użytkownikowi w katalogu zasobów zażądanie tego pakietu dostępu.

13. W sekcji **Użytkownicy, którzy mogą żądać dostępu** , kliknij pozycję **dla użytkowników w katalogu** , a następnie kliknij pozycję **określeni użytkownicy i grupy**.

    ![Nowy pakiet dostępu — karta żądania](./media/entitlement-management-access-package-first/requests.png)

14. Kliknij pozycję **Dodaj użytkowników i grupy**.

15. W okienku wybierz użytkowników i grupy wybierz utworzonego wcześniej użytkownika **Requestor1** .

    ![Nowy pakiet dostępu — karta żądania — Wybieranie użytkowników i grup](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Kliknij pozycję **Wybierz**.

17. Przewiń w dół do sekcji **zatwierdzenie** i **Włącz żądania** .

18. Pozostaw pole **Wymagaj zatwierdzenia** ustawione na wartość **nie**.

19. W przypadku **żądań włączania**kliknij przycisk **tak** , aby umożliwić żądanie tego pakietu dostępu zaraz po jego utworzeniu.

    ![Nowy pakiet dostępu — żąda zatwierdzenia karty i żądań włączenia](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Kliknij przycisk **dalej** , aby otworzyć kartę **cykl życia** .

21. W sekcji **wygaśnięcie** Ustaw **przypisania pakietów dostępu wygasnąć** do **liczby dni**.

22. Ustawianie **przydziałów wygasa po upływie** do **30** dni.

    ![Nowy pakiet dostępu — karta cykl życia](./media/entitlement-management-access-package-first/lifecycle.png)

23. Kliknij przycisk **dalej** , aby otworzyć kartę **Recenzja + tworzenie** .

    ![Nowy pakiet dostępu — karta przegląd + tworzenie](./media/entitlement-management-access-package-first/review-create.png)

    Po kilku chwilach powinien pojawić się powiadomienie, że pakiet dostępu został pomyślnie utworzony.

24. W menu po lewej stronie pakietu dostęp do kampanii marketingowej kliknij pozycję **Przegląd**.

25. Skopiuj **link portalu My Access**.

    Ten link zostanie użyty do następnego kroku.

    ![Przegląd pakietu dostępu — mój link portalu dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Krok 3. żądanie dostępu

W tym kroku wykonasz kroki jako **wewnętrzny Obiekt żądający** i zażądasz dostępu do pakietu dostępu. Osoby żądające przesyłają żądania przy użyciu witryny o nazwie Portal dostępu. Portal My Access umożliwia żądającym przesyłanie żądań dotyczących pakietów dostępu, zapoznaj się z pakietami dostępu, do których mają dostęp, i Wyświetl ich historię żądań.

**Rola wymagana wstępnie:** Wewnętrzny Obiekt żądający

1. Wyloguj się z Azure Portal.

1. W nowym oknie przeglądarki przejdź do linku do portalu My Access, który został skopiowany w poprzednim kroku.

1. Zaloguj się do portalu My Access jako **Requestor1**.

    Powinien zostać wyświetlony pakiet dostępu do **kampanii marketingowej** .

1. W razie potrzeby w kolumnie **Opis** kliknij strzałkę, aby wyświetlić szczegółowe informacje o pakiecie dostępu.

    ![Portal dostępu — dostęp — pakiety](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet.

1. Kliknij pozycję **Zażądaj dostępu** , aby otworzyć okienko żądania dostępu.

    ![Portal dostępu — dostęp do żądania](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. W polu **uzasadnienie biznesowe** wpisz uzasadnienie, w **którym pracujemy nad nową kampanią marketingową**.

    ![Portal dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Kliknij przycisk **Prześlij**.

1. W menu po lewej stronie kliknij pozycję **historia żądań** , aby sprawdzić, czy Twoje żądanie zostało przesłane.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Krok 4. Weryfikowanie, czy dostęp został przypisany

W tym kroku potwierdzisz, że **wewnętrzny Obiekt żądający** został przypisany do pakietu dostępu, a teraz jest członkiem grupy **zasobów marketingowych** .

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. Wyloguj się z portalu My Access.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako **admin1**.

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.

1. Znajdź i kliknij pakiet dostępu do **kampanii marketingowej** .

1. W menu po lewej stronie kliknij pozycję **żądania**.

    Należy zobaczyć Requestor1 i początkową zasadę ze stanem **dostarczone**.

1. Kliknij żądanie, aby wyświetlić szczegóły żądania.

    ![Dostęp do pakietu — szczegóły żądania](./media/entitlement-management-access-package-first/request-details.png)

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. Kliknij pozycję **grupy** , a następnie otwórz grupę **zasobów marketingowych** .

1. Kliknij pozycję **Członkowie**.

    Powinna zostać wyświetlona lista **Requestor1** jako element członkowski.

    ![Elementy członkowskie zasobów marketingowych](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Krok 5. Czyszczenie zasobów

W tym kroku usuniesz wprowadzone zmiany i usuniesz pakiet dostępu do **kampanii marketingowej** .

**Rola wymagana wstępnie:**  Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. Otwórz pakiet dostępu do **kampanii marketingowej** .

1. Kliknij pozycję **przypisania**.

1. W przypadku **Requestor1**kliknij przycisk wielokropka (**...**), a następnie kliknij przycisk **Usuń dostęp**. W wyświetlonym komunikacie kliknij przycisk **tak**.

    Po kilku chwilach stan zmieni się z dostarczone na wygasłe.

1. Kliknij pozycję **role zasobów**.

1. W przypadku **zasobów marketingowych**kliknij przycisk wielokropka (**...**), a następnie kliknij pozycję **Usuń rolę zasobu**. W wyświetlonym komunikacie kliknij przycisk **tak**.

1. Otwórz listę pakietów dostępu.

1. W obszarze **kampania marketingowa**kliknij przycisk wielokropka (**...**), a następnie kliknij przycisk **Usuń**. W wyświetlonym komunikacie kliknij przycisk **tak**.

1. W Azure Active Directory Usuń wszystkich utworzonych użytkowników, takich jak **Requestor1** i **admin1**.

1. Usuń grupę **zasobów marketingowych** .

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej o typowych krokach związanych z scenariuszem zarządzania prawami.
> [!div class="nextstepaction"]
> [Typowe scenariusze](entitlement-management-scenarios.md)