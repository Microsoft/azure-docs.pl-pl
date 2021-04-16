---
title: Rozwiązywanie problemów IoT Plug and Play urządzeniem
description: Przewodnik po zalecanych krokach rozwiązywania problemów dla partnerów certyfikowanych IoT Plug and Play urządzenia.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559187"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Rozwiązywanie problemów z IoT Plug and Play certyfikacji aplikacji

W fazie testowania & Connect IoT Plug and Play projektu certyfikacji usługi IoT Plug and Play mogą wystąpić pewne scenariusze, które uniemożliwiają przekazanie testów usługi Certyfikacji Azure for IoT (AICS).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz się zalogować i utworzyć projekt dla urządzenia w portalu [certyfikowanych urządzeń platformy Azure.](https://certify.azure.com) Aby uzyskać więcej informacji, zobacz [samouczek](tutorial-01-creating-your-project.md).

## <a name="when-aics-tests-arent-passing"></a>Gdy testy AICS nie są przechodzące

Test AICS może nie przejść z kilku powodów. Wykonaj następujące kroki, aby sprawdzić typowe problemy i rozwiązać problemy z urządzeniem.

1. Sprawdź, czy kod urządzenia ustawia ładunek identyfikator modelu podczas aprowizowania usługi DPS. Jest to wymagane, aby aICS weryfikowała urządzenie.
1. Dzienniki telemetrii z poprzednich przebiegów testów można wyświetlić, naciskając przycisk , aby określić, co `View Logs` powoduje niepowodzenie testu. Do przeglądu są dostępne zarówno komunikaty testowe, jak i dane pierwotne.  

    ![Przeglądanie danych testowych](./media/images/review-logs.png)

1. W niektórych przypadkach, w których dzienniki wskazują na , spróbuj ponownie uruchomić urządzenie `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` i ponownie uruchomić proces aprowacji urządzenia.
1. Jeśli testy automatyczne nadal nie powiodą się, możesz `request a manual review` zastąpić wyniki. Spowoduje to wyzwolenie żądania **ręcznej weryfikacji przez** zespół certyfikowanych urządzeń platformy Azure.  

    ![Żądanie przeglądu ręcznego](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Gdy zostanie wyświetlony wyświetlony "Passed with warnings" (Przekazano z ostrzeżeniami)

Jeśli podczas uruchamiania testów zostanie wyświetlony wynik , oznacza to, że niektóre dane telemetryczne nie zostały `Passed with warnings` odebrane w okresie testowania. Może to być spowodowane zależnością telemetrii w dłuższych przedziałach czasu lub wyzwalaczami zewnętrznymi, które nie były dostępne. Możesz przesłać urządzenie do przeglądu, podczas którego zespół przeglądający określi, czy w przyszłości konieczna jest ręczna walidacja. 

## <a name="when-you-need-help-with-the-model-repository"></a>Gdy potrzebujesz pomocy w repozytorium modeli

Aby IoT Plug and Play związane z repozytorium modeli, zapoznaj się ze wskazówkami w naszej kwestiach [dotyczących repozytorium modeli urządzeń.](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)

## <a name="next-steps"></a>Następne kroki

Mamy nadzieję, że ten przewodnik pomoże Ci kontynuować IoT Plug and Play certyfikacji! Po zakończeniu pracy z usługą AICS możesz kontynuować nasze samouczki, aby przesłać i opublikować urządzenie.

- [Samouczek: testowanie urządzenia](tutorial-03-testing-your-device.md)
