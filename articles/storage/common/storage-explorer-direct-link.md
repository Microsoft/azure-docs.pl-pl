---
title: Link bezpośredni Eksplorator usługi Azure Storage | Microsoft Docs
description: Dokumentacja linku Eksplorator usługi Azure Storage Direct
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582334"
---
# <a name="azure-storage-explorer-direct-link"></a>Link bezpośredni Eksplorator usługi Azure Storage

W systemach Windows i macOS Eksplorator usługi Storage obsługuje identyfikatory URI przy użyciu `storageexplorer://` protokołu. Te identyfikatory URI są określane jako linki bezpośrednie. Bezpośredni link wskazuje na zasób usługi Azure Storage w Eksplorator usługi Storage. Po wyświetleniu linku bezpośredniego zostanie otwarty Eksplorator usługi Storage i przejdź do zasobu, na który wskazuje. W tym artykule opisano, jak działają linki bezpośrednie i jak można ich używać.

## <a name="how-direct-links-work"></a>Jak działają linki bezpośrednie

Eksplorator usługi Storage używa widoku drzewa do wizualizacji zasobów na platformie Azure. Link bezpośredni zawiera informacje hierarchiczne dla węzła połączonego zasobu w drzewie. Po nadaniu bezpośredniego linku Eksplorator usługi Storage otwiera i odbiera parametry w linku bezpośrednim. Eksplorator usługi Storage następnie używa tych parametrów do przejścia do połączonego zasobu w widoku drzewa.

> [!IMPORTANT]
> Użytkownik musi być zalogowany i mieć uprawnienia niezbędne do uzyskania dostępu do połączonego zasobu, aby można było uzyskać bezpośredni link do pracy.

## <a name="parameters"></a>Parametry

Link bezpośredni Eksplorator usługi Storage zawsze zaczyna się od protokołu `storageexplorer://` . W poniższej tabeli objaśniono wszystkie możliwe parametry w linku bezpośrednim.

Parametr | Opis
:---------| :---------
`v`         | Wersja protokołu linku bezpośredniego.
`accountid` | Identyfikator zasobu Azure Resource Manager konta magazynu dla połączonego zasobu. Jeśli połączony zasób jest kontem magazynu, ten identyfikator będzie Azure Resource Manager IDENTYFIKATORem zasobu dla tego konta magazynu. W przeciwnym razie ten identyfikator będzie Azure Resource Manager IDENTYFIKATORem zasobu konta magazynu, do którego należy połączony zasób.
`resourcetype` | Opcjonalny. Używany tylko wtedy, gdy połączony zasób jest kontenerem obiektów blob, udziałem plików, kolejką lub tabelą. Musi to być jeden z "Azure. BlobContainer", "Azure. Executable", "Azure. queue", "Azure. Executable".
`resourcename` | Opcjonalny. Używany tylko wtedy, gdy połączony zasób jest kontenerem obiektów blob, udziałem plików, kolejką lub tabelą. Nazwa połączonego zasobu.

Oto przykład bezpośredniego linku do kontenera obiektów BLOB. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Pobierz łącze bezpośrednie z Eksplorator usługi Storage

Za pomocą Eksplorator usługi Storage można uzyskać bezpośredni link do zasobu. Otwórz menu kontekstowe węzła dla zasobu w widoku drzewa. Następnie użyj akcji "Kopiuj link bezpośredni", aby skopiować jej bezpośredni link do Schowka.

## <a name="direct-link-limitations"></a>Bezpośrednie ograniczenia linków

Linki bezpośrednie są obsługiwane tylko dla zasobów w węzłach subskrypcji. Ponadto obsługiwane są tylko następujące typy zasobów:

- Konta magazynu
- Kontenery obiektów blob
- Kolejki
- Udziały plików
- tabelami
