---
title: Diagnozowanie i rozwiązywanie problemów z połączeniami za pomocą usługi Azure IoT Hub DPS
description: Informacje na temat diagnozowania i rozwiązywania typowych błędów przy użyciu łączności urządzeń z usługą Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646476"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Rozwiązywanie problemów z usługą Azure IoT Hub Device Provisioning Service

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii, takich jak niepowodzenia zaświadczania, błędy rejestracji itp. Ten artykuł zawiera wskazówki dotyczące wykrywania i rozwiązywania problemów z łącznością urządzeń za pośrednictwem [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Używanie Azure Monitor do wyświetlania metryk i konfigurowania alertów

Poniższa procedura opisuje sposób wyświetlania i konfigurowania alertu dotyczącego metryki IoT Hub Device Provisioning Service. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do IoT Hub Device Provisioning Service.

3. Wybierz pozycję **Metryki**.

4. Wybierz żądaną metrykę. 
   <br />Obecnie istnieją trzy metryki usługi DPS:

    | Nazwa metryki | Opis |
    |-------|------------|
    | Próby zaświadczania | Liczba urządzeń, które podjęły próbę uwierzytelnienia przy użyciu usługi Device Provisioning|
    | Próby rejestracji | Liczba urządzeń, które próbowano zarejestrować, aby IoT Hub po pomyślnym uwierzytelnieniu|
    | Przypisane urządzenie | Liczba urządzeń, które zostały pomyślnie przypisane do IoT Hub|

5. Wybierz żądaną metodę agregacji, aby utworzyć wizualny widok metryki. 

6. Aby skonfigurować alert dotyczący metryki, wybierz pozycję **nowe reguły alertów** w prawym górnym rogu bloku Metryka, podobnie możesz przejść do bloku **alertu** i wybrać pozycję **nowe reguły alertów**.

7. Wybierz pozycję **Dodaj warunek**, a następnie wybierz żądaną metrykę i próg, postępując zgodnie z poniższymi instrukcjami.

Aby dowiedzieć się więcej, zobacz [co to są klasyczne alerty w Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Wyświetlanie i rozwiązywanie błędów przy użyciu usługi log Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz pozycję **Ustawienia diagnostyki**.

4. Wybierz pozycję **Włącz diagnostykę**.

5. Włącz zbieranie żądanych dzienników.

    | Nazwa dziennika | Opis |
    |-------|------------|
    | DeviceOperations | Dzienniki związane ze zdarzeniami połączenia urządzenia |
    | Operacje serviceoperations | Dzienniki zdarzeń związane z korzystaniem z zestawu SDK usługi (np. Tworzenie lub aktualizowanie grup rejestracji)|

6. Włącz opcję **Wyślij do log Analytics** ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Przejdź do karty **dzienniki** w Azure Portal w obszarze zasób usługi Device Provisioning.

8. Kliknij przycisk **Uruchom** , aby wyświetlić ostatnie zdarzenia.

9. Jeśli istnieją wyniki, Wyszukaj `OperationName` , `ResultType` , `ResultSignature` i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów dotyczących błędu.


## <a name="common-error-codes"></a>Typowe kody błędów
Skorzystaj z tej tabeli, aby zrozumieć i rozwiązać typowe błędy.

| Kod błędu| Opis | Kod stanu HTTP |
|-------|------------|------------|
| 400 | Treść żądania jest nieprawidłowa; na przykład nie można jej przeanalizować lub nie można sprawdzić poprawności obiektu.| 400 zły format |
| 401 | Nie można zweryfikować tokenu autoryzacji; na przykład wygasła lub nie dotyczy identyfikatora URI żądania. Ten kod błędu jest również zwracany do urządzeń w ramach przepływu zaświadczania modułu TPM. | 401 Brak autoryzacji|
| 404 | Wystąpienie usługi Device Provisioning lub zasób (np. Rejestracja) nie istnieje. |404 — Nie znaleziono |
| 412 | Element ETag w żądaniu jest niezgodny z elementem ETag istniejącego zasobu, zgodnie z RFC7232. | 412 warunek wstępny nie powiódł się |
| 429 | Operacje są ograniczane przez usługę. Aby uzyskać szczegółowe limity usługi, zobacz [limity IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 zbyt wiele żądań |
| 500 | Wystąpił błąd wewnętrzny. | 500 Wewnętrzny błąd serwera|
