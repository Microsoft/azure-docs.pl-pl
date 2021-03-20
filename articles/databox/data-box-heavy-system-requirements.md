---
title: Microsoft Azure Data Box Heavy wymagania systemowe | Microsoft Docs
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84707756"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Wymagania systemowe Azure Data Box Heavy

W tym artykule opisano ważne wymagania systemowe dotyczące urządzenia Azure Data Box Heavy oraz klientów nawiązujących połączenie z urządzeniem. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem Data Box Heavy, a następnie odwoływanie się do niego w razie potrzeby podczas wdrażania i kolejnej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla hostów łączących się z Data Box Heavy** — opisuje obsługiwane platformy, przeglądarki dla lokalnego interfejsu użytkownika sieci Web, klientów protokołu SMB i dodatkowe wymagania dotyczące hostów, które mogą łączyć się z urządzenie Data Box.
* **Wymagania sieciowe dotyczące Data Box Heavy** — zawiera informacje o wymaganiach sieciowych dla optymalnej operacji urządzenia Data Box Heavy.

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania obejmują informacje w obsługiwanych systemach operacyjnych, obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web i klientów SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Obsługiwane systemy plików dla klientów z systemem Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci Web

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. W przypadku najszybszych szybkości kopiowania połączenia 2 40-GbE mogą być wykorzystywane równolegle (jeden na węzeł). Jeśli nie masz dostępnych 40 GbE, zalecamy korzystanie z co najmniej 2 10-GbE połączeń (jeden na węzeł).

### <a name="port-requirements"></a>Wymagania dotyczące portów

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby zezwalać na ruch SMB lub NFS. W tej tabeli *w programie lub w* *ruchu przychodzącym* odwołuje się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym urządzenie Data Box Heavy wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzące do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Data Box](data-box-deploy-ordered.md)
