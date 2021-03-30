---
title: Konfigurowanie mapowania zestawu danych w udziale danych platformy Azure
description: Dowiedz się, jak skonfigurować mapowanie zestawu danych dla odebranego udziału za pomocą udziału danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257838"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak skonfigurować mapowanie zestawu danych dla odebranego udziału w udziale danych platformy Azure

W tym artykule opisano sposób konfigurowania mapowania zestawu danych dla odebranego udziału za pomocą udziału danych platformy Azure. Możesz chcieć skonfigurować mapowanie zestawu danych, aby określić docelowy magazyn danych, w którym mają zostać odebrane dane, lub jeśli trzeba zmienić docelowy magazyn danych.

## <a name="navigate-to-a-received-data-share"></a>Przejdź do odebranego udziału danych

W usłudze Azure Data Share przejdź do odebranego udziału i wybierz kartę **zestawy** danych. 

![Mapowanie zestawu danych](./media/dataset-mapping.png "Mapowanie zestawu danych") 

Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **+ Mapuj na element docelowy** , aby wybrać nowy magazyn docelowy. Wybierz opcję **Usuń z mapowania** najpierw, jeśli zestaw danych jest już zamapowany i chcesz zmienić docelowy magazyn danych.

![Mapuj do elementu docelowego](./media/dataset-map-target.png "Mapuj do elementu docelowego") 

## <a name="select-a-target-store"></a>Wybierz magazyn docelowy

Wybierz docelowy typ magazynu danych, w którym chcesz umieścić dane. W przypadku udostępniania opartego na migawce wszystkie dane, które już istnieją na żadnym wcześniej mapowanym koncie magazynu, nie zostaną automatycznie przeniesione do nowego magazynu docelowego. W przypadku udostępniania w miejscu wybierz magazyn danych w określonej lokalizacji. Lokalizacja to centrum danych platformy Azure, w którym znajduje się źródłowy magazyn danych dostawcy danych.

![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Magazyn docelowy") 

## <a name="select-a-file-format-sql-sources-only"></a>Wybierz format pliku (tylko źródła SQL)

Jeśli dane źródłowe pochodzą ze źródła opartego na języku SQL, a chcesz je otrzymać jako plik, możesz wybrać format, w którym został on odebrany. 

![Wybierz format](./media/sql-file-formats.png "Formaty plików SQL")

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .



