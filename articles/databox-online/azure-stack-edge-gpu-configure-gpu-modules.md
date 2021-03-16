---
title: Uruchamianie modułu GPU na urządzeniu Microsoft Azure Stack Edge z procesorem GPU Pro | Microsoft Docs
description: Opisuje sposób konfigurowania i uruchamiania modułu na procesorach GPU na urządzeniu z systemem Azure Stack brzeg Pro za pośrednictwem Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 348ddff56ed61cd608d6b9f28417e7cd4c4e6b13
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563967"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Konfigurowanie i uruchamianie modułu na procesorach GPU na Azure Stack na urządzeniu brzegowym Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Urządzenie Azure Stack EDGE Pro zawiera jedną lub więcej jednostek przetwarzania grafiki (GPU). Procesory GPU są popularnym wyborem w przypadku obliczeń dla sztucznej inteligencji, ponieważ oferują funkcje przetwarzania równoległego i są szybsze przy renderowaniu obrazu niż centralne jednostki obliczeniowe (CPU). Aby uzyskać więcej informacji na temat procesora GPU znajdującego się na urządzeniu Azure Stack brzeg Pro, przejdź do pozycji [Azure Stack EDGE Pro techniczne urządzenia](azure-stack-edge-gpu-technical-specifications-compliance.md)techniczne.

W tym artykule opisano sposób konfigurowania i uruchamiania modułu na procesorze GPU na urządzeniu Azure Stack EDGE Pro. W tym artykule opisano użycie publicznie dostępnych **cyfr** modułu kontenera przeznaczonych dla procesorów GPU firmy NVIDIA. Tej procedury można użyć do skonfigurowania innych modułów opublikowanych przez NVIDIA dla tych procesorów GPU.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do procesora GPU z włączonym 1 węzłem Azure Stack Edge na urządzeniu. To urządzenie jest aktywowane przy użyciu zasobu na platformie Azure.  

## <a name="configure-module-to-use-gpu"></a>Konfigurowanie modułu do użycia procesora GPU

Aby skonfigurować moduł do korzystania z procesora GPU na urządzeniu Azure Stack brzeg Pro w celu uruchomienia modułu,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> wykonaj te kroki.

1. W Azure Portal przejdź do zasobu skojarzonego z urządzeniem.

2. W obszarze **Przegląd** wybierz pozycję **IoT Edge**.

    ![Konfigurowanie modułu do użycia procesora GPU 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. W obszarze **Włączanie usługi IoT Edge** wybierz pozycję **Dodaj**.

   ![Konfigurowanie modułu do korzystania z procesora GPU 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. W obszarze **Tworzenie usługi IoT Edge** wprowadź ustawienia dla zasobu IoT Hub:

   |Pole   |Wartość    |
   |--------|---------|
   |Subskrypcja      | Subskrypcja używana przez zasób Azure Stack Edge. |
   |Grupa zasobów    | Grupa zasobów używana przez zasób Azure Stack Edge. |
   |Usługa IoT Hub           | Wybierz opcję **Utwórz nową** lub **Użyj istniejącej**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, które są używane przez zasób usługi Azure Stack Edge.     |
   |Nazwa              | Jeśli nie chcesz używać domyślnej nazwy podanej dla nowego zasobu IoT Hub, wprowadź inną nazwę. |

   Po zakończeniu ustawień wybierz pozycję **Przegląd + Utwórz**. Sprawdź ustawienia zasobu IoT Hub i wybierz pozycję **Utwórz**.

   ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   Tworzenie zasobów dla zasobu IoT Hub trwa kilka minut. Po utworzeniu zasobu **Przegląd** wskazuje, że usługa IoT Edge jest teraz uruchomiona.

   ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. Aby upewnić się, że skonfigurowano rolę obliczeń brzegowych, wybierz pozycję **Właściwości**.

   ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. W obszarze **Właściwości** wybierz łącze **IoT Edge urządzenia**.

   ![Konfigurowanie modułu do użycia procesora GPU 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   W okienku po prawej stronie zostanie wyświetlone urządzenie IoT Edge skojarzone z urządzeniem Azure Stack EDGE Pro. To urządzenie odpowiada urządzeniu IoT Edge, które zostało utworzone podczas tworzenia zasobu IoT Hub.
 
7. Wybierz to urządzenie IoT Edge.

   ![Konfigurowanie modułu do korzystania z procesora GPU 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. Wybierz pozycję **Ustaw moduły**.

   ![Konfigurowanie modułu do użycia procesora GPU 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. Wybierz pozycję **+ Dodaj** , a następnie pozycję **+ moduł IoT Edge**. 

    ![Konfigurowanie modułu do korzystania z procesora GPU 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. Na karcie **Dodaj moduł IoT Edge** :

    1. Podaj **Identyfikator URI obrazu**. W tym miejscu będziesz używać publicznie dostępnych **cyfr** modułu NVIDIA. 
    
    2. Ustaw **zasady ponownego uruchamiania** na **zawsze**.
    
    3. Ustaw **żądany stan** na **uruchomiony**.
    
    ![Konfigurowanie modułu do użycia procesora GPU 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. Na karcie **zmienne środowiskowe** Podaj nazwę zmiennej i odpowiednią wartość. 

    1. Aby bieżący moduł używał jednego procesora GPU na tym urządzeniu, użyj NVIDIA_VISIBLE_DEVICES. 

    2. Ustaw wartość na 0 lub 1. Wartość 0 lub 1 zapewnia, że na urządzeniu jest używany co najmniej jeden procesor GPU dla tego modułu. Ustawienie wartości 0, 1 oznacza, że w tym module są używane procesory GPU na urządzeniu.

       ![Konfigurowanie modułu do korzystania z procesora GPU 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Aby uzyskać więcej informacji na temat zmiennych środowiskowych, których można użyć z procesorem GPU NVIDIA, przejdź do [środowiska uruchomieniowego kontenera NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Moduł może korzystać z jednego lub bez procesorów GPU.

12. Wprowadź nazwę modułu. W tym momencie możesz wybrać opcję tworzenia kontenera i zmodyfikować ustawienia sznurka modułu lub jeśli to zrobisz, wybierz pozycję **Dodaj**. 

    ![Konfigurowanie modułu do użycia procesora GPU 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. Upewnij się, że moduł jest uruchomiony, a następnie wybierz pozycję **Przegląd + Utwórz**.

    ![Konfigurowanie modułu do użycia procesora GPU 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. Na karcie **Recenzja + tworzenie** zostaną wyświetlone wybrane opcje wdrażania. Przejrzyj opcje i wybierz pozycję **Utwórz**.
    
    ![Konfigurowanie modułu do użycia procesora GPU 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. Zanotuj **stan środowiska uruchomieniowego** modułu.
    
    ![Konfigurowanie modułu do użycia procesora GPU 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    Wdrożenie modułu może potrwać kilka minut. Wybierz pozycję **Odśwież** , aby sprawdzić, czy aktualizacja **stanu środowiska uruchomieniowego** została **uruchomiona**.

    ![Konfigurowanie modułu do użycia procesora GPU 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zmiennych środowiskowych, których można używać z procesorem GPU firmy NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
