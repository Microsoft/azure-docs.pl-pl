---
title: 'Szybki start: tworzenie konta usługi Azure Purview w Azure Portal (wersja zapoznawcza)'
description: W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Purview i konfigurowania uprawnień w celu rozpoczęcia korzystania z niego.
author: nayenama
ms.author: nayenama
ms.date: 10/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 158eed6d287fa384023defbb20a7a1c39ea3d838
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728594"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Szybki start: tworzenie konta usługi Azure Purview w Azure Portal

> [!IMPORTANT]
> Usługa Azure Purview jest obecnie dostępna w wersji zapoznawczej. Dodatkowe warunki użytkowania wersji [zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) programu Microsoft Azure obejmują dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.

W tym przewodniku Szybki start utworzysz konto usługi Azure Purview.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Twoje konto musi mieć uprawnienia do tworzenia zasobów w subskrypcji

* Jeśli nie **Azure Policy** tworzenia konta usługi **Storage** i przestrzeni nazw **usługi EventHub,** należy wprowadzić wyjątek zasad przy użyciu tagu , który można wprowadzić podczas tworzenia konta usługi Purview. Główną przyczyną jest to, że dla każdego utworzonego konta programu Purview musi ona utworzyć zarządzaną grupę zasobów w ramach tej grupy zasobów, konta magazynu i przestrzeni nazw usługi EventHub.

    > [!important]
    > Nie musisz wykonać tego kroku, jeśli nie masz konta usługi Azure Policy lub istniejący Azure Policy nie  blokuje tworzenia konta magazynu i przestrzeni nazw **usługi EventHub.**

    1. Przejdź do Azure Portal i wyszukaj **zasady**
    1. Postępuj [zgodnie z zasadami Utwórz niestandardową definicję zasad](../governance/policy/tutorials/create-custom-policy-definition.md) lub zmodyfikuj istniejące zasady, aby dodać dwa wyjątki za pomocą operatora i `not` `resourceBypass` tagu:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > Tag może być innym tagiem i to ty musisz zdefiniować wartość podczas tworzenia aplikacji Purview w ostatnich krokach, o ile zasady `resourceBypass` mogą wykryć tag.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia definicji zasad.":::

    1. [Utwórz przypisanie zasad przy](../governance/policy/assign-policy-portal.md) użyciu utworzonych zasad niestandardowych.

        [![Zrzut ekranu przedstawiający sposób tworzenia przypisania zasad](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

W razie potrzeby wykonaj następujące kroki, aby skonfigurować subskrypcję, aby umożliwić uruchamianie usługi Azure Purview w ramach subskrypcji:

   1. W skrypcie Azure Portal i wybierz **pozycję Subskrypcje.**

   1. Z listy subskrypcji wybierz subskrypcję, której chcesz użyć. Wymagane jest uprawnienie dostępu administracyjnego dla subskrypcji.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania subskrypcji w Azure Portal.":::

   1. W przypadku subskrypcji wybierz pozycję **Dostawcy zasobów.** W **okienku Dostawcy** zasobów wyszukaj i zarejestruj wszystkich trzech dostawców zasobów: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Jeśli nie są one zarejestrowane, zarejestruj je, wybierając pozycję **Zarejestruj**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania dostawcy zasobów Microsoft dot Azure Purview w Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Tworzenie wystąpienia konta usługi Azure Purview

1. Przejdź do **strony Konta programu Purview** w Azure Portal, a następnie wybierz pozycję **Dodaj,** aby utworzyć nowe konto usługi Azure Purview. Alternatywnie możesz przejść do witryny Marketplace i wyszukać pozycję **Konta purview i** wybrać pozycję **Utwórz.** Pamiętaj, że jednocześnie możesz dodać tylko jedno konto usługi Azure Purview.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia konta usługi Azure Purview w Azure Portal.":::

    > [!Note] 
    > Usługa Azure Purview nie obsługuje przenoszenia swojego konta między regionami. Aby uzyskać więcej informacji na ten temat, zobacz [Obsługa operacji przenoszenia dla zasobów](../azure-resource-manager/management/move-support-resources.md).

1. Na **karcie Podstawowe** wykonaj następujące czynności:
    1. Wybierz **grupę zasobów**.
    1. Wprowadź nazwę **konta programu Purview** dla katalogu. Spacje i symbole są niedozwolone.
    1. Wybierz **lokalizację**, a następnie wybierz pozycję **Dalej: Konfiguracja.**
1. Na karcie **Konfiguracja** wybierz żądany rozmiar **platformy** — dozwolone wartości to 4 jednostki wydajności (CU) i 16 CU. Wybierz **pozycję Dalej: Tagi**.
1. Na karcie **Tagi** możesz opcjonalnie dodać jeden lub więcej tagów. Te tagi są używane tylko w usłudze Azure Portal, a nie w usłudze Azure Purview. 

    > [!Note] 
    > Jeśli masz już **Azure Policy** i musisz dodać wyjątek, tak jak w wymaganiach **wstępnych,** musisz dodać poprawny tag. Możesz na przykład dodać `resourceBypass` tag: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Dodaj tag do konta purview.":::

1. Wybierz **pozycję Przejrzyj & Utwórz,** a następnie wybierz pozycję **Utwórz.** Tworzenie zajmuje kilka minut. Nowo utworzone wystąpienie konta usługi Azure Purview zostanie wyświetlone na liście na **stronie kont usługi Purview.**
1. Po zakończeniu aprowowania nowego konta wybierz pozycję **Przejdź do zasobu.**

    > [!Note]
    > Jeśli aprowizowanie nie powiodło się ze stanem, oznacza to, że istnieją zasady platformy Azure blokujące aplikacji Purview tworzenie konta magazynu i przestrzeni `Conflict` **nazw usługi EventHub.**  Aby dodać wyjątki, należy wykonać **kroki opisane** w tece Wymagania wstępne.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Komunikat o błędzie konfliktów programu Purview":::

1. Wybierz **pozycję Uruchom konto programu Purview.**

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Zrzut ekranu przedstawiający wybór w celu uruchomienia katalogu kont usługi Azure Purview.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Dodawanie podmiotu zabezpieczeń do roli płaszczyzny danych

Zanim Ty lub Twój zespół będziecie w stanie rozpocząć korzystanie z usługi Azure Purview, należy dodać co najmniej jeden podmiot zabezpieczeń do jednej ze wstępnie zdefiniowanych ról płaszczyzny danych: **Purview Data Reader**, **Purview Data Curator** lub **Purview Data Source Administrator**. Aby uzyskać więcej informacji na temat uprawnień usługi Azure Purview Data Catalog, zobacz [Uprawnienia katalogu](catalog-permissions.md).

Aby dodać podmiot zabezpieczeń do roli płaszczyzny danych **programu Purview Data Curator** na koncie usługi Azure Purview:

1. Przejdź do [**strony Konta programu Purview**](https://aka.ms/purviewportal) w Azure Portal.

1. Wybierz konto usługi Azure Purview, które chcesz zmodyfikować.

1. Na stronie **Konto programu Purview** wybierz kartę **Kontrola dostępu (IAM)**

1. Kliknij **pozycję + Dodaj**

Jeśli po kliknięciu przycisku Dodaj są wyświetlane dwie opcje, które są oznaczone (wyłączone), oznacza to, że nie masz odpowiednich uprawnień do dodawania kogokolwiek do roli płaszczyzny danych na koncie usługi Azure Purview. Na twoim koncie usługi Azure Purview musisz znaleźć właściciela, administratora dostępu użytkowników lub inną osobę z uprawnieniami do przypisywania ról. Możesz poszukać odpowiednich osób,  wybierając kartę Przypisania ról, a następnie przewijając w dół, aby znaleźć administratora dostępu właściciela lub użytkownika i skontaktować się z tymi osobami.

1. Wybierz pozycję **Dodaj przypisanie roli**.

1. W przypadku roli Typ roli w roli **curatora** danych programu Purview lub Roli administratora źródła [](catalog-permissions.md) danych **Purview** w zależności od tego, do czego będzie używany podmiot zabezpieczeń (aby uzyskać szczegółowe informacje, zobacz Uprawnienia katalogu i obiekty aplikacji i jednostki usługi w usłudze [Azure Active Directory).](../active-directory/develop/app-objects-and-service-principals.md)

1. W **przypadku ustawienia Przypisz dostęp** pozostaw wartość domyślną **Użytkownik, grupa lub nazwa główna usługi**.

1. W **menu** Wybierz wprowadź nazwę użytkownika, Azure Active Directory grupę lub jednostkę usługi, którą chcesz przypisać, a następnie kliknij jego nazwę w okienku wyników.

1. Kliknij pozycję **Zapisz.**

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli to konto usługi Azure Purview nie jest już potrzebne, usuń je, aby wykonać następujące czynności:

1. Przejdź do **strony Konta programu Purview** w Azure Portal.

2. Wybierz konto usługi Azure Purview utworzone na początku tego przewodnika Szybki start. Wybierz **pozycję** Usuń, wprowadź nazwę konta, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Purview.

W następnym artykule dowiesz się, jak zezwolić użytkownikom na dostęp do konta usługi Azure Purview. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure Purview](catalog-permissions.md)
