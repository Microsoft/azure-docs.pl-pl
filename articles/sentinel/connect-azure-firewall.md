---
title: Łączenie danych zapory platformy Azure z usługą Azure wskaźnikowego
description: Dowiedz się, jak połączyć dane zapory platformy Azure z wskaźnikiem kontrolnym platformy Azure.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05a2869152def0fc9a44defaa21f0643d9fdbb08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504880"
---
# <a name="connect-data-from-azure-firewall"></a>Łączenie danych z zapory platformy Azure

> [!IMPORTANT]
> Łącznik danych zapory platformy Azure w usłudze Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury. 

Dzienniki zapory platformy Azure można połączyć z platformą Azure wskaźnikiem wydajności, co pozwala na wyświetlanie danych dziennika w skoroszytach, korzystanie z nich do tworzenia niestandardowych alertów i uwzględnianie ich w celu usprawnienia badania.

Dowiedz się więcej o [monitorowaniu dzienników zapory platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

## <a name="connect-to-azure-firewall"></a>Nawiązywanie połączenia z zaporą platformy Azure
    
1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz pozycję **Zapora platformy Azure** z galerii łączników danych, a następnie wybierz pozycję **Otwórz stronę łącznika**  w okienku podglądu.

1. Włącz **dzienniki diagnostyczne** na wszystkich zaporach, których dzienniki chcesz połączyć:

    1. Wybierz łącze **Otwórz zasób zapory platformy Azure >** .

    1. Z menu nawigacji **zapory** wybierz pozycję **Ustawienia diagnostyczne**.

    1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** u dołu listy.

    1. Na ekranie **Ustawienia diagnostyki** wprowadź nazwę w polu  **Nazwa ustawień diagnostycznych** .
    
    1. Zaznacz pole wyboru **Wyślij do log Analytics** . Zostaną wyświetlone dwa nowe pola poniżej. Wybierz odpowiednią **subskrypcję** i **log Analytics obszar roboczy** (gdzie znajduje się wskaźnik platformy Azure).

    1. Zaznacz pola wyboru typów reguł, których dzienników chcesz pozyskać. Zalecamy **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule**.

    1. Wybierz pozycję **Zapisz** w górnej części ekranu.

1. Aby użyć odpowiedniego schematu w Log Analytics na potrzeby alertów dotyczących zapory platformy Azure, wyszukaj ciąg **AzureDiagnostics**.

> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia dzienników zapory platformy Azure z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
