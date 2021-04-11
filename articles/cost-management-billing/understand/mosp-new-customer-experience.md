---
title: Rozpoczynanie pracy ze zaktualizowanym kontem rozliczeniowym platformy Azure
description: Rozpocznij pracę ze zaktualizowanym kontem rozliczeniowym platformy Azure, aby poznać zmiany w nowym środowisku rozliczeń i zarządzania kosztami
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: 4f7179a5ad35b4d3ca9a92119fb7b492e2aff779
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122530"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Rozpoczynanie pracy ze zaktualizowanym kontem rozliczeniowym platformy Azure

Zarządzanie kosztami i fakturami to jeden z najważniejszych składników środowiska chmury. Ułatwia on kontrolę i zrozumienie wydatków w chmurze. Aby ułatwić zarządzanie kosztami i fakturami, firma Microsoft aktualizuje Twoje konto rozliczeniowe platformy Azure o ulepszone funkcje zarządzania kosztami i rozliczeń. W tym artykule opisano aktualizacje konta rozliczeniowego i przedstawiono jego nowe możliwości.

> [!IMPORTANT]
> Twoje konto zostanie zaktualizowane po wysłaniu do Ciebie wiadomość e-mail z firmy Microsoft z powiadomieniem o aktualizacjach Twojego konta. Takie powiadomienie jest wysyłane 60 dni przed zaktualizowaniem konta.

## <a name="more-flexibility-with-your-new-billing-account"></a>Większa elastyczność z nowym kontem rozliczeniowym

Na poniższym diagramie porównano stare i nowe konto rozliczeniowe:

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="Diagram przedstawiający porównanie między hierarchią rozliczeń w starym i nowym koncie." border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

Twoje nowe konto rozliczeniowe zawiera co najmniej jeden profil rozliczeniowy, który umożliwia zarządzanie fakturami i formami płatności. Każdy profil rozliczeniowy zawiera co najmniej jedną sekcję faktury, w której można organizować koszty na fakturze profilu rozliczeniowego.

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="Diagram przedstawiający nową hierarchię rozliczeń." border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

Role na koncie rozliczeniowym mają najwyższy poziom uprawnień. Role te powinny być przypisywane do użytkowników, którzy muszą wyświetlać faktury i śledzić koszty dotyczące całego konta, takich jak menedżerowie działu finansów lub IT w organizacji bądź osoby, które zarejestrowały swoje konto. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role konta rozliczeniowego](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Po zaktualizowaniu konta użytkownik mający rolę administratora konta w starym koncie rozliczeniowym ma przydzielaną rolę właściciela w nowym koncie.

## <a name="billing-profiles"></a>Profile rozliczeniowe

Profil rozliczeniowy służy do zarządzania fakturami i formami płatności. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera odpowiednie opłaty z poprzedniego miesiąca za wszystkie subskrypcje skojarzone z profilem rozliczeniowym.

Po zaktualizowaniu konta dla każdej subskrypcji zostanie automatycznie utworzony profil rozliczeniowy. Opłaty za subskrypcję są naliczane w odpowiednim profilu rozliczeniowym i wyświetlane na fakturze dotyczącej tego profilu.

Role w profilach rozliczeniowych mają uprawnienia do wyświetlania faktur i metod płatności oraz zarządzania nimi. Te role należy przypisywać do użytkowników płacących faktury, takich jak członkowie zespołu księgowego w organizacji. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Po zaktualizowaniu konta w każdej subskrypcji, w której nadano innym osobom uprawnienia do [wyświetlania faktur](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice), użytkownikom, którzy mają rolę platformy Azure właściciela, współautora, czytelnika lub czytelnika rozliczeń, jest przypisywana rola czytelnika w odpowiednim profilu rozliczeniowym.

## <a name="invoice-sections"></a>Sekcje faktury

Sekcje faktury umożliwiają organizowanie kosztów na fakturze. Przykładowo możesz potrzebować jednej faktury, ale chcesz zorganizować koszty według działu, zespołu lub projektu. W tym scenariuszu istnieje pojedynczy profil rozliczeniowy, w którym jest tworzona sekcja faktury dla każdego działu, zespołu lub projektu.

Po zaktualizowaniu konta dla każdego profilu rozliczeniowego zostanie utworzona sekcja faktury, do której zostanie przypisana odpowiednia subskrypcja. W miarę dodawania kolejnych subskrypcji można tworzyć dodatkowe sekcje faktury i przypisywać do nich dodane subskrypcje. Sekcje na fakturze profilu rozliczeniowego odzwierciedlają użycie poszczególnych przypisanych do nich subskrypcji.

Role w sekcji faktury mają uprawnienia do kontrolowania, kto tworzy subskrypcje platformy Azure. Te role powinny być przypisywane do użytkowników, którzy konfigurują środowisko platformy Azure dla zespołów w organizacji, np. liderów zespołów inżynierów i architektów technicznych. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role sekcji faktur](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Ulepszone funkcje w nowym środowisku

Nowe środowisko obejmuje następujące funkcje zarządzania kosztami i rozliczeń, które ułatwiają zarządzanie kosztami i fakturami:

#### <a name="invoice-management"></a>Zarządzanie fakturami

**Bardziej przewidywalny miesięczny okres rozliczeniowy** — w nowym koncie okres rozliczeniowy rozpoczyna się od pierwszego dnia miesiąca i kończy ostatniego dnia miesiąca, niezależnie od tego, kiedy nastąpiła rejestracja w celu korzystania z platformy Azure. Faktura będzie generowana na początku każdego miesiąca i będzie zawierać wszystkie opłaty z poprzedniego miesiąca.

**Uzyskaj jedną fakturę miesięczną dla wielu subskrypcji** — na istniejącym koncie otrzymujesz faktury dla każdej subskrypcji platformy Azure. W przypadku zaktualizowania konta istniejące zachowanie jest zachowywane, ale istnieje możliwość ujednolicenia opłat za subskrypcje w ramach jednej faktury. Po zaktualizowaniu konta postępuj zgodnie z poniższymi instrukcjami, aby skonsolidować opłaty na jednej fakturze:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
   ![Zrzut ekranu przedstawiający wyszukiwanie narzędzia Cost Management + Billing w witrynie Azure Portal.](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. Wybierz pozycję **subskrypcje platformy Azure** z lewej strony ekranu. 
4. W tabeli wymieniono subskrypcje platformy Azure, dla których płacisz. W kolumnie profil rozliczania znajdziesz profil rozliczeń, który jest rozliczany dla subskrypcji. Opłaty za subskrypcję są wyświetlane na fakturze dla profilu rozliczania. Aby skonsolidować opłaty dla wszystkich subskrypcji na jednej fakturze, należy połączyć wszystkie subskrypcje z jednym profilem rozliczania.  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Zrzut ekranu przedstawiający listę subskrypcji platformy Azure." lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. Wybierz profil rozliczeń, który ma być używany. 
6. Wybierz subskrypcję, która nie jest połączona z profilem rozliczeń wybranym w kroku 5. Kliknij przycisk wielokropka (trzy kropki) dla subskrypcji. Wybierz pozycję **Zmień sekcję faktury**.  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="Zrzut ekranu pokazujący, gdzie znaleźć opcję zmiany sekcji faktury." lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. Wybierz profil rozliczeń wybrany w kroku #5.  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="Zrzut ekranu pokazujący sposób zmiany sekcji faktury." lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. Wybierz pozycję **Zmień**.
9. Powtórz kroki od 6-8 dla wszystkich innych subskrypcji. 

**Jedna miesięczna faktura za subskrypcje platformy Azure, plany pomocy technicznej i produkty z portalu Azure Marketplace** — otrzymasz jedną miesięczną fakturę obejmującą wszystkie opłaty, w tym opłaty za użycie subskrypcji platformy Azure, plany pomocy technicznej i zakupy w portalu Azure Marketplace.

**Grupowanie kosztów na fakturze według potrzeb** — możesz grupować koszty na fakturze zgodnie ze swoimi potrzebami — według działów, projektów lub zespołów.

**Ustawianie na fakturze opcjonalnego numeru zamówienia zakupu** — aby skojarzyć fakturę z Twoimi wewnętrznymi systemami finansowymi, ustaw numer zamówienia zakupu. Te numery można aktualizować w dowolnym momencie i zarządzać nimi w witrynie Azure Portal.

#### <a name="payment-management"></a>Zarządzanie płatnościami

**Natychmiastowe płatności za fakturę za pomocą karty kredytowej** — nie musisz czekać, aż automatyczne płatności obciążą Twoją kartę kredytową. Możesz użyć dowolnej karty kredytowej, aby uregulować należne lub zaległe opłaty za faktury w witrynie Azure Portal.

**Śledzenie wszystkich płatności mających zastosowanie do konta** — możesz wyświetlać wszystkie płatności mające zastosowanie do Twojego konta, w tym informacje, takie jak forma płatności, zapłacona kwota i data dokonania płatności w witrynie Azure Portal.

#### <a name="cost-management"></a>Zarządzanie kosztami

**Ustawianie harmonogramu comiesięcznego eksportu danych użycia na konto magazynu** — automatycznie publikuj dane o kosztach i użyciu na koncie magazynu w systemie codziennym, cotygodniowym lub comiesięcznym.

#### <a name="account-and-subscription-management"></a>Zarządzanie kontami i subskrypcjami

**Przypisywanie wielu administratorów do wykonywania operacji rozliczeniowych** — przypisz uprawnienia do rozliczeń do wielu użytkowników, którzy będą zarządzać rozliczeniami konta. Uzyskaj elastyczność, przyznając innym osobom uprawnienia do odczytu, zapisu lub oba.

**Tworzenie kolejnych subskrypcji bezpośrednio w witrynie Azure Portal** — twórz wszystkie subskrypcje, dokonując jednego wyboru w witrynie Azure Portal.

#### <a name="api-support"></a>Obsługa interfejsu API

**Wykonywanie operacji rozliczeniowych i związanych z zarządzaniem kosztami za pomocą interfejsów API, zestawów SDK i programu PowerShell** — użyj funkcji zarządzania kosztami, rozliczeń i interfejsów API zużycia, aby ściągnąć dane dotyczące rozliczeń i kosztów do preferowanych narzędzi do analizy danych.

**Wykonywanie wszystkich operacji subskrypcji za pomocą interfejsów API, zestawów SDK i programu PowerShell** — używaj interfejsów API subskrypcji platformy Azure, aby automatyzować zarządzanie subskrypcjami platformy Azure, w tym tworzenie, zmienianie nazw i anulowanie subskrypcji.

## <a name="get-prepared-for-your-new-experience"></a>Przygotowywanie się na nowe środowisko

Zalecamy wykonanie następujących czynności w ramach przygotowywania się do nowego środowiska:

**Miesięczny okres rozliczeniowy i inna data faktury**

W nowym środowisku faktura będzie generowana około dziewiątego dnia każdego miesiąca i będzie zawierać wszystkie opłaty z poprzedniego miesiąca. Ta data może się różnić od daty generowania faktury w starym koncie. Jeśli udostępniasz faktury innym osobom, powiadom je o zmianie daty.


**Faktury w pierwszym miesiącu po migracji**

Dzień, w którym Twoje konto zostało zaktualizowane, Twoje istniejące nieobciążane opłaty są finalizowane i otrzymasz faktury za te opłaty w dniu, w którym zazwyczaj otrzymujesz faktury. Na przykład Jan ma dwie subskrypcje platformy Azure — z licencji na platformę Azure z cyklem rozliczeniowym od piątego dnia miesiąca do czwartego dnia następnego miesiąca i z systemu Azure, z upływem dziesiątego dnia miesiąca do dziewiątego dnia następnego miesiąca. Jan pobiera faktury dla subskrypcji platformy Azure zwykle w ciągu piątego miesiąca. Jeśli konto Jan zostanie zaktualizowane 4 kwietnia, opłaty za usługę Azure sub 01 od 5 marca do 4 kwietnia i opłaty za usługę Azure sub 02 od dnia 10 marca do czwartego dnia zostaną zakończone. Jan otrzyma dwie faktury, jedną dla każdej z nich w dniu 5 kwietnia. Po zaktualizowaniu konta cykl rozliczeniowy firmy Jan będzie oparty na miesiącu kalendarzowym i będzie obejmować wszystkie opłaty wynikające z początku miesiąca kalendarzowego do końca tego miesiąca kalendarzowego.  Faktury za poprzednie opłaty za miesiąc w kalendarzu są dostępne w dziewiątym miesiącu każdego miesiąca. W powyższym przykładzie Jan otrzyma kolejną fakturę w dniu piątym od 5 kwietnia do 30 kwietnia. 


**Nowe interfejsy API rozliczeń i zarządzania kosztami**

Jeśli korzystasz z usługi Cost Management lub interfejsów API rozliczeń do wysyłania zapytań i aktualizowania danych dotyczących rozliczeń bądź kosztów, musisz używać nowych interfejsów API. W poniższej tabeli przedstawiono interfejsy API, które nie będą działać z nowym kontem rozliczeniowym, oraz zmiany, które należy wprowadzić w nowym koncie rozliczeniowym.

|Interfejs API | Zmiany  |
|---------|---------|
|[Billing Accounts — List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | W interfejsie API Billing Accounts — List stare konto rozliczeniowe ma parametr agreementType o wartości **MicrosoftOnlineServiceProgram**, a nowe konto rozliczeniowe będzie miało parametr agreementType o wartości **MicrosoftCustomerAgreement**. Jeśli korzystasz z zależności od parametru agreementType, zaktualizuj ją. |
|[Invoices — List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Ten interfejs API zwróci tylko faktury, które zostały wygenerowane przed zaktualizowaniem konta. Aby zwrócić faktury wygenerowane w nowym koncie rozliczeniowym, należy użyć interfejsu API [Invoices — List By Billing Account](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |


## <a name="cost-management-updates-after-account-update"></a>Aktualizacje usługi Cost Management po aktualizacji konta

Zaktualizowane konto rozliczeniowe platformy Azure dla Umowy z Klientem Microsoft daje dostęp do nowych i rozbudowanych funkcji Cost Management w witrynie Azure Portal, które nie były dostępne w ramach konta z płatnością zgodnie z rzeczywistym użyciem.

### <a name="new-capabilities"></a>Nowe możliwości

Poniższe nowe możliwości są dostępne na koncie rozliczeniowym platformy Azure.

#### <a name="new-billing-scopes"></a>Nowe zakresy rozliczeniowe

W ramach zaktualizowanego konta masz nowe zakresy w funkcji Cost Management + Billing. Oprócz tego, ze pomagają one z hierarchiczną organizacją i fakturowaniem, stanowią też sposób wyświetlania połączonych opłat z wielu subskrypcji bazowych. Aby uzyskać więcej informacji na temat zakresów rozliczeniowych, zobacz [Zakresy Umowy z Klientem Microsoft](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Możesz również uzyskać dostęp do interfejsów API usługi Cost Management, aby uzyskać połączone widoki kosztów w wyższych zakresach. Wszystkie interfejsy API usługi Cost Management korzystające z zakresu subskrypcji są nadal dostępne z niewielkimi zmianami w schemacie. Aby uzyskać więcej informacji na temat interfejsów API, zobacz [Interfejsy API usługi Azure Cost Management](/rest/api/cost-management/) i [Interfejsy API użycia platformy Azure](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Alokacja kosztów

Dzięki zaktualizowanemu kontu można używać funkcji alokacji kosztów do dystrybucji kosztów z usług udostępnionych w organizacji. Aby uzyskać więcej informacji na temat alokowania kosztów, zobacz [Tworzenie reguł alokacji kosztów platformy Azure i zarządzanie nimi](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Łącznik usługi Azure Cost Management dla programu Power BI Desktop ułatwia tworzenie niestandardowych wizualizacji i raportów dotyczących użycia oraz wydatków na platformie Azure. Dostęp do danych dotyczących kosztów i użycia można uzyskać po nawiązaniu połączenia ze zaktualizowanym kontem. Aby uzyskać więcej informacji na temat łącznika usługi Azure Cost Management dla programu Power BI Desktop, zobacz [Tworzenie wizualizacji i raportów za pomocą łącznika usługi Azure Cost Management w programie Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Zaktualizowane funkcje

Następujące zaktualizowane funkcje są dostępne na koncie rozliczeniowym platformy Azure.

#### <a name="cost-analysis"></a>Analiza kosztów

Możesz nadal wyświetlać i śledzić miesięczne koszty użycia, a do tego teraz możesz wyświetlać rezerwacje i koszty zakupów w portalu Marketplace w analizie kosztów.

Na zaktualizowanym koncie otrzymujesz jedną fakturę ze wszystkimi opłatami za platformę Azure. Teraz masz też uproszczony widok pojedynczego miesięcznego kalendarza zastępujący wcześniejszy widok okresów rozliczeniowych.

Na przykład jeśli okres rozliczeniowy był od 24 listopada do 23 grudnia dla starego konta, po uaktualnieniu okres będzie od 1 listopada do 30 listopada, od 1 grudnia do 31 grudnia i tak dalej.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Zrzut ekranu przedstawiający porównanie starych i nowych okresów rozliczeniowych." lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budżety

Możesz teraz tworzyć budżety dla konta rozliczeniowego, co pozwala na śledzenie kosztów w ramach subskrypcji. Dzięki budżetom możesz również mieć koszty zakupu pod kontrolą. Aby uzyskać więcej informacji na temat budżetów, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Eksporty

Nowe konto rozliczeniowe zapewnia ulepszone funkcje eksportu. Możesz na przykład utworzyć eksporty dla rzeczywistych kosztów obejmujących zakupy lub amortyzowane koszty (rozproszenie kosztów zakupu rezerwacji w okresie zakupu). Możesz również utworzyć eksport dla konta rozliczeniowego, aby uzyskać dane dotyczące użycia i opłat dla wszystkich subskrypcji na koncie rozliczeniowym. Aby uzyskać więcej informacji na temat eksportów, zobacz [Tworzenie eksportowanych danych i zarządzanie nimi](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Eksporty utworzone przed aktualizacją konta przy użyciu typu **Miesięczny eksport kosztów z ostatniego miesiąca** będą eksportować dane dla ostatniego miesiąca kalendarzowego, a nie ostatniego okresu rozliczeniowego.

Na przykład w przypadku okresu rozliczeniowego od 23 grudnia do 22 stycznia wyeksportowany plik CSV będzie zawierał dane dotyczące kosztów i użycia dla tego okresu. Po aktualizacji eksport będzie zawierać dane dotyczące miesiąca kalendarzowego. Na przykład od 1 stycznia do 31 stycznia i tak dalej.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Zrzuty ekranu przedstawiające porównanie starych i nowych szczegółów eksportu." lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Dodatkowe informacje

W poniższych sekcjach znajdują się dodatkowe informacje o nowym środowisku.

**Brak przestojów usługi** Usługi platformy Azure w ramach Twojej subskrypcji będą działały bez jakiejkolwiek przerwy. Aktualizacją jest objęte tylko Twoje środowisko rozliczeniowe. Istniejące zasoby, grupy zasobów czy grupy zarządzania pozostaną niezmienione.

**Brak zmian w zasobach platformy Azure** Ta aktualizacja nie ma wpływu na dostęp do zasobów platformy Azure ustawiony przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure.

**Wcześniejsze faktury są dostępne w nowym środowisku** Faktury wygenerowane przed zaktualizowaniem konta będą nadal dostępne w witrynie Azure Portal.

**Faktury dla konta zaktualizowanego w połowie miesiąca** Jeśli Twoje konto zostanie zaktualizowane w połowie miesiąca, otrzymasz jedną fakturę za opłaty naliczone do momentu zaktualizowania Twojego konta. Za pozostałą część miesiąca otrzymasz kolejną fakturę. Na przykład Twoje konto ma jedną subskrypcję i jest aktualizowane 15 września. Otrzymasz jedną fakturę za opłaty naliczone do 15 września. Otrzymasz kolejną fakturę za okres od 15 września do 30 września. Od października będziesz otrzymywać jedną fakturę miesięcznie.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat konta rozliczeniowego.

- [Role administracyjne w nowym koncie rozliczeniowym](../manage/understand-mca-roles.md)
- [Tworzenie dodatkowej subskrypcji platformy Azure na nowym koncie rozliczeniowym](../manage/create-subscription.md)
- [Tworzenie sekcji na fakturze w celu organizacji kosztów](../manage/mca-section-invoice.md)