---
title: Łączenie danych zapory Windows Defender z usługą Azure wskaźnikowego | Microsoft Docs
description: Włącz łącznik zapory systemu Windows na platformie Azure, aby łatwo przesyłać zdarzenia zapory z maszyn z systemem Windows, na których zainstalowano Log Analytics agenci.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655344"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Połącz zaporę Windows Defender z zabezpieczeniami zaawansowanymi z platformą Azure — wskaźnikiem

Łącznik [zapory Windows Defender z zabezpieczeniami Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) umożliwia korzystanie z platformy Azure do łatwego pozyskiwania zapory Windows Defender z zaawansowanymi dziennikami zabezpieczeń z dowolnego komputera z systemem Windows w obszarze roboczym. To połączenie umożliwia wyświetlanie i analizowanie zdarzeń zapory systemu Windows w skoroszytach, używanie ich w tworzeniu niestandardowych alertów i wprowadzanie ich w celu zapewnienia bezpieczeństwa, dzięki czemu możesz uzyskać więcej informacji o sieci organizacji i zwiększyć możliwości operacji związanych z bezpieczeństwem. 

Rozwiązanie zbiera zdarzenia Zapory systemu Windows z maszyn z systemem Windows, na których zainstalowano agenta Log Analytics. 

> [!NOTE]
> - Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.
>
> - Jeśli alerty usługi Azure Defender z Azure Security Center są już zbierane do obszaru roboczego wskaźnikowego platformy Azure, nie ma potrzeby włączania rozwiązania Zapora systemu Windows za pośrednictwem tego łącznika. Jeśli jednak go włączysz, nie spowoduje to duplikowania danych. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w obszarze roboczym, w którym są połączone maszyny, które mają być monitorowane.

- Musisz mieć przypisaną rolę **Współautora log Analytics** w rozwiązaniu SecurityInsights w tym obszarze roboczym, oprócz ról **wskaźnikowych platformy Azure** . [Dowiedz się więcej](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Włącz łącznik 

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Wybierz opcję **Zapora systemu Windows** w galerii łączników, a następnie kliknij przycisk **Otwórz stronę łącznika**.

### <a name="instructions-tab"></a>Karta instrukcje

- **Jeśli maszyny z systemem Windows znajdują się na platformie Azure:**

    1. Wybierz pozycję **Zainstaluj agenta na maszynie wirtualnej platformy Azure systemu Windows**.

    1. Kliknij link **pobierz & Zainstaluj agenta dla maszyn wirtualnych systemu Azure Windows >** .

    1. Na liście **maszyny wirtualne** wybierz maszynę z systemem Windows, która ma zostać przesłana do usługi Azure wskaźnikowej. (Możesz wybrać **okna** w filtrze kolumn systemu operacyjnego, aby upewnić się, że są wyświetlane tylko maszyny wirtualne z systemem Windows).

    1. W oknie otwartym dla tej maszyny wirtualnej kliknij pozycję **Połącz**.

    1. Wróć do okienka **Virtual Machines** i Powtórz dwa poprzednie kroki dla innych maszyn wirtualnych, które chcesz połączyć. Gdy skończysz, Wróć do okienka **Zapora systemu Windows** .

- **Jeśli maszyna z systemem Windows nie jest maszyną wirtualną platformy Azure:**

    1. Wybierz pozycję **Zainstaluj agenta na komputerze spoza systemu Windows**.

    1. Kliknij link **pobierz & Zainstaluj agenta dla maszyn nienależących do platformy Azure z systemem Windows >** .

    1. W okienku **Zarządzanie agentami** wybierz opcję **Pobierz agenta systemu Windows (64 bit)** lub **pobierz agenta systemu Windows (32 bit)**, zgodnie z wymaganiami.

    1. Skopiuj ciągi **identyfikatora obszaru roboczego**, **klucza podstawowego** i **klucza pomocniczego** do pliku tekstowego. Skopiuj ten plik i pobrany plik instalacyjny na komputer z systemem Windows. Uruchom plik instalacyjny i po wyświetleniu monitu wprowadź identyfikator i ciągi klucza w pliku tekstowym podczas instalacji.

    1. Wróć do okienka **Zapora systemu Windows** .

1. Kliknij przycisk **Zainstaluj rozwiązanie**.

### <a name="next-steps-tab"></a>Karta następne kroki

- Zobacz dostępne zalecane skoroszyty i przykłady zapytań powiązane z łącznikiem danych **zapory systemu Windows** , aby uzyskać wgląd w dane dziennika zapory systemu Windows.

- Aby zbadać dane zapory systemu Windows w **dziennikach**, wpisz **WindowsFirewall** w oknie zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
 
Ze względu na to, że Dzienniki zapory systemu Windows są wysyłane do usługi Azure wskaźnikowej tylko wtedy, gdy lokalny plik dziennika osiągnie pojemność, pozostawienie dziennika o rozmiarze domyślnym 4096 KB najprawdopodobniej spowoduje opóźnienie dużej kolekcji. Opóźnienie można obniżyć przez zmniejszenie rozmiaru pliku dziennika. Zapoznaj się z instrukcjami dotyczącymi [konfigurowania dziennika zapory systemu Windows](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Należy pamiętać, że podczas definiowania minimalnego możliwego rozmiaru dziennika (1 KB) praktycznie eliminuje opóźnienie kolekcji, może to mieć negatywny wpływ na wydajność maszyny lokalnej. 

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zapory systemu Windows z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).