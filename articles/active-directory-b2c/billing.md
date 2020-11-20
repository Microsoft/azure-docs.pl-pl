---
title: Model rozliczeń dla Azure Active Directory B2C
description: Dowiedz się więcej na temat modelu rozliczeń miesięcznych użytkowników usługi Azure AD B2C's (MAU), sposobu łączenia dzierżawy Azure AD B2C z subskrypcją platformy Azure oraz wybierania odpowiednich cen warstwy Premium.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: edff5354d0565bd32cd0332b4aa0f215c2980d73
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949789"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model rozliczeń dla Azure Active Directory B2C

Ceny za Azure Active Directory B2C (Azure AD B2C) są oparte na miesięcznych aktywnych użytkowników (MAU), czyli liczbie unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego. Ten model rozliczeń dotyczy zarówno dzierżawców Azure AD B2C, jak i [współpracy użytkowników Gości usługi Azure AD (B2B)](../active-directory/external-identities/external-identities-pricing.md). Rozliczanie MAU pomaga obniżyć koszty, oferując bezpłatną warstwę i elastyczne, przewidywalne ceny. W tym artykule dowiesz się więcej na temat rozliczeń MAU, łączenia dzierżawców Azure AD B2C z subskrypcją i zmieniania warstwy cenowej.

> [!IMPORTANT]
> Ten artykuł nie zawiera szczegółów cennika. Aby uzyskać najnowsze informacje dotyczące rozliczeń i cen użytkowania, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="what-do-i-need-to-do"></a>Co mam zrobić?

Aby skorzystać z rozliczeń MAU, dzierżawca Azure AD B2C musi być połączony z subskrypcją platformy Azure. Może być również konieczne przełączenie dzierżawy Azure AD B2C do innej warstwy cenowej, jeśli chcesz użyć funkcji Azure AD B2C Premium P2, na przykład dostępu warunkowego opartego na ryzyku.

|Jeśli Twoja dzierżawa to:  |Należy:  |
|---------|---------|
| Dzierżawa Azure AD B2C już rozliczana na podstawie MAU     | Nic nie robić. Gdy użytkownicy uwierzytelniają się w dzierżawie Azure AD B2C, zostanie automatycznie rozliczona przy użyciu modelu rozliczania opartego na MAU.        |
| Dzierżawa Azure AD B2C nie została jeszcze połączona z subskrypcją     |  [Połącz dzierżawę Azure AD B2C z subskrypcją](#link-an-azure-ad-b2c-tenant-to-a-subscription) w celu aktywowania rozliczeń Mau.     |
| Dzierżawa Azure AD B2C, która została połączona z subskrypcją przed 1 listopada 2019    | [Przejdź do Mau rozliczeń (zalecane)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)lub Pozostań w modelu rozliczania na uwierzytelnianie.     |
| Dzierżawa Azure AD B2C i chcesz korzystać z funkcji premium (takich jak dostęp warunkowy oparty na ryzyku)    | [Przejdź do warstwy cenowej usługi Azure AD](#change-your-azure-ad-pricing-tier) , która obsługuje funkcje, których chcesz użyć.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Informacje o modelu rozliczania miesięcznych użytkowników (MAU)

MAU naliczanie opłat za dzierżawy Azure AD B2C **1 listopada 2019**. Wszystkie dzierżawy Azure AD B2C utworzone i połączone z subskrypcją w dniu lub po tej dacie są rozliczane na podstawie MAU. Jeśli masz dzierżawę Azure AD B2C, która nie została połączona z subskrypcją, musisz teraz wykonać tę czynność. Jeśli masz istniejącą dzierżawę Azure AD B2C, która została połączona z subskrypcją przed 1 listopada 2019, zalecamy uaktualnienie do modelu rozliczania miesięcznych aktywnych użytkowników (MAU) lub można pozostać w modelu rozliczania za uwierzytelnianie.
  
Dzierżawa Azure AD B2C musi być również połączona z odpowiednią warstwą cenową platformy Azure na podstawie funkcji, które mają być używane. Funkcje Premium wymagają Azure AD B2C [cen Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Może być konieczne uaktualnienie warstwy cenowej w miarę korzystania z nowych funkcji. Na przykład dostęp warunkowy, należy wybrać warstwę cenową usługi Azure AD B2C Premium P2 dla dzierżawy.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Łączenie dzierżawy Azure AD B2C z subskrypcją

Opłaty za użycie dla Azure Active Directory B2C (Azure AD B2C) są rozliczane w ramach subskrypcji platformy Azure. Musisz jawnie połączyć dzierżawę Azure AD B2C z subskrypcją platformy Azure, tworząc *zasób* Azure AD B2C w ramach docelowej subskrypcji platformy Azure. Kilka zasobów Azure AD B2C można utworzyć w ramach jednej subskrypcji platformy Azure, a także innych zasobów platformy Azure, takich jak maszyny wirtualne, konta magazynu i Logic Apps. Wszystkie zasoby w ramach subskrypcji można wyświetlić, przechodząc do dzierżawy usługi Azure Active Directory (Azure AD), z którą skojarzona jest subskrypcja.

Subskrypcja połączona z dzierżawą Azure AD B2C może być używana do rozliczania Azure AD B2C użycia lub innych zasobów platformy Azure, w tym dodatkowych zasobów Azure AD B2C. Nie można jej użyć do dodania innych licencji usługi Azure opartych na licencji lub pakietu Office 365 w ramach dzierżawy Azure AD B2C.

### <a name="prerequisites"></a>Wymagania wstępne

* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
* [Azure AD B2C dzierżawy](tutorial-create-tenant.md) , którą chcesz połączyć z subskrypcją
  * Musisz być administratorem dzierżawy
  * Dzierżawca nie może już być połączony z subskrypcją

### <a name="create-the-link"></a>Utwórz łącze

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający subskrypcję platformy Azure, której chcesz użyć (*nie* katalog zawierający dzierżawę Azure AD B2C).
3. Wybierz pozycję **Utwórz zasób**, wprowadź wartość `Active Directory B2C` w polu **Wyszukaj w witrynie Marketplace** , a następnie wybierz pozycję **Azure Active Directory B2C**.
4. Wybierz pozycję **Utwórz**.
5. Wybierz pozycję **Połącz istniejącą dzierżawę Azure AD B2C z moją subskrypcją platformy Azure**.
6. Wybierz **dzierżawę Azure AD B2C** z listy rozwijanej. Wyświetlane są tylko dzierżawy, dla których jesteś administratorem globalnym i które nie są już połączone z subskrypcją. Pole **nazwy zasobu Azure AD B2C** jest wypełniane nazwą domeny wybranej dzierżawy Azure AD B2C.
7. Wybierz aktywną **subskrypcję** platformy Azure, której jesteś administratorem.
8. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie określ **lokalizację grupy zasobów**. Ustawienia grupy zasobów nie mają wpływu na lokalizację dzierżawy Azure AD B2C, wydajność ani stan rozliczeń.
9. Wybierz pozycję **Utwórz**.

    ![Strona tworzenia zasobów Azure AD B2C w programie Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Po wykonaniu tych kroków dla dzierżawy Azure AD B2C subskrypcja platformy Azure jest rozliczana na podstawie szczegółowych informacji dotyczących usługi Azure Direct lub Umowa Enterprise, jeśli ma to zastosowanie.

## <a name="change-your-azure-ad-pricing-tier"></a>Zmień warstwę cenową usługi Azure AD

Dzierżawa musi być połączona z odpowiednią warstwą cenową platformy Azure na podstawie funkcji, które mają być używane z dzierżawą Azure AD B2C. Funkcje Premium wymagają Azure AD B2C Premium P1 lub P2, zgodnie z opisem w [Azure Active Directory B2C cenach](https://azure.microsoft.com/pricing/details/active-directory-b2c/). W niektórych przypadkach należy uaktualnić warstwę cenową podczas korzystania z nowych funkcji. Na przykład jeśli chcesz używać ochrony tożsamości, dostępu warunkowego opartego na ryzyku i wszelkich przyszłych możliwości Premium P2 z Azure AD B2C, musisz wybrać warstwę cenową Azure AD B2C Premium P2 dla dzierżawy.

Aby zmienić warstwę cenową, wykonaj następujące kroki.

1. Zaloguj się w witrynie Azure Portal.

2. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający subskrypcję platformy Azure, z którą jest połączona dzierżawa usługi Azure B2C (*nie* wybieraj samej dzierżawy Azure AD B2C).

3. W polu wyszukiwania w górnej części portalu wprowadź nazwę dzierżawy Azure AD B2C. Następnie wybierz dzierżawcę w wynikach wyszukiwania w obszarze **zasoby**.

4. Na stronie **Przegląd** zasobów w obszarze **warstwa cenowa** wybierz pozycję **Zmień**.

   ![Zmiana warstwy cenowej](media/billing/change-pricing-tier.png)
 
5. Wybierz warstwę cenową obejmującą funkcje, które chcesz włączyć.

   ![Wybierz warstwę cenową](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Przełączanie do rozliczeń MAU (przed listopadem Azure AD B2C 2019 dzierżawców)

Jeśli połączysz dzierżawę Azure AD B2C z subskrypcją przed **1 listopada 2019**, używany jest poprzedni model rozliczeń na uwierzytelnienie. Zalecamy przeprowadzenie uaktualnienia do modelu rozliczania miesięcznych aktywnych użytkowników (MAU). Opcje rozliczeń są konfigurowane w zasobie Azure AD B2C.

Przełączenie na rozliczenia miesięcznie aktywnych użytkowników (MAU) jest **nieodwracalne**. Po przekonwertowaniu zasobu Azure AD B2C na model rozliczeń oparty na MAU nie można przywrócić tego zasobu do modelu rozliczania na uwierzytelnianie.

Oto jak włączyć MAU rozliczeń dla istniejącego zasobu Azure AD B2C:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako właściciel subskrypcji z dostępem administracyjnym do zasobu Azure AD B2C.

2. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog Azure AD B2C, który chcesz uaktualnić, aby Mau rozliczanie.<br/>

    ![Filtr katalogów i subskrypcji w Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.

4. Na stronie **Przegląd** dzierżawy Azure AD B2C wybierz link w obszarze **nazwa zasobu**. Nastąpi przekierowanie do zasobu Azure AD B2C w dzierżawie usługi Azure AD.<br/>

    ![Azure AD B2C wyróżnionego linku zasobu Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Na stronie **Przegląd** zasobu Azure AD B2C w obszarze **jednostki rozliczeniowe** wybierz łącze **uwierzytelnianie (Zmień na Mau)** .<br/>

    ![Zmień na link MAU wyróżniony w Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Wybierz pozycję **Potwierdź** , aby zakończyć uaktualnianie do Mau rozliczeń.<br/>

    ![Okno dialogowe potwierdzenia rozliczenia opartego na MAU w Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Czego można oczekiwać po przejściu do MAU rozliczeń za uwierzytelnianie na podstawie uwierzytelniania

Pomiar oparty na MAU jest włączony zaraz po wykonaniu tej subskrypcji/właściciela zasobu, potwierdzić zmianę. Rachunek miesięczny będzie uwzględniać jednostki uwierzytelniania, które są rozliczane do momentu zmiany, oraz nowe jednostki MAU rozpoczynające się od zmiany.

Użytkownicy nie są wliczane podwójnie w ciągu miesiąca przejścia. Unikatowi aktywni użytkownicy, którzy uwierzytelniają się przed zmianą, są obciążani stawką za uwierzytelnianie w miesiącu kalendarzowym. Ci użytkownicy nie są włączeni do obliczenia MAU w pozostałej części cyklu rozliczeniowego subskrypcji. Przykład:

* Dzierżawa contoso B2C ma 1 000 użytkowników. 250 użytkowników jest aktywnych w danym miesiącu. Administrator subskrypcji zmieni się z uwierzytelniania na comiesięcznych użytkowników aktywnych (MAU) w dziesiątym miesiącu.
* Naliczanie opłat za 1 dziesiątki jest rozliczane przy użyciu modelu na uwierzytelnianie.
  * Jeśli 100 użytkowników loguje się w tym okresie (1 – 10), użytkownicy są oznaczeni jako *zapłacone za miesiąc*.
* Rozliczenia od dziesiątego (czasu obowiązywania przejścia) są rozliczane według stawki za MAU.
  * Jeśli w tym okresie zostanie zastosowana dodatkowa 150 użytkownicy, opłaty są naliczane tylko za dodatkowe 150.
  * Kontynuacja działania pierwszych 100 użytkowników nie ma wpływu na rozliczenia w pozostałej części miesiąca kalendarzowego.

W okresie rozliczeniowym przejścia właściciel subskrypcji będzie prawdopodobnie widział wpisy dla obu metod (za uwierzytelnianie i za MAU), które są wyświetlane w ich zestawie rozliczeń subskrypcji platformy Azure:

* Wpis do użycia do daty/godziny zmiany, która odzwierciedla na uwierzytelnianie.
* Wpis do użycia po zmianie, która odzwierciedla miesięcznych użytkowników aktywnych (MAU).

Aby uzyskać najnowsze informacje o rozliczeniach użycia i cenach dla Azure AD B2C, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy Azure AD B2C

Po utworzeniu zasobu Azure AD B2C w ramach subskrypcji platformy Azure powinien zostać wyświetlony nowy zasób typu "dzierżawa B2C" z innymi zasobami platformy Azure.

Tego zasobu można użyć do:

* Przejdź do subskrypcji, aby przejrzeć informacje o rozliczeniach
* Uzyskaj identyfikator dzierżawy Azure AD B2C dzierżawy w formacie identyfikatora GUID
* Przejdź do dzierżawy Azure AD B2C
* Prześlij żądanie pomocy technicznej
* Przenieś zasób dzierżawy Azure AD B2C do innej subskrypcji platformy Azure lub grupy zasobów

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

### <a name="move-by-unlinking-and-relinking"></a>Przenieś przez odłączenie i ponowne łączenie

Jeśli subskrypcje źródłowe i docelowe są skojarzone z różnymi dzierżawcami Azure Active Directory, nie można wykonać przechodzenia przez Azure Resource Manager, jak wyjaśniono powyżej. Jednak nadal można uzyskać ten sam wynik, odłączając dzierżawę Azure AD B2C od subskrypcji źródłowej i ponownie łącząc ją z subskrypcją docelową. Ta metoda jest bezpieczna, ponieważ jedynym usuwanym obiektem jest *link rozliczenia*, a nie dzierżawa Azure AD B2C. Nie wpłynie to na żadną z użytkowników, aplikacji, przepływów użytkowników itp.

1. W samym katalogu Azure AD B2C [zapraszaj użytkownika-gościa](user-overview.md#guest-user) od docelowej dzierżawy usługi Azure AD (tej, która jest połączona z docelową subskrypcją platformy Azure) i upewnij się, że ten użytkownik ma rolę **administratora globalnego** w programie Azure AD B2C.
1. Przejdź do *zasobu platformy Azure* reprezentującego Azure AD B2C w źródłowej subskrypcji platformy Azure zgodnie z opisem w sekcji [Zarządzanie zasobami dzierżawy Azure AD B2C](#manage-your-azure-ad-b2c-tenant-resources) powyżej. Nie przełączaj do rzeczywistej dzierżawy Azure AD B2C.
1. Wybierz przycisk **Usuń** na stronie **Przegląd** . *Nie spowoduje to usunięcia powiązanych* użytkowników lub aplikacji dzierżawy Azure AD B2C. Powoduje tylko usunięcie linku do rozliczeń z subskrypcji źródłowej.
1. Zaloguj się do Azure Portal przy użyciu konta użytkownika, który został dodany jako administrator w Azure AD B2C w kroku 1. Następnie przejdź do docelowej subskrypcji platformy Azure, która jest połączona z dzierżawą Azure Active Directory docelowej. 
1. Ponownie Ustanów link rozliczeń w ramach subskrypcji docelowej, wykonując powyższą procedurę [tworzenia łącza](#create-the-link) .
1. Zasób Azure AD B2C został przeniesiony do docelowej subskrypcji platformy Azure (połączonej z Azure Active Directoryą docelową) i zostanie naliczona za pośrednictwem tej subskrypcji od teraz.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać najnowsze informacje o cenach, zobacz [Cennik usługi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).