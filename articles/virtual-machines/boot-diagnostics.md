---
title: Diagnostyka rozruchu platformy Azure
description: Omówienie diagnostyki rozruchu platformy Azure i zarządzanej diagnostyki rozruchu
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 52a2b5e27cd5857416343e559237d08ea9a591be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972394"
---
# <a name="azure-boot-diagnostics"></a>Diagnostyka rozruchu platformy Azure

Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu.

## <a name="boot-diagnostics-storage-account"></a>Konto magazynu diagnostyki rozruchu
Podczas tworzenia maszyny wirtualnej w Azure Portal Diagnostyka rozruchu jest domyślnie włączona. Zalecanym działaniem diagnostyki rozruchu jest użycie zarządzanego konta magazynu, ponieważ zapewnia ono znaczną poprawę wydajności w czasie tworzenia maszyny wirtualnej platformy Azure. Jest to spowodowane tym, że używane jest konto magazynu zarządzanego przez platformę Azure, usuwając czas potrzebny do utworzenia nowego konta magazynu użytkownika w celu przechowywania danych diagnostycznych rozruchowych.

Alternatywna Obsługa diagnostyki rozruchu polega na użyciu konta magazynu zarządzanego przez użytkownika. Użytkownik może utworzyć nowe konto magazynu lub użyć istniejącego.

> [!IMPORTANT]
> Klienci platformy Azure nie będą obciążani kosztami magazynu związanymi z diagnostyką rozruchu przy użyciu zarządzanego konta magazynu do 2020 października.
>
> Obiekty BLOB danych diagnostyki rozruchu (które składają się z dzienników i obrazów migawek) są przechowywane na zarządzanym koncie magazynu. Klienci będą obciążani wyłącznie GiBs używanymi przez obiekty blob, a nie na dysku o nieobsługiwanym rozmiarze. Liczniki migawek zostaną użyte do rozliczania zarządzanego konta magazynu. Ze względu na to, że konta zarządzane są tworzone przy użyciu standardowej usługi LRS lub standardowej ZRS, w celu uzyskania rozmiaru obiektów BLOB danych diagnostycznych klienci będą obciążani za $0,05/GB miesięcznie. Aby uzyskać więcej informacji na temat tych cen, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Klienci będą widzieć tę opłatę powiązana z identyfikatorem URI zasobu maszyny wirtualnej. 

## <a name="boot-diagnostics-view"></a>Widok diagnostyki rozruchu
W bloku maszyna wirtualna opcja Diagnostyka rozruchu znajduje się w sekcji *Pomoc techniczna i rozwiązywanie problemów* w Azure Portal. Wybranie opcji Diagnostyka rozruchu spowoduje wyświetlenie zrzutu ekranu i informacji o dzienniku seryjnym. Dziennik seryjny zawiera komunikaty jądra, a zrzut ekranu jest migawką bieżącego stanu maszyn wirtualnych. Na podstawie tego, czy na maszynie wirtualnej jest uruchomiony system Windows lub Linux, decyduje o tym, jak będzie wyglądać oczekiwany zrzut ekranu. W przypadku systemu Windows użytkownicy zobaczą tło pulpitu i system Linux zobaczy monit logowania.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::


## <a name="limitations"></a>Ograniczenia
- Diagnostyka rozruchu jest dostępna tylko dla maszyn wirtualnych Azure Resource Manager. 
- Diagnostyka rozruchu nie obsługuje kont usługi Premium Storage, jeśli konto magazynu w warstwie Premium jest używane na potrzeby diagnostyki rozruchu, `StorageAccountTypeNotSupported` podczas uruchamiania maszyny wirtualnej wystąpi błąd. 
- Zarządzane konta magazynu są obsługiwane w Menedżer zasobów interfejsie API w wersji "2020-06-01" i nowszych.
- Usługa Azure serial Console jest obecnie niezgodna z zarządzanym kontem magazynu na potrzeby diagnostyki rozruchu. Dowiedz się więcej o [usłudze Azure serial Console](./troubleshooting/serial-console-overview.md).
- Diagnostyka rozruchu przy użyciu konta zarządzania magazynu może być obecnie stosowana tylko w Azure Portal. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konsoli szeregowej platformy Azure](./troubleshooting/serial-console-overview.md) i sposobach [rozwiązywania problemów z maszynami wirtualnymi na platformie Azure](./troubleshooting/boot-diagnostics.md)przy użyciu diagnostyki rozruchu.