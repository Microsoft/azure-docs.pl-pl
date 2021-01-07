---
title: Interpretacja szczegółów użycia i opłat
description: Dowiedz się, jak odczytywać i interpretować szczegółowe informacje dotyczące użycia i opłat. Wyświetl listę warunków i opisów używanych w pliku.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: banders
ms.openlocfilehash: 07e3cfdce238d5fc4e2737a49dde6fd624de8506
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882504"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Interpretacja terminów w pliku Użycie i opłaty platformy Azure

Plik szczegółów użycia i opłat zawiera informacje na temat dziennego użycia oparte na stawkach negocjowanych, zakupach (na przykład rezerwacjach, opłatach w witrynie Marketplace) i zwrotach w wybranym okresie.
Opłaty nie obejmują kredytów, podatków ani innych obciążeń i rabatów.
W poniższej tabeli przedstawiono opłaty za poszczególne typy kont.

Typ konta | Użycie platformy Azure | Użycie portalu Marketplace | Zakupy | Zwroty
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Yes | Yes | Tak | Nie
Umowa klienta firmy Microsoft (MCA) | Yes | Yes | Yes | Tak
Płatność zgodnie z rzeczywistym użyciem | Yes | Tak | Nie | Nie

Aby dowiedzieć się więcej o zamówieniach w witrynie Marketplace (znanych również jako usługi zewnętrzne), zobacz temat [Understand your Azure external service charges](understand-azure-marketplace-charges.md) (Omówienie opłat za usługi zewnętrzne platformy Azure).

Instrukcje dotyczące pobierania można znaleźć w temacie [How to get your Azure billing invoice and daily usage data](../manage/download-azure-invoice-daily-usage-date.md) (Jak pobrać fakturę rozliczeniową za platformę Azure i dane dziennego użycia).
Plik CSV użycia i opłat można otworzyć w programie Microsoft Excel lub w innej aplikacji obsługującej arkusze kalkulacyjne.

## <a name="list-of-terms-and-descriptions"></a>Lista terminów i opisów

W poniższej tabeli przedstawiono ważne terminy używane w najnowszej wersji pliku użycia i opłat platformy Azure.
Lista obejmuje konta płatności zgodnie z rzeczywistym użyciem, umów Enterprise Agreement (EA) i umów klienta firmy Microsoft (MCA).

Okres | Typ konta | Opis
--- | --- | ---
AccountName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa wyświetlana konta rejestracji umowy EA lub konta rozliczeniowego płatności zgodnie z rzeczywistym użyciem.
AccountOwnerId<sup>1</sup> | EA, płatność zgodnie z rzeczywistym użyciem | Unikatowy konta rejestracji umowy EA lub konta rozliczeniowego płatności zgodnie z rzeczywistym użyciem.
AdditionalInfo | Wszyscy | Metadane dotyczące konkretnej usługi. Na przykład typ obrazu dla maszyny wirtualnej.
BillingAccountId<sup>1</sup> | Wszyscy | Unikatowy identyfikator głównego konta rozliczeniowego.
BillingAccountName | Wszyscy | Nazwa konta rozliczeniowego.
BillingCurrency | Wszyscy | Waluta skojarzona z kontem rozliczeniowym.
BillingPeriod | EA, płatność zgodnie z rzeczywistym użyciem | Okres rozliczeniowy opłaty.
BillingPeriodEndDate | Wszyscy | Data zakończenia okresu rozliczeniowego.
BillingPeriodStartDate | Wszyscy | Data rozpoczęcia okresu rozliczeniowego.
BillingProfileId<sup>1</sup> | Wszyscy | Unikatowy identyfikator rejestracji umowy EA, subskrypcji płatności zgodnie z rzeczywistym użyciem, profilu rozliczeniowego umowy MCA lub skonsolidowanego konta AWS.
BillingProfileName | Wszyscy | Nazwa rejestracji umowy EA, subskrypcji płatności zgodnie z rzeczywistym użyciem, profilu rozliczeniowego umowy MCA lub skonsolidowanego konta AWS.
ChargeType | Wszyscy | Wskazuje, czy opłata reprezentuje użycie (**Usage**), zakup (**Purchase**) czy zwrot (**Refund**).
ConsumedService | Wszyscy | Nazwa usługi, z którą skojarzono opłatę.
CostCenter<sup>1</sup> | EA, umowa klienta firmy Microsoft | Centrum kosztów zdefiniowane dla subskrypcji na potrzeby śledzenia kosztów (dostępne tylko w otwartych okresach rozliczeniowych dla kont umów klienta z firmą Microsoft).
Koszty | EA, płatność zgodnie z rzeczywistym użyciem | Zobacz CostInBillingCurrency.
CostInBillingCurrency | Umowa klienta firmy Microsoft | Koszt opłaty w walucie rozliczeniowej przed odliczeniem środków lub podatków.
CostInPricingCurrency | Umowa klienta firmy Microsoft | Koszt opłaty w walucie ceny przed odliczeniem środków lub podatków.
Waluta | EA, płatność zgodnie z rzeczywistym użyciem | Zobacz BillingCurrency.
Data<sup>1</sup> | Wszyscy | Użycie lub data zakupu dla opłaty.
EffectivePrice | Wszyscy | Łączna cena jednostkowa w danym okresie. Ceny łączne umożliwiają uśrednienie wahań ceny jednostkowej, takich jak w przypadku stopniowej obsługi warstw, które obniżają cenę w miarę wzrostu ilości w czasie.
ExchangeRateDate | Umowa klienta firmy Microsoft | Data ustalenia kursu wymiany.
ExchangeRatePricingToBilling | Umowa klienta firmy Microsoft | Kurs wymiany używany do konwersji kosztów w walucie ceny na walutę rozliczeń.
Częstotliwość | Wszyscy | Wskazuje, czy oczekuje się powtórzenia opłaty. Opłaty mogą mieć być naliczane jednorazowo (**OneTime**), powtarzać się co miesiąc lub co rok (**Recurring**) lub opierać się na użyciu (**UsageBased**).
InvoiceId | Płatność zgodnie z rzeczywistym użyciem, umowa klienta firmy Microsoft | Unikatowy identyfikator dokumentu w pliku PDF faktury.
InvoiceSection | Umowa klienta firmy Microsoft | Zobacz InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, umowa klienta firmy Microsoft | Unikatowy identyfikator dla działu umów EA lub sekcji faktury umowy klienta firmy Microsoft.
InvoiceSectionName | EA, umowa klienta firmy Microsoft | Nazwa działu umów EA lub sekcji faktury umowy klienta firmy Microsoft.
IsAzureCreditEligible | Wszyscy | Wskazuje, czy opłatę można uiścić przy użyciu środków na korzystanie z platformy Azure (wartości: True, False).
Lokalizacja | Umowa klienta firmy Microsoft | Lokalizacja centrum danych, w którym jest uruchamiany zasób.
MeterCategory | Wszyscy | Nazwa kategorii klasyfikacji dla miernika. Na przykład *Usługi w chmurze* i *Sieć*.
MeterId<sup>1</sup> | Wszyscy | Unikatowy identyfikator miernika.
MeterName | Wszyscy | Nazwa miernika.
MeterRegion | Wszyscy | Nazwa lokalizacji centrum danych dla usług, które są wyceniane na podstawie lokalizacji. Zobacz Location.
MeterSubCategory | Wszyscy | Nazwa kategorii klasyfikacji podrzędnej miernika.
OfferId<sup>1</sup> | Wszyscy | Nazwa zakupionej oferty.
PayGPrice | Wszyscy | Cena detaliczna zasobu.
PartNumber<sup>1</sup> | EA, płatność zgodnie z rzeczywistym użyciem | Identyfikator używany do uzyskiwania określonych cen taryfowych.
PlanName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa planu witryny Marketplace.
PreviousInvoiceId | Umowa klienta firmy Microsoft | Odwołanie do oryginalnej faktury, jeśli ten element wiersza to zwrot.
PricingCurrency | Umowa klienta firmy Microsoft | Waluta używana w przypadku klasyfikacji na podstawie cen negocjowanych.
PricingModel | Wszyscy | Identyfikator wskazujący, w jaki sposób są naliczane opłaty za licznik. (Wartości: Na żądanie, Rezerwacja, Spot)
Product (Produkt) | Wszyscy | Nazwa produktu.
ProductId<sup>1</sup> | Umowa klienta firmy Microsoft | Unikatowy identyfikator produktu.
ProductOrderId | Wszyscy | Unikatowy identyfikator zamówienia produktu.
ProductOrderName | Wszyscy | Unikatowy identyfikator zamówienia produktu.
PublisherName | Wszyscy | Wydawca dla usług Marketplace.
PublisherType | Wszyscy | Typ wydawcy (wartości: **Azure**, **AWS**, **Marketplace**).
Liczba | Wszyscy | Liczba jednostek zakupionych lub użytych.
ReservationId | EA, umowa klienta firmy Microsoft | Unikatowy identyfikator zakupionego wystąpienia rezerwacji.
ReservationName | EA, umowa klienta firmy Microsoft | Nazwa zakupionego wystąpienia rezerwacji.
ResourceGroup | Wszyscy | Nazwa [grupy zasobów](../../azure-resource-manager/management/overview.md), w której znajduje się zasób. Nie wszystkie opłaty wynikają z użycia zasobów wdrożonych w grupach zasobów. Opłaty, które nie są związane z grupą zasobów, będą wyświetlane jako null/puste, **Inne** lub z oznaczeniem **Nie dotyczy**.
ResourceId<sup>1</sup> | Wszyscy | Unikatowy identyfikator zasobu usługi [Azure Resource Manager](/rest/api/resources/resources).
ResourceLocation | Wszyscy | Lokalizacja centrum danych, w którym jest uruchamiany zasób. Zobacz Location.
ResourceName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa zasobu. Nie wszystkie opłaty wynikają z użycia wdrożonych zasobów. Opłaty, które nie są związane z typem zasobu, będą wyświetlane jako null/puste, **Inne** lub z oznaczeniem **Nie dotyczy**.
ResourceType | Umowa klienta firmy Microsoft | Typ wystąpienia zasobu. Nie wszystkie opłaty wynikają z użycia wdrożonych zasobów. Opłaty, które nie są związane z typem zasobu, będą wyświetlane jako null/puste, **Inne** lub z oznaczeniem **Nie dotyczy**.
ServiceFamily | Umowa klienta firmy Microsoft | Rodzina usług, do której należy usługa.
ServiceInfo1 | Wszyscy | Metadane dotyczące konkretnej usługi.
ServiceInfo2 | Wszyscy | Starsze pole z opcjonalnymi metadanymi specyficznymi dla usługi.
ServicePeriodEndDate | Umowa klienta firmy Microsoft | Data zakończenia okresu klasyfikacji, w którym zdefiniowano i zablokowano ceny usługi wykorzystanej lub zakupionej.
ServicePeriodStartDate | Umowa klienta firmy Microsoft | Data rozpoczęcia okresu klasyfikacji, w którym zdefiniowano i zablokowano ceny usługi wykorzystanej lub zakupionej.
SubscriptionId<sup>1</sup> | Wszyscy | Unikatowy identyfikator subskrypcji platformy Azure.
SubscriptionName | Wszyscy | Nazwa subskrypcji platformy Azure.
Tags<sup>1</sup> | Wszyscy | Tagi przypisane do zasobu. Nie obejmuje tagów grupy zasobów. Może służyć do grupowania lub dystrybuowania kosztów na potrzeby wewnętrznego obciążenia zwrotnego. Aby uzyskać więcej informacji, zobacz temat [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizowanie zasobów platformy Azure za pomocą tagów).
Okres | Wszyscy | Przedstawia okres ważności oferty. Przykład: W przypadku wystąpień zarezerwowanych wyświetlany okres to 12 miesięcy. W przypadku zakupów jednorazowych lub cyklicznych okres to 1 miesiąc (SaaS, pomoc techniczna witryny Marketplace). Nie dotyczy to użycia platformy Azure.
UnitOfMeasure | Wszyscy | Jednostka miary dla rozliczeń usługi. Na przykład usługi obliczeniowe są rozliczane godzinowo.
UnitPrice | EA, płatność zgodnie z rzeczywistym użyciem | Cena za jednostkę dla opłaty.

_<sup>**1**</sup> Pola używane do tworzenia unikatowego identyfikatora dla pojedynczego rekordu kosztu._

Pamiętaj, że niektóre pola mogą różnić się wielkością liter i odstępów między typami kont.
Starsze wersje plików użycia typu „płatność zgodnie z rzeczywistym użyciem” mają oddzielne sekcje dla instrukcji i użycia dziennego.

### <a name="list-of-terms-from-older-apis"></a>Lista terminów ze starszych interfejsów API
Poniższa tabela mapuje terminy używane we wcześniejszych interfejsach API na nowe terminy. Opisy można znaleźć w powyższej tabeli.

Stary termin | Nowy termin
--- | ---
ConsumedQuantity | Liczba
IncludedQuantity | Nie dotyczy
InstanceId | ResourceId
Stawka | EffectivePrice
Jednostka | UnitOfMeasure
UsageDate | Data
UsageEnd | Data
UsageStart | Data

## <a name="ensure-charges-are-correct"></a>Sprawdzanie, czy opłaty są poprawne

Aby dowiedzieć się więcej na temat szczegółów użycia i opłat, przeczytaj więcej na temat interpretowania faktury typu [Płatność zgodnie z rzeczywistym użyciem](review-individual-bill.md) lub [Umowa klienta firmy Microsoft](review-customer-agreement-bill.md).

## <a name="unexpected-usage-or-charges"></a>Nieoczekiwane użycie lub opłaty

W przypadku pojawienia się użycia lub opłat, których nie rozpoznajesz, istnieje kilka czynności, które można wykonać w celu wyjaśnienia sytuacji:

- Przegląd faktury, która zawiera opłaty za zasób
- Przegląd zafakturowanych kosztów w analizie kosztów
- Znajdowanie osób odpowiedzialnych za zasób i kontaktowanie się z nimi
- Analizowanie dzienników inspekcji
- Analizowanie uprawnień użytkowników do nadrzędnego zakresu zasobu
- Utwórz [wniosek o pomoc techniczną platformy Azure](https://go.microsoft.com/fwlink/?linkid=2083458), aby pomóc w zidentyfikowaniu opłat

Aby uzyskać więcej informacji, zobacz [Analizowanie nieoczekiwanych opłat](analyze-unexpected-charges.md).

Pamiętaj, że platforma Azure nie rejestruje większości akcji użytkownika. Zamiast tego, firma Microsoft rejestruje użycie zasobów na potrzeby rozliczeń. Jeśli zauważysz wzrost użycia w przeszłości, a rejestrowanie nie zostało włączone, firma Microsoft nie może wskazać przyczyny. Włącz rejestrowanie dla usługi, dla której chcesz wyświetlić zwiększone użycie, aby odpowiedni zespół techniczny mógł pomóc Ci w rozwiązaniu problemu.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](download-azure-invoice.md)
- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)