---
title: Debugowanie aplikacji serwera proxy aplikacji — Azure Active Directory | Microsoft Docs
description: Problemy z debugowaniem przy użyciu aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e61ea30f01e8ae141d24c9bd91b08edef4dbe74e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85555059"
---
# <a name="debug-application-proxy-application-issues"></a>Debugowanie problemów z aplikacjami serwera proxy aplikacji 

Ten artykuł pomaga rozwiązywać problemy z aplikacjami serwera proxy aplikacji Azure Active Directory (Azure AD). Jeśli używasz usługi serwera proxy aplikacji w celu uzyskania dostępu zdalnego do lokalnej aplikacji sieci Web, ale masz problemy z połączeniem z aplikacją, użyj tego schematu blokowego do debugowania problemów z aplikacjami. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W przypadku rozwiązywania problemów z serwerem proxy aplikacji zalecamy rozpoczęcie od łączników. Najpierw postępuj zgodnie z przepływem rozwiązywania problemów w [Debuguj problemy dotyczące łącznika serwera proxy aplikacji](application-proxy-debug-connectors.md) , aby upewnić się, że łączniki serwera proxy aplikacji są prawidłowo Jeśli nadal występują problemy, Wróć do tego artykułu, aby rozwiązać problem z aplikacją.  

Aby uzyskać więcej informacji o serwerze proxy aplikacji, zobacz:

- [Dostęp zdalny do aplikacji lokalnych przy użyciu serwera proxy aplikacji](application-proxy.md)
- [Łączniki serwera proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Schemat blokowy dotyczący problemów z aplikacjami

Ten schemat blokowy przeprowadzi Cię przez kroki dotyczące debugowania niektórych typowych problemów związanych z nawiązywaniem połączenia z aplikacją. Aby uzyskać szczegółowe informacje o każdym z kroków, zobacz tabelę po schemacie blokowym.

![Schemat blokowy przedstawiający kroki debugowania aplikacji](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Krok | Akcja | Opis |
|---------|---------|---------|
|1 | Otwórz przeglądarkę, uzyskaj dostęp do aplikacji, a następnie wprowadź swoje poświadczenia | Spróbuj użyć poświadczeń, aby zalogować się do aplikacji, i sprawdź, czy nie występują błędy związane z użytkownikiem, takie jak [nie można uzyskać dostępu do tej aplikacji firmowej](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Weryfikuj przypisanie użytkownika do aplikacji | Upewnij się, że konto użytkownika ma uprawnienia dostępu do aplikacji z poziomu sieci firmowej, a następnie przetestuj logowanie do aplikacji, wykonując czynności opisane w sekcji [testowanie aplikacji](application-proxy-add-on-premises-application.md#test-the-application). Jeśli problemy związane z logowaniem będą nadal występować, zobacz [jak rozwiązać problemy z logowaniem](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Otwórz przeglądarkę i spróbuj uzyskać dostęp do aplikacji | Jeśli błąd pojawia się natychmiast, sprawdź, czy serwer proxy aplikacji jest prawidłowo skonfigurowany. Aby uzyskać szczegółowe informacje o konkretnych komunikatach o błędach, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md).  |
|4 | Sprawdzanie konfiguracji domeny niestandardowej lub Rozwiązywanie problemów z błędem | Jeśli strona nie jest wyświetlana, upewnij się, że domena niestandardowa została prawidłowo skonfigurowana, przeglądając temat [Praca z domenami niestandardowymi](application-proxy-configure-custom-domain.md).<br></br>Jeśli strona nie zostanie załadowana i pojawi się komunikat o błędzie, Rozwiąż ten problem, odwołując się do  [rozwiązywania problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md). <br></br>Jeśli pojawi się komunikat o błędzie dłuższy niż 20 sekund, może wystąpić problem z połączeniem. Przejdź do artykułu [debugowanie łączników serwera proxy aplikacji](application-proxy-debug-connectors.md) .  |
|5 | Jeśli problemy będą nadal występować, przejdź do debugowania łącznika | Może wystąpić problem z łącznością między serwerem proxy a łącznikiem lub między łącznikiem i zapleczem. Przejdź do artykułu [debugowanie łączników serwera proxy aplikacji](application-proxy-debug-connectors.md) . |
|6 | Publikowanie wszystkich zasobów, sprawdzanie narzędzi programistycznych przeglądarki i usuwanie linków | Upewnij się, że ścieżka publikowania zawiera wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Użyj narzędzi programistycznych przeglądarki (F12 Tools w programie Internet Explorer lub Microsoft Edge) i sprawdź, czy problemy z publikowaniem, zgodnie z opisem na [stronie aplikacji, nie są wyświetlane poprawnie](application-proxy-page-appearance-broken-problem.md). <br></br>Przejrzyj opcje rozwiązywania przerwanych linków w [linkach na stronie nie działają](application-proxy-page-links-broken-problem.md). |
|7 | Sprawdzanie opóźnienia sieci | Jeśli strona ładuje się powoli, Dowiedz się więcej na temat sposobów minimalizowania opóźnienia sieci w [przypadku zmniejszenia opóźnień](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobacz dodatkową pomoc dotyczącą rozwiązywania problemów | Jeśli problemy będą nadal występować, Znajdź dodatkowe artykuły dotyczące rozwiązywania problemów w [dokumentacji rozwiązywania problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Następne kroki


* [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md)
* [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji i błędami łącznika](application-proxy-troubleshoot.md)
* [Jak zainstalować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD w trybie dyskretnym](application-proxy-register-connector-powershell.md)
