---
title: Diagnostyka rozruchu platformy Azure
description: Omówienie diagnostyki rozruchu platformy Azure i zarządzanej diagnostyki rozruchu
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067090"
---
# <a name="azure-boot-diagnostics"></a>Diagnostyka rozruchu platformy Azure

Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu.

## <a name="boot-diagnostics-view"></a>Widok diagnostyki rozruchu
W bloku maszyna wirtualna opcja Diagnostyka rozruchu znajduje się w sekcji *Pomoc techniczna i rozwiązywanie problemów* w Azure Portal. Wybranie opcji Diagnostyka rozruchu spowoduje wyświetlenie zrzutu ekranu i informacji o dzienniku seryjnym. Dziennik seryjny zawiera komunikaty jądra, a zrzut ekranu jest migawką bieżącego stanu maszyn wirtualnych. Na podstawie tego, czy na maszynie wirtualnej jest uruchomiony system Windows lub Linux, decyduje o tym, jak będzie wyglądać oczekiwany zrzut ekranu. W przypadku systemu Windows użytkownicy zobaczą tło pulpitu i system Linux zobaczy monit logowania.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Zrzut ekranu przedstawiający diagnostykę rozruchu systemu Windows":::


## <a name="limitations"></a>Ograniczenia
- Diagnostyka rozruchu jest dostępna tylko dla maszyn wirtualnych Azure Resource Manager. 
- Diagnostyka rozruchu nie obsługuje kont usługi Premium Storage, jeśli konto magazynu w warstwie Premium jest używane na potrzeby diagnostyki rozruchu, `StorageAccountTypeNotSupported` podczas uruchamiania maszyny wirtualnej wystąpi błąd. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konsoli szeregowej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) i sposobach [rozwiązywania problemów z maszynami wirtualnymi na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)przy użyciu diagnostyki rozruchu.
