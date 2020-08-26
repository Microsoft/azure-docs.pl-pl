---
title: Wprowadzenie do konta rozliczeniowego w ramach Umowy klienta firmy Microsoft — Azure
description: Dowiedz się więcej o koncie rozliczeniowym dla Umowy z Klientem Microsoft, w tym o profilach rozliczeniowych i formach płatności za faktury.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 632fac953a54e661164152cda79082aed2c0bc06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690480"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Wprowadzenie do konta rozliczeniowego w ramach Umowy klienta firmy Microsoft

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład utworzyć konto platformy Azure w celu pracy z projektami osobistymi. Dostęp do platformy Azure można również uzyskiwać w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe.

Ten artykuł dotyczy konta rozliczeniowego w ramach Umowy klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Konto rozliczeniowe

Konto rozliczeniowe w ramach Umowy klienta firmy Microsoft zawiera co najmniej jeden profil rozliczeniowy, który umożliwia zarządzanie fakturami i formami płatności. Każdy profil rozliczeniowy zawiera co najmniej jedną sekcję faktury, w której można organizować koszty na fakturze profilu rozliczeniowego.

Na poniższym diagramie przedstawiono relację między kontem rozliczeniowym, profilami rozliczeniowymi i sekcjami faktury.

![Diagram przedstawiający hierarchię rozliczeń w Umowie klienta firmy Microsoft](./media/mca-overview/mca-billing-hierarchy.png)

Role na koncie rozliczeniowym mają najwyższy poziom uprawnień. Domyślnie tylko użytkownik, który utworzył konto platformy Azure, uzyskuje dostęp do konta rozliczeniowego. Role te powinny być przypisywane do użytkowników, którzy muszą wyświetlać faktury i śledzić koszty całej organizacji, takich jak menedżerowie działu finansów lub IT. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role konta rozliczeniowego](../manage/understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Profile rozliczeniowe

Profil rozliczeniowy służy do zarządzania fakturami i metodami płatności. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera odpowiednie opłaty za wszystkie subskrypcje platformy Azure i inne zakupy w poprzednim miesiącu.

Profil rozliczeniowy jest automatycznie tworzony dla konta rozliczeniowego. Domyślnie zawiera on jedną sekcję faktury. Możesz utworzyć dodatkowe sekcje, aby łatwo śledzić i organizować koszty zgodnie z wymaganiami danego projektu, działu lub środowiska deweloperskiego. Sekcje na fakturze profilu rozliczeniowego odzwierciedlają użycie poszczególnych subskrypcji i przypisanych do nich zakupów.

Role w profilach rozliczeniowych mają uprawnienia do wyświetlania faktur i metod płatności oraz zarządzania nimi. Role te należy przypisywać do użytkowników płacących faktury, takich jak członkowie zespołu księgowego w organizacji. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Każdy profil rozliczeniowy otrzymuje fakturę miesięczną

Dla każdego profilu rozliczeniowego na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera wszystkie opłaty z poprzedniego miesiąca.

W witrynie Azure Portal możesz wyświetlić fakturę, pobrać dokumenty i zmienić ustawienie, aby otrzymywać przyszłe faktury za pośrednictwem poczty e-mail. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [pobierania faktur w ramach Umowy klienta firmy Microsoft](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Fakturowanie form płatności

Każdy profil rozliczeniowy ma swoje formy płatności za faktury. Obsługiwane są następujące formy płatności:

| Typ             | Definicja  |
|------------------|-------------|
|Środki na korzystanie z platformy Azure    |  Środki są automatycznie stosowane do kwalifikujących się opłat na fakturze, co zmniejsza wymaganą kwotę do zapłacenia. Aby uzyskać więcej informacji, zobacz temat [Track Azure credit balance for your billing profile](../manage/mca-check-azure-credits-balance.md) (Śledzenie salda środków na korzystanie z platformy Azure dla profilu rozliczeniowego). |
|Czek / przelew bankowy | Jeśli konto zostało zatwierdzone do dokonywania płatności przy użyciu czeku/przelewu bankowego. Możesz zapłacić kwotę należną za fakturę za pomocą czeku lub przelewu bankowego. Instrukcje dotyczące płatności są podane na fakturze |
|Karta kredytowa | Klienci, którzy rejestrują się na platformie Azure za pomocą witryny internetowej tej platformy, mogą płacić przy użyciu karty kredytowej. |

### <a name="apply-policies-to-control-purchases"></a>Stosowanie zasad w celu kontrolowania zakupów

Zasady można stosować w celu kontrolowania zakupów w portalu Azure Marketplace i zakupów rezerwacji przy użyciu profilu rozliczeniowego. Można ustawić zasady, aby wyłączyć możliwość zakupu rezerwacji platformy Azure i produktów z witryny Marketplace. Po zastosowaniu zasad subskrypcje rozliczane w profilu rozliczeniowym nie mogą być używane do dokonywania tych zakupów.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Plany platformy Azure określają ceny i umowę dotyczącą poziomu usług dla subskrypcji

Plany platformy Azure określają ceny i umowę dotyczącą poziomu usług dla subskrypcji platformy Azure. Są one automatycznie włączane podczas tworzenia profilu rozliczeniowego. Wszystkie sekcje faktury skojarzone z profilem rozliczeniowym mogą korzystać z tych planów. Użytkownicy z dostępem do sekcji faktury używają planów do tworzenia subskrypcji platformy Azure. Następujące plany platformy Azure są obsługiwane na kontach rozliczeniowych w przypadku Umowy klienta firmy Microsoft:

| Planowanie             | Definicja  |
|------------------|-------------|
|Plan platformy Microsoft Azure   | Zezwól użytkownikom na tworzenie subskrypcji, w których można uruchamiać dowolne obciążenia.  |
|Plan platformy Microsoft Azure na potrzeby tworzenia i testowania | Zezwól subskrybentom programu Visual Studio na tworzenie subskrypcji, które są ograniczone do obsługi obciążeń programistycznych lub testowych. Te subskrypcje uzyskują korzyści, takie jak niższe stawki i dostęp do wyłącznego obrazu maszyn wirtualnych w witrynie Azure Portal. |

## <a name="invoice-sections"></a>Sekcje faktury

Sekcje faktury można tworzyć w celu organizowania kosztów na fakturze. Możesz na przykład potrzebować jednej faktury dla organizacji, ale chcesz zorganizować koszty według działu, zespołu lub projektu. W tym scenariuszu istnieje pojedynczy profil rozliczeniowy, w którym jest tworzona sekcja faktury dla każdego działu, zespołu lub projektu.

Po utworzeniu sekcji faktury możesz nadać innym użytkownikom uprawnienia do tworzenia subskrypcji platformy Azure, które są rozliczane w ramach sekcji. Wszelkie opłaty za użycie i zakupy dla subskrypcji są następnie rozliczane w ramach sekcji.

Role w sekcji faktury mają uprawnienia do kontrolowania, kto tworzy subskrypcje platformy Azure. Przypisz te role do użytkowników, którzy konfigurują środowisko platformy Azure dla zespołów w naszej organizacji, np. liderów zespołów inżynierów i architektów technicznych. Aby uzyskać więcej informacji, zobacz sekcję [Zadania i role sekcji faktur](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do Umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Understand Microsoft Customer Agreement administrative roles in Azure](../manage/understand-mca-roles.md) (Omówienie ról administracyjnych dla Umowy klienta firmy Microsoft na platformie Azure)
- [Tworzenie dodatkowej subskrypcji platformy Azure dla Umowy klienta firmy Microsoft](../manage/create-subscription.md)
- [Tworzenie sekcji na fakturze w celu organizacji kosztów](../manage/mca-section-invoice.md)
