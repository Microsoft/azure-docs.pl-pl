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
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491499"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Samouczek: wizualizowanie i monitorowanie danych

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) z platformą Azure — wskaźnikiem możliwości można wizualizować i monitorować dane przy użyciu systemu Azure wskaźnikowego wdrażania Azure monitor skoroszytów, które zapewniają uniwersalność tworzenia niestandardowych pulpitów nawigacyjnych. Gdy skoroszyty są wyświetlane inaczej na platformie Azure — wskaźnikiem, może być przydatne, aby zobaczyć, jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/visualize/workbooks-overview.md). System Azure — wskaźnik kontrolny umożliwia tworzenie niestandardowych skoroszytów w danych, a także udostępnia wbudowane szablony skoroszytów umożliwiające szybkie uzyskiwanie szczegółowych informacji na temat danych, gdy tylko nawiążesz połączenie ze źródłem danych.

Ten samouczek ułatwia wizualizowanie danych na platformie Azure — wskaźnikiem.
> [!div class="checklist"]
> * Używanie wbudowanych skoroszytów
> * Utwórz nowe skoroszyty

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć co najmniej uprawnienia **czytelnika skoroszytu** lub **współautor skoroszytu** w grupie zasobów obszaru roboczego wskaźnikowego platformy Azure.

> [!NOTE]
> Skoroszyty, które można zobaczyć na platformie Azure, są zapisywane w grupie zasobów obszaru roboczego wskaźnikowego platformy Azure i są znakowane przez obszar roboczy, w którym zostały utworzone.

## <a name="use-built-in-workbooks"></a>Używanie wbudowanych skoroszytów

1. Przejdź do obszaru **skoroszyty** , a następnie wybierz pozycję **Szablony** , aby wyświetlić pełną listę wbudowanych skoroszytów platformy Azure. 

    Aby zobaczyć, które są istotne dla połączonych typów danych, pole **wymagane typy danych** w każdym skoroszycie wyświetli typ danych obok zielonego znacznika wyboru, jeśli dane są już strumieniowo powiązane z platformą Azure.

    [![Przejdź do skoroszytów. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Wybierz pozycję **Wyświetl szablon** , aby wyświetlić szablon wypełniony danymi.

1. Aby edytować skoroszyt, wybierz pozycję **Zapisz**, a następnie wybierz lokalizację, w której chcesz zapisać plik JSON dla szablonu.

   > [!NOTE]
   > Spowoduje to utworzenie zasobu platformy Azure na podstawie odpowiedniego szablonu i zapisanie pliku JSON skoroszytu, a nie danych.


1. Wybierz pozycję **Wyświetl zapisany skoroszyt**. 

    [![Wyświetl skoroszyty. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Wybierz przycisk **Edytuj** na pasku narzędzi skoroszytu, aby dostosować skoroszyt zgodnie z potrzebami. Gdy skończysz, wybierz pozycję **Zapisz** , aby zapisać zmiany.

    Aby uzyskać więcej informacji, zobacz jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Aby sklonować skoroszyt, wybierz pozycję **Edytuj** , a następnie **Zapisz jako**, a następnie zapisz go przy użyciu innej nazwy w ramach tej samej subskrypcji i grupy zasobów.
> Sklonowane skoroszyty są wyświetlane na karcie **Moje skoroszyty** .
>
## <a name="create-new-workbook"></a>Utwórz nowy skoroszyt

1. Przejdź do obszaru **skoroszyty** , a następnie wybierz pozycję **Dodaj skoroszyt** , aby utworzyć nowy skoroszyt od podstaw.

    [![Nowy skoroszyt. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Aby edytować skoroszyt, wybierz opcję **Edytuj**, a następnie w razie potrzeby Dodaj tekst, zapytania i parametry. Aby uzyskać więcej informacji na temat dostosowywania skoroszytu, zobacz jak [tworzyć interaktywne raporty przy użyciu skoroszytów Azure monitor](../azure-monitor/visualize/workbooks-overview.md). 

1. Podczas kompilowania zapytania upewnij się, że **Źródło danych** ma ustawioną wartość **dzienniki** i **typ zasobu** jest ustawiony na **log Analytics**, a następnie wybierz odpowiednie obszary robocze. 

1. Po utworzeniu skoroszytu Zapisz skoroszyt, a następnie zapisz go w obszarze subskrypcji i grupy zasobów obszaru roboczego wskaźnikowego platformy Azure.

1. Jeśli chcesz zezwolić innym osobom w organizacji na korzystanie ze skoroszytu, w obszarze **Zapisz** wybierz **raporty udostępnione**. Jeśli chcesz, aby ten skoroszyt był dostępny tylko dla Ciebie, wybierz pozycję **Moje raporty**.

1. Aby przełączać się między skoroszytami w obszarze roboczym, wybierz pozycję **Otwórz** ![ , aby otworzyć skoroszyt.](./media/tutorial-monitor-data/switch.png) na pasku narzędzi dowolnego skoroszytu. Ekran przechodzi do listy innych skoroszytów, do których można się przełączyć.

    Wybierz skoroszyt, który chcesz otworzyć:

    [![Przełączanie skoroszytów. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Odświeżanie danych skoroszytu

Odśwież skoroszyt, aby wyświetlić zaktualizowane dane. Na pasku narzędzi wybierz jedną z następujących opcji:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::**Odśwież**, aby ręcznie odświeżyć dane skoroszytu.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Automatycznie Odświeżaj**, aby ustawić skoroszyt do automatycznego odświeżania w skonfigurowanym interwale.

    - Obsługiwane Interwały autoodświeżania mieszczą się w zakresie od **5 minut** do **1 dnia**.

    - Autoodświeżanie jest wstrzymane podczas edytowania skoroszytu, a interwały są uruchamiane ponownie przy każdym przełączeniu do trybu widoku z trybu edycji.

    - Interwały autoodświeżania są również uruchamiane ponownie, jeśli ręcznie odświeżasz dane.

    > [!TIP]
    > Domyślnie funkcja AutoRefresh jest wyłączona. Aby zoptymalizować wydajność, Autoodświeżanie jest również wyłączone przy każdym zamknięciu skoroszytu i nie jest uruchamiane w tle. Włącz Autoodświeżanie w razie konieczności przy następnym otwarciu skoroszytu.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Drukuj skoroszyt lub Zapisz jako plik PDF

Aby wydrukować skoroszyt lub zapisać go jako plik PDF, użyj menu Opcje z prawej strony tytułu skoroszytu.

1. Wybierz opcje > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Drukuj zawartość**. 
2. Na ekranie drukowania Dostosuj ustawienia drukowania odpowiednio do potrzeb lub wybierz pozycję **Zapisz jako plik PDF** , aby zapisać je lokalnie.

Na przykład:

[![Wydrukuj skoroszyt lub Zapisz jako plik PDF. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Jak usunąć skoroszyty

Aby usunąć zapisany skoroszyt (zapisany szablon lub dostosowany skoroszyt), na stronie skoroszyty wybierz zapisany skoroszyt, który chcesz usunąć, a następnie wybierz pozycję **Usuń**. Spowoduje to usunięcie zapisanego skoroszytu.

> [!NOTE]
> Spowoduje to usunięcie zasobu skoroszytu oraz wszelkich zmian wprowadzonych w szablonie. Oryginalny szablon pozostanie dostępny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wizualizacji danych w usłudze Azure wskaźnikowej przy użyciu skoroszytów platformy Azure.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, zobacz [Konfigurowanie zautomatyzowanych reakcji na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
