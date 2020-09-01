---
title: Model rozliczeń dla Azure Active Directory B2C
description: Dowiedz się więcej na temat modelu rozliczeń miesięcznych użytkowników usługi Azure AD B2C's (MAU) i sposobu włączania rozliczeń dla określonej subskrypcji platformy Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: f9adf6ce4559234eec74c92f09aa752eb1f9ab51
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177333"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model rozliczeń dla Azure Active Directory B2C

Użycie Azure Active Directory B2C (Azure AD B2C) jest rozliczane w połączonej subskrypcji platformy Azure i korzysta z modelu rozliczania comiesięcznych użytkowników (MAU). Dowiedz się, jak połączyć zasób Azure AD B2C z subskrypcją i jak działa model rozliczeń MAU w poniższych sekcjach.

> [!IMPORTANT]
> Ten artykuł nie zawiera informacji o cenach. Aby uzyskać najnowsze informacje dotyczące rozliczeń i cen użytkowania, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Miesięczne rozliczenia aktywnych użytkowników (MAU)

Opłaty za Azure AD B2C są naliczane według liczby unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego, znanej jako comiesięczne rozliczenia aktywnych użytkowników (MAU).

Od **01 listopada 2019**wszystkie nowo utworzone dzierżawy Azure AD B2C są rozliczane jako użytkownicy aktywni miesięcznie (Mau). W przypadku istniejących dzierżawców, które są [połączone z subskrypcją](#link-an-azure-ad-b2c-tenant-to-a-subscription) w dniu lub po 01 listopada 2019, będą naliczane opłaty za miesięcznych użytkowników (Mau).

Jeśli masz istniejącą dzierżawę Azure AD B2C, która została połączona z subskrypcją przed 01 listopada 2019, możesz wykonać jedną z następujących czynności:

* Uaktualnij do modelu rozliczania miesięcznych aktywnych użytkowników (MAU) lub
* Pozostań w modelu rozliczania na uwierzytelnienie

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Uaktualnianie do modelu rozliczeń aktywnych użytkowników w miesiącu

Właściciele subskrypcji platformy Azure z dostępem administracyjnym do zasobu Azure AD B2C mogą przełączyć się na model rozliczeń MAU. Opcje rozliczeń są konfigurowane w zasobie Azure AD B2C.

Przełączenie na rozliczenia miesięcznie aktywnych użytkowników (MAU) jest **nieodwracalne**. Po przekonwertowaniu zasobu Azure AD B2C na model rozliczeń oparty na MAU nie można przywrócić tego zasobu do modelu rozliczania na uwierzytelnianie.

Oto jak włączyć MAU rozliczeń dla istniejącego zasobu Azure AD B2C:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako właściciel subskrypcji.
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog Azure AD B2C, który chcesz uaktualnić, aby Mau rozliczanie.<br/>
    ![Filtr katalogów i subskrypcji w Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie **Przegląd** dzierżawy Azure AD B2C wybierz link w obszarze **nazwa zasobu**. Nastąpi przekierowanie do zasobu Azure AD B2C w dzierżawie usługi Azure AD.<br/>
    ![Azure AD B2C wyróżnionego linku zasobu Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na stronie **Przegląd** zasobu Azure AD B2C w obszarze **jednostki rozliczeniowe**wybierz łącze **uwierzytelnianie (Zmień na Mau)** .<br/>
    ![Zmień na link MAU wyróżniony w Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Wybierz pozycję **Potwierdź** , aby zakończyć uaktualnianie do Mau rozliczeń.<br/>
    ![Okno dialogowe potwierdzenia rozliczenia opartego na MAU w Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Czego można oczekiwać po przejściu do MAU rozliczeń za uwierzytelnianie na podstawie uwierzytelniania

Pomiar oparty na MAU jest włączony zaraz po wykonaniu tej subskrypcji/właściciela zasobu, potwierdzić zmianę. Rachunek miesięczny będzie uwzględniać jednostki uwierzytelniania, które są rozliczane do momentu zmiany, oraz nowe jednostki MAU rozpoczynające się od zmiany.

Użytkownicy nie są podwójnie zliczane w miesiącu przejścia. Unikatowi aktywni użytkownicy, którzy uwierzytelniają się przed zmianą, są obciążani stawką za uwierzytelnianie w miesiącu kalendarzowym. Ci użytkownicy nie są uwzględnieni w obliczeniach MAU dla pozostałej części cyklu rozliczeniowego subskrypcji. Na przykład:

* Dzierżawa contoso B2C ma 1 000 użytkowników. 250 użytkowników jest aktywnych w danym miesiącu. Administrator subskrypcji zmieni się z uwierzytelniania na comiesięcznych użytkowników aktywnych (MAU) w dziesiątym miesiącu.
* Naliczanie opłat za 1 dziesiątki jest rozliczane przy użyciu modelu na uwierzytelnianie.
  * Jeśli 100 użytkowników loguje się w tym okresie (1 – 10), użytkownicy są oznaczeni jako *zapłacone za miesiąc*.
* Rozliczenia od dziesiątego (czasu obowiązywania przejścia) są rozliczane według stawki za MAU.
  * Jeśli w tym okresie zostanie zastosowana dodatkowa 150 użytkownicy, opłaty są naliczane tylko za dodatkowe 150.
  * Kontynuacja działania pierwszych 100 użytkowników nie ma wpływu na rozliczenia za resztę miesiąca kalendarzowego.

W okresie rozliczeniowym przejścia właściciel subskrypcji będzie prawdopodobnie widział wpisy dla obu metod (za uwierzytelnianie i za MAU), które są wyświetlane w ich zestawie rozliczeń subskrypcji platformy Azure:

* Wpis do użycia do daty/godziny zmiany, która odzwierciedla na uwierzytelnianie.
* Wpis do użycia po zmianie, która odzwierciedla miesięcznych użytkowników aktywnych (MAU).

Aby uzyskać najnowsze informacje o rozliczeniach użycia i cenach dla Azure AD B2C, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Łączenie dzierżawy Azure AD B2C z subskrypcją

Opłaty za użycie dla Azure Active Directory B2C (Azure AD B2C) są rozliczane w ramach subskrypcji platformy Azure. Po utworzeniu dzierżawy Azure AD B2C Administrator dzierżawy musi jawnie połączyć dzierżawę Azure AD B2C z subskrypcją platformy Azure.

Link do subskrypcji uzyskuje się przez utworzenie *zasobu* Azure AD B2C w ramach docelowej subskrypcji platformy Azure. Kilka zasobów Azure AD B2C można utworzyć w ramach jednej subskrypcji platformy Azure, a także innych zasobów platformy Azure, takich jak maszyny wirtualne, konta magazynu i Logic Apps. Wszystkie zasoby w ramach subskrypcji można wyświetlić, przechodząc do dzierżawy usługi Azure Active Directory (Azure AD), z którą skojarzona jest subskrypcja.

Subskrypcja połączona z dzierżawą Azure AD B2C może być używana do rozliczania Azure AD B2C użycia lub innych zasobów platformy Azure, w tym dodatkowych zasobów Azure AD B2C. Nie można jej używać do dodawania innych licencji usługi Azure opartych na licencji lub pakietu Office 365 w ramach dzierżawy Azure AD B2C.

### <a name="prerequisites"></a>Wymagania wstępne

* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
* [Azure AD B2C dzierżawy](tutorial-create-tenant.md) , którą chcesz połączyć z subskrypcją
  * Musisz być administratorem dzierżawy
  * Dzierżawca nie może już być połączony z subskrypcją

### <a name="create-the-link"></a>Utwórz łącze

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający subskrypcję platformy Azure, której chcesz użyć (*nie* katalog zawierający dzierżawę Azure AD B2C).
1. Wybierz pozycję **Utwórz zasób**, wprowadź wartość `Active Directory B2C` w polu **Wyszukaj w witrynie Marketplace** , a następnie wybierz pozycję **Azure Active Directory B2C**.
1. Wybierz pozycję **Utwórz**
1. Wybierz pozycję **Połącz istniejącą dzierżawę Azure AD B2C z moją subskrypcją platformy Azure**.
1. Wybierz **dzierżawę Azure AD B2C** z listy rozwijanej. Wyświetlane są tylko dzierżawy, dla których jesteś administratorem globalnym i które nie są już połączone z subskrypcją. Pole **nazwy zasobu Azure AD B2C** jest wypełniane nazwą domeny wybranej dzierżawy Azure AD B2C.
1. Wybierz aktywną **subskrypcję** platformy Azure, której jesteś administratorem.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**, a następnie określ **lokalizację grupy zasobów**. Ustawienia grupy zasobów nie mają wpływu na lokalizację dzierżawy Azure AD B2C, wydajność ani stan rozliczeń.
1. Wybierz przycisk **Utwórz**.
    ![Strona tworzenia zasobów Azure AD B2C w programie Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Po wykonaniu tych kroków dla dzierżawy Azure AD B2C subskrypcja platformy Azure jest rozliczana zgodnie ze szczegółowymi informacjami dotyczącymi usługi Azure Direct lub Umowa Enterprise, jeśli ma to zastosowanie.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy Azure AD B2C

Po utworzeniu zasobu Azure AD B2C w ramach subskrypcji platformy Azure powinien zostać wyświetlony nowy zasób typu "dzierżawa B2C" z innymi zasobami platformy Azure.

Tego zasobu można użyć do:

* Przejdź do subskrypcji, aby przejrzeć informacje o rozliczeniach
* Uzyskaj identyfikator dzierżawy Azure AD B2C dzierżawy w formacie identyfikatora GUID
* Przejdź do dzierżawy Azure AD B2C
* Prześlij żądanie pomocy technicznej
* Przenieś zasób dzierżawy Azure AD B2C do innej subskrypcji platformy Azure lub grupy zasobów

![Strona ustawień zasobów B2C w Azure Portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Ograniczenia regionalne

Jeśli zostały ustanowione ograniczenia regionalne dla tworzenia zasobów platformy Azure w ramach subskrypcji, ograniczenie może uniemożliwić utworzenie zasobu Azure AD B2C.

Aby uniknąć tego problemu, Zmniejsz ograniczenia regionalne.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Subskrypcje dostawcy rozwiązań w chmurze Azure (CSP)

Subskrypcje dostawcy rozwiązań w chmurze (CSP) platformy Azure są obsługiwane w Azure AD B2C. Ta funkcja jest dostępna przy użyciu interfejsów API lub Azure Portal dla Azure AD B2C i dla wszystkich zasobów platformy Azure. Administratorzy subskrypcji CSP mogą łączyć, przenosić i usuwać relacje z Azure AD B2C jak w przypadku innych zasobów platformy Azure.

Skojarzenie między dzierżawą Azure AD B2C i subskrypcją dostawcy usług kryptograficznych platformy Azure nie ma wpływ na zarządzanie Azure AD B2C przy użyciu kontroli dostępu opartej na rolach. Kontrola dostępu oparta na rolach jest realizowana przy użyciu ról opartych na dzierżawie, a nie ról opartych na subskrypcji.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Zmień subskrypcję rozliczeń dzierżawy Azure AD B2C

### <a name="move-using-azure-resource-manager"></a>Przenieś przy użyciu Azure Resource Manager

Dzierżawy Azure AD B2C można przenieść do innej subskrypcji przy użyciu Azure Resource Manager, jeśli subskrypcje źródłowe i docelowe istnieją w ramach tej samej dzierżawy Azure Active Directory.

Aby dowiedzieć się, jak przenieść zasoby platformy Azure, takie jak dzierżawa Azure AD B2C do innej subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Przed rozpoczęciem przenoszenia należy przeczytać cały artykuł, aby w pełni zrozumieć ograniczenia i wymagania dotyczące takiego przeniesienia. Oprócz instrukcji dotyczących przenoszenia zasobów zawiera informacje o kluczowym znaczeniu, takie jak lista kontrolna przedniesiona i sposób sprawdzania poprawności operacji przenoszenia.

### <a name="move-by-un-linking-and-re-linking"></a>Przenieś przez cofnięcie konsolidacji i ponowne łączenie

Jeśli subskrypcje źródłowe i docelowe są skojarzone z różnymi dzierżawcami Azure Active Directory, nie można wykonać przechodzenia przez Azure Resource Manager, jak wyjaśniono powyżej. Jednak nadal można uzyskać ten sam wynik końcowy przez odłączenie dzierżawy Azure AD B2C z subskrypcji źródłowej i ponowne połączenie jej z subskrypcją docelową. Ta metoda jest bezpieczna, ponieważ jedynym usuwanym obiektem jest *link rozliczenia*, a nie dzierżawa Azure AD B2C. Nie wpłynie to na żadną z użytkowników, aplikacji, przepływów użytkowników itp.

1. W samym katalogu Azure AD B2C [zapraszaj użytkownika-gościa](user-overview.md#guest-user) od docelowej dzierżawy usługi Azure AD (tej, która jest połączona z docelową subskrypcją platformy Azure) i upewnij się, że ten użytkownik ma rolę **administratora globalnego** w programie Azure AD B2C.
1. Przejdź do *zasobu platformy Azure* reprezentującego Azure AD B2C w źródłowej subskrypcji platformy Azure zgodnie z opisem w sekcji [Zarządzanie zasobami dzierżawy Azure AD B2C](#manage-your-azure-ad-b2c-tenant-resources) powyżej. Nie przełączaj do rzeczywistej dzierżawy Azure AD B2C.
1. Kliknij przycisk **Usuń** na stronie **Przegląd** . *Nie spowoduje to usunięcia powiązanych* użytkowników lub aplikacji dzierżawy Azure AD B2C. Powoduje tylko usunięcie linku do rozliczeń z subskrypcji źródłowej.
1. Zaloguj się do Azure Portal przy użyciu konta użytkownika, który został dodany jako administrator w Azure AD B2C w kroku 1. Następnie przejdź do docelowej subskrypcji platformy Azure, która jest połączona z dzierżawą Azure Active Directory docelowej. 
1. Ponownie Ustanów link rozliczeń w ramach subskrypcji docelowej, wykonując powyższą procedurę [tworzenia łącza](#create-the-link) .
1. Zasób Azure AD B2C został przeniesiony do docelowej subskrypcji platformy Azure (połączonej z Azure Active Directoryą docelową) i zostanie naliczona za pośrednictwem tej subskrypcji do przodu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać najnowsze informacje o cenach, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
