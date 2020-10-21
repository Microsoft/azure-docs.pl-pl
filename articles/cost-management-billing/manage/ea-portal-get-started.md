---
title: Wprowadzenie do witryny Azure Enterprise Portal
description: W tym artykule omówiono sposób korzystania z witryny Azure Enterprise Portal przez klientów mających umowę Enterprise Agreement platformy Azure (Azure EA).
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 617351d5d1bdf9b6528803552e2d34766e9ab217
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132656"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Wprowadzenie do witryny Azure Enterprise Portal

Ten artykuł ułatwia rozpoczęcie korzystania z witryny [Azure Enterprise Portal](https://ea.azure.com) bezpośrednim i pośrednim klientom, którzy mają umowę Enterprise Agreement platformy Azure (Azure EA). Artykuł zawiera podstawowe informacje na temat następujących zagadnień:

- Struktura witryny Azure Enterprise Portal.
- Role używane w witrynie Azure Enterprise Portal.
- Tworzenie subskrypcji.
- Analiza kosztów w witrynach Azure Enterprise Portal i Azure Portal.

Ten film przedstawia pełną sesję dołączania do witryny Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarchia witryny Azure Enterprise Portal

Hierarchia witryny Azure Enterprise Portal obejmuje następujące elementy:

- **Azure Enterprise Portal** — portal zarządzania online, który ułatwia zarządzanie kosztami usług w ramach umowy EA platformy Azure. Możesz:

  - Tworzyć hierarchię umowy EA platformy Azure, w tym działy, konta i subskrypcje.
  - Uzgadniać koszty używanych usług, pobierać raporty użycia i wyświetlać cenniki.
  - Tworzyć klucze interfejsu API dla rejestracji.

- **Działy** ułatwiają dzielenie kosztów na logiczne grupy. Działy umożliwiają ustawianie budżetu lub limitu przydziału na poziomie działu.

- **Konta** są jednostkami organizacyjnymi w witrynie Azure Enterprise Portal. Konta umożliwiają zarządzanie subskrypcjami i wyświetlanie raportów.

- **Subskrypcje** są najmniejszymi jednostkami w witrynie Azure Enterprise Portal. Są to kontenery dla usług platformy Azure zarządzanych przez administratora usługi.

Na poniższym diagramie przedstawiono proste hierarchie witryny Azure EA Portal.

![Diagram prostych hierarchii witryny Azure EA Portal](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

W ramach rejestracji przedsiębiorstwa występują następujące role użytkowników administracyjnych:

- Administrator przedsiębiorstwa
- Administrator działu
- Właściciel konta
- Administrator usługi
- Kontakt dla powiadomień

Role umożliwiają realizowanie zadań w dwóch różnych portalach. Witryna [Azure Enterprise Portal](https://ea.azure.com) służy do zarządzania rozliczeniami i kosztami, a witryna [Azure Portal](https://portal.azure.com) umożliwia zarządzanie usługami platformy Azure.

Role użytkowników są skojarzone z kontem użytkownika. Aby zweryfikować autentyczność użytkowników, każdy użytkownik musi mieć prawidłowe konto służbowe lub Microsoft. Upewnij się, że każde konto jest skojarzone z używanym adresem e-mail. Powiadomienia konta są wysyłane na adres e-mail.

Podczas konfigurowania użytkowników do roli administratora przedsiębiorstwa można przypisać wiele kont. Jednak tylko jedno konto może mieć rolę właściciela konta. Ponadto do jednego konta można przypisać zarówno rolę administratora przedsiębiorstwa, jak i rolę właściciela konta.

### <a name="enterprise-administrator"></a>Administrator przedsiębiorstwa

Użytkownicy z tą rolą mają najwyższy poziom dostępu. Mogą wykonywać następujące czynności:

- Zarządzanie kontami i właścicielami kont.
- Zarządzanie innymi administratorami przedsiębiorstwa.
- Zarządzanie administratorami działów.
- Zarządzanie kontaktami dla powiadomień.
- Wyświetlanie użycia na wszystkich kontach.
- Wyświetlanie nienaliczonych opłat na wszystkich kontach.

W ramach rejestracji przedsiębiorstwa może być wielu administratorów przedsiębiorstwa. Administratorom przedsiębiorstwa można przyznać dostęp tylko do odczytu. Wszyscy oni dziedziczą rolę administratora działu.

### <a name="department-administrator"></a>Administrator działu

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie działów i zarządzanie nimi.
- Tworzenie nowych właścicieli kont.
- Wyświetlanie szczegółów użycia dla działów, którymi zarządzają.
- Wyświetlanie kosztów w przypadku posiadania niezbędnych uprawnień.

Dla każdej rejestracji przedsiębiorstwa można mieć wielu administratorów działów.

Tworząc lub edytując konto administratora działu, można przyznać mu dostęp tylko do odczytu. Dla opcji tylko do odczytu ustaw wartość **Tak**.

### <a name="account-owner"></a>Właściciel konta

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie subskrypcji i zarządzanie nimi.
- Zarządzanie administratorami usług.
- Wyświetlanie użycia dla subskrypcji.

Każde konto wymaga unikatowego konta służbowego lub konta Microsoft. Aby uzyskać więcej informacji na temat ról administracyjnych w witrynie Azure Enterprise Portal, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrator usługi

Rola administratora usługi ma uprawnienia do zarządzania usługami w witrynie Azure Portal i przypisywania użytkowników do roli współadministratora.

### <a name="notification-contact"></a>Kontakt dla powiadomień

Kontakt dla powiadomień odbiera powiadomienia o użyciu dotyczące rejestracji.

## <a name="activate-your-enrollment"></a>Aktywowanie rejestracji

W celu aktywowania usługi początkowy administrator przedsiębiorstwa otwiera witrynę [Azure Enterprise Portal](https://ea.azure.com) i loguje się przy użyciu adresu e-mail z wiadomości e-mail z zaproszeniem.

Jeśli skonfigurowano Cię jako administratora przedsiębiorstwa, nie musisz otrzymać wiadomości e-mail dotyczącej aktywacji. Przejdź do witryny [Azure Enterprise Portal](https://ea.azure.com) i zaloguj się przy użyciu konta szkolnego lub służbowego albo konta Microsoft (używając adresu e-mail i hasła).

Jeśli masz więcej niż jedną rejestrację, wybierz tę, którą chcesz aktywować. Domyślnie są wyświetlane tylko aktywne rejestracje. Aby wyświetlić historię rejestracji, wyczyść opcję **Aktywne** w prawym górnym rogu witryny Azure Enterprise Portal.

W obszarze **Rejestracja** wyświetlany jest stan **Aktywne**.

![Przykład pokazujący aktywną rejestrację](./media/ea-portal-get-started/ea-enrollment-status.png)

Tylko istniejący administratorzy przedsiębiorstwa platformy Azure mogą tworzyć konta innych administratorów przedsiębiorstwa.

### <a name="create-another-enterprise-administrator"></a>Tworzenie innego administratora przedsiębiorstwa

Aby dodać innego administratora przedsiębiorstwa:

1. Zaloguj się w witrynie [Azure Enterprise Portal](https://ea.azure.com).
1. Wybierz pozycję **Zarządzaj** > **Szczegóły rejestracji**.
1. Wybierz pozycję **+ Dodaj administratora** w prawym górnym rogu.

Upewnij się, że znasz adres e-mail użytkownika i preferowaną metodę uwierzytelniania, na przykład konto szkolne lub służbowe albo konto Microsoft.

Jeśli nie jesteś administratorem przedsiębiorstwa, skontaktuj się z administratorem przedsiębiorstwa, aby zażądać dodania Ciebie do rejestracji. Po dodaniu do rejestracji otrzymasz wiadomość e-mail dotyczącą aktywacji.

Jeśli administrator przedsiębiorstwa nie może Ci pomóc, utwórz [wniosek o pomoc techniczną w witrynie Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Podaj następujące informacje:

- Numer rejestracji
- Adres e-mail do dodania i typ uwierzytelnienia (konto służbowe lub konto Microsoft)
- Wiadomość e-mail z zatwierdzeniem od istniejącego administratora przedsiębiorstwa
  - Jeśli istniejący administrator przedsiębiorstwa nie jest dostępny, skontaktuj się z partnerem lub doradcą ds. oprogramowania, aby zażądać zmiany danych kontaktowych za pomocą narzędzia Volume Licensing Service Center (VLSC).

Aby uzyskać więcej informacji na temat ról administracyjnych w przedsiębiorstwie, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

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

![Przykładowe okno dialogowe Dodawanie administratora działu](./media/ea-portal-get-started/ea-create-add-department-admin.png)

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

![Przykład przedstawiający listę kont i opcję +Dodaj konto](./media/ea-portal-get-started/create-ea-add-an-account.png)

Możesz dodać kolejne konto, wybierając pozycję **Dodaj kolejne konto** lub pozycję **Dodaj** w prawym dolnym rogu lewego paska narzędzi.

Aby potwierdzić własność konta:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. Wyświetl stan.

   Stan powinien ulec zmianie z **Oczekujące** na **Data początkowa/końcowa**. Data początkowa/końcowa to data pierwszego logowania użytkownika i data zakończenia obowiązywania umowy.
1. Gdy zostanie wyświetlony komunikat **Ostrzeżenie**, właściciel konta musi wybrać przycisk **Kontynuuj**, aby aktywować konto podczas pierwszego logowania się w witrynie Azure Enterprise Portal.

## <a name="change-account-owner"></a>Zmienianie właściciela konta

Administratorzy przedsiębiorstwa mogą korzystać z witryny Azure Enterprise Portal do przenoszenia własności konta subskrypcji w ramach rejestracji. Działanie to powoduje przeniesienie wszystkich subskrypcji z konta użytkownika źródłowego na konto użytkownika docelowego.

Przenosząc własność do konta, należy pamiętać o następujących kwestiach:

- Obsługiwane są następujące transfery:
  - Z konta służbowego na inne konto służbowe.
  - Z konta Microsoft na konto służbowe.
  - Z konta Microsoft na inne konto Microsoft.

    Konto docelowe musi być prawidłowym kontem komercyjnym platformy Azure, aby być prawidłowym miejscem docelowym umożliwiającym przenoszenie. W przypadku nowych kont przed zalogowaniem do witryny Azure Enterprise Portal zostanie wyświetlony monit o utworzenie konta komercyjnego platformy Azure. W przypadku istniejących kont, aby konto było uprawnione, należy najpierw utworzyć nową subskrypcję platformy Azure.

- Nie można przenieść subskrypcji z konta służbowego na konto Microsoft.

- Po zakończeniu przenoszenia subskrypcji firma Microsoft aktualizuje właściciela konta.

Należy pamiętać o następujących zasadach kontroli dostępu na podstawie ról (RBAC) platformy Azure:

- W przypadku przenoszenia subskrypcji między dwoma identyfikatorami organizacyjnymi w tej samej dzierżawie zasady RBAC platformy Azure i istniejące role administratora usługi oraz współadministratora są zachowywane.
- Inne przenoszenie subskrypcji powoduje utratę zasad RBAC platformy Azure oraz przypisań ról.
- Zasady i role administratorów nie są przenoszone między różnymi katalogami. Administratorzy usługi są aktualizowani do właściciela konta docelowego.

Przed zmianą właściciela konta:

1. W witrynie Azure Enterprise Portal wyświetl kartę **Konto** i sprawdź konto źródłowe. Konto źródłowe musi być aktywne.
1. Zidentyfikuj konto docelowe i upewnij się, że jest ono aktywne.

Aby przenieść własność konta dla wszystkich subskrypcji:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
1. Wybierz ikonę zmiany właściciela konta po prawej stronie. Ikona przedstawia osobę.
1. Wybierz kwalifikujące się konto, a następnie wybierz pozycję **Dalej**.
1. Potwierdź przeniesienie i wybierz pozycję **Prześlij**.

![Obraz przedstawiający symbol Zmiana właściciela konta](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Aby przenieść własność konta dla jednej subskrypcji:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj**.
1. Wybierz kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
1. Wybierz ikonę przenoszenia subskrypcji po prawej stronie. Ikona przedstawia stronę.
1. Wybierz kwalifikującą się subskrypcję, a następnie wybierz pozycję **Dalej**.
1. Potwierdź przeniesienie, a następnie wybierz pozycję **Prześlij**.

![Obraz przedstawiający symbol Przenoszenie subskrypcji](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

W poniższym filmie przedstawiono zarządzanie użytkownikami w witrynie Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

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

- [Edytowanie szczegółów subskrypcji ](https://account.azure.com/Subscriptions)
- [Zarządzanie usługami subskrypcji](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Przenoszenie subskrypcji Enterprise do subskrypcji rozliczanej zgodnie z rzeczywistym użyciem

Aby przenieść subskrypcję Enterprise do pojedynczej subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem, należy utworzyć nowy wniosek o pomoc techniczną w witrynie Azure Enterprise Portal. Aby utworzyć wniosek o pomoc techniczną, wybierz pozycję **Nowy wniosek o pomoc techniczną** w obszarze **Pomoc i obsługa techniczna**.

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

## <a name="view-usage-summary-and-download-reports"></a>Wyświetlanie podsumowania użycia i pobieranie raportów

Administratorzy przedsiębiorstwa mogą wyświetlić podsumowanie danych użycia, użytej przedpłaty za platformę Azure i opłat związanych z dodatkowym użyciem w witrynie Azure Enterprise Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

Aby wyświetlić szczegółowe informacje o użyciu na określonych kontach, pobierz raport ze szczegółami użycia:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. Wybierz pozycję **Raporty**.
1. Wybierz kartę **Pobierz zestawienie użycia**.
1. Na liście raportów wybierz pozycję **Pobierz** dla miesięcznego raportu, który chcesz uzyskać.

   > [!NOTE]
   > Raport ze szczegółami użycia nie zawiera żadnych należnych podatków.
   >
   > Może wystąpić opóźnienie do ośmiu godzin od momentu, w którym wystąpiło użycie, do chwili, gdy zostanie ono odzwierciedlone w raporcie.

Aby wyświetlić raporty i wykresy podsumowania użycia:

1. Zaloguj się w witrynie Azure Enterprise Portal.

1. Wybierz termin przedpłaty.

   Aby zmienić zakres dat w raporcie **Podsumowanie użycia**, można zmienić ustawienie przełącznika z **M** (miesięczne) na **C** (niestandardowe) w prawym górnym rogu strony, a następnie wprowadzić datę początkową i datę końcową.

   ![Tworzenie i wyświetlanie podsumowania użycia i pobieranie raportów w widoku niestandardowym](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Wybierz okres lub miesiąc na wykresie, aby wyświetlić dodatkowe szczegóły.

   - Wykres przedstawia użycie miesiąc do miesiąca z podziałem na wykorzystane użycie, nadpłatę za usługę, opłaty naliczane osobno i opłaty za korzystanie z witryny Azure Marketplace.
   - W wybranym miesiącu można filtrować informacje według działów, kont i subskrypcji, korzystając z pól poniżej wykresu.
   - Można przełączać się między pozycjami **Opłata według usług** i **Opłata według hierarchii**.
   - Rozwijając sekcje **Usługa platformy Azure**, **Opłaty rozliczane oddzielnie** i **Azure Marketplace**, można wyświetlać szczegółowe informacje.

W tym filmie przedstawiono sposób wyświetlania użycia:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Pobieranie raportów CSV

Na stronie Pobieranie raportu miesięcznego administratorzy przedsiębiorstwa mogą pobierać następujące raporty jako pliki CSV:

- Saldo i opłaty
- Szczegóły użycia
- Opłaty za korzystanie z witryny Azure Marketplace
- Arkusz cen

Aby pobrać raporty:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raporty**.
2. Wybierz pozycję **Pobieranie danych użycia** w górnej części strony.
3. Wybierz pozycję **Pobierz** obok raportu za dany miesiąc.

   > [!NOTE]
   > Może wystąpić opóźnienie do pięciu dni od daty, kiedy wystąpiło użycie, do daty, gdy zostanie ono wykazane w raporcie.
   >
   > Użytkownicy pobierający pliki CSV przy użyciu przeglądarki Safari do programu Excel mogą napotkać błędy formatowania. Aby uniknąć błędów, otwórz plik za pomocą edytora tekstu.

![Przykład pokazujący stronę Pobieranie danych użycia](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

W tym filmie przedstawiono sposób pobierania informacji o użyciu:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Zaawansowane pobieranie raportu

W przypadku raportowania dla określonych zakresów dat lub kont można użyć funkcji pobierania raportu zaawansowanego. Plik wyjściowy ma format CSV, w którym można zawrzeć duże zestawy rekordów.

1. W witrynie Azure Enterprise Portal wybierz pozycję **Pobierz raport zaawansowany**.
1. Wybierz odpowiedni zakres dat i konta.
1. Wybierz pozycję **Żądaj danych użycia**.
1. Wybieraj przycisk **Odśwież** do momentu aktualizacji stanu raportu na **Pobierz**.
1. Pobierz raport.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Pobieranie raportów użycia oraz informacji rozliczeniowych dotyczących wcześniejszej rejestracji

Po przeniesieniu rejestracji można pobrać raporty użycia oraz informacje rozliczeniowe dotyczące wcześniejszej rejestracji. Raporty historyczne są dostępne zarówno w witrynie Azure Enterprise Portal, jak i obszarze zarządzania kosztami.

Witryna Azure Enterprise Portal umożliwia ukrycie nieaktywnych rejestracji. Aby wyświetlić nieaktywne przeniesione rejestracje, musisz usunąć zaznaczenie pola **Aktywne**.  

![Usunięcie zaznaczenia pola Aktywne umożliwia wyświetlenie nieaktywnych rejestracji](./media/ea-portal-get-started/unchecked-active-box.png)

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
- **Konto służbowe**: Dla organizacji, które skonfigurowały usługę Active Directory z federacją w chmurze i mają wszystkie konta w jednej dzierżawie.

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

## <a name="get-started-on-azure-ea---faq"></a>Wprowadzenie do umowy EA platformy Azure — często zadawane pytania

Ta sekcja zawiera szczegółowe informacje dotyczące typowych pytań, które zadawali klienci podczas procesu dołączania.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Czy można skojarzyć istniejące konto platformy Azure z rejestracją EA platformy Azure?

Tak. Wszystkie subskrypcje platformy Azure, w których jesteś właścicielem konta, zostaną przekonwertowane na umowę Enterprise Agreement. Dotyczy to subskrypcji, w których są używane środki miesięczne, takich jak na przykład Visual Studio, AzurePass, MPN i BizSpark. Konwersja takich subskrypcji powoduje utratę środków miesięcznych.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Przypadkowo skojarzono moje istniejące konto platformy Azure z rejestracją EA platformy Azure. W wyniku środki miesięczne zostały utracone. Czy jest możliwe odzyskanie tych środków miesięcznych?

Jeśli zalogowano się jako właściciel konta EA platformy Azure przy użyciu tych samych poświadczeń, które są używane w subskrypcji programu Visual Studio, można odzyskać korzyść platformy Azure dla indywidualnej subskrypcji programu Visual Studio, wykonując jedną z następujących akcji:

- Usuń lub przenieś wszystkie skojarzone subskrypcje platformy Azure, a następnie usuń właściciela konta w witrynie Azure Enterprise Portal. Następnie zarejestruj się ponownie w celu uzyskania indywidualnych korzyści platformy Azure związanych z programem Visual Studio.
- Usuń subskrybenta programu Visual Studio z lokacji administracyjnej w programie VLSC i ponownie przypisz subskrypcję do konta z innymi poświadczeniami. Następnie zarejestruj się ponownie w celu uzyskania indywidualnych korzyści platformy Azure związanych z programem Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Jakiego typu subskrypcję należy utworzyć?

Witryna Azure Enterprise Portal oferuje dwa typy subskrypcji dla klientów korporacyjnych:

- Microsoft Azure Enterprise — idealne pod kątem:
  - wszelkich zastosowań produkcyjnych
  - najlepszych cen na podstawie wydatków na infrastrukturę

  Aby uzyskać więcej informacji, [skontaktuj się z działem sprzedaży platformy Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise — tworzenie i testowanie. Idealne pod kątem:
  - wszelkich zespołowych obciążeń tworzenia/testowania
  - średnich i dużych indywidualnych obciążeń tworzenia/testowania
  - dostępu do specjalnych obrazów MSDN i preferencyjnych stawek za usługi

  Aby uzyskać więcej informacji, zapoznaj się z ofertą [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Czy można przenieść własność subskrypcji na inne konto?

Tak, można przenieść własność subskrypcji na inne konto. Jeśli na przykład konto A ma trzy subskrypcje, administrator przedsiębiorstwa może przenieść jedną subskrypcję na konto B, jedną na konto C i jedną na konto D. Administrator może również przenieść wszystkie subskrypcje na konto E.

Aby przenieść subskrypcje:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Zarządzaj** > **Konto**.
1. Umieść wskaźnik myszy na skrajnej prawej pozycji **Konto**, aby wyświetlić opcje **Przenieś własność** (ikona osoby) i **Przenieś subskrypcję** (ikona listy). Te opcje są widoczne tylko w przypadku aktywnych kont.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Nazwa mojej subskrypcji jest taka sama jak nazwa oferty. Czy należy zmienić nazwę subskrypcji, tak aby była bardziej zrozumiała w mojej organizacji?

Domyślna nazwa utworzonej subskrypcji odpowiada typowi wybranej oferty. Zalecamy zmianę nazwy subskrypcji na coś, co ułatwi śledzenie subskrypcji.

Aby zmienić nazwę:

1. Zaloguj się do witryny [https://account.windowsazure.com](https://account.windowsazure.com).
1. Wybierz listę subskrypcji.
1. Wybierz subskrypcję, którą chcesz edytować.
1. Wybierz ikonę **Zarządzaj subskrypcją**.
1. Edytuj szczegóły subskrypcji.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Jak mogę śledzić koszty naliczane w centrum kosztu?

Aby śledzić koszty według centrum kosztu, należy zdefiniować centrum kosztu na dowolnym z następujących poziomów:

- Dział
- Konto
- Subskrypcja

W zależności od potrzeb można użyć tego samego centrum kosztu, aby śledzić użycie i koszty związane z określonym centrum kosztu.

Aby na przykład śledzić koszty dla określonego projektu, w którym uczestniczy wiele działów, można zdefiniować centrum kosztu na poziomie subskrypcji w celu śledzenia użycia i kosztów.

Nie można zdefiniować centrum kosztu na poziomie usługi. Jeśli chcesz śledzić użycie na poziomie usługi, możesz użyć funkcji _tagowania_ dostępnej na poziomie usługi.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Jak mogę śledzić użycie i wydatki dla różnych działów w mojej organizacji?

W ramach rejestracji EA platformy Azure możesz utworzyć tyle działów, ile potrzebujesz. Aby prawidłowo śledzić użycie, należy się upewnić, że subskrypcje nie są współużytkowane przez działy.

Po utworzeniu działów i subskrypcji można wyświetlić dane w raporcie użycia. Te informacje ułatwiają śledzenie użycia oraz zarządzanie kosztami i wydatkami na poziomie działu.

Dane użycia są również dostępne za pośrednictwem interfejsu API raportowania. Aby uzyskać szczegółowe informacje i przykładowy kod, zobacz artykuł [Interfejsy API REST usługi Azure Enterprise](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Czy mogę ustawić limit przydziału wydatków i otrzymywać alerty w miarę zbliżania się do tego limitu?

Limit przydziału wydatków można ustawić na poziomie działu, a system automatycznie powiadomi Cię, gdy limit przydziału wydatków osiągnie 50%, 75%, 90% i 100% zdefiniowanej wartości.

Aby zdefiniować limit przydziału wydatków, wybierz dział, a następnie wybierz ikonę edycji. Gdy zakończysz edytowanie limitu wydatków, wybierz pozycję **Zapisz**.

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Grupy zasobów były używane do implementowania kontroli RBAC platformy Azure i śledzenia użycia. Jak wyświetlić skojarzone szczegóły użycia?

W przypadku używania _grup zasobów_ i _tagów_ informacje te są śledzone na poziomie usługi. Dostęp do nich można uzyskać, pobierając plik CSV ze szczegółowym użyciem. Zobacz [Pobieranie raportu użycia](https://ea.azure.com/report/downloadusage) w witrynie Azure Enterprise Portal.

Dane o użyciu są również dostępne za pośrednictwem interfejsu API. Aby uzyskać szczegółowe informacje i przykładowy kod, zobacz artykuł [Interfejsy API REST usługi Azure Enterprise](./ea-portal-rest-apis.md).

> [!NOTE]
> Tagi można stosować tylko do zasobów, które obsługują operacje usługi Azure Resource Manager. W przypadku utworzenia maszyny wirtualnej, sieci wirtualnej lub magazynu za pomocą klasycznego modelu wdrażania (na przykład za pośrednictwem portalu klasycznego) nie można zastosować znacznika do takiego zasobu. Aby umożliwić obsługę tagowania, należy ponownie wdrożyć te zasoby za pomocą Menedżera zasobów. Wszystkie inne zasoby obsługują tagowanie.

### <a name="can-i-perform-analyses-using-power-bi"></a>Czy mogę wykonywać analizy przy użyciu usługi Power BI?

Tak. Pakiet zawartości Microsoft Azure Enterprise dla usługi Power BI umożliwia wykonywanie następujących działań:

- Szybkie importowanie i analizowanie danych dotyczących wykorzystania platformy Azure na potrzeby rejestracji w przedsiębiorstwie.
- Sprawdzanie, który dział, konto lub subskrypcja odnotowały największe użycie.
- Sprawdzanie, z której usługi organizacja korzystała najczęściej.
- Śledzenie trendów dotyczących wydatków i użycia.

Aby użyć usługi Power BI:

1. Przejdź do witryny internetowej usługi Power BI.
1. Zaloguj się za pomocą ważnego konta służbowego.

   Konto służbowe może być takie samo jak konto używane do uzyskiwania dostępu do rejestracji za pomocą witryny Azure Enterprise Portal. Można również użyć innego konta.
1. Na pulpicie nawigacyjnym usług wybierz kafelek Microsoft Azure Enterprise, a następnie wybierz pozycję **Połącz**.
1. Na ekranie **Nawiązywanie połączenia z platformą Azure Enterprise** wprowadź następujące informacje:
    - Adres URL środowiska platformy Azure: [https://ea.azure.com](https://ea.azure.com)
    - Liczba miesięcy: od 1 do 36
    - Numer rejestracji: numer Twojej rejestracji
1. Wybierz opcję **Dalej**.
1. W polu **Klucz konta** podaj klucz interfejsu API.

   Klucz interfejsu API można znaleźć w witrynie Azure Enterprise Portal. Otwórz kartę **Pobieranie danych użycia**, a następnie wybierz pozycję **Klucz dostępu interfejsu API**. Skopiuj klucz, a następnie go wklej w polu **Klucz konta** w usłudze Power BI.

W zależności od rozmiaru zestawu danych ładowanie danych w usłudze Power BI może potrwać od 5 do 30 minut.

Funkcja raportowania usługi Power BI jest dostępna dla bezpośrednich klientów z umową EA platformy Azure, partnerów i klientów pośrednich, którzy mogą wyświetlać informacje dotyczące rozliczeń.

## <a name="next-steps"></a>Następne kroki

- Administratorzy witryny Azure Enterprise Portal powinni przeczytać artykuł [Administracja w witrynie Azure Enterprise Portal](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure Enterprise Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure Enterprise Portal](ea-portal-troubleshoot.md).
- Przewodnik dołączania do umowy EA platformy Azure znajduje się w temacie [Przewodnik po dołączaniu do umowy EA platformy Azure (plik PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).