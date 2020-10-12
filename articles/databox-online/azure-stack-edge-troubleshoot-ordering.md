---
title: Użyj Azure Portal, aby rozwiązać problemy związane z porządkowaniem Azure Stack EDGE Pro Microsoft Docs
description: Opisuje sposób rozwiązywania problemów związanych z porządkowaniem Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: 3087c209b844d691173edbbd8a993c1d0aae319b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903645"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Rozwiązywanie problemów związanych z porządkowaniem programu Azure Stack EDGE Pro

W tym artykule opisano sposób rozwiązywania problemów związanych z porządkowaniem programu Azure Stack EDGE Pro.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Rozwiązywanie problemów z porządkowaniem

## <a name="unsupported-subscription-or-region"></a>Nieobsługiwana subskrypcja lub region

**Opis błędu:** W Azure Portal, jeśli wystąpi błąd:

*Wybrana subskrypcja lub region nie jest obsługiwany. Wybierz inną subskrypcję lub region.*

![Nieobsługiwana subskrypcja lub region](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Sugerowane rozwiązanie:**  Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). Subskrypcje z płatność zgodnie z rzeczywistym użyciem nie są obsługiwane. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge — wymagania wstępne dotyczące zasobów](azure-stack-edge-deploy-prep.md#prerequisites).

Istnieje możliwość, że firma Microsoft może zezwolić na uaktualnienie typu subskrypcji na zasadzie przypadku. Skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/) , aby móc zrozumieć Twoje potrzeby i odpowiednio dostosować te limity.

## <a name="selected-subscription-type-not-supported"></a>Wybrany typ subskrypcji nie jest obsługiwany

**Błąd:** Masz subskrypcję z umową EA, CSP lub sponsorowaną i zostanie wyświetlony następujący błąd:

*Wybrany typ subskrypcji nie jest obsługiwany. Upewnij się, że używasz obsługiwanej subskrypcji. [Dowiedz się więcej](azure-stack-edge-deploy-prep.md#prerequisites). W przypadku korzystania z obsługiwanego typu subskrypcji upewnij się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, zobacz [Rejestrowanie dostawcy zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Sugerowane rozwiązanie:** Wykonaj następujące kroki, aby zarejestrować dostawcę zasobów Azure Stack Edge:

1. W Azure Portal przejdź do pozycji **Home**  >  **subskrypcje**główne.

2. Wybierz subskrypcję, która będzie używana do zamawiania urządzenia.

3. Wybierz pozycję **dostawcy zasobów** , a następnie wyszukaj ciąg **Microsoft. DataBoxEdge**.

    ![Rejestrowanie dostawcy zasobów](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Jeśli nie masz dostępu właściciela lub współautora do zarejestrowania dostawcy zasobów, zostanie wyświetlony następujący błąd: * &lt; Nazwa subskrypcji subskrypcji &gt; nie ma uprawnień do rejestrowania dostawców zasobów: Microsoft. DataBoxEdge.*

Aby uzyskać więcej informacji, zobacz [Rejestrowanie dostawców zasobów](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Nie zarejestrowano elementu Microsoft. DataBoxEdge dla subskrypcji

**Błąd:** W Azure Portal wybierz subskrypcję, która ma być używana dla Azure Stack EDGE Pro lub Data Box Gateway i uzyskaj następujący błąd:

*Dostawcy zasobów: Microsoft. DataBoxEdge nie są zarejestrowane dla &lt; nazwy subskrypcji subskrypcji &gt; i nie masz uprawnień do zarejestrowania dostawcy zasobów dla &lt; nazwy &gt; subskrypcji subskrypcji*.

**Sugerowane rozwiązanie:** Podnieś poziom dostępu do subskrypcji lub Znajdź kogoś z dostępem właściciela lub współautora w celu zarejestrowania dostawcy zasobów.

## <a name="resource-disallowed-by-policy"></a>Zasób niedozwolony przez zasady

**Błąd:** W Azure Portal próbujesz zarejestrować dostawcę zasobów i uzyskać następujący błąd:

*&lt;Nazwa zasobu zasobu &gt; została niedozwolona przez zasady. (Kod: RequestDisallowedByPolicy). Inicjatywa: Odmów zwykle niechcianych typów zasobów. Zasady: Niedozwolone typy zasobów.*

**Sugerowane rozwiązanie:** Ten błąd występuje z powodu istniejących zasad platformy Azure, które blokują tworzenie zasobów. Zasady platformy Azure są ustawiane przez administratora systemu organizacji w celu zapewnienia zgodności podczas korzystania z lub tworzenia zasobów platformy Azure. Jeśli te zasady blokują Azure Stack tworzenia zasobów brzegowych, skontaktuj się z administratorem systemu w celu edytowania zasad platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [rozwiązywania problemów z programem Azure Stack EDGE Pro](azure-stack-edge-troubleshoot.md).
