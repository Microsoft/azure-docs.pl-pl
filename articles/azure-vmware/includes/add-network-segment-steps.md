---
title: Dodaj segment sieci NSX-T
description: Kroki umożliwiające dodanie segmentu sieci NSX-T dla rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335059"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. W Menedżerze NSX-T wybierz pozycję **Networking**  >  **segmenty** sieci, a następnie wybierz pozycję **Dodaj segment**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania nowego segmentu":::

1. Wybierz pozycję **Dodaj segment** i wprowadź nazwę segmentu.

1. Wybierz pozycję Brama pomoc (TNTxx-T1) jako **połączoną bramę** i pozostaw **Typ** jako elastyczny.

1. Wybierz wstępnie skonfigurowaną **strefę transportu** nakładki (TNTXX-nałóż-lt), a następnie wybierz pozycję **Ustaw podsieci**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Ustaw nazwę segmentu, połączoną bramę i typ, a następnie wybierz pozycję Ustaw podsieć.":::

1. Wprowadź adres IP bramy, a następnie wybierz pozycję **Dodaj**. 

   >[!IMPORTANT]
   >Adres IP musi znajdować się w nienakładających się blokach adresów RFC1918, co zapewnia połączenie z maszynami wirtualnymi w nowym segmencie.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Ustaw adres IP bramy dla nowego segmentu, a następnie wybierz pozycję Dodaj.":::

1. Wybierz pozycję **Zastosuj** , a następnie **Zapisz**.

1. Wybierz pozycję **nie** , aby odrzucić opcję kontynuowania konfigurowania segmentu. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Odrzuć, aby skonfigurować nowo utworzony segment sieci, wybierając pozycję nie.":::

1. Potwierdź obecność nowego segmentu sieci. W tym przykładzie **ls01** jest nowym segmentem sieci.

   1. W Menedżerze NSX-T wybierz pozycję **Networking**  >  **segmenty** sieci. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Upewnij się, że nowy segment sieci jest obecny w NSX-T.":::

   1. W programie vCenter wybierz pozycję **sieć > SDDC — centrum** danych.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Upewnij się, że nowy segment sieci jest obecny w programie vCenter.":::