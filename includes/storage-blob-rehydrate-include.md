---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684075"
---
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten jest znany jako nawodnienie i może potrwać wiele godzin. Zalecamy duże rozmiary obiektów blob dla optymalnej wydajności nawodnienia. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu. Obecnie istnieją dwa priorytety ponownego nawodnienia, Wysoki i Standardowy, które można ustawić za pomocą opcjonalnej właściwości *x-ms-rehydrate-priority* w operacji [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) lub [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standardowy priorytet**: Wniosek o nawodnienie zostanie rozpatrzony w kolejności, w jakiej został otrzymany i może potrwać do 15 godzin.
* **Wysoki priorytet:** Wniosek o nawodnienie będzie traktowany priorytetowo w stosunku do żądań standardowych i może zakończyć się w mniej niż 1 godzinę. Wysoki priorytet może trwać dłużej niż 1 godzinę, w zależności od rozmiaru obiektu blob i bieżącego zapotrzebowania. Żądania o wysokim priorytecie są gwarantowane jako priorytetowe w stosunku do żądań priorytetu standardowego.

> [!NOTE]
> Standardowy priorytet jest domyślną opcją nawodnienia dla archiwum. Wysoki priorytet to szybsza opcja, która będzie kosztować więcej niż standardowe nawodnienie priorytetowe i jest zwykle zarezerwowana do użytku w sytuacjach przywracania danych w sytuacjach awaryjnych.

Po zainicjowaniu żądania ponownego nawodnienia nie można go anulować. Podczas procesu nawadniania właściwość obiektu blob *x-ms-access-tier* będzie nadal pokazywała jako archiwum do momentu ukończenia ponownego nawodnienia do warstwy online. Aby potwierdzić stan nawodnienia i postęp, można wywołać [Get Blob Properties,](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) aby sprawdzić *x-ms-archive-status* i *x-ms-rehydrate-priority* właściwości obiektu blob. Stan archiwum może odczytywać "nawodnienie-pending-to-hot" lub "rehydrate-pending-to-cool" w zależności od warstwy docelowej nawadniania. Priorytet nawodnienia wskazuje prędkość "Wysoki" lub "Standardowy". Po zakończeniu stan archiwum i ponownie nawodnienia właściwości priorytetu są usuwane, a właściwość obiektu blob warstwy dostępu zostanie zaktualizowana w celu odzwierciedlenia wybranej warstwy gorącej lub chłodnej.
