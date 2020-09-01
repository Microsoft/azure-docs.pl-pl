---
title: Diagnostyka rozruchu platformy Azure
description: Omówienie diagnostyki rozruchu platformy Azure i zarządzanej diagnostyki rozruchu
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257759"
---
# <a name="azure-boot-diagnostics"></a>Diagnostyka rozruchu platformy Azure

Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu.

## <a name="boot-diagnostics-storage-account"></a>Konto magazynu diagnostyki rozruchu
Podczas tworzenia maszyny wirtualnej w Azure Portal Diagnostyka rozruchu jest domyślnie włączona. Zalecanym działaniem diagnostyki rozruchu jest użycie zarządzanego konta magazynu, ponieważ zapewnia ono znaczną poprawę wydajności w czasie tworzenia maszyny wirtualnej platformy Azure. Jest to spowodowane tym, że używane jest konto magazynu zarządzanego przez platformę Azure, usuwając czas potrzebny do utworzenia nowego konta magazynu użytkownika w celu przechowywania danych diagnostycznych rozruchowych.

Alternatywna Obsługa diagnostyki rozruchu polega na użyciu konta magazynu zarządzanego przez użytkownika. Użytkownik może utworzyć nowe konto magazynu lub użyć istniejącego.

> [!IMPORTANT]
> Klienci platformy Azure nie będą obciążani kosztami usługi Storage associted z użyciem diagnostyki rozruchu przy użyciu zarządzanego konta magazynu do października 2020.

## <a name="boot-diagnostics-view"></a>Widok diagnostyki rozruchu
W bloku maszyna wirtualna opcja Diagnostyka rozruchu znajduje się w sekcji *Pomoc techniczna i rozwiązywanie problemów* w Azure Portal. Wybranie opcji Diagnostyka rozruchu spowoduje wyświetlenie zrzutu ekranu i informacji o dzienniku seryjnym. Dziennik seryjny zawiera komunikaty jądra, a zrzut ekranu jest migawką bieżącego stanu maszyn wirtualnych. Na podstawie tego, czy na maszynie wirtualnej jest uruchomiony system Windows lub Linux, decyduje o tym, jak będzie wyglądać oczekiwany zrzut ekranu. W przypadku systemu Windows użytkownicy zobaczą tło pulpitu i system Linux zobaczy monit logowania.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Zrzut ekranu przedstawiający diagnostykę rozruchu systemu Windows":::


## <a name="limitations"></a>Ograniczenia
- Diagnostyka rozruchu jest dostępna tylko dla maszyn wirtualnych Azure Resource Manager. 
- Diagnostyka rozruchu nie obsługuje kont usługi Premium Storage, jeśli konto magazynu w warstwie Premium jest używane na potrzeby diagnostyki rozruchu, `StorageAccountTypeNotSupported` podczas uruchamiania maszyny wirtualnej wystąpi błąd. 
- Usługa Azure serial Console jest obecnie niezgodna z zarządzanym kontem magazynu na potrzeby diagnostyki rozruchu. Dowiedz się więcej o [usłudze Azure serial Console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konsoli szeregowej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) i sposobach [rozwiązywania problemów z maszynami wirtualnymi na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)przy użyciu diagnostyki rozruchu.
