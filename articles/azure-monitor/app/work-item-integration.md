---
title: Integracja elementów roboczych (wersja zapoznawcza) — Application Insights
description: Dowiedz się, jak tworzyć elementy robocze w witrynie GitHub lub Azure DevOps, korzystając z osadzonych w nich danych Application Insights.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731922"
---
# <a name="work-item-integration-preview"></a>Integracja elementów roboczych (wersja zapoznawcza)

Funkcja integracji elementów roboczych umożliwia łatwe tworzenie elementów roboczych w witrynie GitHub lub Azure DevOps, które mają odpowiednie Application Insights dane.

> [!IMPORTANT]
> Integracja elementów roboczych jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Utwórz i skonfiguruj szablon elementu pracy

1. Aby utworzyć szablon elementu pracy, przejdź do zasobu Application Insights i po lewej stronie w obszarze *Konfigurowanie* wybierz **elementy robocze** , a następnie wybierz pozycję **Utwórz nowy szablon** .

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Zrzut ekranu przedstawiający kartę elementy robocze z wybranym nowym szablonem." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Możesz również utworzyć szablon elementu pracy na podstawie karty szczegóły typu end-to-end transakcji, jeśli żaden szablon nie istnieje. Wybierz zdarzenie i po prawej stronie wybierz pozycję **Utwórz element roboczy**, a następnie **Zacznij od szablonu skoroszytu**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Zrzut ekranu przedstawiający kartę Szczegóły transakcji kompleksowe z poleceniem Utwórz element roboczy, rozpoczynając od wybranego szablonu skoroszytu." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Po wybraniu opcji **Utwórz nowy szablon** można wybrać systemy śledzenia, nawiązać nazwę skoroszytu, połączyć się z wybranym systemem śledzenia i wybrać region przechowywania szablonu (wartość domyślna to region, w którym znajduje się zasób Application Insights). Parametry URL są domyślnymi adresami URL dla repozytorium, na przykład `https://github.com/myusername/reponame` lub `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Zrzut ekranu przedstawiający tworzenie nowego szablonu skoroszytu elementu pracy.":::

## <a name="create-a-work-item"></a>Utwórz element roboczy

 Możesz uzyskać dostęp do nowego szablonu z dowolnych szczegółowych informacji o transakcjach, do których można uzyskać dostęp z poziomu wydajności, niepowodzeń, dostępności lub innych kart.

1. Aby utworzyć element roboczy, przejdź do szczegółów transakcji do końca, wybierz zdarzenie, a następnie wybierz pozycję **Utwórz element roboczy** i wybierz szablon elementu pracy.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Zrzut ekranu przedstawiający kartę Szczegóły transakcji końca do końca z wybranym elementem roboczym Utwórz." lightbox="./media/work-item-integration/create-work-item.png":::

1. Nowa karta w przeglądarce zostanie otwarta w systemie śledzenia SELECT. Na platformie Azure DevOps można utworzyć usterkę lub zadanie, a w usłudze GitHub można utworzyć nowy problem w repozytorium. Nowy element roboczy jest automatycznie tworzony z użyciem informacji kontekstowych dostarczonych przez Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Zrzut ekranu przedstawiający automatycznie utworzony problem z usługą GitHub" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Zrzut ekranu przedstawiający automatyczne tworzenie usterki w usłudze Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Edytowanie szablonu

Aby edytować szablon, przejdź do karty **elementy robocze** w obszarze *Konfiguracja* i wybierz ikonę ołówka obok skoroszytu, który chcesz zaktualizować.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Zrzut ekranu karty elementu pracy z wybraną ikoną Edytuj ołówek.":::

Wybierz ![ ikonę Edytuj Edytuj ](./media/work-item-integration/edit-icon.png) u góry, aby rozpocząć edytowanie szablonu. Szablony elementów roboczych są oparte na [Azure monitor skoroszytach](../visualize/workbooks-overview.md). Informacje o elementach roboczych są generowane przy użyciu języka zapytań słowa kluczowego. Możesz zmodyfikować zapytania, aby dodać do zespołu bardziej istotne znaczenie. Po zakończeniu edycji Zapisz skoroszyt, wybierając ikonę Zapisz ikonę ![ Zapisz ](./media/work-item-integration/save-icon.png) na górnym pasku narzędzi.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Zrzut ekranu przedstawiający skoroszyt szablonów elementów roboczych w trybie edycji." lightbox="./media/work-item-integration/edit-workbook.png":::

Można utworzyć więcej niż jedną konfigurację elementów roboczych i mieć niestandardowy skoroszyt, który będzie spełniał każdy scenariusz. Skoroszyty można również wdrażać, Azure Resource Manager zapewnianie standardowych implementacji w środowiskach.