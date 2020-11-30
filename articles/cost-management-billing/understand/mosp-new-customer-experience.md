---
title: Rozpoczynanie pracy ze zaktualizowanym kontem rozliczeniowym platformy Azure
description: Rozpocznij pracę ze zaktualizowanym kontem rozliczeniowym platformy Azure, aby poznać zmiany w nowym środowisku rozliczeń i zarządzania kosztami
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 7bec455b804d1f4b13ab7e13677092077214a121
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965854"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Rozpoczynanie pracy ze zaktualizowanym kontem rozliczeniowym platformy Azure

Zarządzanie kosztami i fakturami to jeden z najważniejszych składników środowiska chmury. Ułatwia on kontrolę i zrozumienie wydatków w chmurze. Aby ułatwić zarządzanie kosztami i fakturami, firma Microsoft aktualizuje Twoje konto rozliczeniowe platformy Azure o ulepszone funkcje zarządzania kosztami i rozliczeń. W tym artykule opisano aktualizacje konta rozliczeniowego i przedstawiono jego nowe możliwości.

> [!IMPORTANT]
> Twoje konto zostanie zaktualizowane po wysłaniu do Ciebie wiadomość e-mail z firmy Microsoft z powiadomieniem o aktualizacjach Twojego konta. Takie powiadomienie jest wysyłane 60 dni przed zaktualizowaniem konta.

## <a name="more-flexibility-with-your-new-billing-account"></a>Większa elastyczność z nowym kontem rozliczeniowym

Na poniższym diagramie porównano stare i nowe konto rozliczeniowe:

![Diagram przedstawiający porównanie hierarchii rozliczeń w starym i nowym koncie](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Twoje nowe konto rozliczeniowe zawiera co najmniej jeden profil rozliczeniowy, który umożliwia zarządzanie fakturami i formami płatności. Każdy profil rozliczeniowy zawiera co najmniej jedną sekcję faktury, w której można organizować koszty na fakturze profilu rozliczeniowego.

![Diagram przedstawiający nową hierarchię rozliczeń](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

Role na koncie rozliczeniowym mają najwyższy poziom uprawnień. Role te powinny być przypisywane do użytkowników, którzy muszą wyświetlać faktury i śledzić koszty dotyczące całego konta, takich jak menedżerowie działu finansów lub IT w organizacji bądź osoby, które zarejestrowały swoje konto. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role konta rozliczeniowego](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Po zaktualizowaniu konta użytkownik mający rolę administratora konta w starym koncie rozliczeniowym ma przydzielaną rolę właściciela w nowym koncie.

## <a name="billing-profiles"></a>Profile rozliczeniowe

Profil rozliczeniowy służy do zarządzania fakturami i formami płatności. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera odpowiednie opłaty z poprzedniego miesiąca za wszystkie subskrypcje skojarzone z profilem rozliczeniowym.

Po zaktualizowaniu konta dla każdej subskrypcji zostanie automatycznie utworzony profil rozliczeniowy. Opłaty za subskrypcję są naliczane w odpowiednim profilu rozliczeniowym i wyświetlane na fakturze dotyczącej tego profilu.

Role w profilach rozliczeniowych mają uprawnienia do wyświetlania faktur i metod płatności oraz zarządzania nimi. Te role należy przypisywać do użytkowników płacących faktury, takich jak członkowie zespołu księgowego w organizacji. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). 

Po zaktualizowaniu konta w każdej subskrypcji, w której nadano innym osobom uprawnienia do [wyświetlania faktur](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice), użytkownikom, którzy mają rolę platformy Azure właściciela, współautora, czytelnika lub czytelnika rozliczeń, jest przypisywana rola czytelnika w odpowiednim profilu rozliczeniowym.

## <a name="invoice-sections"></a>Sekcje faktury

Sekcje faktury umożliwiają organizowanie kosztów na fakturze. Przykładowo możesz potrzebować jednej faktury, ale chcesz zorganizować koszty według działu, zespołu lub projektu. W tym scenariuszu istnieje pojedynczy profil rozliczeniowy, w którym jest tworzona sekcja faktury dla każdego działu, zespołu lub projektu.

Po zaktualizowaniu konta dla każdego profilu rozliczeniowego zostanie utworzona sekcja faktury, do której zostanie przypisana odpowiednia subskrypcja. W miarę dodawania kolejnych subskrypcji można tworzyć dodatkowe sekcje faktury i przypisywać do nich dodane subskrypcje. Sekcje na fakturze profilu rozliczeniowego odzwierciedlają użycie poszczególnych przypisanych do nich subskrypcji.

Role w sekcji faktury mają uprawnienia do kontrolowania, kto tworzy subskrypcje platformy Azure. Te role powinny być przypisywane do użytkowników, którzy konfigurują środowisko platformy Azure dla zespołów w organizacji, np. liderów zespołów inżynierów i architektów technicznych. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role sekcji faktur](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Ulepszone funkcje w nowym środowisku

Nowe środowisko obejmuje następujące funkcje zarządzania kosztami i rozliczeń, które ułatwiają zarządzanie kosztami i fakturami:

#### <a name="invoice-management"></a>Zarządzanie fakturami

**Bardziej przewidywalny miesięczny okres rozliczeniowy** — w nowym koncie okres rozliczeniowy rozpoczyna się od pierwszego dnia miesiąca i kończy ostatniego dnia miesiąca, niezależnie od tego, kiedy nastąpiła rejestracja w celu korzystania z platformy Azure. Faktura będzie generowana na początku każdego miesiąca i będzie zawierać wszystkie opłaty z poprzedniego miesiąca.

**Pojedyncza miesięczna faktura za wiele subskrypcji** — możesz wybrać, czy będziesz otrzymywać jedną miesięczną fakturę dla każdej subskrypcji, czy pojedynczą fakturę za wiele subskrypcji.

**Jedna miesięczna faktura za subskrypcje platformy Azure, plany pomocy technicznej i produkty z portalu Azure Marketplace** — otrzymasz jedną miesięczną fakturę obejmującą wszystkie opłaty, w tym opłaty za użycie subskrypcji platformy Azure, plany pomocy technicznej i zakupy w portalu Azure Marketplace.

**Grupowanie kosztów na fakturze według potrzeb** — możesz grupować koszty na fakturze zgodnie ze swoimi potrzebami — według działów, projektów lub zespołów.

**Ustawianie na fakturze opcjonalnego numeru zamówienia zakupu** — aby skojarzyć fakturę z Twoimi wewnętrznymi systemami finansowymi, ustaw numer zamówienia zakupu. Te numery można aktualizować w dowolnym momencie i zarządzać nimi w witrynie Azure Portal.

#### <a name="payment-management"></a>Zarządzanie płatnościami

**Natychmiastowe płatności za fakturę za pomocą karty kredytowej** — nie musisz czekać, aż automatyczne płatności obciążą Twoją kartę kredytową. Możesz użyć dowolnej karty kredytowej, aby uregulować należne lub zaległe opłaty za faktury w witrynie Azure Portal.

**Śledzenie wszystkich płatności mających zastosowanie do konta** — możesz wyświetlać wszystkie płatności mające zastosowanie do Twojego konta, w tym informacje, takie jak forma płatności, zapłacona kwota i data dokonania płatności w witrynie Azure Portal.

#### <a name="cost-management"></a>Zarządzanie kosztami

**Ustawianie harmonogramu comiesięcznego eksportu danych użycia na konto magazynu** — automatycznie publikuj dane o kosztach i użyciu na koncie magazynu w systemie codziennym, cotygodniowym lub comiesięcznym.

#### <a name="account-and-subscription-management"></a>Zarządzanie kontami i subskrypcjami

**Przypisywanie wielu administratorów do wykonywania operacji rozliczeniowych** — przypisz uprawnienia do rozliczeń do wielu użytkowników, którzy będą zarządzać rozliczeniami konta. Uzyskaj elastyczność, przypisując innym osobom uprawnienia do odczytu, zapisu lub oba te uprawnienia.

**Tworzenie dodatkowych subskrypcji bezpośrednio w witrynie Azure Portal** — twórz wszystkie subskrypcje jednym kliknięciem w witrynie Azure Portal.

#### <a name="api-support"></a>Obsługa interfejsu API

**Wykonywanie operacji rozliczeniowych i związanych z zarządzaniem kosztami za pomocą interfejsów API, zestawów SDK i programu PowerShell** — Użyj funkcji zarządzania kosztami, rozliczeń i interfejsów API zużycia, aby ściągnąć dane dotyczące rozliczeń i kosztów do preferowanych narzędzi do analizy danych.

**Wykonywanie wszystkich operacji subskrypcji za pomocą interfejsów API, zestawów SDK i programu PowerShell** — używaj interfejsów API subskrypcji platformy Azure, aby automatyzować zarządzanie subskrypcjami platformy Azure, w tym tworzenie, zmienianie nazw i anulowanie subskrypcji.

## <a name="get-prepared-for-your-new-experience"></a>Przygotowywanie się na nowe środowisko

Zalecamy wykonanie następujących czynności w ramach przygotowywania się do nowego środowiska:

**Miesięczny okres rozliczeniowy i inna data faktury**

W nowym środowisku faktura będzie generowana około dziewiątego dnia każdego miesiąca i będzie zawierać wszystkie opłaty z poprzedniego miesiąca. Ta data może się różnić od daty generowania faktury w starym koncie. Jeśli udostępniasz faktury innym osobom, powiadom je o zmianie daty.

**Nowe interfejsy API rozliczeń i zarządzania kosztami**

Jeśli korzystasz z usługi Cost Management lub interfejsów API rozliczeń do wysyłania zapytań i aktualizowania danych dotyczących rozliczeń bądź kosztów, musisz używać nowych interfejsów API. W poniższej tabeli przedstawiono interfejsy API, które nie będą działać z nowym kontem rozliczeniowym, oraz zmiany, które należy wprowadzić w nowym koncie rozliczeniowym.

|Interfejs API | Zmiany  |
|---------|---------|
|[Billing Accounts — List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | W interfejsie API Billing Accounts — List stare konto rozliczeniowe ma parametr agreementType o wartości **MicrosoftOnlineServiceProgram**, a nowe konto rozliczeniowe będzie miało parametr agreementType o wartości **MicrosoftCustomerAgreement**. Jeśli korzystasz z zależności od parametru agreementType, zaktualizuj je. |
|[Invoices — List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Ten interfejs API zwróci tylko faktury, które zostały wygenerowane przed zaktualizowaniem konta. Aby zwrócić faktury wygenerowane w nowym koncie rozliczeniowym, należy użyć interfejsu API [Invoices — List By Billing Account](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |

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