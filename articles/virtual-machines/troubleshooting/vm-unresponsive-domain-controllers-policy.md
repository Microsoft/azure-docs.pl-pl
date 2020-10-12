---
title: Maszyna wirtualna nie odpowiada podczas stosowania domyślnych zasad kontrolerów domeny
titlesuffix: Azure Virtual Machines
description: W tym artykule przedstawiono kroki rozwiązywania problemów, w których domyślne zasady kontrolerów domeny uniemożliwiają rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 53e1daca47a2917a19cbc30db5348e4fcc06b325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90039152"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>Maszyna wirtualna nie odpowiada podczas stosowania domyślnych zasad kontrolerów domeny

Ten artykuł zawiera kroki rozwiązywania problemów, w których domyślne zasady kontrolerów domeny uniemożliwiają rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla system operacyjny, który nie odpowiada, podczas rozruchu przy użyciu **domyślnych zasad kontrolerów domeny**komunikatów.

  ![Rysunek 1 wyświetla system operacyjny zablokowany przy użyciu komunikatu "domyślne zasady kontrolerów domeny"](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być Niedawne wprowadzenie zmian w domyślnych zasadach kontrolerów domeny. W przeciwnym razie analiza pliku zrzutu pamięci będzie musiała zostać wykonana w celu określenia głównej przyczyny.

## <a name="solution"></a>Rozwiązanie

Jeśli niedawno wprowadzono zmiany w domyślnych zasadach kontrolerów domeny, można cofnąć te zmiany, aby rozwiązać problem. Jeśli nie masz pewności, co jest przyczyną problemu, Zbierz zrzut pamięci, a następnie Prześlij bilet pomocy technicznej.

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii, a następnie skontaktować się z pomocą techniczną przy użyciu pliku zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , aby przygotować maszynę wirtualną naprawy.

1. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na stronie Naprawa maszyny wirtualnej przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera dysku przypisana do dołączonego dysku systemu operacyjnego to `F`, należy przejść do folderu `F:\Windows`.

1. Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.

1. Jeśli masz problemy z lokalizowaniem pliku Memory. dmp, możesz zamiast tego użyć [wywołań przerwań, które nie są maskowane (NMI) w konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Postępuj zgodnie z przewodnikiem, aby [wygenerować plik zrzutu awaryjnego za pomocą wywołań NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).
