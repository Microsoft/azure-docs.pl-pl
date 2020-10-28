---
title: Często zadawane pytania dotyczące platformy Cloudyn na platformie Azure
description: Dowiedz się, jak za pomocą portalu Cloudyn rozwiązywać typowe problemy z pośrednią konfiguracją przedsiębiorstwa, oraz poznaj odpowiedzi na inne często zadawane pytania.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 92a62de4223d2ec6d0441840b90fdce20c656d02
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546232"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Często zadawane pytania dotyczące platformy Cloudyn

W tym artykule podano odpowiedzi na niektóre często zadawane pytania dotyczące platformy Cloudyn. Jeśli masz pytania na temat platformy Cloudyn, możesz je zadawać na stronie [często zadawanych pytań dotyczących platformy Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak mogę rozwiązać typowe problemy z pośrednią konfiguracją przedsiębiorstwa?

Przy pierwszym użyciu portalu Cloudyn możesz zobaczyć poniższe komunikaty, jeśli jesteś użytkownikiem z umową Enterprise Agreement lub dostawcą rozwiązań w chmurze (CSP, Cloud Solution Provider):

- Komunikat „Wybrany klucz interfejsu API nie jest kluczem rejestracji najwyższego poziomu” wyświetlany w kreatorze **Konfigurowanie platformy Cloudyn** .
- Komunikat „Bezpośrednia rejestracja — nie” wyświetlany w portalu umowy Enterprise Agreement.
- Komunikat „Nie znaleziono danych użycia z ostatnich 30 dni. Skontaktuj się z dystrybutorem i upewnij się, że dla konta platformy Azure włączono znaczniki” wyświetlany w portalu Cloudyn.

Poprzednie komunikaty oznaczają, że umowa Azure Enterprise Agreement została zakupiona od odsprzedawcy lub dostawcy rozwiązań w chmurze. Odsprzedawca lub dostawca rozwiązań w chmurze musi włączyć _znaczniki_ dla konta platformy Azure, aby można było wyświetlać dane na platformie Cloudyn.

Oto sposoby rozwiązywania tych problemów:

1. Odsprzedawca musi włączyć _znaczniki_ dla Twojego konta. Aby uzyskać instrukcje, zapoznaj się z dokumentem [Indirect Customer Onboarding Guide (Przewodnik dołączania klienta pośredniego)](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Wygenerujesz klucz umowy Azure Enterprise Agreement do użycia na platformie Cloudyn.

Tylko administrator usług platformy Azure może włączyć platformę Cloudyn. Uprawnienia współadministratora są niewystarczające.

Przed wygenerowaniem klucza interfejsu API umowy Azure Enterprise Agreement w celu skonfigurowania platformy Cloudyn należy włączyć interfejs API rozliczeń platformy Azure, wykonując następujące instrukcje:

- [Overview of Reporting APIs for Enterprise customers (Omówienie interfejsów API raportowania dla klientów korporacyjnych)](../manage/enterprise-api.md)
- [Interfejs API raportowania w witrynie Microsoft Azure dla przedsiębiorstw](https://ea.azure.com/helpdocs/reportingAPI) w obszarze **Włączanie dostępu danych do interfejsu API**


Być może trzeba będzie również nadać administratorom działów, właścicielom kont i administratorom przedsiębiorstwa uprawnienia do _wyświetlania opłat_ przy użyciu interfejsu API rozliczeń.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Dlaczego nie widzę zaleceń optymalizatora?

Zalecenia są dostępne tylko dla aktywowanych kont. W kategoriach raportów **Optymalizator** nie będą wyświetlane żadne informacje w przypadku *nieaktywowanych* kont, w tym:

- Menedżer optymalizacji
- Optymalizacja rozmiaru
- Nieefektywności

Jeśli nie możesz wyświetlić żadnych danych zaleceń optymalizatora, najprawdopodobniej masz konta, które nie są aktywowane. Aby aktywować konto, musisz je zarejestrować przy użyciu poświadczeń platformy Azure.

Aby aktywować konto:

1.    W portalu Cloudyn kliknij pozycję **Ustawienia** w górnym prawym rogu i wybierz pozycję **Konta w chmurze** .
2.    Na karcie Konta platformy Microsoft Azure wyszukaj konta, które mają **nieaktywowaną** subskrypcję.
3.    Po prawej stronie nieaktywowanego konta kliknij symbol **edycji** , który przypomina ołówek.
4.    Identyfikator dzierżawy i identyfikator stawki zostaną automatycznie wykryte. Kliknij przycisk **Dalej** .
5.    Nastąpi przekierowywanie do witryny Azure Portal. Zaloguj się do portalu i autoryzuj dostęp modułu zbierającego platformy Cloudyn do danych platformy Azure.
6.    Następnie nastąpi przekierowanie do strony zarządzania kontami platformy Cloudyn, a subskrypcja zostanie zaktualizowana przy użyciu stanu konta **Aktywne** . Będzie wyświetlany symbol zielonego znacznika wyboru.
7.    Jeśli nie widzisz symbolu zielonego znacznika wyboru dla co najmniej jednej z subskrypcji, oznacza to, że nie masz uprawnień do tworzenia aplikacji czytnika (CloudynCollector) dla subskrypcji. Użytkownik z wyższymi uprawnieniami do subskrypcji musi powtórzyć kroki 3 i 4.  

Po wykonaniu powyższych kroków zalecenia optymalizatora staną się widoczne w ciągu od jednego do dwóch dni. Jednak może upłynąć do pięciu dni, zanim staną się dostępne pełne dane optymalizacji.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak mogę włączyć zawieszonych lub zablokowanych użytkowników?

Najpierw przyjrzyjmy się najbardziej typowemu scenariuszowi powodującemu uzyskanie przez konta użytkowników stanu *initiallySuspended* .

> Admin1 to użytkownik z umową Enterprise Agreement lub dostawca Microsoft Cloud Solution Provider. Jego organizacja jest gotowa do rozpoczęcia korzystania z platformy Cloudyn.  Rejestruje się za pośrednictwem witryny Azure Portal i loguje do portalu Cloudyn. Ponieważ jest osobą, która rejestruje usługę Cloudyn i loguje się do portalu Cloudyn, użytkownik Admin1 staje się *głównym administratorem* . Użytkownik Admin1 nie tworzy żadnych kont użytkowników. Jednak przy użyciu portalu Cloudyn tworzy konta platformy Azure i konfiguruje hierarchię jednostek. Użytkownik Admin1 informuje użytkownika Admin2, administratora dzierżawy, że musi on zarejestrować się na platformie Cloudyn i zalogować do portalu Cloudyn.
>
> Użytkownik Admin2 rejestruje się za pomocą witryny Azure Portal. Jednak podczas próby zalogowania się do portalu Cloudyn pojawia się błąd informujący o tym, że konto jest **zawieszone** . Główny administrator, Admin1, jest powiadamiany o zawieszeniu konta. Użytkownik Admin1 musi aktywować konto użytkownika Admin2 i przyznać *dostęp jednostki administracyjnej* dla odpowiednich jednostek, a także zezwolić na dostęp do zarządzania użytkownikami i aktywowanie konta użytkownika.


W przypadku otrzymania alertu z żądaniem zezwolenia na dostęp użytkownika należy aktywować konto użytkownika.

Aby aktywować konto użytkownika:

1. Zaloguj się do platformy Cloudyn przy użyciu konta użytkownika administracyjnego platformy Azure, którego użyto do skonfigurowania platformy Cloudyn. Alternatywnie zaloguj się przy użyciu konta użytkownika, któremu przyznano dostęp administratora.
2. Wybierz symbol koła zębatego w prawym górnym rogu i wybierz pozycję **Zarządzanie użytkownikami** .
3. Znajdź użytkownika, wybierz symbol ołówka, a następnie edytuj użytkownika.
4. W obszarze **Stan użytkownika** zmień stan z **Zawieszony** na **Aktywny** .

Konta użytkowników platformy Cloudyn łączą się przy użyciu logowania jednokrotnego z platformą Azure. Jeśli użytkownik popełni błąd podczas wpisywania hasła, jego konto platformy Cloudyn może zostać zablokowane, choć nadal będzie mógł uzyskiwać dostęp do platformy Azure.

Konto może zostać zablokowane w przypadku zmiany adresu e-mail na platformie Cloudyn na adres inny niż adres domyślny na platformie Azure. Może być wyświetlana informacja „stan initiallySuspended”. W przypadku zablokowania konta użytkownika skontaktuj się z alternatywnym administratorem w celu zresetowania konta.

Zalecamy utworzenie co najmniej dwóch kont administratorów platformy Cloudyn na wypadek, gdyby jedno z kont zostało zablokowane.

Jeśli nie możesz zalogować się do portalu Cloudyn, upewnij się, że używasz poprawnego adresu URL, aby zalogować się do platformy Cloudyn. Użyj witryny [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Unikaj używania bezpośredniego adresu URL platformy Cloudyn: `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak mogę aktywować nieaktywowane konta przy użyciu poświadczeń platformy Azure?

Gdy tylko konta platformy Azure zostaną odnalezione przez platformę Cloudyn, dane kosztów będą od razu udostępniane w raportach opartych na kosztach. Jednak aby platforma Cloudyn udostępniała dane dotyczące użycia i wydajności, należy zarejestrować poświadczenia platformy Azure dla kont. Aby uzyskać instrukcje, zobacz [Dodawanie konta lub aktualizowanie subskrypcji](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Aby dodać poświadczenia platformy Azure dla konta, w portalu Cloudyn wybierz symbol edycji z prawej strony nazwy konta, a nie subskrypcji.

Dopóki poświadczenia platformy Azure nie zostaną dodane do platformy Cloudyn, konto będzie wyświetlane jako _nieaktywowane_ .

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak mogę dodać wiele kont i jednostek do istniejącej subskrypcji?

Dodatkowe jednostki są używane do dodawania kolejnych umów Enterprise Agreement do subskrypcji platformy Cloudyn. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostek i zarządzanie nimi](tutorial-user-access.md#create-and-manage-entities).

Dla dostawców CSP:

Aby dodać kolejne konta CSP do jednostki, wybierz pozycję **Dostęp dostawcy usług zarządzanych** zamiast **Przedsiębiorstwo** podczas tworzenia nowej jednostki. Jeśli konto jest zarejestrowane jako konto z umową Enterprise Agreement, a chcesz dodać poświadczenia CSP, może być konieczna modyfikacja ustawień konta przez personel pomocy technicznej platformy Cloudyn. Jeśli jesteś subskrybentem mającym płatną subskrypcję platformy Azure, możesz utworzyć nowy wniosek o pomoc techniczną w witrynie Azure Portal. Wybierz pozycję **Pomoc i obsługa techniczna** , a następnie pozycję **Nowy wniosek o pomoc techniczną** .

## <a name="currency-symbols-in-cloudyn-reports"></a>Symbole walut w raportach Cloudyn

Być może masz wiele kont platformy Azure korzystających z różnych walut. Jednak raporty kosztów na platformie Cloudyn nie wyświetlają więcej niż jednego typu waluty na raport.

Jeśli masz wiele subskrypcji korzystających z różnych walut, wartości walut w jednostce nadrzędnej i jej jednostce podrzędnej są wyświetlane w USD ( **$** ). Sugerowane najlepsze rozwiązanie to unikanie korzystania z różnych walut w tej samej hierarchii jednostek. Innymi słowy wszystkie subskrypcje zorganizowane w strukturze jednostek powinny używać tej samej waluty.

Platforma Cloudyn automatycznie wykrywa walutę subskrypcji Enterprise Agreement i prawidłowo przedstawia ją w raportach.  Jednak w przypadku kont platformy Azure Web-Direct i CSP platforma Cloudyn wyświetla tylko walutę USD ( **$** ).

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Jakie są osie czasu odświeżania danych platformy Cloudyn?

Platforma Cloudyn ma następujące osie czasu odświeżania danych:

- **Początkowa** : Po skonfigurowaniu mogą upłynąć nawet 24 godziny, zanim będzie możliwe wyświetlanie danych kosztów na platformie Cloudyn. Zebranie wystarczającej ilości danych, aby platforma Cloudyn wyświetlała zalecenia dotyczące rozmiaru, może zająć do 10 dni.
- **Codzienna** : Od dziesiątego dnia do końca każdego miesiąca platforma Cloudyn powinna wyświetlać aktualne dane z poprzedniego dnia mniej więcej od godziny UTC+3 następnego dnia.
- **Miesięczna** : Od pierwszego do dziesiątego dnia każdego miesiąca platforma Cloudyn może pokazywać tylko dane do końca poprzedniego miesiąca.

Platforma Cloudyn przetwarza dane z poprzedniego dnia, gdy dostępne są pełne dane z poprzedniego dnia. Dane z poprzedniego dnia są zwykle dostępne na platformie Cloudyn około godziny UTC+3 każdego dnia. Przetworzenie niektórych danych, takich jak tagi, może zająć kolejne 24 godziny.

Danych z bieżącego miesiąca nie można zbierać na początku każdego miesiąca. W tym okresie dostawcy usług finalizują rozliczenia za poprzedni miesiąc. Dane z poprzedniego miesiąca są wyświetlane na platformie Cloudyn od 5 do 10 dni po rozpoczęciu każdego miesiąca. W tym czasie mogą być widoczne tylko koszty zamortyzowane z poprzedniego miesiąca. Codzienne dane dotyczące rozliczeń i użycia mogą nie być wyświetlane. Gdy dane staną się dostępne, platforma Cloudyn przetworzy je wstecznie. Po przetworzeniu wszystkie miesięczne dane staną się widoczne między piątym i dziesiątym dniem każdego miesiąca.

W przypadku opóźnienia wysyłania danych z platformy Azure do platformy Cloudyn dane są nadal rejestrowane na platformie Azure. Dane są przesyłane do platformy Cloudyn po przywróceniu połączenia.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Wahania kosztów w raportach kosztów Cloudyn

Koszty w raportach kosztów mogą się zmieniać, gdy dostawcy usług w chmurze wyślą zaktualizowane pliki rozliczeń. Wahania kosztów występują po otrzymaniu nowych plików od dostawcy usług w chmurze poza zwykłym codziennym lub comiesięcznym harmonogramem raportowania. Zmiany kosztów nie są spowodowane ponownym obliczaniem przez platformę Cloudyn.

Wszystkie pliki rozliczeń wysyłane przez dostawcę usług w chmurze w ciągu miesiąca to szacunki dziennych kosztów. Czasami dane są aktualizowane często — niekiedy kilka razy dziennie. Aktualizacje są wykonywane częściej w przypadku platformy AWS niż platformy Azure. Sumy kosztów nie powinny się zmieniać po ukończeniu obliczeń dotyczących rozliczeń za poprzedni miesiąc i otrzymaniu końcowego pliku rozliczeń. Zwykle ma to miejsce do 10 dnia miesiąca.

Zmiany występują, jeśli otrzymasz korekty kosztów od dostawcy usług w chmurze. Przykładem takiej sytuacji jest uzyskanie środków. Zmiany mogą nastąpić wiele miesięcy po zamknięciu danego miesiąca. Zmiany są widoczne za każdym razem, gdy dostawca usług w chmurze dokona ponownego obliczenia. Platforma Cloudyn aktualizuje dane historyczne, aby upewnić się, że wszystkie korekty zostały ponownie obliczone. Sprawdza także dokładność kosztów wyświetlanych w raportach.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak bezpośredni dostawca CSP może skonfigurować dostęp do platformy Cloudyn dla pośrednich klientów lub partnerów dostawcy CSP?

Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu dla pośrednich dostawców CSP na platformie Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co powoduje wyświetlenie elementu menu Optymalizator?

Po dodaniu dostępu do usługi Azure Resource Manager i zebraniu danych opcja **Optymalizator** powinna być widoczna. Aby aktywować dostęp do usługi Azure Resource Manager, zobacz [Jak mogę aktywować nieaktywowane konta przy użyciu poświadczeń platformy Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Czy platforma Cloudyn jest oparta na agentach?

Nie. Agenci nie są używani. Dane metryk maszyn wirtualnych platformy Azure są zbierane z interfejsu API usługi Microsoft Insights. Aby zbierać dane metryk z maszyn wirtualnych platformy Azure, muszą one mieć włączone ustawienia diagnostyki.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Czy w jednym raporcie platformy Cloudyn jest wyświetlana więcej niż jedna dzierżawa usługi AD?

Tak. Możesz [utworzyć odpowiednią jednostkę konta w chmurze](tutorial-user-access.md#create-and-manage-entities) dla każdej dzierżawy usługi AD. Następnie można wyświetlać dane wszystkich dzierżaw usługi Azure AD i innych dostawców platform w chmurze, w tym Amazon Web Services i Google Cloud Platform.
