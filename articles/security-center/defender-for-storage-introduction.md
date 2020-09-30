---
title: Azure Defender for Storage — zalety i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla magazynu.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4677426337a48d4fde74f61b8a4ad6fcb695f420
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577819"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Wprowadzenie do usługi Azure Defender dla magazynu

**Usługa Azure Defender for Storage** wykrywa potencjalnie szkodliwe działanie na kontach usługi Azure Storage. Dane można chronić, niezależnie od tego, czy są przechowywane jako kontenery obiektów blob, udziały plików czy jeziora danych.

Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń *bez* konieczności posiadania eksperta zabezpieczeń oraz zarządzania systemami monitorowania zabezpieczeń.


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|**Usługa Azure Defender dla magazynu** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Chronione typy magazynów:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jakiego rodzaju alerty zapewnia usługa Azure Defender dla magazynu?

Alerty zabezpieczeń są wyzwalane w przypadku:

- **Podejrzane działanie** — na przykład dostęp do konta magazynu powiodło się z adresu IP, który jest znany jako aktywny węzeł zakończenia tor
- **Nietypowe zachowanie** — na przykład zmiany w wzorcu dostępu do konta magazynu
- **Przekazane potencjalne złośliwe oprogramowanie** — analiza reputacji skrótu wskazuje, że przekazany plik zawiera złośliwe oprogramowanie

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń.

> [!TIP]
> Alerty magazynu można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


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
- [Jak włączyć funkcję Advanced Defender for Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Lista alertów usługi Azure Defender dla magazynu](alerts-reference.md#alerts-azurestorage)
- [Możliwości analizy zagrożeń firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)