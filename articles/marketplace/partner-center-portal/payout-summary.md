---
title: Podsumowanie komercyjnej wypłaty z witryny Marketplace | Portal Azure Marketplace
description: Podsumowanie wypłaty zawiera szczegółowe informacje o pieniędzy uzyskanych w ramach oferty. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730202"
---
# <a name="payout-reporting"></a>Raportowanie wypłaty

[**Podsumowanie wypłaty**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) zawiera szczegółowe informacje o pieniędzy uzyskanych w firmie Microsoft. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.

Jeśli sprzedajesz oferty w portalu Azure Marketplace, zobaczysz również informacje o pomyślnych wypłatach w **podsumowaniu wypłat**. Aby uzyskać więcej informacji na temat płatności w portalu Azure Marketplace, zapoznaj się z tematem [Microsoft Azure Marketplace zasady udziału](https://go.microsoft.com/fwlink/p/?LinkId=722436) oraz [umowę wydawcy Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby można było skorzystać z wypłat, Twoje przejścia muszą osiągać [próg płatności](payment-thresholds-methods-timeframes.md) wynoszący $50. Aby uzyskać szczegółowe informacje na temat progu płatności, zobacz Tę stronę i zapoznaj się z [umową wydawcy Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Role i uprawnienia dostępu do raportu wypłaty](#roles-and-permission-to-access-the-payout-report)
- [Raport wypłaty: różnica między portal Cloud Partner a centrum partnerskim](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy klientów](#customer-types)
- [Relacja między wypłatami i użyciem](#corelation-between-payout-and-usage)
- [Pobieranie historii transakcji](#transaction-history-download-export)
- [Pytania i pomoc techniczna dotyczące rozliczeń](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Role i uprawnienia dostępu do raportu wypłaty

| Raporty/strony    | Właściciel konta    | Menedżer  | Deweloper | Współautor firmy |  Współautor finansów | Wiodący |
|------------------|------------------|----------|-----------|----|----|-----|
| Raport pozyskiwania (w tym dane niemal w czasie rzeczywistym) | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu | Może wyświetlać | Brak dostępu |
| Raport/odpowiedzi na Opinie | Może wyświetlać i przesyłać opinie | Może wyświetlać i przesyłać opinie | Może wyświetlać i przesyłać opinie | Brak dostępu | Brak dostępu | Może wyświetlać i przesyłać opinie |
| Raport o kondycji (łącznie z danymi niemal w czasie rzeczywistym) | Może wyświetlać | Może wyświetlać | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu |
| Raport użycia | Może wyświetlać | Może wyświetlać | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu |
| Konto wypłaty | Może aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Może aktualizować | Brak dostępu |
| Profil podatkowy | Może aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Może aktualizować | Brak dostępu |
| Podsumowanie wypłaty | Może wyświetlać | Brak dostępu | Brak dostępu | Brak dostępu | Może wyświetlać | Brak dostępu |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Raport wypłaty: różnica między portal Cloud Partner a centrum partnerskim

| | Portal Cloud Partner | Centrum partnerskie |
|---------|---------|---------|
| Linki | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)lub[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Nawigacja | Raportowanie wypłaty w usłudze Insights | Raportowanie wypłaty w centrum partnerskim — ikona wypłaty |
| Zakres | <ul> <li>Element Transaction na wiersz jest widoczny dla kolekcji w toku, zebranych i płatnych </li> <li>Raportowanie — pokazuje wszystkie elementy wiersza po utworzeniu zamówienia zakupu, w tym zbieranie w toku i rozliczanie w toku oraz stan kolekcji i elementy wierszy, które nie są jeszcze kwalifikujące się do płatności. </li> </ul> | <ul> <li>Pokazuje elementy wiersza, gdy są one uznawane za kwalifikujące się zyski.</li> <li>Klienci zwracają się najpierw do firmy Microsoft, a następnie dostawcy ISV mogą zobaczyć raport wypłaty.</li> <li>Raport wypłaty nie będzie wyświetlał kolekcji w toku i rozliczeń w toku.  </li> </ul>  |
| Transakcja nie jest gotowa do wypłaty | Rozliczanie w toku | Następna Szacowana płatność: stan wypłaty jest w stanie nieprzetworzonym.  |
| Stan wypłaty |  | Nieprzetworzone <br> Zdobywanie oferty kwalifikuje się do płatności. Pozostaje w tym stanie przez okres chłodzenia, zgodnie z definicją w przewodniku programu dla programu zachęty. <br> <br> Nowego <br> Zamówienie płatności — generowane w trakcie weryfikacji wewnętrznej przed przetworzeniem płatności. <br> <br> Wysłana <br> Płatność została wysłana do Twojego banku. |

## <a name="customer-types"></a>Typy klientów

### <a name="enterprise-agreement"></a>Umowa Enterprise Agreement

Klienci bez Umowa Enterprise są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Umowa Enterprise są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

### <a name="credit-cards-and-monthly-invoice"></a>Karty kredytowe i Faktura miesięczna

Klienci mogą również skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencje na oprogramowanie będą naliczane miesięcznie.

### <a name="csp-and-direct-pay-users"></a>Dostawcy usług kryptograficznych i bezpośrednich wynagrodzeń

Na przykład jeśli klient kupi przy użyciu karty kredytowej.

## <a name="corelation-between-payout-and-usage"></a>Relacja między wypłatami i użyciem

|Opis    |    Date  | Zamówienia/użycie  | Płat |
|----------|----------|-----------|-------------|
|Okres zamówienia   | 15 sierpnia 2019 — 30 sierpnia, 2019 | **Kolejność atrybutów korelacji** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId</li> </ul> <br> **Wykorzystywani** <br> <ul> <li>CustomerId </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (komputer) </li> </ul> |  |
|Zakończenie okresu (miesiąc)   | 30 sie 2019 | | |
|Data rozliczenia | Września 1, 2019 | | |
|Data płatności klienta | Września 1, 2019 | | |
|Okres płatności (tylko karty kredytowe:, 30 dni) | Września 1, 2019 – września 30, 2019 | | **Kolejność atrybutów korelacji:** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Wykorzystywani** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Nieprzetworzone |
|Początek okresu zbierania | Września 1, 2019 | | |
|Koniec okresu zbierania danych (maksymalnie 30 dni) | Września 30, 2019 | | |
|Data wyliczenia wypłaty (co miesiąc 15) | 1 października 2019 | | **Atrybuty korelacji** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li>Nazwa klienta</li> </ul> <br> **Wykorzystywani** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Nowego |
|Data wypłaty | 15 października 2019 | | **Atrybuty korelacji** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Wykorzystywani** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Płatność wysłana |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Umowa Enterprise Agreement (kwartalne/miesięczne klienci)

| Opis |    Date  | Sposób użycia | Płat |
|----------|----------|---------|-----------|
|Okres zamówienia | 15 sierpnia 2019 — 30 sierpnia, 2019 | **Kolejność atrybutów korelacji** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId</li> </ul> <br> **Raport użycia** <br> <ul> <li>CustomerId </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (komputer) </li> </ul> | |
|Zakończenie okresu (kwartał) | Września 30, 2019 | | |
|Data rozliczenia | 15 października 2019 | | |
|Okres płatności (tylko karty kredytowe:, 30 dni) | n/d | | |
|Początek okresu zbierania | 15 października 2019 | | |
|Tylko karty kredytowe, 30 dni | LIS 1, 2019-lis 30, 2019 | | |
|Koniec okresu zbierania danych (maksymalnie 90 dni) | 15 stycznia 2020 | | |
|Data płatności klienta | 30 grudnia, 2019 | | |
|Obliczanie wypłaty | 15 stycznia 2020 | | |
|Data wypłaty | 15 lutego 2020 | | **Dla kwartalnych klientów** <br> <br> **Raport o zamówieniach** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Wykorzystywani** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** wysłano |

## <a name="transaction-history-download-export"></a>Pobieranie historii transakcji — eksport

Ta opcja zapewnia pobranie każdego elementu linii zdobywania, który jest widoczny na stronie historii transakcji, typu zdobywania, daty, skojarzonej transakcji, liczby klientów, produktu i innych informacji transakcyjnych dotyczących programu zachęt.

| Nazwa kolumny     | Opis    |
|-------------|-------------------------------|
| earningId                      | Unikatowy identyfikator dla każdego zdobywania                                                                                                       |
| participantId                  | Podstawowa tożsamość ponosząca partnera w ramach programu                                                                            |
| participantIdType              | Przede wszystkim IDENTYFIKATORem programu dla programów zachęt i sprzedawcy w przypadku programów do magazynowania i Azure Marketplace                                          |
| uczestnikname                | Nazwa partnera zdobywania                                                                                                              |
| partnerCountryCode             | Lokalizacja/kraj partnera zdobywania                                                                                                  |
| programName                    | Nazwa programu zachęty/sklepu                                                                                                             |
| transactionId                  | Unikatowy identyfikator transakcji                                                                                                    |
| transactionCurrency            | Waluta, w której wystąpiła oryginalna transakcja klienta (nie jest to waluta lokalizacji partnera)                                     |
| transactionDate                | Data transakcji. Przydatne w przypadku programów, w których wiele transakcji przyczynia się do jednego zdobywania                                           |
| transactionExchangeRate        | Kurs wymiany używany do wyświetlania odpowiedniej kwoty (USD) transakcji                                                                 |
| transactionAmount              | Kwota transakcji w pierwotnej walucie transakcji oparta na wygenerowanym wykorzystaniu                                              |
| transactionAmountUSD           | Kwota transakcji w USD                                                                                                                |
| górę                          | Oznacza regułę biznesową dla zdobywania                                                                                                  |
| earningRate                    | Stawka zachęt zastosowana do kwoty transakcji w celu wygenerowania zdobywania                                                                      |
| quantity                       | Różni się w zależności od programu. Wskazuje liczbę rozliczeń dla programów transakcyjnych                                                            |
| Liczba                   | Wskazuje typ ilości, na przykład: rozliczona ilość, MAU                                                                                     |
| Pozostały                    | Wskazuje, czy jest to opłata, Rabat, Coop, sprzedaż itp.                                                                                          |
| earningAmount                  | Kwota zdobywania w pierwotnej walucie transakcji                                                                                      |
| earningAmountUSD               | Kwota zdobywania w USD                                                                                                                    |
| earningDate                    | Data okresu zdobywania                                                                                                                      |
| calculationDate                | Data obliczenia okresu zdobywania w systemie                                                                                            |
| earningExchangeRate            | Kurs wymiany używany do wyświetlania odpowiedniej ilości USD                                                                                  |
| exchangeRateDate               | Data kursu wymiany używana do obliczania EarningAmount USD                                                                                   |
| paymentAmountWOTax             | Kwota zarobków (bez podatku) w ramach płatności w walucie w przypadku płatności "wysłanych"                                                                 |
| paymentCurrency                | Płatność na walutę wybraną przez partnera w profilu płatności. Wyświetlane tylko dla wysłanych płatności                                                   |
| paymentExchangeRate            | Kurs wymiany używany do obliczania paymentAmountWOTax w walucie płatności przy użyciu ExchangeRateDate                                            |
| paymentId            | Unikatowy identyfikator płatności. Ta liczba jest widoczna w wyrażeniu bankowym                                            |
| paymentStatus            | Stan płatności                                            |
| paymentStatusDescription            | Przyjazny opis stanu płatności                                            |
| customerId                     | Zawsze będzie puste                                                                                                                     |
| customerName                   | Zawsze będzie puste                                                                                                                     |
| partNumber                     | Zawsze będzie puste                                                                                                                     |
| productName                    | Nazwa produktu połączona z transakcją                                                                                                       |
| productId                      | Unikatowy identyfikator produktu                                                                                                                |
| parentProductId                | Unikatowy identyfikator nadrzędnego produktu. Uwaga: Jeśli nie istnieje produkt nadrzędny dla transakcji, identyfikator produktu nadrzędny = identyfikator produktu. |
| parentProductName              | Nazwa produktu nadrzędnego. Uwaga: Jeśli nie istnieje produkt nadrzędny dla transakcji, nazwa produktu nadrzędnego = Nazwa produktu.   |
| productType                    | Typ produktu (na przykład aplikacja, dodatek, gra itp.)                                                                                        |
| invoiceNumber                  | Numer faktury (dotyczy tylko EA)                                                                                                  |
| resellerId                     | Identyfikator odsprzedawcy                                                                                                                      |
| Nazwa odsprzedawcy                   | Nazwa odsprzedawcy                                                                                                                            |
| transactionType                | Typ transakcji (na przykład zakup, zwrot, odwrócenie, obciążenia zwrotnego itd.)                                                               |
| localProviderSeller            | Dostawca lokalny/sprzedawca rekordu                                                                                                          |
| taxRemitted                    | Kwota podatku (sprzedaż, użycie lub podatki VAT/GST ().                                                                                   |
| taxRemitModel                  | Osoba odpowiedzialna za przekazaną podatki (sprzedaż, użycie lub podatki VAT/GST ().                                                                    |
| storeFee                       | Kwota zachowywana przez firmę Microsoft jako opłata za udostępnienie aplikacji lub dodatku w sklepie.                                            |
| transactionPaymentMethod       | Instrument płatniczy klienta używany do transakcji (na przykład karta, rozliczenia z nośnika komórkowego, płatności w systemie PayPal itp.)                                |
| tpan                           | Wskazuje sieć usługi AD innej firmy                                                                                                     |
| customerCountry                | Kraj klienta                                                                                                                         |
| customerCity                   | Miasto klienta                                                                                                                            |
| customerState                  | Stan klienta                                                                                                                           |
| customerZip                    | Kod pocztowy klienta                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Unikatowy identyfikator programu                                                                                                        |
| externalReferenceIdLabel       | Etykieta unikatowego identyfikatora                                                                                                                  |
| transactionCountryCode       | Kod kraju, w którym wystąpiła transakcja                                                                                                                  |
| taxCountry       | Sprzedaż do kraju klienta                                                                                                                  |
| taxState       | Sprzedaż do stanu klienta                                                                                                                  |
| taxCity       | Sprzedaż na miasto klienta                                                                                                                  |
| taxZipCode       | Sprzedaż do pliku zip klienta                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Kod programu       | Ciąg do mapowania przy użyciu nazwy programu                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Kwota zarobków w ostatniej walucie płatności (pole będzie puste, jeśli nie zostały uiszczone żadne wcześniejsze płatności)                                                                                                                   |
| lastPaymentCurrency       | Waluta ostatniej płatności (pole będzie puste, jeśli nie zapłacisz żadnej wcześniejszej płatności)                                                                                                                   |
| AssetId       | Unikatowy identyfikator zamówień klientów dla usługi w portalu Marketplace.  Reprezentuje elementy wiersza zakupu transakcji transakcyjnych. Może istnieć wiele zasobów.                                                                                                                   |
| OrderId (Identyfikator zamówienia)       | odnosi się do faktury klienta                                                                                                                   |
| LineItemId       | pojedynczy wiersz faktury klienta                                                                                                                   |
| Kraj klienta       | Nazwa kraju podana przez klienta.  Może to być inne niż kraj w subskrypcji platformy Azure klienta.                                                                                                                   |
| EmailAddress klienta       | Adres e-mail dostarczony przez klienta końcowego.  Może się to różnić od adresu e-mail w subskrypcji platformy Azure klienta.                                                                                                                   |
| Identyfikatora skuId       | Identyfikator jednostki SKU zdefiniowany podczas publikowania. Oferta może mieć wiele jednostek SKU, ale jednostka SKU może być skojarzona tylko z jedną ofertą.                                                                                                                   |

>[!Note]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji szczegółowe informacje w sekcji portal Cloud Partner lub Analytics Centrum partnerskiego.

## <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://partner.microsoft.com/support/v2/?stage=1).
