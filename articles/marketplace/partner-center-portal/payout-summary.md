---
title: Podsumowanie wypłat na rynku komercyjnym | Azure Marketplace
description: Podsumowanie wypłat zawiera szczegółowe informacje o pieniądzach zarobionych w swojej ofercie. Informuje również, kiedy otrzymasz płatności i ile zapłacisz.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730202"
---
# <a name="payout-reporting"></a>Raportowanie wypłaty

[**Podsumowanie wypłat**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) zawiera szczegółowe informacje o pieniądzach zarobionych w firmie Microsoft. Informuje również, kiedy otrzymasz płatności i ile zapłacisz.

Jeśli sprzedajesz oferty w portalu Azure Marketplace, w **podsumowaniu wypłat**zobaczysz również informacje o udanych wypłatach. Aby uzyskać więcej informacji dotyczących płatności w portalu Azure Marketplace, zobacz [zasady uczestnictwa w portalu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) i umowę [wydawcy portalu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby kwalifikować się do wypłaty, twoje wpływy muszą osiągnąć [próg płatności](payment-thresholds-methods-timeframes.md) w wysokości 50 USD. Aby uzyskać szczegółowe informacje o progu płatności, zobacz tę stronę i zapoznaj się z [umową wydawcy portalu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Role i uprawnienia dostępu do raportu wypłat](#roles-and-permission-to-access-the-payout-report)
- [Raport wypłat: różnica między portalem cloud partnerów a centrum partnerów](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy klientów](#customer-types)
- [Corelation między wypłatą a użyciem](#corelation-between-payout-and-usage)
- [Pobieranie historii transakcji](#transaction-history-download-export)
- [Pytania dotyczące rozliczeń i pomoc techniczna](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Role i uprawnienia dostępu do raportu wypłat

| Raporty/strony    | Właściciel konta    | Menedżer  | Developer | Współpracownik biznesowy |  Podmiot przekazu finansowego | Marketer |
|------------------|------------------|----------|-----------|----|----|-----|
| Raport akwizycji (w tym dane w czasie zbliżonym do rzeczywistego) | Można wyświetlić | Można wyświetlić | Brak dostępu | Brak dostępu | Można wyświetlić | Brak dostępu |
| Raport opinii/odpowiedzi | Może wyświetlać i wysyłać opinie | Może wyświetlać i wysyłać opinie | Może wyświetlać i wysyłać opinie | Brak dostępu | Brak dostępu | Może wyświetlać i wysyłać opinie |
| Raport o kondycji (w tym dane w czasie zbliżonym do rzeczywistego) | Można wyświetlić | Można wyświetlić | Można wyświetlić | Można wyświetlić | Brak dostępu | Brak dostępu |
| Raport użycia | Można wyświetlić | Można wyświetlić | Można wyświetlić | Można wyświetlić | Brak dostępu | Brak dostępu |
| Konto wypłat | Można aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Można aktualizować | Brak dostępu |
| Profil podatkowy | Można aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Można aktualizować | Brak dostępu |
| Podsumowanie wypłaty | Można wyświetlić | Brak dostępu | Brak dostępu | Brak dostępu | Można wyświetlić | Brak dostępu |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Raport wypłat: różnica między portalem cloud partnerów a centrum partnerów

| | Portal Cloud Partner | Centrum partnerskie |
|---------|---------|---------|
| Linki | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)I[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Nawigacja | Raportowanie wypłat dostępne w sekcji Insights Payout | Raportowanie wypłat dostępne w Centrum partnerów – ikona wypłat |
| Zakres | <ul> <li>Transakcja na element zamówienia jest widoczna, dla kolekcji w toku, zebranych i zapłaconych </li> <li>Raportowanie — pokazuje wszystkie elementy zamówienia po utworzeniu zamówienia zakupu, w tym kolekcję w toku i rozliczenia w toku oraz stan kolekcji i elementy zamówienia, które nie kwalifikują się jeszcze do zapłaty. </li> </ul> | <ul> <li>Pokazuje elementy zamówienia, gdy zostaną uznane za kwalifikujące się zarobki.</li> <li>Klienci najpierw płacą firmie Microsoft, a następnie dostawcy oprogramowania mogą zobaczyć rozpoczynający się raport wypłat.</li> <li>Raport wypłat nie będzie pokazywał kolekcji w toku i rozliczeń w toku.  </li> </ul>  |
| Transakcja nie jest gotowa do wypłaty | Rozliczenia w toku | Następna szacowana płatność: Stan wypłaty jest w stanie nieprzetworzenia.  |
| Status wypłaty |  | Nieprzetworzonych: <br> Zarabianie kwalifikuje się do płatności. Pozostaje w tym stanie przez okres chłodzenia, zgodnie z definicją w przewodniku po programie Incentive. <br> <br> Nadchodzących: <br> Wygenerowane zlecenie płatności oczekujące na wewnętrzne przeglądy przed przetworzeniem płatności. <br> <br> Wysłane: <br> Płatność została wysłana do Twojego banku. |

## <a name="customer-types"></a>Typy klientów

### <a name="enterprise-agreement"></a>Umowa enterprise

Klienci bez umowy Enterprise Agreement są co miesiąc rozliczani za licencje na oprogramowanie w marketplace. Klienci z umową Enterprise Agreement są rozliczani co miesiąc za pomocą faktury prezentowanej kwartalnie.

### <a name="credit-cards-and-monthly-invoice"></a>Karty kredytowe i faktura miesięczna

Klienci mogą również płacić kartą kredytową i fakturą miesięczną. W takim przypadku opłaty licencyjne za oprogramowanie będą naliczane co miesiąc.

### <a name="csp-and-direct-pay-users"></a>CSP i bezpośredni płatni użytkownicy

Na przykład, jeśli klient kupuje przy użyciu karty kredytowej.

## <a name="corelation-between-payout-and-usage"></a>Corelation między wypłatą a użyciem

|Opis    |    Date  | Zamówienia/użycie  | Wypłata |
|----------|----------|-----------|-------------|
|Okres zamówienia   | 15 sierpnia 2019 - 30 sierpnia 2019 | **Zlecenia atrybutów korelacji** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId</li> </ul> <br> **Użycia** <br> <ul> <li>CustomerId </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (PC) </li> </ul> |  |
|Zakończenie okresu (miesiąc)   | 30 sierpnia 2019 r. | | |
|Data rozliczenia | 1 września 2019 r. | | |
|Data płatności odbiorcy | 1 września 2019 r. | | |
|Okres escrow (tylko karty kredytowe, 30 dni) | 1 września 2019 - 30 września 2019 | | **Polecenia atrybutów korelacji:** <br> <ul><li>AssetId (ida aktywów)</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycia** <br> <ul> <li>AssetId (ida aktywów)</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>Ijka linii</li> <li>transakcjaMount</li> <li>ZarabianieAmountInLastPaymentCurrency</li> </ul> <br> **Status wypłaty:** Nieprzetworzonych |
|Początek okresu zbiórki | 1 września 2019 r. | | |
|Koniec okresu zbierania (maksymalnie 30 dni) | 30 września 2019 r. | | |
|Data obliczania wypłaty (co miesiąc 15) | 1 października 2019 r. | | **Atrybuty korelacji** <br> <ul><li>AssetId (ida aktywów)</li> <li>Identyfikator klienta</li> <li>Nazwa klienta</li> </ul> <br> **Użycia** <br> <ul> <li>AssetId (ida aktywów)</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>Ijka linii</li> <li>transakcjaMount</li> <li>ZarabianieAmountInLastPaymentCurrency</li> </ul> <br> **Status wypłaty:** Nadchodzących |
|Data wypłaty | 15 października 2019 r. | | **Atrybuty korelacji** <br> <ul><li>AssetId (ida aktywów)</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycia** <br> <ul> <li>AssetId (ida aktywów)</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>Ijka linii</li> <li>transakcjaMount</li> <li>ZarabianieAmountInLastPaymentCurrency</li> </ul> <br> **Status wypłaty:** Płatność wysłana |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Umowa enterprise (klienci kwartalni/miesięczni)

| Opis |    Date  | Sposób użycia | Wypłata |
|----------|----------|---------|-----------|
|Okres zamówienia | 15 sierpnia 2019 - 30 sierpnia 2019 | **Korelacja przypisuje zamówienia** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId</li> </ul> <br> **Raport użycia** <br> <ul> <li>CustomerId </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (PC) </li> </ul> | |
|Zakończenie kadencji (kwartał) | 30 września 2019 r. | | |
|Data rozliczenia | 15 października 2019 r. | | |
|Okres escrow (tylko karty kredytowe, 30 dni) | Nie dotyczy | | |
|Początek okresu zbiórki | 15 października 2019 r. | | |
|Tylko karty kredytowe, 30 dni | 1 listopada 2019 - 30 listopada 2019 | | |
|Koniec okresu zbierania (maksymalnie 90 dni) | 15 stycznia 2020 r. | | |
|Data płatności odbiorcy | 30 grudnia 2019 r. | | |
|Obliczanie wypłat | 15 stycznia 2020 r. | | |
|Data wypłaty | 15 lutego 2020 r. | | **Dla klientów kwartalnych** <br> <br> **Raport zamówień** <br> <ul><li>AssetId (ida aktywów)</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycia** <br> <ul> <li>AssetId (ida aktywów)</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>Ijka linii</li> <li>transakcjaMount</li> <li>ZarabianieAmountInLastPaymentCurrency</li> </ul> <br> **Status wypłaty:** wysłane |

## <a name="transaction-history-download-export"></a>Eksport pobierania historii transakcji

Ta opcja umożliwia pobranie każdego zyskującego elementu zamówienia widocznego na stronie Historia transakcji, typ zarobkowania, data, powiązana kwota transakcji, klient, produkt i inne szczegóły transakcyjne mające zastosowanie do programu Zachęty.

| Nazwa kolumny     | Opis    |
|-------------|-------------------------------|
| zarabianieId                      | Unikatowy identyfikator dla każdego zarabiającego                                                                                                       |
| participantId (ida)                  | Podstawowa tożsamość partnera zarabiającego w ramach programu                                                                            |
| typ participantIdType              | Głównie identyfikator programu dla programów motywacyjnych i programów SELLER IF dla sklepu i portalu Azure Marketplace                                          |
| nazwa uczestnika                | Imię i nazwisko partnera zarabiającego                                                                                                              |
| kod partnerski             | Lokalizacja/kraj partnera zarobkowego                                                                                                  |
| nazwa programu                    | Nazwa programu Zachęty/Sklepu                                                                                                             |
| identyfikator transakcji                  | Unikatowy identyfikator transakcji                                                                                                    |
| transakcjaCurrency            | Waluta, w której wystąpiła pierwotna transakcja odbiorcy (nie jest to waluta lokalizacji partnera)                                     |
| Transactiondate                | Data transakcji. Przydatne w przypadku programów, w których wiele transakcji przyczynia się do jednego zarabiania                                           |
| TransactionExchangeRate (Szybkość wymiany transakcji)        | Kurs wymiany używany do pokazania odpowiedniej kwoty transakcji w USD                                                                 |
| transakcjaMount              | Kwota transakcji w oryginalnej walucie transakcji, na podstawie której generowane jest zarabianie                                              |
| transakcjaAmountUSD           | Kwota transakcji w USD                                                                                                                |
| Dźwignia                          | Wskazuje regułę biznesową dla zarabiania                                                                                                  |
| zarabianieWłasna                    | Stawka zachęty stosowana w odniesieniu do kwoty transakcji w celu wygenerowania                                                                      |
| quantity                       | Różni się w zależności od programu. Wskazuje naliczioną ilość dla programów transakcyjnych                                                            |
| ilośćType                   | Wskazuje typ ilości, na przykład: Ilość naliczona, MAU                                                                                     |
| zarabianieTyp                    | Wskazuje, czy jest to opłata, rabat, coop, sprzedać itp.                                                                                          |
| zarabianieMount                  | Kwota zarobkowania w oryginalnej walucie transakcji                                                                                      |
| zarabianieAmountUSD               | Wysokość zarabiania w USD                                                                                                                    |
| zarabianieDate                    | Data uzyskania przychodu                                                                                                                      |
| obliczeńDaj.                | Data obliczenia zarobków w systemie                                                                                            |
| zarabianieWyświetlać            | Kurs wymiany używany do pokazania odpowiedniej kwoty w USD                                                                                  |
| exchangeRateDate               | Data kursu wymiany używana do obliczania zarabianiaAla USD                                                                                   |
| płatnośćMountWOTax             | Kwota zarobkowania (bez podatku) w walucie płatności Płatności Do tylko dla płatności "Wysłane"                                                                 |
| płatnośćCurrency                | Płać w walucie wybranej przez partnera w profilu płatności. Wyświetlane tylko dla wysłanych płatności                                                   |
| płatnośćWyświetlenie            | Kurs wymiany używany do obliczania płatnościWypłata w walucie płatności przy użyciu metody ExchangeRateDate                                            |
| identyfikator płatności            | Unikatowy identyfikator płatności. Ten numer jest widoczny na wyciągu bankowym                                            |
| status płatności            | Stan płatności                                            |
| paymentStatusDescription (Opis statusu płatności)            | Przyjazny opis statusu płatności                                            |
| customerId                     | Zawsze będzie pusty                                                                                                                     |
| Customername                   | Zawsze będzie pusty                                                                                                                     |
| partNumber                     | Zawsze będzie pusty                                                                                                                     |
| Productname                    | Nazwa produktu powiązana z transakcją                                                                                                       |
| productId                      | Unikatowy identyfikator produktu                                                                                                                |
| element nadrzędnyProductId                | Unikatowy identyfikator produktu nadrzędnego. Uwaga: jeśli nie ma produktu nadrzędnego dla transakcji, a następnie identyfikator produktu nadrzędnego = Identyfikator produktu. |
| parentProductName              | Nazwa produktu nadrzędnego. Uwaga: jeśli nie ma produktu nadrzędnego dla transakcji, a następnie nadrzędna nazwa produktu = Nazwa produktu.   |
| productType                    | Typ produktu (np. aplikacja, dodatek, gra itp.)                                                                                        |
| numer faktury                  | Numer faktury (dotyczy tylko EA)                                                                                                  |
| sprzedawcaId                     | Identyfikator sprzedawcy                                                                                                                      |
| Nazwa sprzedawcy                   | Nazwa odsprzedawcy                                                                                                                            |
| Transactiontype                | Rodzaj transakcji (np. zakup, zwrot, cofnięcie, obciążenie zwrotne itp.)                                                               |
| localProviderSprzedawca            | Lokalny dostawca/sprzedawca rekordu                                                                                                          |
| podatekZnieszony                    | Kwota podatku umorzona (podatki od sprzedaży, użytkowania lub VAT/GST).                                                                                   |
| taxRemitModel                  | Strona odpowiedzialna za zwrot podatków (podatki od sprzedaży, użytkowania lub VAT/GST).                                                                    |
| storeFee                       | Kwota zatrzymana przez firmę Microsoft jako opłata za udostępnienie aplikacji lub dodatku w Sklepie.                                            |
| transactionPaymentMetoda       | Instrument płatniczy klienta używany do transakcji (np. karta, rachunek operatora komórkowego, paypal itp.)                                |
| tpan (tpan)                           | Wskazuje sieć reklamową innej firmy                                                                                                     |
| klientKraj                | Kraj klienta                                                                                                                         |
| customerCity (klientMiasto                   | Miasto klienta                                                                                                                            |
| customerState                  | Stan klienta                                                                                                                           |
| customerZip (Numer klienta)                    | Kod pocztowy odbiorcy                                                                                                                 |
| Identyfikator dzierżawy                       |                                                                                                                                          |
| identyfikator zewnętrzny            | Unikatowy identyfikator programu                                                                                                        |
| zewnętrznaReferystykieIłabel       | Unikatowa etykieta identyfikatora                                                                                                                  |
| transactionCountryCode       | Kod kraju, w którym doszło do transakcji                                                                                                                  |
| podatkiKraj       | Sprzedane do kraju klienta                                                                                                                  |
| taxState (stan podatku)       | Stan sprzedany klientowi                                                                                                                  |
| taxCity (podatek)       | Sprzedane do Customer City                                                                                                                  |
| taxZipCode       | Sprzedane do klienta Zip                                                                                                                  |
| Nazwa programu licencjonowania       |                                                                                                                   |
| Kod programu       | Ciąg do mapy z nazwą programu                                                                                                                   |
| zarabianieAmountInLastPaymentCurrency       | Kwota zarobkowania w ostatniej walucie płatności (pole będzie puste, jeśli nie zapłacono żadnych wcześniejszych płatności)                                                                                                                   |
| lastPaymentCurrency       | Ostatnia waluta płatności (pole będzie puste, jeśli nie zapłacono wcześniejszej płatności)                                                                                                                   |
| AssetId (ida aktywów)       | Unikatowy identyfikator zamówień klientów dla usługi marketplace.  Reprezentuje elementy zamówienia zakupu zrealizowane. Może istnieć wiele zasobów.                                                                                                                   |
| OrderId (Identyfikator zamówienia)       | odnosi się do faktury klienta                                                                                                                   |
| Ijka linii       | indywidualny wiersz na fakturze odbiorcy                                                                                                                   |
| Kraj klienta       | Nazwa kraju podana przez klienta.  Może to być inny niż kraj w subskrypcji platformy Azure klienta.                                                                                                                   |
| Adres e-mail klienta       | Adres e-mail podany przez klienta końcowego.  Może to być inna niż adres e-mail w subskrypcji platformy Azure klienta.                                                                                                                   |
| SkuId ( skuid )       | Identyfikator jednostki SKU zdefiniowany podczas publikowania. Oferta może mieć wiele jednostek SKU, ale jednostka SKU może być skojarzona tylko z jedną ofertą.                                                                                                                   |

>[!Note]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania transakcji są dostępne za pośrednictwem sekcji Insights w witrynie Cloud Partner Portal lub Analytics w Centrum partnerów.

## <a name="billing-questions-and-support"></a>Pytania dotyczące rozliczeń i pomoc techniczna

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu marketplace.](https://partner.microsoft.com/support/v2/?stage=1)
