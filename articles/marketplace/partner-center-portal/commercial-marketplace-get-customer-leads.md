---
title: Konfigurowanie potencjalnych klientów | Azure Marketplace
description: Konfigurowanie potencjalnych klientów w komercyjnym rynku.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731135"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Potencjalni klienci z oferty w witrynie Marketplace

Potencjalni klienci to klienci zainteresowani lub wdrażający oferty z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) lub [AppSource.](https://appsource.microsoft.com) Otrzymasz potencjalnych klientów po opublikowaniu oferty na rynku. Ten artykuł wyjaśni:

* Sposób, w jaki twoja oferta rynkowa generuje klientów, zapewniając, że nie przegapisz możliwości biznesowych. 
* Połącz crm do oferty, dzięki czemu można zarządzać potencjalnymi klientami w jednej centralnej lokalizacji.
* Poznaj dane potencjalnych klientów, które wysyłamy, abyś mógł śledzić klientów, którzy się z Tobą skontaktowali.

## <a name="generate-customer-leads"></a>Generowanie potencjalnych klientów

Oto miejsca, w których generowany jest potencjalny klient:

1. Gdy klient wyraża zgodę na udostępnianie swoich informacji po wybraniu opcji "Skontaktuj się ze mną" z rynku. Ten potencjalny klient jest początkowym potencjalnym **klientem,** w którym udostępniamy informacje o kliencie, który wyraził zainteresowanie uzyskaniem produktu. Ołów jest górną częścią lejka akwizycyjnego.

      ![Dynamics 365 Skontaktuj się ze mną](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Gdy klient wybierze opcję "Pobierz teraz" lub "Utwórz" (w [witrynie Azure portal),](https://portal.azure.com/)aby uzyskać ofertę, ten potencjalny klient jest **aktywnym potencjalnym klientem,** w którym udostępniamy informacje o kliencie, który rozpoczął wdrażanie produktu.

    ![SQL Pobierz go teraz](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Tworzenie systemu Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Klient bierze "Test Drive" lub rozpoczyna "Bezpłatną wersję próbną" twojej oferty. Jazdy testowe lub bezpłatne wersje próbne to przyspieszone możliwości natychmiastowego dzielenia się swoją firmą z potencjalnymi klientami bez żadnych barier wejścia.

    ![Napęd testowy Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Napęd testowy Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Połącz się z systemem CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Zrozumienie danych potencjalnego klienta

Każdy potencjalny klient otrzymujesz podczas procesu pozyskiwania klientów ma dane w określonych polach. Pierwsze pole, na które `LeadSource` należy zwrócić uwagę, to pole, które jest zgodne z tym **formatem: Oferta działania źródłowego** | **.**

**Źródła:** Wartość tego pola jest wypełniana na podstawie rynku, który wygenerował potencjalnego klienta. Możliwe wartości `"AzureMarketplace"` `"AzurePortal"`to `"AppSource (SPZA)"`, i .

**Akcje:** Wartość tego pola jest wypełniana na podstawie akcji podjętej przez klienta na rynku, która wygenerowała potencjalnego klienta. 

Możliwe wartości:

- "INS" -- Instalacja. Ta akcja jest w witrynie Azure Marketplace lub AppSource, gdy klient kupuje produkt.
- "PLT" - Oznacza test prowadzony przez partnera. Ta akcja jest dostępna w udziale appsource, gdy klient używa opcji Skontaktuj się ze mną.
- "DNC" -- Nie kontaktuj się. Ta akcja jest na AppSource, gdy partner, który został krzyżyk na stronie aplikacji zostanie poproszony o kontakt. Udostępniamy informacje, że ten klient został wymieniony w twojej aplikacji, ale nie trzeba się z nimi kontaktować.
- "Utwórz" — ta akcja jest dostępna tylko w witrynie Azure portal i jest generowana, gdy klient kupuje ofertę na swoim koncie.
- "StartTestDrive" — ta akcja jest dostępna tylko dla dysków testowych i jest generowana, gdy klient rozpoczyna jazdę próbną.

**Oferty**: Możesz mieć wiele ofert na rynku. Wartość tego pola jest wypełniana na podstawie oferty, która wygenerowała potencjalnego klienta. Identyfikator wydawcy i identyfikator oferty są wysyłane w tym polu i są wartościami podanymi podczas publikowania oferty w portalu marketplace.

Poniższe przykłady pokazują przykładowe `publisherid.offerid`wartości w oczekiwanym formacie: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informacje o kliencie

Informacje o kliencie są wysyłane za pośrednictwem wielu pól. W poniższym przykładzie przedstawiono informacje o kliencie, które są zawarte w potencjalnym kliencie.

- Imię: John
- Nazwisko: Kowalski
- E-mail:\@jsmith microsoft.com
- Numer telefonu: 1234567890
- Państwo: USA
- Firma: Microsoft
- Tytuł: CTO

>[!Note]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta. Ponieważ otrzymasz potencjalnych klientów z wielu kroków, jak wspomniano w sekcji Potencjalni klienci, najlepszym sposobem obsługi potencjalnych klientów jest usunięcie duplikatu rekordów i spersonalizowanie działań następczych. W ten sposób każdy klient otrzymuje odpowiedni komunikat i tworzysz unikalną relację.

## <a name="best-practices-for-lead-management"></a>Najważniejsze wskazówki dotyczące zarządzania potencjalnymi klientami

1. *Proces* — definiowanie przejrzystego procesu sprzedaży z punktami kontrolnymi, kluczowymi wskaźnikami wydajności i przejrzystą własnością zespołu.
2. *Kwalifikacja* — zdefiniuj wymagania wstępne, które wskazują, czy potencjalny klient został w pełni zakwalifikowany. Upewnij się, że przedstawiciele sprzedaży lub marketingu kwalifikują potencjalnych klientów starannie przed podjęciem ich przez cały proces sprzedaży.
3. *Kontynuacja* - Nie zapomnij o kontynuacji, spodziewaj się, że typowa transakcja wymaga od 5 do 12 połączeń uzupełniających
4. *Pielęgnować* - Pielęgnować swoje przewody, w celu uzyskania cię na drodze do wyższej marży zysku.

## <a name="leads-frequently-asked-questions"></a>Często zadawane pytania dotyczące potencjalnych klientów

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?

Możesz znaleźć dokumentację [tutaj](#connect-to-your-crm-system) lub https://partner.microsoft.com/support/v2/?stage=1 przesłać bilet pomocy technicznej, a następnie wybrać **"tworzenie oferty"** → **rodzaj oferty** → **"konfiguracja zarządzania potencjalnymi klientami".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Czy muszę skonfigurować miejsce docelowe potencjalnego klienta, aby opublikować ofertę na rynku?

Odpowiedź zależy od typu oferty, którą publikujesz. Oferty SaaS i Dynamics 365 for Customer Engagement są wymieniane jako "Skontaktuj się ze mną", wszystkie oferty Dynamics 365 for Operations, wszystkie oferty Dynamics 365 Business Central i wszystkie oferty usługi konsultingowej wymagają połączenia z głównym miejscem docelowym. Jeśli typ oferty nie został wymieniony, nie jest wymagany. Zaleca się jednak skonfigurowanie miejsca docelowego potencjalnego klienta, aby nie przegapić możliwości biznesowych.

### <a name="how-can-i-find-the-test-lead"></a>Jak znaleźć potencjalnego klienta testowego?

Wyszukaj `"MSFT_TEST"` w miejscu docelowym potencjalnego klienta, oto przykładowy potencjalny klient firmy Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Mam ofertę na żywo, ale nie widzę żadnych potencjalnych klientów?

Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest prawidłowe. Wyślemy Ci przewagę testową po naciśnięciu opublikowania oferty w Centrum partnerskim. Jeśli widzisz potencjalnego klienta testowego, połączenie jest prawidłowe. Możesz również przetestować połączenie potencjalnego klienta, próbując uzyskać podgląd oferty podczas kroku podglądu, klikając "pobierz go teraz", "skontaktuj się ze mną" lub "bezpłatna wersja próbna" na aukcji w portalu marketplace.

Upewnij się również, że szukasz odpowiednich danych. Zawartość w sekcji [Zrozumienie danych potencjalnego klienta](#understand-lead-data) w tym dokumencie opisuje dane potencjalnego klienta, które wysyłamy do miejsca docelowego potencjalnego klienta.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Skonfigurowano usługę Azure BLOB jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?

Miejsce docelowe potencjalnego klienta obiektów Blob platformy Azure nie jest już obsługiwane, więc brakuje potencjalnych klientów generowanych przez ofertę. Przełącz się do dowolnej z innych [opcji docelowych potencjalnych klienta](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Otrzymałem wiadomość e-mail z Marketplace, dlaczego nie mogę znaleźć potencjalnego klienta w moim programie CRM?

Jest możliwe, że domena poczty e-mail użytkownika końcowego pochodzi z domeny .edu. Ze względu na prywatność nie przekazujemy prywatnych informacji umożliwiających identyfikację z domeny .edu. Prześlij https://partner.microsoft.com/support/v2/?stage=1zgłoszenie pomocy technicznej za pośrednictwem pliku .

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Skonfigurowano tabelę platformy Azure jako miejsce docelowe potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?

Możesz uzyskać dostęp do danych potencjalnych potencjalnych klienta przechowywanych w tabeli platformy Azure z witryny Azure portal lub pobrać i zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) za darmo, aby wyświetlić dane tabel konta magazynu platformy Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Skonfigurowano tabelę platformy Azure jako miejsce docelowe potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient jest wysyłany przez marketplace?

Tak, postępuj zgodnie z instrukcjami, aby skonfigurować przepływ firmy Microsoft, który wysyła wiadomość e-mail, jeśli potencjalny klient zostanie dodany do tabeli platformy Azure w dokumentacji [w tym miejscu](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Skonfigurowałem Salesforce jako miejsce docelowe potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?

Sprawdź, czy formularz "web to lead" jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz pole na nieokonolikowe pole tekstowe.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Nie było problemu z moim głównym przeznaczenia, a ja brakowało niektórych prowadzi. Czy mogę je wysłać do mnie w wiadomości e-mail?

Ze względu na prywatne zasady dotyczące informacji umożliwiających identyfikację nie możemy udostępniać informacji kontaktowych za pośrednictwem niezabezpieczonych wiadomości e-mail.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Skonfigurowano tabelę platformy Azure jako miejsce docelowe potencjalnego klienta, ile będzie kosztować?

Dane o generacji potencjalnych klienta są niskie (<1 GB dla prawie wszystkich wydawców). Koszt będzie zależeć od liczby otrzymanych potencjalnych klientów, jeśli 1000 potencjalnych klientów zostanie odebranych w ciągu miesiąca, kosztuje około 50 centów. Aby uzyskać więcej informacji na temat cen magazynowania, zobacz [ceny magazynowania](https://azure.microsoft.com/pricing/details/storage/).

Jeśli twoje pytanie nadal nie jest na nieodebrane, skontaktuj się z pomocą techniczną za pośrednictwem aka.ms/marketplacepublishersupport, a następnie wybierz **opcję "tworzenie oferty"** → **typ oferty** → **"konfiguracja zarządzania potencjalnymi klientami".** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Otrzymuję powiadomienia e-mail po otrzymaniu nowych potencjalnych klientów. Jak skonfigurować, kto ma otrzymywać te wiadomości e-mail?

Uzyskaj dostęp do oferty w Centrum partnerów i przejdź do strony **Ustawienia oferty** >**edycji** **zarządzania potencjalnymi klientami** -> . Zaktualizuj adresy e-mail w polu **Kontaktowy adres e-mail.**

## <a name="next-steps"></a>Następne kroki

Po zakończeniu konfiguracji technicznej należy włączyć te przewody do bieżącej strategii marketingowej & sprzedaży i procesów operacyjnych. Jesteśmy zainteresowani lepszym zrozumieniem ogólnego procesu sprzedaży i chcemy ściśle współpracować z Tobą w zakresie dostarczania wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby odnieść sukces. Cieszymy się z Twojej opinii na temat tego, jak możemy zoptymalizować i ulepszyć potencjalnych klientów, które wysyłamy Ci z dodatkowymi danymi, które pomogą ci pomóc tym klientom odnieść sukces. Daj nam znać, jeśli chcesz [przekazać opinie](mailto:AzureMarketOnboard@microsoft.com) i sugestie, aby twój zespół sprzedaży mógł odnieść większy sukces z potencjalnymi klientami marketplace.
