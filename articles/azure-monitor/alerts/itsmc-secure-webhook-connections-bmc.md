---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure Monitor konfiguracji z kontrolerem BMC
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z kontrolerem BMC w sprawie bezpiecznego eksportu w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 5a78dc3923c8183db6dd2ddea1d2233149201c07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620833"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Łączenie składnika BMC Helix z Azure Monitor

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu BMC Helix i bezpiecznego eksportowania na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Dostępna jest obsługiwana wersja składnika BMC Helix do zarządzania usługami w chmurze (wersja 19,08 lub nowsza).

## <a name="configure-the-bmc-helix-connection"></a>Konfigurowanie połączenia BMC Helix

1. Aby uzyskać identyfikator URI dla bezpiecznego eksportu, wykonaj czynności opisane w poniższej procedurze w środowisku BMC Helix:

   1. Zaloguj się do programu Integration Studio.
   1. Wyszukaj **zdarzenie tworzenia zdarzenia na podstawie alertów platformy Azure** .
   1. Skopiuj adres URL elementu webhook.
   
   ![Zrzut ekranu przedstawiający element webhook U R L w programie Integration Studio.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Postępuj zgodnie z instrukcjami w zależności od wersji:
   * [Włączenie prekompilowanej integracji z Azure monitor w wersji 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Włączenie prekompilowanej integracji z Azure monitor w wersji 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. W ramach konfiguracji połączenia w obszarze BMC Helix przejdź do swojego wystąpienia BMC Integration i postępuj zgodnie z następującymi instrukcjami:

   1. Wybierz pozycję **katalog**.
   2. Wybierz pozycję **alerty platformy Azure**.
   3. Wybierz pozycję **Łączniki**.
   4. Wybierz pozycję **Konfiguracja**.
   5. Wybierz pozycję **Dodaj nową konfigurację połączenia** .
   6. Wypełnij informacje dotyczące sekcji Konfiguracja:
      - **Nazwa**: Utwórz własne.
      - **Typ autoryzacji**: **Brak**
      - **Opis**: Utwórz własny.
      - **Lokacja**: **chmura**
      - **Liczba wystąpień**: **2**, wartość domyślna.
      - **Sprawdź**: wybrane domyślnie, aby włączyć użycie.
      - Identyfikator dzierżawy platformy Azure i identyfikator aplikacji platformy Azure są pobierane z aplikacji, która została wcześniej zdefiniowana.

![Zrzut ekranu pokazujący konfigurację składnika BMC.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
