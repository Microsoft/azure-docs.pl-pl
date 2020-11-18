---
title: Dołączanie lub odłączanie galerii obrazów udostępnionych w Azure Lab Services | Microsoft Docs
description: W tym artykule opisano sposób dołączania udostępnionej galerii obrazów do laboratorium zajęć w Azure Lab Services.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: ae0870139d2320fa079f6705956e124f61479882
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660104"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Dołączanie lub odłączanie galerii obrazów udostępnionych w Azure Lab Services
W tym artykule opisano sposób dołączania lub odłączania udostępnionej galerii obrazów do konta laboratorium. 

> [!NOTE]
> Gdy [zapisujesz obraz szablonu laboratorium](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) w Azure Lab Services do galerii obrazów udostępnionych, obraz zostanie przekazany do galerii jako obraz wyspecjalizowany. [Wyspecjalizowane obrazy](../virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) przechowują informacje specyficzne dla maszyn i profile użytkowników. Nadal możesz bezpośrednio przekazać uogólniony obraz do galerii poza Azure Lab Services. 
>
> Twórca laboratorium może utworzyć maszynę wirtualną z szablonem w oparciu o uogólnione i wyspecjalizowane obrazy w Azure Lab Services. 

## <a name="scenarios"></a>Scenariusze
Oto kilka scenariuszy obsługiwanych przez tę funkcję: 

- Administrator konta laboratorium dołącza udostępnioną galerię obrazów do konta laboratorium i przekazuje obraz do galerii obrazów udostępnionych poza kontekstem laboratorium. Następnie twórcy laboratorium mogą używać tego obrazu z galerii obrazów udostępnionych do tworzenia laboratoriów. 
- Administrator konta laboratorium dołącza udostępnioną galerię obrazów do konta laboratorium. Twórca laboratorium (instruktor) zapisuje dostosowany obraz swojego laboratorium do galerii obrazów udostępnionych. Następnie inni Twórcy laboratorium mogą wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich laboratoriów. 

    Gdy obraz jest zapisywany w galerii obrazów udostępnionych, Azure Lab Services replikuje zapisany obraz do innych regionów dostępnych w tej samej lokalizacji [geograficznej](https://azure.microsoft.com/global-infrastructure/geographies/). Zapewnia, że obraz jest dostępny dla laboratoriów utworzonych w innych regionach w tej samej lokalizacji geograficznej. Zapisanie obrazów do udostępnionej galerii obrazów wiąże się z dodatkowym kosztem, który obejmuje koszt wszystkich zreplikowanych obrazów. Ten koszt jest oddzielony od kosztu użycia Azure Lab Services. Aby uzyskać więcej informacji na temat cen udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych — rozliczenia](../virtual-machines/windows/shared-image-galleries.md#billing).

> [!IMPORTANT]
> W przypadku korzystania z galerii obrazów udostępnionych Azure Lab Services obsługuje tylko obrazy z mniej niż 128 GB miejsca na dysku systemu operacyjnego. Obrazy zawierające więcej niż 128 GB miejsca na dysku lub wiele dysków nie będą wyświetlane na liście obrazów maszyn wirtualnych podczas tworzenia laboratorium.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfiguruj podczas tworzenia konta laboratorium
Podczas tworzenia konta laboratorium możesz dołączyć udostępnioną galerię obrazów do konta laboratorium. Możesz wybrać istniejącą galerię obrazów udostępnionych z listy rozwijanej lub utworzyć nową. Aby utworzyć i dołączyć udostępnioną galerię obrazów do konta laboratorium, wybierz pozycję **Utwórz nowy**, wprowadź nazwę galerii, a następnie wprowadź **OK**. 

![Konfigurowanie udostępnionej galerii obrazów w momencie tworzenia konta laboratorium](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfiguruj po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium można wykonać następujące zadania:

- Tworzenie i dołączanie galerii obrazów udostępnionych
- Dołącz udostępnioną galerię obrazów do konta laboratorium
- Odłączanie udostępnionej galerii obrazów z konta laboratorium

## <a name="create-and-attach-a-shared-image-gallery"></a>Tworzenie i dołączanie galerii obrazów udostępnionych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **usługi laboratoryjne** w sekcji **DEVOPS** . Jeśli wybierzesz pozycję gwiazdka ( `*` ) obok pozycji **usługi Lab Services**, zostanie ona dodana do sekcji **Ulubione** w menu po lewej stronie. Od następnego momentu wybierz pozycję **usługi laboratoryjne** w obszarze **Ulubione**.

    ![Wszystkie usługi — > usług Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wybierz swoje konto laboratorium, aby wyświetlić stronę **konto laboratorium** . 
4. Wybierz pozycję **Galeria obrazów udostępnionych** w menu po lewej stronie, a następnie wybierz pozycję **+ Utwórz** na pasku narzędzi.  

    ![Przycisk tworzenia galerii obrazów udostępnionych](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. W oknie **Tworzenie galerii obrazów udostępnionych** wprowadź **nazwę** galerii, a następnie wprowadź **OK**. 

    ![Utwórz okno galerii obrazów udostępnionych](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services tworzy galerię obrazów udostępnionych i dołączy ją do konta laboratorium. Wszystkie laboratoria utworzone w ramach tego konta laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    ![Galeria dołączonych obrazów](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    W dolnym okienku zobaczysz obrazy w galerii obrazów udostępnionych. W tej nowej galerii nie ma obrazów. Po przekazaniu obrazów do galerii są one widoczne na tej stronie.     

    Wszystkie obrazy z dołączonej udostępnionej galerii obrazów są domyślnie włączone. Można włączać lub wyłączać wybrane obrazy, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub **Wyłącz wybrane obrazy** .

## <a name="attach-an-existing-shared-image-gallery"></a>Dołączanie istniejącej galerii obrazów udostępnionych
Poniższa procedura przedstawia sposób dołączania istniejącej galerii obrazów udostępnionych do konta laboratorium. 

1. Na stronie **konto laboratorium** wybierz pozycję **Galeria obrazów udostępnionych** w menu po lewej stronie, a następnie wybierz pozycję **Dołącz** na pasku narzędzi. 

    ![Galeria obrazów udostępnionych — przycisk Dodaj](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na stronie **dołączanie istniejącej galerii obrazów udostępnionych** Wybierz galerię udostępnionych obrazów, a następnie wybierz **przycisk OK**.

    ![Wybierz istniejącą galerię](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobaczysz następujący ekran: 

    ![Moja Galeria na liście](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    W tym przykładzie nie ma jeszcze obrazów w galerii obrazów udostępnionych.

    Azure Lab Services tożsamość jest dodawana jako współautor do galerii obrazów udostępnionych, która jest dołączona do laboratorium. Umożliwia ona nauczycielom/administratorom IT zapisywanie obrazów maszyn wirtualnych w galerii obrazów udostępnionych. Wszystkie laboratoria utworzone w ramach tego konta laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    Wszystkie obrazy z dołączonej udostępnionej galerii obrazów są domyślnie włączone. Można włączać lub wyłączać wybrane obrazy, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub **Wyłącz wybrane obrazy** . 

## <a name="detach-a-shared-image-gallery"></a>Odłączanie galerii obrazów udostępnionych
Tylko jedna udostępniona Galeria obrazów może być dołączona do laboratorium. Jeśli chcesz dołączyć kolejną galerię obrazów udostępnionych, odłącz ją od bieżącego przed dołączeniem nowego. Aby odłączyć galerię obrazów udostępnionych z laboratorium, wybierz opcję **Odłącz** na pasku narzędzi i potwierdź operację odłączania. 

![Odłączanie galerii obrazów udostępnionych z konta laboratorium](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zapisać obraz laboratorium w galerii obrazów udostępnionych lub użyć obrazu z galerii obrazów udostępnionych, aby utworzyć maszynę wirtualną, zobacz [jak używać galerii obrazów udostępnionych](how-to-use-shared-image-gallery.md).

Aby uzyskać więcej informacji na temat ogólnie udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md).