---
title: Terminologia związana z usługą Azure Data Share
description: Poznaj typowe terminy używane do opisywania zasobów używanych w udziale danych platformy Azure (dostawca danych, odbiorca danych, udział danych, subskrypcja udostępniania, migawka, zaproszenie, odbiorca).
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73468560"
---
# <a name="azure-data-share-concepts"></a>Pojęcia dotyczące udziałów danych platformy Azure 

W udziale danych platformy Azure wprowadzono nową terminologię związaną z udostępnianiem danych. W tym artykule opisano niektóre często używane terminy, które mogą być używane w całej usłudze. 

## <a name="data-provider"></a>Dostawca danych

Dostawca danych jest organizacją, która udostępnia dane klientom. Zazwyczaj dostawca danych może być właścicielem lub Curator danych. Dostawcy danych chcą udostępniać dane różnych typów. Niektóre przykłady danych, które mogą być udostępniane dostawcy danych, obejmują dane pierwotne, takie jak punkt sprzedaży lub dane szeregów czasowych. Dostawca danych może również chcieć udostępnić wstępnie przetworzone, nadzorowane dane, które już zawierają analizę i szczegółowe informacje. 

## <a name="data-consumer"></a>Odbiorca danych 

Odbiorca danych to organizacja, która otrzymuje dane od dostawcy danych. Odbiorca danych może chcieć przyłączyć dane udostępnione do swoich danych w celu uzyskania szczegółowych informacji. W niektórych przypadkach odbiorca danych może odbierać dane, które zostały już przetworzone. 

## <a name="data-share"></a>Data Share

Udział danych to grupa zestawów danych, które są udostępniane jako pojedyncza jednostka. Zestawy danych mogą pochodzić z wielu źródeł danych platformy Azure, które są obsługiwane przez usługę Azure Data Share. Obecnie usługa Azure Data Share obsługuje usługi Azure Blob Storage i Azure Data Lake Store. 

## <a name="share-subscription"></a>Udostępnianie subskrypcji 

Subskrypcja udziału jest tworzona, gdy odbiorca danych akceptuje zaproszenie do udziału danych od dostawcy danych. Dostawcy danych mogą przeglądać aktywne subskrypcje udziałów, przechodząc do **wysłanych udziałów** na koncie usługi Azure Data Share i wybierając pozycję **Udostępnij subskrypcje**.

Odbiorca danych może sprawdzić, czy ma aktywną subskrypcję udziałów, przechodząc do **odebranych udziałów** i wyświetlając stan odebranych udziałów. 

## <a name="snapshot"></a>Snapshot

Migawkę można utworzyć przez odbiorcę danych po zaakceptowaniu zaproszenia udziału danych. Po zaakceptowaniu zaproszenia mogą wyzwalać pełną migawkę danych, które są do nich udostępniane. Migawka jest kopią danych w punkcie w czasie, w którym odbiorca danych wygenerował migawkę. 

Istnieją dwa typy migawek — pełne i przyrostowe. Pełna migawka zawiera wszystkie dane w udziale danych. Migawka przyrostowa zawiera wszystkie dane, które zostały zaktualizowane/dodane od momentu wyzwolenia ostatniej migawki. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ustawienia migawki w udziale danych platformy Azure
 
Dostawca danych może włączyć ustawienie migawki dla udziału danych. To ustawienie umożliwia konsumentom danych otrzymywanie aktualizacji przyrostowych w miarę ich występowania. To ustawienie powinno być włączone, jeśli dostawca danych ma otrzymywać aktualizacje dla danych, które zostały udostępnione. 

Jeśli dostawca danych włączy to ustawienie, można wybrać interwał cyklu. Interwał cyklu może być godzinny lub codzienny. 

Odbiorca danych ma możliwość wyboru tego harmonogramu migawki w celu otrzymywania aktualizacji przyrostowych, które obejmują wszystkie dane, które uległy zmianie od czasu pierwszej generacji nowej migawki. 

## <a name="invitation"></a>Zaproszenie

Dostawca danych może zaprosić wielu odbiorców do swojego udziału danych. Można to zrobić, dodając odbiorców do udziału danych. Zaproszenia można także dodawać po utworzeniu udziału danych. 

Dostawca danych może usunąć zaproszenie po jego wysłaniu, jeśli nie zostało ono zaakceptowane. Jeśli dostawca danych usunie zaproszenie i nie zostało jeszcze zaakceptowane, konsument danych nie będzie mógł go zaakceptować. 

Zaproszenia można wysyłać do pięciu razy dziennie. 

## <a name="recipient"></a>Adresat

Odbiorca to osoba, która otrzymuje zaproszenie do udziału danych. Zazwyczaj dostawca danych doda odbiorców do udziału danych, który tworzy. Gdy odbiorca zaproszenia zaakceptuje zaproszenie, staje się konsumentem danych.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
