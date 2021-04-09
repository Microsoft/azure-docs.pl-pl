---
title: Azure Monitor skoroszyty zapewniają własny magazyn
description: Dowiedz się, jak zabezpieczyć skoroszyt, zapisując zawartość skoroszytu w magazynie
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100618943"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Przenoszenie własnych magazynów w celu zapisywania skoroszytów

Istnieją sytuacje, w których może istnieć zapytanie lub pewna logika biznesowa, która ma być zabezpieczona. Skoroszyty udostępniają opcję Zabezpieczenia skoroszytów przez zapisanie skoroszytów zawartości w magazynie. Konto magazynu można następnie zaszyfrować przy użyciu kluczy zarządzanych przez firmę Microsoft lub można zarządzać szyfrowaniem, dostarczając własne klucze. [Zapoznaj się z dokumentacją platformy Azure na szyfrowanie usługi Storage.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Zapisywanie skoroszytu z tożsamościami zarządzanymi

1. Aby można było zapisać skoroszyt w magazynie, należy utworzyć zarządzaną tożsamość (wszystkie usługi — > zarządzane tożsamości) i udzielić `Storage Blob Data Contributor` dostępu do konta magazynu. [Zapoznaj się z dokumentacją platformy Azure dotyczącą zarządzanych tożsamości.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Zrzut ekranu przedstawiający Dodawanie przypisania roli](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Utwórz nowy skoroszyt.
3. Wybierz przycisk **Zapisz** , aby zapisać skoroszyt.
4. Istnieje opcja `Save content to an Azure Storage Account` , aby zapisać na koncie usługi Azure Storage.

    ![Zrzut ekranu przedstawiający zapisane okno dialogowe](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Wybierz żądane konto magazynu i kontener. Lista kont magazynu pochodzi z subskrypcji wybranej powyżej.

    ![Zrzut ekranu przedstawiający okno dialogowe zapisywania z opcją Storage](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Następnie wybierz pozycję **Zmień** , aby wybrać wcześniej utworzoną tożsamość zarządzaną.

    [![Zrzut ekranu przedstawiający okno dialogowe zmiany tożsamości](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Po wybraniu opcji magazynu naciśnij pozycję **Zapisz** , aby zapisać skoroszyt.

## <a name="limitations"></a>Ograniczenia

- Podczas zapisywania w magazynie niestandardowym nie można przypiąć poszczególnych części skoroszytu do pulpitu nawigacyjnego, ponieważ poszczególne numery PIN zawierają chronione informacje na pulpicie nawigacyjnym. W przypadku korzystania z magazynu niestandardowego można przypinać tylko linki do samego skoroszytu do pulpitów nawigacyjnych.
- Gdy skoroszyt zostanie zapisany w magazynie niestandardowym, będzie zawsze zapisywany w magazynie niestandardowym i nie można go wyłączyć. Aby zapisać w innym miejscu, możesz użyć "Save as" i wybrać opcję niezapisywania kopii w magazynie niestandardowym.
- Skoroszyty w zasobie Application Insights są skoroszytami "starsze" i nie obsługują magazynu niestandardowego. Najnowsze skoroszyty w Application Insights zasobów to "... Więcej "wyboru. Starsze skoroszyty nie mają opcji subskrypcji podczas zapisywania.

   ![Zrzut ekranu przedstawiający skoroszyt ze starszymi wersjami](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak utworzyć wizualizację [mapy](workbooks-map-visualizations.md) w skoroszytach.
- Dowiedz się, jak używać [grup w skoroszytach](../visualize/workbooks-groups.md).