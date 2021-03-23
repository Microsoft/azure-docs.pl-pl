---
title: Zaoszczędź na dużych wystąpieniach platformy SAP HANA dzięki rezerwacji platformy Azure
description: Najważniejsze rzeczy, które trzeba wiedzieć przed zakupem rezerwacji dużego wystąpienia platformy HANA, oraz sposób dokonywania zakupu.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 579c7b5f66f52551e2504101406e1f7b2bb69e92
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775531"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Zaoszczędź na dużych wystąpieniach platformy SAP HANA dzięki rezerwacji platformy Azure

Możesz zaoszczędzić na kosztach dużych wystąpień platformy SAP HANA (HLI, HANA Large Instances), dokonując zakupu rezerwacji platformy Azure na 1 rok lub 3 lata w przedsprzedaży. Rabat za rezerwację jest stosowany do zaaprowizowanej jednostki SKU HLI, która jest zgodna z zakupionym wystąpieniem zarezerwowanym. Dzięki temu artykułowi poznasz najważniejsze rzeczy, które trzeba wiedzieć przed zakupem rezerwacji, oraz sposób dokonywania zakupu.

Kupując rezerwację, zobowiązujesz się do używania wystąpienia HLI przez 1 rok lub 3 lata. Zakup pojemności zarezerwowanej wystąpienia HLI obejmuje magazyn zasobów obliczeniowych i NFS, który znajduje się w pakiecie z jednostką SKU. Rezerwacja nie obejmuje kosztów licencjonowania oprogramowania, takiego jak system operacyjny, oprogramowanie SAP czy dodatkowe koszty magazynowania. Rabat za rezerwację jest automatycznie stosowany do zaaprowizowanego wystąpienia HLI SAP. Po zakończeniu obowiązywania rezerwacji do zaaprowizowanego zasobu stosowane są stawki płatności zgodnie z rzeczywistym użyciem.

## <a name="purchase-considerations"></a>Uwagi dotyczące zakupu

Przed przystąpieniem do zakupu pojemności zarezerwowanej należy zaaprowizować jednostkę SKU wystąpienia HLI. Rezerwacja jest płatna z góry lub w ratach miesięcznych. Obowiązują następujące ograniczenia dotyczące pojemności zarezerwowanej wystąpienia HLI:

- Rabaty za rezerwację mają zastosowanie tylko w przypadku subskrypcji w ramach umowy Enterprise Agreement i Umowy z Klientem Microsoft. Inne subskrypcje nie są obsługiwane.
- Elastyczność rozmiaru wystąpienia nie jest obsługiwana w przypadku pojemności zarezerwowanej wystąpienia HLI. Rezerwacja dotyczy tylko tej jednostki SKU i regionu, dla których ją zakupiono.
- Samoobsługowe anulowanie i wymiana nie są obsługiwane.
- Zakres pojemności zarezerwowanej jest pojedynczym zakresem, dlatego dotyczy pojedynczej subskrypcji i grupy zasobów. Zakupionej pojemności Nie można zaktualizować do użytku przez inną subskrypcję.
- Nie można mieć zakresu udostępnionego rezerwacji dla pojemności zarezerwowanej HANA. Zakresu rezerwacji nie można podzielić, scalić ani zaktualizować.
- Można kupić jedno wystąpienie HLI jednocześnie, korzystając z wywołań interfejsu API pojemności zarezerwowanych. Aby kupić więcej wystąpień, wykonaj dodatkowe wywołania interfejsu API.

Pojemność zarezerwowaną można kupić w witrynie Azure Portal lub przy użyciu interfejsu [API REST](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Kupowanie rezerwacji dużego wystąpienia HANA

Skorzystaj z poniższych informacji, aby kupić rezerwację wystąpienia HLI przy użyciu [interfejsów API REST zamówień rezerwacji](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Uzyskiwanie zamówienia rezerwacji oraz ceny

Najpierw uzyskaj zamówienie rezerwacji oraz i cenę dla zaaprowizowanej jednostki SKU dużego wystąpienia HANA przy użyciu interfejsu API [Oblicz cenę](/rest/api/reserved-vm-instances/reservationorder/calculate).

W poniższym przykładzie użyto klienta [armclient](https://github.com/projectkudu/ARMClient), aby wykonać wywołania interfejsu API REST przy użyciu programu PowerShell. Oto jak powinno wyglądać żądanie zamówienia rezerwacji i interfejsu API Oblicz cenę oraz treść żądania:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Aby uzyskać więcej informacji na temat pól danych i ich opisów, zobacz [Pola rezerwacji wystąpień HLI](#hli-reservation-fields).

Poniższa przykładowa odpowiedź jest podobna do zwracanych danych. Zwróć uwagę na wartość zwróconą dla elementu `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Dokonywanie zakupu

Dokonaj zakupu przy użyciu zwróconych elementów `quoteId` i `reservationOrderId`, które otrzymano w poprzedniej sekcji [Uzyskiwanie zamówienia rezerwacji oraz ceny](#get-the-reservation-order-and-price).

Oto przykładowe żądanie:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Oto przykładowa odpowiedź. Jeśli zamówienie zostało pomyślnie złożone, element `provisioningState` powinien mieć wartość `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Weryfikowanie powodzenia stanu zakupu

Uruchom żądanie GET zamówienia rezerwacji, aby wyświetlić stan zamówienia zakupu. Element `provisioningState` powinien mieć wartość `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Odpowiedź powinna przypominać następujący przykład.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Pola rezerwacji wystąpienia HLI

Poniższe informacje wyjaśniają znaczenie różnych pól rezerwacji.

  **Jednostka SKU** Nazwa jednostki SKU wystąpienia HLI. Wygląda następująco: `SAP_HANA_On_Azure_<SKUname>`.

  **Lokalizacja** Dostępne regiony wystąpienia HLI. Zobacz [Jednostki SKU dla platformy SAP HANA na platformie Azure (duże wystąpienia)](../../virtual-machines/workloads/sap/hana-available-skus.md), aby poznać dostępne regiony. Aby uzyskać format ciągu lokalizacji, użyj [wywołania interfejsu API Pobierz lokalizacje](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Typ zasobu zarezerwowanego** `SapHana`

  **Subskrypcja** Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Wymagany typ subskrypcji to Enterprise Agreement (numery ofert: MS-AZR-0017P or MS-AZR-0148P) lub Umowa z Klientem Microsoft. Opłaty są odliczane od salda opłaty z góry za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym), jeśli jest dostępne, lub naliczane jako nadwyżka.

  **Zakres** Zakres rezerwacji powinien być pojedynczym zakresem.

  **Okres** Jeden rok lub trzy lata. Wygląda następująco: `P1Y` lub `P3Y`.

  **Ilość** Liczba wystąpień zakupionych dla rezerwacji. Ilość do zakupu to pojedyncze wystąpienie HLI naraz. Aby kupić więcej rezerwacji, powtórz wywołanie interfejsu API z odpowiednimi polami.

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Po dokonaniu zakupu rezerwacji może wystąpić błąd podobny do następującego przykładu. Możliwą przyczyną jest to, że wystąpienie HLI nie zostało zaaprowizowane do zakupu. W takim przypadku należy skontaktować się z zespołem kont Microsoft w celu zaaprowizowania wystąpienia HLI przed próbą zakupu rezerwacji.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak wywoływać interfejsy API REST przy użyciu narzędzia Postman i cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Zobacz [Jednostki SKU dla platformy SAP HANA na platformie Azure (duże wystąpienia)](../../virtual-machines/workloads/sap/hana-available-skus.md), aby poznać listę dostępnych jednostek SKU i regionów.