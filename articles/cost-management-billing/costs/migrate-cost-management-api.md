---
title: Migrowanie z umowy EA do interfejsów API umowy klienta firmy Microsoft — Azure
description: Ten artykuł pomaga zrozumieć konsekwencje wynikające z migracji z umowy Microsoft Enterprise Agreement (EA) do umowy klienta firmy Microsoft.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078353"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrowanie z umowy Enterprise Agreement do interfejsów API umowy klienta firmy Microsoft

Ten artykuł ułatwia zapoznanie się z różnicami w strukturze danych i interfejsach API oraz innymi różnicami w zakresie integracji systemu między kontem z umową Enterprise Agreement (EA) a kontem z umową klienta firmy Microsoft (MCA). Usługa Azure Cost Management obsługuje interfejsy API dla obu typów kont. Przed wykonaniem kolejnych czynności zapoznaj się z artykułem [Konfigurowanie konta rozliczeniowego dla umowy klienta firmy Microsoft](../manage/mca-setup-account.md).

W przypadku organizacji korzystających z kont EA należy zapoznać się z tym artykułem oraz skonfigurować konto MCA. Wcześniej odnowienie konta EA wymagało minimalnego nakładu pracy związanej z przejściem ze starej rejestracji do nowej. Jednak migracja do konta MCA wymaga wykonania dodatkowych czynności. Jest to związane ze zmianami w bazowym podsystemie rozliczeniowym, które wpływają na wszystkie oferty usług oraz interfejsy API związane z kosztami.

## <a name="mca-apis-and-integration"></a>Integracja oraz interfejsy API MCA

Interfejsy API MCA i nowe możliwości integracji umożliwiają wykonywanie następujących działań:

- Korzystanie z pełnej dostępności interfejsów API za pośrednictwem natywnych interfejsów API platformy Azure.
- Konfigurowanie wielu faktur na jednym koncie rozliczeniowym.
- Uzyskiwanie dostępu do połączonego interfejsu API z użyciem usług platformy Azure, użyciem usług innych firm w witrynie Marketplace oraz zakupami w witrynie Marketplace.
- Wyświetlanie kosztów w profilach rozliczeniowych (takich jak rejestracje) przy użyciu usługi Azure Cost Management.
- Uzyskiwanie dostępu do nowych interfejsów API umożliwiających wyświetlanie kosztów, otrzymywanie powiadomień o przekroczeniu wstępnie zdefiniowanych progów kosztów i automatyczne eksportowanie danych pierwotnych.

## <a name="migration-checklist"></a>Lista kontrolna migracji

Poniższa lista ułatwia przejście na interfejsy API MCA.

- Zapoznaj się z nowym [kontem rozliczeniowym umowy klienta firmy Microsoft](../understand/mca-overview.md).
- Ustal, których interfejsów API używasz. W następnej sekcji sprawdź, które interfejsy API zostały zastąpione.
- Zapoznaj się z [interfejsami API REST usługi Azure Resource Manager](/rest/api/azure).
- Jeśli jeszcze nie używasz interfejsów API usługi Azure Resource Manager, [zarejestruj aplikację kliencką w usłudze Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Zaktualizuj kod w celu [używania uwierzytelniania usługi Azure AD](/rest/api/azure/#create-the-request).
- Zaktualizuj kod, zastępując wywołania interfejsu API EA wywołaniami interfejsu API MCA.
- Zaktualizuj obsługę błędów, wprowadzając nowe kody błędów.
- Zapoznaj się z dodatkowymi ofertami rozwiązań integracyjnych, takich jak Cloudyn i Power BI, do innych potrzebnych zastosowań.

## <a name="ea-apis-replaced-with-mca-apis"></a>Interfejsy API EA zastąpione przez interfejsy API MCA

Interfejsy API EA używają klucza interfejsu API do uwierzytelniania i autoryzacji. Interfejsy API MCA używają uwierzytelniania usługi Azure AD.

| Przeznaczenie | Interfejs API EA | Interfejs API MCA |
| --- | --- | --- |
| Saldo i środki | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Użycie (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Użycie (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Użycie w witrynie Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Okresy rozliczeniowe | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Arkusz cen | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. rozliczenia/billingAccounts/billingProfiles/arkusza cen/default/Download Format = plik \| CSV JSON Microsoft. rozliczeń/billingAccounts/.../billingProfiles/.../faktury/... /pricesheet/default/Download Format = plik \| CSV JSON Microsoft. rozliczeń/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Zakupy rezerwacji | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Rekomendacje dotyczące rezerwacji | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Użycie rezerwacji | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Użycie usług platformy Azure oraz usług innych firm w witrynie Marketplace jest dostępne za pośrednictwem [interfejsu API szczegółów użycia](/rest/api/consumption/usagedetails).

Następujące interfejsy API są dostępne na kontach rozliczeniowych MCA:

| Przeznaczenie | Interfejs API umowy klienta firmy Microsoft (MCA) |
| --- | --- |
| Konta rozliczeniowe<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profile rozliczeniowe<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Sekcje faktury<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faktury | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Subskrypcje rozliczeniowe | {zakres}/billingSubscriptions |

<sup>2</sup> Interfejsy API zwracają listy obiektów, czyli zakresy, w których działają interfejsy API oraz funkcje usługi Cost Management w witrynie Azure Portal. Aby uzyskać więcej informacji na temat zakresów usługi Cost Management, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

W przypadku używania interfejsów API EA należy je zaktualizować pod kątem obsługi kont rozliczeniowych MCA. W poniższej tabeli przedstawiono inne zmiany dotyczące integracji:

| Przeznaczenie | Stara oferta | Nowa oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pakiet zawartości i łącznik [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Łącznik Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Interfejsy API do uzyskiwania informacji o saldzie i środkach

Interfejs API [pobierania podsumowania salda](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) umożliwia uzyskanie miesięcznego podsumowania następujących elementów:

- Salda
- Nowe zakupy
- Opłaty za usługę Azure Marketplace
- Korekty
- Opłaty za nadwyżkowe użycie usług

Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request).

Interfejs API pobierania podsumowania salda został zastąpiony przez interfejs API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Aby uzyskać informacje o dostępnych saldach za pomocą interfejsu API dostępnego salda:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Interfejsy API do uzyskiwania informacji o kosztach i użyciu

Następujące interfejsy API umożliwiają uzyskanie dziennego podziału kosztów użycia usług platformy Azure, użycia usług innych firm w witrynie Marketplace oraz pozostałych zakupów w witrynie Marketplace. Następujące odrębne interfejsy API zostały scalone na potrzeby obsługi użycia usług platformy Azure i usług innych firm w witrynie Marketplace. Stare interfejsy API zostały zastąpione przez interfejs API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Dodano zakupy w witrynie Marketplace, które wcześniej były wyświetlane tylko w podsumowaniu salda do bieżącej daty.

- [Pobieranie szczegółów użycia/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobieranie szczegółów użycia/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Pobieranie szczegółów użycia/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobieranie szczegółów użycia/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Pobieranie opłat za sklep Marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Pobieranie opłat za sklep Marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request).

Wszystkie wymienione interfejsy API zostały zastąpione przez interfejs API zużycia/szczegółów użycia.

Aby uzyskać szczegółowe informacje dotyczące użycia za pomocą interfejsu API szczegółów użycia:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Tak jak wszystkie interfejsy API usługi Cost Management, interfejs API szczegółów użycia jest dostępny w wielu zakresach. Aby uzyskać informacje o zafakturowanych kosztach, tradycyjnie otrzymywane na poziomie rejestracji, użyj zakresu profilu rozliczeniowego.  Aby uzyskać więcej informacji na temat zakresów usługi Cost Management, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

| Typ | Format identyfikatora |
| --- | --- |
| Konto billingowe | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profil rozliczeniowy | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subskrypcja | `/subscriptions/{subscriptionId}` |
| Grupa zasobów | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Aktualizując kod, użyj następujących parametrów ciągu zapytania.

| Stare parametry | Nowe parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nieobsługiwane |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Treść odpowiedzi również uległa zmianie.

Stara treść odpowiedzi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nowa treść odpowiedzi:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Nazwa właściwości zawierającej tablicę rekordów użycia została zmieniona z data na _values_. Wcześniej każdy rekord zawierał niezhierarchizowaną listę szczegółowych właściwości. Aktualnie wszystkie szczegóły znajdują się w zagnieżdżonej właściwości o nazwie _properties_, z wyjątkiem tagów. Nowa struktura jest spójna z innymi interfejsami API platformy Azure. Nazwy niektórych właściwości zostały zmienione. W poniższej tabeli przedstawiono odpowiadające właściwości.

| Poprzednia właściwość | Nowa właściwość | Uwagi |
| --- | --- | --- |
| AccountId | Nie dotyczy | Twórca subskrypcji nie jest śledzony. Użyj właściwości invoiceSectionId (takiej samej jak departmentId). |
| AccountNameAccountOwnerId i AccountOwnerEmail | Nie dotyczy | Twórca subskrypcji nie jest śledzony. Użyj właściwości invoiceSectionName (takiej samej jak departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Należy pamiętać, że te właściwości są przeciwstawne. Jeśli isAzureCreditEnabled ma wartość true, ChargesBilledSeparately ma wartość false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Dokładne wartości ciągów mogą się różnić. |
| ConsumedServiceId | Brak | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date i usageStartDate | data | &nbsp;  |
| Dzień | Brak | Analizuje informacje o dniu na podstawie daty. |
| DepartmentId | invoiceSectionId | Dokładne wartości są różne. |
| DepartmentName | invoiceSectionName | Dokładne wartości ciągów mogą się różnić. W razie potrzeby należy dopasować sekcje faktury do działów. |
| ExtendedCost i Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | Brak | &nbsp;  |
| Lokalizacja | location | &nbsp;  |
| MeterCategory | meterCategory | Dokładne wartości ciągów mogą się różnić. |
| MeterId | meterId | Dokładne wartości ciągów są różne. |
| MeterName | meterName | Dokładne wartości ciągów mogą się różnić. |
| MeterRegion | meterRegion | Dokładne wartości ciągów mogą się różnić. |
| MeterSubCategory | meterSubCategory | Dokładne wartości ciągów mogą się różnić. |
| Month (Miesiąc) | Brak | Analizuje informacje o miesiącu na podstawie daty. |
| Offer Name | Brak | Należy użyć właściwości publisherName i productOrderName. |
| OfferID | Brak | &nbsp;  |
| Order Number | Brak | &nbsp;  |
| PartNumber | Brak | Należy użyć właściwości meterId i productOrderName do unikatowego identyfikowania cen. |
| Plan Name | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | Dokładne wartości ciągów są różne. |
| Publisher Name | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Dokładne wartości ciągów są różne. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Brak | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Nie dotyczy | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Dokładne wartości ciągów mogą się różnić. |
| ServiceTier | meterSubCategory | Dokładne wartości ciągów mogą się różnić. |
| StoreServiceIdentifier | Nie dotyczy | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tagi | tags | Właściwość tags ma zastosowanie do obiektu głównego, a nie do zagnieżdżonej właściwości properties. |
| UnitOfMeasure | unitOfMeasure | Dokładne wartości ciągów są różne. |
| usageEndDate | data | &nbsp;  |
| Year (Rok) | Brak | Analizuje informacje o roku na podstawie daty. |
| (nowa) | billingCurrency | Waluta używana do naliczania opłat. |
| (nowa) | billingProfileId | Unikatowy identyfikator profilu rozliczeniowego (taki sam jak rejestracja). |
| (nowa) | billingProfileName | Nazwa profilu rozliczeniowego (taka sama jak rejestracja). |
| (nowa) | chargeType | Umożliwia identyfikację użycia usług platformy Azure, użycia usług z witryny Marketplace i zakupów. |
| (nowa) | invoiceId | Unikatowy identyfikator faktury. W przypadku bieżącego, otwartego miesiąca jest pusta. |
| (nowa) | publisherType | Typ wydawcy dotyczący zakupów. Wartość pusta. |
| (nowa) | serviceFamily | Typ zakupu. Wartość pusta. |
| (nowa) | servicePeriodEndDate | Data końcowa zakupionej usługi. |
| (nowa) | servicePeriodStartDate | Data początkowa zakupionej usługi. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Interfejs API okresów rozliczeniowych został zastąpiony przez interfejs API faktur

Okresy rozliczeniowe nie są używane na kontach rozliczeniowych MCA. Do określania zakresu kosztów we wskazanych okresach rozliczeniowych służą faktury. [Interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) został zastąpiony przez interfejs API faktur. Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request).

Aby uzyskać faktury za pomocą interfejsu API faktur:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Interfejs API arkusza cen

W tej sekcji omówiono istniejące interfejsy API arkusza cen oraz zamieszczono zalecenia dotyczące przejścia na interfejs API arkusza cen dla umów klienta firmy Microsoft. Omówiono w niej również interfejs API arkusza cen dla umów klienta firmy Microsoft oraz objaśniono przeznaczenie pól w arkuszach cen. Interfejsy API [pobierania arkusza cen dla przedsiębiorstw](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) i [pobierania okresów rozliczeniowych dla przedsiębiorstw](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zostały zastąpione przez interfejs API arkusza cen dla umów klienta firmy Microsoft (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). Nowy interfejs API obsługuje zarówno format JSON, jak i CSV w formatach asynchronicznych REST. Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Interfejsy API rozliczeń dla przedsiębiorstw

Interfejsy API rozliczeń dla przedsiębiorstw były używane razem z rejestracjami Enterprise do uzyskiwania informacji o cenach i okresach rozliczeniowych. W ramach uwierzytelniania i autoryzacji były używane tokeny internetowe Azure Active Directory.

Aby uzyskać odpowiednie ceny dla określonej rejestracji Enterprise przy użyciu interfejsów API arkusza cen i okresu rozliczeniowego:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Interfejs API arkusza cen dla umów klienta firmy Microsoft

Interfejs API arkusza cen dla umów klienta firmy Microsoft umożliwia wyświetlanie cen za użycie wszystkich usług platformy Azure i witryny Marketplace. Ceny widoczne w profilu rozliczeniowym mają zastosowanie do wszystkich subskrypcji należących do tego profilu.

Interfejs API arkusza cen umożliwia wyświetlenie danych użycia wszystkich usług platformy Azure w formacie CSV:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Interfejs API arkusza cen umożliwia wyświetlenie danych użycia wszystkich usług platformy Azure w formacie JSON:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Użycie interfejsu API powoduje zwrócenie arkusza cen dla całego konta. Można jednak uzyskać skróconą wersję arkusza cen w formacie PDF. Podsumowanie obejmuje użycie usług platformy Azure i usług witryny Marketplace, które są rozliczane na określonej fakturze. Faktura jest identyfikowana przez właściwość {invoiceId}, która jest taka sama jak **numer faktury** widoczny w plikach PDF z podsumowaniem faktury. Oto przykład.

![Przykładowy obraz przedstawiający numer faktury odpowiadający właściwości InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Aby wyświetlić informacje o fakturze w formacie CSV przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić informacje o fakturze w formacie JSON przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Można również sprawdzić szacowane ceny za użycie dowolnej usługi platformy Azure lub witryny Marketplace w bieżącym otwartym cyklu rozliczeniowym albo okresie korzystania z usługi.

Aby wyświetlić szacowane ceny za użycie usług w formacie CSV przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Aby wyświetlić szacowane ceny za użycie usług w formacie JSON przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Interfejsy API arkusza cen umowy klienta firmy Microsoft to *asynchroniczne interfejsy API REST*. Odpowiedzi interfejsów API różnią się od odpowiedzi starszych, synchronicznych interfejsów API. Treść odpowiedzi interfejsu API również uległa zmianie.

#### <a name="old-response-body"></a>Stara treść odpowiedzi

Oto przykładowa synchroniczna odpowiedź interfejsu API REST:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nowa treść odpowiedzi

Interfejsy API obsługują [asynchroniczny format REST platformy Azure](../../azure-resource-manager/management/async-operations.md). Po wywołaniu interfejsu API za pomocą metody GET otrzymujemy następującą odpowiedź:

```
No Response Body

HTTP Status 202 Accepted
```

Następujące nagłówki zawierają lokalizację danych wyjściowych:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Wykonaj kolejne wywołanie metody GET do lokalizacji. Odpowiedź na wywołanie metody GET pozostaje taka sama, aż operacja osiągnie stan ukończenia lub niepowodzenia. Po zakończeniu odpowiedź na wywołanie metody GET lokalizacji zwraca adres URL pobierania — tak jak w przypadku wykonania operacji w tym samym czasie. Oto przykład:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Klient może również wykonać wywołanie metody GET dla nagłówka `Azure-AsyncOperation`. Punkt końcowy zwraca stan operacji.

W poniższej tabeli przedstawiono pola w starszym interfejsie API pobierania arkusza cen dla przedsiębiorstw. Tabela zawiera odpowiadające pola w nowym arkuszu cen dla umów klienta firmy Microsoft:

| Poprzednia właściwość | Nowa właściwość | Uwagi |
| --- | --- | --- |
| billingPeriodId  | _Nie dotyczy_ | Nie dotyczy. W przypadku umów klienta firmy Microsoft właściwość billingPeriodId została zastąpiona przez fakturę i skojarzony arkusz cen. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Dokładne wartości ciągów mogą się różnić. |
| includedQuantity  | includedQuantity | Nie dotyczy usług w umowach klienta firmy Microsoft. |
| partNumber  | _Nie dotyczy_ | Zamiast tego należy użyć kombinacji właściwości productOrderName (takiej samej jak offerID) i meterID. |
| unitPrice  | unitPrice | Cena jednostkowa ma zastosowanie do usług używanych w umowach klienta firmy Microsoft. |
| currencyCode  | pricingCurrency | Reprezentacja cen w umowach klienta firmy Microsoft obejmuje walutę cennika i walutę rozliczeniową. Właściwość currencyCode odpowiada właściwości pricingCurrency w umowach klienta firmy Microsoft. |
| offerID | productOrderName | Zamiast właściwości OfferID można użyć właściwości productOrderName, ale nie jest ona taka sama jak OfferID. Jednak właściwość productOrderName i miernik określają cenę w umowach klienta firmy Microsoft. Elementy te są powiązane z właściwościami meterId i OfferID w starszych rejestracjach. |

## <a name="consumption-price-sheet-api-operations"></a>Operacje interfejsu API arkusza cen za użycie

W umowach Enterprise Agreement były używane operacje interfejsu API [pobierania](/rest/api/consumption/pricesheet/get) i [pobierania według okresu rozliczeniowego](/rest/api/consumption/pricesheet/getbybillingperiod) arkusza cen za użycie. Zakresy tych operacji były ustalane za pomocą właściwości subscriptionId lub okresu rozliczeniowego. Interfejs API używa uwierzytelniania usługi Azure Resource Management.

Aby uzyskać informacje o arkuszu cen w określonym zakresie przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Aby uzyskać informacje o arkuszu cen według okresu rozliczeniowego przy użyciu interfejsu API arkusza cen:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Zamiast powyższych punktów końcowych interfejsu API w przypadku umów klienta firmy Microsoft należy używać następujących punktów końcowych:

**Interfejs API arkusza cen dla umów klienta firmy Microsoft (asynchroniczny interfejs API REST)**

Ten interfejs API jest przeznaczony dla umów klienta firmy Microsoft i udostępnia dodatkowe atrybuty.

**Arkusz cen dla zakresu profilu rozliczeń na koncie rozliczeniowym**

Jest to istniejący interfejs API. Został on zaktualizowany — dostarcza arkusza cen dla profilu rozliczeniowego na koncie rozliczeniowym.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Arkusz cen w określonym zakresie według konta rozliczeniowego

W przypadku uzyskiwania arkusza cen w zakresie rejestracji na koncie rozliczeniowym jest używane uwierzytelnianie usługi Azure Resource Manager.

Aby uzyskać arkusz cen na koncie rejestracji na koncie rozliczeniowym:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

W przypadku umowy klienta firmy Microsoft należy skorzystać z informacji podanych w poniższej sekcji. Zawiera ona właściwości pól używane w umowach klienta firmy Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Arkusz cen dla zakresu profilu rozliczeniowego na koncie rozliczeniowym

Zaktualizowany interfejs API arkusza cen według konta rozliczeniowego umożliwia pobieranie arkusza cen w formacie CSV. Aby uzyskać arkusz cen w zakresie profilu rozliczeniowego dla umowy MCA:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

W zakresie rejestracji umowy EA odpowiedź interfejsu API i właściwości są identyczne. Właściwości są zgodne z właściwościami umowy MCA.

Poniższa tabela przedstawia starsze właściwości [interfejsów API arkusza cen usługi Azure Resource Manager](/rest/api/consumption/pricesheet) oraz nowe właściwości, które są takie same.

| Stara właściwość interfejsu API arkusza cen usługi Azure Resource Manager  | Nowa właściwość interfejsu API arkusza cen umowy klienta firmy Microsoft   | Opis |
| --- | --- | --- |
| Meter ID | _meterId_ | Unikatowy identyfikator miernika. Taki sam jak właściwość meterID. |
| Nazwa miernika | meterName | Nazwa miernika. Miernik reprezentuje możliwy do wdrożenia zasób usługi platformy Azure. |
| Kategoria miernika  | usługa | Nazwa kategorii klasyfikacji dla miernika. Jest ona taka sama jak usługa w arkuszu cen umowy klienta firmy Microsoft. Dokładne wartości ciągów są różne. |
| Podkategoria miernika | meterSubCategory | Nazwa kategorii klasyfikacji podrzędnej miernika. Jest ona oparta na klasyfikacji rozróżniania zestawu funkcji wysokiego poziomu w usłudze. Przykładowa nazwa to podstawowa baza danych SQL lub standardowa baza danych SQL. |
| Region miernika | meterRegion | &nbsp;  |
| Jednostka | _Nie dotyczy_ | Można ją wyodrębnić z właściwości unitOfMeasure. |
| Jednostka miary | unitOfMeasure | &nbsp;  |
| Numer części | _Nie dotyczy_ | Zamiast numeru części należy użyć właściwości productOrderName i MeterID w celu jednoznacznego zidentyfikowania ceny dla profilu rozliczeniowego. Faktura MCA zawiera listę pól zamiast numeru części. |
| Cena jednostkowa | unitPrice | Cena jednostkowa w umowie klienta firmy Microsoft. |
| Kod waluty | pricingCurrency | Reprezentacja cen w umowach klienta firmy Microsoft obejmuje walutę cennika i walutę rozliczeniową. Kod waluty jest taki sam jak właściwość pricingCurrency w umowach klienta firmy Microsoft. |
| Uwzględniona ilość | includedQuantity | Nie dotyczy usług w umowach klienta firmy Microsoft. Wyświetlana z wartościami równymi zero. |
|  Identyfikator oferty  | productOrderName | Zamiast właściwości OfferID należy użyć właściwości productOrderName. Nie jest ona taka sama jak właściwość OfferID, ale właściwość productOrderName i miernik określają ceny w umowach klienta firmy Microsoft. Są one powiązane z właściwościami meterId i OfferID w starszych rejestracjach. |

Cena w umowach klienta firmy Microsoft jest definiowana inaczej niż w umowach Enterprise Agreement. Ceny usług w rejestracji Enterprise są unikatowe dla produktu, numeru części, miernika i oferty. Numer części nie jest używany w umowach klienta firmy Microsoft.

Cena za użycie usługi platformy Azure, która jest częścią umowy klienta firmy Microsoft, jest unikatowa dla właściwości productOrderName i meterID. Reprezentują one miernik usługi i plan produktu.

Aby uzgodnić arkusz cen i użycie w interfejsie API szczegółów użycia, można użyć właściwości productOrderName i meterID.

Arkusz cen mogą pobierać użytkownicy, którzy mają uprawnienia właściciela profilu rozliczeniowego, współautora, czytelnika lub menedżera faktury.

Arkusz cen zawiera ceny usług, które są rozliczane na podstawie użycia. Usługi obejmują użycie platformy Azure i witryny Marketplace. Ostatnia cena w każdym okresie korzystania z usługi jest blokowana i stosowana w pojedynczym okresie korzystania z usługi. W przypadku użycia usług platformy Azure okres korzystania z usługi zwykle odpowiada miesiącowi kalendarzowemu.

### <a name="retired-price-sheet-api-fields"></a>Wycofane pola interfejsu API arkusza cen

Następujące pola nie są dostępne w interfejsach API arkusza cen umowy klienta firmy Microsoft lub są one takie same.

|Wycofane pole| Opis|
|---|---|
| billingPeriodId | Nie dotyczy. Odpowiada właściwości InvoiceId w umowie MCA. |
| offerID | Nie dotyczy. Odpowiada właściwości productOrderName w umowie MCA. |
| meterCategory  | Nie dotyczy. Odpowiada usłudze w umowie MCA. |
| unit | Nie dotyczy. Można ją wyodrębnić z właściwości unitOfMeasure. |
| currencyCode | Taka sama jak właściwość pricingCurrency w umowie MCA. |
| meterLocation | Taka sama jak właściwość meterRegion w umowie MCA. |
| partNumber partnumber | Nie mają zastosowania, ponieważ numer części nie pojawia się na fakturach MCA. Zamiast numeru części należy użyć właściwości meterId i productOrderName do unikatowego identyfikowania cen. |
| totalIncludedQuantity | Nie dotyczy. |
| pretaxStandardRate  | Nie dotyczy. |

## <a name="reservation-instance-charge-api-replaced"></a>Interfejs API opłat za wystąpienia zarezerwowane został zamieniony

Transakcje rozliczeniowe za zakupy rezerwacji można uzyskać przy użyciu [interfejsu API opłat za wystąpienia zarezerwowane](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nowy interfejs API obejmuje wszystkie zakupy, w tym oferty innych firm w witrynie Marketplace. Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request). Interfejs API opłat za wystąpienia zarezerwowane został zastąpiony przez interfejs API transakcji.

Aby uzyskać informacje o transakcjach związanych z zakupami rezerwacji przy użyciu interfejsu API transakcji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Interfejsy API zaleceń zostały zamienione

Interfejsy API zaleceń dotyczących zakupów wystąpień zarezerwowanych udostępniają informacje o użyciu maszyn wirtualnych z ostatnich 7, 30 lub 60 dni. Interfejsy API udostępniają również zalecenia dotyczące zakupów rezerwacji. Obejmują one:

- [Interfejs API zaleceń dotyczących współdzielonych wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Interfejs API zaleceń dotyczących pojedynczego wystąpienia zarezerwowanego](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request). Wymienione wcześniej interfejsy API zaleceń dotyczących rezerwacji zostały zastąpione przez interfejs API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Aby uzyskać zalecenia dotyczące rezerwacji przy użyciu interfejsu API zaleceń dotyczących rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Interfejsy API użycia rezerwacji zostały zamienione

Informacje o użyciu rezerwacji w ramach rejestracji można uzyskać przy użyciu interfejsu API użycia wystąpień zarezerwowanych. Jeśli rejestracja zawiera więcej niż jedno wystąpienie zarezerwowane, ten interfejs API umożliwia również uzyskanie informacji o użyciu wszystkich zakupionych wystąpień zarezerwowanych.

Obejmują one:

- [Szczegóły Użycie wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Podsumowanie użycia wystąpień zarezerwowanych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Wszystkie interfejsy API użycia zostały zastąpione przez natywne interfejsy API platformy Azure, które używają usługi Azure AD do uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat wywoływania interfejsów API REST platformy Azure, zobacz [Wprowadzenie do interfejsu REST](/rest/api/azure/#create-the-request). Wymienione wcześniej interfejsy API zaleceń dotyczących rezerwacji zostały zastąpione przez interfejsy API [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) i [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Aby uzyskać szczegółowe informacje dotyczące rezerwacji przy użyciu interfejsu API szczegółów rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Aby uzyskać podsumowania rezerwacji przy użyciu interfejsu API podsumowań rezerwacji:

| Metoda | Identyfikator URI żądania |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Przechodzenie z platformy Cloudyn na usługę Cost Management

Organizacje korzystające z platformy [Cloudyn](https://cloudyn.com) mogą przejść na usługę [Azure Cost Management](https://azure.microsoft.com/services/cost-management/), która spełnia różne potrzeby związane z zarządzaniem kosztami. Usługa Cost Management jest dostępna w witrynie Azure Portal bez dołączania, a korzystanie z niej nie wymaga ośmiogodzinnego opóźnienia. Aby uzyskać więcej informacji, zobacz [Dokumentacja usługi Cost Management](../index.yml).

Dzięki usłudze Azure Cost Management możesz wykonywać następujące działania:

- Wyświetlaj koszty w czasie względem wstępnie zdefiniowanego budżetu. Analizuj dzienne wzorce kosztów, aby identyfikować nieprawidłowości w wydatkach i zapobiegać im. Dziel koszty według tagów, grup zasobów, usług i lokalizacji.
- Twórz budżety, ustawiając limity użycia oraz kosztów i otrzymuj powiadomienia w przypadku zbliżania się do ważnych wartości progowych. Konfiguruj automatyzację przy użyciu grup akcji, aby wyzwalać niestandardowe zdarzenia i wymuszać stałe limity zgodnie ze swoimi wymaganiami.
- Optymalizuj koszty i użycie, korzystając z rekomendacji usługi Azure Advisor. Odkrywaj możliwości optymalizacji zakupów dzięki rezerwacjom, zmniejszaj rozmiary niedostatecznie używanych maszyn wirtualnych i usuwaj nieużywane zasoby, aby zmieścić się w ramach budżetu.
- Planuj eksportowanie danych kosztów i użycia, aby codziennie publikować plik CSV na swoim koncie magazynu. Automatyzuj integrację z systemami zewnętrznymi w celu synchronizowania i aktualizowania danych dotyczących rozliczeń.

## <a name="power-bi-integration"></a>Integracja z usługą Power BI

Usługa Power BI również umożliwia raportowanie kosztów. [Łącznik usługi Azure Cost Management](/power-bi/desktop-connect-azure-cost-management) dla programu Power BI Desktop pozwala tworzyć zaawansowane, niestandardowe raporty, które pomagają uzyskać dokładniejszy wgląd w wydatki na platformę Azure. Łącznik usługi Azure Cost Management jest aktualnie dostępny dla klientów, którzy mają umowę klienta firmy Microsoft lub umowę Enterprise Agreement (EA).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak monitorować i kontrolować wydatki na platformę Azure, zapoznaj się z [dokumentacją usługi Cost Management](../index.yml). Usługa Cost Management umożliwia również optymalizowanie użycia zasobów.
