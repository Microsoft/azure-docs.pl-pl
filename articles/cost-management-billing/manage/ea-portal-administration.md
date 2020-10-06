---
title: Administracja przy użyciu witryny Azure EA Portal
description: W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: cc44f05491cc17adb9ca51963d09667400fa1ff6
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371919"
---
# <a name="azure-ea-portal-administration"></a>Administracja przy użyciu witryny Azure EA Portal

W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal (https://ea.azure.com). Witryna Azure EA Portal to portal zarządzania online, który ułatwia klientom zarządzanie kosztami usług umowy EA na platformie Azure. Aby uzyskać informacje wprowadzające dotyczące witryny Azure EA Portal, zobacz artykuł [Rozpoczynanie pracy w witrynie Azure EA Portal](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Kojarzenie konta z działem

Administratorzy przedsiębiorstwa mogą skojarzyć istniejące konta z działami w ramach rejestracji.

### <a name="to-associate-an-account-to-a-department"></a>Aby skojarzyć konto z działem

1. Zaloguj się do witryny Azure EA Portal jako administrator przedsiębiorstwa.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz pozycję **Dział**.
1. Umieść kursor na wierszu z kontem, a następnie wybierz ikonę ołówka po prawej stronie.
1. Wybierz dział z menu rozwijanego.
1. Wybierz pozycję **Zapisz**.

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

Aby uzyskać więcej informacji na temat ról użytkownika, zobacz [Role użytkownika przedsiębiorstwa](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Dodawanie konta usługi Azure EA

Konto Azure EA to jednostka organizacyjna w witrynie Azure EA Portal. Służy ono do administrowania subskrypcjami i tworzenia raportów. Aby uzyskać dostęp do usług platformy Azure i korzystać z nich, musisz utworzyć konto lub poprosić o jego utworzenie.

Aby uzyskać więcej informacji na temat kont platformy Azure, zobacz [Dodawanie konta](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account).

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

## <a name="delete-subscription"></a>Usuwanie subskrypcji

Aby usunąć subskrypcję, w której jesteś właścicielem konta:

1. Zaloguj się do witryny Azure Portal przy użyciu poświadczeń skojarzonych z Twoim kontem.
1. W menu Centrum wybierz pozycję **Subskrypcje**.
1. Na karcie subskrypcji w lewym górnym rogu strony wybierz subskrypcję, którą chcesz anulować, a następnie wybierz pozycję **Anuluj subskrypcję**, aby otworzyć kartę anulowania.
1. Wprowadź nazwę subskrypcji i wybierz przyczynę anulowania, a następnie wybierz pozycję **Anuluj subskrypcję**.

Tylko administratorzy kont mogą anulować subskrypcje.

Aby uzyskać więcej informacji, zobacz [Co stanie się, gdy anuluję moją subskrypcję?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

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

Nowy kontakt dla powiadomień zostanie wyświetlony w sekcji **Kontakt dla powiadomień**. Aby zmienić częstotliwość powiadamiania, wybierz kontakt dla powiadomień i wybierz symbol ołówka po prawej stronie wybranego wiersza. Ustaw częstotliwość na **codziennie**, **co tydzień**, **co miesiąc** lub**brak**.

Możesz pominąć powiadomienia dotyczące cyklu życia ze _zbliżającą się datą zakończenia okresu pokrycia_ oraz _ze zbliżającą się datą wyłączenia i anulowania aprowizacji_. Wyłączenie powiadomień dotyczących cyklu życia powoduje pomijanie powiadomień o okresie pokrycia i dacie zakończenia obowiązywania umowy.

## <a name="azure-sponsorship-offer"></a>Oferta dostępu sponsorowanego Azure

Oferta dostępu sponsorowanego Azure to ograniczone, sponsorowane konto platformy Microsoft Azure. Jest ono dostępne tylko na zaproszenie e-mail dla ograniczonej liczby klientów wybranych przez firmę Microsoft. Jeśli masz upoważnienie do oferty dostępu sponsorowanego Microsoft Azure, otrzymasz zaproszenie e-mail dla Twojego identyfikatora konta.

Aby uzyskać więcej informacji, utwórz [wniosek o pomoc techniczną](https://aka.ms/azrsponsorship) dotyczący aktywacji sponsorowania.

## <a name="conversion-to-work-or-school-account-authentication"></a>Konwersja na uwierzytelnianie konta służbowego

Użytkownicy z umową Azure Enterprise mogą konwertować typ uwierzytelniania z konta Microsoft (MSA lub Live ID) na konto służbowe (które używa usługi Active Directory na platformie Azure).

Aby rozpocząć:

1. Dodaj konto służbowe do witryny Azure EA Portal w wymaganych rolach.
1. W przypadku wystąpienia błędów konto może być nieprawidłowe w usłudze Active Directory.  Platforma Azure używa głównej nazwy użytkownika (UPN), która nie zawsze jest identyczna z adresem e-mail.
1. Uwierzytelnij się w witrynie Azure EA Portal za pomocą konta służbowego.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Aby przekonwertować subskrypcje z kont Microsoft na konta służbowe:

1. Zaloguj się do portalu zarządzania przy użyciu konta Microsoft, do którego należą subskrypcje.
1. Użyj transferu własności konta, aby przejść do nowego konta.
1. Teraz konto Microsoft powinno być wolne od aktywnych subskrypcji i można je usunąć.
1. Wszystkie usunięte konta pozostaną widoczne w portalu w stanie nieaktywnym z powodów związanych z rozliczeniami historycznymi.  Możesz odfiltrować je z widoku, zaznaczając pole wyboru, aby wyświetlić tylko aktywne konta.

## <a name="account-subscription-ownership-faq"></a>Własność subskrypcji konta — często zadawane pytania

Ten dokument zawiera odpowiedzi na często zadawane pytania dotyczące własności subskrypcji konta.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Ilu właścicieli konta platformy Azure można mieć na subskrypcję?

Dozwolony jest tylko jeden właściciel konta na subskrypcję.  Dodatkowe role można dodawać przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure na karcie subskrypcji w lewym górnym rogu strony witryny [Azure Portal](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Czy właściciel konta platformy Azure może być wymieniony w więcej niż jednym dziale?

Nie, właściciel konta może być skojarzony tylko z jednym działem. Te zasady ułatwiają dokładne monitorowanie i rozdzielanie kosztów oraz wydatków związanych z działem, do którego jest dopasowany w ramach rejestracji EA w witrynie Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Czy właściciel konta platformy Azure może być wymieniony jako grupa zabezpieczeń?

Nie, właściciel subskrypcji musi być unikatowym kontem Microsoft (MSA) lub uwierzytelnieniem usługi Azure Active Directory (AAD). Aby uwzględnić dziedziczenie w organizacji, możesz rozważyć utworzenie kont ogólnych i użycie usługi AAD w celu zarządzania dostępem do subskrypcji.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Czy użytkownicy indywidualni mogą mieć wiele subskrypcji?

Właściciel konta platformy Azure może utworzyć nieograniczoną liczbę subskrypcji i zarządzać nimi.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Jak mogę wyświetlić wszystkie subskrypcje mojej organizacji lub uzyskać do nich dostęp?

Obecnie robi się to za pomocą zasad. Oznacza to, że należy wymagać tego dla każdej utworzonej subskrypcji i że Twoje konto jest dodawane do roli subskrypcji przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure.

### <a name="where-do-i-go-to-create-a-subscription"></a>Gdzie mogę utworzyć subskrypcję?

Aby można było utworzyć subskrypcję oferty w ramach umowy Enterprise Azure (EA), Twoje konto musi zostać dodane do roli właściciela konta przez administratora rejestracji EA w witrynie Azure EA Portal. Następnie należy zalogować się w witrynie Azure EA Portal w celu uzyskania uprawnień do tworzenia subskrypcji typu oferta EA. Zalecamy, aby pierwszą subskrypcję EA utworzyć przy użyciu linku „+ Dodaj subskrypcję” na karcie subskrypcji w witrynie EA Portal.  Jednak po uzyskaniu uprawnień przez konto łatwiejsze może być tworzenie subskrypcji na stronie portal.azure.com na karcie subskrypcji w lewym górnym rogu strony, gdzie można utworzyć subskrypcję i zmienić jej nazwę w jednym kroku.

### <a name="who-can-create-a-subscription"></a>Kto może utworzyć subskrypcję?

Aby utworzyć subskrypcję oferty typu Enterprise Azure, musisz mieć uprawnienia w roli właściciela konta w witrynie [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej o tym, jak [rezerwacje maszyn wirtualnych](ea-portal-vm-reservations.md) mogą pomóc Ci zaoszczędzić pieniądze.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
