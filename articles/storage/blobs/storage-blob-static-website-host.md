---
title: 'Samouczek: hostowanie statycznej witryny sieci Web w usłudze BLOB Storage — Azure Storage'
description: Dowiedz się, w jaki sposób skonfigurować konto magazynu w celu hostowania statycznej witryny internetowej i wdrożyć statyczną witrynę internetową w usłudze Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888508"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Samouczek: hostowanie statycznej witryny sieci Web na Blob Storage

W tym samouczku dowiesz się, jak skompilować i wdrożyć statyczną witrynę sieci Web w usłudze Azure Storage. Po zakończeniu będzie gotowa statyczna witryna internetowa, do której użytkownicy będą mieli publiczny dostęp. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie hostowania statycznej witryny internetowej
> * Wdrażanie witryny internetowej Hello world

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Upewnij się, że utworzono standardowe konto magazynu ogólnego przeznaczenia w wersji 2. Statyczne witryny sieci Web nie są dostępne na żadnym innym typie konta magazynu.

W tym samouczku wykorzystany zostanie program [Visual Studio Code](https://code.visualstudio.com/download), czyli bezpłatne narzędzie dla programistów umożliwiające utworzenie statycznej witryny internetowej i wdrożenie jej na koncie usługi Azure Storage.

Po zainstalowaniu programu Visual Studio Code zainstaluj rozszerzenie wersji zapoznawczej usługi Azure Storage. To rozszerzenie integruje funkcje zarządzania usługą Azure Storage z programem Visual Studio Code. To rozszerzenie zostanie użyte do wdrożenia statycznej witryny internetowej w usłudze Azure Storage. Aby zainstalować rozszerzenie:

1. Uruchom program Visual Studio Code.
2. Na pasku narzędzi kliknij pozycję **Extensions** (Rozszerzenia). Wyszukaj hasło *Azure Storage* i wybierz rozszerzenie **Azure Storage** na liście. Następnie kliknij przycisk **Install** (Zainstaluj), aby zainstalować rozszerzenie.

    ![Instalowanie rozszerzenia usługi Azure Storage w programie VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

## <a name="configure-static-website-hosting"></a>Konfigurowanie hostowania statycznej witryny internetowej

Pierwszym krokiem jest skonfigurowanie konta magazynu na potrzeby hostowania statycznej witryny internetowej w witrynie Azure Portal. Gdy skonfigurujesz konto pod kątem hostowania statycznej witryny internetowej, usługa Azure Storage automatycznie utworzy kontener o nazwie *$web*. Kontener *$web* będzie zawierał pliki statycznej witryny internetowej. 

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/) w przeglądarce internetowej. 
1. Znajdź konto magazynu i wyświetl omówienie konta.
1. Wybierz pozycję **Statyczna witryna internetowa**, aby wyświetlić stronę konfiguracji dla statycznych witryn internetowych.
1. Wybierz pozycję **Włączone**, aby umożliwić hostowanie statycznej witryny internetowej na koncie magazynu.
1. W polu **Nazwa dokumentu indeksu** określ domyślną stronę indeksu *index.html*. Gdy użytkownik przejdzie do głównego elementu statycznej witryny internetowej, wyświetlona zostanie domyślna strona indeksu.  
1. W polu **Ścieżka dokumentu błędu** określ domyślną stronę błędu *404. html*. Gdy użytkownik spróbuje przejść do strony, która nie istnieje w statycznej witrynie internetowej, wyświetlona zostanie domyślna strona błędu.
1. Kliknij przycisk **Zapisz**. W witrynie Azure Portal jest teraz wyświetlany punkt końcowy statycznej witryny internetowej. 

    ![Włączanie hostowania statycznej witryny internetowej na koncie magazynu](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Wdrażanie witryny internetowej Hello world

Następnie utwórz stronę internetową Hello world w programie Visual Studio Code i wdróż ją w statycznej witrynie internetowej hostowanej na koncie usługi Azure Storage.

1. Utwórz pusty folder o nazwie *mywebsite* w lokalnym systemie plików. 
1. Uruchom program Visual Studio Code i otwórz utworzony folder na panelu **Explorer**.

    ![Otwieranie folderu w programie Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Utwórz domyślny plik indeksu w folderze *mywebsite* i nazwij go *index.html*.

    ![Tworzenie domyślnego pliku indeksu w programie Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Otwórz plik *index.html* w edytorze, wklej następujący tekst do pliku i zapisz go:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Utwórz domyślny plik błędu i nazwij go *404. html*.
1. Otwórz plik *404.html* w edytorze, wklej następujący tekst do pliku i zapisz go:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. Kliknij prawym przyciskiem myszy w obszarze folderu *mywebsite* na panelu **Explorer**, a następnie wybierz pozycję **Deploy to Static Website...** (Wdrażanie w statycznej witrynie internetowej), aby wdrożyć witrynę internetową. Zostanie wyświetlony monit o zalogowanie się na platformie Azure w celu pobrania listy subskrypcji.

1. Wybierz subskrypcję zawierającą konto magazynu, dla którego włączono hostowanie statycznej witryny internetowej. Następnie po wyświetleniu monitu wybierz konto magazynu.

Program Visual Studio Code przekaże pliki do internetowego punktu końcowego i wyświetli pasek stanu powodzenia. Uruchom witrynę internetową, aby ją wyświetlić na platformie Azure.

Samouczek został pomyślnie ukończony i wdrożono statyczną witrynę sieci Web na platformie Azure.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania konta usługi Azure Storage na potrzeby hostingu statycznej witryny sieci Web oraz tworzenia i wdrażania statycznej witryny sieci Web w punkcie końcowym platformy Azure.

Następnie Dowiedz się, jak skonfigurować domenę niestandardową przy użyciu statycznej witryny sieci Web.

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md)
