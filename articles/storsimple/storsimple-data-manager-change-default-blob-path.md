---
title: Zmień ścieżkę obiektu BLOB z domyślnego
description: Dowiedz się, jak skonfigurować funkcję platformy Azure, aby zmienić nazwę domyślnej ścieżki pliku obiektu BLOB i przenieść obiekty blob do innej lokalizacji.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011442"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Zmiana ścieżki obiektu blob ze ścieżki domyślnej

Gdy Usługa StorSimple Data Manager przekształca dane, domyślnie umieszcza przekształcone obiekty blob w kontenerze magazynu określonym podczas tworzenia repozytorium docelowego. Gdy obiekty blob docierają do tej lokalizacji, warto przenieść te obiekty blob do lokalizacji alternatywnej. W tym artykule opisano sposób konfigurowania funkcji platformy Azure w celu zmiany nazwy domyślnej ścieżki pliku obiektu BLOB, a tym samym przenoszenia obiektów BLOB do innej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że w usłudze StorSimple Data Manager masz poprawnie skonfigurowaną definicję zadania.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Aby utworzyć funkcję platformy Azure, wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij pozycję **+Utwórz zasób**. W polu **wyszukiwania** wpisz **aplikacja funkcji** i naciśnij klawisz **Enter**. Wybierz i kliknij pozycję **aplikacja funkcji** na wyświetlonej liście aplikacji.

    ![Wpisz "aplikacja funkcji" w polu wyszukiwania](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kliknij pozycję **Utwórz**.

    ![Przycisk "Utwórz" okna aplikacja funkcji](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. W bloku konfiguracja **aplikacja funkcji** wykonaj następujące czynności:

    1. Podaj unikatową **nazwę aplikacji**.
    2. Z listy rozwijanej wybierz **subskrypcję**. Ta subskrypcja powinna być taka sama jak ta, która jest skojarzona z usługą StorSimple Data Manager.
    3. Wybierz pozycję **Utwórz nową** grupę zasobów.
    4. Na liście rozwijanej **Plan hostingu** wybierz pozycję **Plan zużycia**.
    5. Określ lokalizację, w której działa funkcja. Należy zlokalizować ten sam region, w którym znajduje się Usługa StorSimple Data Manager i konto magazynu skojarzone z definicją zadania.
    6. Wybierz istniejące konto magazynu lub utwórz nowe. Konto magazynu jest używane wewnętrznie przez funkcję.

        ![Wprowadź nowe dane konfiguracji aplikacja funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kliknij pozycję **Utwórz**. Zostanie utworzona aplikacja funkcji.
     
        ![Utworzono aplikacja funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Wybierz pozycję **funkcje**, a następnie kliknij pozycję **+ Nowa funkcja**.

    ![Kliknij pozycję + Nowa funkcja](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Wybierz język **C#** dla tego języka. W tablicy kafelków szablonu wybierz pozycję **C#** na kafelku **QueueTrigger-CSharp** .

7. W **wyzwalaczu kolejki**:

    1. Wprowadź **nazwę** funkcji.
    2. W polu **Nazwa kolejki** wpisz nazwę definicji zadania transformacji danych.
    3. W obszarze **połączenie konta magazynu** kliknij pozycję **Nowy**. Z listy kont magazynu wybierz konto skojarzone z definicją zadania. Zanotuj nazwę połączenia (wyróżnioną). Nazwa jest wymagana w dalszej części funkcji platformy Azure.

        ![Tworzenie nowej funkcji języka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kliknij pozycję **Utwórz**. Zostanie utworzona **Funkcja** .

     
10. W oknie funkcji Uruchom plik _. CSX_ .

    ![Tworzenie nowej funkcji języka C# 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Wykonaj poniższe kroki.

    1. Wklej następujący kod:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Zastąp **STORAGE_CONNECTIONNAME** w wierszu 11 wierszem do połączenia z kontem magazynu (zobacz krok 7c).

        ![Kopiuj nazwę połączenia magazynu](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Zapisz** funkcję.

        ![Funkcja Save](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Aby ukończyć funkcję, Dodaj jeden plik, wykonując następujące czynności:

    1. Kliknij pozycję **Wyświetl pliki**.

       ![Link "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kliknij pozycję **+ Dodaj**.
        
        ![Dodaj nową funkcję Dodaj plik](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Wpisz **project.jsw**, a następnie naciśnij klawisz **Enter**. W **project.js** pliku wklej następujący kod:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Kliknij pozycję **Zapisz**.

        ![Nowy plik JSON projektu funkcji](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Utworzono funkcję platformy Azure. Ta funkcja jest wyzwalana za każdym razem, gdy nowy obiekt BLOB jest generowany przez zadanie przekształcania danych.

## <a name="next-steps"></a>Następne kroki

[Używanie StorSimple Data Manager interfejsu użytkownika do przekształcania danych](storsimple-data-manager-ui.md)
