---
title: Microsoft Azure urządzenie Data Box wymagania systemowe | Microsoft Docs
description: Dowiedz się więcej o ważnych wymaganiach systemowych dla Azure Data Box i dla klientów łączących się z urządzenie Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 85a0ce20f1d8e5a7e943efc088c19a8ad1912fc1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706042"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe Azure Data Box

W tym artykule opisano ważne wymagania systemowe dotyczące urządzenie Data Box Microsoft Azure oraz klientów łączących się z urządzenie Data Box. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem urządzenie Data Box, a następnie odwoływanie się do niego, gdy zachodzi taka potrzeba podczas wdrażania i działania.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania:** W przypadku hostów, które łączą się z urządzenie Data Box, opisano obsługiwane systemy operacyjne, protokoły transferu plików, konta magazynu, typy magazynów i przeglądarki dla lokalnego interfejsu użytkownika sieci Web.
* **Wymagania dotyczące sieci:** Urządzenie Data Box zawiera opis wymagań dotyczących połączeń sieciowych i portów w celu uzyskania najlepszej operacji urządzenie Data Box.


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

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zalecamy, aby masz co najmniej 1 10-GbE połączenie. Jeśli połączenie 10-GbE nie jest dostępne, można użyć linku danych 1 GbE do kopiowania danych, ale ma to zastosowanie do szybkości kopiowania.

### <a name="port-requirements"></a>Wymagania dotyczące portów

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby zezwalać na ruch SMB lub NFS. W tej tabeli *w* (*przychodzący*) odnosi się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* (lub *wychodzący*) odnosi się do kierunku, w którym urządzenie urządzenie Data Box wysyła dane zewnętrznie poza wdrożeniem. Na przykład dane mogą być wychodzące do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Data Box](data-box-deploy-ordered.md)
