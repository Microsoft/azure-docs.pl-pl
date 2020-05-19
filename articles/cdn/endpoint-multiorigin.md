---
title: Azure CDN wiele lokalizacji punktu końcowego
description: Rozpocznij pracę z wieloma źródłami punktu końcowego Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 5/8/2020
ms.author: allensu
ms.openlocfilehash: 628a284335b7285a2ee989511b130fb7d4d3cde6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597877"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN wiele lokalizacji punktu końcowego

Dzięki obsłudze wielu źródeł można ustalić globalną nadmiarowość i wyeliminować przestój przez wybranie wielu źródeł w ramach punktu końcowego Azure CDN. Nadmiarowość zapewniana przez wiele lokalizacji ryzyka przez badanie kondycji każdego źródła i przechodzenie w tryb failover w razie potrzeby. Aby skonfigurować wiele lokalizacji, skonfiguruj co najmniej jedną grupę początkową. Każda grupa pochodzenia jest kolekcją co najmniej jednego źródła, które może przyjmować podobne obciążenia.

> [!NOTE]
> Obecnie ta funkcja jest dostępna tylko w Azure CDN firmy Microsoft. 

## <a name="create-the-origin-group"></a>Tworzenie grupy pochodzenia

1. Zaloguj się do [Azure Portal](https://portal.azure.com)

2. Wybierz profil Azure CDN a następnie wybierz punkt końcowy, który ma zostać skonfigurowany dla wieloskładnikowego.

3. Wybierz pozycję **Źródło** w obszarze **Ustawienia** w konfiguracji punktu końcowego:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Azure CDN wiele źródeł" border="true":::

4. Aby można było włączyć wiele źródeł, potrzebna jest co najmniej jedna grupa pochodzenia. Wybierz pozycję **Utwórz grupę pierwotną**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Azure CDN wiele źródeł" border="true":::

5. W konfiguracji **grupy dodawania pochodzenia** wprowadź lub wybierz następujące informacje:

   | Ustawienie           | Wartość                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nazwa grupy pochodzenia | Wprowadź nazwę grupy pierwotnej.                                   |
   | Ścieżka sondy        | Ścieżka w pochodzeniu, która jest używana do określania kondycji. |
   | Interwał sondy    | Wybierz interwał sondowania równy 1, 2 lub 4 minuty.                        |
   | Protokół sondy    | Wybierz pozycję **http** lub **https**.                                         |
   | Metoda sondowania      | Wybierz pozycję **główna** lub **Pobierz**.                                           |
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Azure CDN wiele źródeł" border="true":::

6. Wybierz pozycję **Dodaj**.

7. Aby wybrać domyślną grupę pierwotną, wybierz pozycję **Konfiguruj grupę pierwotną**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-4.png" alt-text="Azure CDN wiele źródeł" border="true":::

8. W polu rozwijanym wybierz grupę pierwotną i wybierz pozycję **OK**.

## <a name="add-multiple-origins"></a>Dodaj wiele źródeł

1. W ustawieniach pochodzenia dla punktu końcowego wybierz pozycję **+ Utwórz źródło**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Azure CDN wiele źródeł" border="true":::

2. Wprowadź lub wybierz następujące informacje z konfiguracji **dodawania źródła** :

   | Ustawienie           | Wartość                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Typ źródła | Wybierz pozycję **Magazyn**, **Usługa w chmurze**, **aplikacja internetowa**lub **Źródło niestandardowe**.                                   |
   | Nazwa hosta źródła        | Wybierz lub wprowadź nazwę hosta źródła.  Na liście rozwijanej są wyświetlane wszystkie dostępne źródła typu określonego w poprzednim ustawieniu. W przypadku wybrania jako typu źródła **niestandardowego źródła** danych wprowadź domenę serwera pochodzenia klienta. |
   | Nagłówek hosta źródła    | Wprowadź nagłówek hosta, który ma Azure CDN wysyłać z każdym żądaniem, lub pozostaw wartość domyślną.                        |
   | Port HTTP   | Wprowadź port HTTP.                                         |
   | Port HTTPS     | Wprowadź port HTTPS.                                           |
   | Priorytet    | Wprowadź liczbę z zakresu od 1 do 5.       |
   | Waga      | Wprowadź liczbę z zakresu od 1 do 1000.   |

    > [!NOTE]
    > Gdy źródło jest tworzone w ramach grupy pierwotnej, musi mieć według priorytetu i wagi. Jeśli grupa pochodzenia ma tylko jeden element źródłowy, wówczas domyślny priorytet i waga są ustawiane jako 1. Ruch jest kierowany do źródła o najwyższym priorytecie, jeśli źródło jest w dobrej kondycji. Jeśli źródło jest określone jako w złej kondycji, połączenia są kierowane do innego źródła w kolejności priorytetu. Jeśli dwa źródła mają ten sam priorytet, ruch jest dystrybuowany zgodnie z wagą określoną dla źródła 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Azure CDN wiele źródeł" border="true":::

3. Wybierz pozycję **Dodaj**.

4. Wybierz pozycję **Konfiguruj Źródło** , aby ustawić ścieżkę źródła dla wszystkich źródeł:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Azure CDN wiele źródeł" border="true":::

5. Wybierz przycisk **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurowanie ustawień pochodzenia i grupy pochodzenia

Gdy masz kilka źródeł i grupę pierwotną, możesz dodawać lub usuwać źródła w różnych grupach. Źródła w obrębie tej samej grupy powinny być podobne do obciążeń. Ruch będzie dystrybuowany do tych źródeł w oparciu o ich stan w dobrej kondycji, priorytet i wartość wagi. 

1. W ustawieniach pierwotnego punktu końcowego Azure CDN wybierz pozycję **Konfiguruj grupę pierwotną**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN wiele źródeł" border="true":::

2. Wybierz grupę pierwotną, którą chcesz skonfigurować, w polu rozwijanym, a następnie wybierz **przycisk OK**.

3. W obszarze **Aktualizuj grupę pierwotną**wybierz pozycję **+ Dodaj źródło**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Azure CDN wiele źródeł" border="true":::

4. Wybierz źródło, które ma zostać dodane do grupy w polu ściągania i wybierz **przycisk OK**.

5. Sprawdź, czy źródło zostało dodane do grupy, a następnie wybierz pozycję **Zapisz**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Azure CDN wiele źródeł" border="true":::

## <a name="remove-origin-from-origin-group"></a>Usuń źródło z grupy pochodzenia

1. W ustawieniach pierwotnego punktu końcowego Azure CDN wybierz pozycję **Konfiguruj grupę pierwotną**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN wiele źródeł" border="true":::

2. Wybierz grupę pierwotną, którą chcesz skonfigurować, w polu rozwijanym, a następnie wybierz **przycisk OK**.

3. Aby usunąć źródło z grupy pierwotnej, wybierz ikonę kosza obok początku i wybierz pozycję **Zapisz**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Azure CDN wiele źródeł" border="true":::

## <a name="next-steps"></a>Następne kroki
W tym artykule włączono obsługę wielopunktowego punktu końcowego Azure CDN.

Aby uzyskać więcej informacji na temat Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Usługa Azure CDN](./cdn-overview.md)
* [Porównaj funkcję produktu Azure CDN](./cdn-features.md)
