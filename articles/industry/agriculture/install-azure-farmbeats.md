---
title: Zainstaluj projekt Azure FarmBeats
description: W tym artykule opisano sposób instalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79479563"
---
# <a name="install-azure-farmbeats"></a>Zainstaluj projekt Azure FarmBeats

W tym artykule opisano sposób instalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure.

Azure FarmBeats to oferta biznesowa dostępna w witrynie Azure Marketplace. Umożliwia ona agregację zestawów danych rolnictwa między dostawcami i generowanie szczegółowych informacji umożliwiających podjęcie odpowiednich działań. Usługa Azure FarmBeats pozwala na tworzenie modeli sztucznej inteligencją (AI) lub uczenia maszynowego (ML) w oparciu o odrzucane zestawy danych. Dwa główne składniki usługi Azure FarmBeats:

- **Datahub**: Warstwa interfejsu API, która umożliwia agregację, normalizację i contextualization różnych zestawów danych w rolnictwie różnych dostawców.

- **Akcelerator**: aplikacja sieci Web, która jest oparta na Datahub. Przeskoczy — umożliwia rozpoczęcie tworzenia modeli i wizualizacji. Akcelerator używa interfejsów API usługi Azure FarmBeats, aby przedstawić wizualizację pozyskiwanych danych z czujnika jako wykresów i wizualizacji wyników dla modelu jako mapy.

## <a name="general-information"></a>Informacje ogólne

### <a name="components-installed"></a>Zainstalowane składniki

Podczas instalowania usługi Azure FarmBeats są obsługiwane następujące zasoby w ramach subskrypcji platformy Azure:

| Zainstalowane zasoby platformy Azure  | Składnik Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Akcelerator & Datahub      |
| App Service     |     Akcelerator & Datahub     |
| Plan usługi App Service   | Akcelerator & Datahub  |
| Połączenie interfejsu API    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Akcelerator & Datahub      |
| Konto usługi Azure Batch    | Datahub   |
| W usłudze Azure Key Vault |  Akcelerator & Datahub        |
| Konto Azure Maps       |     Skrót    |
| Przestrzeń nazw centrum zdarzeń    |     Datahub      |
| Aplikacja logiki      |  Datahub       |
| Konto magazynu      |     Akcelerator & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Poniesione koszty

Koszt usługi Azure FarmBeats to zagregowany koszt podstawowych usług platformy Azure. Informacje o cenach dla usług platformy Azure można obliczyć przy użyciu [kalkulatora cen](https://azure.microsoft.com/pricing/calculator). Rzeczywisty koszt całkowitej instalacji będzie się różnić w zależności od użycia. Koszt stałego stanu dla dwóch składników:

- Datahub-mniej niż $10 dziennie
- Akcelerator-mniejszy niż $2 dziennie

### <a name="regions-supported"></a>Obsługiwane regiony

Obecnie platforma Azure FarmBeats jest obsługiwana w środowiskach chmury publicznej w następujących regionach:

- Australia Wschodnia
- Central US
- East US
- Wschodnie stany USA 2
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- West Europe
- Azja Wschodnia
- Azja Południowo-Wschodnia

### <a name="time-taken"></a>Czas trwania

Cała konfiguracja usługi Azure FarmBeats, łącznie z przygotowaniem i instalacją, zajmie mniej niż godzinę.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem rzeczywistej instalacji usługi Azure FarmBeats należy wykonać następujące czynności:

### <a name="verify-permissions"></a>Weryfikuj uprawnienia

Do zainstalowania usługi Azure FarmBeats wymagane są następujące uprawnienia w dzierżawie platformy Azure:

- Kreator aplikacji dzierżawcy — AAD
- Subskrypcja — właściciel
- Grupa zasobów, w której jest instalowana FarmBeats — właściciel

Pierwsze dwa uprawnienia są niezbędne do [utworzenia kroku aplikacji usługi AAD](#create-an-aad-application) . W razie potrzeby możesz uzyskać kogoś z odpowiednimi uprawnieniami do tworzenia aplikacji usługi AAD.

Osoba, która uruchamia instalację FarmBeats z witryny Marketplace, musi być właścicielem grupy zasobów, w której jest instalowany FarmBeats. W przypadku właścicieli subskrypcji jest to wykonywane automatycznie podczas tworzenia grupy zasobów. W przypadku innych użytkowników należy najpierw utworzyć grupę zasobów i poproś właściciela subskrypcji o utworzenie właściciela grupy zasobów.

Możesz sprawdzić uprawnienia dostępu w Azure Portal, postępując zgodnie z instrukcjami na stronie [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Wybieranie subskrypcji i regionu

Będziesz potrzebować identyfikatora subskrypcji platformy Azure i regionu, w którym chcesz zainstalować usługę Azure FarmBeats. Wybierz jeden z regionów wymienionych w sekcji [Obsługiwane regiony](#regions-supported) .

Zanotuj **Identyfikator subskrypcji platformy Azure** i **region świadczenia usługi Azure**.

### <a name="create-an-aad-application"></a>Tworzenie aplikacji usługi AAD

Usługa Azure FarmBeats wymaga Azure Active Directory tworzenia i rejestrowania aplikacji. Aby pomyślnie uruchomić skrypt tworzenia usługi AAD, są konieczne następujące uprawnienia:

- Kreator aplikacji dzierżawcy — AAD
- Subskrypcja — właściciel

Wykonaj następujące kroki w wystąpieniu Cloud Shell przy użyciu środowiska programu PowerShell. Użytkownicy korzystający z pierwszego czasu będą monitowani o wybranie subskrypcji i utworzenie konta magazynu. Ukończ instalację zgodnie z instrukcjami.

1. Pobierz [skrypt generatora aplikacji usługi AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Domyślnie plik jest pobierany do katalogu macierzystego. Przejdź do katalogu.

    ```azurepowershell-interactive
        cd
    ```

3. Uruchom skrypt usługi AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skrypt pyta o następujące trzy dane wejściowe:

    - **Nazwa witryny internetowej FarmBeats**: jest to unikatowy prefiks adresu URL aplikacji sieci Web FarmBeats. W przypadku, gdy prefiks został już pobrany, skrypt wystąpi błąd. Po zainstalowaniu wdrożenie FarmBeats będzie dostępne z https:// \<FarmBeats-website-name> . azurewebsites.NET, a interfejsy API programu Swagger będą w https:// \<FarmBeats-website-name> -API.azurewebsites.NET

    - **Identyfikator logowania platformy Azure**: podaj identyfikator logowania platformy Azure dla użytkownika, który ma zostać dodany jako administrator FarmBeats. Ten użytkownik może następnie udzielić dostępu FarmBeats aplikacji sieci Web innym użytkownikom. Identyfikator logowania ma zwykle postać john.doe@domain.com . Dostępna jest również nazwa UPN platformy Azure.

    - **Identyfikator subskrypcji**: to jest Identyfikator subskrypcji, w której chcesz zainstalować usługę Azure FarmBeats

5. Skrypt usługi AAD zajmuje około 2 minut na uruchomienie i wyjście wartości na ekranie oraz do pliku JSON w tym samym katalogu. Jeśli ktoś inny uruchomił skrypt, poproś go o udostępnienie danych wyjściowych.

### <a name="create-sentinel-account"></a>Utwórz konto wskaźnikiem

Twoja konfiguracja usługi Azure FarmBeats umożliwia pobieranie obrazów satelitarnych z satelitarnej agencji kosmicznej z [branży danych firmy](https://scihub.copernicus.eu/) Microsoft dla farmy. Aby skonfigurować tę konfigurację, wymagane jest konto wskaźnikiem.

Postępuj zgodnie z instrukcjami, aby utworzyć bezpłatne konto przy użyciu kontrolki wskaźnikowej:

1. Przejdź do oficjalnej strony [rejestracji](https://aka.ms/SentinelRegistration) .
2. Podaj wymagane szczegóły (imię, nazwisko, nazwę użytkownika, hasło i identyfikator poczty e-mail) i wypełnij formularz.
3. Do zarejestrowanego identyfikatora wiadomości zostanie wysłany link weryfikacyjny. Wybierz link podany w wiadomości e-mail i Ukończ weryfikację.

Proces rejestracji został ukończony. Zanotuj **nazwę użytkownika** i hasło do urządzenia **wskaźnikowego**, po zakończeniu weryfikacji.

## <a name="install"></a>Instalowanie

Teraz można przystąpić do instalowania FarmBeats. Wykonaj poniższe kroki, aby rozpocząć instalację:

1. Zaloguj się do Portalu Azure. Wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD, w której chcesz zainstalować usługę Azure FarmBeats.

2. Przejdź do witryny Azure Marketplace w portalu i Wyszukaj **usługę Azure FarmBeats** w witrynie Marketplace.

3. Zostanie wyświetlone nowe okno z omówieniem usługi Azure FarmBeats. Wybierz przycisk **Utwórz**.

4. Zostanie wyświetlone nowe okno. Ukończ proces rejestracji, wybierając poprawną subskrypcję, grupę zasobów i lokalizację, w której chcesz zainstalować usługę Azure FarmBeats.

5. Podaj adres e-mail, który powinien otrzymywać alerty usługi związane z usługą Azure FarmBeats w sekcji **alertów usługi FarmBeats** . Wybierz pozycję **dalej** w dolnej części strony, aby przejść do karty **zależności** .

    ![Karta podstawy](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Skopiuj pojedyncze wpisy z danych wyjściowych [skryptu usługi AAD](#create-an-aad-application) do wejść w sekcji aplikacji usługi AAD.

7. Wprowadź nazwę użytkownika [konta wskaźnikowego](#create-sentinel-account) i hasło w sekcji konto wskaźnikiem. Wybierz pozycję **dalej** , aby przejść do karty **Recenzja + tworzenie** .

    ![Karta zależności](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Po sprawdzeniu poprawności wprowadzonych szczegółów wybierz **przycisk OK**. Zostanie wyświetlona strona Warunki użytkowania. Przejrzyj warunki i wybierz pozycję **Utwórz** , aby rozpocząć instalację. Nastąpi przekierowanie do strony, na której można wykonać postęp instalacji.

Po zakończeniu instalacji możesz zweryfikować instalację i rozpocząć korzystanie z portalu FarmBeats, przechodząc do nazwy witryny sieci Web podanej podczas instalacji: https:// \<FarmBeats-website-name> . azurewebsites.NET. Powinien zostać wyświetlony interfejs użytkownika FarmBeats z opcją tworzenia Farm.

**Datahub** można znaleźć pod adresem https:// \<FarmBeats-website-name> -API.azurewebsites.NET/Swagger. W tym miejscu zobaczysz różne obiekty interfejsu API FarmBeats i wykonamy operacje REST w interfejsach API.

## <a name="upgrade"></a>Uaktualnienie

Aby uaktualnić FarmBeats do najnowszej wersji, uruchom następujące kroki w wystąpieniu Cloud Shell przy użyciu środowiska programu PowerShell. Użytkownik musi być właścicielem subskrypcji, w której zainstalowano FarmBeats.

Użytkownicy korzystający z pierwszego czasu będą monitowani o wybranie subskrypcji i utworzenie konta magazynu. Ukończ instalację zgodnie z instrukcjami.

1. Pobierz [skrypt uaktualnienia](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Domyślnie plik jest pobierany do katalogu macierzystego. Przejdź do katalogu.

    ```azurepowershell-interactive
        cd
    ```

3. Uruchamianie skryptu uaktualnienia

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Ścieżka do input.jsw pliku jest opcjonalna. Jeśli nie zostanie określony, skrypt będzie pytał wszystkie wymagane dane wejściowe. Uaktualnienie powinno zakończyć się około 30 minut.

## <a name="uninstall"></a>Odinstalowanie

Aby odinstalować usługę Azure FarmBeats Datahub lub akcelerator, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i **Usuń grupy zasobów** , w których są zainstalowane te składniki.

2. Przejdź do Azure Active Directory & **usunąć aplikacji usługi Azure AD** połączonej z instalacją Azure FarmBeats.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak zainstalować usługę Azure FarmBeats w ramach subskrypcji platformy Azure. Teraz Dowiedz się, jak [dodać użytkowników](manage-users-in-azure-farmbeats.md#manage-users) do wystąpienia usługi Azure FarmBeats.
