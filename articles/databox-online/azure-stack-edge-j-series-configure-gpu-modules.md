---
title: Uruchamianie modułu GPU na urządzeniu Microsoft Azure Stack Edge z procesorem GPU Pro | Microsoft Docs
description: Opisuje sposób konfigurowania i uruchamiania modułu na procesorach GPU na urządzeniu z systemem Azure Stack brzeg Pro za pośrednictwem Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 8b9f1180639f638e72fdea2f87958628a2e9e86b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891465"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Konfigurowanie i uruchamianie modułu na procesorach GPU na Azure Stack na urządzeniu brzegowym Pro

Urządzenie Azure Stack EDGE Pro zawiera jedną lub więcej jednostek przetwarzania grafiki (GPU). Procesory GPU są popularnym wyborem w przypadku obliczeń dla AI, ponieważ oferują funkcje przetwarzania równoległego i są szybsze przy renderowaniu obrazu niż centralne jednostki przetwarzania (CPU). Aby uzyskać więcej informacji na temat procesora GPU znajdującego się na urządzeniu Azure Stack brzeg Pro, przejdź do pozycji [Azure Stack EDGE Pro techniczne urządzenia](azure-stack-edge-gpu-technical-specifications-compliance.md)techniczne.

W tym artykule opisano sposób konfigurowania i uruchamiania modułu na procesorze GPU na urządzeniu Azure Stack EDGE Pro. W tym artykule opisano użycie publicznie dostępnych **cyfr** modułu kontenera przeznaczonych dla procesorów GPU firmy NVIDIA. Tej procedury można użyć do skonfigurowania innych modułów opublikowanych przez NVIDIA dla tych procesorów GPU.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do procesora GPU z włączonym 1 węzłem Azure Stack Edge na urządzeniu. To urządzenie jest aktywowane przy użyciu zasobu na platformie Azure.  

## <a name="configure-module-to-use-gpu"></a>Konfigurowanie modułu do użycia procesora GPU

Aby skonfigurować moduł do używania procesora GPU na urządzeniu z systemem Azure Stack Edge w wersji Pro w celu uruchomienia modułu, wykonaj następujące kroki.

1. W Azure Portal przejdź do zasobu skojarzonego z urządzeniem. 

2. Przejdź do pozycji **obliczenia graniczne > wprowadzenie**. Na kafelku **konfigurowania obliczeń brzegowych** wybierz pozycję Konfiguruj.

    ![Konfigurowanie modułu do użycia procesora GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. W bloku **Konfigurowanie obliczeń krawędzi** :

    1. W obszarze **IoT Hub**wybierz pozycję **Utwórz nowy**.
    2. Podaj nazwę zasobu IoT Hub, który chcesz utworzyć dla urządzenia. Aby skorzystać z warstwy Bezpłatna, wybierz istniejący zasób. 
    3. Zanotuj urządzenie IoT Edge i urządzenie bramy IoT utworzone przy użyciu zasobu IoT Hub. Te informacje będą używane w dalszych krokach.

    ![Konfigurowanie modułu do korzystania z procesora GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Utworzenie zasobu IoT Hub może zająć kilka minut. Po utworzeniu zasobu na kafelku **konfigurowania obliczeń brzegowych** wybierz pozycję **Wyświetl konfigurację** , aby wyświetlić szczegóły zasobu IoT Hub.

    ![Konfigurowanie modułu do użycia procesora GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Przejdź do pozycji **Automatyczne zarządzanie urządzeniami > IoT Edge**.

    ![Konfigurowanie modułu do użycia procesora GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    W okienku po prawej stronie zostanie wyświetlone urządzenie IoT Edge skojarzone z urządzeniem Azure Stack EDGE Pro. Odnosi się to do IoT Edge urządzenia utworzonego w poprzednim kroku podczas tworzenia zasobu IoT Hub. 
    
6. Wybierz to urządzenie IoT Edge.

   ![Konfigurowanie modułu do korzystania z procesora GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Wybierz pozycję **Ustaw moduły**.

    ![Konfigurowanie modułu do użycia procesora GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Wybierz pozycję **+ Dodaj** , a następnie pozycję **+ moduł IoT Edge**. 

    ![Konfigurowanie modułu do korzystania z procesora GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. Na karcie **Dodaj moduł IoT Edge** :

    1. Podaj **Identyfikator URI obrazu**. W tym miejscu będziesz używać publicznie dostępnych **cyfr** modułu NVIDIA. 
    
    2. Ustaw **zasady ponownego uruchamiania** na **zawsze**.
    
    3. Ustaw **żądany stan** na **uruchomiony**.
    
    ![Konfigurowanie modułu do użycia procesora GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. Na karcie **zmienne środowiskowe** Podaj nazwę zmiennej i odpowiednią wartość. 

    1. Aby bieżący moduł używał jednego procesora GPU na tym urządzeniu, użyj NVIDIA_VISIBLE_DEVICES. 

    2. Ustaw wartość na 0 lub 1. Gwarantuje to, że urządzenie jest używane co najmniej jeden procesor GPU dla tego modułu. Ustawienie wartości 0, 1 oznacza, że w tym module są używane procesory GPU na urządzeniu.

        ![Konfigurowanie modułu do korzystania z procesora GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Aby uzyskać więcej informacji na temat zmiennych środowiskowych, których można użyć z procesorem GPU NVIDIA, przejdź do [środowiska uruchomieniowego kontenera NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Procesor GPU można zamapować tylko do jednego modułu. Moduł może jednak używać jednego lub bez procesorów GPU. 

11. Wprowadź nazwę modułu. W tym momencie możesz wybrać opcję tworzenia kontenera i zmodyfikować ustawienia sznurka modułu lub jeśli to zrobisz, wybierz pozycję **Dodaj**. 

    ![Konfigurowanie modułu do użycia procesora GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Upewnij się, że moduł jest uruchomiony, a następnie wybierz pozycję **Przegląd + Utwórz**.    

    ![Konfigurowanie modułu do użycia procesora GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. Na karcie **Recenzja + tworzenie** zostaną wyświetlone wybrane opcje wdrażania. Przejrzyj opcje i wybierz pozycję **Utwórz**.
    
    ![Konfigurowanie modułu do użycia procesora GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Zanotuj **stan środowiska uruchomieniowego** modułu. 
    
    ![Konfigurowanie modułu do użycia procesora GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Wdrożenie modułu może potrwać kilka minut. Wybierz pozycję **Odśwież** , aby sprawdzić, czy aktualizacja **stanu środowiska uruchomieniowego** została **uruchomiona**.

    ![Konfigurowanie modułu do użycia procesora GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zmiennych środowiskowych, których można używać z procesorem GPU firmy NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
