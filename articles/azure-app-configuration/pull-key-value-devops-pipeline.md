---
title: Konfiguracja aplikacji settingsfromo z użyciem Azure Pipelines
description: Dowiedz się, jak używać Azure Pipelines do ściągania wartości klucza do magazynu konfiguracji aplikacji
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 7bd163781203a277f4c9d6866a156c11e4d5d520
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979576"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Ustawienia ściągnięcia do konfiguracji aplikacji z Azure Pipelines

Zadanie [konfiguracji aplikacji platformy Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) pobiera wartości klucza z magazynu konfiguracji aplikacji i ustawia je jako zmienne potokowe platformy Azure, które mogą być używane przez kolejne zadania. To zadanie uzupełnia zadanie [wypychania konfiguracji aplikacji platformy Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) , które służy do wypchnięcia wartości klucza z pliku konfiguracji do magazynu konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [Ustawienia wypychania do konfiguracji aplikacji przy użyciu Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Magazyn konfiguracji aplikacji — możesz utworzyć jeden bezpłatnie w [Azure Portal](https://portal.azure.com).
- Projekt usługi Azure DevOps — [Utwórz go bezpłatnie](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Zadanie konfiguracji aplikacji platformy Azure — Pobierz bezpłatnie z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Utwórz połączenie usługi

[Połączenie z usługą](/azure/devops/pipelines/library/service-endpoints) umożliwia dostęp do zasobów w ramach subskrypcji platformy Azure z projektu usługi Azure DevOps.

1. W usłudze Azure DevOps przejdź do projektu zawierającego potok docelowy i Otwórz **Ustawienia projektu** w lewym dolnym rogu.
1. W obszarze **potoki** wybierz pozycję **połączenia usługi**.
1. Jeśli nie masz żadnych istniejących połączeń usług, kliknij przycisk **Utwórz połączenie z usługą** w środku ekranu. W przeciwnym razie kliknij pozycję **nowe połączenie usługi** w prawym górnym rogu strony.
1. Wybierz **Azure Resource Manager**.
1. Wybierz **nazwę główną usługi (automatycznie)**.
1. Wypełnij swoją subskrypcję i zasób. Nadaj nazwę połączeniu z usługą.

Po utworzeniu połączenia usługi Znajdź nazwę przypisanej do niej nazwy głównej usługi. W następnym kroku dodasz nowe przypisanie roli do tej jednostki usługi.

1. Przejdź do pozycji **Ustawienia projektu**  >  **połączenia usługi**.
1. Wybierz połączenie usługi utworzone w poprzedniej sekcji.
1. Wybierz pozycję Zarządzaj jednostką **usługi**.
1. Zwróć uwagę na wymienioną **nazwę wyświetlaną** .

## <a name="add-role-assignment"></a>Dodaj przypisanie roli

Przypisz odpowiednią rolę konfiguracji aplikacji do połączenia z usługą używaną w ramach zadania, aby zadanie miało dostęp do magazynu konfiguracji aplikacji.

1. Przejdź do magazynu konfiguracji aplikacji docelowej. Przewodnik konfigurowania magazynu konfiguracji aplikacji można znaleźć w temacie [Tworzenie magazynu konfiguracji aplikacji](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) w jednej z przewodników szybki start dotyczących konfiguracji aplikacji platformy Azure.
1. Po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)**.
1. W górnej części wybierz pozycję **+ Dodaj** i wybierz opcję **Dodaj przypisanie roli**.
1. W obszarze **rola** wybierz pozycję **czytnik danych konfiguracji aplikacji**. Ta rola pozwala zadanie odczytać z magazynu konfiguracji aplikacji. 
1. Wybierz nazwę główną usługi skojarzoną z połączeniem usługi, które zostało utworzone w poprzedniej sekcji.

> [!NOTE]
> Aby rozwiązać Azure Key Vault odwołań w ramach konfiguracji aplikacji, połączenie z usługą musi również mieć uprawnienia do odczytu wpisów tajnych w odwołaniach do magazynów kluczy platformy Azure, do których się odwołuje.
  
## <a name="use-in-builds"></a>Użyj w kompilacjach

W tej sekcji zawarto informacje dotyczące korzystania z zadania konfiguracji aplikacji platformy Azure w potoku kompilacji usługi Azure DevOps.

1. Przejdź do strony potoku kompilacji, klikając **pozycję potoki potoki**  >  . Aby zapoznać się z dokumentacją potoku kompilacji, zobacz  [Tworzenie pierwszego potoku](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - W przypadku tworzenia nowego potoku kompilacji kliknij pozycję **Nowy potok**, wybierz repozytorium dla potoku. Wybierz pozycję **Pokaż asystenta** po prawej stronie potoku, a następnie wyszukaj zadanie **konfiguracji aplikacji platformy Azure** .
      - Jeśli używasz istniejącego potoku kompilacji, wybierz pozycję **Edytuj** , aby edytować potok. Na karcie **zadania** Wyszukaj zadanie **Konfiguracja aplikacji platformy Azure** .
1. Skonfiguruj parametry niezbędne do wykonania zadania w celu ściągnięcia wartości klucza z magazynu konfiguracji aplikacji. Opisy parametrów są dostępne w poniższej sekcji **Parametry** i w etykietce narzędzia obok każdego parametru.
      - Ustaw parametr **subskrypcji platformy Azure** na nazwę połączenia usługi utworzonego w poprzednim kroku.
      - W polu **nazwa konfiguracji aplikacji** Ustaw nazwę zasobu magazynu konfiguracji aplikacji.
      - Pozostaw wartości domyślne pozostałych parametrów.
1. Zapisz i zakolejkowaniaj kompilację. W dzienniku kompilacji zostaną wyświetlone wszystkie błędy, które wystąpiły podczas wykonywania zadania.

## <a name="use-in-releases"></a>Użyj w wersjach

W tej sekcji zawarto informacje dotyczące korzystania z zadania konfiguracji aplikacji platformy Azure w potoku wydania usługi Azure DevOps.

1. Przejdź do strony potoku wydania, wybierając pozycję wersje **potoków**  >  . Aby uzyskać dokumentację potoku wydania, zobacz temat [potoki wersji](/azure/devops/pipelines/release).
1. Wybierz istniejący potok wersji. Jeśli go nie masz, kliknij pozycję **Nowy potok** , aby utworzyć nowy.
1. Wybierz przycisk **Edytuj** w prawym górnym rogu, aby edytować potoku wydania.
1. Wybierz **etap** , aby dodać zadanie. Aby uzyskać więcej informacji na temat etapów, zobacz [Dodawanie etapów, zależności, & warunków](/azure/devops/pipelines/release/environments).
1. Kliknij pozycję **+** "Uruchom w agencie", a następnie Dodaj zadanie **konfiguracji aplikacji platformy Azure** na karcie **Dodawanie zadań** .
1. Skonfiguruj niezbędne parametry w ramach zadania, aby ściągnąć kluczowe wartości z magazynu konfiguracji aplikacji. Opisy parametrów są dostępne w poniższej sekcji **Parametry** i w etykietce narzędzia obok każdego parametru.
      - Ustaw parametr **subskrypcji platformy Azure** na nazwę połączenia usługi utworzonego w poprzednim kroku.
      - W polu **nazwa konfiguracji aplikacji** Ustaw nazwę zasobu magazynu konfiguracji aplikacji.
      - Pozostaw wartości domyślne pozostałych parametrów.
1. Zapisz i wydawanie kolejki. W dzienniku zlecenia zostaną wyświetlone wszystkie błędy, które wystąpiły podczas wykonywania zadania.

## <a name="parameters"></a>Parametry

Następujące parametry są używane przez zadanie konfiguracji aplikacji platformy Azure:

- **Subskrypcja platformy Azure**: Lista rozwijana zawierająca dostępne połączenia usługi platformy Azure. Aby zaktualizować i odświeżyć listę dostępnych połączeń usługi platformy Azure, kliknij przycisk **Odśwież subskrypcję platformy Azure** z prawej strony pola tekstowego.
- **Nazwa konfiguracji aplikacji**: Lista rozwijana, która ładuje dostępne magazyny konfiguracji w ramach wybranej subskrypcji. Aby zaktualizować i odświeżyć listę dostępnych magazynów konfiguracji, naciśnij przycisk **Odśwież nazwę konfiguracji aplikacji** z prawej strony pola tekstowego.
- **Filtr klucza**: filtr służy do wybierania wartości kluczowych żądanych z konfiguracji aplikacji platformy Azure. Wartość * spowoduje wybranie wszystkich wartości klucza. Aby uzyskać więcej informacji na temat, zobacz [wartości klucza zapytania](concept-key-value.md#query-key-values).
- **Etykieta**: określa, która etykieta ma być używana podczas wybierania wartości klucza z magazynu konfiguracji aplikacji. Jeśli nie podano etykiety, wówczas wartości kluczy z etykietą No nie zostaną pobrane. Następujące znaki są niedozwolone:, *.
- **Prefiks klucza przycinania**: określa co najmniej jedną prefiks, który ma zostać przycięty z kluczy konfiguracji aplikacji przed ustawieniem ich jako zmiennych. Wielokrotne prefiksy mogą być oddzielone znakami nowego wiersza.

## <a name="use-key-values-in-subsequent-tasks"></a>Używanie wartości klucza w kolejnych zadaniach

Wartości kluczy pobierane z konfiguracji aplikacji są ustawiane jako zmienne potokowe, które są dostępne jako zmienne środowiskowe. Klucz zmiennej środowiskowej jest kluczem wartości klucza, który jest pobierany z konfiguracji aplikacji po przycięciu prefiksu, jeśli jest określony.

Na przykład jeśli kolejne zadanie uruchamia skrypt programu PowerShell, może użyć wartości klucza z kluczem "myBuildSetting" w następujący sposób:
```powershell
echo "$env:myBuildSetting"
```
A wartość zostanie wydrukowany w konsoli programu.

> [!NOTE]
> Odwołania Azure Key Vault w ramach konfiguracji aplikacji zostaną rozwiązane i ustawione jako [zmienne tajne](/azure/devops/pipelines/process/variables#secret-variables). W przypadku potoków platformy Azure zmienne tajne są maskowane z dziennika. Nie są one przenoszone do zadań jako zmienne środowiskowe i muszą zostać przesłane jako dane wejściowe. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi nieoczekiwany błąd, Dzienniki debugowania można włączyć, ustawiając zmienną potoku `system.debug` na `true` .

## <a name="faq"></a>Często zadawane pytania

**Jak mogę utworzyć moją konfigurację z wielu kluczy i etykiet?**

Czasami może być konieczne tworzenie konfiguracji z wielu etykiet, na przykład domyślnych i deweloperskich. Aby zaimplementować ten scenariusz, można użyć wielu zadań konfiguracyjnych aplikacji w jednym potoku. Wartości klucza pobrane przez zadanie w późniejszym kroku zastąpią wszelkie wartości z poprzednich kroków. W powyższym przykładzie zadanie może służyć do wybierania wartości kluczy z domyślną etykietą, podczas gdy drugie zadanie może wybrać wartości klucza z etykietą dev. Klucze z etykietą dev przesłonią te same klucze z domyślną etykietą.
