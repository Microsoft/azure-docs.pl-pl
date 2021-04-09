---
title: Korzystanie z niestandardowego kafelka promocji na pulpitach nawigacyjnych platformy Azure
description: Dowiedz się, jak dodać kafelek z promocji do pulpitu nawigacyjnego platformy Azure w celu wyświetlenia zawartości statycznej
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774460"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Użyj kafelka promocji na pulpitach nawigacyjnych platformy Azure, aby pokazać zawartość niestandardową

Możesz dodać kafelek promocji do pulpitów nawigacyjnych platformy Azure, aby wyświetlić niestandardową zawartość statyczną. Na przykład możesz wyświetlić podstawowe instrukcje, obraz lub zestaw hiperlinków na kafelku promocji.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Dodawanie kafelka promocji do pulpitu nawigacyjnego

1. Wybierz pozycję **pulpit nawigacyjny** na pasku bocznym Azure Portal.

   ![Zrzut ekranu przedstawiający pasek boczny portalu](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. W widoku pulpitu nawigacyjnego wybierz Pulpit nawigacyjny, w którym ma zostać wyświetlona niestandardowa Tabliczka promocji, a następnie wybierz pozycję **Edytuj**.

   ![Zrzut ekranu przedstawiający widok edycji pulpitu nawigacyjnego](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. W **galerii kafelków** Znajdź kafelek o nazwie **promocji** i wybierz pozycję **Dodaj**. Kafelek zostanie dodany do pulpitu nawigacyjnego i zostanie otwarte okienko **Edycja promocji** .

1. Wprowadź wartości dla **tytułu** i **podtytuł**, które są wyświetlane na kafelku po przejściu do innego pola.

   ![Zrzut ekranu przedstawiający wyniki wprowadzania tytułu i podtytuł](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Wybierz jedną z opcji, aby dołączać zawartość promocji: **Edytowanie w tekście** lub **Wstawianie zawartości przy użyciu adresu URL**.

   - Wybierz opcję **Edytowanie w tekście** , jeśli chcesz bezpośrednio wprowadzić pozostałą.

      ![Zrzut ekranu przedstawiający wprowadzanie zawartości wbudowanej](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Wybierz pozycję **Wstaw zawartość przy użyciu adresu URL** , jeśli chcesz używać istniejącej zawartości promocji, która jest hostowana w trybie online.

      ![Zrzut ekranu przedstawiający wprowadzanie adresu URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Aby zwiększyć bezpieczeństwo, można utworzyć plik o promocji i zapisać go w [obiekcie blob konta usługi Azure Storage, na którym włączono szyfrowanie](../storage/common/storage-service-encryption.md), a następnie wskazać plik przy użyciu opcji adresu URL. Zawartość promocji jest zaszyfrowana przy użyciu opcji szyfrowania konta magazynu. Tylko użytkownicy z uprawnieniami do pliku mogą zobaczyć zawartość z promocji na pulpicie nawigacyjnym. Może być konieczne ustawienie reguły [CORS (Cross-Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) na koncie magazynu, aby Azure Portal ( _https://portal.azure.com/_ ) mógł uzyskać dostęp do pliku o promocji w obiekcie blob.

1. Wybierz pozycję **gotowe** , aby zamknąć okienko **Edycja promocji** . Zawartość jest wyświetlana na kafelku promocji, którego rozmiar można zmienić, przeciągając uchwyt w prawym dolnym rogu.

   ![Zrzut ekranu przedstawiający niestandardowy kafelek promocji](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Możliwości i ograniczenia dotyczące promocji

Możesz użyć dowolnej kombinacji zwykłego tekstu, składni promocji i zawartości HTML na kafelku promocji. Azure Portal używa biblioteki Open Source o nazwie _oznaczonej_ do przekształcenia zawartości na HTML, która jest wyświetlana na kafelku. KOD HTML utworzony przez _program jest wstępnie przetworzony przez portal_ przed jego renderowaniem. Ten krok pomaga upewnić się, że dostosowanie nie będzie miało wpływu na zabezpieczenia ani układ portalu. Podczas tego wstępnego przetwarzania jakakolwiek część kodu HTML, która stanowi potencjalne zagrożenie, jest usuwana. Portal nie zezwala na następujące typy zawartości:

* Język JavaScript — `<script>` obliczenia tagów i wbudowanych wersji języka JavaScript są usuwane.
* iframes — `<iframe>` Tagi są usuwane.
* Tagi stylów `<style>` są usuwane. Atrybuty stylu wbudowanego dla elementów HTML nie są oficjalnie obsługiwane. Może się okazać, że niektóre elementy stylu wbudowanego działają dla Ciebie, ale jeśli zakłócają układ portalu, mogą przestać działać w dowolnym momencie. Kafelek promocji jest przeznaczony dla podstawowej, statycznej zawartości, która używa domyślnych stylów portalu.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć niestandardowy pulpit nawigacyjny, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../azure-portal/azure-portal-dashboards.md)
