---
title: Konfigurowanie aplikacji asystenta głosowego w usłudze Azure Percept Studio
description: Konfigurowanie aplikacji asystenta głosowego w usłudze Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e41dea3b47c608b9e82ac57fa1cfe5247ea6cc2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099016"
---
# <a name="managing-your-voice-assistant"></a>Zarządzanie asystentem głosu

W tym artykule opisano sposób konfigurowania słowa kluczowego i poleceń aplikacji asystenta głosowego w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Aby uzyskać wskazówki dotyczące konfigurowania słowa kluczowego w IoT Hub zamiast portalu, zobacz ten [artykuł](./how-to-configure-voice-assistant.md).

Jeśli aplikacja asystenta głosowego nie została jeszcze utworzona, zobacz [Tworzenie asystenta głosowego bez kodu za pomocą usługi Azure Percept Studio i usługi Azure Percept audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Konfiguracja słowa kluczowego

Słowo kluczowe to słowo lub krótka fraza służąca do uaktywnienia asystenta głosowego. Na przykład "Hey Cortana" jest słowem kluczowym dla Asystenta Cortany. Aktywacja głosowa umożliwia użytkownikom rozpoczęcie pracy z produktem całkowicie bezpłatnym, po prostu mówiąc słowa kluczowego. Gdy produkt ciągle nasłuchuje słowa kluczowego, cały dźwięk jest przetwarzany lokalnie na urządzeniu do momentu wykrycia, że dane użytkownika pozostaną jako prywatne, jak to możliwe.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguracja w oknie pokazu asystenta głosowego

1. Kliknij pozycję **Zmień** obok **słowa kluczowego Custom** na stronie demonstracyjnej.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Zrzut ekranu przedstawiający okno demonstracyjne Hotelarstwo.":::

    Jeśli nie masz otwartej strony demonstracyjnej, przejdź do strony urządzenia (patrz poniżej), a następnie kliknij pozycję **Testuj asystenta głosowego** w obszarze **Akcje** , aby uzyskać dostęp do demonstracyjnej wersji.

1. Wybierz jedno z dostępnych słów kluczowych i kliknij przycisk **Zapisz** , aby zastosować zmiany.

1. Trzy sygnalizatory LED na urządzeniu z systemem Azure Percept audio zmienią się na jasnoniebieski (nie miga), gdy konfiguracja zostanie ukończona, a asystent głosowy jest gotowy do użycia.

### <a name="configuration-within-the-device-page"></a>Konfiguracja na stronie urządzenia

1. Na stronie Przegląd w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)kliknij pozycję **urządzenia** w okienku menu po lewej stronie.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Zrzut ekranu przedstawiający stronę omówienia usługi Azure Percept Studio z wyróżnionymi urządzeniami.":::

1. Wybierz urządzenie, na którym aplikacja asystenta głosowego została wdrożona.

1. Otwórz kartę **mowa** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Zrzut ekranu przedstawiający stronę urządzenia brzegowego z wyróżnioną kartą mowy.":::

1. Kliknij pozycję **Zmień** obok **słowa kluczowego**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Zrzut ekranu przedstawiający akcje dostępnego rozwiązania mowy.":::

1. Wybierz jedno z dostępnych słów kluczowych i kliknij przycisk **Zapisz** , aby zastosować zmiany.

1. Trzy sygnalizatory LED na urządzeniu z systemem Azure Percept audio zmienią się na jasnoniebieski (nie miga), gdy konfiguracja zostanie ukończona, a asystent głosowy jest gotowy do użycia.

## <a name="create-a-custom-keyword"></a>Tworzenie niestandardowego słowa kluczowego

Za pomocą programu [Speech Studio](https://speech.microsoft.com/)można utworzyć niestandardowe słowo kluczowe dla asystenta głosowego. Uczenie podstawowego niestandardowego modelu słów kluczowych może potrwać do 30 minut.

Postępuj zgodnie z [dokumentacją programu Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) , aby uzyskać wskazówki dotyczące tworzenia niestandardowego słowa kluczowego. Po skonfigurowaniu nowe słowo kluczowe będzie dostępne w portalu Santa Cruz do użycia z aplikacją asystenta głosowego.

## <a name="commands-configuration"></a>Konfiguracja poleceń

Polecenia niestandardowe ułatwiają tworzenie zaawansowanych poleceń głosowych zoptymalizowanych pod kątem interakcji głosowych. Polecenia niestandardowe najlepiej nadają się do wykonywania zadań lub scenariuszy kontroli i sterowania.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguracja w oknie pokazu asystenta głosowego

1. Kliknij pozycję **Zmień** obok **polecenia niestandardowe** na stronie demonstracyjnej. Jeśli nie masz otwartej strony demonstracyjnej, przejdź do strony urządzenia (patrz poniżej), a następnie kliknij pozycję **Testuj asystenta głosowego** w obszarze **Akcje** , aby uzyskać dostęp do demonstracyjnej wersji.

1. Wybierz jedno z dostępnych poleceń niestandardowych, a następnie kliknij przycisk **Zapisz** , aby zastosować zmiany.

### <a name="configuration-within-the-device-page"></a>Konfiguracja na stronie urządzenia

1. Na stronie Przegląd w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)kliknij pozycję **urządzenia** w okienku menu po lewej stronie.

1. Wybierz urządzenie, na którym aplikacja asystenta głosowego została wdrożona.

1. Otwórz kartę **mowa** .

1. Kliknij pozycję **Zmień** obok **polecenia**.

1. Wybierz jedno z dostępnych poleceń niestandardowych, a następnie kliknij przycisk **Zapisz** , aby zastosować zmiany.

## <a name="create-custom-commands"></a>Tworzenie poleceń niestandardowych

Za pomocą programu [Speech Studio](https://speech.microsoft.com/)można utworzyć niestandardowe polecenia dla asystenta głosowego do wykonania.

Postępuj zgodnie z [dokumentacją programu Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) , aby uzyskać wskazówki dotyczące tworzenia poleceń niestandardowych. Po skonfigurowaniu nowe polecenia będą dostępne w usłudze Azure Percept Studio do użycia z aplikacją asystenta głosowego.

## <a name="next-steps"></a>Następne kroki

Po skompilowaniu aplikacji asystenta głosowego Wypróbuj [rozwiązanie "Programowanie bez kodu"](./tutorial-nocode-vision.md) w ramach platformy Azure Percept DK.