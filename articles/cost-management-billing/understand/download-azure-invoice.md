---
title: Wyświetlanie i pobieranie faktury platformy Azure
description: Dowiedz się, jak wyświetlać i pobierać faktury platformy Azure. Możesz pobrać fakturę w Azure Portal lub wysłać ją w wiadomości e-mail.
keywords: faktura rozliczeniowa, pobieranie faktury, faktura platformy azure, dane użycia platformy azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 37ce1a292b6ff2efe0abecdb2ab934f096689f87
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "105560807"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Wyświetlanie i pobieranie faktury platformy Microsoft Azure

Fakturę możesz pobrać w [witrynie Azure Portal](https://portal.azure.com/) lub zażądać jej wysłania w wiadomości e-mail. Jeśli jesteś klientem platformy Azure z umową Enterprise Agreement (umową EA), nie możesz pobrać faktur swojej organizacji. Zamiast tego faktury są wysyłane do osoby skonfigurowanej jako odbiorca faktur dla rejestracji.

## <a name="when-invoices-are-generated"></a>Czas generowania faktur

Faktura jest generowana na podstawie typu konta rozliczeniowego. Faktury są tworzone dla kont rozliczeniowych programu Microsoft Online Service Program (MOSP), Umowy z Klientem Microsoft (MCA) i umowy Microsoft Partner Agreement (MPA). Faktury są także generowane dla kont rozliczeniowych umowy Enterprise Agreement (EA). Jednak faktury dla kont z rozliczeniowych umowy EA nie są wyświetlane w witrynie Azure Portal.

Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](../manage/view-all-accounts.md).

### <a name="invoice-status"></a>Stan faktury

Po przejrzeniu stanu faktury w Azure Portal każda faktura ma jeden z następujących symboli stanu.

|  Symbol stanu | Opis  |
|---|---|
| ![Symbol stanu ukończenia](./media/download-azure-invoice/due.svg) | *Jest wyświetlany* po wygenerowaniu faktury, ale jeszcze nie została wypłacona. |
| ![Ostatni symbol stanu do wykonania](./media/download-azure-invoice/past-due.svg)  | *Data ukończenia* jest wyświetlana, gdy platforma Azure próbowała obciążać Twoją formę płatności, ale płatność została odrzucona. |
| ![Symbol płatnego stanu](./media/download-azure-invoice/paid.svg)  | Stan *płatny* jest wyświetlany, gdy platforma Azure pomyślnie rozliczyła metodę płatności. |

Po utworzeniu faktury zostanie ona wyświetlona w Azure Portal ze stanem *ukończenia* . Stan ukończenia jest normalny i oczekiwany.  

Gdy faktura nie została zapłacona, jego stan jest pokazywany jako *zaległy*. Zaległa subskrypcja zostanie wyłączona, jeśli faktura nie zostanie zapłacona.

## <a name="invoices-for-mosp-billing-accounts"></a>Zarządzanie kontami rozliczeniowymi programu MOSP

Konto rozliczeniowe programu MOSP jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej platformy Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe dla umowy MCA.

Jeśli nie masz pewności co do typu Twojego konta rozliczeniowego, zobacz [Sprawdzanie typu konta rozliczeniowego](../manage/view-all-accounts.md#check-the-type-of-your-account) przed wykonaniem instrukcji opisanych w tym artykule. 

Konto rozliczeniowe programu MOSP może zawierać następujące faktury:

**Opłaty za usługi Azure** — faktura jest generowana dla każdej subskrypcji platformy Azure, która zawiera zasoby platformy Azure używane przez subskrypcję. Faktura zawiera opłaty za okres rozliczeniowy. Okres rozliczeniowy jest określony przez dzień miesiąca utworzenia subskrypcji.

Na przykład Jan tworzy subskrypcję *Azure-01* 5 marca i *Azure-02* 10 marca. Na fakturze za subskrypcję *Azure-01* będą znajdować się opłaty naliczone za okres od piątego dnia miesiąca do czwartego dnia następnego miesiąca. Na fakturze za subskrypcję *Azure-02* będą znajdować się opłaty naliczone za okres od dziesiątego dnia miesiąca do dziewiątego dnia następnego miesiąca. Faktury dla wszystkich subskrypcji platformy Azure są zwykle generowane w dniu miesiąca utworzenia konta, lecz może to być także do dwóch dni później. W tym przykładzie, jeśli Jan utworzył konto 2 lutego, faktury dla obu subskrypcji, *Azure-01* i *Azure-02*, będą standardowo generowane w drugim dniu każdego miesiąca, przy czym możliwe jest, że do dwóch dni później.

**Portal Azure Marketplace, rezerwacje i maszyny wirtualne typu spot** — faktura jest generowana dla rezerwacji, produktów z platformy handlowej i maszyn wirtualnych typu spot kupionych za pomocą subskrypcji. Faktura zawiera odpowiednie opłaty z poprzedniego miesiąca. Na przykład Jan kupił rezerwację 1 marca i kolejną rezerwacją 30 marca. Pojedyncza faktura zostanie wygenerowana dla obu rezerwacji w kwietniu. Faktury za portal Azure Marketplace, rezerwacje i maszyny wirtualne typu spot są zawsze generowana około dziewiątego dnia miesiąca.

Jeśli płacisz za platformę Azure za pomocą karty kredytowej i kupujesz rezerwację, platforma Azure wygeneruje fakturę natychmiast. Jednak w przypadku rozliczania za pomocą faktury opłata za rezerwację zostanie naliczona w ramach następnej faktury miesięcznej.

**Plan pomocy technicznej platformy Azure** — faktura jest generowana co miesiąc dla subskrypcji planu pomocy technicznej. Pierwsza faktura jest generowana w dniu zakupu lub do dwóch dni później. Kolejne faktury za plan pomocy technicznej są zwykle generowane w dniu miesiąca utworzenia konta, lecz może to być także do dwóch dni później.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Pobieranie faktury za subskrypcję platformy Azure dla programu MOSP

Faktura jest generowana tylko dla subskrypcji należącej do konta rozliczeniowego programu MOSP. [Sprawdzanie dostępu do konta programu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Do pobrania faktury jest wymagana rola administratora konta dla subskrypcji. Użytkownik z rolami właściciela, współautora lub czytelnika może pobrać fakturę, jeśli administrator konta udzielił mu uprawnienia. Aby uzyskać więcej informacji, zobacz [Zezwalanie użytkownikom na pobieranie faktur](../manage/manage-billing-access.md#opt-in).

1. Wybierz subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal.
1. Wybierz pozycję **Faktury** w sekcji rozliczeń.  
    ![Zrzut ekranu przedstawiający użytkownika wybierającego opcję faktur dla subskrypcji](./media/download-azure-invoice/select-subscription-invoice.png)
1. Wybierz fakturę, którą chcesz pobrać, a następnie kliknij pozycję **Pobierz faktury**.  
    ![Zrzut ekranu z opcją pobrania dla faktury MOSP](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. Możesz również pobrać dzienny podział zużytych ilości i opłat, klikając ikonę Pobierz, a następnie klikając przycisk **Przygotuj plik użycia platformy Azure** w sekcji Szczegóły użycia. Przygotowanie pliku CSV może potrwać kilka minut.  
    ![Zrzut ekranu przedstawiający stronę Pobieranie faktur i danych użycia](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Aby uzyskać więcej informacji na temat faktury, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](../understand/review-individual-bill.md). Aby uzyskać pomoc w zidentyfikowaniu nietypowych kosztów, zobacz [Analizowanie nieoczekiwanych opłat](analyze-unexpected-charges.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Pobieranie faktury dla planu pomocy technicznej programu MOSP

Faktura jest generowana tylko dla subskrypcji planu pomocy technicznej należącej do konta rozliczeniowego programu MOSP. [Sprawdzanie dostępu do konta programu MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account).

Do pobrania faktury jest wymagana rola administratora konta dla subskrypcji planu pomocy technicznej.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/download-azure-invoice/search-cmb.png)
1. Wybierz pozycję **Faktury** po lewej stronie.
1. Wybierz swoją subskrypcję planu pomocy technicznej.  
    [![Zrzut ekranu przedstawiający listę profilów rozliczeń w ramach planu pomocy technicznej MOSP](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Wybierz fakturę, którą chcesz pobrać, a następnie kliknij pozycję **Pobierz faktury**.  
    ![Zrzut ekranu przedstawiający opcję pobierania faktury planu pomocy technicznej MOSP ](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>Zezwalanie innym osobom na pobieranie faktury subskrypcji

Aby pobrać fakturę:

1.  Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta dla subskrypcji.

2.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/download-azure-invoice/search-cmb.png)

3.  Wybierz pozycję **Faktury** po lewej stronie.

4.  Wybierz subskrypcję platformy Azure, a następnie kliknij pozycję **Zezwól innym osobom na pobranie faktury**.

    [![Zrzut ekranu przedstawiający wybieranie dostępu do faktury](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  Wybierz pozycję **Włączone**, a następnie **Zapisz** w górnej części strony.  
    ![Zrzut ekranu przedstawiający włączanie dostępu do faktury](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> Firma Microsoft nie zaleca udostępniania jakichkolwiek informacji poufnych lub danych osobowych innym firmom. To zalecenie ma zastosowanie do udostępniania rachunku lub faktury na platformie Azure innym firmom w celu optymalizacji kosztów. Aby uzyskać więcej informacji, zobacz https://azure.microsoft.com/support/legal/ i https://www.microsoft.com/trust-center.

## <a name="get-mosp-subscription-invoice-in-email"></a>Uzyskiwanie faktury subskrypcji programu MOSP za pomocą wiadomości e-mail

Musisz mieć rolę administratora konta dla subskrypcji lub planu pomocy technicznej, aby zgodzić się na otrzymywania faktur pocztą e-mail. Faktury e-mail są dostępne tylko dla subskrypcji i planów pomocy technicznej, a nie są dostępne dla rezerwacji ani zakupów w portalu Azure Marketplace. Po udzieleniu zgody możesz dodać kolejnych adresatów, którzy także będą otrzymywać fakturę pocztą e-mail.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
3.  Wybierz pozycję **Faktury** po lewej stronie.
4.  Wybierz subskrypcję platformy Azure lub subskrypcję planu pomocy technicznej, a następnie wybierz pozycję **Otrzymuj faktury pocztą e-mail**.  
    [![Zrzut ekranu przedstawiający opcję Odbierz fakturę za pośrednictwem poczty e-mail](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Kliknij pozycję **Wysyłaj faktury pocztą e-mail** i zaakceptuj warunki.  
    ![Zrzut ekranu pokazujący 2. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoicearticlestep02.png)
6. Faktura jest wysyłana na preferowany adres e-mail komunikacji. Wybierz pozycję **Aktualizuj profil**, aby zaktualizować adres e-mail.  
    ![Zrzut ekranu pokazujący 3. krok przepływu z wyrażeniem zgody](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>Udostępnianie faktury i planu pomocy technicznej

Możesz chcieć udostępnić fakturę za subskrypcję i plan wsparcia dla każdego miesiąca z zespołem księgowym lub wysłać je do jednego z innych adresów e-mail.

1. Wykonaj kroki opisane w sekcji [Pobieranie faktur dla subskrypcji i planu pomocy technicznej za pomocą wiadomości e-mail](#get-mosp-subscription-invoice-in-email) i wybierz pozycję **Konfiguruj adresatów**.  
    [![Zrzut ekranu pokazujący użytkownika wybierającego pozycję Konfiguruj odbiorców](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. Podaj adres e-mail, a następnie wybierz pozycję **Dodaj adresatów**. Możesz dodać wiele adresów e-mail.  
    ![Zrzut ekranu przedstawiający użytkownika dodającego kolejnych adresatów](./media/download-azure-invoice/invoice-article-step04.png)
1. Po dodaniu wszystkich adresów e-mail wybierz pozycję **Gotowe** w dolnej części ekranu.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faktury dla kont rozliczeniowych umów MCA i MPA

Konto rozliczeniowe umowy MCA jest tworzone w ramach współpracy organizacji z przedstawicielem firmy Microsoft w celu podpisania umowy MCA. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia [konta ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), mogą mieć również konto rozliczeniowe dla umowy MCA. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy MCA](../understand/mca-overview.md).

Konto rozliczeniowe dla umowy MPA jest tworzone dla dostawców rozwiązań w chmurze (CSP) w celu zarządzania ich klientami w nowym środowisku handlowym. Partnerzy muszą mieć co najmniej jednego klienta z [planem platformy Azure](/partner-center/purchase-azure-plan), aby mogli zarządzać kontem rozliczeniowym w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy MPA](../understand/mpa-overview.md).

Dla każdego profilu rozliczeniowego na koncie na początku miesiąca jest generowana faktura miesięczna. Faktura zawiera odpowiednie opłaty za wszystkie subskrypcje platformy Azure i inne zakupy w poprzednim miesiącu. Na przykład Jan utworzył subskrypcję *Azure-01* 5 marca i *Azure-02* 10 marca. Subskrypcję *Azure-pomoc-01* kupił 28 marca przy użyciu profilu *Profil rozliczeniowy 01*. Jan otrzyma jedną fakturę na początku kwietnia, która będzie zawierać opłaty za subskrypcje platformy Azure i planu pomocy technicznej.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Pobieranie faktury dla profilu rozliczeniowego programu MCA lub MPA

Aby pobierać faktury z witryny Azure Portal, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego. Użytkownicy z rolą właściciela, współautora lub czytelnika dla konta rozliczeniowego mogą pobierać faktury dla wszystkich profilów rozliczeniowych w ramach konta.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/download-azure-invoice/search-cmb.png)

3. Wybierz pozycję **Faktury** po lewej stronie.

    [![Zrzut ekranu przedstawiający stronę faktur dla konta rozliczeniowego umowy MCA](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. W tabeli faktur wybierz fakturę, którą chcesz pobrać.

5. Kliknij przycisk **Pobierz plik PDF z fakturą** w górnej części strony.

    [![Zrzut ekranu przedstawiający pobieranie faktury w postaci pliku pdf](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. Możesz również pobrać dzienne zestawienie ilości zużytych zasobów i szacowanych opłat, klikając pozycję **Pobierz użycie platformy Azure**. Przygotowanie pliku CSV może potrwać kilka minut.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Pobieranie faktury dla profilu rozliczeniowego za pomocą wiadomości e-mail

Aby zaktualizować preferencje wiadomości e-mail dla faktury, musisz mieć rolę właściciela lub współautora dla profilu rozliczeniowego lub jego konta rozliczeniowego. Po udzieleniu zgody wszyscy użytkownicy z rolą właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego będą otrzymywać fakturę w wiadomości e-mail. 

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1.  Wybierz pozycję **faktury** z lewej strony, a następnie wybierz pozycję **preferencja wiadomość e-mail do faktury** w górnej części strony.  
    [![Zrzut ekranu pokazujący opcję faktury E-mail dla faktur](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Jeśli masz wiele profilów rozliczeń, wybierz profil rozliczeń, a następnie wybierz pozycję **tak**.  
    [![Zrzut ekranu pokazujący opcję wyboru](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Wybierz pozycję **Zapisz**.

Możesz nadać innym osobom dostęp do wyświetlania, pobierania i opłacania faktur, przypisując im rolę menedżera faktur dla profilu rozliczeniowego umowy MCA lub MPA. Jeśli wybrano opcję otrzymywania faktury w wiadomości e-mail, użytkownicy ci będą także otrzymywać faktury w wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1. Po lewej stronie wybierz pozycję **Profile rozliczeniowe**. Z listy profilów rozliczeniowych wybierz profil rozliczeniowy, dla którego chcesz przypisać rolę menedżera faktur.  
   ![Zrzut ekranu przedstawiający listę profilów rozliczeń, w której można wybrać profil rozliczeń](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Wybierz pozycję **Kontrola dostępu (IAM)** po lewej stronie, a następnie wybierz pozycję **Dodaj** w górnej części strony.  
    ![Zrzut ekranu przedstawiający stronę kontroli dostępu](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Z listy rozwijanej Rola wybierz pozycję **Menedżer faktur**. Wprowadź adres e-mail użytkownika, któremu chcesz nadać dostęp. Wybierz przycisk **Zapisz**, aby przypisać rolę.  
    [![Zrzut ekranu pokazujący dodawanie użytkownika jako menedżera faktur](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>Udostępnianie faktury profilu rozliczeń

Możesz chcieć udostępnić fakturę co miesiąc członkom zespołu ds. księgowości lub wysłać je do jednego z innych adresów e-mail bez udzielania zespołowi księgowemu ani innych uprawnień do poczty e-mail do Twojego profilu rozliczeń.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.  
1.  Wybierz pozycję **faktury** z lewej strony, a następnie wybierz pozycję **preferencja wiadomość e-mail do faktury** w górnej części strony.  
    [![Zrzut ekranu pokazujący opcję faktury E-mail dla faktur](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Jeśli masz wiele profilów rozliczeń, wybierz profil rozliczeń.
1.  W sekcji dodatkowi adresaci Dodaj adresy e-mail, aby otrzymywać faktury.
    [![Zrzut ekranu pokazujący dodatkowych adresatów wiadomości e-mail z fakturą](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  Wybierz pozycję **Zapisz**.

##  <a name="why-you-might-not-see-an-invoice"></a>Dlaczego możesz nie widzieć faktury

<a name="noinvoice"></a>

Może istnieć kilka przyczyn, dla których faktura nie jest widoczna:

- Faktura nie jest jeszcze gotowa
    
    - Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni. 

    - Platforma Azure tworzy rozliczenia kilka dni po upływie okresu rozliczeniowego. W związku z tym faktura mogła jeszcze nie zostać wygenerowana.

- Nie masz uprawnień do wyświetlania faktur. 
    
    - W przypadku konta rozliczeniowego umowy MCA lub MPA, aby wyświetlić faktury profilu rozliczeniowego, musisz mieć rolę właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego albo właściciela, współautora lub czytelnika dla konta rozliczeniowego. 
    
    - W przypadku innych kont rozliczeniowych faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta.

- Twoje konto nie obsługuje faktury.

    - W przypadku konta rozliczeniowego programu Microsoft Online Services Program (MOSP) i zarejestrowania się w celu uzyskania bezpłatnego konta platformy Azure lub subskrypcji z miesięczną kwotą środków, otrzymasz fakturę tylko wtedy, gdy miesięczna kwota środków zostanie przekroczona.

    - Użytkownicy z kontem rozliczeniowym w ramach umowy klienta firmy Microsoft (MCA) lub umową partnerską firmy Microsoft (MPA) zawsze otrzymują fakturę.

- Masz dostęp do faktury za pomocą jednego z innych kont.

    - Ta sytuacja zwykle występuje po kliknięciu linku w wiadomości e-mail z prośbą o wyświetlenie faktury w portalu. Kliknij link i zobaczysz komunikat o błędzie — `We can't display your invoices. Please try again`. Sprawdź, czy zalogowano się przy użyciu adresu e-mail, który ma uprawnienia do wyświetlania faktur.

- Masz dostęp do faktury za pomocą innej tożsamości. 

    - Niektórzy klienci mają dwie tożsamości o tym samym adresie e-mail — konto służbowe i konto Microsoft. Zazwyczaj tylko jedna z tożsamości ma uprawnienia do wyświetlania faktur. Jeśli użytkownik loguje się przy użyciu tożsamości, która nie ma uprawnień, nie zobaczy faktur. Sprawdź, czy używasz prawidłowej tożsamości do logowania.

- Zalogowano się do nieprawidłowej dzierżawy usługi Azure Active Directory (Azure AD). 

    - Twoje konto rozliczeniowe jest skojarzone z dzierżawą usługi Azure AD. Jeśli logujesz się do nieprawidłowej dzierżawy, nie zobaczysz faktury dla subskrypcji na koncie rozliczeniowym. Sprawdź, czy zalogowano się do poprawnej dzierżawy usługi Azure AD. Jeśli nie zalogowano się w poprawnej dzierżawie, użyj następujących opcji, aby przełączyć dzierżawę w witrynie Azure Portal:

        1. Wybierz swój adres e-mail w prawym górnym rogu strony.

        2. Wybierz pozycję **Przełącz katalog**.

           ![Zrzut ekranu przedstawiający wybieranie przełączenia katalogu w portalu](./media/download-azure-invoice/select-switch-directory.png)

        3. Wybierz katalog z sekcji **Wszystkie katalogi**.

           ![Zrzut ekranu przedstawiający wybieranie katalogu w portalu](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat, zobacz:

- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](review-individual-bill.md)
- [Omówienie terminów na fakturze za korzystanie z platformy Azure](understand-invoice.md)

Jeśli masz umowę MCA, zobacz:

- [Omówienie opłat na fakturze dla profilu rozliczeniowego](review-customer-agreement-bill.md)
- [Omówienie terminów na fakturze dla profilu rozliczeniowego](mca-understand-your-invoice.md)
- [Omówienie pliku użycia i opłat dotyczących platformy Azure dla profilu rozliczeniowego](mca-understand-your-usage.md)