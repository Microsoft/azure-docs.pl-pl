---
title: Microsoft Azure urządzenie Data Box wymagania systemowe | Microsoft Docs
description: Dowiedz się więcej o ważnych wymaganiach systemowych dla Azure Data Box i dla klientów łączących się z urządzenie Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767854"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe Azure Data Box

W tym artykule opisano ważne wymagania systemowe dotyczące urządzenie Data Box Microsoft Azure oraz klientów łączących się z urządzenie Data Box. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem urządzenie Data Box, a następnie odwoływanie się do niego w razie potrzeby podczas wdrażania i działania.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania:** W przypadku hostów łączących się z urządzenie Data Boxą opisano obsługiwane systemy operacyjne, protokoły transferu plików, konta magazynu, typy magazynów i przeglądarki dla lokalnego interfejsu użytkownika sieci Web.
* **Wymagania dotyczące sieci:** W urządzenie Data Box opisano wymagania dotyczące połączeń sieciowych i portów w celu optymalnej operacji urządzenie Data Box.


## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania obejmują obsługiwane systemy operacyjne, protokoły transferu plików, konta magazynu, typy magazynów i przeglądarki dla lokalnego interfejsu użytkownika sieci Web.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Obsługiwane protokoły transferu plików dla klientów

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Połączenia z udziałami urządzenie Data Boxymi nie są obsługiwane za pośrednictwem usługi REST dla zamówień eksportu. 

### <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci Web

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10-GbE nie jest dostępne, można użyć linku danych 1 GbE do skopiowania danych, ale ma to mieć wartość Szybkość kopiowania.

### <a name="port-requirements"></a>Wymagania dotyczące portów

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby zezwalać na ruch SMB lub NFS. W tej tabeli *w* (*przychodzący*) odnosi się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* (lub *wychodzący*) odnosi się do kierunku, w którym urządzenie urządzenie Data Box wysyła dane zewnętrznie, poza wdrożeniem: na przykład, ruch wychodzący do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Data Box](data-box-deploy-ordered.md)
