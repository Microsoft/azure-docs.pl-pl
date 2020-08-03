---
title: Konfigurowanie mapowania zestawu danych w udziale danych platformy Azure
description: Dowiedz się, jak skonfigurować mapowanie zestawu danych dla odebranego udziału za pomocą udziału danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511959"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak skonfigurować mapowanie zestawu danych dla odebranego udziału w udziale danych platformy Azure

W tym artykule opisano sposób konfigurowania mapowania zestawu danych dla odebranego udziału za pomocą udziału danych platformy Azure. Należy to zrobić, Jeśli zaakceptujesz zaproszenie do udziału danych, ale wybrano opcję "Akceptuj i skonfiguruj później" lub jeśli dane są udostępniane w miejscu. Można skonfigurować mapowanie zestawu danych, jeśli trzeba zmienić miejsce docelowe dla danych, które są Ci udostępniane, lub jeśli chcesz otrzymywać dane do SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Przejdź do odebranego udziału danych

W usłudze udział danych platformy Azure przejdź do otrzymanego udziału i wybierz kartę **szczegóły** . 

![Mapowanie zestawu danych](./media/dataset-mapping.png "Mapowanie zestawu danych") 

Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **Usuń z mapowania** , aby usunąć mapowanie istniejącego mapowania. Wybierz pozycję **+ Mapuj na element docelowy** , aby wybrać nowy magazyn docelowy. 

![Mapuj do elementu docelowego](./media/dataset-map-target.png "Mapuj do elementu docelowego") 

## <a name="select-a-new-target-store"></a>Wybierz nowy magazyn docelowy

Wybierz docelowy typ danych, w którym chcesz umieścić dane. W przypadku udostępniania opartego na migawce wszystkie dane, które już istnieją na żadnym wcześniej mapowanym koncie magazynu, nie zostaną automatycznie przeniesione do nowego magazynu docelowego. W przypadku udostępniania w miejscu wybierz magazyn danych w określonej lokalizacji. Lokalizacja to centrum danych platformy Azure, w którym znajduje się źródłowy magazyn danych dostawcy danych.

![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Magazyn docelowy") 

## <a name="select-a-file-format-sql-sources-only"></a>Wybierz format pliku (tylko źródła SQL)

Jeśli dane źródłowe pochodzą ze źródła opartego na języku SQL, można wybrać format, w którym został on odebrany. 

![Wybierz format](./media/sql-file-formats.png "Formaty plików SQL")

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .



