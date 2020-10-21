---
title: Samouczek — prognozowanie wydatków przy użyciu usługi Cloudyn na platformie Azure
description: W tym samouczku przedstawiono sposób prognozowania wydatków za pomocą historycznych danych użycia i wydatków.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 70a5a4bf6981aa115c923559112d3533eebf3086
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132180"
---
# <a name="tutorial-forecast-future-spending"></a>Samouczek: Prognozowanie wydatków w przyszłości

Usługa Cloudyn umożliwia prognozowanie wydatków w przyszłości na podstawie historycznych danych o użyciu usług i wydatkach. Raporty usługi Cloudyn zawierają wszystkie dane dotyczące przewidywanych kosztów. Korzystając z przykładów w tym samouczku, zapoznasz się z przewidywanymi kosztami przy użyciu tych raportów. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Prognozowanie wydatków w przyszłości

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję usługi Cloudyn.

## <a name="forecast-future-spending"></a>Prognozowanie wydatków w przyszłości

Usługa Cloudyn oferuje raporty dotyczące przewidywanych kosztów, ułatwiające prognozowanie kosztów na podstawie użycia usług w czasie. Przede wszystkim ułatwiają one sprawdzenie, czy trendy związane z kosztami nie spowodują przekroczenia planowanych wydatków w organizacji. Możesz używać raportów Przewidywany koszt dla bieżącego miesiąca oraz Przewidywany koszt roczny. Oba raporty przedstawiają prognozę wydatków w przyszłości, o ile użycie usług przez ostatnie 30 dni pozostawało na relatywnie stabilnym poziomie.

Raport Przewidywany koszt dla bieżącego miesiąca przedstawia koszty usług. Przewidywany koszt jest określany na podstawie kosztów z początku bieżącego miesiąca oraz z poprzedniego miesiąca. W menu Raporty w górnej części portalu kliknij pozycję **Koszty** > **Prognoza i budżet** > **Przewidywany koszt dla bieżącego miesiąca**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykładowe informacje wyświetlane w raporcie Przewidywany koszt dla bieżącego miesiąca](./media/tutorial-forecast-spending/project-month01.png)

Na przykładzie widać, które usługi wiązały się z największymi wydatkami. Koszty usług Azure były niższe niż koszty usług AWS. Jeśli chcesz wyświetlić szczegółową prognozę kosztów maszyn wirtualnych platformy Azure, na liście **Filtr** zaznacz pozycję **Azure/VM**.

![Przykład przedstawiający przewidywany koszt maszyny wirtualnej platformy Azure dla bieżącego miesiąca](./media/tutorial-forecast-spending/project-month02.png)

Tak samo możesz wykonać powyższe czynności, aby wyświetlić prognozę miesięcznych kosztów innych interesujących Cię usług.

Raport Przewidywany koszt roczny przedstawia ekstrapolację kosztów usług na najbliższych 12 miesięcy.

W menu Raporty w górnej części portalu kliknij pozycję **Koszty** > **Prognoza i budżet** > **Przewidywany koszt roczny**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład przedstawiający raport Przewidywany koszt roczny](./media/tutorial-forecast-spending/project-annual01.png)

Na przykładzie widać, które usługi wiązały się z największymi wydatkami. Tak jak w przykładzie raportu miesięcznego, koszty usług Azure były niższe niż koszty usług AWS. Jeśli chcesz wyświetlić szczegółową prognozę kosztów maszyn wirtualnych platformy Azure, na liście **Filtr** zaznacz pozycję **Azure/VM**.

![Przykład przedstawiający raport dotyczący przewidywanego kosztu rocznego maszyn wirtualnych](./media/tutorial-forecast-spending/project-annual02.png)

Na powyższej ilustracji przewidywany roczny koszt maszyn wirtualnych platformy Azure wynosi 28 374 USD.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Prognozowanie wydatków w przyszłości


Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać kosztami za pomocą alokacji kosztów i raportów przewidywanych kosztów.

> [!div class="nextstepaction"]
> [Zarządzanie kosztami przy użyciu alokacji kosztów oraz raportów przewidywanych kosztów](./tutorial-manage-costs.md)