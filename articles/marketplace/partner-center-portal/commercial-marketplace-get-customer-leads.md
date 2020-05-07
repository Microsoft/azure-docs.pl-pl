---
title: Zarządzanie potencjalnymi klientami z portalu komercyjnego firmy Microsoft
description: Dowiedz się więcej na temat generowania i otrzymywania potencjalnych klientów z Microsoft AppSource i ofert w portalu Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5d1bf859968c7b7a889abe635c917d0da11bf6e1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837332"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Potencjalni klienci z oferty komercyjnej witryny Marketplace

Potencjalni klienci są zainteresowani lub wdrażają oferty w witrynie [Microsoft AppSource](https://appsource.microsoft.com) i [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Potencjalni klienci mogą otrzymywać dostęp do klientów po opublikowaniu oferty na komercyjnym rynku. W tym artykule wyjaśniono następujące pojęcia związane z zarządzaniem potencjalnym liderem:

* Jak Twoja oferta komercyjnej witryny Marketplace generuje potencjalnych klientów, aby upewnić się, że nie trafią możliwości biznesowe. 
* Jak połączyć system zarządzania relacjami z klientami (CRM) z ofertą, aby umożliwić zarządzanie potencjalnymi klientami w jednej centralnej lokalizacji.
* Dane potencjalnych klientów, które wysyłamy, aby można było wykonać kolejne czynności w odniesieniu do klientów, którzy dotarły do Ciebie.

## <a name="generate-customer-leads"></a>Generowanie potencjalnych klientów

Poniżej znajdują się miejsca, w których generowany jest potencjalny klient:

- Klient wyraża zgodę na udostępnianie informacji po wybraniu opcji **kontakt ze mną** w portalu komercyjnym. Ten potencjalny klient jest potencjalnym liderem *zainteresowania* . Udostępnimy nam informacje o kliencie, który wyraził zainteresowanie uzyskaniem produktu. Potencjalny klient to Góra lejka pozyskiwania.

    ![Dynamics 365 kontakt ze mną](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Klient wybierze opcję **Pobierz teraz** (lub wybierze opcję **Utwórz** w [Azure Portal](https://portal.azure.com/)), aby uzyskać ofertę. Ten potencjalny klient jest *aktywnym* liderem. Udostępniamy Ci informacje o kliencie, który rozpoczął wdrażanie produktu.

    ![Przycisk Pobierz teraz](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Przycisk tworzenia systemu Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Klient wybiera **dysk testowy** lub **bezpłatną wersję próbną** , aby wypróbować ofertę. W przypadku dysków testowych lub bezpłatnych wersji próbnych można szybko udostępnić swoją firmę potencjalnym klientom bez żadnych przeszkód związanych z wprowadzaniem.

    ![Przycisk wersji testowej systemu Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Przycisk bezpłatnej wersji próbnej usługi Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Nawiązywanie połączenia z systemem CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informacje o potencjalnych klientach

Każdy potencjalny klient, który otrzymasz w procesie pozyskiwania klientów, ma dane w określonych polach. Pierwsze pole, dla którego ma zostać wyszukane, `LeadSource` to pole, które jest zgodne z następującym formatem:**Oferta** **akcji** | źródłowej.

**Źródła**: wartość tego pola jest wypełniana na podstawie rynku, który wygenerował potencjalnego klienta. Możliwe wartości to `"AzureMarketplace"`, `"AzurePortal"`, i `"AppSource (SPZA)"`.

**Akcje**: wartość dla tego pola jest wypełniana na podstawie akcji wykonywanej przez klienta w witrynie Marketplace, która wygenerowała potencjalnego klienta.

Możliwe wartości:

- **"Ins"**: oznacza *instalację*. Ta akcja znajduje się w witrynie Azure Marketplace lub AppSource, gdy klient uzyskuje produkt.
- **"PLT"**: oznacza *wersję próbną dla partnera*. Ta akcja jest w AppSource, gdy klient wybierze opcję **kontakt ze mną** .
- **"DNC"**: oznacza *nie kontakt*. Ta akcja jest w AppSource, gdy Partner, który został krzyżowo wymieniony na stronie Twojej aplikacji, otrzymuje prośbę o kontakt. Udostępnimy powiadomienie, że ten klient został wystawiony w aplikacji, ale nie musisz się z nimi skontaktować.
- **"Utwórz"**: Ta akcja jest dostępna tylko wewnątrz Azure Portal i jest generowana, gdy klient kupi ofertę na swoje konto.
- **"StartTestDrive"**: Ta akcja dotyczy tylko opcji **dysku testowego** i jest generowana, gdy klient uruchamia ich dysk testowy.

**Oferty**: w komercyjnej witrynie Marketplace może być wiele ofert. Wartość tego pola jest wypełniana na podstawie oferty, która wygenerowała potencjalny klient. IDENTYFIKATOR wydawcy i identyfikator oferty są wysyłane w tym polu i są wartościami podanymi podczas opublikowania oferty w portalu Marketplace.

W poniższych przykładach przedstawiono wartości w oczekiwanym `publisherid.offerid`formacie: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informacje o kliencie

Informacje o kliencie są wysyłane za pośrednictwem wielu pól. W poniższym przykładzie przedstawiono informacje o kliencie, które znajdują się w potencjalnym kliencie:

- Imię: Jan
- LastName: Kowalski
- Poczta e-mail:\@jsmith Microsoft.com
- Telefon: 1234567890
- Kraj: US
- Firma: Microsoft
- Tytuł: dyrektor ds

>[!NOTE]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta. Ponieważ będziesz otrzymywać potencjalni klienci z wielu kroków, jak wspomniano w sekcji "Generowanie potencjalnych klientów", najlepszym sposobem obsługi potencjalnych klientów jest cofnięcie duplikatów rekordów i spersonalizowanie kolejnych działań. W ten sposób każdy klient otrzymuje odpowiedni komunikat i utworzysz unikatową relację.

## <a name="best-practices-for-lead-management"></a>Najlepsze rozwiązania dotyczące zarządzania potencjalnymi klientami

- **Proces**: Zdefiniuj czysty proces sprzedaży z punktami kontrolnymi, analizą i jasno własnością zespołu.
- **Kwalifikacja**: Zdefiniuj wymagania wstępne, które wskazują, czy potencjalny klient został w pełni kwalifikowany. Upewnij się, że przedstawiciele ds. sprzedaży i marketingu kwalifikują się dokładnie do wzięcia pod uwagę przed przeprowadzeniem przez cały proces sprzedaży
- **Monitowanie**: nie zapomnij się monitować. Oczekiwano, że typowa transakcja wymaga od 5 do 12 wywołań kolejnych.
- **Informacyjna**: informacyjna potencjalni klienci w celu uzyskania lepszego marginesu zysków.

## <a name="common-questions-about-lead-management"></a>Często zadawane pytania dotyczące zarządzania potencjalnymi klientami

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?

Wykonaj kroki opisane w sekcji [łączenie z systemem CRM](#connect-to-your-crm-system)lub Prześlij bilet pomocy technicznej za pomocą [pomocy i obsługi technicznej Centrum partnerskiego](https://partner.microsoft.com/support/v2/?stage=1). Następnie wybierz pozycję **Utwórz** > ofertę**Typ** > oferty**Konfiguracja zarządzania liderem**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Czy muszę skonfigurować miejsce docelowe potencjalnego klienta w celu opublikowania oferty w portalu komercyjnym?

Odpowiedź zależy od typu publikowanej oferty. Oprogramowanie jako usługa (SaaS) i Dynamics 365 Customer Engagement używają do **kontaktowania** się ze wszystkimi usługami Dynamics 365 for Finanse i operacje, wszystkie oferty usługi Dynamics 365 Business Central i wszystkie oferty usług konsultingowych. W związku z tym wymagają połączenia z miejscem docelowym potencjalnego klienta. Jeśli typu oferty nie ma na liście, połączenie z miejscem docelowym potencjalnego klienta nie jest wymagane. Zalecamy skonfigurowanie miejsca docelowego potencjalnego klienta, aby nie przegapić możliwości biznesowych.

### <a name="how-can-i-find-the-test-lead"></a>Jak mogę znaleźć potencjalnego klienta testowego?

Wyszukaj `"MSFT_TEST"` w miejscu docelowym potencjalnego klienta. Oto przykładowy test, który prowadzi od firmy Microsoft:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Mam ofertę na żywo, ale dlaczego nie widzę żadnych potencjalnych klientów?

Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest prawidłowe. Wyślemy Ci lidera testowego po wybraniu opcji **Publikuj** w ofercie w centrum partnerskim. Jeśli widzisz potencjalnego klienta testowego, połączenie jest prawidłowe. Możesz również przetestować połączenie z liderem, próbując uzyskać podgląd oferty w ramach kroku wersji zapoznawczej. Wybierz pozycję **Pobierz teraz**, **skontaktuj się z**nami lub **bezpłatną wersję próbną** na liście w portalu komercyjnym.

Upewnij się również, że szukasz odpowiednich danych. Zawartość w sekcji Informacje o [potencjalnych klientach](#understand-lead-data) w tym artykule opisuje dane potencjalnych klientów wysyłanych do miejsca docelowego potencjalnego klienta.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Mam skonfigurowany magazyn obiektów blob platformy Azure jako miejsce docelowe potencjalnego klienta, ale dlaczego nie widzę potencjalnego klienta?

Usługa Azure Blob Storage nie jest już obsługiwana jako miejsce docelowe potencjalnego klienta, dlatego nie masz żadnych potencjalnych klientów wygenerowanych przez ofertę. Przełącz się do dowolnego z opcji innych elementów [docelowych potencjalnego klienta](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Z komercyjnej witryny Marketplace otrzymałem wiadomość e-mail, ale dlaczego nie mogę znaleźć potencjalnego klienta w moim programie CRM?

Istnieje możliwość, że domena poczty e-mail użytkownika końcowego pochodzi z. edu. Ze względu na prywatność nie przekazujemy informacji osobistych z domeny. edu. Prześlij bilet pomocy technicznej, korzystając z [pomocy i pomocy technicznej Centrum partnerskiego](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Jak mogę wyświetlić potencjalnych klientów?

Możesz uzyskać dostęp do danych potencjalnych klientów przechowywanych w tabeli platformy Azure z Azure Portal. Możesz również pobrać i zainstalować [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić dane tabeli dla konta usługi Azure Storage.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Czy mogę otrzymywać powiadomienia o każdym wysłaniu nowego komercyjnego lidera rynku?

Tak. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie zarządzania potencjalnymi klientami przy użyciu tabeli platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) w celu skonfigurowania usługi Microsoft Flow, która wysyła wiadomość e-mail, jeśli potencjalny klient zostanie dodany do tabeli platformy Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Mam skonfigurowany program Salesforce jako lokalizację docelową mojego potencjalnego klienta, ale dlaczego nie mogę znaleźć potencjalnych klientów?

Sprawdź, czy formularz sieci Web do potencjalnego klienta jest polem obowiązkowym opartym na liście wyboru. Jeśli tak jest, przełącz pole do nieobowiązkowego pola tekstowego.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Wystąpił problem z miejscem docelowym potencjalnego klienta i pominięto kilka potencjalnych klientów. Czy mogę je wysłać do mnie w wiadomości e-mail?

Ze względu na zasady dotyczące informacji osobistych nie można udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonej poczty e-mail.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Ile będzie kosztować?

Dane generowania potencjalnego klienta są niskie. Jest ona mniejsza niż 1 GB dla niemal wszystkich wydawców. Koszt zależy od liczby odebranych potencjalnych klientów. Jeśli na przykład w miesiącu otrzymasz 1 000 potencjalnych klientów, koszt wynosi około 50 centów. Aby uzyskać więcej informacji o cenach magazynu, zobacz [Cennik usługi Azure Storage — Omówienie](https://azure.microsoft.com/pricing/details/storage/).

Jeśli Twoje pytanie nie zostało odebrane, skontaktuj się z firmą pomoc techniczna firmy Microsoft za pomocą [pomocy i pomocy technicznej Centrum partnerskiego](https://aka.ms/marketplacepublishersupport). Następnie wybierz pozycję **Utwórz** > ofertę**Typ** > oferty**Konfiguracja zarządzania liderem**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Otrzymuję powiadomienia e-mail po odebraniu nowych potencjalnych klientów. Jak skonfigurować kogoś innego do otrzymywania tych wiadomości e-mail?

Uzyskaj dostęp do oferty w centrum partnerskim i przejdź do **strony Konfiguracja oferty** > > **Edit** **Zarządzanie potencjalnymi klientami**. Zaktualizuj adresy e-mail w polu **kontaktowy adres e-mail** .

## <a name="next-steps"></a>Następne kroki

Po zakończeniu instalacji technicznej należy uwzględnić te potencjalni klienci w bieżącej strategii sprzedaży i marketingu oraz procesach operacyjnych. Chcielibyśmy lepiej zrozumieć swój ogólny proces sprzedaży i chcemy ściśle współpracować z nim w celu zapewnienia wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Daj nam znać, Jeśli interesuje Cię [przekazywanie opinii](mailto:AzureMarketOnboard@microsoft.com) i sugestii, aby umożliwić zespołowi sprzedaży lepsze pomyślną pracę z komercyjnymi klientami portalu Marketplace.
