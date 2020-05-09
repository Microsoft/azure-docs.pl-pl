---
title: Administracja przy użyciu witryny Azure EA Portal
description: W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 2b2ec7a5954649c36cf9da5d7933c83eec67faa3
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692637"
---
# <a name="azure-ea-portal-administration"></a>Administracja przy użyciu witryny Azure EA Portal

W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal (https://ea.azure.com). Witryna Azure EA Portal to portal zarządzania online, który ułatwia klientom zarządzanie kosztami usług umowy EA na platformie Azure. Aby uzyskać informacje wprowadzające dotyczące witryny Azure EA Portal, zobacz artykuł [Rozpoczynanie pracy w witrynie Azure EA Portal](ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Dodawanie nowego administratora przedsiębiorstwa

Administratorzy przedsiębiorstwa mają największe uprawnienia podczas zarządzania rejestracją w portalu Azure EA. Początkowy administrator platformy Azure EA został utworzony podczas konfigurowania umowy EA. Można jednak dodawać i usuwać nowych administratorów w dowolnym momencie. Nowi administratorzy są dodawani tylko przez istniejących administratorów. Aby uzyskać więcej informacji na temat dodawania dodatkowych administratorów przedsiębiorstwa, zobacz sekcję [Tworzenie innego administratora przedsiębiorstwa](ea-portal-get-started.md#create-another-enterprise-administrator). Aby uzyskać więcej informacji o rolach i zadaniach dotyczących rozliczeń, zobacz sekcję [Role i zadania profilów rozliczeniowych](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aktualizowanie stanu użytkownika z „oczekujące” na „aktywne”

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji w ramach umowy EA, ich stan jest wyświetlany jako _oczekujące_. Gdy nowy właściciel konta otrzymuje powitalną wiadomość e-mail z informacjami o aktywacji, może się zalogować, aby aktywować swoje konto. Po aktywowaniu konta stan konta jest aktualizowany z _oczekujące_ na _aktywne_. Właściciel konta musi odczytać komunikat z ostrzeżeniem, a następnie wybrać pozycję **Kontynuuj**. Nowi użytkownicy mogą zobaczyć monit o wprowadzenie imienia i nazwiska, aby utworzyć konto komercyjne. W takiej sytuacji muszą dodać wymagane informacje, aby kontynuować, a konto jest aktywowane.

## <a name="add-a-department-admin"></a>Dodawanie administratora działu

Po utworzeniu działu przez administratora portalu Azure EA administrator oferty Azure Enterprise może dodać administratorów działu i skojarzyć ich z działem. Administrator działu może tworzyć nowe konta. Nowe konta są potrzebne do tworzenia subskrypcji portalu Azure EA.

Aby uzyskać więcej informacji o dodawaniu administratora działu, zobacz [Tworzenie administratora działu w portalu Azure EA](ea-portal-get-started.md#add-a-department-administrator).

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

Klienci z umowami EA mogą ustawiać lub zmieniać limity przydziałów wydatków dla każdego działu w ramach rejestracji. Kwota przydziału wydatków jest ustawiana dla bieżącego okresu zobowiązania. Na koniec bieżącego okresu zobowiązania system przedłuży istniejący limit przydziału wydatków na następny okres zobowiązania, chyba że wartości zostaną zaktualizowane.

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

Limit przydziału wydatków działu będzie teraz widoczny w widoku Lista działów na karcie Dział. Na koniec bieżącego zobowiązania witryna Azure EA Portal zachowa limity przydziału wydatków na następny okres zobowiązania.

Kwota limitu przydziału dla działu jest niezależna od bieżącego zobowiązania pieniężnego. Ponadto kwota limitu przydziału i alerty są stosowane tylko do użycia tej samej firmy. Limit przydziału wydatków dla działu jest przeznaczony wyłącznie do celów informacyjnych i nie wymusza limitów wydatków.

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

Aby uzyskać więcej informacji na temat kont platformy Azure, zobacz temat dotyczący dodawania konta.

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

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Przenoszenie konta przedsiębiorstwa do nowej rejestracji

Przeniesienie konta powoduje przeniesienie właściciela konta z jednej rejestracji do innej. Wszystkie powiązane subskrypcje należące do właściciela konta zostaną przeniesione do rejestracji docelowej. Użyj procedury przenoszenia konta, gdy masz wiele aktywnych rejestracji i chcesz przenieść tylko wybranych właścicieli kont.

Ta sekcja służy tylko do celów informacyjnych, ponieważ akcji nie może wykonać administrator przedsiębiorstwa. W celu przeniesienia konta przedsiębiorstwa do nowej rejestracji jest wymagany wniosek o pomoc techniczną.

Podczas przenoszenia konta przedsiębiorstwa do nowej rejestracji pamiętaj o następujących kwestiach:

- Przenoszone są tylko konta określone w żądaniu. Jeśli wybierzesz wszystkie konta, wszystkie zostaną przeniesione.
- Rejestracja źródłowa zachowuje stan jako aktywny lub rozszerzony. Z rejestracji można korzystać do momentu jej wygaśnięcia.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia konta podaj następujące informacje:

- Numer rejestracji docelowej, nazwa konta i adres e-mail właściciela konta do przeniesienia
- W przypadku rejestracji źródłowej numer rejestracji i konto do przeniesienia

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem konta:

- Zatwierdzenie od administratora umowy EA jest wymagane w przypadku rejestracji docelowej i źródłowej
- Jeśli przeniesienie konta nie spełnia Twoich wymagań, rozważ przeniesienie rejestracji.
- Przenoszenie kont powoduje przeniesienie wszystkich usług i subskrypcji powiązanych z określonymi kontami.
- Po zakończeniu przenoszenia przeniesione konto będzie widoczne jako nieaktywne w ramach rejestracji źródłowej i jako aktywne w ramach rejestracji docelowej.
- Data zakończenia widoczna na koncie będzie odpowiadać efektywnej dacie przeniesienia w rejestracji źródłowej oraz dacie rozpoczęcia w rejestracji docelowej.
- Wszelkie użycie konta przed efektywną datą przeniesienia pozostanie w ramach rejestracji źródłowej.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Przenoszenie rejestracji przedsiębiorstwa do nowej rejestracji

Przeniesienie rejestracji rozważa się, gdy:

- Został osiągnięty termin zobowiązania bieżącej rejestracji.
- Rejestracja ma stan wygasła/rozszerzona i jest negocjowana nowa umowa.
- Jeśli masz wiele rejestracji i chcesz połączyć wszystkie konta oraz rozliczenia w ramach jednej rejestracji.

Ta sekcja służy tylko do celów informacyjnych, ponieważ akcji nie może wykonać administrator przedsiębiorstwa. Do przeniesienia rejestracji przedsiębiorstwa do nowej rejestracji jest wymagany wniosek o pomoc techniczną.

Po utworzeniu żądania dotyczącego przeniesienia całej rejestracji w przedsiębiorstwie do rejestracji zostaną wykonane następujące akcje:

- Wszystkie usługi, subskrypcje, konta, działy i cała struktura rejestracji, w tym wszyscy administratorzy działu umów EA, zostaną przeniesione do nowej rejestracji docelowej.
- Stan rejestracji zostanie ustawiono na _Przeniesione_. Przeniesiona rejestracja jest dostępna wyłącznie dla celów raportowania użycia historycznego.
- Do przeniesionej rejestracji nie można dodawać ról ani subskrypcji. Stan Przeniesione uniemożliwia dodatkowe użycie w odniesieniu do rejestracji.
- Pozostałe saldo zobowiązania pieniężnego w umowie zostanie utracone, łącznie z przyszłymi okresami.
-    Jeśli rejestracja, z której się przenosisz, obejmuje zakupy wystąpień zarezerwowanych, opłata za zakup wystąpień zarezerwowanych pozostaje w rejestracji źródłowej, jednak wszystkie korzyści wynikające z wystąpienia zarezerwowanego zostaną przeniesione do wykorzystania w nowej rejestracji.
-    Opłata za jednorazowe zakupy w witrynie Marketplace i wszystkie miesięczne opłaty stałe naliczone już w ramach starej rejestracji nie zostaną przeniesione do nowej rejestracji. Opłaty witryny Marketplace naliczone na podstawie użycia zostaną przeniesione.

### <a name="effective-transfer-date"></a>Data obowiązywania przeniesienia

Data obowiązywania przeniesienia może być datą początkową rejestracji docelowej lub późniejszą.

Użycie rejestracji źródłowej jest obciążane opłatą w odniesieniu do zobowiązania pieniężnego lub jako nadwyżka. Użycie, które następuje po dacie obowiązywania przeniesienia, jest transferowane do nowej rejestracji i są naliczane odpowiednie opłaty.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia rejestracji podaj następujące informacje:

- W przypadku rejestracji źródłowej numer rejestracji.
- W przypadku rejestracji docelowej numer rejestracji, do której nastąpi przeniesienie.
- W przypadku daty obowiązywania przeniesienia rejestracji może to być data początkowa rejestracji docelowej lub późniejsza. Wybrana data nie może wpływać na użycie dla żadnej wystawionej już faktury nadwyżkowej.

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem rejestracji:

- Wymagane jest zatwierdzenie od administratorów umowy EA rejestracji docelowej i źródłowej.
- Jeśli przeniesienie rezerwacji nie spełnia Twoich wymagań, rozważ przeniesienie konta.
- Stan rejestracji źródłowej zostanie zaktualizowany na „przeniesiona” i będzie dostępny tylko dla celów raportowania użycia historycznego.

### <a name="monetary-commitment"></a>Zobowiązanie pieniężne

Zobowiązania pieniężnego nie można przenosić między rejestracjami. Salda zobowiązania pieniężnego są powiązane z umową w ramach rejestracji, w której zostały zamówione. Zobowiązanie pieniężne nie jest przenoszone jako część procesu przenoszenia konta lub rejestracji.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Brak usług, których dotyczą przeniesienia kont i rejestracji

Podczas przenoszenia konta lub rejestracji nie występują przestoje. Można je wykonać w tym samym dniu żądania, jeśli zostaną podane wszystkie wymagane informacje.

## <a name="change-account-owner"></a>Zmienianie właściciela konta

W witrynie Azure EA Portal można przenosić subskrypcje od jednego właściciela konta do innego. Aby uzyskać więcej informacji, zobacz sekcję [Zmienianie właściciela konta](ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Efekty przeniesienia subskrypcji

Jeśli subskrypcja platformy Azure zostanie przeniesiona na konto w tej dzierżawie usługi Azure Active Directory, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [kontrolą dostępu na podstawie ról (RBAC)](../../role-based-access-control/overview.md) używaną do zarządzania zasobami, zachowają dostęp.

Aby wyświetlić użytkowników z dostępem RBAC do subskrypcji:

1. W witrynie Azure Portal otwórz pozycję **Subskrypcje**.
2. Wybierz subskrypcję, którą chcesz wyświetlić, a następnie wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** .
3. Wybierz pozycję **Przypisania ról**. Na stronie przypisań ról są wyświetlani wszyscy użytkownicy z dostępem RBAC do subskrypcji.

Jeśli subskrypcja zostanie przeniesiona na konto w innej dzierżawie usługi Azure AD, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [dostępem RBAC](../../role-based-access-control/overview.md) do zarządzania zasobami _utracą_ dostęp. Chociaż dostęp RBAC nie istnieje, dostęp do subskrypcji może być dostępny w ramach mechanizmów zabezpieczeń, takich jak przykład:

- Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
- Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../../storage/common/storage-account-overview.md).
- Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**.
3. Wybierz zasób.
4. Na stronie zasobu wybierz pozycję **Ustawienia**, aby wyświetlić i zaktualizować istniejące wpisy tajne.

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

## <a name="manage-partner-administrators"></a>Zarządzanie administratorami partnerów

Każdy administrator partnera w witrynie Azure EA Portal ma możliwość dodawania i usuwania innych administratorów partnerów. Administratorzy partnerów są powiązani z organizacjami partnerskimi rejestracji pośrednich i nie są powiązani bezpośrednio z rejestracjami.

### <a name="add-a-partner-administrator"></a>Dodawanie administratora partnera

Aby wyświetlić listę wszystkich rejestracji skojarzonych z tą samą organizacją partnerską, co bieżący użytkownik, wybierz kartę **Rejestracja** i wybierz odpowiednie pole rejestracji.

1. Zaloguj się jako administrator partnera.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz kartę **Partner**.
1. Wybierz pozycję **+ Dodaj administratora** i wypełnij pole adresu e-mail, kontaktu do powiadomień i szczegółów powiadomienia.
1. Wybierz pozycję **Dodaj**.

### <a name="remove-a-partner-administrator"></a>Usuwanie administratora partnera

Aby wyświetlić listę wszystkich rejestracji skojarzonych z tą samą organizacją partnerską, co bieżący użytkownik, wybierz kartę **Rejestracja** i wybierz odpowiednie pole rejestracji.

1. Zaloguj się jako administrator partnera.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Wybierz kartę **Partner**.
1. W sekcji Administrator wybierz odpowiedni wiersz dla administratora, którego chcesz usunąć.
1. Wybierz symbol X po prawej stronie.
1. Potwierdź, że chcesz usunąć.

## <a name="manage-partner-notifications"></a>Zarządzanie powiadomieniami partnerów

Administratorzy partnerów mogą zarządzać częstotliwością otrzymywania powiadomień o użyciu we własnych rejestracjach. Automatycznie otrzymują oni powiadomienia dotyczące nierozliczonego salda. Mogą zmieniać częstotliwość poszczególnych powiadomień na powiadomienia comiesięczne, cotygodniowe lub codzienne albo wyłączać je całkowicie.

Jeśli użytkownik nie otrzymuje powiadomienia, sprawdź, czy ustawienia powiadomień użytkownika są poprawne, wykonując poniższe kroki.

1. Zaloguj się do witryny Azure EA Portal jako administrator partnera.
2. Wybierz pozycję **Zarządzaj**, a następnie wybierz kartę **Partner**.
3. Przejrzyj listę administratorów w sekcji Administrator.
4. Aby edytować preferencje powiadomień, umieść kursor nad odpowiednim administratorem i wybierz symbol ołówka.
5. Zwiększ częstotliwość powiadamiania i zmodyfikuj powiadomienia dotyczące cyklu życia zgodnie z wymaganiami.
6. W razie potrzeby dodaj kontakt, a następnie wybierz pozycję **Dodaj**.
7. Wybierz pozycję **Zapisz**.

![Przykład pokazujący okno dodawania kontaktu ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Wyświetlanie rejestracji dla administratorów partnerów

Administratorzy partnerów mogą wyświetlić listę wszystkich swoich bezpośrednich i pośrednich rejestracji w witrynie Azure EA Portal. Pola zawierające przegląd każdej rejestracji będą wyświetlane z numerem rejestracji, nazwą rejestracji, saldem i kwotami nadwyżkowymi.

### <a name="view-a-list-of-enrollments"></a>Wyświetlanie listy rejestracji

1. Zaloguj się jako administrator partnera.
1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej stronie.
1. Wybierz kartę **Rejestracja**.
1. Wybierz pole dla rejestracji.

Widok wszystkich rejestracji pozostanie w górnej części strony razem z polami dla każdej rejestracji. Ponadto możesz przechodzić między rejestracjami, wybierając numer bieżącej rejestracji w obszarze nawigacji po lewej stronie. Zostanie wyświetlone wyskakujące okienko umożliwiające wyszukiwanie rejestracji lub wybranie innej rejestracji przez wybranie odpowiedniego pola.

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

Dozwolony jest tylko jeden właściciel konta na subskrypcję.  Dodatkowe role można dodawać przy użyciu funkcji dostępu opartego na rolach lub kontroli dostępu (zarządzanie dostępem i tożsamościami) na karcie subskrypcji w lewym górnym rogu strony [portal.azure.com]] (https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Czy właściciel konta platformy Azure może być wymieniony w więcej niż jednym dziale?

Nie, właściciel konta może być skojarzony tylko z jednym działem. Te zasady ułatwiają dokładne monitorowanie i rozdzielanie kosztów oraz wydatków związanych z działem, do którego jest dopasowany w ramach rejestracji EA w witrynie Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Czy właściciel konta platformy Azure może być wymieniony jako grupa zabezpieczeń?

Nie, właściciel subskrypcji musi być unikatowym kontem Microsoft (MSA) lub uwierzytelnieniem usługi Azure Active Directory (AAD). Aby uwzględnić dziedziczenie w organizacji, możesz rozważyć utworzenie kont ogólnych i użycie usługi AAD w celu zarządzania dostępem do subskrypcji.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Czy użytkownicy indywidualni mogą mieć wiele subskrypcji?

Właściciel konta platformy Azure może utworzyć nieograniczoną liczbę subskrypcji i zarządzać nimi.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Jak mogę wyświetlić wszystkie subskrypcje mojej organizacji lub uzyskać do nich dostęp?

Obecnie robi się to za pomocą zasad. Oznacza to, że należy wymagać tego dla każdej utworzonej subskrypcji i że Twoje konto jest dodawane do roli subskrypcji przy użyciu dostępu opartego na rolach.

### <a name="where-do-i-go-to-create-a-subscription"></a>Gdzie mogę utworzyć subskrypcję?

Aby można było utworzyć subskrypcję oferty w ramach umowy Enterprise Azure (EA), Twoje konto musi zostać dodane do roli właściciela konta przez administratora rejestracji EA w witrynie Azure EA Portal. Następnie należy zalogować się w witrynie Azure EA Portal w celu uzyskania uprawnień do tworzenia subskrypcji typu oferta EA. Zalecamy, aby pierwszą subskrypcję EA utworzyć przy użyciu linku „+ Dodaj subskrypcję” na karcie subskrypcji w witrynie EA Portal.  Jednak po uzyskaniu uprawnień przez konto łatwiejsze może być tworzenie subskrypcji na stronie portal.azure.com na karcie subskrypcji w lewym górnym rogu strony, gdzie można utworzyć subskrypcję i zmienić jej nazwę w jednym kroku.

### <a name="who-can-create-a-subscription"></a>Kto może utworzyć subskrypcję?

Aby utworzyć subskrypcję oferty typu Enterprise Azure, musisz mieć uprawnienia w roli właściciela konta w witrynie [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej o tym, jak [rezerwacje maszyn wirtualnych](ea-portal-vm-reservations.md) mogą pomóc Ci zaoszczędzić pieniądze.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
