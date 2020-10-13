---
title: Samouczek dotyczący filtrowania, analizowania danych na potrzeby zaawansowanego wdrażania przy użyciu obliczeń na Azure Stack EDGE Pro | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową w programie Azure Stack EDGE Pro i użyć jej do przekształcenia danych w zaawansowanym przepływie wdrażania przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: bcad165f5d0ba2cf652cff35091e05b4414193c8
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951795"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Samouczek: Przekształcanie danych za pomocą Azure Stack EDGE Pro dla zaawansowanego przepływu wdrażania

W tym samouczku opisano, jak skonfigurować rolę obliczeniową dla zaawansowanego przepływu wdrażania na urządzeniu Azure Stack brzeg Pro. Po skonfigurowaniu roli obliczeniowej Azure Stack EDGE Pro może przekształcić dane przed wysłaniem ich do platformy Azure.

Obliczenia można skonfigurować dla prostego lub zaawansowanego przepływu wdrażania na urządzeniu.

| Kryteria | Proste wdrożenie                                | Wdrożenie zaawansowane                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Przeznaczone dla     | Administratorzy IT                                | Deweloperzy                            |
| Typ             | Wdrażanie modułów przy użyciu usługi Azure Stack Edge      | Wdrażanie modułów przy użyciu usługi IoT Hub |
| Wdrożone moduły | Pojedynczy                                           | Łańcucha lub wiele modułów           |


Wykonanie tej procedury może potrwać od 20 do 30 minut.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie wyzwalacza
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu z systemem Azure Stack EDGE Pro upewnij się, że:

- Aktywowano urządzenie Azure Stack EDGE Pro zgodnie z opisem w temacie [Connect, Set up i activate Azure Stack EDGE Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurowanie obliczeń

W celu skonfigurowania obliczeń na Azure Stack EDGE Pro utworzysz zasób IoT Hub.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia**. W okienku po prawej stronie na kafelku **Oblicz** wybierz pozycję **Rozpocznij**.

    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na kafelku **Konfigurowanie obliczeń krawędzi** wybierz pozycję **Konfiguruj obliczenia**.

    ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. W bloku **Konfigurowanie obliczeń krawędzi** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Azure Stack Edge.     |
    |Nazwa     |Wprowadź nazwę dla zasobu IoT Hub.         |

    ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Wybierz pozycję **Utwórz**. Tworzenie zasobów IoT Hub trwa kilka minut. Po utworzeniu zasobu IoT Hub, konfiguracja kafelka **obliczenia krawędzi** zostanie zaktualizowana tak, aby pokazywać konfigurację obliczeniową. Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **Wyświetl konfigurację** na kafelku **Konfiguruj obliczenia** .
    
    ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge.

    W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.


## <a name="add-shares"></a>Dodaj udziały

W przypadku wdrożenia zaawansowanego w tym samouczku potrzebne są dwa udziały: jeden udział graniczny i inny udział lokalny Edge.

1. Dodaj udział graniczny na urządzeniu, wykonując następujące czynności:

    1. W przystawce zasób Azure Stack Edge przejdź do pozycji **Oblicz compute > Rozpocznij**.
    2. Na kafelku **Dodaj udziały** wybierz pozycję **Dodaj**.
    3. W bloku **Dodaj udział** Podaj nazwę udziału i wybierz typ udziału.
    4. Aby zainstalować udział brzegowy, zaznacz pole wyboru **Użyj opcji Udostępnij przy użyciu obliczeń brzegowych**.
    5. Wybierz **konto magazynu**, **usługę magazynu**, istniejącego użytkownika, a następnie wybierz pozycję **Utwórz**.

        ![Dodawanie udziału brzegowego](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po utworzeniu udziału brzegowego otrzymasz powiadomienie o pomyślnym utworzeniu. Lista udziałów jest aktualizowana w celu odzwierciedlenia nowego udziału.

2. Dodaj udział lokalny z krawędzią na urządzeniu brzegowym, powtarzając wszystkie kroki w poprzednim kroku i zaznaczając pole wyboru **Konfiguruj jako krawędź lokalna udziału**. Dane w udziale lokalnym pozostają na urządzeniu.

    ![Dodawanie udziału lokalnego krawędzi](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. W bloku **udziały** zobaczysz zaktualizowaną listę udziałów.

    ![Zaktualizowana lista udziałów](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Aby wyświetlić właściwości nowo utworzonego udziału lokalnego, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Pole „Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge”](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Aby wyświetlić właściwości utworzonego udziału brzegowego, wybierz udział z listy. W polu **Lokalny punkt instalacji dla modułów funkcji obliczeniowej Edge** skopiuj wartość odpowiadającą temu udziałowi.

    Użyjesz tego lokalnego punktu instalacji podczas wdrażania modułu.

    ![Dodawanie modułu niestandardowego](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

1. Przejdź do pozycji **Oblicz compute > Triggers**. Wybierz pozycję **+ Dodaj wyzwalacz**.

    ![Dodawanie wyzwalacza](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. W bloku **Dodawanie wyzwalacza** wprowadź następujące wartości.

    |Pole  |Wartość  |
    |---------|---------|
    |Nazwa wyzwalacza     | Unikatowa nazwa wyzwalacza.         |
    |Typ wyzwalacza     | Wybierz pozycję wyzwalacz **pliku** . Wyzwalacz pliku jest uruchamiany za każdym razem, gdy występuje zdarzenie pliku, takie jak zapisanie pliku w udziale wejściowym. Zaplanowany wyzwalacz z drugiej strony jest uruchamiany zgodnie z harmonogramem zdefiniowanym przez użytkownika. W tym przykładzie potrzebny jest wyzwalacz pliku.    |
    |Udział wejściowy     | Wybierz udział wejściowy. Udział lokalny krawędzi jest w tym przypadku udziałem wejściowym. Używany tutaj moduł przenosi pliki z udziału lokalnego Edge do udziału granicznego, w którym są przekazywane do chmury.        |

    ![Dodaj wyzwalacz 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Po utworzeniu wyzwalacza otrzymasz powiadomienie. Lista wyzwalaczy została zaktualizowana w celu wyświetlenia nowo utworzonego wyzwalacza. Wybierz wyzwalacz, który został właśnie utworzony.

    ![Dodaj wyzwalacz 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Skopiuj i Zapisz przykładową trasę. Ta Przykładowa trasa zostanie zmodyfikowana i będzie używana później w IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Dodaj wyzwalacz 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Dodawanie modułu

Na tym urządzeniu brzegowym nie ma modułów niestandardowych. Możliwe jest dodanie niestandardowego lub wstępnie skompilowanego modułu. Aby dowiedzieć się, jak utworzyć niestandardowy moduł, przejdź do pozycji [opracowywanie modułu języka C# dla urządzenia z Azure Stack EDGE Pro](azure-stack-edge-create-iot-edge-module.md).

W tej sekcji dowiesz się, jak dodać niestandardowy moduł do urządzenia IoT Edge utworzonego w programie [opracowywanie modułu C# dla Azure Stack EDGE Pro](azure-stack-edge-create-iot-edge-module.md). Ten moduł niestandardowy pobiera pliki z udziału lokalnego z krawędzi na urządzeniu brzegowym i przenosi je do udziału brzegowego (chmurowego) na urządzeniu. Następnie udział chmurowy wypycha pliki na konto magazynu platformy Azure skojarzone z tym udziałem chmurowym.

1. Przejdź do pozycji **obliczenia graniczne > wprowadzenie**. Na kafelku **Dodawanie modułów** wybierz typ scenariusza jako **Zaawansowany**. Wybierz pozycję **Przejdź do IoT Hub**.

    ![Wybieranie wdrożenia zaawansowanego](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. W zasobie IoT Hub przejdź do pozycji **IoT Edge Device** , a następnie wybierz urządzenie IoT Edge.

    ![Przejdź do urządzenia IoT Edge w IoT Hub](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. W obszarze **szczegóły urządzenia**wybierz pozycję **Ustaw moduły**.

    ![Link Ustawianie modułów](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. W obszarze **Dodawanie modułów** wykonaj następujące czynności:

    1. Wprowadź nazwę, adres, nazwę użytkownika i hasło dla ustawień rejestru kontenerów dla modułu niestandardowego.
    Nazwa, adres i wymienione poświadczenia są używane do pobierania modułów za pomocą zgodnego adresu URL. Aby wdrożyć ten moduł, w obszarze **Deployment modules** (Moduły wdrażania) wybierz opcję **IoT Edge module** (Moduł usługi IoT Edge). Ten moduł IoT Edge jest kontenerem platformy Docker, który można wdrożyć na urządzeniu IoT Edge skojarzonym z urządzeniem Azure Stack Edge.

        ![Strona Ustawianie modułów](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Określ ustawienia dla niestandardowego modułu usługi IoT Edge. Wprowadź następujące wartości.
     
        |Pole  |Wartość  |
        |---------|---------|
        |Nazwa     | Unikatowa nazwa modułu. Ten moduł jest kontenerem platformy Docker, który można wdrożyć na urządzeniu IoT Edge skojarzonym z Azure Stack krawędzią Pro.        |
        |Identyfikator URI obrazu     | Identyfikator URI obrazu dla odpowiedniego obrazu kontenera modułu.        |
        |Wymagane są poświadczenia     | Jeśli ta opcja jest zaznaczona, nazwa użytkownika i hasło są używane do pobierania modułów z pasującym adresem URL.        |
    
        W polu **Opcje tworzenia kontenera** wprowadź lokalne punkty instalacji dla modułów brzegowych, które zostały skopiowane w powyższych krokach dla udziału brzegowego i udziału lokalnego Edge.

        > [!IMPORTANT]
        > Używane tu ścieżki są instalowane w kontenerze, dlatego muszą być zgodne z oczekiwaniami funkcji w kontenerze. Jeśli korzystasz z [tworzenia modułu niestandardowego](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code), kod określony w tym module oczekuje na skopiowane ścieżki. Nie należy modyfikować tych ścieżek.
    
        W polu **Opcje tworzenia kontenera** możesz wkleić poniższy przykład:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Podaj wszelkie zmienne środowiskowe używane dla modułu. Zmienne środowiskowe zawierają opcjonalne informacje ułatwiające Definiowanie środowiska, w którym działa moduł.

        ![Pole Opcje tworzenia kontenera](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Jeśli to konieczne, skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge, a następnie kliknij pozycję **Dalej**.

        ![Dodawanie modułu niestandardowego 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. W obszarze **Określ trasy**Ustaw trasy między modułami.  
   
   ![Określanie tras](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    Możesz zamienić *trasę* na następujący wcześniej skopiowany ciąg trasy. W tym przykładzie podaj nazwę udziału lokalnego, który wypchnie dane do udziału chmurowego. Zamień na `modulename` nazwę modułu. Wybierz pozycję **Dalej**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Sekcja Określanie tras](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. W obszarze **Przejrzyj wdrożenie** sprawdź wszystkie ustawienia, a następnie wybierz pozycję **Prześlij**, aby przesłać moduł do wdrożenia.

   ![Strona Ustawianie modułów 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ta akcja uruchamia wdrożenie modułu. Po zakończeniu wdrażania **stan środowiska uruchomieniowego** modułu jest **uruchomiony**.

    ![Dodaj moduł niestandardowy 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Weryfikowanie transformacji danych, transfer

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj następujące kroki, aby zweryfikować transformację i transfer danych na platformę Azure.
 
1. W Eksploratorze plików Połącz się z utworzonymi wcześniej udziałami lokalnymi i krawędziami krawędzi.

   ![Weryfikowanie przekształcania danych](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Dodaj dane do udziału lokalnego.

   ![Weryfikowanie przekształcenia danych 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Dane zostaną przeniesione do udziału chmurowego.

    ![Weryfikowanie przekształcenia danych 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Następnie dane zostaną wypchnięte z udziału chmurowego na konto magazynu. Aby wyświetlić dane, przejdź do swojego konta magazynu, a następnie wybierz pozycję **Eksplorator usługi Storage**. Przekazane dane można wyświetlić na koncie magazynu.

    ![Weryfikowanie przekształcenia danych 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Ukończono proces walidacji.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Dodaj udziały
> * Dodawanie wyzwalacza
> * Dodawanie modułu obliczeniowego
> * Weryfikowanie przekształcania danych i transferu

Aby dowiedzieć się, jak administrować urządzeniem Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Używanie lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
