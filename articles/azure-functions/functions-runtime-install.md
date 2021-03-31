---
title: Instalacja środowisko uruchomieniowe usługi Azure Functions
description: Jak zainstalować wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86540387"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Zainstaluj wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Jeśli chcesz zainstalować wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions 2, wykonaj następujące kroki:

1. Upewnij się, że komputer spełnia minimalne wymagania.
1. Pobierz [Instalatora programu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej](https://aka.ms/azafrv2).
1. Odinstaluj wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions 1.
1. Zainstaluj środowisko uruchomieniowe usługi Azure Functions wersji zapoznawczej 2.
1. Ukończ konfigurację środowisko uruchomieniowe usługi Azure Functions wersji zapoznawczej 2.
1. Tworzenie pierwszej funkcji w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions muszą być dostępne następujące zasoby:

1. Komputer z systemem Microsoft Windows Server 2016 lub Microsoft Windows 10 Creators Update (Professional lub Enterprise Edition).
1. Wystąpienie SQL Server działające w sieci.  Minimalna wymagana wersja jest SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstaluj poprzednią wersję

Jeśli wcześniej zainstalowano wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions, należy ją odinstalować przed zainstalowaniem najnowszej wersji.  Odinstaluj Podgląd środowisko uruchomieniowe usługi Azure Functions, usuwając program w aplecie Dodaj/Usuń programy w systemie Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Zainstaluj wersję zapoznawczą środowisko uruchomieniowe usługi Azure Functions

Instalator środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej prowadzi użytkownika przez proces instalacji funkcji zarządzania i ról procesu roboczego środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej.  Można zainstalować rolę zarządzanie i proces roboczy na tym samym komputerze.  Jednak w miarę dodawania większej liczby aplikacji funkcji należy wdrożyć więcej ról procesów roboczych na dodatkowych maszynach, aby umożliwić skalowanie funkcji na wielu pracowników.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Zainstaluj rolę zarządzania i procesu roboczego na tym samym komputerze

1. Uruchom Instalatora programu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej.

    ![środowisko uruchomieniowe usługi Azure Functions Instalatora podglądu][1]

1. Kliknij przycisk **Dalej**.
1. Po przeczytaniu warunków **umowy licencyjnej** **zaznacz pole wyboru** , aby zaakceptować warunki i kliknij przycisk **dalej** .
1. Wybierz role, które chcesz zainstalować, w tej roli **proces roboczy** **rola zarządzania** usługą i/lub funkcja, a następnie kliknij przycisk **dalej**.

    ![Środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej — wybór roli][3]

    > [!NOTE]
    > **Rolę procesu roboczego funkcji** można zainstalować na wielu innych maszynach. Aby to zrobić, wykonaj te instrukcje i wybierz tylko **rolę proces roboczy funkcji** w instalatorze.

1. Kliknij przycisk **dalej** , aby **Kreator instalacji środowisko uruchomieniowe usługi Azure Functions** rozpoczął proces instalacji na maszynie.
1. Po zakończeniu pracy Kreator instalacji uruchomi narzędzie konfiguracji **środowisko uruchomieniowe usługi Azure Functions** .

    ![Zakończono środowisko uruchomieniowe usługi Azure Functions Instalatora podglądu][6]

    > [!NOTE]
    > W przypadku instalowania w **systemie Windows 10** , gdy funkcja **kontenera** nie została wcześniej włączona, **Instalator środowisko uruchomieniowe usługi Azure Functions** monituje o ponowne uruchomienie komputera w celu ukończenia instalacji.

## <a name="configure-the-azure-functions-runtime"></a>Skonfiguruj środowisko uruchomieniowe usługi Azure Functions

Aby ukończyć instalację środowisko uruchomieniowe usługi Azure Functions, należy ukończyć konfigurację.

1. Narzędzie konfiguracji **środowisko uruchomieniowe usługi Azure Functions** pokazuje, które role są zainstalowane na komputerze.

    ![Narzędzie konfiguracji środowisko uruchomieniowe usługi Azure Functions Preview][7]

1. Kliknij kartę **baza danych** , wprowadź szczegóły połączenia dla wystąpienia SQL Server, w tym określenie [klucza głównego bazy danych](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), a następnie kliknij przycisk **Zastosuj**.  Połączenie z wystąpieniem SQL Server jest wymagane, aby środowisko uruchomieniowe usługi Azure Functions utworzyć bazę danych do obsługi środowiska uruchomieniowego.

    ![środowisko uruchomieniowe usługi Azure Functions konfigurację bazy danych w wersji zapoznawczej][8]

1. Kliknij kartę **poświadczenia** .  W tym miejscu należy utworzyć dwa nowe poświadczenia do użycia z udziałem plików do hostowania wszystkich aplikacji funkcji.  Określ kombinacje **nazw użytkowników** i **haseł** dla **właściciela udziału plików** i dla **użytkownika udział plików**, a następnie kliknij przycisk **Zastosuj**.

    ![Poświadczenia środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej][9]

1. Kliknij kartę **udział plików** .  W tym miejscu należy określić szczegóły lokalizacji udziału plików.  Udział plików można utworzyć dla Ciebie lub użyć istniejącego udziału plików, a następnie kliknąć przycisk **Zastosuj**.  W przypadku wybrania nowej lokalizacji udziału plików należy określić katalog do użycia przez środowisko uruchomieniowe usługi Azure Functions.

    ![środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej udziału plików][10]

1. Kliknij kartę **IIS** .  Na tej karcie są wyświetlane szczegółowe informacje o witrynach sieci Web w usługach IIS, które tworzy narzędzie konfiguracji środowisko uruchomieniowe usługi Azure Functions.  W tym miejscu możesz określić niestandardową nazwę DNS dla portalu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej.  Kliknij przycisk **Zastosuj** , aby zakończyć.

    ![środowisko uruchomieniowe usługi Azure Functions Podgląd usług IIS][11]

1. Kliknij kartę **usługi** .  Ta karta przedstawia stan usług w narzędziu konfiguracji środowisko uruchomieniowe usługi Azure Functions.  Jeśli  **Usługa aktywacji hosta Azure Functions** nie jest uruchomiona po początkowej konfiguracji, kliknij przycisk **Uruchom usługę**.

    ![Zakończono środowisko uruchomieniowe usługi Azure Functions konfigurację podglądu][12]

1. Przejdź do **portalu środowisko uruchomieniowe usługi Azure Functions** jako `https://<machinename>.<domain>/` .

    ![Portal środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Tworzenie pierwszej funkcji w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions

Aby utworzyć swoją pierwszą funkcję w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions

1. Przejdź do **portalu środowisko uruchomieniowe usługi Azure Functions** , jak `https://<machinename>.<domain>` na przykład `https://mycomputer.mydomain.com` .

1. Zostanie wyświetlony monit o **zalogowanie** się, jeśli został wdrożony w domenie, użyj nazwy użytkownika i hasła konta domeny, w przeciwnym razie użyj nazwy użytkownika i hasła konta lokalnego, aby zalogować się do portalu.

    ![Logowanie do portalu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej][14]

1. Aby tworzyć aplikacje funkcji, należy utworzyć subskrypcję.  W lewym górnym rogu portalu kliknij **+** opcję obok subskrypcji.

    ![Subskrypcje portalu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej][15]

1. Wybierz pozycję **DefaultPlan**, wprowadź nazwę subskrypcji, a następnie kliknij pozycję **Utwórz**.

    ![środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej planu subskrypcji i nazwy portalu][16]

1. Wszystkie aplikacje funkcji są wyświetlane w okienku po lewej stronie portalu.  Aby utworzyć nowy aplikacja funkcji, wybierz pozycję **aplikacje funkcji** i kliknij **+** opcję.

1. Wprowadź nazwę aplikacji funkcji, wybierz odpowiednią subskrypcję, wybierz wersję środowiska uruchomieniowego Azure Functions, w której chcesz programować, a następnie kliknij pozycję **Utwórz** .

    ![środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej portalu Nowa aplikacja funkcji][17]

1. Nowa aplikacja funkcji zostanie wyświetlona w okienku po lewej stronie portalu.  Wybierz pozycję funkcje, a następnie kliknij pozycję **Nowa funkcja** w górnej części środkowego okienka w portalu.

    ![Szablony podglądu środowisko uruchomieniowe usługi Azure Functions][18]

1. Wybierz funkcję wyzwalacza czasomierza, w oknie wysuwanym po prawej stronie Nazwij funkcję i Zmień harmonogram na `*/5 * * * * *` (to wyrażenie firmy CRONUS powoduje, że funkcja Timer jest uruchamiana co pięć sekund), a następnie kliknij pozycję **Utwórz** .

    ![środowisko uruchomieniowe usługi Azure Functions Podgląd nowej konfiguracji funkcji czasomierza][19]

1. Twoja funkcja została teraz utworzona.  Dziennik wykonywania aplikacji funkcji można wyświetlić, rozwijając okienko **Dziennik** w dolnej części portalu.

    ![Wykonywanie funkcji w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
