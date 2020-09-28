---
title: Poprawki na platformie Azure API Management | Microsoft Docs
description: Dowiedz się więcej o koncepcji poprawek w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403242"
---
# <a name="revisions-in-azure-api-management"></a>Poprawki w usłudze Azure API Management

Poprawki umożliwiają wprowadzanie zmian w interfejsach API w sposób kontrolowany i bezpieczny. Jeśli chcesz wprowadzić zmiany, Utwórz nową poprawkę. Następnie można edytować i testować interfejs API bez zakłócania pracy odbiorców interfejsu API. Gdy wszystko będzie gotowe, dokonaj aktualnej poprawki. W tym samym czasie można opcjonalnie ogłosić wpis w dzienniku zmian, aby zapewnić aktualność swoich klientów interfejsu API, co uległo zmianie. Dziennik zmian jest publikowany w portalu dla deweloperów.

> [!NOTE]
> Portal dla deweloperów nie jest dostępny w warstwie zużycia.

Za pomocą poprawek można:

- Bezpiecznie Wprowadzaj zmiany w definicjach i zasadach interfejsu API, bez zakłócania pracy z interfejsem API produkcji.
- Wypróbuj zmiany przed opublikowaniem.
- Udokumentowanie wprowadzonych zmian, aby deweloperzy mogli zrozumieć, co nowego.
- Wycofaj, jeśli znajdziesz problemy.

[Rozpocznij pracę z poprawkami, postępując zgodnie z naszym przewodnikiem.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Uzyskiwanie dostępu do określonych poprawek

Do każdej poprawki interfejsu API można uzyskać dostęp za pomocą specjalnie uformowanego adresu URL. Dołącz `;rev={revisionNumber}` na końcu adresu URL interfejsu API, ale przed ciągiem zapytania, aby uzyskać dostęp do określonej poprawki tego interfejsu API. Na przykład możesz użyć tego adresu URL, aby uzyskać dostęp do poprawki 3 `customers` interfejsu API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Domyślnie każda poprawka ma te same ustawienia zabezpieczeń co bieżąca wersja. Można celowo zmienić zasady dla określonej poprawki, jeśli chcesz mieć różne zabezpieczenia dla każdej poprawki. Na przykład możesz chcieć dodać [zasady filtrowania adresów IP](./api-management-access-restriction-policies.md#RestrictCallerIPs) , aby uniemożliwić zewnętrznym obiektom wywołującym dostęp do poprawki, która jest nadal w fazie projektowania.

Poprawka może zostać przełączona w tryb offline, co sprawia, że jest ona niedostępna dla obiektów wywołujących, nawet jeśli próbują uzyskać dostęp do poprawki za pomocą adresu URL. Poprawkę można oznaczyć jako offline przy użyciu Azure Portal. Jeśli używasz programu PowerShell, możesz użyć `Set-AzApiManagementApiRevision` polecenia cmdlet i ustawić `Path` argument na `$null` .

> [!NOTE]
> Zalecamy przełączenie poprawek do trybu offline, gdy nie są używane do testowania.

## <a name="current-revision"></a>Bieżąca wersja

Pojedynczą poprawkę można ustawić jako *bieżącą* poprawkę. Ta wersja będzie używana dla wszystkich żądań interfejsu API, które nie określają jawnego numeru poprawki w adresie URL. Możesz przywrócić poprzednią wersję, ustawiając tę poprawkę jako aktualną.

Poprawkę można ustawić jako bieżącą przy użyciu Azure Portal. Jeśli używasz programu PowerShell, możesz użyć `New-AzApiManagementApiRelease` polecenia cmdlet.

## <a name="revision-descriptions"></a>Opisy poprawek

Podczas tworzenia poprawki można ustawić opis dla własnych celów śledzenia. Opisy nie są odtwarzane użytkownikom interfejsu API.

Po ustawieniu poprawki jako bieżąca można także opcjonalnie określić publiczny dziennik zmian. Dziennik zmian jest dostępny w portalu dla deweloperów, który umożliwia użytkownikom wyświetlanie interfejsu API. Notatkę dziennika zmian można zmodyfikować przy użyciu `Update-AzApiManagementApiRelease` polecenia cmdlet programu PowerShell.

## <a name="versions-and-revisions"></a>Wersje i poprawki

Wersje i poprawki są odrębnymi funkcjami. Każda wersja może mieć wiele poprawek, podobnie jak interfejs API bez wersji. Można używać poprawek bez używania wersji lub w inny sposób. Zwykle wersje są używane do oddzielania wersji interfejsu API z istotnymi zmianami, podczas gdy poprawki mogą być używane dla drobnych i nieistotnych zmian w interfejsie API.

Należy sprawdzić, czy poprawka zawiera istotne zmiany, lub jeśli chcesz formalnie przekształcić poprawkę w wersję beta/testową, możesz utworzyć wersję z poprawki. Za pomocą Azure Portal kliknij pozycję "Utwórz wersję z poprawki" w menu kontekstowym poprawek na karcie poprawki.
