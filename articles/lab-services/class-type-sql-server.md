---
title: Skonfiguruj laboratorium do zarządzania i programowania przy użyciu Azure SQL Database | Azure Lab Services
description: Dowiedz się, jak skonfigurować laboratorium do zarządzania i programowania przy użyciu Azure SQL Database.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9fc0a965869207ba8d1b4eb6f45e878ae4b93c3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88079026"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Skonfiguruj laboratorium do zarządzania i programowania przy użyciu SQL Server

W tym artykule opisano sposób konfigurowania laboratorium na potrzeby podstawowej klasy zarządzania i programowania SQL Server w Azure Lab Services.  Pojęcia dotyczące baz danych są jednym z kursów wprowadzających w większości działów nauki komputerowych w szkole. Structured Query Language (SQL) to międzynarodowy standard.  SQL to standardowy język zarządzania bazami danych, w tym dodawanie, uzyskiwanie dostępu do zawartości i zarządzanie nią w bazie danych.  Jest to najbardziej zanotowane dla szybkiego przetwarzania, sprawdzonej niezawodności, prostoty i elastyczności.

W tym artykule przedstawiono sposób konfigurowania szablonu maszyny wirtualnej w laboratorium przy użyciu [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)i [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  W przypadku tego laboratorium będziemy używać jednej udostępnionej [SQL Server bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) dla całego laboratorium. [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) to oferta aparatu bazy danych platformy jako usługi (PaaS) z platformy Azure.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów z witryny Marketplace, zobacz [Określanie obrazów z portalu Marketplace dostępnych dla twórców laboratorium](specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
| Obraz witryny Marketplace | Włącz obraz "Visual Studio 2019 Community (Najnowsza wersja) w systemie Windows 10 Enterprise N (x64)", który ma być używany w ramach Twojego konta laboratorium. |

### <a name="shared-resource-configuration"></a>Konfiguracja zasobów udostępnionych

Aby użyć zasobu udostępnionego w usługach laboratoryjnych, należy najpierw utworzyć sieć wirtualną i zasoby.  Aby utworzyć sieć wirtualną i połączyć ją z laboratorium, postępuj zgodnie z instrukcjami [w temacie Jak utworzyć laboratorium z udostępnionym zasobem w Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Pamiętaj, że wszystkie zasoby spoza usług Lab są rozliczane osobno i nie zostaną uwzględnione w szacunkach kosztów laboratorium.

>[!WARNING]
>Przed utworzeniem laboratorium należy skonfigurować zasoby udostępnione dla laboratorium.  Jeśli sieć wirtualna nie jest połączona [z kontem laboratorium](how-to-connect-peer-virtual-network.md) *przed* utworzeniem laboratorium, laboratorium nie będzie miało dostępu do zasobu udostępnionego.

Teraz, gdy strona sieci jest obsługiwana, umożliwia utworzenie bazy danych SQL Server.  Zamierzamy utworzyć [pojedynczą bazę danych](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal) , ponieważ jest to najszybsza opcja wdrażania dla Azure SQL Database.  W przypadku innych opcji wdrażania Utwórz [pulę elastyczną](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)lub [maszynę wirtualną SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal).

1. Z menu Azure Portal wybierz pozycję **Utwórz nowy zasób**.
2. Wybierz **SQL Database** i kliknij przycisk **Utwórz** .
3. Na karcie **podstawowe** w formularzu **Tworzenie bazy danych SQL** wybierz grupę zasobów dla bazy danych.  Będziemy używać *SQLDB-RG*.
4. W obszarze **Nazwa bazy danych**wprowadź *classlabdb*.
5. W obszarze Ustawienia **serwera** kliknij pozycję **Utwórz nowy** , aby utworzyć nowy serwer, na którym ma być przechowywana baza danych.
6. W oknie wysuwanym **nowy serwer** wprowadź nazwę serwera.  Będziemy używać *classlabdbserver*.  Nazwa serwera musi być globalnie unikatowa.
7. Wprowadź *azureuser* dla **identyfikatora logowania administratora serwera**.
8. Wprowadź zapomniane hasło.  Hasło musi mieć długość co najmniej ośmiu znaków i zawierać znaki specjalne.
9. Wybierz region dla **lokalizacji**.  Jeśli to możliwe, wprowadź tę samą lokalizację, w której znajduje się konto laboratorium i równorzędna Sieć wirtualna, aby zminimalizować opóźnienie.
10. Kliknij przycisk **OK** , aby powrócić do formularza **tworzenia SQL Database** .
11. Kliknij pozycję **Konfiguruj bazę danych** w obszarze Ustawienia **obliczenia + magazyn** .
12. Zmodyfikuj ustawienia bazy danych zgodnie z wymaganiami klasy.  Można wybrać opcję między opcją zainicjowaną i bezserwerową.  Na potrzeby tego przykładu użyjemy opcji bezserwerowego skalowania automatycznego z maksymalną rdzeni wirtualnychą 4, minimum rdzeni wirtualnych z 1. Ustawienie AutoPause zostanie zachowane co najmniej 1 godzina. Kliknij pozycję **Zastosuj**.
13. Kliknij przycisk **Dalej: sieć** przycisk.
14. Na karcie Sieć wybierz pozycję prywatny punkt końcowy dla **metody łączności**.
15. W sekcji **prywatne punkty końcowe** kliknij pozycję **Dodaj prywatny punkt końcowy**.
16. W menu wysuwanym **Tworzenie prywatnego punktu końcowego** wybierz tę samą grupę zasobów, która została połączona z siecią wirtualną za pomocą komunikacji równorzędnej z kontem laboratorium.
17. W polu **Lokalizacja**wybierz tę samą lokalizację co sieć wirtualna.
18. W obszarze **Nazwa**wprowadź *labsql-Endpt*.
19. Pozostaw element docelowy elementu docelowego ustawiony na wartość SqlServer.
20. W obszarze **Sieć wirtualna**wybierz tę samą sieć wirtualną równorzędną do konta laboratorium.
21. W obszarze **podsieć**wybierz pozycję podsieć, w której ma być hostowany punkt końcowy.  Adres IP przypisany do punktu końcowego będzie należeć do zakresu przypisanego do tej podsieci.
22. Ustaw **integrację z prywatnym systemem DNS** na wartość **nie**. Dla uproszczenia będziemy używać systemu DNS platformy Azure za pośrednictwem prywatnej strefy DNS i własnych serwerów DNS.
23. Kliknij przycisk **OK**.
24. Kliknij przycisk **Dalej: Ustawienia dodatkowe**.
25. Dla ustawienia **Użyj istniejących danych** wybierz pozycję **przykład**.  Dane z bazy danych AdventureWorksLT będą używane podczas tworzenia bazy danych.
26. Kliknij pozycję **Przejrzyj i utwórz**.
27. Kliknij przycisk **Utwórz**.

Po pomyślnym zakończeniu wdrażania SQL Database można utworzyć laboratorium i zainstalować oprogramowanie na komputerze szablonu laboratorium.

### <a name="lab-settings"></a>Ustawienia laboratorium

W poniższej tabeli przedstawiono ustawienia, które należy wykonać podczas konfigurowania laboratorium zajęć. Aby uzyskać więcej informacji na temat tworzenia laboratorium klasy, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej | Średnia. Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy. |
| Obraz maszyny wirtualnej | Visual Studio 2019 Community (Najnowsza wersja) w systemie Windows 10 Enterprise N (x64) |

Teraz, gdy nasze laboratorium zostało utworzone, zmodyfikujemy maszynę szablonową przy użyciu wymaganego oprogramowania.

## <a name="visual-studio"></a>Visual Studio

Wybrany powyżej obraz zawiera [program Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/).  Wszystkie obciążenia i zestawy narzędzi są już zainstalowane na obrazie.  Użyj Instalator programu Visual Studio, aby [zainstalować dowolne opcjonalne narzędzia](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) .  [Zaloguj się do programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) , aby odblokować wersję Community.

Program Visual Studio zawiera zestaw narzędzi do **przechowywania i przetwarzania danych** , w tym narzędzia do SQL Server Data Tools (SSDT).  Aby uzyskać więcej informacji na temat możliwości SSDT, zobacz [SQL Server Data Tools — Omówienie](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools?view=sql-server-ver15).  Aby sprawdzić, czy połączenie z udostępnioną SQL Server dla klasy zakończyło się pomyślnie, zobacz [nawiązywanie połączenia z bazą danych i przeglądanie istniejących obiektów](https://docs.microsoft.com/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15). Jeśli zostanie wyświetlony monit, Dodaj adres IP komputera szablonu do [listy dozwolonych komputerów](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure) , które mogą połączyć się z wystąpieniem SQL Server.

Program Visual Studio obsługuje kilka obciążeń, w tym **& sieci Web w chmurze** i **pulpitu & dla urządzeń przenośnych** .  Oba te obciążenia obsługują SQL Server jako źródło danych. Aby uzyskać więcej informacji na temat korzystania z ASP.NET Core do SQL Server, zobacz [Tworzenie aplikacji ASP.NET Core i SQL Database w](https://docs.microsoft.com/azure/app-service/tutorial-dotnetcore-sqldb-app) samouczku Azure App Service.  Użyj biblioteki [System. Data. SqlClient](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) , aby nawiązać połączenie z SQL Database z aplikacji platformy [Xamarin](https://docs.microsoft.com/xamarin) .

## <a name="install-azure-data-studio"></a>Zainstaluj Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) to wieloplatformowe środowisko komputerowe dla specjalistów ds. danych korzystające z rodziny lokalnych i chmurowych platform danych w systemach Windows, MacOS i Linux.

1. Pobierz [Instalatora *systemu* Azure Data Studio dla systemu Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Aby znaleźć Instalatory dla innych obsługiwanych systemów operacyjnych, przejdź do strony pobierania [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download) .
2. Na stronie **Umowa licencyjna** zaznacz opcję **Akceptuję Umowę**. Kliknij przycisk **Dalej**.
3. Na stronie **Wybierz lokalizację docelową** kliknij pozycję **Dalej**.
4. Na stronie **Wybierz folder menu Start** kliknij pozycję **Dalej**.
5. Na stronie **Wybierz dodatkowe zadania** Sprawdź **ikonę Utwórz pulpit** , jeśli chcesz mieć ikonę pulpitu.  Kliknij przycisk **Dalej**.
6. Na stronie **gotowy do instalacji**kliknij przycisk **dalej**.
7. Poczekaj na uruchomienie Instalatora.  Kliknij przycisk **Finish** (Zakończ).

Teraz, gdy zainstalowano Azure Data Studio, Skonfigurujmy połączenie z Azure SQL Database.

1. Na stronie **powitalnej** Azure Data Studio kliknij link **nowe połączenie** .
2. W polu **szczegóły połączenia** wprowadź niezbędne informacje.
    - Ustaw **serwer** na *classlabdbserver.Database.Windows.NET*
    - Ustaw nazwę **użytkownika** na *azureuser*
    - Ustaw **hasło** do hasła używanego do tworzenia bazy danych.
    - Zaznacz pole wyboru **Zapamiętaj hasło**.
    - W obszarze **baza danych**wybierz pozycję *classlabdb*.
3. Kliknij przycisk **Połącz**.

## <a name="install-sql-server-management-studio"></a>Zainstaluj SQL Server Management Studio

[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) to zintegrowane środowisko do zarządzania dowolną infrastrukturą SQL.  Program SSMS jest narzędziem używanym przez administratorów bazy danych do wdrażania, monitorowania i uaktualniania infrastruktury danych.

1. [Pobierz Management Studio programu SQL Server](https://aka.ms/ssmsfullsetup). Po pobraniu Uruchom Instalatora.
2. Na stronie **Zapraszamy** kliknij przycisk **Instaluj**.
3. Na stronie **Instalacja ukończona** kliknij przycisk **Zamknij**.
4. Uruchom program SQL Server Management Studio.  
5. Na stronie **proces konfiguracji zależności** kliknij przycisk **Zamknij**.

Przed zainstalowaniem programu SSMS można [nawiązać połączenie i wykonać zapytanie dotyczące SQL Server](https://docs.microsoft.com/sql/ssms/tutorials/connect-query-sql-server). Podczas konfigurowania połączenia należy użyć następujących wartości:

- Typ serwera: aparat bazy danych
- Nazwa serwera: *classlabdbserver.Database.Windows.NET*
- Uwierzytelnianie: uwierzytelnianie SQL Server
- Logowanie: *azureuser*
- Hasło: hasło używane do tworzenia bazy danych.

## <a name="cost-estimate"></a>Oszacowanie kosztów

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy. Szacowanie nie obejmuje kosztu uruchomienia SQL Server.  Zobacz [cennik SQL Database](https://azure.microsoft.com/pricing/details/sql-database) , aby uzyskać aktualne informacje o cenach bazy danych.

Będziemy używać klasy 25 studentów. Zaplanowana godzina klasy wynosi 20 godzin. Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy. Wybrany rozmiar maszyny wirtualnej to średnia, czyli 42 jednostek laboratorium.

Oto przykład możliwego oszacowania kosztów dla tej klasy:

25 studentów \* (20 zaplanowanych godzin \+ 10 godzin przydziału) * 0,42 USD za godzinę = 315,00 USD

>[!IMPORTANT]
>Oszacowanie kosztów odbywa się tylko na przykład. Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie i publikowanie szablonu oraz zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)
