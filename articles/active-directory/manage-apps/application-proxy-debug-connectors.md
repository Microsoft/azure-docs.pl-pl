---
title: Debugowanie łączników serwera proxy aplikacji — Azure Active Directory | Microsoft Docs
description: Problemy z debugowaniem przy użyciu łączników serwera proxy aplikacji Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256970"
---
# <a name="debug-application-proxy-connector-issues"></a>Problemy z debugowaniem łącznika serwera proxy aplikacji 

Ten artykuł pomaga rozwiązywać problemy z łącznikami serwera proxy aplikacji Azure Active Directory (Azure AD). Jeśli używasz usługi serwera proxy aplikacji w celu uzyskania dostępu zdalnego do lokalnej aplikacji sieci Web, ale masz problemy z połączeniem z aplikacją, użyj tego schematu blokowego do debugowania problemów z łącznikiem. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że zainstalowano łącznik serwera proxy aplikacji i występuje problem. W przypadku rozwiązywania problemów z serwerem proxy aplikacji zalecamy rozpoczęcie pracy z tym przepływem rozwiązywania problemów w celu ustalenia, czy łączniki serwera proxy aplikacji są prawidłowo skonfigurowane. Jeśli nadal występują problemy z nawiązywaniem połączenia z aplikacją, postępuj zgodnie z przepływem rozwiązywania problemów, aby [debugować problemy aplikacji serwera proxy aplikacji](application-proxy-debug-apps.md).  


Aby uzyskać więcej informacji na temat serwera proxy aplikacji i korzystania z jego łączników, zobacz:

- [Dostęp zdalny do aplikacji lokalnych przy użyciu serwera proxy aplikacji](application-proxy.md)
- [Łączniki serwera proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Schemat blokowy dotyczący problemów z łącznikiem

Ten schemat blokowy przeprowadzi Cię przez kroki dotyczące debugowania niektórych typowych problemów z łącznikiem. Aby uzyskać szczegółowe informacje o każdym z kroków, zobacz tabelę po schemacie blokowym.

![Schemat blokowy przedstawiający kroki debugowania łącznika](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Krok | Akcja | Opis |
|---------|---------|---------|
|1 | Znajdowanie grupy łączników przypisanej do aplikacji | Prawdopodobnie masz łącznik zainstalowany na wielu serwerach, w takim przypadku łączniki powinny być [przypisane do grup łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Aby dowiedzieć się więcej na temat grup łączników, zobacz [publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md). |
|2 | Instalowanie łącznika i przypisywanie grupy | Jeśli nie masz zainstalowanego łącznika, zobacz [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Jeśli masz problemy z instalowaniem łącznika, zobacz [problem z instalowaniem łącznika](application-proxy-connector-installation-problem.md).<br></br> Jeśli łącznik nie jest przypisany do grupy, zobacz [przypisywanie łącznika do grupy](application-proxy-connector-groups.md#create-connector-groups).<br></br>Jeśli aplikacja nie jest przypisana do grupy łączników, zobacz [przypisywanie aplikacji do grupy łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Uruchamianie testu portu na serwerze łącznika | Na serwerze łącznika Uruchom test portu przy użyciu programu [Telnet](/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów, aby sprawdzić [, czy są otwarte porty 443 i 80](application-proxy-add-on-premises-application.md#open-ports).|
|4 | Konfigurowanie domen i portów | Upewnij [się, że domeny i porty są prawidłowo skonfigurowane](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Aby łącznik działał prawidłowo, istnieją pewne porty, które muszą być otwarte i adresy URL, do których serwer musi mieć dostęp. |
|5 | Sprawdź, czy serwer proxy zaplecza jest używany | Sprawdź, czy łączniki korzystają z serwerów proxy zaplecza, czy ich pomijania. Aby uzyskać szczegółowe informacje, zobacz [Rozwiązywanie problemów z serwerem proxy łącznika i problemy z łącznością usług](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizowanie łącznika i Aktualizator w celu korzystania z serwera proxy zaplecza | Jeśli serwer proxy zaplecza jest używany, należy się upewnić, że łącznik używa tego samego serwera proxy. Aby uzyskać szczegółowe informacje na temat rozwiązywania problemów i konfigurowania łączników do pracy z serwerami proxy, zobacz [pracy z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Załaduj wewnętrzny adres URL aplikacji na serwerze łącznika | Na serwerze łącznika Załaduj wewnętrzny adres URL aplikacji. |
|8 | Sprawdź połączenie z siecią wewnętrzną | Wystąpił problem z łącznością w sieci wewnętrznej, który nie może zdiagnozować tego przepływu debugowania. Aby łączniki działały, aplikacja musi być wewnętrznie dostępna. Można włączyć i wyświetlić dzienniki zdarzeń łącznika zgodnie z opisem w [łącznikach serwera proxy aplikacji](application-proxy-connectors.md#under-the-hood). |
|9 | Wydłużenie limitu czasu na zapleczu | W **dodatkowych ustawieniach** aplikacji zmień ustawienie **limitu czasu aplikacji zaplecza** na wartość **Long**. Zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Jeśli problemy będą nadal występować, należy określić problemy związane z przepływem, przejrzeć przepływy debugowania aplikacji i rejestracji jednokrotnej | Użyj narzędzia [debugowania aplikacji serwera proxy](application-proxy-debug-apps.md) aplikacji. |

## <a name="next-steps"></a>Następne kroki


* [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md)
* [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji i błędami łącznika](application-proxy-troubleshoot.md)
* [Jak zainstalować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD w trybie dyskretnym](application-proxy-register-connector-powershell.md)