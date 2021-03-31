---
title: Szybki Start — Tworzenie obiektu BLOB przy użyciu Azure Portal
titleSuffix: Azure Storage
description: W tym przewodniku Szybki start witryna Azure Portal jest używana w ramach magazynu obiektów (blob). Następnie przy użyciu witryny Azure Portal przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz obiekty blob w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95523335"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal

Dzięki temu przewodnikowi Szybki start dowiesz się, w jaki sposób za pomocą witryny [Azure portal](https://portal.azure.com/) tworzyć kontener w usłudze Azure Storage oraz przekazywać i pobierać blokowe obiekty blob w ramach tego kontenera.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego nowego konta magazynu.
2. W menu po lewej stronie konta magazynu przewiń do sekcji **BLOB Service** , a następnie wybierz pozycję **Containers (kontenery**).
3. Wybierz przycisk **+ Kontener**.
4. Wpisz nazwę nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Ustaw poziom dostępu publicznego do kontenera. Domyślny poziom to **Prywatny (bez dostępu anonimowego)**.
6. Wybierz przycisk **OK**, aby utworzyć kontener.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia kontenera w witrynie Azure Portal":::

## <a name="upload-a-block-blob"></a>Przekazywanie blokowego obiektu blob

Blokowe obiekty blob składają się z bloków danych złożonych w celu utworzenia obiektu blob. W większości scenariuszy dotyczących użycia usługi Blob Storage używane są blokowe obiekty blob. Blokowe obiekty blob idealnie nadają się do przechowywania tekstu i danych binarnych w chmurze, takich jak pliki, obrazy lub filmy wideo. W tym przewodniku Szybki start przedstawiono sposób pracy z blokowymi obiektami blob.

Aby przekazać blokowy obiekt blob do nowego kontenera w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do kontenera utworzonego w poprzedniej sekcji.
1. Wybierz kontener, aby wyświetlić listę obiektów blob, które zawiera. Ten kontener jest nowy, więc nie zawiera jeszcze żadnych obiektów BLOB.
1. Wybierz przycisk **Przekaż** , aby otworzyć blok przekazywanie i przeglądać lokalny system plików, aby znaleźć plik do przekazania jako blokowy obiekt BLOB. Opcjonalnie można rozwinąć sekcję **Zaawansowane** , aby skonfigurować inne ustawienia dla operacji przekazywania.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Zrzut ekranu przedstawiający sposób przekazywania obiektu BLOB z dysku lokalnego za pośrednictwem Azure Portal":::

1. Wybierz przycisk **Przekaż** , aby przekazać obiekt BLOB.
1. Przekaż w ten sposób dowolną liczbę obiektów blob. Nowe obiekty blob zostaną wyświetlone w kontenerze.

## <a name="download-a-block-blob"></a>Pobieranie blokowego obiektu blob

Blokowy obiekt blob możesz pobrać, aby wyświetlić go w przeglądarce lub zapisać w lokalnym systemie plików. Aby pobrać blokowy obiekt blob, wykonaj następujące kroki:

1. Przejdź do listy obiektów blob przekazanych w poprzedniej sekcji.
1. Kliknij prawym przyciskiem myszy obiekt blob, który chcesz pobrać, a następnie wybierz pozycję **Pobierz**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania obiektu blob w witrynie Azure Portal":::

## <a name="delete-a-block-blob"></a>Usuwanie blokowego obiektu BLOB

Aby usunąć co najmniej jeden obiekt BLOB w Azure Portal, wykonaj następujące kroki:

1. W Azure Portal przejdź do kontenera.
1. Wyświetl listę obiektów BLOB w kontenerze.
1. Użyj pola wyboru, aby wybrać co najmniej jeden obiekt BLOB z listy.
1. Wybierz przycisk **Usuń** , aby usunąć wybrane obiekty blob.
1. W oknie dialogowym Potwierdź usunięcie i wskaż, czy chcesz również usunąć migawki obiektów BLOB.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania obiektów blob z Azure Portal":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start, możesz po prostu usunąć kontener. Wszystkie obiekty blob w kontenerze również zostaną usunięte.

Aby usunąć kontener:

1. W witrynie Azure Portal przejdź do listy kontenerów w ramach swojego konta magazynu.
1. Wybierz kontener do usunięcia.
1. Wybierz przycisk **Więcej** (**...**), a następnie wybierz pozycję **Usuń**.
1. Potwierdź, że chcesz usunąć kontener.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia kontenera i przekazywania obiektu BLOB z Azure Portal. Aby dowiedzieć się więcej o pracy z usługą BLOB Storage z poziomu aplikacji sieci Web, przejdź do samouczka, w którym pokazano, jak przekazywać obrazy do konta magazynu.

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie danych obrazu do chmury za pomocą usługi Azure Storage](storage-upload-process-images.md)