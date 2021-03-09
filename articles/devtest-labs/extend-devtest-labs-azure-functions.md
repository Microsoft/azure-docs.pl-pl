---
title: Rozszerzając Azure DevTest Labs przy użyciu Azure Functions | Microsoft Docs
description: Dowiedz się, jak rozciągnąć Azure DevTest Labs przy użyciu Azure Functions.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 620cda83094ee65f421a5529a9d5b51e505ec48e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501162"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Rozszerzanie usługi DevTest Labs za pomocą usługi Azure Functions
Za pomocą Azure Functions można obsługiwać dodatkowe scenariusze wykraczające poza te, które są już obsługiwane przez DevTest Labs. Azure Functions może służyć do rozbudowania wbudowanych funkcji usługi w celu spełnienia wymagań specyficznych dla firmy. Poniższa lista zawiera niektóre z możliwych scenariuszy. W tym artykule przedstawiono sposób implementacji jednego z tych przykładowych scenariuszy.

- Podaj podsumowanie najwyższego poziomu maszyn wirtualnych w laboratorium
- [Konfigurowanie laboratorium w celu użycia bramy pulpitu zdalnego](configure-lab-remote-desktop-gateway.md)
- Raportowanie zgodności na stronie pomocy technicznej wewnętrznej
- Umożliwienie użytkownikom wykonywania operacji wymagających zwiększonych uprawnień w ramach subskrypcji
- [Uruchamianie przepływów pracy na podstawie zdarzeń DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Omówienie
[Azure Functions](../azure-functions/functions-overview.md) to bezserwerowa platforma obliczeniowa na platformie Azure. Używanie Azure Functions w rozwiązaniu z DevTest Labs umożliwia nam rozszerzanie istniejących funkcji przy użyciu własnego kodu niestandardowego. Aby uzyskać więcej informacji na temat Azure Functions, zobacz [dokumentację dotyczącą Azure Functions](../azure-functions/functions-overview.md). Aby zilustrować, jak Azure Functions może pomóc spełnić Twoje wymagania lub kompletne scenariusze w DevTest Labs, w tym artykule przedstawiono przykład tworzenia podsumowania najwyższego poziomu maszyn wirtualnych w laboratorium w następujący sposób:

**Przykładowe wymaganie/scenariusz**: użytkownicy mogą zobaczyć szczegóły dotyczące wszystkich maszyn wirtualnych w laboratorium, w tym systemu operacyjnego, właściciela i wszelkich zastosowanych artefaktów.  Ponadto jeśli artefakt **Zastosuj aktualizacje systemu Windows** nie został ostatnio zastosowany, istnieje prosty sposób na jego zastosowanie.

Aby ukończyć ten scenariusz, będziesz używać dwóch funkcji zgodnie z opisem na poniższym diagramie:  

![Przepływ ogólny](./media/extend-devtest-labs-azure-functions/flow.png)

Kod źródłowy tych przykładowych funkcji znajduje się w [repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (dostępne są implementacje języka C# i programu PowerShell).

- **UpdateInternalSupportPage**: Ta funkcja wysyła zapytanie do programu DevTest Labs i aktualizuje wewnętrzną stronę obsługi bezpośrednio ze szczegółowymi informacjami o maszynach wirtualnych.
- **ApplyWindowsUpdateArtifact**: dla maszyny wirtualnej w laboratorium, ta funkcja stosuje artefakt usługi **Windows Update** .

## <a name="how-it-works"></a>Jak to działa
Gdy użytkownicy wybierzą **wewnętrzną stronę pomocy technicznej** w DevTest Labs, mają wstępnie wypełnioną stronę z informacjami o maszynach wirtualnych, właścicielach laboratorium i kontaktach pomocy technicznej.  

Po wybraniu przycisku **Wybierz tutaj do odświeżenia** Strona wywoła pierwszą funkcję platformy Azure: **UpdateInternalSupportPage**. Funkcja wysyła zapytanie do DevTest Labs o informacje, a następnie ponownie zapisuje **wewnętrzną stronę techniczną** przy użyciu nowych informacji.

Istnieje dodatkowa akcja, która może zostać podjęta w przypadku maszyn wirtualnych, na których nie zastosowano Windows Update artefakty, będzie dostępny przycisk umożliwiający stosowanie aktualizacji systemu Windows do maszyny wirtualnej. Po wybraniu przycisku ***Uruchom usługę Windows Update** dla maszyny wirtualnej na stronie jest wywoływana Druga funkcja platformy Azure: **ApplyWindowsUpdateArtifact**. Ta funkcja sprawdza, czy maszyna wirtualna jest uruchomiona, a jeśli tak, stosuje artefakt [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) bezpośrednio.

## <a name="step-by-step-walkthrough"></a>Przewodnik krok po kroku
Ta sekcja zawiera instrukcje krok po kroku dotyczące konfigurowania zasobów platformy Azure wymaganych do zaktualizowania wewnętrznej strony **pomocy technicznej** . Ten Instruktaż zawiera jeden przykład rozszerzania laboratoriów DevTest. Tego wzorca można użyć dla innych scenariuszy.

### <a name="step-1-create-a-service-principal"></a>Krok 1. Tworzenie nazwy głównej usługi 
Pierwszym krokiem jest uzyskanie jednostki usługi z uprawnieniami do subskrypcji zawierającej laboratorium. Nazwa główna usługi musi korzystać z uwierzytelniania opartego na hasłach. Można to zrobić za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)lub [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md). Jeśli masz już jednostkę usługi do użycia, możesz pominąć ten krok.

Zanotuj **Identyfikator aplikacji**, **klucz** i **Identyfikator dzierżawy** dla jednostki usługi. Będą one potrzebne w dalszej części tego przewodnika. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Krok 2. Pobieranie przykładu i otwieranie go w programie Visual Studio 2019
Pobierz kopię [przykładowej Azure Functions C#](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) lokalnie (przez sklonowanie repozytorium lub pobranie repozytorium z tego [miejsca](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Otwórz przykładowe rozwiązanie przy użyciu programu Visual Studio 2019.  
1. Zainstaluj obciążenie **Programowanie na platformie Azure** dla programu Visual Studio, jeśli nie masz już zainstalowanego. Można go zainstalować za pośrednictwem **narzędzi**  ->  **Pobierz narzędzia i funkcje** menu.

    ![Obciążenie Programowanie na platformie Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Skompiluj rozwiązanie. Wybierz pozycję **kompilacja** , a następnie pozycję Kompiluj element menu **rozwiązania** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Krok 3. wdrożenie przykładu na platformie Azure
W programie Visual Studio, w oknie **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **AzureFunctions** , a następnie wybierz pozycję **Publikuj**. Postępuj zgodnie z instrukcjami kreatora, aby zakończyć publikowanie na nowym lub istniejącym aplikacja funkcji platformy Azure. Aby uzyskać szczegółowe informacje na temat tworzenia i wdrażania usługi Azure Functions przy użyciu programu Visual Studio, zobacz [programowanie Azure Functions przy użyciu programu Visual Studio](../azure-functions/functions-develop-vs.md).

![Okno dialogowe publikowania](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Krok 4. zbieranie ustawień aplikacji
Po opublikowaniu funkcji należy uzyskać adresy URL dla tych funkcji z Azure Portal. 

1. Przejdź do [Azure Portal](https://portal.azure.com). 
1. Znajdź aplikację funkcji.
1. Na stronie **aplikacje funkcji** wybierz funkcję. 
1. Wybierz pozycję **Pobierz adres URL funkcji** , jak pokazano na poniższej ilustracji. 

    ![Adresy URL usługi Azure Functions](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Skopiuj i Zapisz adres URL. Powtórz te kroki dla innej funkcji platformy Azure. 

Potrzebne są również dodatkowe informacje o jednostce usługi, takie jak identyfikator aplikacji, klucz i identyfikator dzierżawy.


### <a name="step-5--update-application-settings"></a>Krok 5. aktualizowanie ustawień aplikacji
W programie Visual Studio po opublikowaniu funkcji platformy Azure wybierz pozycję **edytuj Azure App Service ustawienia** w obszarze **Akcje**. Zaktualizuj następujące ustawienia aplikacji (zdalne):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (domyślnie 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Ustawienia aplikacji](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Krok 6. Testowanie funkcji platformy Azure
Ostatnim krokiem w tym instruktażu jest przetestowanie funkcji platformy Azure.  

1. Przejdź do funkcji **UpdateInternalSupportPage** w aplikacji funkcji utworzonej w kroku 3. 
1. Wybierz opcję **Testuj** po prawej stronie. 
1. Wprowadź we właściwościach trasy (LABNAME, RESOURCEGROUPNAME i Identyfikator subskrypcji).
1. Wybierz pozycję **Uruchom** , aby wykonać funkcję.  

    Ta funkcja zaktualizuje wewnętrzną stronę techniczną określonego laboratorium. Zawiera również przycisk umożliwiający użytkownikom bezpośrednie wywoływanie funkcji po raz kolejny

    ![Funkcja testowa](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Następne kroki
Azure Functions może pomóc w zwiększeniu funkcjonalności DevTest Labs poza to, co już jest wbudowane, i pomóc klientom w spełnieniu ich unikatowych wymagań dla swoich zespołów. Ten wzorzec można rozszerzyć & jeszcze bardziej rozwinięte, aby objąć jeszcze więcej.  Aby dowiedzieć się więcej na temat DevTest Labs, zobacz następujące artykuły: 

- [Architektura referencyjna DevTest Labs Enterprise](devtest-lab-reference-architecture.md)
- [Często zadawane pytania](devtest-lab-faq.md)
- [Skalowanie w górę DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatyzacja DevTest Labs przy użyciu programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








