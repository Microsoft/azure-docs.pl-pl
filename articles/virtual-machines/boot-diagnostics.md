---
title: Diagnostyka rozruchu platformy Azure
description: Omówienie diagnostyki rozruchu platformy Azure i zarządzanej diagnostyki rozruchu
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042976"
---
# <a name="azure-boot-diagnostics"></a>Diagnostyka rozruchu platformy Azure

Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu.

## <a name="boot-diagnostics-storage-account"></a>Konto magazynu diagnostyki rozruchu
Domyślnie Diagnostyka rozruchu jest włączona dla wszystkich maszyn wirtualnych utworzonych przy użyciu Azure Portal i wykorzystuje zarządzane konto magazynu. Przy użyciu zarządzanych kont magazynu użytkownicy uzyskują znaczącą poprawę czasu wdrożenia maszyny wirtualnej. Z tego powodu zalecamy klientom korzystanie z diagnostyki rozruchu z zarządzanym kontem magazynu dla wszystkich maszyn wirtualnych.

> [!NOTE]
> Klienci korzystający z platformy Azure nie będą obciążani opłatami za magazyn, gdy będziesz korzystać z diagnostyki rozruchu z zarządzanym kontem magazynu do 1 października 2020.

Alternatywna Obsługa diagnostyki rozruchu polega na użyciu niestandardowego konta magazynu. Użytkownik może utworzyć nowe konto magazynu lub użyć istniejącego. Aby uzyskać więcej informacji na temat niestandardowych kont magazynu, zobacz [Omówienie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania diagnostyki rozruchu":::

## <a name="boot-diagnostics-view"></a>Widok diagnostyki rozruchu
W bloku maszyna wirtualna opcja Diagnostyka rozruchu znajduje się w sekcji *Pomoc techniczna i rozwiązywanie problemów* w Azure Portal. Wybranie opcji Diagnostyka rozruchu spowoduje wyświetlenie zrzutu ekranu i informacji o dzienniku seryjnym. Dziennik seryjny zawiera komunikaty jądra, a zrzut ekranu jest migawką bieżącego stanu maszyn wirtualnych. Na podstawie tego, czy na maszynie wirtualnej jest uruchomiony system Windows lub Linux, decyduje o tym, jak będzie wyglądać oczekiwany zrzut ekranu. W przypadku systemu Windows użytkownicy zobaczą tło pulpitu i system Linux zobaczy monit logowania.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Zrzut ekranu przedstawiający diagnostykę rozruchu systemu Windows":::


## <a name="limitations"></a>Ograniczenia
- Diagnostyka rozruchu jest dostępna tylko dla maszyn wirtualnych Azure Resource Manager (ARM). 
- Diagnostyka rozruchu nie obsługuje kont usługi Premium Storage, jeśli konto magazynu w warstwie Premium jest używane na potrzeby diagnostyki rozruchu, `StorageAccountTypeNotSupported` podczas uruchamiania maszyny wirtualnej wystąpi błąd. 
- Konsola szeregowa Azure nie obsługuje obecnie zarządzanego konta magazynu na potrzeby diagnostyki rozruchu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konsoli szeregowej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) i sposobach [rozwiązywania problemów z maszynami wirtualnymi na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)przy użyciu diagnostyki rozruchu.
