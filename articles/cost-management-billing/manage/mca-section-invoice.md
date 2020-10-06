---
title: Organizowanie faktury zgodnie z własnymi potrzebami — Azure
description: Dowiedz się, jak zorganizować koszty na fakturze. Możesz dostosować konto rozliczeniowe, tworząc profile rozliczeniowe i sekcje faktury.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 6424fc0ff49566fad949b3fba4718acb2bad4cd3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362779"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organizowanie kosztów przez dostosowanie konta rozliczeniowego

Konto rozliczeniowe dla Umowy z Klientem Microsoft zapewnia elastyczność organizowania kosztów w zależności od potrzeb: według działu, projektu lub środowiska programistycznego.

W tym artykule opisano, jak można zorganizować koszty za pomocą witryny Azure Portal. Ten artykuł dotyczy konta rozliczeniowego dla Umowy z Klientem Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Przygotowanie struktury konta za pomocą profilów rozliczeniowych i sekcji faktury

Na koncie rozliczeniowym dla Umowy z Klientem Microsoft do organizowania kosztów służą profile rozliczeniowe i sekcje faktury.

![Zrzut ekranu przedstawiający hierarchię rozliczeń dla Umowy z Klientem Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Profil rozliczeniowy

Profil rozliczeniowy reprezentuje fakturę i powiązane informacje rozliczeniowe, takie jak formy płatności i adres rozliczeniowy. Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera opłaty za użycie platformy Azure i inne zakupy w poprzednim miesiącu.

Profil rozliczeniowy jest tworzony automatycznie wraz z kontem rozliczeniowym podczas tworzenia konta na platformie Azure. Aby zorganizować koszty w postaci wielu faktur miesięcznych, można utworzyć dodatkowe profile rozliczeniowe.

> [!IMPORTANT]
>
> Utworzenie dodatkowych profilów rozliczeniowych może mieć wpływ na całkowity koszt. Aby uzyskać więcej informacji, zobacz [Kwestie do rozważenia podczas dodawania nowych profilów rozliczeniowych](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Sekcja faktury

Sekcja faktury reprezentuje grupę kosztów na fakturze. Sekcja faktury jest automatycznie tworzona dla każdego profilu rozliczeniowego na Twoim koncie. Aby zorganizować koszty stosownie do własnych potrzeb, możesz utworzyć dodatkowe sekcje. Każda sekcja faktury widoczna na fakturze zawiera opłaty za dany miesiąc.

Na poniższej ilustracji przedstawiono fakturę z dwiema sekcjami faktury — dotyczącymi inżynierii i marketingu. Na fakturze widoczne jest podsumowanie i szczegółowe opłaty dla każdej sekcji. Ceny pokazane na ilustracji są tylko przykładowe i nie stanowią rzeczywistych cen usług platformy Azure.

![Obraz przedstawiający fakturę z sekcjami](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struktura konta rozliczeniowego dla typowych scenariuszy

W tej sekcji opisano typowe scenariusze organizowania kosztów i właściwe dla nich struktury kont rozliczeniowych:

|Scenariusz  |Struktura  |
|---------|---------|
|Jacek zakłada konto na platformie Azure i potrzebuje pojedynczej faktury miesięcznej. | Profil rozliczeniowy i sekcja faktury. Ta struktura jest automatycznie konfigurowana dla Jacka podczas zakładania konta na platformie Azure i nie wymaga żadnych dodatkowych kroków. |

![Grafika informacyjna dotycząca jednego scenariusza rozliczania.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenariusz  |Struktura  |
|---------|---------|
|Firma Contoso to mała organizacja, która potrzebuje jednej faktury miesięcznej, ale z grupowaniem kosztów według działów — marketingu i inżynierii.  | Profil rozliczeniowy dla firmy Contoso i sekcje faktury dla działów marketingu i inżynierii. |

![Grafika informacyjna dotycząca wielu scenariuszów rozliczania.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenariusz  |Struktura  |
|---------|---------|
|Fabrikam to organizacja średniej wielkości, która potrzebuje oddzielnych faktur dla działów inżynierii i marketingu. Dla działu inżynierii koszty mają być grupowane według środowisk — produkcyjnego i programistycznego.  | Profile rozliczeniowe dla działów marketingu i inżynierii. Dla działu inżynierii sekcje faktury dotyczące środowiska produkcyjnego i programistycznego. |

![Grafika informacyjna dotycząca wielu scenariuszów rozliczania z oddzielnymi rozliczeniami dla środowisk produkcyjnego i programistycznego.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Tworzenie nowej sekcji faktury

Aby utworzyć sekcję faktury, musisz być **właścicielem profilu rozliczeniowego** lub **współautorem profilu rozliczeniowego**. Aby uzyskać więcej informacji, zobacz [Zarządzanie sekcjami faktury dla profilu rozliczeniowego](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/mca-section-invoice/search-cmb.png)

3. W okienku po lewej stronie wybierz pozycję **Profile rozliczeniowe**. Wybierz z listy profil rozliczeniowy. Nowa sekcja zostanie pokazana na fakturze wybranego profilu rozliczeniowego.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Wybierz pozycję **Sekcje faktury** z okienka po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.

   [![Zrzut ekranu przedstawiający dodawanie sekcji faktury](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Wprowadź nazwę sekcji faktury.

   [![Zrzut ekranu przedstawiający stronę tworzenia sekcji faktury](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Wybierz pozycję **Utwórz**.

## <a name="create-a-new-billing-profile"></a>Tworzenie nowego profilu rozliczeniowego

Aby utworzyć profil rozliczeniowy, musisz być **właścicielem konta rozliczeniowego** lub **współautorem konta rozliczeniowego**. Aby uzyskać więcej informacji, zobacz [Zarządzanie profilami rozliczeniowymi dla konta rozliczeniowego](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Utworzenie dodatkowych profilów rozliczeniowych może mieć wpływ na całkowity koszt. Aby uzyskać więcej informacji, zobacz [Kwestie do rozważenia podczas dodawania nowych profilów rozliczeniowych](#things-to-consider-when-adding-new-billing-profiles).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/mca-section-invoice/search-cmb.png)

3. Wybierz pozycję **Profile rozliczeniowe** z okienka po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.

   [![Zrzut ekranu przedstawiający listę profilów rozliczeniowych z wybraną pozycją Dodaj.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Jeśli nie widzisz przycisku Dodaj na stronie Profil rozliczeniowy, ta funkcja nie jest dostępna dla Twojego konta. Obecnie jest ona dostępna tylko dla kont, które zostały skonfigurowane podczas pracy z przedstawicielem firmy Microsoft.

4. Wypełnij formularz i kliknij przycisk **Utwórz**.

   [![Zrzut ekranu przedstawiający stronę tworzenia profilu rozliczeniowego](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Pole  |Definicja  |
    |---------|---------|
    |Nazwa     | Nazwa wyświetlana ułatwiająca znajdowanie profilu rozliczeniowego w witrynie Azure Portal.  |
    |Numer zamówienia zakupu    | Opcjonalny numer zamówienia zakupu. Numer zamówienia zakupu będzie pokazywany na fakturach wygenerowanych dla profilu rozliczeniowego. |
    |Adres do faktury   | Adres rozliczeniowy będzie pokazywany na fakturach wygenerowanych dla profilu rozliczeniowego. |
    |Wysyłanie faktury pocztą e-mail   | Zaznacz pole wysyłania faktury pocztą e-mail, aby otrzymywać faktury dla tego profilu rozliczeniowego za pośrednictwem poczty e-mail. Jeśli nie zdecydujesz się na to, możesz wyświetlać i pobierać faktury w witrynie Azure Portal.|

5. Wybierz pozycję **Utwórz**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Łączenie opłat z sekcjami faktury i profilami rozliczeniowymi

Po dostosowaniu konta rozliczeniowego do własnych potrzeb możesz połączyć subskrypcje i inne produkty z odpowiednią sekcją faktury i profilem rozliczeniowym.

### <a name="link-a-new-subscription"></a>Łączenie nowej subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Subskrypcje**.

   [![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w witrynie Azure Portal.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Wybierz pozycję **Dodaj** w górnej części strony.

   ![Zrzut ekranu przedstawiający przycisk Dodaj w widoku Subskrypcje dla nowej subskrypcji.](./media/mca-section-invoice/subscription-add.png)

4. Jeśli masz dostęp do wielu kont rozliczeniowych, wybierz konto rozliczeniowe dla Twojej Umowy z Klientem Microsoft.

   ![Zrzut ekranu przedstawiający okienko Tworzenie subskrypcji.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Wybierz profil rozliczeniowy, dla którego zostanie naliczona opłata za użycie subskrypcji. Opłaty za użycie platformy Azure i inne zakupy dla tej subskrypcji będą naliczane na fakturze w ramach wybranego profilu rozliczeniowego.

6. Wybierz sekcję faktury, aby połączyć opłaty za subskrypcję. Opłaty będą pokazywane w tej sekcji na fakturze w ramach profilu rozliczeniowego.

7. Wybierz plan platformy Azure, a następnie wprowadź przyjazną nazwę subskrypcji.

9. Kliknij pozycję **Utwórz**.  

### <a name="link-existing-subscriptions-and-products"></a>Łączenie istniejących subskrypcji i produktów

Jeśli masz istniejące subskrypcje platformy Azure lub inne produkty, takie jak zasoby z usług Azure Marketplace i AppSource, możesz je przenieść z istniejącej sekcji faktury do innej sekcji faktury, aby przeorganizować koszty.

> [!IMPORTANT]
>
> Subskrypcje i inne produkty można przenosić tylko między sekcjami faktury, które należą do tego samego profilu rozliczeniowego. Przenoszenie subskrypcji i produktów między sekcjami faktury w różnych profilach rozliczeniowych nie jest obsługiwane.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie narzędzia Cost Management + Billing w witrynie Azure Portal.](./media/mca-section-invoice/search-cmb.png)

3. Aby połączyć subskrypcję z nową sekcją faktury, wybierz pozycję **Subskrypcje platformy Azure** z lewej strony ekranu. W przypadku innych produktów, takich jak zasoby z usług Azure Marketplace i AppSource, wybierz pozycję **Opłaty cykliczne**.

   [![Zrzut ekranu pokazujący opcję zmiany sekcji faktury](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Kliknij na stronie wielokropek (trzy kropki) dla subskrypcji lub produktu, który chcesz połączyć z nową sekcją faktury. Wybierz pozycję **Zmień sekcję faktury**.

5. Wybierz nową sekcję faktury z listy rozwijanej. Na liście rozwijanej będą widoczne tylko te sekcje faktury, które są skojarzone z tym samym profilem rozliczeniowym co istniejąca sekcja faktury.

    [![Zrzut ekranu przedstawiający wybieranie nowej sekcji faktury](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Wybierz pozycję **Zapisz**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Kwestie do rozważenia przy dodawaniu nowych profilów rozliczeniowych

### <a name="azure-usage-charges-may-be-impacted"></a>Może to mieć wpływ na opłaty za użycie platformy Azure

Na koncie rozliczeniowym dla Umowy z Klientem Microsoft użycie platformy Azure jest agregowane miesięcznie dla każdego profilu rozliczeniowego. Ceny zasobów platformy Azure z cennikami warstwowymi są określane na podstawie użycia osobno dla każdego profilu rozliczeniowego. Podczas obliczania ceny użycie nie jest agregowane między profilami rozliczeniowymi. Może to mieć wpływ na ogólne koszty użycia platformy Azure dla kont z wieloma profilami rozliczeniowymi.

Przyjrzyjmy się przykładowi z odmiennymi kosztami dla dwóch scenariuszy. Ceny użyte w scenariuszach są tylko przykładowe i nie stanowią rzeczywistych cen usług platformy Azure.

#### <a name="you-only-have-one-billing-profile"></a>Masz tylko jeden profil rozliczeniowy.

Załóżmy, że korzystasz z magazynu blokowych obiektów blob platformy Azure, którego cena to 0,00184 USD za GB dla pierwszych 50 terabajtów (TB), a następnie 0,00177 USD za GB dla następnych 450 terabajtów (TB). W subskrypcjach rozliczanych w ramach Twojego profilu rozliczeniowego użyto 100 TB danych.

|  Cennik warstwy (USD) |Liczba | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB za pierwsze 50 TB/miesiąc    | 50 TB        | 92,0   |
|1,77 za TB za następne 450 TB/miesiąc    |  50 TB         | 88,5   |
|Łącznie     |     100 TB  | 180,5

Łączne opłaty za użycie 100 TB danych w tym scenariuszu to **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Masz wiele profilów rozliczeniowych.

Załóżmy, że masz utworzony inny profil rozliczeniowy i użyto 50 GB danych za pośrednictwem subskrypcji rozliczanych w ramach pierwszego profilu rozliczeniowego i 50 GB danych za pośrednictwem subskrypcji rozliczanych w ramach drugiego profilu rozliczeniowego.

`Charges for the first billing profile`

|  Cennik warstwy (USD) |Liczba | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB za pierwsze 50 TB/miesiąc    | 50 TB        | 92,0  |
|1,77 za TB za następne 450 TB/miesiąc    |  0 TB         | 0.0  |
|Łącznie     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Cennik warstwy (USD) |Liczba | Kwota (USD)|
|---------|---------|---------|
|1,84 za TB za pierwsze 50 TB/miesiąc    | 50 TB        | 92,0  |
|1,77 za TB za następne 450 TB/miesiąc    |  0 TB         | 0.0  |
|Łącznie     |     50 TB  | 92,0

Łączne opłaty za użycie 100 TB danych w tym scenariuszu to **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Korzyści z rezerwacji platformy Azure mogą nie dotyczyć wszystkich subskrypcji

Rezerwacje platformy Azure z zakresem współużytkowanym są stosowane do subskrypcji w pojedynczym profilu rozliczeniowym (nie są współużytkowane przez różne profile rozliczeniowe).

![Grafika informacyjna dotycząca aplikacji rezerwacji dla innej struktury konta rozliczeniowego](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na ilustracji powyżej firma Contoso ma dwie subskrypcje. Korzyść z rezerwacji platformy Azure jest stosowana inaczej zależnie od struktury konta rozliczeniowego. W scenariuszu po lewej stronie korzyść z rezerwacji jest stosowana do obu subskrypcji, które są rozliczane w ramach profilu rozliczeniowego działu inżynierii. W scenariuszu po prawej stronie korzyść z rezerwacji zostanie zastosowana tylko do subskrypcji 1, ponieważ jest to jedyna subskrypcja rozliczana w ramach profilu rozliczeniowego działu inżynierii.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie dodatkowej subskrypcji platformy Azure dla Umowy klienta firmy Microsoft](create-subscription.md)
- [Zarządzanie rolami rozliczeniowymi w witrynie Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure od użytkowników z innych kont rozliczeniowych](mca-request-billing-ownership.md)
