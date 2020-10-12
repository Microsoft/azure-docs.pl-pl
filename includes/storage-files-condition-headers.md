---
title: plik dołączany
description: plik dołączany
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70737495"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Błąd ConditionHeadersNotSupported z aplikacji sieci Web przy użyciu Azure Files z przeglądarki

Wystąpił błąd ConditionHeadersNotSupported podczas uzyskiwania dostępu do zawartości hostowanej w Azure Files za pomocą aplikacji, która korzysta z nagłówków warunkowych, takich jak przeglądarka sieci Web, dostęp kończy się niepowodzeniem. Stan błędów, które nie są obsługiwane przez nagłówki warunku.

![Błąd Azure Files nagłówki warunkowe](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Przyczyna

Nagłówki warunkowe nie są jeszcze obsługiwane. Wdrażane aplikacje będą musiały zażądać pełnego pliku za każdym razem, gdy jest on dostępny.

### <a name="workaround"></a>Obejście

Po przekazaniu nowego pliku właściwość Cache-Control domyślnie ma wartość "no-cache". Aby wymusić żądanie pliku za każdym razem, należy zaktualizować Właściwość kontroli pamięci podręcznej pliku z "Brak pamięci podręcznej" do "Brak pamięci podręcznej, bez magazynu ani ponownie sprawdzić poprawności". Można to osiągnąć przy użyciu [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

![Modyfikacja pamięci podręcznej zawartości Eksploratora magazynu dla Azure Files nagłówki warunkowe](media/storage-files-condition-headers/storage-explorer-cache.png)