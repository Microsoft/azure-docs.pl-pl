---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure Monitor
description: Ten artykuł zawiera informacje o sposobach łączenia narzędzia ITSM produktów/usług z bezpiecznym eksportem w Azure Monitor do centralnego monitorowania elementów roboczych narzędzia ITSM i zarządzania nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568646"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Łączenie platformy Azure z narzędziami narzędzia ITSM przy użyciu bezpiecznego eksportu

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między produktem narzędzia ITSM/usługą przy użyciu funkcji bezpiecznego eksportowania.

Bezpieczny eksport to wersja [ITSMC](./itsmc-overview.md) (zarządzanie usługami IT). Obie wersje umożliwiają tworzenie elementów roboczych w narzędziu narzędzia ITSM Azure Monitor podczas uruchamiania alertów. Funkcje obejmują alerty dotyczące metryk, dzienników i dzienników aktywności.

[ITSMC](./itsmc-overview.md) używa poświadczeń użytkownika i hasła, podczas gdy Bezpieczny eksport ma silniejsze uwierzytelnianie, ponieważ używa Azure Active Directory (Azure AD). Azure Active Directory (Azure AD) to chmurowa usługa firmy Microsoft do zarządzania tożsamościami i dostępem. Ułatwia ona użytkownikom logowanie się i dostęp do zasobów wewnętrznych lub zewnętrznych. Korzystanie z usługi Azure AD z usługą narzędzia ITSM ułatwia identyfikowanie alertów platformy Azure (przy użyciu identyfikatora aplikacji usługi Azure AD), które zostały wysłane do systemu zewnętrznego.

> [!NOTE]
> Narzędzia Połącz platformę Azure z usługą narzędzia ITSM przy użyciu funkcji bezpiecznego eksportu są w wersji zapoznawczej

## <a name="secure-export-architecture"></a>Architektura bezpiecznego eksportu

Architektura bezpiecznego eksportu zawiera następujące nowe możliwości:

* **Nowa grupa akcji** — alerty są wysyłane do narzędzia Narzędzia ITSM za pomocą grupy akcji bezpiecznego elementu webhook (zamiast narzędzia ITSM grupy akcji przy użyciu programu w ITSMC).
* **Uwierzytelnianie usługi Azure AD** — uwierzytelnianie odbywa się przy użyciu usługi Azure AD zamiast poświadczeń użytkownika/hasła.

## <a name="secure-export-data-flow"></a>Przepływ danych bezpiecznego eksportu

W ramach przepływu danych bezpiecznego eksportu są wykonywane następujące czynności:

1) Alert skonfigurowany do używania bezpiecznego eksportu w Azure Monitor
2) Ładunek alertu jest wysyłany przez bezpieczną akcję elementu webhook do narzędzia Narzędzia ITSM.
3) Aplikacja narzędzia ITSM sprawdza za pomocą usługi Azure AD, jeśli alert jest autoryzowany do wprowadzania narzędzia Narzędzia ITSM.
4) Jeśli alert jest autoryzowany dla aplikacji:
    1) Tworzy element roboczy (np. zdarzenie) w narzędziu narzędzia ITSM.
    2) Powiąże identyfikator elementu konfiguracji (CI) z bazą danych zarządzania klientami (CMDB).
![Diagram narzędzia ITSM](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Połączenie z kontrolerem BMC Helix

Bezpieczny eksport obsługuje BMC Helix. Oto niektóre zalety integracji:

* **Lepsze uwierzytelnianie** — usługa Azure AD zapewnia bezpieczniejsze uwierzytelnianie bez przekroczeń limitu czasu, które często występują w ITSMC.
* **Alerty zostały rozwiązane w narzędziu narzędzia ITSM** — alerty metryk implementują stan "uruchomił" i "rozwiązany". Gdy warunek jest spełniony, stan alertu to "wyzwolone". Gdy warunek nie zostanie już spełniony, stan alertu to "rozwiązany". W ITSMC nie można automatycznie rozpoznać alertów. W przypadku bezpiecznego eksportu stan rozwiązanych przepływów do narzędzia Narzędzia ITSM i tak więc aktualizacje są aktualizowane automatycznie.
* **[Wspólny schemat umożliwia](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** — w ITSMC, schemat ładunku alertu różni się w zależności od typu alertu. W przypadku bezpiecznego eksportu mamy wspólny schemat dla wszystkich typów alertów. Ten nowy wspólny schemat zawiera element CI dla wszystkich typów alertów. Dzięki temu wszystkie typy alertów będą mogły powiązać ich CI z elementem CMDB.

Zacznij korzystać z łącznik ITSM, wykonując następujące czynności:

1. Zarejestruj swoją aplikację w Azure Active Directory.
2. Utwórz grupę akcji bezpiecznego elementu webhook.
3. Skonfiguruj środowisko partnerskie.

## <a name="register-with-azure-active-directory"></a>Zarejestruj się w Azure Active Directory

Wykonaj następujące kroki, aby zarejestrować aplikację usługi Azure AD za pomocą Azure Active Directory

1) [Tworzenie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) W Azure Active Directory wybierz pozycję "Uwidacznianie aplikacji"
3) Wybierz pozycję Ustaw dla identyfikatora aplikacji identyfikator URI [ ![ usługi Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Kliknij pozycję Zapisz.

## <a name="create-a-secure-webhook-action-group"></a>Utwórz grupę akcji bezpiecznego elementu webhook

Po zarejestrowaniu usługi Azure AD można tworzyć elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure za pomocą akcji bezpiecznego elementu webhook w grupach akcji.
Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w programie Azure Portal.
Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Postępuj zgodnie z następującą procedurą:

W środowisku BMC Helix:

1. Zaloguj się do programu Integration Studio.
2. Wyszukaj zdarzenie tworzenia zdarzenia na podstawie alertów platformy Azure.
3. Skopiuj adres URL elementu webhook.
![ADRES URL BMC](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Aby dodać element webhook do akcji, postępuj zgodnie z instrukcjami dotyczącymi bezpiecznego elementu webhook:

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz pozycję **monitor**. Okienko **monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.
2. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Zarządzaj akcjami**.
3. Wybierz pozycję [Dodaj grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)i wypełnij pola.
4. Wprowadź nazwę w polu **Nazwa grupy akcji** , a następnie wprowadź nazwę w polu **krótka nazwa** . Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.
5. Wybierz **zabezpieczony element webhook**
6. Wybierz pozycję Edytuj szczegóły. Na poniższej ilustracji przedstawiono przykładową zabezpieczoną akcję elementu webhook:
    1. Wybierz odpowiedni identyfikator obiektu Azure Active Directory zarejestrowany
    2. Wklej pole in URI adres URL elementu webhook skopiowanego z "środowiska BMC Helix"
    3. Ustaw dla **typowego schematu alertu** **wartość tak**. 
7. Na poniższej ilustracji przedstawiono przykładową konfigurację akcji bezpiecznego elementu webhook: ![ bezpieczny element webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Skonfiguruj środowisko partnerskie

### <a name="connect-bmc-helix-to-azure-monitor"></a>Łączenie składnika BMC Helix z Azure Monitor

W poniższej sekcji przedstawiono szczegółowe informacje na temat sposobu łączenia produktu BMC Helix i bezpiecznego eksportowania na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Obsługiwana wersja składnika BMC Helix w zakresie zarządzania usługą w chmurze: 20,02 wersja lub nowsza

Aby skonfigurować połączenie BMC Helix:

1) [Włączanie prekompilowanej integracji z Azure Monitor w wersji 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) W ramach konfiguracji połączenia w obszarze BMC Helix przejdź do swojego wystąpienia BMC Integration i postępuj zgodnie z instrukcjami:

1. Wybierz **katalog**
2. Wybieranie **alertów platformy Azure**
3. Wybieranie **łączników**
4. Wybierz **konfigurację**
5. Wybierz pozycję **Dodaj nową konfigurację połączenia**
6. Wypełnij informacje dotyczące sekcji Konfiguracja.
    1. **Nazwa** — Utwórz własne
    2. **Typ autoryzacji** — brak
    3. **Opis**— Utwórz własne
    4. **Lokacja**-chmura
    5. **Liczba wystąpień** -2 — wartość domyślna
    6. **Zaznacz** domyślnie i Włącz użycie
    7. Identyfikator dzierżawy platformy Azure, identyfikator aplikacji platformy Azure, jest pobierany z aplikacji, która została zdefiniowana w kroku "utworzono Azure Active Directory".
![Konfiguracja kontrolera BMC](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
