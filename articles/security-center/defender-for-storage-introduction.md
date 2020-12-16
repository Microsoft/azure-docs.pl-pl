---
title: Azure Defender for Storage — zalety i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla magazynu.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516522"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Wprowadzenie do usługi Azure Defender dla magazynu


**Usługa Azure Defender for Storage** to oparta na platformie Azure warstwa analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Wykorzystuje zaawansowane funkcje zabezpieczeń AI i [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) , aby zapewnić kontekstowe alerty i zalecenia dotyczące zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty są zintegrowane z usługą Azure Security Center i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|**Usługa Azure Defender dla magazynu** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Chronione typy magazynów:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jakiego rodzaju alerty zapewnia usługa Azure Defender dla magazynu?

Alerty zabezpieczeń są wyzwalane w przypadku:

- **Podejrzane wzorce dostępu** — takie jak pomyślne dostęp z węzła wyjścia tor lub z adresu IP uznawane za podejrzane przez program Microsoft Threat Intelligence
- **Podejrzane działania** — takie jak nietypowe wyodrębnianie danych lub nietypowa zmiana uprawnień dostępu
- **Przekazywanie złośliwej zawartości** — takich jak potencjalne pliki złośliwego oprogramowania (w oparciu o analizę reputacji) lub hostowanie zawartości wyłudzające informacje

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń. Alerty można eksportować do usługi Azure wskaźnikowej lub innego SIEM innej firmy lub dowolnego innego narzędzia zewnętrznego.

> [!TIP]
> Najlepszym rozwiązaniem jest [skonfigurowanie usługi Azure Defender for Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) na poziomie subskrypcji, ale można [ją również skonfigurować na poszczególnych kontach magazynu](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Co to jest analiza reputacji w przypadku złośliwego oprogramowania?

Aby określić, czy przekazany plik jest podejrzany, usługa Azure Defender for Storage używa analizy reputacji skrótu obsługiwanej przez [program Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Narzędzia ochrony przed zagrożeniami nie skanują przekazanych plików, a zamiast nich sprawdzają dzienniki magazynu i porównują skróty nowo przekazanych plików z tymi znanymi wirusami, koniami trojańskimi, programami szpiegującymi i oprogramowaniem wymuszającego okup. 

W przypadku podejrzenia, że plik zawiera złośliwe oprogramowanie, Security Center wyświetla alert i opcjonalnie może wysłać wiadomość e-mail do właściciela magazynu w celu zatwierdzenia, aby usunąć podejrzany plik. Aby skonfigurować to automatyczne usuwanie plików, których analiza reputacji jest określona, zawiera złośliwe oprogramowanie, wdróż [automatyzację przepływu pracy, aby wyzwolić alerty zawierające "potencjalne złośliwe oprogramowanie przekazane do konta magazynu"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Aby włączyć funkcje ochrony przed zagrożeniami Security Center, należy włączyć usługę Azure Defender w ramach subskrypcji zawierającej odpowiednie obciążenia.
>
> **Usługę Azure Defender Storage** można włączyć na poziomie subskrypcji lub poziomu zasobów.



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla magazynu.

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- Czy alert jest generowany przez Security Center, czy odbierany przez Security Center z innego produktu zabezpieczeń, można go wyeksportować. Aby wyeksportować alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).
- [Jak włączyć funkcję Advanced Defender for Storage](../storage/common/azure-defender-storage-configure.md)
- [Lista alertów usługi Azure Defender dla magazynu](alerts-reference.md#alerts-azurestorage)
- [Możliwości analizy zagrożeń firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)
