---
title: Zadania umowy EA w umowie klienta firmy Microsoft — Azure
description: Dowiedz się, jak wykonywać zadania umowy Enterprise Agreement na nowym koncie rozliczeniowym.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 928800f22011f6388ec50a1ab5e906b2080a4125
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682896"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Wykonywanie zadań umowy Enterprise Agreement na koncie rozliczeniowym dla umowy klienta firmy Microsoft

Jeśli w Twojej organizacji podpisano umowę klienta firmy Microsoft umożliwiającą odnowienie rejestracji Enterprise Agreement, dla tej umowy zostało utworzone nowe konto rozliczeniowe. Rozliczenia na nowym koncie wyglądają inaczej niż w przypadku umowy Enterprise Agreement. W tym artykule opisano, jak wykonywać zadania umowy Enterprise Agreement na nowym koncie rozliczeniowym.

## <a name="billing-organization-in-the-new-account"></a>Organizacja rozliczeń na nowym koncie

Na poniższym schemacie pokazano organizację rozliczeń w ramach nowego konta rozliczeniowego.

![Obraz elementu ea-mca-post-transition-hierarchy](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Umowa klienta firmy Microsoft    |
|------------------------|--------------------------------------------------------|
| Rejestracja            | Profil rozliczeniowy służy do zarządzania rozliczeniami w organizacji, podobnie jak w przypadku rejestracji umowy Enterprise Agreement. Administratorzy przedsiębiorstwa stają się właścicielami profilu rozliczeniowego. Aby dowiedzieć się więcej o profilach rozliczeniowych, zobacz [Omówienie profilów rozliczeniowych](../understand/mca-overview.md#billing-profiles).
| Dział            | Sekcja faktur służy do organizowania kosztów, podobnie jak w przypadku działów w rejestracji umowy Enterprise Agreement. Działy stają się sekcjami faktur, a administratorzy działów stają się właścicielami odpowiednich sekcji faktur. Aby dowiedzieć się więcej na temat sekcji faktur, zobacz [Omówienie sekcji faktur](../understand/mca-overview.md#invoice-sections). |
| Konto               | Konta utworzone w ramach umowy Enterprise Agreement nie są obsługiwane na nowym koncie rozliczeniowym. Subskrypcje konta należą do sekcji faktur odpowiedniej dla ich działu. Właściciele konta mogą tworzyć subskrypcje dla swoich sekcji faktur i zarządzać nimi. |

## <a name="changes-for-enterprise-administrators"></a>Zmiany dla administratorów przedsiębiorstwa

Poniższe zmiany dotyczą administratorów przedsiębiorstwa, w którym umowa Enterprise Agreement została odnowiona do umowy klienta firmy Microsoft.

- Dla rejestracji jest tworzony profil rozliczeniowy. Służy on do zarządzania rozliczeniami w organizacji, podobnie jak w przypadku rejestracji umowy Enterprise Agreement. Aby dowiedzieć się więcej o profilach rozliczeniowych, zobacz [Omówienie profilów rozliczeniowych](../understand/mca-overview.md#billing-profiles).
- Dla każdego działu w ramach rejestracji umowy Enterprise Agreement tworzona jest sekcja faktur. Sekcje faktur umożliwiają zarządzanie działami. Można skonfigurować dodatkowe działy, tworząc nowe sekcje faktur. Aby dowiedzieć się więcej na temat sekcji faktur, zobacz [omówienie sekcji faktur](../understand/mca-overview.md#invoice-sections).
- Za pomocą roli twórcy subskrypcji platformy Azure używanej w sekcjach faktury można nadawać innym osobom uprawnienie do tworzenia subskrypcji platformy Azure, podobnie jak w przypadku kont utworzonych w ramach rejestracji umowy Enterprise Agreement.
- Zarządzanie rozliczeniami w organizacji odbywa się w witrynie [Azure Portal](https://portal.azure.com), a nie witrynie Azure EA Portal.

Na nowym koncie rozliczeniowym są nadawane następujące role:

**Właściciel profilu rozliczeniowego** — masz przypisaną rolę właściciela profilu rozliczeniowego, który został utworzony przy podpisywaniu umowy. Rola ta umożliwia zarządzanie rozliczeniami w organizacji. Możesz wyświetlać opłaty i faktury, organizować koszty na fakturze, zarządzać formami płatności i kontrolować dostęp do rozliczeń w organizacji.

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury dla wszystkich sekcji faktury utworzonych dla działów w ramach rejestracji umowy Enterprise Agreement. Rola ta pozwala kontrolować, kto może tworzyć subskrypcje platformy Azure i kupować inne produkty.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Wyświetlanie opłat i salda środków dla organizacji

Śledzenie opłat i salda środków na korzystanie z platformy Azure odbywa się za pomocą profilu rozliczeniowego, podobnie jak w przypadku rejestracji umowy Enterprise Agreement.

Aby dowiedzieć się, jak wyświetlić saldo środków dla profilu rozliczeniowego, zobacz [Śledzenie salda środków na korzystanie z platformy Azure dla profilu rozliczeniowego](mca-check-azure-credits-balance.md).

Aby dowiedzieć się, jak wyświetlić opłaty dla profilu rozliczeniowego, zobacz [Informacje o opłatach na fakturze dla umowy klienta firmy Microsoft](../understand/review-customer-agreement-bill.md).

### <a name="view-charges-for-a-department"></a>Wyświetlanie opłat dla działu

Dla każdego działu w ramach umowy Enterprise Agreement tworzona jest sekcja faktur. Opłaty dla sekcji faktury można sprawdzić w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie transakcji według sekcji faktury](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Wyświetlanie opłat dla konta

Konta utworzone w ramach rejestracji umowy Enterprise Agreement nie są obsługiwane na nowym koncie rozliczeniowym. Subskrypcje konta należą do sekcji faktur odpowiedniej dla ich działu. Właściciele konta mogą tworzyć subskrypcje dla swoich sekcji faktur i zarządzać nimi.

Aby wyświetlić zagregowany koszt dla subskrypcji należących do konta, musisz ustawić centrum kosztu dla każdej subskrypcji. Następnie możesz odfiltrować subskrypcje według centrum kosztu za pomocą pliku CSV zawierającego opłaty i dane użycia platformy Azure.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Pobieranie pliku CSV zawierającego opłaty i dane użycia, arkusza cen i dokumentów podatkowych

Dla każdego profilu rozliczeniowego na koncie jest generowana faktura miesięczna. Dla każdej faktury można pobrać plik CSV zawierający opłaty i dane użycia platformy Azure, arkusz cen i dokument podatkowy (jeśli dotyczy). Można również pobrać plik CSV zawierający dane użycia platformy Azure i opłaty w bieżącym miesiącu.

Aby dowiedzieć się, jak pobrać plik CSV zawierający opłaty i dane użycia platformy Azure, zobacz [Pobieranie zestawienia użycia dotyczącego umowy klienta firmy Microsoft](../understand/download-azure-daily-usage.md).

Aby dowiedzieć się, jak pobrać arkusz cen, zobacz [Pobieranie cennika dotyczącego umowy klienta firmy Microsoft](ea-pricing.md).

Aby dowiedzieć się, jak pobrać dokumenty podatkowe, zobacz [Wyświetlanie dokumentów podatkowych dotyczących umowy klienta firmy Microsoft](../understand/mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Dodawanie administratora przedsiębiorstwa

Aby umożliwić użytkownikom wyświetlanie rozliczeń organizacji i zarządzanie nimi, przyznaj im dostęp do profilu rozliczeniowego. Dostęp możesz przyznać na stronie **Kontrola dostępu (zarządzanie dostępem i tożsamościami)** w witrynie Azure Portal.  Aby dowiedzieć się więcej o rolach profilu rozliczeniowego, zobacz [Zadania i role profilu rozliczeniowego](understand-mca-roles.md#billing-profile-roles-and-tasks).

Aby dowiedzieć się, jak zapewnić dostęp do profilu rozliczeniowego, zobacz [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Tworzenie nowego działu

Po utworzeniu sekcji faktury można organizować koszty zgodnie z potrzebami, podobnie jak w przypadku działów w rejestracji umowy Enterprise Agreement. Nową sekcję faktury można utworzyć w witrynie Azure Portal. Aby dowiedzieć się więcej, zobacz [Tworzenie sekcji na fakturze w celu organizacji kosztów](mca-section-invoice.md).

### <a name="create-a-new-account"></a>Tworzenie nowego konta

Przypisując użytkownikom rolę twórcy subskrypcji platformy Azure w sekcjach faktury, można nadawać im uprawnienie do tworzenia subskrypcji platformy Azure, podobnie jak w przypadku kont utworzonych w ramach rejestracji umowy Enterprise Agreement. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>Zmiany dla administratorów działów

Poniższe zmiany dotyczą administratorów działów, w których umowa Enterprise Agreement została odnowiona do umowy klienta firmy Microsoft.

- Dla każdego działu w ramach rejestracji umowy Enterprise Agreement tworzona jest sekcja faktur. Sekcje faktur umożliwiają zarządzanie działami. Aby dowiedzieć się więcej na temat sekcji faktur, zobacz [omówienie sekcji faktur](../understand/mca-overview.md#invoice-sections).
- Za pomocą roli twórcy subskrypcji platformy Azure używanej w sekcjach faktury można nadawać innym osobom uprawnienie do tworzenia subskrypcji platformy Azure, podobnie jak w przypadku kont utworzonych w ramach rejestracji umowy Enterprise Agreement.
- Zarządzanie rozliczeniami w organizacji odbywa się w witrynie Azure Portal, a nie witrynie Azure EA Portal.

Na nowym koncie rozliczeniowym są nadawane następujące role:

**Właściciel sekcji faktury** — masz przypisaną rolę właściciela sekcji faktury dla sekcji faktury utworzonej dla działów w ramach umowy Enterprise Agreement. Rola ta pozwala wyświetlać i śledzić opłaty oraz kontrolować, kto może tworzyć subskrypcje platformy Azure i kupować inne produkty w sekcji faktury.

### <a name="view-charges-for-your-department"></a>Wyświetlanie opłat dla działu

Opłaty w sekcji faktury utworzonej dla działu można wyświetlić na stronie [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) w witrynie Azure Portal.

### <a name="add-an-additional-department-administrator"></a>Dodawanie administratora działu

Dla każdego działu w ramach umowy Enterprise Agreement tworzona jest sekcja faktur. Korzystając ze strony **Kontrola dostępu (zarządzanie dostępem i tożsamościami)**  w witrynie Azure Portal, można umożliwić innym osobom wyświetlanie sekcji faktury i zarządzanie nią. Aby dowiedzieć się więcej o rolach sekcji faktury, zobacz [Zadania i role sekcji faktur](understand-mca-roles.md#invoice-section-roles-and-tasks).

Aby dowiedzieć się, jak zapewnić dostęp do sekcji faktury, zobacz [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Tworzenie nowego konta w dziale

Przypisz użytkownikom rolę twórcy subskrypcji platformy Azure w sekcji faktury, która została utworzona dla działu. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>Wyświetlanie opłat dla kont w działach

Konta utworzone w ramach rejestracji umowy Enterprise Agreement nie są obsługiwane na nowym koncie rozliczeniowym. Subskrypcje konta należą do sekcji faktur odpowiedniej dla ich działu. Właściciele konta mogą tworzyć subskrypcje dla swoich sekcji faktur i zarządzać nimi.

Aby wyświetlić zagregowany koszt dla subskrypcji należących do konta w dziale, musisz ustawić centrum kosztu dla każdej subskrypcji. Następnie możesz odfiltrować subskrypcje według centrum kosztu za pomocą pliku zawierającego opłaty i dane użycia platformy Azure.

## <a name="changes-for-account-owners"></a>Zmiany dla właścicieli kont

Właściciele kont w ramach umowy Enterprise Agreement zyskują uprawnienie do tworzenia subskrypcji platformy Azure na nowym koncie rozliczeniowym. Istniejące subskrypcje platformy Azure należą do sekcji faktury utworzonej dla działu. Jeśli konto nie należy do działu, subskrypcje należą do sekcji faktury o nazwie Domyślna sekcja faktury.

Na nowym koncie rozliczeniowym masz przypisaną następującą rolę, która umożliwia tworzenie dodatkowych subskrypcji platformy Azure.

**Twórca subskrypcji platformy Azure** — masz przypisaną rolę twórcy subskrypcji platformy Azure w sekcji faktury utworzonej dla działu w umowie Enterprise Agreement. Jeśli konto nie należy do działu, otrzymujesz rolę twórcy subskrypcji platformy Azure w sekcji o nazwie Domyślna sekcja faktury. Rola ta umożliwia tworzenie subskrypcji platformy Azure dla sekcji faktury.

### <a name="create-an-azure-subscription"></a>Tworzenie subskrypcji platformy Azure

Subskrypcje platformy Azure dla sekcji faktury można utworzyć w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Tworzenie dodatkowej subskrypcji platformy Azure dla umowy klienta firmy Microsoft](create-subscription.md).

### <a name="view-charges-for-your-account"></a>Wyświetlanie opłat dla konta

Aby wyświetlić opłaty za subskrypcje należące do konta, przejdź do [strony subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal. Na stronie subskrypcji są wyświetlane opłaty za wszystkie subskrypcje.

### <a name="view-charges-for-a-subscription"></a>Wyświetlanie opłat za subskrypcję

Opłaty za subskrypcję można wyświetlić na [stronie subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lub stronie analizy kosztów platformy Azure. Aby uzyskać więcej informacji na temat analizy kosztów na platformie Azure, zobacz [Poznawanie i analizowanie kosztów za pomocą funkcji Analiza kosztów](../costs/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta rozliczeniowego umowy klienta firmy Microsoft](../understand/mca-overview.md)
- [Opis zawartości faktury](../understand/review-individual-bill.md)
- [Opis zawartości rachunku](../understand/understand-invoice.md)
- [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure od innych użytkowników](mca-request-billing-ownership.md)
