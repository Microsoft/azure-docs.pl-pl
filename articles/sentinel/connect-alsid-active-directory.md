---
title: Połącz swoje Alsid Active Directory z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika Alsid for Active Directory do ściągania dzienników Alsid do usługi Azure wskaźnikowej. Wyświetlaj dane Alsid w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566796"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Połącz swoją usługę Alsid dla Active Directory (AD) z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Alsid for Active Directory jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć rozwiązanie Alsid for AD z platformą Azure. Łącznik Alsid dla Active Directory danych umożliwia łatwe łączenie Alsid z dziennikami usługi AD przy użyciu platformy Azure, dzięki czemu można wyświetlać dane w skoroszytach, wysyłać zapytania do tworzenia niestandardowych alertów i uwzględniać je w celu usprawnienia badania. Integracja między programem Alsid for AD i systemem Azure wskaźnikowym korzysta z serwera dziennika systemowego z zainstalowanym agentem Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienie do zapisu w obszarze roboczym wskaźnik platformy Azure.

- Rozwiązanie Alsid for AD musi być skonfigurowane do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Wysyłaj Alsid na potrzeby dzienników usługi AD do usługi Azure wskaźnikowej za pośrednictwem agenta dziennika systemu

Skonfiguruj Alsid dla usługi AD do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego wskaźnikowego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz łącznik **Alsid for Active Directory (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika **Alsid for Active Directory** :

    1. Konfigurowanie serwera dziennika systemowego

        1. Jeśli jeszcze tego nie zrobiono, Utwórz serwer dziennika systemu Linux dla Alsid for AD, do którego mają być wysyłane dzienniki. Wskaźnik **"Azure"** obsługuje demony **rsyslog** i dziennika systemowego. 

        1. Skonfiguruj serwer dziennika systemowego do danych wyjściowych Alsid dla dzienników usługi AD w osobnym pliku.

    1. Skonfiguruj Alsid dla usługi AD w celu wysyłania dzienników do serwera dziennika systemowego

        1. W portalu **usługi Alsid for AD** przejdź do pozycji *system*, *Konfiguracja* i *Dziennik* systemu. Z tego miejsca można utworzyć nowy Alert dziennika systemu na serwerze dziennika systemowego. Na serwerze zdalnym Użyj adresu IP maszyny z systemem Linux, na której zainstalowano agenta systemu Linux.

        1. Sprawdź, czy dzienniki są poprawnie zbierane na serwerze w osobnym pliku (Aby to zrobić, możesz użyć przycisku **Testuj konfigurację** w konfiguracji alertu *dziennika* systemu w Alsid for AD).

    1. Instalowanie i dołączanie agenta Log Analytics dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna). Postępuj zgodnie z linkami i instrukcjami wyświetlanymi na ekranie. Aby uzyskać więcej informacji [, zobacz Konfigurowanie komputera z systemem Linux lub urządzenia](connect-syslog.md#configure-your-linux-machine-or-appliance) .

    1. Konfigurowanie dzienników do zebrania przez agenta Log Analytics

        - Wybierz funkcje i możliwości w obszarze Konfiguracja ustawień zaawansowanych obszaru roboczego.

            1. Kliknij link **>Otwórz konfigurację ustawień zaawansowanych obszaru roboczego** na stronie łącznik.

            1. Na ekranie **Ustawienia zaawansowane** wybierz kolejno pozycje **dane** i **dzienniki niestandardowe**.

            1. Zaznacz pole wyboru **Zastosuj poniższą konfigurację do moich maszyn z systemem Linux** , a następnie kliknij przycisk **Dodaj**.

            1. Kliknij pozycję **Wybierz plik** , aby przekazać przykładowy plik dziennika systemu Alsid dla usługi AD z komputera z systemem Linux z uruchomionym serwerem dziennika systemowego, a następnie kliknij przycisk **dalej**.

            1. Sprawdź, czy **ustawiony ogranicznik rekordu** jest ustawiony na **nowy wiersz** i kliknij przycisk **dalej**.

            1. Wybierz pozycję **Linux** i wprowadź ścieżkę do pliku dziennika systemowego, **+** a następnie kliknij przycisk **dalej**.

            1. W polu Nazwa wpisz *AlsidForADLog* przed sufiksem _CL, a następnie kliknij przycisk **gotowe**.
    
Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w obszarze **dzienniki niestandardowe** w tabeli *AlsidForADLog_CL* .

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. Wykonaj następujące kroki, aby skonfigurować funkcję Kusto **afad_parser** , która będzie używana w zapytaniach i skoroszytach.

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **dzienniki**.

1. Skopiuj poniższe zapytanie i wklej je w oknie zapytania.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Kliknij listę rozwijaną **Zapisz** , a następnie kliknij przycisk **Zapisz**. W panelu **zapisywania** ,

    1. W obszarze **Nazwa** wprowadź **afad_parser**.

    1. W obszarze **Zapisz jako** wybierz pozycję **Funkcja**.

    1. W obszarze **alias funkcji** wprowadź **afad_parser**.

    1. W obszarze **Kategoria** wpisz **funkcje**.

    1. Kliknij pozycję **Zapisz**.

    Aktywowanie aplikacji funkcji zwykle trwa od 10 do 15 minut.

Teraz możesz przystąpić do kwerendy Alsid dla danych usługi AD, wprowadzając `afad_parser` w górnej linii okna zapytania.

Więcej przykładów zapytań można znaleźć na karcie **następne kroki** na stronie łącznika.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Alsid dla usługi AD z wskaźnikiem na platformę Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
