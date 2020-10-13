---
title: Używanie galerii obrazów udostępnionych w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować konto laboratorium do korzystania z galerii obrazów udostępnionych, aby użytkownik mógł udostępnić obraz innym osobom, a inny użytkownik może użyć obrazu, aby utworzyć szablon maszyny wirtualnej w laboratorium.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 04e3764b095706d091bb72baaae77f5a4016fd28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052838"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Używanie galerii obrazów udostępnionych w Azure Lab Services
W tym artykule pokazano, jak wykładowcy/Administratorzy laboratorium mogą zapisywać obraz maszyny wirtualnej szablonu w [galerii obrazów udostępnionych](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) , aby mógł być używany przez inne osoby do tworzenia laboratoriów. 

> [!IMPORTANT]
> W przypadku korzystania z galerii obrazów udostępnionych Azure Lab Services obsługuje tylko obrazy z mniej niż 128 GB miejsca na dysku systemu operacyjnego. Obrazy zawierające więcej niż 128 GB miejsca na dysku lub wiele dysków nie będą wyświetlane na liście obrazów maszyn wirtualnych podczas tworzenia laboratorium.

## <a name="scenarios"></a>Scenariusze
Oto kilka scenariuszy obsługiwanych przez tę funkcję: 

- Administrator konta laboratorium dołącza udostępnioną galerię obrazów do konta laboratorium i przekazuje obraz do galerii obrazów udostępnionych poza kontekstem laboratorium. Następnie twórcy laboratorium mogą używać tego obrazu z galerii obrazów udostępnionych do tworzenia laboratoriów. 
- Administrator konta laboratorium dołącza udostępnioną galerię obrazów do konta laboratorium. Twórca laboratorium (instruktor) zapisuje dostosowany obraz swojego laboratorium do galerii obrazów udostępnionych. Następnie inni Twórcy laboratorium mogą wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich laboratoriów. 

    Gdy obraz jest zapisywany w galerii obrazów udostępnionych, Azure Lab Services replikuje zapisany obraz do innych regionów dostępnych w tej samej lokalizacji [geograficznej](https://azure.microsoft.com/global-infrastructure/geographies/). Zapewnia, że obraz jest dostępny dla laboratoriów utworzonych w innych regionach w tej samej lokalizacji geograficznej. Zapisanie obrazów do udostępnionej galerii obrazów wiąże się z dodatkowym kosztem, który obejmuje koszt wszystkich zreplikowanych obrazów. Ten koszt jest oddzielony od kosztu użycia Azure Lab Services. Aby uzyskać więcej informacji na temat cen udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych — rozliczenia]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz udostępnioną galerię obrazów przy użyciu [Azure PowerShell](../virtual-machines/windows/shared-images.md) lub [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/shared-images.md).
- Dołączono galerię obrazów udostępnionych do konta laboratorium. Instrukcje krok po kroku znajdują się w temacie [How to dołączanie lub odłączanie galerii obrazów udostępnionych](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Zapisywanie obrazu w galerii obrazów udostępnionych
Po dołączeniu udostępnionej galerii obrazów administrator konta laboratorium lub nauczycieli może zapisać obraz do galerii obrazów udostępnionych, aby można go było ponownie wykorzystać przez innych nauczycieli. 

1. Na stronie **szablon** laboratorium wybierz pozycję **Eksportuj do galerii obrazów udostępnionych** na pasku narzędzi.

    ![Przycisk zapisywania obrazu](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. W oknie dialogowym **Eksportuj do galerii obrazów udostępnionych** wprowadź **nazwę obrazu**, a następnie wybierz pozycję **Eksportuj**. 

    ![Okno dialogowe eksportowania do udostępnionej galerii obrazów](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Postęp tej operacji można zobaczyć na stronie **szablonu** . Ta operacja może trwać jakiś czas. 

    ![Eksportowanie w toku](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Po pomyślnym zakończeniu operacji eksportowania zostanie wyświetlony następujący komunikat:

    ![Eksportowanie zakończone](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Po zapisaniu obrazu do galerii obrazów udostępnionych możesz użyć tego obrazu z galerii podczas tworzenia kolejnego laboratorium. Możesz również przekazać obraz do galerii obrazów udostępnionych poza kontekstem laboratorium. Aby uzyskać więcej informacji, zobacz [Omówienie galerii obrazów udostępnionych](../virtual-machines/windows/shared-images.md). 

    > [!IMPORTANT]
    > Gdy [zapisujesz obraz szablonu laboratorium](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) w Azure Lab Services do galerii obrazów udostępnionych, obraz zostanie przekazany do galerii jako **obraz wyspecjalizowany**. [Wyspecjalizowane obrazy](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) przechowują informacje specyficzne dla maszyn i profile użytkowników. Nadal możesz bezpośrednio przekazać uogólniony obraz do galerii poza Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Korzystanie z obrazu z galerii obrazów udostępnionych
Nauczycieli może wybrać niestandardowy obraz dostępny w galerii obrazów udostępnionych dla szablonu podczas tworzenia nowego laboratorium.

![Korzystanie z obrazu maszyny wirtualnej z galerii](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Można utworzyć szablon maszyny wirtualnej na podstawie **uogólnionych** i **wyspecjalizowanych** obrazów w Azure Lab Services. 


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries).
