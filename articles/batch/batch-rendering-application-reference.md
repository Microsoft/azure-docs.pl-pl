---
title: Korzystanie z aplikacji renderowania
description: Jak używać aplikacji renderowania z Azure Batch. Ten artykuł zawiera krótki opis sposobu uruchamiania każdej aplikacji renderowania.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964876"
---
# <a name="rendering-applications"></a>Renderowanie aplikacji

Renderowanie aplikacji jest używane przez tworzenie zadań i zadań wsadowych. Właściwość wiersza polecenia zadania określa odpowiedni wiersz polecenia i parametry.  Najprostszym sposobem tworzenia zadań zadania jest użycie szablonów Batch Explorer zgodnie z opisem w [tym artykule](./batch-rendering-using.md#using-batch-explorer).  Szablony mogą być przeglądane i modyfikowane w razie potrzeby.

Ten artykuł zawiera krótki opis sposobu uruchamiania każdej aplikacji renderowania.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderowanie przy użyciu programu Autodesk 3ds Max

### <a name="renderer-support"></a>Obsługa modułu renderowania

Oprócz renderowania wbudowanych w 3ds Max, następujące moduły renderowania są dostępne na wyrenderowanych obrazach maszyn wirtualnych i można do nich odwoływać się przy użyciu maksymalnego pliku sceny 3DS:

* Autodesk Arnold
* Grupa chaos V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

Wywołaj `3dsmaxcmdio.exe` aplikację, aby wykonać renderowanie wiersza polecenia w węźle puli.  Ta aplikacja znajduje się na ścieżce, gdy zadanie jest uruchomione. `3dsmaxcmdio.exe`Aplikacja ma takie same parametry `3dsmaxcmd.exe` , jak aplikacja, która jest udokumentowana w sekcji [3ds Max help](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (renderowanie | Command-Line renderowania).

Na przykład:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Uwagi:

* Należy zachować szczególną ostrożność, aby upewnić się, że pliki zasobów zostaną znalezione.  Upewnij się, że ścieżki są poprawne i względne przy użyciu okna **śledzenia zasobów** , lub Użyj `-bitmapPath` parametru w wierszu polecenia.
* Sprawdź, czy występują problemy z renderowaniem, takie jak niemożność odnalezienia zasobów, sprawdzając `stdout.txt` plik zapisany przez 3ds Max, gdy zadanie jest uruchomione.

### <a name="batch-explorer-templates"></a>Szablony Batch Explorer

Dostęp do puli i szablonów zadań można uzyskać z **galerii** w Batch Explorer.  Pliki źródłowe szablonu są dostępne w [repozytorium danych Batch Explorer w witrynie GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Renderowanie przy użyciu Autodesk Maya

### <a name="renderer-support"></a>Obsługa modułu renderowania

Oprócz renderowania wbudowanych w Maya, następujące moduły renderowania są dostępne na wyrenderowanych obrazach maszyn wirtualnych i można do nich odwoływać się przy użyciu maksymalnego pliku sceny 3DS:

* Autodesk Arnold
* Grupa chaos V-Ray

### <a name="task-command-line"></a>Wiersz polecenia zadania

`renderer.exe`Renderowanie w wierszu polecenia jest używane w wierszu polecenia zadania. Renderowanie w wierszu polecenia jest udokumentowane w [pomocy Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

W poniższym przykładzie zadanie przygotowania zadania służy do kopiowania plików sceny i zasobów do katalogu roboczego przygotowania zadania, folder wyjściowy jest używany do przechowywania obrazu renderowania, a ramka 10 jest renderowana.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

W przypadku renderowania za pomocą usługi V-Ray plik sceny Maya zazwyczaj określa V-Ray jako moduł renderowania.  Można go również określić w wierszu polecenia:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

W przypadku renderowania Arnold plik sceny Maya zwykle określa Arnold jako moduł renderowania.  Można go również określić w wierszu polecenia:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Szablony Batch Explorer

Dostęp do puli i szablonów zadań można uzyskać z **galerii** w Batch Explorer.  Pliki źródłowe szablonu są dostępne w [repozytorium danych Batch Explorer w witrynie GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Następne kroki

Użyj szablonów puli i zadań z [repozytorium danych w usłudze GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) przy użyciu Batch Explorer.  W razie potrzeby utwórz nowe szablony lub zmodyfikuj jeden z podanych szablonów.
