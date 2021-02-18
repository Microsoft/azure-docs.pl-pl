---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure Monitor
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z bezpiecznym eksportem w Azure Monitor, aby centralnie monitorować narzędzia ITSM elementy robocze i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 6b273b864c8329547ef91eb7063a064e0f5cb77d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620873"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Łączenie platformy Azure z narzędziami narzędzia ITSM przy użyciu funkcji bezpiecznego eksportowania

W tym artykule opisano sposób konfigurowania połączenia między produktem IT lub usługą usługi IT (narzędzia ITSM) przy użyciu funkcji bezpiecznego eksportowania.

Bezpieczny eksport to zaktualizowana wersja [Łącznik zarządzania usługami IT (ITSMC)](../platform/itsmc-overview.md). Obie wersje umożliwiają tworzenie elementów roboczych w narzędziu narzędzia ITSM, gdy Azure Monitor wysyła alerty. Funkcje obejmują alerty dotyczące metryk, dzienników i dzienników aktywności.

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

## <a name="next-steps"></a>Następne kroki

* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](../platform/itsmc-overview.md)
