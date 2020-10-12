---
title: plik dołączany
description: plik dołączany
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 53d2d47143c5a2cefbd50faca9a02af18ffae315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754608"
---
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Ten proces jest nazywany odpełnieniem i może trwać kilka godzin. Zalecamy duże rozmiary obiektów BLOB w celu uzyskania optymalnej wydajności. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu. Istnieją obecnie dwa priorytety odnoszące się do warstwy "High i standard", które można ustawić za pośrednictwem opcjonalnej właściwości " *x-MS-dehydratacji-Priority" na poziomie* [zestawu](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) lub operacji [kopiowania obiektu](https://docs.microsoft.com/rest/api/storageservices/copy-blob) BLOB.

* **Priorytet standardowy**: żądanie Odnotuj zostanie przetworzone w kolejności, w jakiej zostało odebrane i może trwać do 15 godzin.
* **Wysoki priorytet**: żądanie ponownego wypełniania będzie ustalane według priorytetu w przypadku żądań standardowych i może zakończyć się w ciągu 1 godziny w przypadku obiektów w rozmiarze 10 GB. 

> [!NOTE]
> Priorytet standardowy to domyślna opcja uzupełniania dla archiwum. Wysoki priorytet to szybsza opcja, która będzie kosztowała więcej niż normalne uzupełnianie priorytetów i jest zazwyczaj zarezerwowana do użycia w sytuacjach awaryjnych przywracania danych.
>
> Wysoki priorytet może potrwać dłużej niż 1 godzina, w zależności od rozmiaru obiektu BLOB i bieżącego zapotrzebowania. Priorytet żądań o wysokim priorytecie jest gwarantowany przez żądania standardowego priorytetu.

Po zainicjowaniu żądania ponownego wypełniania nie można go anulować. Podczas procesu ponownego wypełniania Właściwość *x-MS-Access-warstwa* obiektu BLOB będzie nadal wyświetlana jako archiwum do momentu ukończenia ponownego uzupełniania do warstwy online. Aby potwierdzić stan i postęp uzupełniania, możesz wywołać polecenie [Pobierz właściwości obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) , aby sprawdzić właściwości obiektu BLOB *x-MS-Archive-status* i *x-MS-rehydratacji* . Stan archiwum może odczytywać "rehydrat-Pending-to-gorąc" lub "rehydrat-Pending-to-chłodny" w zależności od warstwy docelowej rehydratacji. Priorytet rehydratacji wskazuje prędkość "High" lub "standard". Po zakończeniu wszystkie właściwości stanu archiwum i zmiany priorytetu zostaną usunięte, a właściwość obiektu BLOB warstwy dostępu zostanie zaktualizowana w celu odzwierciedlenia wybranej warstwy gorąca lub chłodna.
