---
title: Wdrażanie aplikacji sieci Web przy użyciu szablonu Azure Cosmos DB
description: Dowiedz się, jak wdrożyć konto Azure Cosmos DB, Azure App Service Web Apps i przykładową aplikację sieci Web przy użyciu szablonu Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128374"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Wdrażanie Azure Cosmos DB i Azure App Service Web Apps przy użyciu szablonu Azure Resource Manager
W tym samouczku pokazano, jak używać szablonu Azure Resource Manager do wdrażania i integrowania [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) aplikacji sieci Web oraz przykładowej aplikacji sieci Web.

Za pomocą szablonów Azure Resource Manager można łatwo zautomatyzować wdrażanie i konfigurację zasobów platformy Azure.  W tym samouczku pokazano, jak wdrożyć aplikację sieci Web i automatycznie skonfigurować informacje o połączeniu z kontem Azure Cosmos DB.

Po ukończeniu tego samouczka będziesz mieć możliwość udzielenia odpowiedzi na następujące pytania:  

* Jak można użyć szablonu Azure Resource Manager, aby wdrożyć i zintegrować konto Azure Cosmos DB i aplikację sieci Web w Azure App Service?
* Jak użyć szablonu Azure Resource Manager, aby wdrożyć i zintegrować konto Azure Cosmos DB, aplikację sieci Web w App Service Web Apps i aplikację webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne
> [!TIP]
> Chociaż w tym samouczku nie założono wcześniejszego doświadczenia z szablonami Azure Resource Manager lub JSON, należy zmodyfikować przywoływane szablony lub opcje wdrażania, a następnie trzeba znać każdy z tych obszarów.
> 
> 

Przed wykonaniem instrukcji przedstawionych w tym samouczku upewnij się, że masz subskrypcję platformy Azure. Azure to platforma oparta na subskrypcjach.  Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [Opcje zakupu](https://azure.microsoft.com/pricing/purchase-options/), [oferty członków](https://azure.microsoft.com/pricing/member-offers/)lub [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Krok 1. Pobieranie plików szablonów
Zacznijmy od pobrania plików szablonów wymaganych przez ten samouczek.

1. Pobierz szablon [utwórz Azure Cosmos DB konto, Web Apps i Wdróż przykładową aplikację demonstracyjną](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) do folderu lokalnego (na przykład C:\Azure Cosmos dbtemplates). Ten szablon służy do wdrażania konta Azure Cosmos DB, aplikacji internetowej App Service i aplikacji sieci Web.  Automatycznie konfiguruje również aplikację sieci Web w celu nawiązania połączenia z kontem Azure Cosmos DB.
2. Pobierz [Azure Cosmos DB Utwórz konto i Web Apps przykładowy](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) szablon do folderu lokalnego (na przykład C:\Azure Cosmos dbtemplates). Ten szablon służy do wdrażania konta Azure Cosmos DB, aplikacji internetowej App Service i modyfikacji ustawień aplikacji witryny w celu łatwego poAzure Cosmos DB informacji o połączeniu, ale nie obejmuje aplikacji sieci Web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2. wdrażanie konta Azure Cosmos DB, aplikacji sieci Web App Service i przykładowej aplikacji demonstracyjnej
Teraz wdróżmy swój pierwszy szablon.

> [!TIP]
> Szablon nie sprawdza, czy nazwa aplikacji sieci Web i nazwa konta Azure Cosmos DB wprowadzone w następującym szablonie są prawidłowe i b).  Zdecydowanie zaleca się sprawdzenie dostępności nazw planowanych do dostarczenia przed przesłaniem wdrożenia.
> 
> 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com), kliknij przycisk New (nowy) i wyszukaj ciąg "Template Deployment".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment1.png)
2. Wybierz element Template Deployment i kliknij pozycję **Utwórz** ![zrzut ekranu dla interfejsu użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij pozycję **Edytuj szablon**, wklej zawartość pliku szablonu DocDBWebsiteTodo. JSON, a następnie kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment3.png)
4. Kliknij przycisk **Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych, a następnie kliknij przycisk **OK**.  Używane są następujące parametry:
   
   1. SITEname: Określa nazwę aplikacji sieci Web App Service i służy do konstruowania adresu URL, który jest używany w celu uzyskania dostępu do aplikacji sieci Web (na przykład jeśli określisz wartość "mydemodocdbwebapp", adres URL, do którego uzyskujesz `mydemodocdbwebapp.azurewebsites.net`dostęp do aplikacji sieci Web).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu App Service, który ma zostać utworzony.
   3. Lokalizacja: określa lokalizację platformy Azure, w której mają zostać utworzone zasoby Azure Cosmos DB i aplikacji sieci Web.
   4. DATABASEACCOUNTNAME: Określa nazwę konta Azure Cosmos DB, które ma zostać utworzone.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby utworzyć nową grupę zasobów, a następnie wybierz lokalizację grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment5.png)
6. Kliknij pozycję **Przejrzyj postanowienia prawne**, **Kup**, a następnie kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie.  Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby wyniki wdrożenia były łatwo widoczne na stronie głównej Azure Portal.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrożenia zostanie otwarte okienko Grupa zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Aby użyć aplikacji, przejdź do adresu URL aplikacji sieci Web (w powyższym przykładzie adres URL zostałby `http://mydemodocdbwebapp.azurewebsites.net`).  Zostanie wyświetlona następująca aplikacja sieci Web:
   
   ![Przykładowa aplikacja do zrobienia](./media/create-website/image2.png)
9. Przejdź dalej i Utwórz kilka zadań w aplikacji sieci Web, a następnie wróć do okienka grupy zasobów w Azure Portal. Kliknij zasób konta Azure Cosmos DB na liście zasoby, a następnie kliknij pozycję **Eksplorator danych**.
10. Uruchom domyślne zapytanie, "SELECT * FROM c" i sprawdź wyniki.  Zwróć uwagę, że zapytanie pobrało reprezentację w formacie JSON elementów do wykonania utworzonych w kroku 7 powyżej.  Śmiało, aby eksperymentować z zapytaniami; na przykład spróbuj uruchomić polecenie SELECT * FROM c, gdzie c. IsComplete = true, aby zwrócić wszystkie elementy do wykonania, które zostały oznaczone jako ukończone.
11. Możesz skorzystać z bezpłatnej witryny, aby poznać środowisko portalu Azure Cosmos DB lub zmodyfikować przykładową aplikację do zrobienia.  Gdy wszystko będzie gotowe, wdróżmy kolejny szablon.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3. wdrożenie przykładu konta dokumentu i aplikacji internetowej
Teraz wdróżmy drugi szablon.  Ten szablon jest przydatny do pokazania, jak można wstrzyknąć Azure Cosmos DB informacje o połączeniu, takie jak punkt końcowy konta i klucz główny do aplikacji sieci Web jako ustawienia aplikacji lub niestandardowe parametry połączenia. Na przykład być może masz własną aplikację sieci Web, którą chcesz wdrożyć przy użyciu konta Azure Cosmos DB i że informacje o połączeniu są automatycznie wypełniane podczas wdrażania.

> [!TIP]
> Szablon nie sprawdza, czy nazwa aplikacji sieci Web i nazwa konta Azure Cosmos DB podane poniżej są prawidłowe i b).  Zdecydowanie zaleca się sprawdzenie dostępności nazw planowanych do dostarczenia przed przesłaniem wdrożenia.
> 
> 

1. W [witrynie Azure Portal](https://portal.azure.com)kliknij pozycję Nowy i wyszukaj ciąg "Template Deployment".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment1.png)
2. Wybierz element Template Deployment i kliknij pozycję **Utwórz** ![zrzut ekranu dla interfejsu użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij pozycję **Edytuj szablon**, wklej zawartość pliku szablonu DocDBWebSite. JSON, a następnie kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment3.png)
4. Kliknij przycisk **Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych, a następnie kliknij przycisk **OK**.  Używane są następujące parametry:
   
   1. SITEname: Określa nazwę aplikacji sieci Web App Service i służy do konstruowania adresu URL, który będzie używany w celu uzyskania dostępu do aplikacji sieci Web (na przykład jeśli określisz wartość "mydemodocdbwebapp", adres URL, do którego uzyskujesz dostęp do aplikacji sieci Web, to mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu App Service, który ma zostać utworzony.
   3. Lokalizacja: określa lokalizację platformy Azure, w której mają zostać utworzone zasoby Azure Cosmos DB i aplikacji sieci Web.
   4. DATABASEACCOUNTNAME: Określa nazwę konta Azure Cosmos DB, które ma zostać utworzone.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby utworzyć nową grupę zasobów, a następnie wybierz lokalizację grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment5.png)
6. Kliknij pozycję **Przejrzyj postanowienia prawne**, **Kup**, a następnie kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie.  Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby wyniki wdrożenia były łatwo widoczne na stronie głównej Azure Portal.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonów](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrożenia zostanie otwarte okienko Grupa zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Kliknij zasób aplikacji sieci Web na liście zasoby, a następnie kliknij pozycję **Ustawienia** ![aplikacji zrzut ekranu przedstawiający grupę zasobów.](./media/create-website/TemplateDeployment9.png)  
9. Zwróć uwagę na to, jak dostępne są ustawienia aplikacji dla punktu końcowego Azure Cosmos DB i każdego Azure Cosmos DB kluczy głównych.

    ![Zrzut ekranu ustawień aplikacji](./media/create-website/TemplateDeployment10.png)  
10. Możesz kontynuować Eksplorowanie witryny Azure Portal lub wykonać jedną z naszych [przykładów](https://go.microsoft.com/fwlink/?LinkID=402386) Azure Cosmos DB, aby utworzyć własną aplikację Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Następne kroki
Gratulacje! Wdrożono Azure Cosmos DB, App Service aplikację sieci Web i przykładową aplikację internetową przy użyciu szablonów Azure Resource Manager.

* Aby dowiedzieć się więcej na temat Azure Cosmos DB, kliknij [tutaj](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej na temat Azure App Service Web Apps, kliknij [tutaj](https://go.microsoft.com/fwlink/?LinkId=325362).
* Aby dowiedzieć się więcej na temat szablonów Azure Resource Manager, kliknij [tutaj](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co zostało zmienione
* Aby uzyskać Przewodnik dotyczący zmiany z witryn sieci Web do App Service Zobacz: [Azure App Service i jej wpływ na istniejące usługi platformy Azure](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://go.microsoft.com/fwlink/?LinkId=523751) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

