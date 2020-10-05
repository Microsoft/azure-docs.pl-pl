---
title: Szybki Start — Tworzenie profilu Azure CDN i punktu końcowego
description: W tym przewodniku Szybki start przedstawiono sposób włączania usługi Azure CDN przez utworzenie nowego profilu usługi CDN i punktu końcowego usługi CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: af90166b688dee104e7bda18a88a2fe7c98f657b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "82996235"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN

W tym przewodniku szybki start włączysz usługę Azure Content Delivery Network (CDN) przez utworzenie nowego profilu CDN, który jest kolekcją jednego lub większej liczby punktów końcowych usługi CDN. Po utworzeniu profilu i punktu końcowego można rozpocząć dostarczanie zawartości do klientów.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto magazynu platformy Azure o nazwie *cdnstorageacct123*, które jest używane dla nazwy hosta pochodzenia. Aby wykonać to wymaganie, zobacz [integrowanie konta usługi Azure Storage z usługą Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Tworzenie nowego punktu końcowego usługi CDN

Utworzony profil usługi CDN jest używany do tworzenia punktu końcowego.

1. W witrynie Azure Portal na pulpicie nawigacyjnym wybierz utworzony profil usługi CDN. Jeśli nie możesz go znaleźć, możesz otworzyć grupę zasobów, w której została utworzona, lub użyć paska wyszukiwania w górnej części portalu, wprowadzić nazwę profilu i wybrać profil z wyników.
   
1. Na stronie profil usługi CDN wybierz pozycję **+ punkt końcowy**.
   
    ![Profil CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Zostanie wyświetlone okienko **Dodawanie punktu końcowego**.

3. Wprowadź następujące wartości ustawień:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Nazwa** | Wprowadź nazwę sieci *CDN-Endpoint-123* dla nazwy hosta punktu końcowego. Ta nazwa musi być globalnie unikatowa na platformie Azure; Jeśli jest już używana, wprowadź inną nazwę. Ta nazwa jest używana do uzyskiwania dostępu do buforowanych zasobów w domenie _ &lt; Endpoint name &gt; _. azureedge.NET.|
    | **Typ źródła** | Wybierz pozycję **Magazyn**. | 
    | **Nazwa hosta źródła** | Wybierz nazwę hosta konta usługi Azure Storage, którego używasz, z listy rozwijanej, na przykład *cdnstorageacct123.blob.Core.Windows.NET*. |
    | **Ścieżka do źródła** | Pozostaw to pole puste. |
    | **Nagłówek hosta źródła** | Pozostaw wartość domyślną (czyli nazwę hosta dla konta magazynu). |  
    | **Protokół** | Pozostaw domyślnie wybrane opcje **HTTP** i **HTTPS**. |
    | **Port źródła** | Pozostaw domyślne wartości portów. | 
    | **Zoptymalizowano pod kątem** | Pozostaw domyślnie wybraną pozycję **Ogólne dostarczanie w Internecie**. |

    ![Okienko Dodawanie punktu końcowego](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Wybierz pozycję **Dodaj**, aby utworzyć nowy punkt końcowy. Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych dla profilu.
    
   ![Punkt końcowy usługi CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Czas przeznaczony na propagację punktu końcowego zależy od warstwy cenowej wybranej podczas tworzenia profilu. **Standardowa Akamai** jest zwykle zakończona w ciągu jednej minuty, **standardowej firmie Microsoft** w 10 minutach oraz **standardowej Verizon** i **Premium Verizon** w maksymalnie 90 minutach.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono profil i punkt końcowy usługi CDN w grupie zasobów. Zapisz te zasoby, jeśli chcesz przejść do sekcji [Następne kroki](#next-steps) i dowiedzieć się, jak dodać domenę niestandardową do punktu końcowego. Jeśli jednak nie będziesz już potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów, unikając w ten sposób dodatkowych opłat:

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **grupy zasobów** , a następnie wybierz pozycję **CDNQuickstart-RG**.

2. Na stronie **Grupa zasobów** wybierz pozycję **Usuń grupę zasobów**, w polu tekstowym wpisz *CDNQuickstart-RG* , a następnie wybierz pozycję **Usuń**. Ta akcja spowoduje usunięcie grupy zasobów, profilu i punktu końcowego, który został utworzony w tym przewodniku Szybki Start.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Używanie sieci CDN do statycznej zawartości serwera z poziomu aplikacji internetowej](cdn-add-to-web-app.md)

> [!div class="nextstepaction"]
> [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)
