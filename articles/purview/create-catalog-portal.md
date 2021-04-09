---
title: 'Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal (wersja zapoznawcza)'
description: W tym przewodniku szybki start opisano, jak utworzyć konto usługi Azure kontrolą i skonfigurować uprawnienia, aby rozpocząć korzystanie z niego.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 0346b467bc299b4eb6125df04a4449e94c035e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666471"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym przewodniku szybki start utworzysz konto usługi Azure kontrolą.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Twoje konto musi mieć uprawnienia do tworzenia zasobów w ramach subskrypcji

* Jeśli masz **Azure Policy** blokujące wszystkie aplikacje z tworzenia **konta magazynu** i **przestrzeni nazw EventHub**, musisz utworzyć wyjątek zasad przy użyciu tagu, który można wprowadzić podczas procesu tworzenia konta kontrolą. Głównym powodem jest to, że dla każdego utworzonego konta kontrolą należy utworzyć zarządzaną grupę zasobów i w ramach tej grupy zasobów, konto magazynu i przestrzeń nazw EventHub.

    > [!important]
    > Nie trzeba wykonywać tego kroku, jeśli nie masz Azure Policy lub istniejący Azure Policy nie blokuje tworzenia **konta magazynu** i **przestrzeni nazw EventHub**.

    1. Przejdź do Azure Portal i Wyszukaj **zasady**
    1. Wykonaj instrukcje [tworzenia niestandardowej definicji zasad](../governance/policy/tutorials/create-custom-policy-definition.md) lub zmodyfikuj istniejące zasady, aby dodać dwa wyjątki z `not` operatorem i `resourceBypass` tagiem:

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
        > Tag może znajdować się w dowolnym `resourceBypass` momencie i może definiować wartość podczas tworzenia kontrolą w innych krokach, o ile zasady mogą wykryć tag.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia definicji zasad.":::

    1. [Utwórz przypisanie zasad](../governance/policy/assign-policy-portal.md) przy użyciu utworzonych zasad niestandardowych.

        [![Zrzut ekranu przedstawiający sposób tworzenia przypisania zasad](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

W razie potrzeby wykonaj następujące kroki, aby skonfigurować swoją subskrypcję, aby umożliwić uruchamianie usługi Azure kontrolą w ramach subskrypcji:

   1. W Azure Portal Wyszukaj i wybierz pozycję **subskrypcje**.

   1. Z listy subskrypcji wybierz subskrypcję, której chcesz użyć. Wymagane jest uprawnienie dostępu administracyjnego dla subskrypcji.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania subskrypcji w Azure Portal.":::

   1. W przypadku subskrypcji wybierz pozycję **dostawcy zasobów**. W okienku **dostawcy zasobów** Wyszukaj i zarejestruj wszystkich trzech dostawców zasobów: 
       1. **Microsoft. kontrolą**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Jeśli nie są zarejestrowane, zarejestruj je, wybierając pozycję **zarejestruj**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania dostawcy zasobów usługi Azure kontrolą firmy Microsoft w Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Tworzenie wystąpienia konta usługi Azure kontrolą

1. Przejdź do strony **konta kontrolą** w Azure Portal a następnie wybierz pozycję **Dodaj** , aby utworzyć nowe konto usługi Azure kontrolą. Alternatywnie możesz przejść do witryny Marketplace Search dla **kont kontrolą** i wybrać pozycję **Utwórz**. Należy pamiętać, że w danym momencie można dodać tylko jedno konto usługi Azure kontrolą.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia konta usługi Azure kontrolą w Azure Portal.":::

    > [!Note] 
    > Usługa Azure kontrolą nie obsługuje przeniesienia swojego konta między regionami. Więcej informacji na ten temat można znaleźć na [stronie usługi obsługiwane przez platformę Azure](../azure-resource-manager/management/region-move-support.md).

1. Na karcie **podstawowe** wykonaj następujące czynności:
    1. Wybierz **grupę zasobów**.
    1. Wprowadź **nazwę konta usługi kontrolą** dla katalogu. Spacje i symbole są niedozwolone.
    1. Wybierz  **lokalizację**, a następnie wybierz kolejno pozycje **Dalej: Konfiguracja**.
1. Na karcie **Konfiguracja** wybierz żądany **rozmiar platformy** — dozwolone wartości to 4 jednostki pojemności (CU) i 16 cu. Wybierz pozycję **Dalej: Tagi**.
1. Na karcie **Tagi** możesz opcjonalnie dodać jeden lub więcej tagów. Tagi te są używane tylko w Azure Portal, a nie na platformie Azure kontrolą. 

    > [!Note] 
    > Jeśli masz **Azure Policy** i musisz dodać wyjątek jako **warunek wstępny**, musisz dodać poprawny tag. Na przykład możesz dodać `resourceBypass` tag: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Dodaj tag do konta kontrolą.":::

1. Wybierz pozycję **przegląd & Utwórz**, a następnie wybierz pozycję **Utwórz**. Ukończenie tworzenia może potrwać kilka minut. Nowo utworzone wystąpienie konta usługi Azure kontrolą pojawia się na liście na stronie **konta kontrolą** .
1. Po zakończeniu aprowizacji nowego konta wybierz pozycję **Przejdź do zasobu**.

    > [!Note]
    > Jeśli Inicjowanie obsługi nie powiodło się ze `Conflict` stanem, oznacza to, że istnieje możliwość blokowania zasad platformy Azure kontrolą, aby utworzyć **konto magazynu** i **przestrzeń nazw EventHub**. Aby dodać wyjątki, należy zapoznać się z instrukcjami dotyczącymi **wymagań wstępnych** .
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Komunikat o błędzie konfliktu kontrolą":::

1. Wybierz pozycję **Uruchom konto kontrolą**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Zrzut ekranu przedstawiający wybór w celu uruchomienia wykazu kont usługi Azure kontrolą.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Dodawanie podmiotu zabezpieczeń do roli płaszczyzny danych

Zanim Ty lub Twój zespół będzie mógł zacząć korzystać z usługi Azure kontrolą, należy dodać co najmniej jeden podmiot zabezpieczeń do jednej ze wstępnie zdefiniowanych ról płaszczyzny danych: **kontrolą Data Reader**, **kontrolą Data Curator** lub **kontrolą źródła danych**. Aby uzyskać więcej informacji na temat uprawnień do usługi Azure kontrolą Data Catalog, zobacz [uprawnienia katalogu](catalog-permissions.md).

Aby dodać podmiot zabezpieczeń do roli płaszczyzny danych **kontrolą Data Curator** na koncie usługi Azure kontrolą:

1. Przejdź do strony [**konta kontrolą**](https://aka.ms/purviewportal) w Azure Portal.

1. Wybierz konto usługi Azure kontrolą, które chcesz zmodyfikować.

1. Na stronie **konto kontrolą** wybierz pozycję **Kontrola dostępu do karty (IAM).**

1. Kliknij pozycję **+ Dodaj**

Jeśli po kliknięciu pozycji Dodaj zobaczysz dwie opcje pokazujące (wyłączone), oznacza to, że nie masz odpowiednich uprawnień, aby dodać każdego do roli płaszczyzny danych na koncie usługi Azure kontrolą. Na koncie usługi Azure kontrolą należy znaleźć właściciela, administratora dostępu użytkowników lub kogoś innego. Aby wyszukać odpowiednie osoby, wybierz pozycję **przypisanie roli** kartę, a następnie przewiń w dół, aby wyszukać administratora dostępu właściciela lub użytkownika i skontaktować się z tymi osobami.

1. Wybierz pozycję **Dodaj przypisanie roli**.

1. W przypadku typu roli w roli **kontrolą Data Curator** lub **administratora źródła danych kontrolą** w zależności od tego, co podmiot zabezpieczeń ma być używany (Aby uzyskać szczegółowe informacje, zobacz [uprawnienia katalogu](catalog-permissions.md) i [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) ).

1. W przypadku **uprawnienia do przypisywania** pozostaw wartość domyślną, **użytkownika, grupy lub nazwy głównej usługi**.

1. W polu **Wybierz** wprowadź nazwę użytkownika, grupę Azure Active Directory lub jednostkę usługi, którą chcesz przypisać, a następnie kliknij jej nazwę w okienku wyników.

1. Kliknij pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli to konto usługi Azure kontrolą nie jest już potrzebne, usuń je, wykonując następujące czynności:

1. Przejdź do strony **konta kontrolą** w Azure Portal.

2. Wybierz konto usługi Azure kontrolą utworzone na początku tego przewodnika Szybki Start. Wybierz pozycję **Usuń**, wprowadź nazwę konta, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure kontrolą.

Przejdź do następnego artykułu, aby dowiedzieć się, jak umożliwić użytkownikom dostęp do konta usługi Azure kontrolą. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure kontrolą](catalog-permissions.md)