---
title: Monitoruj aktywność chmury prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje informacje dostępne dla działania w ramach rozwiązania Azure VMware przez środowisko CloudSimple, w tym alerty, zdarzenia, zadania i inspekcje.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb597cc71d228a1daaaa4e52d76b301764ff9292
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899307"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitoruj rozwiązanie VMware według działania CloudSimple

Dzienniki aktywności CloudSimple zapewniają wgląd w operacje wykonywane w portalu CloudSimple.  Lista zawiera alerty, zdarzenia, zadania i inspekcje.  Użyj dzienników aktywności, aby określić, kto i kiedy operacje zostały wykonane.  Dzienniki aktywności nie obejmują żadnych operacji odczytu wykonywanych przez użytkownika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informacje o działaniu

Aby uzyskać dostęp do stron działań, wybierz pozycję **działanie** w menu po stronie.

![Przegląd strony działania](media/activity-page-overview.png)

Aby wyświetlić szczegółowe informacje o działaniach na stronie działania, wybierz działanie. Po prawej stronie zostanie otwarty panel szczegóły. Akcje w panelu są zależne od typu działania. Kliknij przycisk **X** , aby zamknąć panel.

Kliknij nagłówek kolumny, aby posortować ekran.  Można filtrować kolumny pod kątem określonych wartości do wyświetlenia.  Pobierz raport dotyczący aktywności, klikając ikonę **Pobierz jako plik CSV** .

## <a name="alerts"></a>Alerty

Alerty są powiadomieniami o wszelkich znaczących działaniach w środowisku CloudSimple.  Alerty obejmują zdarzenia, które mają wpływ na rozliczenia lub dostęp użytkownika.

Aby potwierdzić alerty i usunąć je z listy, wybierz co najmniej jedną z listy i kliknij przycisk **Potwierdź**.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Typ alertu | Kategoria alertu.|
| Godzina | Czas wystąpienia alertu. |
| Ważność | Istotność alertu.|
| Nazwa zasobu | Nazwa przypisana do zasobu, taka jak nazwa chmury prywatnej. |
| Typ zasobu | Kategoria zasobu: Chmura prywatna, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Opis | Opis wyzwalania alertu. |
| Potwierdzony | Wskazanie, czy alert został potwierdzony. |

## <a name="events"></a>Zdarzenia

Zdarzenia pokazują aktywność użytkownika i systemu w portalu CloudSimple. Na stronie zdarzenia jest wyświetlana aktywność skojarzona z określonym zasobem i ważność wpływu.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Godzina | Data i godzina wystąpienia zdarzenia. |
| Typ zdarzenia | Kod liczbowy, który identyfikuje zdarzenie. |
| Ważność | Ważność zdarzenia.|
| Nazwa zasobu | Nazwa przypisana do zasobu, taka jak nazwa chmury prywatnej. |
| Typ zasobu | Kategoria zasobu: Chmura prywatna, stojak w chmurze. |
| Opis | Opis wyzwalania alertu. |

## <a name="tasks"></a>Zadania

Zadania są działaniami w chmurze prywatnej, które powinny trwać 30 sekund lub dłużej. (Działania, które powinny zająć mniej niż 30 sekund, są raportowane tylko jako zdarzenia). Otwórz strony zadań, aby śledzić postęp zadań w chmurze prywatnej.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Identyfikator zadania | Unikatowy identyfikator zadania. |
| Operacja | Akcja wykonywana przez zadanie. |
| Użytkownik | Użytkownik przypisany do ukończenia zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobu: Chmura prywatna, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Rozpocznij | Godzina rozpoczęcia zadania. |
| End | Godzina zakończenia zadania. |
| Stan | Bieżący stan zadania. |
| Upłynęło czasu | Czas trwania zadania (jeśli został ukończony) lub trwa jego wykonywanie (jeśli jest w toku). |
| Opis | Opis zadania. |

## <a name="audit"></a>Inspekcja

Dzienniki inspekcji śledzą aktywność użytkownika. Dzienników inspekcji można użyć do monitorowania aktywności użytkowników dla wszystkich użytkowników.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Godzina | Godzina wpisu inspekcji. |
| Operacja | Akcja wykonywana przez zadanie. |
| Użytkownik | Użytkownik przypisany do zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobu: Chmura prywatna, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Wynik | Wynik działania, na przykład **powodzenie**. |
| Czas trwania | Czas na ukończenie zadania. |
| Opis | Opis akcji. |

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
