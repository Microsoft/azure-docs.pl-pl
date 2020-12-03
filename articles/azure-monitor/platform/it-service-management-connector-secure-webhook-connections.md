---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure Monitor
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z bezpiecznym eksportem w Azure Monitor, aby centralnie monitorować narzędzia ITSM elementy robocze i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 3f6342fcb658611c754a16399ec05f5fa76c79b8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546118"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Łączenie platformy Azure z narzędziami narzędzia ITSM przy użyciu funkcji bezpiecznego eksportowania

W tym artykule opisano sposób konfigurowania połączenia między produktem IT lub usługą usługi IT (narzędzia ITSM) przy użyciu funkcji bezpiecznego eksportowania.

Bezpieczny eksport to zaktualizowana wersja [Łącznik zarządzania usługami IT (ITSMC)](./itsmc-overview.md). Obie wersje umożliwiają tworzenie elementów roboczych w narzędziu narzędzia ITSM, gdy Azure Monitor wysyła alerty. Funkcje obejmują alerty dotyczące metryk, dzienników i dzienników aktywności.

ITSMC używa poświadczeń nazwy użytkownika i hasła. Bezpieczny eksport ma silniejsze uwierzytelnianie, ponieważ używa Azure Active Directory (Azure AD). Usługa Azure AD to usługa w chmurze firmy Microsoft do zarządzania tożsamościami i dostępem. Ułatwia ona użytkownikom logowanie się i dostęp do zasobów wewnętrznych lub zewnętrznych. Korzystanie z usługi Azure AD z usługą narzędzia ITSM pomaga identyfikować alerty platformy Azure (za pośrednictwem identyfikatora aplikacji usługi Azure AD), które zostały wysłane do systemu zewnętrznego.

> [!NOTE]
> Możliwość połączenia platformy Azure z narzędziami narzędzia ITSM przy użyciu funkcji bezpiecznego eksportu jest w wersji zapoznawczej.

## <a name="secure-export-architecture"></a>Architektura bezpiecznego eksportu

Architektura bezpiecznego eksportu zawiera następujące nowe możliwości:

* **Nowa grupa akcji**: alerty są wysyłane do narzędzia Narzędzia ITSM za pośrednictwem grupy akcji bezpiecznego elementu webhook, a nie do grupy akcji narzędzia ITSM używanej przez ITSMC.
* **Uwierzytelnianie usługi Azure AD**: Uwierzytelnianie odbywa się za pomocą usługi Azure AD, a nie poświadczeń nazwy użytkownika/hasła.

## <a name="secure-export-data-flow"></a>Przepływ danych bezpiecznego eksportu

Poniżej przedstawiono kroki przepływu danych bezpiecznego eksportu:

1. Azure Monitor wysyła alert skonfigurowany do korzystania z bezpiecznego eksportu.
2. Ładunek alertu jest wysyłany przez akcję bezpiecznego elementu webhook do narzędzia Narzędzia ITSM.
3. Aplikacja narzędzia ITSM sprawdza za pomocą usługi Azure AD, jeśli alert jest autoryzowany do wprowadzania narzędzia Narzędzia ITSM.
4. Jeśli alert jest autoryzowany, aplikacja:
   
   1. Tworzy element roboczy (na przykład zdarzenie) w narzędziu narzędzia ITSM.
   2. Wiąże identyfikator elementu konfiguracji (CI) z bazą danych zarządzania klientami (CMDB).

![Diagram pokazujący, jak narzędzie narzędzia ITSM komunikuje się z platformą Azure A D, alertami platformy Azure i grupą akcji.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Zalety bezpiecznego eksportu

Główne zalety integracji są następujące:

* **Lepsze uwierzytelnianie**: usługa Azure AD zapewnia bezpieczniejsze uwierzytelnianie bez przekroczeń limitu czasu, które często występują w ITSMC.
* **Alerty zostały rozwiązane w narzędziu narzędzia ITSM**: alerty metryk implementują Stany "uruchomiłne" i "rozwiązany". Gdy warunek jest spełniony, stan alertu to "wyzwolone". Gdy warunek nie zostanie już spełniony, stan alertu to "rozwiązany". W ITSMC nie można automatycznie rozwiązywać alertów. W przypadku bezpiecznego eksportu stan rozwiązanych przepływów do narzędzia Narzędzia ITSM, więc jest automatycznie aktualizowany.
* **[Wspólny schemat alertów](./alerts-common-schema.md)**: w ITSMC schemat ładunku alertu różni się w zależności od typu alertu. W przypadku bezpiecznego eksportu istnieje wspólny schemat dla wszystkich typów alertów. Ten wspólny schemat zawiera element CI dla wszystkich typów alertów. Wszystkie typy alertów będą mogły powiązać ich CI z elementem CMDB.

Rozpocznij korzystanie z narzędzia łącznik ITSM, wykonując następujące czynności:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Utwórz grupę akcji bezpiecznego elementu webhook.
3. Skonfiguruj środowisko partnerskie. 

Bezpieczny eksport obsługuje połączenia z następującymi narzędziami narzędzia ITSM:
* [ServiceNow](#connect-servicenow-to-azure-monitor)
* [Kontroler BMC Helix](#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Zarejestruj się w Azure Active Directory

Wykonaj następujące kroki, aby zarejestrować aplikację w usłudze Azure AD:

1. Wykonaj kroki opisane w temacie [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. W usłudze Azure AD wybierz opcję **Uwidocznij aplikację**.
3. Wybierz pozycję **Ustaw** dla **identyfikatora aplikacji identyfikator URI**.

   [![Zrzut ekranu przedstawiający opcję Ustawienia U R I aplikacji I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Wybierz pozycję **Zapisz**.

## <a name="create-a-secure-webhook-action-group"></a>Utwórz grupę akcji bezpiecznego elementu webhook

Po zarejestrowaniu aplikacji w usłudze Azure AD można utworzyć elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure za pomocą akcji bezpiecznego elementu webhook w grupach akcji.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w Azure Portal.
Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](./action-groups.md).

Aby dodać element webhook do akcji, wykonaj następujące instrukcje dotyczące bezpiecznego elementu webhook:

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz pozycję **monitor**. Okienko **monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.
2. Wybierz kolejno pozycje **alerty**  >  **Zarządzanie akcje**.
3. Wybierz pozycję [Dodaj grupę akcji](./action-groups.md#create-an-action-group-by-using-the-azure-portal)i wypełnij pola.
4. Wprowadź nazwę w polu **Nazwa grupy akcji** , a następnie wprowadź nazwę w polu **krótka nazwa** . Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.
5. Wybierz pozycję **bezpieczny element webhook**.
6. Wybierz następujące szczegóły:
   1. Wybierz identyfikator obiektu zarejestrowanego wystąpienia Azure Active Directory.
   2. W przypadku identyfikatora URI Wklej w adresie URL elementu webhook skopiowanym ze [środowiska narzędzia Narzędzia ITSM](#configure-the-itsm-tool-environment).
   3. Ustaw opcję **Włącz wspólny schemat alertów** na **wartość tak**. 

   Na poniższej ilustracji przedstawiono konfigurację przykładowej akcji bezpiecznego elementu webhook:

   ![Zrzut ekranu pokazujący bezpieczną akcję elementu webhook.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurowanie środowiska narzędzia Narzędzia ITSM

Konfiguracja zawiera dwie czynności:
1. Pobierz identyfikator URI dla definicji bezpiecznego eksportu.
2. Definicje zgodnie z przepływem narzędzia Narzędzia ITSM.


### <a name="connect-servicenow-to-azure-monitor"></a>Połącz usługi ServiceNow z Azure Monitor

W poniższych sekcjach znajdują się szczegółowe informacje dotyczące sposobu łączenia produktu usługi ServiceNow i bezpiecznego eksportowania na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Dostępna jest obsługiwana wersja usługi ServiceNow zarządzania zdarzeniami — ITOM (wersja Orlando lub nowsza).

### <a name="configure-the-servicenow-connection"></a>Konfigurowanie połączenia usługi usługi ServiceNow

1. Użyj linku https://(nazwa wystąpienia). Service-teraz. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor identyfikator URI dla definicji bezpiecznego eksportu.

2. Postępuj zgodnie z instrukcjami w zależności od wersji:
   * [Paryż](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Nowy Jork](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)

### <a name="connect-bmc-helix-to-azure-monitor"></a>Łączenie składnika BMC Helix z Azure Monitor

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu BMC Helix i bezpiecznego eksportowania na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Dostępna jest obsługiwana wersja składnika BMC Helix do zarządzania usługami w chmurze (wersja 19,08 lub nowsza).

### <a name="configure-the-bmc-helix-connection"></a>Konfigurowanie połączenia BMC Helix

1. Aby uzyskać identyfikator URI dla bezpiecznego eksportu, użyj następującej procedury w środowisku BMC Helix:

   1. Zaloguj się do programu Integration Studio.
   2. Wyszukaj **zdarzenie tworzenia zdarzenia na podstawie alertów platformy Azure** .
   3. Skopiuj adres URL elementu webhook.
   
   ![Zrzut ekranu przedstawiający element webhook U R L w programie Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
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

![Zrzut ekranu pokazujący konfigurację składnika BMC.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)




## <a name="next-steps"></a>Następne kroki

* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-overview.md)
