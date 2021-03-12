---
title: Role rozliczeń dla umów klienta firmy Microsoft — Azure
description: Dowiedz się więcej o rolach rozliczeniowych dla umów klienta firmy Microsoft na potrzeby kont rozliczeniowych na platformie Azure.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011667"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure

Aby zarządzać kontem rozliczeniowym dla umowy klienta firmy Microsoft, użyj ról opisanych w poniższych sekcjach. Te role są dodatkiem do wbudowanych ról platformy Azure, które umożliwiają kontrolowanie dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do Umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Obejrzyj wideo [Zarządzanie dostępem do konta rozliczania MCA](https://www.youtube.com/watch?v=9sqglBlKkho) , aby dowiedzieć się, jak kontrolować dostęp do konta rozliczeń umowy klienta firmy Microsoft (MCA).

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>Definicje ról rozliczeniowych

W poniższej tabeli opisano role rozliczeniowe używane do zarządzania kontem rozliczeniowym, profilami rozliczeniowymi i sekcjami faktur.

|Rola|Opis|
|---|---|
|Właściciel konta rozliczeniowego|Zarządza wszystkimi elementami dotyczącymi konta rozliczeniowego|
|Współautor konta rozliczeniowego|Zarządza wszystkimi elementami z wyjątkiem uprawnień do konta rozliczeniowego|
|Czytelnik konta rozliczeniowego|Ma widok tylko do odczytu wszystkich elementów dotyczących konta rozliczeniowego|
|Właściciel profilu rozliczeniowego|Zarządza wszystkimi elementami profilu rozliczeniowego|
|Współautor profilu rozliczeniowego|Zarządza wszystkimi elementami z wyjątkiem uprawnień do profilu rozliczeniowego|
|Czytelnik profilu rozliczeniowego|Ma widok tylko do odczytu wszystkich elementów dotyczących profilu rozliczeniowego|
|Menedżer faktur|Wyświetla i reguluje faktury dla profilu rozliczeniowego|
|Właściciel sekcji faktury|Zarządza wszystkimi elementami w sekcji faktury|
|Współautor sekcji faktury|Zarządza wszystkimi elementami z wyjątkiem uprawnień w sekcji faktury|
|Czytelnik sekcji faktury|Ma widok tylko do odczytu wszystkich elementów w sekcji faktury|
|Twórca subskrypcji platformy Azure|Tworzy subskrypcje platformy Azure|

## <a name="billing-account-roles-and-tasks"></a>Role i zadania konta rozliczeniowego

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Role na koncie rozliczeniowym mają najwyższy poziom uprawnień i użytkowników w tych rolach uzyskują wgląd w informacje o kosztach i rozliczeniach dla całego konta. Te role należy przypisywać tylko użytkownikom, którzy muszą wyświetlać faktury, i śledzić koszty dla całego konta, takie jak członek zespołu Finanse i księgowość. Aby uzyskać więcej informacji, zobacz [Omówienie konta rozliczeniowego](../understand/mca-overview.md#your-billing-account).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście konta rozliczeniowego.

### <a name="manage-billing-account-permissions-and-properties"></a>Zarządzanie uprawnieniami i właściwościami konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie przypisań ról dla konta rozliczeniowego|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania konta rozliczeniowego i zarządzania nim|✔|✘|✘|
|Wyświetlanie właściwości konta rozliczeniowego, takich jak adres, umowy i inne|✔|✔|✔|
|Aktualizowanie właściwości konta rozliczeniowego, takich jak sprzedaż-do, nazwa wyświetlana i inne|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Zarządzanie profilami rozliczeniowymi w ramach konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetl wszystkie profile rozliczeń dla konta|✔|✔|✔|
|Utwórz nowe profile rozliczeń|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Zarządzanie fakturami dla konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetl wszystkie faktury dla konta|✔|✔|✔|
|Płatność za faktury za pomocą karty kredytowej|✔|✔|✘|
|Pobieranie faktur, plików użycia platformy Azure, arkuszy cen i dokumentów podatkowych|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Zarządzanie produktami dla konta rozliczeń

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetl wszystkie produkty zakupione dla konta|✔|✔|✔|
|Zarządzanie rozliczeniami dla produktów, takich jak anulowanie, wyłączanie autoodnawiania i inne|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Zarządzanie subskrypcjami dotyczącymi konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetl wszystkie subskrypcje platformy Azure utworzone dla konta rozliczeniowego|✔|✔|✔|
|Utwórz nowe subskrypcje platformy Azure|✔|✔|✘|
|Anuluj subskrypcje platformy Azure|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Role i zadania profilu rozliczeniowego

Każde konto rozliczeniowe ma co najmniej jeden profil rozliczania. Pierwszy profil rozliczeń jest ustawiany po zarejestrowaniu się w celu korzystania z platformy Azure. Dla profilu rozliczania jest generowana Faktura miesięczna i zawiera ona wszystkie powiązane opłaty z poprzedniego miesiąca. Istnieje możliwość skonfigurowania większej liczby profilów rozliczeń na podstawie Twoich potrzeb. Użytkownicy z rolami w profilu rozliczeń mogą wyświetlać koszt, ustawiać budżet i zarządzać i płacić za nie faktury. Przypisz te role do użytkowników, którzy są odpowiedzialni za zarządzanie budżetami i płatnymi fakturami dla profilu rozliczeń, takich jak członkowie zespołów administracji biznesowej w organizacji. Aby uzyskać więcej informacji, zobacz [Omówienie profilów rozliczeniowych](../understand/mca-overview.md#billing-profiles).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście profilu rozliczeniowego.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Zarządzanie uprawnieniami, właściwościami i zasadami profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie przypisań ról dla profilu rozliczeń|✔|✔|✔|✔|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania profilu rozliczeniowego i zarządzania nim|✔|✘|✘|✘|✔|✘|✘|
|Wyświetlanie właściwości profilu rozliczeń, takich jak numer zamówienia zakupu, rachunek i inne|✔|✔|✔|✔|✔|✔|✔|
|Aktualizowanie właściwości profilu rozliczeniowego |✔|✔|✘|✘|✔|✔|✘|
|Wyświetlanie zasad zastosowanych w profilu rozliczeń, takich jak zakupy rezerwacji platformy Azure, zakupy w portalu Azure Marketplace i inne|✔|✔|✔|✔|✔|✔|✔|
|Stosowanie zasad do profilu rozliczeniowego |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Zarządzanie fakturami w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich faktur w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Płatność za faktury za pomocą karty kredytowej|✔|✔|✘|✔|✔|✘|✘|
|Pobieranie faktur, plików użycia oraz opłat platformy Azure, arkuszy cen i dokumentów podatkowych w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Zarządzanie sekcjami faktury dla profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich sekcji faktury w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Tworzenie nowych sekcji faktury w ramach profilu rozliczeniowego|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Zarządzanie produktami dla profilu rozliczeń

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie produkty zakupione dla profilu rozliczeń|✔|✔|✔|✔|✔|✔|✔|
|Zarządzanie rozliczeniami dla produktów, takich jak anulowanie, wyłączanie autoodnawiania i inne|✔|✔|✘|✘|✔|✔|✘|
|Zmień profil rozliczeń dla produktów|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Zarządzanie formami płatności w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie form płatności w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Zarządzanie metodami płatności, takimi jak zastępowanie karty kredytowej, odłączanie karty kredytowej i nie tylko|✔|✔|✘|✘|✔|✔|✘|
|Śledzenie salda środków na korzystanie z platformy Azure w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Zarządzanie subskrypcjami w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich subskrypcji platformy Azure w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Utwórz nowe subskrypcje platformy Azure|✔|✔|✘|✘|✔|✔|✘|
|Anuluj subskrypcje platformy Azure|✘|✘|✘|✘|✘|✘|✘|
|Zmień profil rozliczeń dla subskrypcji platformy Azure|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Role i zadania sekcji faktury

Każdy profil rozliczeń zawiera domyślnie jedną sekcję faktury. Możesz utworzyć więcej sekcji faktur, aby pogrupować koszty na fakturze profilu rozliczenia.  Użytkownicy z rolami w sekcji faktury mogą kontrolować, kto tworzy subskrypcje platformy Azure i dokonuje innych zakupów. Przypisz te role do użytkowników, którzy konfigurują środowisko platformy Azure dla zespołów w naszej organizacji, np. liderów zespołów inżynierów i architektów technicznych. Aby uzyskać więcej informacji, zobacz [Omówienie sekcji faktur](../understand/mca-overview.md#invoice-sections).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście sekcji faktury.

### <a name="manage-invoice-section-permissions-and-properties"></a>Zarządzanie uprawnieniami i właściwościami sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego |Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Wyświetlanie przypisań ról dla faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania sekcji faktury i zarządzania nią|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Wyświetlanie właściwości sekcji faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Aktualizowanie właściwości sekcji faktury|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Zarządzanie produktami na potrzeby sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego |Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie produkty zakupione za sekcję faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Zarządzanie rozliczeniami dla produktów, takich jak anulowanie, wyłączanie autoodnawiania i inne|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Zmiana sekcji faktury na potrzeby produktów|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Zarządzanie subskrypcjami w sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego |Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich subskrypcji platformy Azure w sekcji faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Tworzy subskrypcje platformy Azure|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Anuluj subskrypcje platformy Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Sekcja zmiany faktury dla subskrypcji platformy Azure|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Uzyskiwanie własności rozliczeń subskrypcji od użytkowników z innych kont rozliczeniowych|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Role i zadania rozliczeniowe dotyczące subskrypcji

W poniższej tabeli przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście subskrypcji.

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego |Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Tworzenie subskrypcji|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Aktualizowanie centrum kosztu subskrypcji|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Zmiana sekcji faktury na potrzeby subskrypcji|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Zmień profil rozliczeń dla subskrypcji|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Anuluj subskrypcje platformy Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Wybierz pozycję **Kontrola dostępu (IAM)** w zakresie, takim jak konto rozliczeniowe, profil rozliczeniowy lub sekcja faktury, w którym chcesz udzielić dostępu.

4. Na stronie kontroli dostępu (IAM) znajduje się lista użytkowników i grup przypisanych do poszczególnych ról w tym zakresie.

   ![Zrzut ekranu przedstawiający listę administratorów konta rozliczeniowego](./media/understand-mca-roles/billing-list-admins.png)

5. Aby udzielić dostępu użytkownikowi, wybierz pozycję **Dodaj** w górnej części strony. Z listy rozwijanej Rola wybierz rolę. Wprowadź adres e-mail użytkownika, któremu chcesz przyznać dostęp. Wybierz przycisk **Zapisz**, aby przypisać rolę.

   ![Zrzut ekranu przedstawiający dodawanie administratora do konta rozliczeniowego](./media/understand-mca-roles/billing-add-admin.png)

6. Aby usunąć dostęp dla użytkownika, wybierz użytkownika z przypisaniem roli, które chcesz usunąć. Wybierz pozycję Usuń.

   ![Zrzut ekranu przedstawiający usuwanie administratora konta rozliczeniowego](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną
Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Wprowadzenie do konta rozliczeniowego umowy klienta firmy Microsoft](../understand/mca-overview.md)
- [Tworzenie subskrypcji platformy Azure w ramach konta rozliczeniowego umowy klienta firmy Microsoft](create-subscription.md)
