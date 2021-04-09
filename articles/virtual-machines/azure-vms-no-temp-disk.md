---
title: Często zadawane pytania dotyczące rozmiarów maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Microsoft Azure rozmiarów maszyn wirtualnych, które nie mają lokalnego dysku tymczasowego.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.subservice: sizes
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: bd4dcbdc7ab13d18ef7f2d7102c56d1bd8d8758d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582106"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego 
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozmiarów maszyn wirtualnych platformy Azure, które nie mają lokalnego dysku tymczasowego (tj. braku lokalnego dysku tymczasowego). Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, zobacz [specyfikacje dla serii Dv4 i Dsv4 (ogólnego przeznaczenia obciążeń)](dv4-dsv4-series.md) lub [specyfikacji dla Ev4 i Esv4 (obciążenia zoptymalizowane pod kątem pamięci)](ev4-esv4-series.md).

## <a name="what-does-no-local-temp-disk-mean"></a>Co oznacza brak lokalnego dysku tymczasowego? 
Tradycyjnie mamy rozmiary maszyn wirtualnych (np. Standard_D2s_v3, Standard_E48_v3), które obejmują niewielki dysk lokalny (tj. dysk D:). Teraz przy użyciu nowych rozmiarów maszyn wirtualnych ten mały dysk lokalny już nie istnieje; można jednak nadal dołączać HDD w warstwie Standardowa, SSD w warstwie Premium lub SSD w warstwie Ultra.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>Co zrobić, jeśli nadal chcę mieć lokalny dysk tymczasowy?
Jeśli obciążenie wymaga lokalnego dysku tymczasowego, dostępne są również nowe rozmiary maszyn wirtualnych [Ddv4 i Ddsv4](ddv4-ddsv4-series.md) lub [Edv4 i Edsv4](edv4-edsv4-series.md) . Te rozmiary oferują 50% większego dysku tymczasowego w porównaniu z poprzednimi wersjami v3.

> [!NOTE]
> Lokalny dysk tymczasowy nie jest trwały; Aby upewnić się, że dane są trwałe, użyj opcji HDD w warstwie Standardowa, SSD w warstwie Premium lub SSD w warstwie Ultra. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Jakie są różnice między nowymi rozmiarami maszyn wirtualnych a Ogólnego przeznaczenia Dv3/Dsv3 lub rozmiarem maszyny wirtualnej zoptymalizowanej pod kątem pamięci EV3/Esv3? 
| rodziny maszyn wirtualnych v3 z lokalnym dyskiem tymczasowym   | Nowe rodziny v4 z lokalnym dyskiem tymczasowym | Nowe rodziny v4 bez lokalnego dysku tymczasowego |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| EV3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Czy można zmienić rozmiar maszyny wirtualnej, która ma lokalny dysk tymczasowy do rozmiaru maszyny wirtualnej bez lokalnego dysku tymczasowego?  
Nie. Jedyne kombinacje dozwolone do zmiany rozmiarów to: 

1. Maszyna wirtualna (z lokalnym dyskiem tymczasowym) — > VM (z lokalnym dyskiem tymczasowym); lub 
2. Maszyna wirtualna (bez lokalnego dysku tymczasowego) — > VM (bez lokalnego dysku tymczasowego). 

Jeśli chcesz obejść, zapoznaj się z następnym pytaniem.

> [!NOTE]
> Jeśli obraz zależy od dysku zasobu lub plik stronicowania lub swapfile istnieje na lokalnym dysku tymczasowym, obrazy bezdyskowe nie będą działały — zamiast tego użyj alternatywy "z dyskiem". 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Jak mogę przeprowadzić migrację z rozmiaru maszyny wirtualnej z lokalnym dyskiem tymczasowym do rozmiaru maszyny wirtualnej bez lokalnego dysku tymczasowego?  
Można przeprowadzić migrację, wykonując następujące czynności: 

1. Nawiąż połączenie z maszyną wirtualną, która ma lokalny dysk tymczasowy (na przykład dysk D:) jako administrator lokalny.
2. Postępuj zgodnie z zaleceniami w sekcji "tymczasowe przenoszenie pagefile.sys do dysku C" w temacie [Używanie dysku D: jako dysku danych na maszynie wirtualnej z systemem Windows](./windows/change-drive-letter.md) , aby przenieść plik stronicowania z lokalnego dysku tymczasowego (D: Drive) do dysku C:.

   > [!NOTE]
   > Postępuj zgodnie z zaleceniami w sekcji "tymczasowe przenoszenie pagefile.sys do dysku C" w temacie Używanie dysku D: jako dysku danych na maszynie wirtualnej z systemem Windows, aby przenieść plik stronicowania z lokalnego dysku tymczasowego (D: Drive) do dysku C: Disk. **Odróżnienie od pokreślonych kroków spowoduje wyświetlenie komunikatu o błędzie — "nie można zmienić rozmiaru maszyny wirtualnej, ponieważ zmiana z dysku zasobów na dysk nienależący do zasobów nie jest dozwolona, a na odwrót nie jest dozwolone.**

3. Wykonaj migawkę maszyny wirtualnej, wykonując czynności opisane w temacie [Tworzenie migawki przy użyciu portalu lub interfejsu wiersza polecenia platformy Azure](./linux/snapshot-copy-managed-disk.md). 
4. Użyj migawki, aby utworzyć nową maszynę wirtualną bezdyskową (na przykład Dv4, Dsv4, Ev4, Seria Esv4), wykonując kroki opisane w temacie [Tworzenie maszyny wirtualnej na podstawie migawki przy użyciu interfejsu wiersza polecenia](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot). 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Czy te rozmiary maszyn wirtualnych obsługują systemy operacyjne Linux i Windows (OS)?
Tak.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Czy spowoduje to przerwanie moich skryptów niestandardowych, obrazów niestandardowych lub obrazów systemu operacyjnego, które mają pliki lub pliki stronicowania na lokalnym dysku tymczasowym?
Jeśli obraz niestandardowego systemu operacyjnego wskazuje na lokalny dysk tymczasowy, może to oznaczać, że obraz nie będzie działał prawidłowo z tym rozmiarem bez dysku.

## <a name="have-questions-or-feedback"></a>Masz pytania lub uwagi?
Wypełnij [formularz opinii]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Następne kroki 
W tym dokumencie przedstawiono więcej informacji na temat najczęściej występujących pytań dotyczących maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego. Aby uzyskać więcej informacji o tych rozmiarach maszyn wirtualnych, zobacz następujące artykuły:

- [Specyfikacje dla serii Dv4 i Dsv4 (Ogólnego przeznaczenia obciążenie)](dv4-dsv4-series.md)
- [Specyfikacje dla serii Ev4 i Esv4 (obciążenie zoptymalizowane pod kątem pamięci)](ev4-esv4-series.md)