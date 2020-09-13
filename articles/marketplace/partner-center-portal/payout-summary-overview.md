---
title: Omówienie podsumowania wypłaty, Azure Marketplace
description: Podsumowanie wypłaty zawiera szczegółowe informacje o pieniędzy uzyskanych w ramach oferty. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 708fc2c0783bdefa4ac4fa4b73f10733bba0bc04
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006928"
---
# <a name="payout-summary-overview"></a>Omówienie podsumowania wypłat

[Podsumowanie wypłaty](./payout-summary.md) zawiera szczegółowe informacje o pieniędzy uzyskanych w firmie Microsoft. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.

Jeśli sprzedajesz oferty w portalu Azure Marketplace, zobaczysz również informacje o pomyślnych wypłatach w podsumowaniu wypłat. Aby uzyskać więcej informacji o płatnościach w portalu Azure Marketplace, zobacz artykuł dotyczący sprzedaży [komercyjnej](./get-paid.md) i [umowy Microsoft Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Aby można było skorzystać z wypłat, Twoje przejścia muszą osiągać próg płatności wynoszący $50. Aby uzyskać szczegółowe informacje, zobacz [umowę wydawcy firmy Microsoft](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Aby wyświetlić szczegóły wypłaty, zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home) i wybierz ikonę wypłaty w prawym górnym rogu ekranu:

![Przedstawia ikonę wypłaty w prawym górnym rogu portalu Centrum partnerskiego.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Role i uprawnienia

Oto role i uprawnienia umożliwiające dostęp do raportu wypłaty:

| Raporty/strony | Właściciel konta | Menedżer | Deweloper | Współautor firmy | Współautor finansów | Wiodący |
| --- | --- | --- | --- | --- | --- | --- |
| Raport pozyskiwania (w tym dane niemal w czasie rzeczywistym) | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu | Może wyświetlać | Brak dostępu |
| Raport/odpowiedzi na Opinie | Może wyświetlać i przesyłać opinie | Może wyświetlać i przesyłać opinie | Może wyświetlać i przesyłać opinie | Brak dostępu | Brak dostępu | Może wyświetlać i przesyłać opinie |
| --- | --- | --- | --- | --- | --- | --- |
| Raport o kondycji (łącznie z danymi niemal w czasie rzeczywistym) | Może wyświetlać | Może wyświetlać | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu |
| Raport użycia | Może wyświetlać | Może wyświetlać | Może wyświetlać | Może wyświetlać | Brak dostępu | Brak dostępu |
| Konto wypłaty | Może aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Może aktualizować | Brak dostępu |
| Profil podatkowy | Może aktualizować | Brak dostępu | Brak dostępu | Brak dostępu | Może aktualizować | Brak dostępu |
| Podsumowanie wypłaty | Może wyświetlać | Brak dostępu | Brak dostępu | Brak dostępu | Może wyświetlać | Brak dostępu  |
| | | | | | | |

## <a name="payment-schedules"></a>Harmonogramy płatności

Omówienie harmonogramów płatności, w tym okresów przechowywania, widoczności partnerów i sytuacji, gdy klient korzysta z karty kredytowej lub faktury, zapoznaj się z sekcją [harmonogramy płatności](./payout-policy-details.md#payment-schedules) artykułu **szczegóły wypłaty** .

## <a name="transaction-history-download-export"></a>Pobieranie historii transakcji — eksport

Ta opcja zapewnia pobranie każdego elementu linii zdobywania, który jest widoczny na stronie Historia transakcji. Obejmuje to typ zdobywania, datę, skojarzoną kwotę transakcji, klienta, produkt oraz inne szczegóły transakcyjne powiązane z programem zachęt.

| Nazwa kolumny | Opis |
| --- | --- |
| earningId | Unikatowy identyfikator dla każdego zdobywania |
| participantId | Podstawowa tożsamość ponosząca partnera w ramach programu |
| participantIdType | Identyfikator programu dla programów zachęt i sprzedawcy, jeśli program jest przeznaczony dla programów do magazynowania i Azure Marketplace |
| uczestnikname | Nazwa partnera zdobywania |
| partnerCountryCode | Lokalizacja/kraj/region partnera zdobywania |
| programName | Nazwa programu zachęty/sklepu |
| transactionId | Unikatowy identyfikator transakcji |
| transactionCurrency | Waluta, w której wystąpiła oryginalna transakcja klienta (nie jest to waluta lokalizacji partnera) |
| transactionDate | Data transakcji. Przydatne w przypadku programów, w których wiele transakcji przyczynia się do jednego zdobywania |
| transactionExchangeRate | Kurs wymiany używany do wyświetlania odpowiedniej kwoty (USD) transakcji |
| transactionAmount | Kwota transakcji w pierwotnej walucie transakcji oparta na wygenerowanym wykorzystaniu |
| transactionAmountUSD | Kwota transakcji w dolarach amerykańskich (USD) |
| górę | Reguła biznesowa dla zdobywania |
| earningRate | Stawka zachęt zastosowana do kwoty transakcji w celu wygenerowania zdobywania |
| quantity | Liczba rozliczeń dla programów transakcyjnych. Różni się w zależności od programu |
| Liczba | Typ liczby, na przykład: rozliczona liczba, miesięcznych użytkowników aktywnych (MAU) |
| Pozostały | Wskazuje, czy jest to opłata, Rabat, Coop, sprzedaż itd. |
| earningAmount | Kwota zdobywania w pierwotnej walucie transakcji |
| earningAmountUSD | Kwota zdobywania w USD |
| earningDate | Data okresu zdobywania |
| calculationDate | Data obliczenia okresu zdobywania w systemie |
| earningExchangeRate | Kurs wymiany używany do wyświetlania odpowiedniej ilości USD |
| exchangeRateDate | Data kursu wymiany używana do obliczania EarningAmount USD |
| paymentAmountWOTax | Kwota zarobków (bez podatku) w przypadku płatności w walucie w przypadku &quot; tylko wysłanych &quot; płatności |
| paymentCurrency | Płatność na walutę wybraną przez partnera w profilu płatności. Wyświetlane tylko dla wysłanych płatności |
| paymentExchangeRate | Kurs wymiany używany do obliczania paymentAmountWOTax w walucie płatności przy użyciu ExchangeRateDate |
| paymentId | Unikatowy identyfikator płatności. Ta liczba jest widoczna w wyrażeniu bankowym |
| paymentStatus | Stan płatności |
| paymentStatusDescription | Opis stanu płatności |
| customerId | Zawsze będzie puste |
| customerName | Zawsze będzie puste |
| partNumber | Zawsze będzie puste |
| productName | Nazwa produktu połączona z transakcją |
| productId | Unikatowy identyfikator produktu |
| parentProductId | Unikatowy identyfikator nadrzędnego produktu. Jeśli nie ma produktu nadrzędnego dla transakcji, identyfikator produktu nadrzędny = identyfikator produktu. |
| parentProductName | Nazwa produktu nadrzędnego. Jeśli nie ma produktu nadrzędnego dla transakcji, nazwa produktu nadrzędnego = Nazwa produktu. |
| productType | Typ produktu (na przykład aplikacja, dodatek i gra) |
| invoiceNumber | Numer faktury (dotyczy tylko umów Enterprise Agreement) |
| resellerId | Identyfikator odsprzedawcy |
| Nazwa odsprzedawcy | Nazwa odsprzedawcy |
| transactionType | Typ transakcji (na przykład zakupu, zwrotu, odwrócenia i obciążenia zwrotnego) |
| localProviderSeller | Dostawca lokalny/sprzedawca rekordu |
| taxRemitted | Kwota podatku (sprzedaż, użycie lub podatki VAT/GST (). |
| taxRemitModel | Osoba odpowiedzialna za przekazaną podatki (sprzedaż, użycie lub podatki VAT/GST (). |
| storeFee | Kwota zachowywana przez firmę Microsoft jako opłata za udostępnienie aplikacji lub dodatku na rynku komercyjnym. |
| transactionPaymentMethod | Instrument płatniczy klienta używany do transakcji (na przykład karta, rozliczenia z nośnikami mobilnymi i system PayPal) |
| tpan | Sieć usługi AD innej firmy |
| customerCountry | Kraj/region klienta |
| customerCity | Miasto klienta |
| customerState | Stan klienta |
| customerZip | Kod pocztowy klienta |
| TenantID | Identyfikator dzierżawy |
| externalReferenceId | Unikatowy identyfikator programu |
| externalReferenceIdLabel | Etykieta unikatowego identyfikatora |
| transactionCountryCode | Kod kraju/regionu, w którym wystąpiła transakcja |
| taxCountry | Kraj/region klienta |
| taxState | Stan klienta |
| taxCity | Miasto klienta |
| taxZipCode | Kod pocztowy klienta |
| LicensingProgramName | Nazwa programu licencjonowania |
| Kod programu | Ciąg do mapowania przy użyciu nazwy programu |
| earningAmountInLastPaymentCurrency | Kwota zarobków w ostatniej walucie płatności (pole będzie puste, jeśli nie zostały uiszczone żadne wcześniejsze płatności) |
| lastPaymentCurrency | Waluta ostatniej płatności (pole będzie puste, jeśli nie zapłacisz żadnej wcześniejszej płatności) |
| AssetId | Unikatowy identyfikator zamówień klientów dla usługi w portalu Marketplace. Reprezentuje elementy wiersza zakupu. Może istnieć wiele zasobów. |
| OrderId (Identyfikator zamówienia) | Odnosi się do faktury klienta |
| LineItemId | Pojedynczy wiersz faktury klienta |
| Kraj/region klienta | Nazwa kraju/regionu podana przez klienta. Może to być inne niż kraj/region w subskrypcji platformy Azure klienta. |
| EmailAddress klienta | Adres e-mail podany przez klienta. Może się to różnić od adresu e-mail w subskrypcji platformy Azure klienta. |
| Identyfikatora skuId | Identyfikator jednostki SKU zdefiniowany podczas publikowania. Oferta może mieć wiele jednostek SKU, ale jednostka SKU może być skojarzona tylko z jedną ofertą. |

> [!NOTE]
> Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact można znaleźć w sekcji Analiza w centrum partnerskim.

## <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń
Aby uzyskać pomoc techniczną, skontaktuj się z działem [pomocy technicznej wydawcy](https://partner.microsoft.com/support/v2/?stage=1)w portalu Marketplace.

## <a name="next-step"></a>Następny krok

- [Podsumowania wypłat](./payout-summary.md)
