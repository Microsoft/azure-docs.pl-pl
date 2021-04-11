---
title: Elastyczność rozmiaru maszyny wirtualnej — Azure Reserved VM Instances
description: Dowiedz się, w jaki sposób seria rozmiarów ma zastosowanie rabat rezerwacji w przypadku wystąpienia zarezerwowanych maszyn wirtualnych.
author: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: yashar
ms.openlocfilehash: a6ddcef1493f15442a723bcc93850e6197db84d8
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285602"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Elastyczność rozmiaru maszyny wirtualnej w usłudze Reserved VM Instances

Podczas zakupu wystąpienia zarezerwowanego maszyny wirtualnej można zoptymalizować pod kątem elastyczności rozmiaru wystąpienia lub priorytetu pojemności. Aby uzyskać więcej informacji na temat ustawiania lub zmieniania ustawienia optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych, zobacz [Zmiana ustawienia optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

W przypadku wystąpienia zarezerwowanej maszyny wirtualnej, która jest zoptymalizowana pod kątem elastyczności rozmiaru wystąpienia, rezerwacja kupowana może być stosowana do rozmiarów maszyn wirtualnych w tej samej elastycznej grupie o rozmiarze. Jeśli na przykład zakupisz rezerwację dla rozmiaru maszyny wirtualnej, która jest wymieniona w serii DSv2, jak Standard_DS3_v2, Rabat rezerwacji może dotyczyć innych rozmiarów, które są wymienione w tej samej elastycznej grupie o rozmiarze wystąpienia:

- Standardowa_DS1_v2
- Standard_DS2_v2
- Standardowa_DS3_v2
- Standardowa_DS4_v2

Jednak rabat rezerwacji nie ma zastosowania do rozmiarów maszyn wirtualnych, które są wymienione w różnych elastycznych rozmiarach o rozmiarze wystąpienia, takich jak jednostki SKU w dużej pamięci serii DSv2: Standard_DS11_v2, Standard_DS12_v2 i tak dalej.

W grupie elastycznej rozmiaru wystąpienia liczba maszyn wirtualnych, których dotyczy rabat rezerwacji, zależy od rozmiaru maszyny wirtualnej, który został wybrany podczas zakupu rezerwacji. Zależy to również od rozmiaru maszyn wirtualnych, które są uruchomione. Kolumna współczynnik porównuje względne rozmiary dla każdego rozmiaru maszyny wirtualnej w grupie elastycznej rozmiaru tego wystąpienia. Użyj wartości współczynnika, aby obliczyć, w jaki sposób rabat rezerwacji ma zastosowanie do maszyn wirtualnych, które są uruchomione.

## <a name="examples"></a>Przykłady

W poniższych przykładach używane są rozmiary i wskaźniki w tabeli serii DSv2.

Zakup wystąpienia zarezerwowanego maszyny wirtualnej o rozmiarze Standard_DS4_v2, gdzie stosunek lub względna wartość w porównaniu z innymi rozmiarami w tej serii wynosi 8.

- Scenariusz 1. Uruchamianie ośmiu Standard_DS1_v2 rozmiaru maszyn wirtualnych o współczynniku 1. Rabat związany z rezerwacją dotyczy wszystkich ośmiu z tych maszyn wirtualnych.
- Scenariusz 2. uruchomienie dwóch maszyn wirtualnych o Standard_DS2_v2 rozmiarze z zastosowaniem współczynnika 2 dla każdego z nich. Uruchom również maszynę wirtualną o rozmiarze Standard_DS3_v2 o współczynniku 4. Całkowite rozmiary to 2 + 2 + 4 = 8. W związku z tym rabat dla rezerwacji ma zastosowanie do wszystkich trzech tych maszyn wirtualnych.
- Scenariusz 3: uruchomienie jednego Standard_DS5_v2 z stosunkiem 16. Rabat związany z rezerwacją dotyczy połowy kosztu obliczeniowego maszyny wirtualnej.
- Scenariusz 4: uruchomienie jednego Standard_DS5_v2 z stosunkiem 16 i zakupem dodatkowej rezerwacji Standard_DS4_v2 z stosunkiem 8. Obie rezerwacje łączą i stosują rabat dla całej maszyny wirtualnej.

W poniższych sekcjach przedstawiono, jakie rozmiary znajdują się w tej samej grupie serii rozmiarów, gdy kupowane jest wystąpienie zarezerwowane maszyny wirtualnej zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Stopień elastyczności rozmiaru wystąpienia maszyn wirtualnych 

Poniższy wolumin CSV ma elastyczne grupy o rozmiarze wystąpienia, ArmSkuName i współczynniki.  

[Współczynniki elastyczności rozmiaru wystąpienia](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Platforma Azure utrzymuje zaktualizowany link i schemat, aby można było użyć pliku programowo.

## <a name="view-vm-size-recommendations"></a>Wyświetlanie zaleceń dotyczących rozmiaru maszyny wirtualnej

Na platformie Azure są wyświetlane zalecenia dotyczące rozmiaru maszyny wirtualnej w środowisku zakupu. Aby wyświetlić najmniejsze zalecenia dotyczące rozmiaru, wybierz pozycję **Grupuj według najmniejszego rozmiaru**.

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="Zrzut ekranu przedstawiający zalecane ilości." lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [co to są Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md).
