---
title: 'Szybki Start: Tworzenie wystąpienia trybu hybrydowego z Azure Portal'
titleSuffix: Azure Database Migration Service
description: Użyj Azure Portal, aby utworzyć wystąpienie Azure Database Migration Service w trybie hybrydowym.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: e63f073c4f7166cd205b85ef06589f6056b2a70f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84195688"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Szybki Start: Tworzenie wystąpienia trybu hybrydowego z Azure Portal & Azure Database Migration Service

Azure Database Migration Service Tryb hybrydowy zarządza migracjami baz danych przy użyciu procesu roboczego migracji hostowanego lokalnie wraz z wystąpieniem Azure Database Migration Service uruchomionym w chmurze. Tryb hybrydowy jest szczególnie przydatny w scenariuszach, w których między siecią lokalną a platformą Azure nie ma łączności między lokacjami lub w przypadku ograniczonej przepustowości połączenia typu lokacja-lokacja.

>[!NOTE]
>Obecnie Azure Database Migration Service działające w trybie hybrydowym obsługuje migracje SQL Server do programu:
>
>- Wystąpienie zarządzane Azure SQL z bliskim przestojem (online).
>- Azure SQL Database pojedynczą bazę danych z czasem przestoju (offline).
>- MongoDb do platformy Azure CosmosDB z niemal zerowym przestojem (online).
>- MongoDb do usługi Azure CosmosDB z pewnym przestojem (offline).

W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć wystąpienie Azure Database Migration Service w trybie hybrydowym. Następnie należy pobrać, zainstalować i skonfigurować hybrydowy proces roboczy w sieci lokalnej. W trakcie korzystania z wersji zapoznawczej można użyć Azure Database Migration Service trybu hybrydowego do migrowania danych z lokalnego wystąpienia SQL Server do Azure SQL Database.

> [!NOTE]
> Azure Database Migration Service Instalatora hybrydowego jest uruchamiany w systemie Microsoft Windows Server 2012 R2, 2019 Windows Server 2016, w systemie i Windows 10.

> [!IMPORTANT]
> Azure Database Migration Service Instalatora hybrydowego wymaga platformy .NET w wersji lub nowszej. Aby znaleźć najnowsze wersje programu .NET, zobacz stronę [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu.

Widok domyślny to pulpit nawigacyjny usług.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Zarejestruj dostawcę zasobów Microsoft. datamigration przed utworzeniem pierwszego wystąpienia Azure Database Migration Service.

1. W Azure Portal wybierz pozycję **subskrypcje**, wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyszukaj dostawcę zasobów](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration**wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi

1. Wybierz pozycję +**Utwórz zasób** , aby utworzyć wystąpienie Azure Database Migration Service.

2. Wyszukaj ciąg "migracja" w portalu Marketplace, wybierz pozycję **Azure Database Migration Service**, a następnie na ekranie **Azure Database Migration Service** wybierz pozycję **Utwórz**.

3. Na ekranie **Tworzenie usługi migracji**:

    - Wybierz **nazwę usługi** , która jest dopamiętana i unikatowa, aby zidentyfikować wystąpienie Azure Database Migration Service.
    - Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć wystąpienie.
    - Wybierz istniejącą **grupę zasobów** lub Utwórz nową.
    - Wybierz pozycję **Lokalizacja** położoną najbliżej Twojego serwera źródłowego lub docelowego.
    - W obszarze **tryb usługi**wybierz pozycję **hybrydowe (wersja zapoznawcza)**.

         ![Tworzenie usługi migracji — podstawy](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Wybierz pozycję **Przeglądanie + tworzenie**.

5. Na karcie **Recenzja + tworzenie** Przejrzyj warunki, sprawdź inne podane informacje, a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie usługi migracji — przegląd + tworzenie](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Po kilku chwilach wystąpienie Azure Database Migration Service w trybie hybrydowym zostanie utworzone i będzie gotowe do skonfigurowania. Wystąpienie Azure Database Migration Service jest wyświetlane jak pokazano na poniższej ilustracji:

    ![Azure Database Migration Service wystąpienie trybu hybrydowego](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Po utworzeniu usługi wybierz pozycję **Właściwości**, a następnie skopiuj wartość wyświetlaną w polu **Identyfikator zasobu** , która będzie używana do instalowania Azure Database Migration Service hybrydowego procesu roboczego.

    ![Azure Database Migration Service właściwości trybu hybrydowego](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Utwórz identyfikator rejestracji aplikacji platformy Azure

Musisz utworzyć identyfikator rejestracji aplikacji platformy Azure, który może być używany przez lokalny proces roboczy, aby komunikować się z Azure Database Migration Service w chmurze.

1. W Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
2. Określ nazwę aplikacji, a następnie w obszarze **obsługiwane typy kont**wybierz typ kont do obsługi, aby określić, kto może korzystać z aplikacji.

    ![Azure Database Migration Service aplikacji do rejestrowania w trybie hybrydowym](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Użyj wartości domyślnych dla pól **URI przekierowania (opcjonalnie)** , a następnie wybierz pozycję **zarejestruj**.

4. Po zakończeniu rejestracji identyfikatora aplikacji Zanotuj **Identyfikator aplikacji (klienta)**, który będzie używany podczas instalowania hybrydowego procesu roboczego.

5. W Azure Portal przejdź do Azure Database Migration Service, wybierz pozycję **Kontrola dostępu (IAM)**, a następnie wybierz pozycję **Dodaj przypisanie roli** , aby przypisać dostęp współautora do identyfikatora aplikacji.

    ![Azure Database Migration Service Przypisywanie roli współautor trybu hybrydowego](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Wybierz opcję **współautor** jako rolę, przypisz dostęp do **użytkownika usługi Azure AD lub jednostki usługi**, a następnie wybierz nazwę identyfikatora aplikacji.

    ![Szczegóły roli współautor Azure Database Migration Service trybu hybrydowego](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Wybierz pozycję **Zapisz** , aby zapisać przypisanie roli dla identyfikatora aplikacji w zasobie Azure Database Migration Service.

## <a name="download-and-install-the-hybrid-worker"></a>Pobieranie i Instalowanie hybrydowego procesu roboczego

1. W Azure Portal przejdź do wystąpienia Azure Database Migration Service.

2. W obszarze **Ustawienia**wybierz pozycję **hybrydowe**, a następnie wybierz pozycję **Pobierz instalatora** , aby pobrać hybrydowy proces roboczy.

    ![Azure Database Migration Service pobierania hybrydowego procesu roboczego](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Wyodrębnij plik ZIP na serwerze, który będzie hostować Azure Database Migration Service hybrydowy proces roboczy.

    > [!IMPORTANT]
    > Azure Database Migration Service Instalatora hybrydowego wymaga platformy .NET w wersji lub nowszej. Aby znaleźć najnowsze wersje programu .NET, zobacz stronę [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

4. W folderze Install zlokalizuj i Otwórz **dmsSettings.jsw** pliku, określ identyfikator **aplikacji** i identyfikator **zasobu**, a następnie Zapisz plik.

    ![Azure Database Migration Service ustawień hybrydowego procesu roboczego](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Wygeneruj certyfikat, którego Azure Database Migration Service może użyć do uwierzytelnienia komunikacji od hybrydowego procesu roboczego przy użyciu następującego polecenia.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Certyfikat jest generowany w folderze instalacji.

    ![Azure Database Migration Service certyfikat hybrydowego procesu roboczego](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. W Azure Portal przejdź do identyfikatora aplikacji, w obszarze **Zarządzaj**wybierz pozycję **Certyfikaty & wpisy tajne**, a następnie wybierz pozycję **Przekaż certyfikat** , aby wybrać wygenerowany certyfikat publiczny.

    ![Azure Database Migration Service przekazywanie certyfikatu hybrydowego procesu roboczego](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Zainstaluj Azure Database Migration Service hybrydowy proces roboczy na serwerze lokalnym, uruchamiając następujące polecenie:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Podczas uruchamiania polecenia install można także użyć następujących parametrów:
    >
    > - **-TelemetryOptOut** — wyłącza wysyłanie danych telemetrycznych przez proces roboczy, ale w minimalnym stopniu loguje się lokalnie.  Instalator nadal wysyła dane telemetryczne.
    > - **-p {InstallLocation}**. Włącza zmianę ścieżki instalacji, która domyślnie jest "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Jeśli Instalator zostanie uruchomiony bez błędu, usługa wyświetli stan online w Azure Database Migration Service i wszystko jest gotowe do migracji baz danych.

    ![Azure Database Migration Service online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Odinstaluj Tryb hybrydowy Azure Database Migration Service

Obecnie odinstalowanie Azure Database Migration Service trybu hybrydowego jest obsługiwane tylko za pośrednictwem programu Azure Database Migration Service Instalatora hybrydowego procesu roboczego na serwerze lokalnym przy użyciu następującego polecenia:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Podczas uruchamiania polecenia Odinstaluj można także użyć parametru "-ReuseCert", który utrzymuje certyfikat AdApp wygenerowany przez przepływ pracy generateCert.  Umożliwia to korzystanie z tego samego certyfikatu, który został wcześniej wygenerowany i przekazany.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Konfigurowanie Azure Database Migration Service hybrydowego procesu roboczego przy użyciu programu PowerShell

Oprócz instalacji Azure Database Migration Service hybrydowego procesu roboczego za pośrednictwem Azure Portal udostępniamy [skrypt programu PowerShell](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) , którego można użyć do zautomatyzowania kroków instalacji procesu roboczego po utworzeniu nowego wystąpienia Azure Database Migration Service w trybie hybrydowym. Skrypt:

1. Tworzy nowy AdApp.
2. Pobiera Instalatora.
3. Uruchamia przepływ pracy generateCert.
4. Przekazuje certyfikat.
5. Dodaje AdApp jako współautor do wystąpienia Azure Database Migration Service.
6. Uruchamia przepływ pracy instalacji.

Ten skrypt jest przeznaczony do szybkiego tworzenia prototypów, gdy użytkownik dysponuje już wszystkimi niezbędnymi uprawnieniami w środowisku. Należy pamiętać, że w środowisku produkcyjnym AdApp i certyfikat mogą mieć inne wymagania, więc skrypt może się nie powieść.

> [!IMPORTANT]
> Ten skrypt zakłada, że istnieje wystąpienie Azure Database Migration Service w trybie hybrydowym oraz że używane konto platformy Azure ma uprawnienia do tworzenia AdApps w dzierżawie oraz modyfikowania kontroli RBAC w ramach subskrypcji.

Wypełnij parametry w górnej części skryptu, a następnie uruchom skrypt z wystąpienia administratora programu PowerShell.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie SQL Server do wystąpienia zarządzanego usługi Azure SQL w trybie online](tutorial-sql-server-managed-instance-online.md) 
>  [Migrowanie SQL Server do Azure SQL Database w trybie offline](tutorial-sql-server-to-azure-sql.md)
