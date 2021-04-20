---
title: Administracja przy użyciu witryny Azure EA Portal
description: W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 787f902311437e6ca8a679299ef7a28b34cee4ab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728414"
---
# <a name="azure-ea-portal-administration"></a>Administracja przy użyciu witryny Azure EA Portal

W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal (https://ea.azure.com). Witryna Azure EA Portal to portal zarządzania online, który ułatwia klientom zarządzanie kosztami usług umowy EA na platformie Azure. Aby uzyskać informacje wprowadzające dotyczące witryny Azure EA Portal, zobacz artykuł [Rozpoczynanie pracy w witrynie Azure EA Portal](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Aktywowanie rejestracji

W celu aktywowania usługi początkowy administrator przedsiębiorstwa otwiera witrynę [Azure Enterprise Portal](https://ea.azure.com) i loguje się przy użyciu adresu e-mail z wiadomości e-mail z zaproszeniem.

Jeśli skonfigurowano Cię jako administratora przedsiębiorstwa, nie musisz otrzymać wiadomości e-mail dotyczącej aktywacji. Przejdź do witryny [Azure Enterprise Portal](https://ea.azure.com) i zaloguj się przy użyciu konta szkolnego lub służbowego albo konta Microsoft (używając adresu e-mail i hasła).

Jeśli masz więcej niż jedną rejestrację, wybierz tę, którą chcesz aktywować. Domyślnie są wyświetlane tylko aktywne rejestracje. Aby wyświetlić historię rejestracji, wyczyść opcję **Aktywne** w prawym górnym rogu witryny Azure Enterprise Portal.

W obszarze **Rejestracja** wyświetlany jest stan **Aktywne**.

![Przykład pokazujący aktywną rejestrację](./media/ea-portal-administration/ea-enrollment-status.png)

Tylko istniejący administratorzy przedsiębiorstwa platformy Azure mogą tworzyć konta innych administratorów przedsiębiorstwa.

### <a name="create-another-enterprise-administrator"></a>Tworzenie innego administratora przedsiębiorstwa

W zależności od sytuacji użyj jednej z następujących opcji.

#### <a name="if-youre-already-an-enterprise-administrator"></a>Jeśli jesteś już administratorem przedsiębiorstwa

1. Zaloguj się w witrynie [Azure Enterprise Portal](https://ea.azure.com).
1. Wybierz pozycję **Zarządzaj** > **Szczegóły rejestracji**.
1. Wybierz pozycję **+ Dodaj administratora** w prawym górnym rogu.

Upewnij się, że znasz adres e-mail użytkownika i preferowaną metodę uwierzytelniania, na przykład konto szkolne lub służbowe albo konto Microsoft.

#### <a name="if-youre-not-an-enterprise-administrator"></a>Jeśli nie jesteś administratorem przedsiębiorstwa

Jeśli nie jesteś administratorem przedsiębiorstwa, skontaktuj się z administratorem przedsiębiorstwa, aby poprosić o dodanie Cię do rejestracji. Administrator przedsiębiorstwa używa powyższych kroków, aby dodać Cię jako administratora przedsiębiorstwa. Po dodaniu do rejestracji otrzymasz wiadomość e-mail dotyczącą aktywacji.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>Jeśli administrator przedsiębiorstwa nie może Ci pomóc

Jeśli administrator przedsiębiorstwa nie może Ci pomóc, utwórz [wniosek o pomoc techniczną w witrynie Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Podaj następujące informacje:

- Numer rejestracji
- Adres e-mail do dodania i typ uwierzytelnienia (konto służbowe lub konto Microsoft)
- Wiadomość e-mail z zatwierdzeniem od istniejącego administratora przedsiębiorstwa
  - Jeśli istniejący administrator przedsiębiorstwa nie jest dostępny, skontaktuj się z partnerem lub doradcą ds. oprogramowania, aby zażądać zmiany danych kontaktowych za pomocą narzędzia Volume Licensing Service Center (VLSC).

## <a name="create-an-azure-enterprise-department"></a>Tworzenie działu w witrynie Azure Enterprise

Administratorzy przedsiębiorstwa i administratorzy działów wykorzystują działy do organizowania oraz tworzenia raportów dla przedsiębiorstwa dotyczących usług platformy Azure i użycia wg działów oraz centrów kosztów. Administrator przedsiębiorstwa może wykonywać następujące czynności:

- Dodawanie lub usuwanie działów.
- Kojarzenie konta z działem.
- Tworzenie administratorów działów.
- Zezwalanie administratorom działów na wyświetlanie cen i kosztów.

Administrator działu może dodawać nowe konta do swoich działów. Może on usuwać konta ze swoich działów, ale nie z rejestracji.

Aby dodać dział:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W lewym okienku wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Dział**, a następnie wybierz pozycję **+ Dodaj dział**.
1. Wprowadź informacje.
   Nazwa działu jest jedynym wymaganym polem. Musi zawierać co najmniej trzy znaki.
1. Po zakończeniu wybierz pozycję **Dodaj**.

## <a name="add-a-department-administrator"></a>Dodawanie administratora działu

Po utworzeniu działu administrator przedsiębiorstwa może dodawać administratorów działów i kojarzyć poszczególnych z nich z działami. Administratorzy działów mogą wykonywać następujące czynności w swoich działach:

- Tworzenie innych administratorów działów
- Wyświetlanie i edytowanie właściwości działu, takich jak nazwa lub centrum kosztów
- Dodawanie kont
- Usuwanie kont
- Pobieranie szczegółowego zestawienia użycia
- Wyświetlanie użycia i opłat w danym miesiącu<sup>1</sup>

> <sup>1</sup> Administrator przedsiębiorstwa musi przyznać odpowiednie uprawnienia. Jeśli masz uprawnienia do wyświetlania miesięcznego użycia i opłat dla działu, ale nie widzisz ich, skontaktuj się z partnerem.

### <a name="to-add-a-department-administrator"></a>Aby dodać administratora działu

Jako administrator przedsiębiorstwa:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W lewym okienku wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Dział**, a następnie wybierz dział.
1. Wybierz pozycję **+ Dodaj administratora** i dodaj wymagane informacje.
1. W przypadku dostępu tylko do odczytu dla opcji **Tylko do odczytu** ustaw wartość **Tak**, a następnie wybierz przycisk **Dodaj**.

![Przykładowe okno dialogowe Dodawanie administratora działu](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Aby ustawić dostęp tylko do odczytu

Administratorom działów można przyznać dostęp tylko do odczytu.

- Tworząc konto administratora działu, ustaw wartość **Tak** dla opcji tylko do odczytu.

- Aby edytować istniejącego administratora działu:
   1. Wybierz dział, a następnie wybierz symbol ołówka obok **administratora działu**, którego chcesz edytować.
   1. Dla opcji tylko do odczytu ustaw wartość **Tak**, a następnie wybierz pozycję **Zapisz**.

Administratorzy przedsiębiorstwa automatycznie uzyskują uprawnienia administratora działu.

## <a name="add-an-account"></a>Dodaj konto

Struktura kont i subskrypcji ma wpływ na sposób administrowania nimi oraz sposób ich wyświetlania na fakturach i w raportach. Przykładowe typowe struktury organizacyjne to piony biznesowe, zespoły funkcjonalne i lokalizacje geograficzne.

Aby dodać konto:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej stronie.
1. Wybierz kartę **Konto**. Na stronie **Konto** wybierz pozycję **+Dodaj konto**.
1. Wybierz dział lub pozostaw konto jako nieprzypisane, a następnie wybierz żądany typ uwierzytelniania.
1. Wpisz przyjazną nazwę, która będzie używana do identyfikowania konta w raportach.
1. W polu **Adres e-mail właściciela konta** wpisz adres e-mail, który ma zostać skojarzony z nowym kontem.
1. Potwierdź adres e-mail, a następnie wybierz pozycję **Dodaj**.

![Przykład przedstawiający listę kont i opcję +Dodaj konto](./media/ea-portal-administration/create-ea-add-an-account.png)

Możesz dodać kolejne konto, wybierając pozycję **Dodaj kolejne konto** lub pozycję **Dodaj** w prawym dolnym rogu lewego paska narzędzi.

Aby potwierdzić własność konta:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. Wyświetl stan.

   Stan powinien ulec zmianie z **Oczekujące** na **Data początkowa/końcowa**. Data początkowa/końcowa to data pierwszego logowania użytkownika i data zakończenia obowiązywania umowy.
1. Gdy zostanie wyświetlony komunikat **Ostrzeżenie**, właściciel konta musi wybrać przycisk **Kontynuuj**, aby aktywować konto podczas pierwszego logowania się w witrynie Azure Enterprise Portal.

## <a name="change-azure-subscription-or-account-ownership"></a>Zmienianie własności subskrypcji platformy Azure lub konta

Administratorzy przedsiębiorstwa mogą korzystać z witryny Azure Enterprise Portal do przenoszenia własności konta dla wybranej lub wszystkich subskrypcji w ramach rejestracji.

Po zakończeniu przenoszenia własności subskrypcji lub konta firma Microsoft aktualizuje właściciela konta.

Przed przeprowadzeniem przenoszenia własności należy zrozumieć następujące zasady kontroli dostępu oparte na rolach (RBAC) platformy Azure:

- W przypadku przenoszenia własności subskrypcji lub konta między dwoma identyfikatorami organizacyjnymi w tej samej dzierżawie zasady RBAC platformy Azure oraz istniejące role administratora i współadministratora usługi są zachowywane.
- Przenoszenie własności subskrypcji lub konta między dzierżawami powoduje utratę zasad RBAC platformy Azure oraz przypisań ról.
- Zasady i role administratorów nie są przenoszone między różnymi katalogami. Administratorzy usługi są aktualizowani do właściciela konta docelowego.
- Aby uniknąć utraty zasad RBAC i przypisań ról platformy Azure przy przenoszeniu subskrypcji między dzierżawami, należy się upewnić, że pole wyboru **Przenieś subskrypcje do dzierżawy usługi Azure AD odbiorcy** pozostanie **niezaznaczone**. Spowoduje to zachowanie usług, ról platformy Azure oraz zasad w bieżącej dzierżawie usługi Azure AD i przeniesienie tylko własności rozliczeń dla konta.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Obraz przedstawiający niezaznaczone pole wyboru do przenoszenia subskrypcji do dzierżawy usługi Azure AD" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Przed zmianą właściciela konta:

1. W witrynie Azure Enterprise Portal wyświetl kartę **Konto** i sprawdź konto źródłowe. Konto źródłowe musi być aktywne.
1. Zidentyfikuj konto docelowe i upewnij się, że jest ono aktywne.

Aby przenieść własność konta dla wszystkich subskrypcji:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
1. Wybierz ikonę zmiany właściciela konta po prawej stronie. Ikona przedstawia osobę.  
    ![Obraz przedstawiający symbol Zmiana właściciela konta](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Wybierz konto docelowe, na które chcesz wykonać przenoszenie, a następnie wybierz pozycję **Dalej**.
1. Jeśli chcesz przenieść własność konta między dzierżawami usługi Azure AD, zaznacz pole wyboru **Przenieś subskrypcje do dzierżawy usługi Azure odbiorcy** .  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Obraz przedstawiający zaznaczone pole wyboru do przenoszenia subskrypcji do dzierżawy usługi Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Potwierdź przeniesienie i wybierz pozycję **Prześlij**.

Aby przenieść własność konta dla jednej subskrypcji:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
1. Wybierz ikonę przenoszenia subskrypcji po prawej stronie. Ikona przedstawia stronę.  
    ![Obraz przedstawiający symbol Przenoszenie subskrypcji](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Wybierz konto docelowe, na które chcesz przenieść subskrypcję, a następnie wybierz pozycję **Dalej**.
1. Jeśli chcesz przenieść własność subskrypcji między dzierżawami usługi Azure AD, zaznacz pole wyboru **Przenieś subskrypcje do dzierżawy usługi Azure odbiorcy** .  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Obraz przedstawiający zaznaczone pole wyboru do przenoszenia subskrypcji do dzierżawy usługi Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Potwierdź przeniesienie, a następnie wybierz pozycję **Prześlij**.


## <a name="associate-an-account-to-a-department"></a>Kojarzenie konta z działem

Administratorzy przedsiębiorstwa mogą skojarzyć istniejące konta z działami w ramach rejestracji.

### <a name="to-associate-an-account-to-a-department"></a>Aby skojarzyć konto z działem

1. Zaloguj się do witryny Azure EA Portal jako administrator przedsiębiorstwa.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz pozycję **Dział**.
1. Umieść kursor na wierszu z kontem, a następnie wybierz ikonę ołówka po prawej stronie.
1. Wybierz dział z menu rozwijanego.
1. Wybierz pozycję **Zapisz**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Kojarzenie istniejącego konta z subskrypcją rozliczaną zgodnie z rzeczywistym użyciem

Jeśli masz już konto platformy Microsoft Azure w witrynie Azure Portal, wprowadź skojarzone konto Microsoft lub konto służbowe, aby skojarzyć je z rejestracją umowy Enterprise Agreement.

### <a name="associate-an-existing-account"></a>Kojarzenie istniejącego konta

1. W witrynie Azure Enterprise Portal wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Konto**.
1. Wybierz pozycję **+Dodaj konto**.
1. Wprowadź konto służbowe lub konto Microsoft skojarzone z istniejącym kontem platformy Azure.
1. Upewnij się, że konto to zostało skojarzone z istniejącym kontem platformy Azure.
1. Podaj nazwę, której chcesz używać do identyfikowania tego konta na potrzeby raportowania.
1. Wybierz pozycję **Dodaj**.
1. Możesz dodać kolejne konto, wybierając ponownie opcję **+Dodaj konto**, lub wrócić do strony głównej, wybierając przycisk **Administrator**.
1. Jeśli wyświetlisz stronę **Konto**, nowo dodane konto zostanie wyświetlone ze stanem **Oczekiwanie**.

### <a name="confirm-account-ownership"></a>Potwierdzanie własności konta

1. Zaloguj się na konto e-mail skojarzone z podanym kontem służbowym lub kontem Microsoft.
1. Otwórz powiadomienie e-mail zatytułowane _„Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing”_ (Zaproszenie do aktywacji konta w usłudze Microsoft Azure w ramach licencjonowania zbiorowego firmy Microsoft).
1. Wybierz link **Log into the Microsoft Azure Enterprise Portal** (Zaloguj się do witryny Microsoft Azure Enterprise Portal) w zaproszeniu.
1. Wybierz pozycję **Zaloguj się**.
1. Wprowadź konto Microsoft lub konto służbowe i hasło, aby się zalogować i potwierdzić własność konta.

### <a name="azure-marketplace"></a>Azure Marketplace

Chociaż większość subskrypcji można konwertować ze środowiska płatności naliczanych zgodnie z rzeczywistym użyciem do umowy Enterprise Agreement platformy Azure, nie dotyczy to usług witryny Azure Marketplace. Aby móc korzystać z pojedynczego widoku wszystkich subskrypcji i opłat, zalecamy dodanie usług witryny Azure Marketplace do witryny Azure Enterprise Portal.

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz kartę **Rejestracja**.
1. Wyświetl sekcję **Szczegóły rejestracji**.
1. Na prawo od pola Azure Marketplace wybierz ikonę ołówka, aby włączyć opcję. Wybierz pozycję **Zapisz**.

Właściciel konta może teraz kupić dowolne usługi z witryny Azure Marketplace, do których wykupiono dostęp w ramach subskrypcji rozliczanej zgodnie z rzeczywistym użyciem.

Po aktywowaniu nowych subskrypcji witryny Azure Marketplace w ramach rejestracji EA platformy Azure anuluj usługi witryny Azure Marketplace utworzone w środowisku płatności naliczanych zgodnie z rzeczywistym użyciem. Ten krok ma krytyczne znaczenie, aby subskrypcje witryny Azure Marketplace nie przeszły w nieprawidłowy stan, gdy wygaśnie instrument płatniczy środowiska płatności naliczanych zgodnie z rzeczywistym użyciem.

### <a name="msdn"></a>MSDN

Subskrypcje MSDN są automatycznie konwertowane na ofertę Tworzenie i testowanie MSDN, a oferta EA platformy Azure utraci wszystkie istniejące środki pieniężne.

### <a name="azure-in-open"></a>Licencja Azure w ramach programu licencjonowania Open

Skojarzenie subskrypcji licencji Azure w ramach programu licencjonowania Open z umową Enterprise Agreement powoduje utratę wszystkich niewykorzystanych środków licencji Azure w ramach programu licencjonowania Open. Z tego względu zaleca się, aby klienci wykorzystali wszystkie środki w subskrypcji licencji Azure w ramach programu licencjonowania Open przed dodaniem konta do umowy Enterprise Agreement.  

### <a name="accounts-with-support-subscriptions"></a>Konta z subskrypcjami pomocy technicznej

Jeśli dodasz istniejące konto z subskrypcją pomocy technicznej do witryny Azure Enterprise Portal, a Twoja umowa Enterprise Agreement nie obejmuje subskrypcji pomocy technicznej, subskrypcja pomocy technicznej MOSA nie zostanie przeniesiona automatycznie. W okresie prolongaty — czyli do końca następnego miesiąca — trzeba będzie ponownie zakupić subskrypcję pomocy technicznej w ramach umowy EA platformy Azure.

## <a name="department-spending-quotas"></a>Limity przydziału wydatków dla działu

Klienci z umowami EA mogą ustawiać lub zmieniać limity przydziałów wydatków dla każdego działu w ramach rejestracji. Kwota przydziału wydatków jest ustawiana dla bieżącego okresu przedpłaty. Na koniec bieżącego okresu przedpłaty system przedłuży istniejący limit przydziału wydatków na następny okres przedpłaty, chyba że wartości zostaną zaktualizowane.

Administrator działu może wyświetlić limit przydziału wydatków, ale tylko administrator przedsiębiorstwa może zaktualizować wartość limitu przydziału. Administrator przedsiębiorstwa i administrator działu otrzymają powiadomienia, gdy limit przydziału zostanie wykorzystany w 50%, 75%, 90%, i 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Ustawianie limitu przydziału przez administratora przedsiębiorstwa:

 1. Otwórz witrynę Azure EA Portal.
 1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
 1. Wybierz kartę **Dział**.
 1. Wybierz dział.
 1. Wybierz symbol ołówka w sekcji Szczegóły działu lub wybierz symbol **+ Dodaj dział**, aby dodać limit przydziału wydatków wraz z nowym działem.
 1. W obszarze Szczegóły działu wprowadź kwotę limitu przydziału wydatków w walucie rejestracji w polu Limit przydziału wydatków $ (wartość musi być większa niż 0).
    - W tym miejscu można także edytować nazwę działu i centrum kosztów.
 1. Wybierz pozycję **Zapisz**.

Limit przydziału wydatków działu będzie teraz widoczny w widoku Lista działów na karcie Dział. Na koniec bieżącej przedpłaty witryna Azure EA Portal zachowa limity przydziału wydatków na następny okres przedpłaty.

Kwota limitu przydziału dla działu jest niezależna od bieżącej przedpłaty za platformę Azure. Ponadto kwota limitu przydziału i alerty są stosowane tylko do użycia tej samej firmy. Limit przydziału wydatków dla działu jest przeznaczony wyłącznie do celów informacyjnych i nie wymusza limitów wydatków.

### <a name="department-administrator-to-view-the-quota"></a>Wyświetlanie limitu przydziału przez administratora działu:

1. Otwórz witrynę Azure EA Portal.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz kartę **Dział** i wyświetl widok Lista działów z limitami przydziałów wydatków.

Jeśli jesteś klientem pośrednim, funkcje kosztów muszą zostać włączone przez partnera handlowego.

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

Witryna Azure EA Portal ułatwia administrowanie kosztami i użyciem dla umowy EA platformy Azure. W witrynie Azure EA Portal istnieją trzy główne role:

- Administrator umowy EA
- Administrator działu
- Właściciel konta

Każda rola ma inny poziom dostępu i uprawnień.

Aby uzyskać więcej informacji na temat ról użytkownika, zobacz [Role użytkownika przedsiębiorstwa](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Dodawanie konta usługi Azure EA

Konto Azure EA to jednostka organizacyjna w witrynie Azure EA Portal. Służy ono do administrowania subskrypcjami i tworzenia raportów. Aby uzyskać dostęp do usług platformy Azure i korzystać z nich, musisz utworzyć konto lub poprosić o jego utworzenie.

Aby uzyskać więcej informacji na temat kont platformy Azure, zobacz [Dodawanie konta](#add-an-account).

## <a name="enterprise-devtest-offer"></a>Oferta Enterprise — tworzenie i testowanie

Jako administrator przedsiębiorstwa platformy Azure możesz umożliwiać właścicielom kont w organizacji tworzenie subskrypcji na podstawie oferty Tworzenie i testowanie w ramach umowy EA. Aby to zrobić, wybierz pole Tworzenie i testowanie dla właściciela konta w witrynie Azure EA Portal.

Po zaznaczeniu pola Tworzenie i testowanie poinformuj właściciela konta, aby umożliwić mu skonfigurowanie subskrypcji w ramach umowy EA Tworzenie i testowanie dla zespołów subskrybentów tworzenia i testowania.

Ta oferta umożliwia aktywnym subskrybentom programu Visual Studio uruchamianie obciążeń programistycznych i testowych na platformie Azure przy użyciu specjalnych stawek na potrzeby tworzenia i testowania. Zapewnia ona dostęp do pełnej galerii obrazów tworzenia i testowania, w tym systemów Windows 8.1 i Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Aby skonfigurować ofertę Enterprise — tworzenie i testowanie:

1. Zaloguj się jako administrator przedsiębiorstwa.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz kartę **Konto**.
1. Wybierz wiersz dla konta, dla którego chcesz włączyć dostęp do tworzenia i testowania.
1. Wybierz symbol ołówka po prawej stronie wiersza.
1. Zaznacz pole wyboru Tworzenie i testowanie.
1. Wybierz pozycję **Zapisz**.

Kiedy użytkownik zostanie dodany jako właściciel konta za pośrednictwem witryny Azure EA Portal, wszystkie subskrypcje platformy Azure skojarzone z właścicielem konta, które są oparte na ofercie Tworzenie i testowanie w ramach płatności zgodnie z rzeczywistym użyciem lub w ramach ofert środków miesięcznych dla subskrybentów programu Visual Studio zostaną przekonwertowane na ofertę Tworzenie i testowanie w ramach umowy EA. Subskrypcje oparte na innych typach ofert, takich jak Płatność zgodnie z rzeczywistym użyciem, skojarzone z właścicielem konta, zostaną przekonwertowane na oferty Microsoft Azure Enterprise.

Obecnie oferta Tworzenie i testowanie nie ma zastosowania do klientów platformy Azure dla instytucji rządowych.

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Właściciele kont mogą wyświetlać subskrypcje i zarządzać nimi. Za pomocą subskrypcji można udzielać zespołom w organizacji dostępu do środowisk deweloperskich i projektów. Mogą to być środowiska testowe, produkcyjne, deweloperskie i przejściowe.

Utworzenie różnych subskrypcji dla każdego środowiska aplikacji pomaga zabezpieczyć każde środowisko.

- Do każdej subskrypcji można także przypisać inne konto administratora usługi.
- Subskrypcje można kojarzyć z dowolną liczbą usług.
- Właściciel konta tworzy subskrypcje i przypisuje konto administratora usługi do każdej subskrypcji na swoim koncie.

### <a name="add-a-subscription"></a>Dodawanie subskrypcji

Aby dodać subskrypcję, skorzystaj z poniższych informacji.

Przy pierwszym dodawaniu subskrypcji do konta zostanie wyświetlona prośba o zaakceptowanie umowy subskrypcyjnej dotyczącej usług online firmy Microsoft (MOSA, Microsoft Online Subscription Agreement) oraz planu taryfowego. Umowa MOSA i plan taryfowy nie mają zastosowania do klientów z umową Enterprise Agreement, ale są potrzebne do utworzenia subskrypcji. Poprawka do rejestracji umowy Microsoft Azure Enterprise zastępuje powyższe elementy, a Twoja relacja umowna nie ulega zmianie. Po wyświetleniu monitu zaznacz pole informujące o zaakceptowaniu warunków.

_Microsoft Azure Enterprise_ jest domyślną nazwą tworzonej subskrypcji. Nazwę tę możesz zmienić w celu odróżnienia jej od innych subskrypcji w ramach rejestracji oraz aby zapewnić jej rozpoznawalność w raportach na poziomie przedsiębiorstwa.

Aby dodać subskrypcję:

1. Zaloguj się do konta w witrynie Azure Enterprise Portal.
1. Wybierz kartę **Administrator**, a następnie wybierz pozycję **Subskrypcja** w górnej części strony.
1. Sprawdź fakt zalogowania jako właściciel konta.
1. Wybierz pozycję **+Dodaj subskrypcję**, a następnie pozycję **Kup**.

   Przy pierwszym dodawaniu subskrypcji do konta musisz podać swoje informacje kontaktowe. Podczas dodawania kolejnych subskrypcji Twoje informacje kontaktowe będą dodawane automatycznie.

1. Wybierz pozycję **Subskrypcje**, a następnie wybierz utworzoną subskrypcję.
1. Wybierz pozycję **Edytuj szczegóły subskrypcji**.
1. Zaktualizuj pola **Nazwa subskrypcji** i **Administrator usługi**, a następnie wybierz znacznik wyboru.

   Wprowadzona nazwa subskrypcji będzie wyświetlana w raportach. Jest to nazwa projektu skojarzonego z subskrypcją w portalu deweloperskim.

Nowe subskrypcje mogą pojawić się na liście subskrypcji po upływie do 24 godzin. Po utworzeniu subskrypcji można wykonywać następujące czynności:

- [Edytowanie szczegółów subskrypcji ](https://portal.azure.com)
- [Zarządzanie usługami subskrypcji](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Usuwanie subskrypcji

Aby usunąć subskrypcję, w której jesteś właścicielem konta:

1. Zaloguj się do witryny Azure Portal przy użyciu poświadczeń skojarzonych z Twoim kontem.
1. W menu Centrum wybierz pozycję **Subskrypcje**.
1. Na karcie subskrypcji w lewym górnym rogu strony wybierz subskrypcję, którą chcesz anulować, a następnie wybierz pozycję **Anuluj subskrypcję**, aby otworzyć kartę anulowania.
1. Wprowadź nazwę subskrypcji i wybierz przyczynę anulowania, a następnie wybierz pozycję **Anuluj subskrypcję**.

Tylko administratorzy kont mogą anulować subskrypcje.

Aby uzyskać więcej informacji, zobacz [Co stanie się, gdy anuluję moją subskrypcję?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-account"></a>Usuwanie konta

Usunięcie konta można wykonać tylko dla aktywnych kont bez aktywnych subskrypcji.

1. W witrynie Enterprise Portal wybierz pozycję **Zarządzanie** w obszarze nawigacji po lewej stronie.
1. Wybierz kartę **Konto**.
1. W tabeli Konta wybierz konto, które chcesz usunąć.
1. Wybierz ikonę X znajdującą się po prawej stronie wiersza Konta.
1. Jeśli na koncie nie ma aktywnych subskrypcji, wybierz pozycję **Tak** w wierszu Konto, aby potwierdzić usunięcie konta.

## <a name="update-notification-settings"></a>Aktualizowanie ustawień powiadomień

Administratorzy przedsiębiorstwa są rejestrowani automatycznie w celu otrzymywania powiadomień dotyczących użycia skojarzonych z ich rejestracją. Każdy administrator przedsiębiorstwa może zmienić interwał poszczególnych powiadomień lub może je całkowicie wyłączyć.

Kontakty dla powiadomień są wyświetlane w witrynie Azure EA Portal w sekcji **Kontakt dla powiadomień**. Zarządzanie kontaktami dla powiadomień zapewnia, że odpowiednie osoby w organizacji otrzymują powiadomienia portalu Azure EA.

Aby wyświetlić bieżące ustawienia powiadomień:

1. W witrynie Azure EA Portal przejdź do okna **Zarządzanie** > **Kontakt dla powiadomień**.
2. Adres e-mail — adres e-mail skojarzony z kontem Microsoft lub kontem służbowym administratora przedsiębiorstwa, który otrzymuje powiadomienia.
3. Częstotliwość powiadamiania o nierozliczonym saldzie — ustawiona częstotliwość, z jaką powiadomienia są wysyłane do każdego administratora przedsiębiorstwa.

Aby dodać kontakt:

1. Wybierz pozycję **+Dodaj kontakt**.
2. Wprowadź adres e-mail, a następnie potwierdź go.
3. Wybierz pozycję **Zapisz**.

Nowy kontakt dla powiadomień zostanie wyświetlony w sekcji **Kontakt dla powiadomień**. Aby zmienić częstotliwość powiadamiania, wybierz kontakt dla powiadomień i wybierz symbol ołówka po prawej stronie wybranego wiersza. Ustaw częstotliwość na **codziennie**, **co tydzień**, **co miesiąc** lub **brak**.

Możesz pominąć powiadomienia dotyczące cyklu życia ze _zbliżającą się datą zakończenia okresu pokrycia_ oraz _ze zbliżającą się datą wyłączenia i anulowania aprowizacji_. Wyłączenie powiadomień dotyczących cyklu życia powoduje pomijanie powiadomień o okresie pokrycia i dacie zakończenia obowiązywania umowy.

## <a name="azure-sponsorship-offer"></a>Oferta dostępu sponsorowanego Azure

Oferta dostępu sponsorowanego Azure to ograniczone, sponsorowane konto platformy Microsoft Azure. Jest ono dostępne tylko na zaproszenie e-mail dla ograniczonej liczby klientów wybranych przez firmę Microsoft. Jeśli masz upoważnienie do oferty dostępu sponsorowanego Microsoft Azure, otrzymasz zaproszenie e-mail dla Twojego identyfikatora konta.

Aby uzyskać więcej informacji, utwórz [wniosek o pomoc techniczną](https://aka.ms/azrsponsorship) dotyczący aktywacji sponsorowania.

## <a name="conversion-to-work-or-school-account-authentication"></a>Konwersja na uwierzytelnianie konta służbowego

Użytkownicy z umową Azure Enterprise mogą konwertować typ uwierzytelniania z konta Microsoft (MSA lub Live ID) na konto służbowe (które używa usługi Azure Active Directory).

Aby rozpocząć:

1. Dodaj konto służbowe do witryny Azure EA Portal w wymaganych rolach.
1. W przypadku wystąpienia błędów konto może być nieprawidłowe w usłudze Active Directory.  Platforma Azure używa głównej nazwy użytkownika (UPN), która nie zawsze jest identyczna z adresem e-mail.
1. Uwierzytelnij się w witrynie Azure EA Portal za pomocą konta służbowego.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Aby przekonwertować subskrypcje z kont Microsoft na konta służbowe:

1. Zaloguj się do portalu zarządzania przy użyciu konta Microsoft, do którego należą subskrypcje.
1. Użyj transferu własności konta, aby przejść do nowego konta.
1. Teraz konto Microsoft powinno być wolne od aktywnych subskrypcji i można je usunąć.
1. Wszystkie usunięte konta pozostaną widoczne w portalu w stanie nieaktywnym z powodów związanych z rozliczeniami historycznymi.  Możesz odfiltrować je z widoku, zaznaczając pole wyboru, aby wyświetlić tylko aktywne konta.

## <a name="azure-ea-term-glossary"></a>Słownik terminów umowy EA platformy Azure

- **Konto**: Jednostka organizacyjna w witrynie Azure Enterprise Portal. Służy ona do administrowania subskrypcjami i tworzenia raportów.
- **Właściciel konta**: Osoba, która zarządza subskrypcjami i administratorami usług na platformie Azure. Może wyświetlać dane użycia na tym koncie i w skojarzonych z nim subskrypcjach.
- **Aneks subskrypcji**: Jednoroczna lub współbieżna subskrypcja w ramach poprawki do rejestracji.
- **Przedpłata**: Przedpłata rocznej kwoty pieniężnej dla usług platformy Azure z obniżoną stawką przedpłaty za korzystanie z tej przedpłaty.
- **Administrator działu**: Osoba, która zarządza działem, tworzy nowe konta i właścicieli kont, wyświetla szczegóły użycia dla zarządzanych działów i może wyświetlać koszty po udzieleniu uprawnień.
- **Numer rejestracji**: Unikatowy identyfikator przydzielany przez firmę Microsoft w celu identyfikowania konkretnej rejestracji skojarzonej z umową Enterprise Agreement.
- **Administratora przedsiębiorstwa**: Osoba, która zarządza działami, właścicielami działów, kontami i właścicielami kont na platformie Azure. Ma możliwość zarządzania administratorami przedsiębiorstwa, a także wyświetlania danych użycia, rozliczonych ilości i nierozliczonych opłat na wszystkich kontach i we wszystkich subskrypcjach skojarzonych z rejestracją przedsiębiorstwa.
- **Umowa Enterprise Agreement**: Umowa licencyjna firmy Microsoft dla klientów ze scentralizowanymi zakupami, którzy chcą ustandaryzować całą swoją organizację na technologii firmy Microsoft i utrzymać infrastrukturę technologii informatycznej na standardzie oprogramowania firmy Microsoft.
- **Rejestracja umów Enterprise Agreement**: Rejestracja w programie Enterprise Agreement zapewniającym produkty firmy Microsoft w obniżonych cenach.
- **Konto Microsoft**: Usługa internetowa, która umożliwia stronom uczestniczącym uwierzytelnianie użytkownika za pomocą jednego zestawu poświadczeń.
- **Poprawka rejestracji umowy Microsoft Azure Enterprise (poprawka rejestracji)** : Poprawka podpisana przez przedsiębiorstwo, która zapewnia dostęp do platformy Azure w ramach rejestracji przedsiębiorstwa.
- **Azure Enterprise Portal**: Portal używany przez naszych klientów korporacyjnych do zarządzania kontami platformy Azure i powiązanymi subskrypcjami.
- **Ilość użytych zasobów**: Ilość pojedynczej usługi platformy Azure wykorzystana w danym miesiącu.
- **Administrator usługi**: Osoba, która uzyskuje dostęp do subskrypcji i projektów programistycznych oraz zarządza nimi w witrynie Azure Enterprise Portal.
- **Subskrypcja**: Reprezentuje subskrypcję w witrynie Azure Enterprise Portal i jest kontenerem usług platformy Azure zarządzanych przez tego samego administratora usługi.
- **Konto służbowe**: Dla organizacji, które skonfigurowały usługę Azure Active Directory z federacją w chmurze i mają wszystkie konta w jednej dzierżawie.

### <a name="enrollment-statuses"></a>Stany rejestracji

- **Nowa**: Ten stan jest przypisywany do rejestracji utworzonej w ciągu 24 godzin i zostanie zaktualizowany do stanu Oczekująca w ciągu 24 godzin.
- **Oczekująca**: Administrator rejestracji musi zalogować się w witrynie Azure Enterprise Portal. Po zalogowaniu rejestracja zostanie przełączona do stanu aktywnego.
- **Aktywna**: Rejestracja jest aktywna i można tworzyć konta i subskrypcje w witrynie Azure Enterprise Portal. Rejestracja pozostanie aktywna do daty zakończenia umowy Enterprise Agreement.
- **Przedłużenie na czas nieokreślony**: Przedłużenie na czas nieokreślony występuje po terminie zakończenia umowy Enterprise Agreement. Umożliwia to klientom z umowami EA platformy Azure, którzy wybrali opcję przedłużenia, dalsze korzystanie z usług platformy Azure przez czas nieokreślony po zakończeniu umowy Enterprise Agreement.

   Przed upływem terminu zakończenia umowy Enterprise Agreement związanej z rejestracją EA platformy Azure administrator rejestracji powinien zdecydować, która z poniższych opcji zostanie wybrana:

  - Odnowienie rejestracji przez dodanie kolejnej przedpłaty za platformę Azure.
  - Przeniesienie do nowej rejestracji.
  - Przeprowadzenie migracji do programu subskrypcji online firmy Microsoft (MOSP).
  - Potwierdzenie wyłączenia wszystkich usług skojarzonych z rejestracją.
- **Wygasła**: Klient z umową EA platformy Azure zrezygnował z przedłużenia umowy, a rejestracja EA platformy Azure osiągnęła datę zakończenia umowy Enterprise Agreement. Rejestracja wygaśnie, a wszystkie skojarzone usługi zostaną wyłączone.
- **Przeniesiona**: Rejestracje, dla których wszystkie konta i usługi zostały przeniesione do nowej rejestracji, będą wyświetlane ze stanem Przeniesiona.
  >[!NOTE]
  > Rejestracje nie są przenoszone automatycznie w przypadku wygenerowania nowego numeru rejestracji podczas odnawiania. Aby nastąpiło automatyczne przeniesienie, należy uwzględnić poprzedni numer rejestracji w dokumentacji dotyczącej odnawiania.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej o tym, jak [rezerwacje maszyn wirtualnych](ea-portal-vm-reservations.md) mogą pomóc Ci zaoszczędzić pieniądze.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
- Przeczytaj często [zadawane Cost Management + Billing odpowiedzi](../cost-management-billing-faq.yml) na pytania dotyczące własności subskrypcji EA.