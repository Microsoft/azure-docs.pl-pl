---
title: Poprawki w usłudze Azure API Management | Microsoft Docs
description: Dowiedz się więcej o koncepcji poprawek w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812142"
---
# <a name="revisions-in-azure-api-management"></a>Poprawki w usłudze Azure API Management

Poprawki umożliwiają zmianę interfejsów API w kontrolowany i bezpieczny sposób. Jeśli chcesz wprowadzić zmiany, utwórz nową poprawkę. Następnie możesz edytować i testować interfejs API bez zakłócania pracy użytkowników interfejsu API. Gdy wszystko będzie gotowe, należy wprowadzić poprawkę jako bieżącą. W tym samym czasie możesz opcjonalnie opublikować wpis w dzienniku zmian, aby zapewnić, że użytkownicy interfejsu API będą na bieżąco z tym, co się zmieniło. Dziennik zmian jest publikowany w portalu dla deweloperów.

> [!NOTE]
> Portal dla deweloperów nie jest dostępny w warstwie Zużycie.

Za pomocą poprawek można:

- Bezpiecznie wprowadzaj zmiany w definicjach i zasadach interfejsu API bez zakłócania działania produkcyjnego interfejsu API.
- Wypróbuj zmiany przed ich opublikowaniem.
- Udokumentuj wprowadzone zmiany, aby deweloperzy zrozumieli, co nowego.
- Wycofaj się, jeśli znajdziesz problemy.

[Wprowadzenie do poprawek, korzystając z naszego przewodnika.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Uzyskiwanie dostępu do określonych poprawek

Dostęp do każdej poprawki interfejsu API można uzyskać przy użyciu specjalnie utworzonego adresu URL. Dołącz ciąg na końcu adresu URL interfejsu API, ale przed ciągiem zapytania, aby uzyskać dostęp `;rev={revisionNumber}` do określonej poprawki tego interfejsu API. Na przykład możesz użyć tego adresu URL, aby uzyskać dostęp do poprawki 3 interfejsu `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Domyślnie każda poprawka ma te same ustawienia zabezpieczeń co bieżąca poprawka. Możesz celowo zmienić zasady dla określonej poprawki, jeśli chcesz zastosować różne zabezpieczenia dla każdej poprawki. Można na przykład dodać zasady filtrowania [adresów IP,](./api-management-access-restriction-policies.md#RestrictCallerIPs) aby uniemożliwić zewnętrznym wywołującym dostęp do poprawki, która jest nadal w trakcie opracowywania.

Poprawka może zostać prze offline, co sprawia, że jest ona niedostępna dla wywołujących, nawet jeśli próbują uzyskać dostęp do poprawki za pośrednictwem jej adresu URL. Możesz oznaczyć poprawkę jako wersję w trybie offline przy użyciu Azure Portal. Jeśli używasz programu PowerShell, możesz użyć `Set-AzApiManagementApiRevision` polecenia cmdlet i ustawić `Path` argument na `$null` .

> [!NOTE]
> Zalecamy przesłonienie poprawek do trybu offline, jeśli nie są one do testowania.

## <a name="current-revision"></a>Bieżąca poprawka

Pojedynczą poprawkę można ustawić jako *bieżącą wersję.* Ta poprawka będzie używana dla wszystkich żądań interfejsu API, które nie określają jawnego numeru poprawki w adresie URL. Możesz wycofać poprzednią poprawkę, ustawiając ją jako bieżącą.

Poprawkę można ustawić jako bieżącą przy użyciu Azure Portal. Jeśli używasz programu PowerShell, możesz użyć `New-AzApiManagementApiRelease` polecenia cmdlet .

## <a name="revision-descriptions"></a>Opisy poprawek

Podczas tworzenia poprawki można ustawić opis dla własnych celów śledzenia. Opisy nie są odtwarzane dla użytkowników interfejsu API.

Jeśli ustawisz poprawkę jako bieżącą, możesz również opcjonalnie określić publiczną notatkę dziennika zmian. Dziennik zmian jest zawarty w portalu dla deweloperów, który użytkownicy interfejsu API mogą wyświetlać. Uwaga dziennika zmian można zmodyfikować przy użyciu polecenia `Update-AzApiManagementApiRelease` cmdlet programu PowerShell.

## <a name="versions-and-revisions"></a>Wersje i poprawki

Wersje i poprawki są odrębnymi cechami. Każda wersja może mieć wiele poprawek, podobnie jak interfejs API bez wersji. Poprawek można używać bez używania wersji lub w drugą stronę. Zazwyczaj wersje są używane do oddzielania wersji interfejsu API ze zmianami przerywania, a poprawki mogą być używane w przypadku drobnych i niepowiązywistych zmian interfejsu API.

Jeśli okaże się, że poprawka zawiera zmiany przerywane lub jeśli chcesz formalnie przekształcić poprawkę w wersję beta/testową, możesz utworzyć wersję z poprawki. Przy użyciu Azure Portal kliknij pozycję "Utwórz wersję z poprawki" w menu kontekstowym poprawki na karcie Poprawki.
