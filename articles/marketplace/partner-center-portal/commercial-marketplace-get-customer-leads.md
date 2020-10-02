---
title: Zarządzanie potencjalnymi klientami z portalu komercyjnego firmy Microsoft
description: Dowiedz się więcej na temat generowania i otrzymywania potencjalnych klientów z Microsoft AppSource i ofert w portalu Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 5627d6afab22defebeebd3f04695ef95390f1163
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653319"
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

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informacje o potencjalnych klientach

Każdy potencjalny klient, który otrzymasz w procesie pozyskiwania klientów, ma dane w określonych polach. Pierwsze pole, dla którego ma zostać wyszukane `LeadSource` , to pole, które jest zgodne z następującym formatem: Oferta **akcji źródłowej**  |  **Offer**.

**Źródła**: wartość tego pola jest wypełniana na podstawie rynku, który wygenerował potencjalnego klienta. Możliwe wartości to `"AzureMarketplace"` , `"AzurePortal"` , i `"AppSource (SPZA)"` .

**Akcje**: wartość dla tego pola jest wypełniana na podstawie akcji wykonywanej przez klienta w witrynie Marketplace, która wygenerowała potencjalnego klienta.

Możliwe wartości:

- **"Ins"**: oznacza *instalację*. Ta akcja znajduje się w witrynie Azure Marketplace lub AppSource, gdy klient uzyskuje produkt.
- **"PLT"**: oznacza *wersję próbną dla partnera*. Ta akcja jest w AppSource, gdy klient wybierze opcję **kontakt ze mną** .
- **"DNC"**: oznacza *nie kontakt*. Ta akcja jest w AppSource, gdy Partner, który został krzyżowo wymieniony na stronie Twojej aplikacji, otrzymuje prośbę o kontakt. Udostępnimy powiadomienie, że ten klient został wystawiony w aplikacji, ale nie musisz się z nimi skontaktować.
- **"Utwórz"**: Ta akcja jest dostępna tylko wewnątrz Azure Portal i jest generowana, gdy klient kupi ofertę na swoje konto.
- **"StartTestDrive"**: Ta akcja dotyczy tylko opcji **dysku testowego** i jest generowana, gdy klient uruchamia ich dysk testowy.

**Oferty**: w komercyjnej witrynie Marketplace może być wiele ofert. Wartość tego pola jest wypełniana na podstawie oferty, która wygenerowała potencjalny klient. IDENTYFIKATOR wydawcy i identyfikator oferty są wysyłane w tym polu i są wartościami podanymi podczas opublikowania oferty w portalu Marketplace.

W poniższych przykładach przedstawiono wartości w oczekiwanym formacie `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informacje o kliencie

Informacje o kliencie są wysyłane za pośrednictwem wielu pól. W poniższym przykładzie przedstawiono informacje o kliencie, które znajdują się w potencjalnym kliencie:

- Imię: Jan
- LastName: Kowalski
- Poczta e-mail: jsmith \@ Microsoft.com
- Telefon: 1234567890
- Kraj: US
- Firma: Microsoft
- Tytuł: dyrektor ds

>[!NOTE]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta. Ponieważ będziesz otrzymywać potencjalni klienci z wielu kroków, jak wspomniano w sekcji "Generowanie potencjalnych klientów", najlepszym sposobem obsługi potencjalnych klientów jest cofnięcie duplikatów rekordów i spersonalizowanie kolejnych działań. W ten sposób każdy klient otrzymuje odpowiedni komunikat i utworzysz unikatową relację.

## <a name="best-practices-for-lead-management"></a>Najlepsze rozwiązania dotyczące zarządzania potencjalnymi klientami

Poniżej przedstawiono kilka zaleceń dotyczących prowadzenia potencjalnych klientów w ramach cyklu sprzedaży:

- **Proces**: Zdefiniuj czysty proces sprzedaży z punktami kontrolnymi, analizą i jasno własnością zespołu.
- **Kwalifikacja**: Zdefiniuj wymagania wstępne, które wskazują, czy potencjalny klient został w pełni kwalifikowany. Upewnij się, że przedstawiciele ds. sprzedaży i marketingu kwalifikują się dokładnie do wzięcia pod uwagę przed przeprowadzeniem przez cały proces sprzedaży
- **Zaobserwuj**: nie zapomnij wykonać czynności w ciągu 24 godzin. Potencjalny klient zostanie wyświetlony w wybranym programie CRM natychmiast po wdrożeniu przez klienta dysku testowego; Wyślij je pocztą e-mail w czasie, gdy są one nadal dostępne. Żądaj zaplanowania rozmowy telefonicznej, aby lepiej zrozumieć, czy produkt jest dobrym rozwiązaniem dla swojego problemu. Należy oczekiwać, że typowa transakcja wymaga wielu kolejnych wywołań.
- **Informacyjna**: informacyjna potencjalni klienci w celu uzyskania lepszego marginesu zysków. Zaewidencjonuj, ale nie Bombard. Zalecamy, aby wysłać wiadomość e-mail co najmniej kilka razy przed ich zamknięciem. Nie dodawaj po pierwszej próbie. Pamiętaj, że Ci klienci bezpośrednio zaangażowani w produkt i spędzają czas w bezpłatnej wersji próbnej. są to wspaniałe potencjalni klienci.

Po zakończeniu instalacji technicznej należy uwzględnić te potencjalni klienci w bieżącej strategii sprzedaży i marketingu oraz procesach operacyjnych. Chcielibyśmy lepiej zrozumieć swój ogólny proces sprzedaży i chcemy ściśle współpracować z nim w celu zapewnienia wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Daj nam znać, Jeśli interesuje Cię [przekazywanie opinii](mailto:AzureMarketOnboard@microsoft.com) i sugestii, aby umożliwić zespołowi sprzedaży lepsze pomyślną pracę z komercyjnymi klientami portalu Marketplace.

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania dotyczące zarządzania liderami i rozwiązywanie problemów](../lead-management-faq.md)