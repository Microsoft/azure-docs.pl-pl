---
title: Rozwiązywanie problemów z zestawem SDK urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech Devices SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815567"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Rozwiązywanie problemów z zestawem Speech Devices SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech Devices SDK.

## <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli podczas korzystania z usługi mowy wystąpią błędy certyfikatów, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do obszaru **Settings** (Ustawienia). W obszarze **system** wybierz **datę & czas**.

    ![W obszarze Ustawienia wybierz pozycję Data & godzina](media/speech-devices-sdk/qsg-12.png)

1. Pozostaw wybraną opcję **automatycznego & daty** . W obszarze **Wybierz strefę czasową** wybierz bieżącą strefę czasową.

    ![Wybierz Opcje strefy daty i godziny](media/speech-devices-sdk/qsg-13.png)

    Gdy zobaczysz, że czas zestawu deweloperów jest zgodny z czasem na komputerze, zestaw deweloperów jest połączony z Internetem.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
