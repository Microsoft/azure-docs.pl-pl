---
title: Samouczek — renderowanie sceny programu Blend przy użyciu Azure Batch i Batch Explorer
description: Samouczek — renderowanie wielu ramek ze sceny programu Blender przy użyciu usługi Azure Batch i aplikacji klienckiej narzędzia Batch Explorer
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490844"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Samouczek: renderowanie sceny programu Blender przy użyciu narzędzia Batch Explorer

W tym samouczku przedstawiono sposób renderowania wielu ramek sceny demonstracyjnej programu Blender. Na potrzeby tego samouczka używany jest program Blender, ponieważ jest bezpłatny dla klienta i renderujących maszyn wirtualnych, ale proces jest bardzo podobny w przypadku używania innych aplikacji, takich jak Maya lub 3ds Max.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przekazywanie sceny programu Blender do usługi Azure Storage
> * Tworzenie puli usługi Batch z wieloma węzłami w celu wykonania renderowania
> * Renderowanie wielu ramek
> * Wyświetlanie i pobieranie plików renderowanych ramek

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z aplikacji renderujących w usłudze Batch z opłatami za użycie, potrzebujesz subskrypcji z płatnością zgodnie z rzeczywistym użyciem lub innej opcji zakupu platformy Azure. Licencjonowanie na zasadzie płatności za użycie nie jest obsługiwane, jeśli korzystasz z bezpłatnej oferty platformy Azure, w ramach której otrzymujesz środki pieniężne.

Potrzebujesz konta usługi Azure Batch ze skojarzonym kontem magazynu.  Zobacz dowolny z artykułów przewodników Szybki start usługi Batch, na przykład [artykuł dotyczący interfejsu wiersza polecenia](./quick-create-cli.md), aby utworzyć konto usługi Batch.

Dla rozmiaru maszyny wirtualnej i liczby maszyn wirtualnych określonych w tym samouczku wymagany jest limit przydziału rdzeni o niskim priorytecie wynoszący co najmniej 50 rdzeni. Można użyć domyślnego limitu przydziału, ale wymaga to użycia mniejszego rozmiaru maszyny wirtualnej, co oznacza, że renderowanie obrazów będzie trwało dłużej. Proces żądania zwiększonego limitu przydziału rdzeni został szczegółowo opisany w [tym artykule](./batch-quota-limit.md).

Na koniec wymagane jest zainstalowanie narzędzia [Batch Explorer](https://azure.github.io/BatchExplorer/) (jest ono dostępne dla systemów Windows, OS x i Linux). Program [Blender](https://www.blender.org/download/) jest opcjonalny, ale jeśli jest zainstalowany, można wyświetlić przykładowy plik modelu.

## <a name="download-the-demo-scene"></a>Pobieranie sceny demonstracyjnej

Pobierz plik ZIP pokazu „Class room” dla programu Blender ze [strony internetowej plików demonstracyjnych programu Blender](https://www.blender.org/download/demo-files/) i rozpakuj go na dysku lokalnym.

## <a name="upload-a-scene-to-azure-storage"></a>Przekazywanie sceny do usługi Azure Storage

Utwórz kontener konta magazynu dla plików sceny demonstracyjnej:

* Uruchom narzędzie Batch Explorer
* Wybierz element menu „Data” (Dane) z menu głównego po lewej stronie.
* Upewnij się, że na liście rozwijanej jest wybrana pozycja „File Groups” (Grupy plików).
* Wybierz przycisk „+” i utwórz nowy element „Empty file group” (Pusta grupa plików) o nazwie „blender-classroom”
  * Grupa plików jest po prostu kontenerem obiektów blob usługi Azure Storage z prefiksem „fgrp-”; jest to konwencja używana do filtrowania innych kontenerów w ramach konta magazynu

![Grupa plików dla plików sceny](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Przekaż pliki sceny:

* Wybierz nowy kontener, a następnie przeciągnij i upuść zawartość folderu „classroom” do kontenera w programie Batch Explorer.

![Przekazane pliki sceny](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Tworzenie kontenera usługi Azure Storage dla obrazów wyjściowych

Utwórz kontener konta magazynu dla plików wyjściowych sceny demonstracyjnej:

* Wybierz element menu „Data” (Dane) z menu głównego po lewej stronie.
* Wybierz przycisk „+” i utwórz nowy element „Empty file group” (Pusta grupa plików) o nazwie „render-output”

![Grupa plików dla plików wyjściowych](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Tworzenie puli maszyn wirtualnych na potrzeby renderowania

Utwórz pulę usługi Batch korzystającą z renderowania obrazu maszyn wirtualnych z witryny Azure Marketplace, który zawiera aplikację programu Blender:

* Wybierz element menu „Galery" (Galeria) z menu głównego po lewej stronie.
* Wybierz element „Blender” dla listy elementów aplikacji.
* Wybierz elementy na potrzeby renderowania ramek w systemie Windows Server
* Opcjonalnie można wybrać ikonę linku po prawej stronie elementu, aby wyświetlić pliki szablonów, które będą używane do utworzenia puli i zlecenia.

![Elementy galerii programu Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Wybierz przycisk „Create pool for later use” (Utwórz pulę do późniejszego użycia)
  * W polu „Pool” (Pula) pozostaw nazwę puli „blender-windows”
  * W polu „Dedicated vm count” (Liczba dedykowanych maszyn wirtualnych) ustaw wartość „0”
  * W polu „Low priority vm count" (Liczba maszyn wirtualnych o niskim priorytecie) ustaw wartość „3”
  * W polu „Node size” (Rozmiar węzła) ustaw wartość „Standard_F16” — można wybrać inny rozmiar maszyny wirtualnej, ale czas renderowania ramki będzie zależeć głównie od liczby rdzeni.
* Wybierz zielony przycisk, aby utworzyć pulę
  * Pula zostanie utworzona niemal natychmiast, ale przydzielenie i uruchomienie maszyn wirtualnych może potrwać kilka minut.

![Szablon puli dla programu Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Należy pamiętać, że kiedy w puli znajdują się maszyny wirtualne, kosztami tych maszyn wirtualnych jest obciążana Twoja subskrypcja platformy Azure. Należy usunąć pulę lub usunąć z niej maszyny wirtualne, aby zatrzymać naliczanie opłat. Usuń pulę na końcu tego samouczka, aby zapobiec stałemu naliczaniu opłat.

Stan puli i maszyn wirtualnych można monitorować w widoku „Pools” (Pule). W poniższym przykładzie wszystkie trzy maszyny wirtualne zostały przydzielone, dwie z nich zostały uruchomione i są w stanie bezczynności, a uruchamianie jednej wciąż trwa: ![Mapa cieplna puli](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Tworzenie zadania renderowania

Utwórz zadanie renderowania w celu renderowania niektórych ramek przy użyciu utworzonej puli:

* Wybierz element menu „Galery" (Galeria) z menu głównego po lewej stronie.
* Wybierz element „Blender” dla listy elementów aplikacji.
* Wybierz elementy dla renderowania ramek w systemie Windows Server.
* Wybierz przycisk „Run job with existing pool” (Uruchom zlecenie przy użyciu istniejącej puli)
* Wybierz pulę „blender-windows”
* W polu „Nazwa zadania” ustaw wartość „blender-render-tutorial1”
* Dla pola „Dane wejściowe” wybierz wartość „fgrp-blender-classroom”
* Wybierz ikonę pliku „Blend file” (Plik programu Blend), a następnie wybierz plik „classroom.blend”
* W polu „Frame start” (Początek ramki) pozostaw wartość „1”, a w polu „Frame end” (Koniec ramki) ustaw wartość „5”
* W polu „Outputs” (Dane wyjściowe) ustaw wartość „fgrp-render-output”
* Wybierz zielony przycisk, aby utworzyć zlecenie. Zlecenie zostanie utworzone z pięcioma zadaniami, po jednym dla każdej ramki

![Szablon zlecenia dla programu Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Po utworzeniu zlecenia i wszystkich zadań zlecenie zostanie wyświetlone wraz z zadaniami zlecenia: ![Lista zadań w ramach zlecenia](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Podczas pierwszego uruchomienia zadania na maszynie wirtualnej w puli, uruchamiane jest zadanie podrzędne przygotowania zlecenia usługi Batch, które kopiuje pliki sceny z grupy plików magazynu na maszynę wirtualną, aby były one dostępne dla programu Blender.
Stan renderowania można określić, wyświetlając plik dziennika stdout.txt utworzony przez program Blender.  Wybierz zadanie, aby wyświetlić kartę „Task Outputs” (Dane wyjściowe zadania), na której można wybrać i wyświetlić plik „stdout.txt”.
![plik stdout](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

W przypadku wybrania puli „blender-windows” maszyny wirtualne z puli będą widoczne w stanie uruchomionym: ![Mapa cieplna puli z uruchomionymi węzłami](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

Wygenerowanie renderowanych obrazów potrwa kilka minut w zależności od wybranego rozmiaru maszyny wirtualnej.  Przy użyciu maszyny wirtualnej F16 określonej wcześniej renderowanie ramek trwało około 16 minut.

## <a name="view-the-rendering-output"></a>Wyświetlanie danych wyjściowych renderowania

Po zakończeniu renderowania ramek te zadania będą wyświetlane jako ukończone: ![Kończenie zadań](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

Renderowany obraz jest najpierw zapisywany na maszynie wirtualnej i można go wyświetlić, wybierając folder „wd”: ![Renderowany obraz w węźle puli](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

Szablon zlecenia określa również, że pliki wyjściowe ramki i dziennika są zapisywane zwrotnie w grupie plików konta usługi Azure Storage określonej podczas tworzenia zlecenia.  Interfejs użytkownika „Data” (Dane) może być używany do wyświetlania dzienników i plików wyjściowych, a także do pobierania plików: ![Renderowany obraz w grupie plików usługi Storage](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Po zakończeniu wszystkich zadań zlecenie zostanie oznaczone jako ukończone: ![Zlecenie i wszystkie zadania ukończone](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

> [!WARNING]
> Pula musi zostać usunięta (można również zmienić jej rozmiar na zero węzłów), aby zatrzymać naliczanie opłat za maszyny wirtualne dla subskrypcji platformy Azure.

* Wybierz pozycję „Pools” (Pule)
* Wybierz pulę „blender-windows”
* Kliknij ją prawym przyciskiem myszy i wybierz polecenie „Usuń” lub wybierz ikonę kosza nad pulą

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przekazywanie sceny programu Blender do usługi Azure Storage
> * Tworzenie puli usługi Batch z wieloma węzłami w celu wykonania renderowania
> * Renderowanie wielu ramek
> * Wyświetlanie i pobieranie plików renderowanych ramek

Kontynuuj, przeeksplorowanie aplikacji do renderowania dostępnych za pośrednictwem Batch Explorer w sekcji **Galeria** . Dla każdej aplikacji istnieje kilka dostępnych szablonów, które z czasem zostaną rozwinięte. Na przykład dla programu Blender istnieją szablony, które dzielą pojedynczy obraz na kafelki, dzięki czemu części obrazu mogą być renderowane równolegle.

Aby dowiedzieć się więcej na temat renderowania w skali chmury, zobacz opcje usługi Batch Rendering Service.

> [!div class="nextstepaction"]
> [Usługa renderowania dla usługi Batch](batch-rendering-service.md)
