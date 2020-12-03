---
title: 'Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal (wersja zapoznawcza)'
description: W tym przewodniku szybki start opisano, jak utworzyć konto usługi Azure kontrolą i skonfigurować uprawnienia, aby rozpocząć korzystanie z niego.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 00b504c7bcf51a69d03fb1294de4f52ef35ed163
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555973"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym przewodniku szybki start utworzysz konto usługi Azure kontrolą.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Twoja [dzierżawa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

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

1. Na karcie **podstawowe** wykonaj następujące czynności:
    1. Wybierz **grupę zasobów**.
    1. Wprowadź **nazwę konta usługi kontrolą** dla katalogu. Spacje i symbole są niedozwolone.
    1. Wybierz  **lokalizację**, a następnie wybierz kolejno pozycje **Dalej: Konfiguracja**.
1. Na karcie **Konfiguracja** wybierz żądany **rozmiar platformy** — dozwolone wartości to 4 jednostki pojemności (CU) i 16 cu. Wybierz pozycję **Dalej: Tagi**.
1. Na karcie **Tagi** możesz opcjonalnie dodać jeden lub więcej tagów. Tagi te są używane tylko w Azure Portal, a nie na platformie Azure kontrolą.
1. Wybierz pozycję **przegląd & Utwórz**, a następnie wybierz pozycję **Utwórz**. Ukończenie tworzenia może potrwać kilka minut. Nowo utworzone wystąpienie konta usługi Azure kontrolą pojawia się na liście na stronie **konta kontrolą** .
1. Po zakończeniu aprowizacji nowego konta wybierz pozycję **Przejdź do zasobu**.

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

1. W przypadku typu roli w roli **kontrolą Data Curator** lub **administratora źródła danych kontrolą** w zależności od tego, co jednostka usługi ma być używana (szczegółowe informacje znajdują się w temacie [uprawnienia katalogu](catalog-permissions.md) ).

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
