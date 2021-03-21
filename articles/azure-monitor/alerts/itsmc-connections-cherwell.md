---
title: Połącz Cherwell z łącznik zarządzania usługami IT
description: Ten artykuł zawiera informacje na temat sposobu Cherwell z łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: d1fe5e20c06648aa6efc899fabb3ced3345ab41f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045415"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Połącz Cherwell z łącznik zarządzania usługami IT

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między wystąpieniem usługi Cherwell a łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi.

> [!NOTE]
> Od 1 do 2020 Cherwell narzędzia ITSM integracja z usługą Azure alert nie będzie już dostępna dla nowych klientów. Nowe połączenia narzędzia ITSM nie będą obsługiwane.
> Istniejące połączenia narzędzia ITSM będą obsługiwane.

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu Cherwell z ITSMC na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-definition.md#add-it-service-management-connector).
- Wygenerowano identyfikator klienta. Więcej informacji: [generowanie identyfikatora klienta dla Cherwell](#generate-client-id-for-cherwell).
- Rola użytkownika: administrator.

## <a name="connection-procedure"></a>Procedura połączenia

Aby utworzyć połączenie Cherwell, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2. W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.
    ![Nowe połączenie](/media/itsmc-overview/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.

> [!NOTE]
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Cherwell, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później podczas konfigurowania elementów roboczych w tym narzędzia ITSM/widoku szczegółowej analizy dzienników. |
| **Typ partnera**   | Wybierz pozycję **Cherwell.** |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika Cherwell, która może nawiązać połączenie z ITSMC. |
| **Password** (Hasło)   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania i nie są przechowywane w żadnym miejscu w ramach usługi ITSMC.|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Cherwell, które chcesz połączyć z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta, aby uwierzytelnić to połączenie, które zostało wygenerowane w wystąpieniu usługi Cherwell.   |
| **Zakres synchronizacji danych**   | Wybierz Cherwell elementy robocze, które chcesz synchronizować za pomocą ITSMC.  Te elementy robocze są importowane do usługi log Analytics.   **Opcje:**  Zdarzenia, żądania zmiany. |
| **Synchronizuj dane** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. W przypadku wybrania tej pozycji ITSMC tworzy odpowiednie usługi CIs jako elementy konfiguracji (w przypadku nieistniejących CIs) w obsługiwanym systemie narzędzia ITSM. **Domyślnie**: wyłączone. |

![Połączenie Cherwell](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z tego wystąpienia Cherwell są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu usługi Cherwell.

Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generuj identyfikator klienta dla Cherwell

Aby wygenerować identyfikator klienta/klucz dla Cherwell, należy wykonać poniższą procedurę:

1. Zaloguj się do wystąpienia Cherwell jako administrator.
2. Kliknij pozycję Edytuj **zabezpieczenia**  >  **Ustawienia klienta interfejsu API REST**.
3. Wybierz pozycję **Utwórz nowy**  >  **klucz tajny klienta**.

    ![Identyfikator użytkownika Cherwell](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie łącznika ITSM](itsmc-overview.md)
* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)