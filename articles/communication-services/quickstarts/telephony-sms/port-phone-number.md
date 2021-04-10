---
title: Szybki Start — przenoszenie numeru telefonu do usługi Azure Communications Services
description: Dowiedz się, jak przenieść numer telefonu do zasobu usług komunikacyjnych
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729932"
---
# <a name="quickstart-port-a-phone-number"></a>Szybki Start: Port numeru telefonu

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Rozpocznij pracę z usługami Azure Communications Services, numer telefonu do zasobu usług Azure Communications Services. Numery bezpłatne i geograficzne na podstawie Stany Zjednoczone są uprawnione do przenoszenia. Więcej informacji o typach numerów telefonów można znaleźć w [dokumentacji dotyczącej pojęć dotyczących numeru telefonu](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zasób aktywnych usług komunikacyjnych.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Zbierz szczegóły zasobów platformy Azure

Przed zainicjowaniem żądania portu przejdź do Azure Portal i wybierz zasób usług komunikacyjnych. Po `Overview` wybraniu okienka kliknij `JSON View` link w prawym górnym rogu:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Zrzut ekranu przedstawiający Wybieranie widoku JSON.":::

Zapisz **identyfikator platformy Azure** zasobu i **niezmienny identyfikator zasobu**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Zrzut ekranu przedstawiający Wybieranie właściwości JSON.":::

## <a name="initiate-the-port-request"></a>Inicjowanie żądania portu

Numery bezpłatne i geograficzne na podstawie Stany Zjednoczone są uprawnione do przenoszenia. Aby przesłać żądanie dotyczące portu, użyj jednej z następujących form:

- Bezpłatne numery telefonów: bezpłatne [żądanie portu numer](https://aka.ms/acs-port-form-tollfree) bezpłatny
- Dla numerów geograficznych, które są oparte na [żądaniu portu numeru geograficznego](https://aka.ms/acs-port-form-geographic) US:

Po zakończeniu Wyślij wypełniony formularz żądania portu do programu acsporting@microsoft.com . Upewnij się, że wiersz tematu wiadomości e-mail zaczyna się od "żądanie Port-In ACS".

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat wykonywania tych instrukcji:

> [!div class="checklist"]
> * Pozyskiwanie metadanych zasobów usług komunikacyjnych
> * Prześlij żądanie przewożenia numer telefonu

> [!div class="nextstepaction"]
> [Wyślij wiadomość SMS](../telephony-sms/send.md) 
>  [Wprowadzenie do wywoływania](../voice-video-calling/getting-started-with-calling.md)
