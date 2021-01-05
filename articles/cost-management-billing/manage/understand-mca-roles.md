---
title: Role rozliczeń dla umów klienta firmy Microsoft — Azure
description: Dowiedz się więcej o rolach rozliczeniowych dla umów klienta firmy Microsoft na potrzeby kont rozliczeniowych na platformie Azure.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 511c0ecdef45a882bcc3a427d5ec602b3ebabc66
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673835"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure

Aby zarządzać kontem rozliczeniowym dla umowy klienta firmy Microsoft, użyj ról opisanych w poniższych sekcjach. Te role są dodatkiem do wbudowanych ról platformy Azure, które umożliwiają kontrolowanie dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft.

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
|Menedżer faktur|Wyświetlanie faktur dla profilu rozliczeniowego|
|Właściciel sekcji faktury|Zarządza wszystkimi elementami w sekcji faktury|
|Współautor sekcji faktury|Zarządza wszystkimi elementami z wyjątkiem uprawnień w sekcji faktury|
|Czytelnik sekcji faktury|Ma widok tylko do odczytu wszystkich elementów w sekcji faktury|
|Twórca subskrypcji platformy Azure|Tworzy subskrypcje platformy Azure|

## <a name="billing-account-roles-and-tasks"></a>Role i zadania konta rozliczeniowego

Konto rozliczeniowe umożliwia zarządzanie rozliczeniami w organizacji. Konto rozliczeniowe służy do organizowania kosztów, monitorowania opłat oraz faktur i kontrolowania dostępu do rozliczeń w organizacji. Aby uzyskać więcej informacji, zobacz [Omówienie konta rozliczeniowego](../understand/mca-overview.md#your-billing-account).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście konta rozliczeniowego.

### <a name="manage-billing-account-permissions-and-properties"></a>Zarządzanie uprawnieniami i właściwościami konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie istniejących uprawnień dla konta rozliczeniowego|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania konta rozliczeniowego i zarządzania nim|✔|✘|✘|
|Wyświetlanie właściwości konta rozliczeniowego, takich jak nazwa firmy, adres i inne|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Zarządzanie profilami rozliczeniowymi w ramach konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie wszystkich profilów rozliczeniowych w ramach konta|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Zarządzanie fakturami dla konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie wszystkich faktur w ramach konta|✔|✔|✔|
|Pobieranie faktur, plików użycia oraz opłat platformy Azure, arkuszy cen i dokumentów podatkowych w ramach konta|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Zarządzanie sekcjami faktur w ramach konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie wszystkich sekcji faktur w ramach konta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Zarządzanie transakcjami dotyczącymi konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie wszystkich transakcji rozliczeniowych dla konta|✔|✔|✔|
|Wyświetlanie wszystkich produktów zakupionych w ramach konta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Zarządzanie subskrypcjami dotyczącymi konta rozliczeniowego

|Zadanie|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego|
|---|---|---|---|
|Wyświetlanie wszystkich subskrypcji platformy Azure w ramach konta rozliczeniowego|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Role i zadania profilu rozliczeniowego

Profil rozliczeniowy służy do zarządzania fakturami i formami płatności. Co miesiąc dla subskrypcji platformy Azure i innych produktów zakupionych przy użyciu profilu rozliczeniowego jest generowana faktura. Faktury są regulowane za pomocą form płatności. Aby uzyskać więcej informacji, zobacz [Omówienie profilów rozliczeniowych](../understand/mca-overview.md#billing-profiles).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście profilu rozliczeniowego.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Zarządzanie uprawnieniami, właściwościami i zasadami profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie istniejących uprawnień dla profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania profilu rozliczeniowego i zarządzania nim|✔|✘|✘|✘|✘|✘|✘|
|Wyświetlanie właściwości profilu rozliczeniowego, takich jak numer zamówienia zakupu, preferencja dotycząca wysyłania faktur pocztą e-mail i inne|✔|✔|✔|✔|✔|✔|✔|
|Aktualizowanie właściwości profilu rozliczeniowego |✔|✔|✘|✘|✘|✘|✘|
|Wyświetlanie zasad zastosowanych do profilu rozliczeniowego, takich jak włączanie zakupów rezerwacji platformy Azure, włączanie zakupów w portalu Azure Marketplace i inne|✔|✔|✔|✔|✔|✔|✔|
|Stosowanie zasad do profilu rozliczeniowego |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Zarządzanie fakturami w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich faktur w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Pobieranie faktur, plików użycia oraz opłat platformy Azure, arkuszy cen i dokumentów podatkowych w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Zarządzanie sekcjami faktury dla profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich sekcji faktury w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Tworzenie nowych sekcji faktury w ramach profilu rozliczeniowego|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Zarządzanie transakcjami dotyczącymi profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich transakcji rozliczeniowych dotyczących profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Zarządzanie formami płatności w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie form płatności w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|
|Śledzenie salda środków na korzystanie z platformy Azure w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Zarządzanie subskrypcjami w ramach profilu rozliczeniowego

|Zadanie|Właściciel profilu rozliczeniowego|Współautor profilu rozliczeniowego|Czytelnik profilu rozliczeniowego|Menedżer faktur|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich subskrypcji platformy Azure w ramach profilu rozliczeniowego|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Role i zadania sekcji faktury

Sekcje faktury umożliwiają organizowanie kosztów na fakturze. Utworzenie sekcji umożliwia uporządkowanie kosztów według działu, środowiska deweloperskiego lub w oparciu o potrzeby organizacji. Następnie dla tej sekcji można nadawać innym użytkownikom uprawnienia do tworzenia subskrypcji platformy Azure. Wszelkie opłaty za użycie i zakupy dla subskrypcji są następnie wyświetlane w tej sekcji faktury. Aby uzyskać więcej informacji, zobacz [Omówienie sekcji faktur](../understand/mca-overview.md#invoice-sections).

W poniższych tabelach przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście sekcji faktury.

### <a name="manage-invoice-section-permissions-and-properties"></a>Zarządzanie uprawnieniami i właściwościami sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego |
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich uprawnień dotyczących sekcji faktury|✔|✔|✔|✔|✔|✔|✔|
|Przyznawanie innym osobom uprawnień do wyświetlania sekcji faktury i zarządzania nią|✔|✘|✘|✘|✘|✘|✘|
|Wyświetlanie właściwości sekcji faktury|✔|✔|✔|✔|✔|✔|✔|
|Aktualizowanie właściwości sekcji faktury|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Zarządzanie produktami na potrzeby sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie w sekcji faktury wszystkich zakupionych produktów|✔|✔|✔|✘|✔|✔|✔|
|Zarządzanie rozliczeniami produktów w sekcji faktury, takimi jak anulowanie, wyłączanie automatycznego odnawiania i inne|✔|✔|✘|✘|✘|✘|✘|
|Zmiana sekcji faktury na potrzeby produktów|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Zarządzanie subskrypcjami w sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|Właściciel konta rozliczeniowego|Współautor konta rozliczeniowego|Czytelnik konta rozliczeniowego
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich subskrypcji platformy Azure w sekcji faktury|✔|✔|✔|✘|✔|✔|✔|
|Zmiana sekcji faktury na potrzeby subskrypcji|✔|✔|✘|✘|✘|✘|✘|
|Uzyskiwanie własności rozliczeń subskrypcji od użytkowników z innych kont rozliczeniowych|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Role i zadania rozliczeniowe dotyczące subskrypcji

W poniższej tabeli przedstawiono, jaką należy mieć rolę, aby móc wykonywać zadania w kontekście subskrypcji.

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytelnik sekcji faktury|Twórca subskrypcji platformy Azure|
|---|---|---|---|---|
|Tworzy subskrypcje platformy Azure|✔|✔|✘|✔|
|Aktualizowanie centrum kosztu subskrypcji|✔|✔|✘|✘|
|Zmiana sekcji faktury na potrzeby subskrypcji|✔|✔|✘|✘|

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
