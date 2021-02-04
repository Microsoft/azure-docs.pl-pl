---
title: Azure Defender for Storage — zalety i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla magazynu.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb1635cec2b0bcf7f2c13101b2aeab25a869dc66
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558529"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Wprowadzenie do usługi Azure Defender dla magazynu

**Usługa Azure Defender for Storage** to oparta na platformie Azure warstwa analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Wykorzystuje zaawansowane funkcje zabezpieczeń AI i [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) , aby zapewnić kontekstowe alerty i zalecenia dotyczące zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. Te alerty są zintegrowane z usługą Azure Security Center i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|**Usługa Azure Defender dla magazynu** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Chronione typy magazynów:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Jakie są korzyści z usługi Azure Defender dla magazynu?

Usługa Azure Defender dla magazynu oferuje następujące informacje:

- **Zabezpieczenia natywne platformy Azure** — w przypadku włączenia 1-kliknięcia usługa Defender for Storage chroni dane przechowywane w obiektach Blob platformy Azure, Azure Files i jeziorach danych. Usługa Defender for Storage w ramach usługi Azure Native Service oferuje scentralizowane zabezpieczenia dla wszystkich zasobów danych zarządzanych przez platformę Azure i jest zintegrowana z innymi usługami zabezpieczeń platformy Azure, takimi jak Azure — wskaźnikiem.
- **Rozbudowany zestaw wykrywania** , oparty na usłudze Microsoft Threat Intelligence, wykrywanie w usłudze Defender dla magazynu obejmują największe zagrożenia dla magazynu, takie jak dostęp anonimowy, naruszone poświadczenia, Inżynieria społecznościowa, nadużycia uprawnień i złośliwa zawartość.
- **Odpowiedzi w** narzędziach automatyzacji Security Center skalowania ułatwiają zapobieganie zidentyfikowanym zagrożeniom i reagowanie na nie. Dowiedz się więcej w [automatyzowaniu odpowiedzi na wyzwalacze Security Center](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Ogólne omówienie funkcji usługi Azure Defender dla magazynu":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jakiego rodzaju alerty zapewnia usługa Azure Defender dla magazynu?

Alerty zabezpieczeń są wyzwalane w przypadku:

- **Podejrzane wzorce dostępu** — takie jak pomyślne dostęp z węzła wyjścia tor lub z adresu IP uznawane za podejrzane przez program Microsoft Threat Intelligence
- **Podejrzane działania** — takie jak nietypowe wyodrębnianie danych lub nietypowa zmiana uprawnień dostępu
- **Przekazywanie złośliwej zawartości** — takich jak potencjalne pliki złośliwego oprogramowania (w oparciu o analizę reputacji) lub hostowanie zawartości wyłudzające informacje

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń. Alerty można eksportować do usługi Azure wskaźnikowej lub innego SIEM innej firmy lub dowolnego innego narzędzia zewnętrznego.

> [!TIP]
> Najlepszym rozwiązaniem jest [skonfigurowanie usługi Azure Defender for Storage](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) na poziomie subskrypcji, ale można [ją również skonfigurować na poszczególnych kontach magazynu](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Co to jest analiza reputacji w przypadku złośliwego oprogramowania?

Aby określić, czy przekazany plik jest podejrzany, usługa Azure Defender for Storage używa analizy reputacji skrótu obsługiwanej przez [program Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Narzędzia ochrony przed zagrożeniami nie skanują przekazanych plików, a zamiast nich sprawdzają dzienniki magazynu i porównują skróty nowo przekazanych plików z tymi znanymi wirusami, koniami trojańskimi, programami szpiegującymi i oprogramowaniem wymuszającego okup. 

W przypadku podejrzenia, że plik zawiera złośliwe oprogramowanie, Security Center wyświetla alert i opcjonalnie może wysłać wiadomość e-mail do właściciela magazynu w celu zatwierdzenia, aby usunąć podejrzany plik. Aby skonfigurować to automatyczne usuwanie plików, których analiza reputacji jest określona, zawiera złośliwe oprogramowanie, wdróż [automatyzację przepływu pracy, aby wyzwolić alerty zawierające "potencjalne złośliwe oprogramowanie przekazane do konta magazynu"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Aby włączyć funkcje ochrony przed zagrożeniami Security Center, należy włączyć usługę Azure Defender w ramach subskrypcji zawierającej odpowiednie obciążenia.
>
> **Usługę Azure Defender Storage** można włączyć na poziomie subskrypcji lub poziomu zasobów.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Wyzwalanie alertu testowego usługi Azure Defender dla magazynu

Aby przetestować alerty zabezpieczeń z usługi Azure Defender dla magazynu w środowisku, wygeneruj Alert "dostęp z węzła zakończenia sieci Tor do konta magazynu", wykonując następujące czynności:

1. Otwórz konto magazynu za pomocą usługi Azure Defender dla magazynu.
1. Na pasku bocznym wybierz pozycję "kontenery" i Otwórz istniejący kontener lub Utwórz nowy.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Otwieranie kontenera obiektów blob z konta usługi Azure Storage" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Przekaż plik do tego kontenera.

    > [!CAUTION]
    > Nie przekazuj pliku zawierającego poufne dane.

1. Użyj menu kontekstowego w załadowanym pliku, aby wybrać polecenie Generuj sygnaturę dostępu współdzielonego.

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Opcja Generuj sygnaturę dostępu współdzielonego dla pliku w kontenerze obiektów BLOB":::

1. Pozostaw opcje domyślne i wybierz pozycję **Generuj token SAS i adres URL**.

1. Skopiuj wygenerowany adres URL sygnatury dostępu współdzielonego.

1. Na komputerze lokalnym Otwórz przeglądarkę sieci Tor.

    > [!TIP]
    > Tor można pobrać z witryny sieci Tor [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. W przeglądarce tor przejdź do adresu URL sygnatury dostępu współdzielonego.

1. Pobierz plik przekazany w kroku 3.

    W ciągu dwóch godzin otrzymasz następujący alert zabezpieczeń z Security Center:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Alert zabezpieczeń dotyczący dostępu z węzła zakończenia tor":::

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla magazynu.

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- Czy alert jest generowany przez Security Center, czy odbierany przez Security Center z innego produktu zabezpieczeń, można go wyeksportować. Aby wyeksportować alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).
- [Jak włączyć funkcję Advanced Defender for Storage](../storage/common/azure-defender-storage-configure.md)
- [Lista alertów usługi Azure Defender dla magazynu](alerts-reference.md#alerts-azurestorage)
- [Możliwości analizy zagrożeń firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)