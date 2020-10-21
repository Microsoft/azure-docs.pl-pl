---
title: Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft
description: Dowiedz się, jak sprawdzić saldo środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: cf5772e9cf08eaa7f34acd59a9c96da6f20cd03d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131415"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft

Saldo środków na korzystanie z platformy Azure dla Twojego konta rozliczeniowego związanego z umową klienta firmy Microsoft możesz sprawdzić w witrynie Azure Portal lub za pomocą interfejsów API REST.

Na koncie rozliczeniowym dla umowy klienta firmy Microsoft środki są przypisywane do profilu rozliczeniowego. Każdy profil rozliczeniowy ma własne środki, które są automatycznie stosowane do opłat na jego fakturze. Aby wyświetlić saldo środków na korzystanie z platformy Azure dla profilu rozliczeniowego, musisz mieć rolę właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego albo właściciela, współautora lub czytelnika dla konta rozliczeniowego. Aby dowiedzieć się więcej na temat ról, zobacz [Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure](understand-mca-roles.md).

> [!NOTE]
> Może upłynąć do 24 godzin, zanim nowe środki pojawią się w witrynie Azure Portal. Jeśli po otrzymaniu nowych środków nie widać ich w portalu, poczekaj 24 godziny na ich wyświetlenie.

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Sprawdzanie salda środków

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie narzędzia Cost Management + Billing w portalu.](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Na stronie zakresów rozliczeniowych wybierz konto rozliczeniowe, dla którego chcesz śledzić saldo środków. Typ konta rozliczeniowego to **umowa klienta firmy Microsoft**.

    ![Zrzut ekranu przedstawiający zakresy rozliczeniowe.](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Witryna Azure Portal zapamiętuje ostatni zakres rozliczeniowy, do którego uzyskujesz dostęp, i wyświetla go przy następnym wejściu na stronę Zarządzanie kosztami i rozliczenia. Strona zakresów rozliczeniowych nie zostanie wyświetlona, jeśli wcześniej była odwiedzana strona Zarządzanie kosztami i rozliczenia. W takim przypadku sprawdź, czy jesteś we [właściwym zakresie](#check-access-to-a-microsoft-customer-agreement). W przeciwnym razie [przełącz zakres](view-all-accounts.md#switch-billing-scope-in-the-azure-portal), aby wybrać konto rozliczeniowe dla umowy klienta firmy Microsoft.

3. Po lewej stronie wybierz pozycję **Formy płatności**, a następnie wybierz pozycję **Środki na korzystanie z systemu Azure**.

   ![Zrzut ekranu przedstawiający szacowane i bieżące salda.](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Na stronie środków na korzystanie z platformy Azure znajdują się następujące sekcje:

   #### <a name="balance"></a>Saldo

   Sekcja salda zawiera podsumowanie salda środków na korzystanie z platformy Azure.

   ![Zrzut ekranu przedstawiający saldo środków dla profilu rozliczeniowego](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Okres               | Definicja                           |
   |--------------------|--------------------------------------------------------|
   | Szacowane saldo  | Szacowana kwota środków po uwzględnieniu wszystkich rozliczonych i oczekujących transakcji |
   | Bieżące saldo    | Kwota środków od ostatniej faktury. Nie obejmuje ona oczekujących transakcji |

   Gdy szacowane saldo spadnie do 0, rozpocznie się naliczanie opłat za używanie wszystkich produktów, również tych, których użycie jest rozliczane za pomocą środków.

   #### <a name="credits-list"></a>Lista środków

   Sekcja listy środków zawiera listę środków na korzystanie z platformy Azure.

   ![Zrzut ekranu przedstawiający listę środków dla profilu rozliczeniowego](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Okres | Definicja |
   |---|---|
   | Element źródłowy | Źródło pozyskiwania środków |
   | Data rozpoczęcia | Data nabycia środków |
   | Data wygaśnięcia | Data wygaśnięcia środków |
   | Bieżące saldo | Saldo od ostatniej faktury |
   | Pierwotna kwota | Pierwotna kwota środków |
   | Stan | Bieżący stan środków. Stan może mieć wartość: aktywne, użyte, wygasłe lub wygasające |

   #### <a name="transactions"></a>Transakcje

   W sekcji transakcji są wyświetlane wszystkie transakcje, które wpłynęły na saldo środków.

   ![Zrzut ekranu przedstawiający transakcje środków w profilu rozliczeniowym](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Okres | Definicja |
   |---|---|
   | Data transakcji | Data przeprowadzona transakcji |
   | Opis | Opis transakcji |
   | Kwota| Kwota transakcji |
   | Saldo | Saldo po transakcji |

    > [!NOTE]
    >
    > Jeśli na stronie form płatności nie widzisz środków na korzystanie z platformy Azure, nie masz środków lub nie został wybrany odpowiedni zakres. Wybierz konto rozliczeniowe zawierające środki lub jeden z jego profilów rozliczeniowych. Aby dowiedzieć się, jak zmieniać zakresy, zobacz [Przełączanie zakresów rozliczeniowych w witrynie Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Jeśli wyświetlasz środki na korzystanie z platformy Azure w zakresie konta rozliczeniowego, a konto rozliczeniowe ma więcej niż jeden profil rozliczeniowy, na stronie środków na korzystanie z platformy Azure zostanie wyświetlona tabela zawierająca zestawienie środków na korzystanie z platformy Azure dla każdego profilu rozliczeniowego. Wybierz profil rozliczeniowy z listy, wybierz formy płatności, a następnie środki na korzystanie z platformy Azure, aby wyświetlić szczegóły dotyczące profilu rozliczeniowego.

    ![Zrzut ekranu przedstawiający listę środków dla konta rozliczeniowego](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest)

Aby programowo uzyskać saldo środków na koncie rozliczeniowym, możesz użyć interfejsów API [rozliczeń](/rest/api/billing/) i [użycia](/rest/api/consumption/) platformy Azure.

W przykładach przedstawionych poniżej używane są interfejsy API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-billing-profiles-you-have-access-to"></a>Znajdowanie profilu rozliczeniowego, do którego ma się dostęp

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zwraca listę kont rozliczeniowych i ich profilów rozliczeniowych.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Właściwość `displayName` profilu rozliczeniowego służy do identyfikowania profilu rozliczeniowego, dla którego chce się sprawdzić saldo środków. Skopiuj właściwość `id` profilu rozliczeniowego. Jeśli na przykład chcesz sprawdzić saldo środków dla profilu rozliczeniowego **Development**, skopiujesz wartość ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="get-azure-credit-balance"></a>Pobieranie salda środków na korzystanie z platformy Azure

Wykonaj poniższe żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną w pierwszym kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Odpowiedź interfejsu API zwraca szacowane i bieżące saldo dla profilu rozliczeniowego.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nazwa elementu  | Opis                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Szacowana kwota środków po uwzględnieniu wszystkich rozliczonych i oczekujących transakcji. |
| `currentBalance`   | Kwota środków od ostatniej faktury. Nie obejmuje ona oczekujących transakcji.    |
| `pendingCreditAdjustments`      | Korekty, takie jak zwroty, które nie zostały jeszcze zafakturowane.  |
| `expiredCredit`      |  Środki, które wygasły od ostatniej faktury.  |
| `pendingEligibleCharges`  | Opłaty możliwe do pokrycia ze środków, które nie zostały jeszcze zafakturowane.   |

### <a name="get-list-of-credits"></a>Pobieranie listy środków

Wykonaj poniższe żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną w pierwszym kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Odpowiedź interfejsu API zwraca listy środków na korzystanie z platformy Azure dla profilu rozliczeniowego.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nazwa elementu  | Opis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Pierwotna kwota środków. |
| `closedBalance`   | Saldo od ostatniej faktury.    |
| `source`      | Źródło, które definiuje, kto i jak uzyskał środki. |
| `startDate`      |  Data aktywowania środków.  |
| `expirationDate`  | Data wygaśnięcia środków.   |
| `poNumber`  | Numer zamówienia zakupu faktury, na której rozliczono środki.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Pobieranie transakcji, które wpłynęły na saldo środków

Wykonaj poniższe żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną w pierwszym kroku (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Aby pobrać transakcje dla określonego przedziału czasu, trzeba będzie przekazać wartości **startDate**  i **endDate**.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Odpowiedź interfejsu API zwraca wszystkie transakcje, które wpłynęły na saldo środków dla Twojego profilu rozliczeniowego.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nazwa elementu  | Opis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Data przeprowadzona transakcji. |
| `description` | Opis transakcji. |
| `adjustments`   | Korekty środków dla transakcji.    |
| `creditExpired`      | Kwota środków, która wygasła. |
| `charges`      |  Opłaty za transakcję.  |
| `closedBalance`  | Saldo po transakcji.   |
| `eventType`  | Typ transakcji.   |
| `invoiceNumber`  | Numer faktury, na której rozliczono transakcję. Dla oczekującej transakcji będzie on pusty.   |

---

## <a name="how-credits-are-used"></a>Sposób używania środków

Konto rozliczeniowe umowy klienta firmy Microsoft zawiera profile rozliczeniowe, które umożliwiają zarządzanie fakturami i formami płatności. W każdym profilu rozliczeniowym co miesiąc jest generowana faktura, którą opłaca się przy użyciu wybranych form płatności.

Nabyte środki przypisujesz do profilu rozliczeniowego. Podczas generowania faktury dla profilu rozliczeniowego środki są automatycznie stosowane do łącznych opłat w celu obliczenia kwoty, którą trzeba zapłacić. Pozostałą kwotę trzeba zapłacić za pomocą wybranej formy płatności, takiej jak czek/przelew lub karta kredytowa.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty nieobjęte środkami na korzystanie z platformy Azure

 Następujące produkty nie są objęte środkami na korzystanie z platformy Azure. Za ich używanie są naliczane opłaty — niezależnie od salda środków:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)
- Produkty z witryny Azure Marketplace
- Plany pomocy technicznej platformy Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta rozliczeniowego umowy klienta firmy Microsoft](../understand/mca-overview.md)
- [Omówienie terminów na fakturze za umowę klienta firmy Microsoft](../understand/mca-understand-your-invoice.md)