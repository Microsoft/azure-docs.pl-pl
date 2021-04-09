---
title: Azure CDN wiele lokalizacji punktu końcowego (wersja zapoznawcza)
description: Rozpocznij pracę z wieloma źródłami punktu końcowego Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685775"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN wiele lokalizacji punktu końcowego

Obsługa wieloskładnikowa eliminuje przestoje i ustala globalną nadmiarowość. 

Wybranie wielu źródeł w ramach punktu końcowego Azure CDN zapewnia nadmiarowość ryzyka przez badanie kondycji każdego pochodzenia i przełączenie w tryb failover w razie potrzeby.

Skonfiguruj jedną lub więcej grup pochodzenia i wybierz domyślną grupę pierwotną. Każda grupa pochodzenia jest kolekcją co najmniej jednego źródła, które może przyjmować podobne obciążenia.

> [!NOTE]
> Obecnie ta funkcja jest dostępna tylko w Azure CDN firmy Microsoft. 

## <a name="create-the-origin-group"></a>Tworzenie grupy pochodzenia

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2. Wybierz profil Azure CDN a następnie wybierz punkt końcowy, który ma zostać skonfigurowany dla wieloskładnikowego.

3. Wybierz pozycję **Źródło** w obszarze **Ustawienia** w konfiguracji punktu końcowego:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Punkt końcowy usługi CDN" border="true":::

4. Aby można było włączyć wiele źródeł, potrzebna jest co najmniej jedna grupa pochodzenia. Wybierz pozycję **Utwórz grupę pierwotną**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Ustawienia źródła" border="true":::

5. W konfiguracji **grupy dodawania pochodzenia** wprowadź lub wybierz następujące informacje:

   | Ustawienie           | Wartość                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nazwa grupy pochodzenia | Wprowadź nazwę grupy pierwotnej.                                   |
   | Stan sondy      | Wybierz pozycję **Włączone**. </br> Aby określić kondycję pochodzenia, Azure CDN będą uruchamiać sondy kondycji z różnych punktów na całym świecie. Nie włączaj, jeśli bieżąca Grupa pochodzenia nie jest aktywna, aby uniknąć dodatkowych kosztów.
   | Ścieżka sondy        | Ścieżka w pochodzeniu, która jest używana do określania kondycji. |
   | Interwał sondy    | Wybierz interwał sondowania równy 1, 2 lub 4 minuty.                        |
   | Protokół sondy    | Wybierz pozycję **http** lub **https**.                                         |
   | Metoda sondowania      | Wybierz pozycję **główna** lub **Pobierz**.                                           |
   | Domyślna grupa pierwotna | Zaznacz pole, aby ustawić jako domyślną grupę pierwotną.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Dodaj grupę pochodzenia" border="true":::

6. Wybierz pozycję **Dodaj**.

## <a name="add-multiple-origins"></a>Dodaj wiele źródeł

1. W ustawieniach pochodzenia dla punktu końcowego wybierz pozycję **+ Utwórz źródło**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Utwórz źródło" border="true":::

2. Wprowadź lub wybierz następujące informacje z konfiguracji **dodawania źródła** :

   | Ustawienie           | Wartość                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nazwa        | Wprowadź nazwę źródła.        |
   | Typ źródła | Wybierz pozycję **Magazyn**, **Usługa w chmurze**, **aplikacja internetowa** lub **Źródło niestandardowe**.                                   |
   | Nazwa hosta źródła        | Wybierz lub wprowadź nazwę hosta źródła.  Na liście rozwijanej są wyświetlane wszystkie dostępne źródła typu określonego w poprzednim ustawieniu. W przypadku wybrania jako typu źródła **niestandardowego źródła** danych wprowadź domenę serwera pochodzenia klienta. |
   | Nagłówek hosta źródła    | Wprowadź nagłówek hosta, który ma Azure CDN wysyłać z każdym żądaniem, lub pozostaw wartość domyślną.                        |
   | Port HTTP   | Wprowadź port HTTP.                                         |
   | Port HTTPS     | Wprowadź port HTTPS.                                           |
   | Priorytet    | Wprowadź liczbę z zakresu od 1 do 5.       |
   | Waga      | Wprowadź liczbę z zakresu od 1 do 1000.   |

    > [!NOTE]
    > Gdy źródło jest tworzone w ramach grupy pierwotnej, musi mieć według priorytetu i wagi. Jeśli grupa pochodzenia ma tylko jeden element źródłowy, wówczas domyślny priorytet i waga są ustawiane jako 1. Ruch jest kierowany do źródła o najwyższym priorytecie, jeśli źródło jest w dobrej kondycji. Jeśli źródło jest określone jako w złej kondycji, połączenia są kierowane do innego źródła w kolejności priorytetu. Jeśli dwa źródła mają ten sam priorytet, ruch jest dystrybuowany zgodnie z wagą określoną dla źródła 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Dodaj dodatkowe źródło" border="true":::

3. Wybierz pozycję **Dodaj**.

4. Wybierz pozycję **Konfiguruj Źródło** , aby ustawić ścieżkę źródła dla wszystkich źródeł:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Konfigurowanie ścieżki pochodzenia" border="true":::

5. Wybierz przycisk **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurowanie ustawień pochodzenia i grupy pochodzenia

Gdy masz kilka źródeł i grupę pierwotną, możesz dodawać lub usuwać źródła w różnych grupach. Źródła w obrębie tej samej grupy powinny być podobne do obciążeń. Ruch będzie dystrybuowany do tych źródeł w oparciu o ich stan w dobrej kondycji, priorytet i wartość wagi. 

1. W ustawieniach pierwotnego punktu końcowego Azure CDN wybierz nazwę grupy pochodzenia, którą chcesz skonfigurować:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Konfigurowanie ustawień pochodzenia i grupy pochodzenia" border="true":::

2. W obszarze **Aktualizuj grupę pierwotną** wybierz pozycję **+ źródło pochodzenia**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Aktualizuj grupę pierwotną" border="true":::

4. Wybierz źródło, które ma zostać dodane do grupy w polu ściągania i wybierz **przycisk OK**.

5. Sprawdź, czy źródło zostało dodane do grupy, a następnie wybierz pozycję **Zapisz**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Sprawdź, czy dodano dodatkowe źródło do grupy" border="true":::

## <a name="remove-origin-from-origin-group"></a>Usuń źródło z grupy pochodzenia

1. W ustawieniach pierwotnego punktu końcowego Azure CDN wybierz nazwę grupy pochodzenia:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Usuń źródło z grupy" border="true":::

2. Aby usunąć źródło z grupy pierwotnej, wybierz ikonę kosza obok początku i wybierz pozycję **Zapisz**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Aktualizowanie źródła usuwania grupy pochodzenia" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Przesłoń grupę pierwotną z aparatem reguł

Dostosuj sposób dystrybucji ruchu do różnych grup pochodzenia przy użyciu standardowego aparatu reguł.

Dystrybuuj ruch do innej grupy w oparciu o adres URL żądania.

1. W punkcie końcowym usługi CDN wybierz opcję **aparat reguł** w obszarze **Ustawienia**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Aparat reguł" border="true":::

2. Wybierz pozycję **+ Dodaj regułę**.

3. Wprowadź nazwę reguły w polu **Nazwa**.

4. Wybierz pozycję **+ warunek**, a następnie wybierz pozycję **Ścieżka adresu URL**.

5. W polu ściąganie **operatora** wybierz pozycję **zawiera**.

6. W polu **wartość** wprowadź **/images**.

7. Wybierz pozycję **+ Dodaj akcję**, a następnie wybierz pozycję **Przesłoń grupę pierwotną**.

8. W **grupie pierwotnej** wybierz grupę pierwotną w polu ściągania.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Warunki aparatu reguł" border="true":::

W przypadku wszystkich żądań przychodzących, gdy ścieżka URL zawiera **/images**, żądanie zostanie przypisane do grupy pierwotnej w sekcji akcji **(obiekt pochodzenia)**. 

## <a name="next-steps"></a>Następne kroki
W tym artykule włączono obsługę wielopunktowego punktu końcowego Azure CDN.

Aby uzyskać więcej informacji na temat Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Azure CDN](./cdn-overview.md)
* [Porównaj funkcję produktu Azure CDN](./cdn-features.md)
