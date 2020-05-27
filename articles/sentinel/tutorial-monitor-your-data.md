---
title: Wizualizuj dane przy użyciu skoroszytów Azure Monitor na platformie Azure — wskaźnikowa | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak wizualizować dane przy użyciu skoroszytów na platformie Azure — wskaźnik.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798531"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Samouczek: wizualizowanie i monitorowanie danych



Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md)   z platformą Azure — wskaźnikiem możliwości można wizualizować i monitorować dane przy użyciu systemu Azure wskaźnikowego wdrażania Azure monitor skoroszytów, które zapewniają uniwersalność tworzenia niestandardowych pulpitów nawigacyjnych. Gdy skoroszyty są wyświetlane inaczej na platformie Azure — wskaźnikiem, może być przydatne, aby zobaczyć, jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md). System Azure — wskaźnik kontrolny umożliwia tworzenie niestandardowych skoroszytów w danych, a także udostępnia wbudowane szablony skoroszytów umożliwiające szybkie uzyskiwanie szczegółowych informacji na temat danych, gdy tylko nawiążesz połączenie ze źródłem danych.


Ten samouczek ułatwia wizualizowanie danych na platformie Azure — wskaźnikiem.
> [!div class="checklist"]
> * Używanie wbudowanych skoroszytów
> * Utwórz nowe skoroszyty

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć co najmniej uprawnienia czytelnika skoroszytu lub współautor skoroszytu w grupie zasobów obszaru roboczego wskaźnikowego platformy Azure.

> [!NOTE]
> Skoroszyty, które można zobaczyć na platformie Azure, są zapisywane w grupie zasobów obszaru roboczego wskaźnikowego platformy Azure i są znakowane przez obszar roboczy, w którym zostały utworzone.

## <a name="use-built-in-workbooks"></a>Używanie wbudowanych skoroszytów

1. Przejdź do obszaru **skoroszyty** , a następnie wybierz pozycję **Szablony** , aby wyświetlić pełną listę wbudowanych skoroszytów platformy Azure. Aby zobaczyć, które są istotne dla połączonych typów danych, pole **wymagane typy danych** w każdym skoroszycie wyświetli typ danych obok zielonego znacznika wyboru, jeśli dane są już strumieniowo powiązane z platformą Azure.
  ![Przejdź do skoroszytów](./media/tutorial-monitor-data/access-workbooks.png)
1. Kliknij przycisk **Wyświetl skoroszyt** , aby wyświetlić szablon wypełniony danymi.
  
1. Aby edytować skoroszyt, wybierz pozycję **Zapisz**, a następnie wybierz lokalizację, w której chcesz zapisać plik JSON dla szablonu. 

   > [!NOTE]
   > Spowoduje to utworzenie zasobu platformy Azure na podstawie odpowiedniego szablonu i zapisanie samego szablonu pliku JSON, a nie danych.


1. Wybierz pozycję **Wyświetl skoroszyt**. Następnie kliknij przycisk **Edytuj** znajdujący się u góry. Możesz teraz edytować skoroszyt i dostosować go zgodnie z potrzebami. Aby uzyskać więcej informacji na temat dostosowywania skoroszytu, zobacz jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md).
![Wyświetl skoroszyty](./media/tutorial-monitor-data/workbook-graph.png)
1. Po wprowadzeniu zmian można zapisać skoroszyt. 

1. Można również sklonować skoroszyt: wybierz pozycję **Edytuj** , a następnie pozycję **Zapisz jako**, a następnie zapisz go przy użyciu innej nazwy w ramach tej samej subskrypcji i grupy zasobów. Te skoroszyty są wyświetlane na karcie **Moje skoroszyty** .


## <a name="create-new-workbook"></a>Utwórz nowy skoroszyt

1. Przejdź do obszaru **skoroszyty** , a następnie wybierz pozycję **Dodaj skoroszyt** , aby utworzyć nowy skoroszyt od podstaw.
  ![Przejdź do skoroszytów](./media/tutorial-monitor-data/create-workbook.png)

1. Aby edytować skoroszyt, wybierz opcję **Edytuj**, a następnie w razie potrzeby Dodaj tekst, zapytania i parametry. Aby uzyskać więcej informacji na temat dostosowywania skoroszytu, zobacz jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md). 

1. Podczas kompilowania zapytania Ustaw **Źródło danych** na wartość **dzienniki**, **typ zasobu** jest ustawiony na **log Analytics** a następnie wybierz odpowiednie obszary robocze. 

1. Po utworzeniu skoroszytu Zapisz skoroszyt, aby upewnić się, że jest on zapisany w ramach subskrypcji i grupy zasobów obszaru roboczego wskaźnikowego platformy Azure.

1. Jeśli chcesz zezwolić innym osobom w organizacji na korzystanie ze skoroszytu, w obszarze **Zapisz** wybierz **raporty udostępnione**. Jeśli chcesz, aby ten skoroszyt był dostępny tylko dla Ciebie, wybierz pozycję **Moje raporty**.

1. Aby przełączać się między skoroszytami w obszarze roboczym, możesz wybrać opcję **Otwórz** ![ skoroszyty ](./media/tutorial-monitor-data/switch.png) w górnym okienku dowolnego skoroszytu. W oknie, które jest otwierane z prawej strony, przełącz się między skoroszytami.

   ![Przełącz skoroszyty](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Jak usunąć skoroszyty

Można usuwać skoroszyty, które zostały utworzone na podstawie szablonu wskaźnikowego platformy Azure. 

Aby usunąć dostosowany skoroszyt, na stronie skoroszyty wybierz zapisany skoroszyt, który chcesz usunąć, a następnie wybierz pozycję **Usuń**. Spowoduje to usunięcie zapisanego skoroszytu.

> [!NOTE]
> Spowoduje to usunięcie zasobu, a także wszelkich zmian wprowadzonych w szablonie. Oryginalny szablon pozostanie dostępny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wyświetlania danych na platformie Azure — wskaźnik.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, zobacz [Konfigurowanie zautomatyzowanych reakcji na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
