---
title: Konfigurowanie punktu końcowego usługi Azure Front-Standard/Premium w programie Endpoint Manager
description: W tym artykule przedstawiono sposób konfigurowania punktu końcowego za pomocą Menedżera punktów końcowych.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099669"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Konfigurowanie punktu końcowego (wersja zapoznawcza) platformy Azure w warstwie Standardowa/Premium przy użyciu programu Endpoint Manager

> [!NOTE]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? Wyświetl **[dokumenty z przodu platformy Azure](../front-door-overview.md)**.

W tym artykule pokazano, jak utworzyć punkt końcowy dla istniejącego profilu usługi Azure Front-Standard/Premium przy użyciu Menedżera punktów końcowych.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było utworzyć punkt końcowy Standard/Premium platformy Azure z programem Endpoint Manager, należy utworzyć co najmniej jeden utworzony profil dla drzwi platformy Azure. Profil musi mieć co najmniej jeden punkt końcowy w warstwie Standardowa lub Premium platformy Azure. Aby zorganizować punkty końcowe Standard/Premium na platformie Azure według domeny internetowej, aplikacji sieci Web lub innych kryteriów, można użyć wielu profilów. 

Aby utworzyć profil z przodu platformy Azure, zobacz [Tworzenie nowego profilu usługi Azure Front-Standard/Premium](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Utwórz nowy punkt końcowy standardu/Premium platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do profilu Standard/Premium drzwi platformy Azure.

1. Wybierz pozycję **Menedżer punktów końcowych**. Następnie wybierz pozycję **Dodaj punkt końcowy** , aby utworzyć nowy punkt końcowy.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Zrzut ekranu przedstawiający Dodawanie punktu końcowego za poorednictwem programu Endpoint Manager.":::

1. Na stronie **Dodawanie punktu końcowego** wprowadź i wybierz poniższe ustawienia.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie punktu końcowego.":::

    | Ustawienia | Wartość |
    | -------- | ----- |
    | Nazwa | Wprowadź unikatową nazwę nowego punktu końcowego usługi Azure Front-Standard/Premium. Ta nazwa służy do uzyskiwania dostępu do buforowanych zasobów w domenie `<endpointname>.az01.azurefd.net` |
    | Limit czasu odpowiedzi pochodzenia (s) | Wprowadź wartość limitu czasu (w sekundach), przez jaką drzwi frontonu platformy Azure będą oczekiwać przed uwzględnieniem limitu czasu połączenia ze źródłem. |
    | Stan | Zaznacz pole wyboru, aby włączyć ten punkt końcowy. |

## <a name="add-domains-origin-group-routes-and-security"></a>Dodawanie domen, grup pochodzenia, tras i zabezpieczeń

1. Wybierz pozycję **Edytuj punkt końcowy** w punkcie końcowym, aby skonfigurować trasę.

1. Na stronie **Edytowanie punktu końcowego** wybierz pozycję **+ Dodaj** w obszarze domeny.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Zrzut ekranu przedstawiający stronę Wybieranie domeny na stronie edytowanie punktu końcowego.":::

### <a name="add-domain"></a>Dodaj domenę

1. Na stronie **Dodawanie domeny** wybierz opcję skojarzenia domeny *z profilu usługi Azure front-drzwi* lub *Dodaj nową domenę*. Aby uzyskać informacje na temat sposobu tworzenia zupełnie nowej domeny, zobacz [Tworzenie nowej domeny niestandardowej w warstwie Standardowa/Premium platformy Azure](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie domeny.":::

1. Wybierz pozycję **Dodaj** , aby dodać domenę do bieżącego punktu końcowego. Wybrana domena powinna pojawić się w panelu domeny.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Zrzut ekranu domen w widoku domeny.":::

### <a name="add-origin-group"></a>Dodaj grupę pochodzenia

1. Wybierz pozycję **Dodaj** w widoku grupy pochodzenia. Zostanie wyświetlona strona **Dodawanie grupy pochodzenia** 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie grupy pochodzenia":::

1. W polu **Nazwa** wprowadź unikatową nazwę nowej grupy pochodzenia

1. Wybierz pozycję **Dodaj źródło** , aby dodać nowe źródło do bieżącej grupy.
 
#### <a name="health-probes"></a>Sondy kondycji
Drzwi z przodu wysyłają okresowe żądania sondowania HTTP/HTTPS do każdego źródła. Żądania sondowania określają bliskość i kondycję każdego źródła do równoważenia obciążenia żądaniami użytkowników końcowych. Ustawienia sondowania kondycji grupy pierwotnej definiują sposób sondowania stanu kondycji pochodzenia aplikacji. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

> [!WARNING]
> Ze względu na to, że drzwi z przodu mają wiele środowisk brzegowych, ilość sondy kondycji dla źródła danych może być dość duża od 25 żądań co minutę do 1200 żądań na minutę, w zależności od skonfigurowanej częstotliwości sondowania kondycji. Domyślna częstotliwość sondy wynosząca 30 sekund, objętość sondy w pochodzeniu powinna wynosić około 200 żądań na minutę.

* **Stan**: Określ, czy należy włączyć badanie kondycji. Jeśli w grupie pochodzenia istnieje pojedynczy punkt początkowy, możesz wyłączyć sondy kondycji zmniejszające obciążenie zaplecze aplikacji. Nawet jeśli w grupie znajduje się wiele źródeł, ale tylko jeden z nich jest w stanie włączonym, można wyłączyć sondy kondycji.
   
* **Path**: adres URL używany do żądania sondowania dla wszystkich źródeł w tej grupie pochodzenia. Na przykład jeśli jeden z źródeł jest contoso-westus.azurewebsites.net, a ścieżka jest ustawiona na/Probe/test.aspx, a następnie środowiska z przodu, przy założeniu, że protokół jest ustawiony na HTTP, program wyśle żądania sondy kondycji do `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protokół**: określa, czy wysyłać żądania sondy kondycji z czołowych drzwi do źródła przy użyciu protokołu HTTP lub https.
   
* **Metoda sondowania**: Metoda http używana do wysyłania sond kondycji. Opcje obejmują GET lub głowy (domyślnie).

    > [!NOTE]
    > W celu obniżenia obciążenia i ponoszenia kosztów w pochodzeniu zaleca się użycie żądań głównych dla sond kondycji.

* **Interwał (w sekundach)**: określa częstotliwość sond kondycji do źródła lub interwały, w których każde ze środowisk przed drzwiami wysyła sondę.
   
    >[!NOTE]
    >Aby przyspieszyć przełączanie w tryb failover, ustaw wartość interwał na niższą. Im niższa wartość, tym większy głośność sondy kondycji, która otrzymuje źródło. Na przykład, jeśli interwał jest ustawiony na 30 sekund z wypowiedzią, 100 czołowe drzwi są globalnie, każde zaplecze otrzyma około 200 żądań sondowania na minutę.

#### <a name="load-balancing"></a>Równoważenie obciążenia
Ustawienia równoważenia obciążenia dla grupy pierwotnej definiują, jak oceniane są sondy kondycji. Te ustawienia określają, czy zaplecze jest w dobrej kondycji. Sprawdzają także, jak równoważyć obciążenie ruchu między różnymi źródłami w grupie pierwotnej. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

- **Rozmiar próbki**. Identyfikuje liczbę próbek sond kondycji, które należy wziąć pod uwagę w celu oceny kondycji pochodzenia.

- **Pomyślny rozmiar próbki**. Definiuje rozmiar próbki, jak wspomniano powyżej, liczbę pomyślnych próbek wymaganych do wywołania pozostałej kondycji. Załóżmy na przykład, że interwał sondy kondycji z przodu wynosi 30 sekund, rozmiar próbki wynosi 5, a rozmiar próbki powiódł się 3. Za każdym razem, gdy analizujemy sondy kondycji pochodzenia, analizujemy pięć ostatnich próbek powyżej 150 sekund (5 x 30). Co najmniej trzy pomyślne sondy są wymagane do zadeklarować zaplecze jako dobra kondycja.

- **Czułość opóźnienia (dodatkowe opóźnienie)**. Określa, czy chcesz, aby drzwiczki frontonu wysyłali żądanie do pochodzenia w ramach zakresu czułości pomiaru opóźnienia, lub przesłać żądanie do najbliższego zaplecza.

Wybierz pozycję **Dodaj** , aby dodać grupę pierwotną do bieżącego punktu końcowego. Grupa pierwotna powinna znajdować się w panelu grupy pochodzenia

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Zrzut ekranu przedstawiający źródła w grupie pierwotnej.":::

### <a name="add-route"></a>Dodawanie trasy

Wybierz pozycję **Dodaj** w widoku trasy. zostanie wyświetlona strona **Dodawanie trasy** . Aby uzyskać informacje dotyczące kojarzenia domeny z grupą pierwotną, zobacz [Tworzenie nowej trasy dla drzwi frontonu platformy Azure](how-to-configure-route.md)

### <a name="add-security"></a>Dodawanie zabezpieczeń

1. Wybierz pozycję **Dodaj** w widoku zabezpieczenia zostanie wyświetlona strona **Dodawanie zasad WAFymi**
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie zasad WAFymi.":::

1. **Zasady WAF**: wybierz zasady WAF, które chcesz zastosować dla wybranej domeny w tym punkcie końcowym. 
  
   Wybierz pozycję **Utwórz nowy** , aby utworzyć nowe zasady WAF.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Zrzut ekranu przedstawiający tworzenie nowych zasad WAF.":::
   
    **Nazwa**: Wprowadź unikatową nazwę dla nowych zasad WAF. Można edytować te zasady z większą konfiguracją na stronie zapory aplikacji sieci Web.

    **Domeny**: Wybierz domenę, w której mają zostać zastosowane zasady WAF.

1. Wybierz przycisk **Dodaj** . Zasady WAF powinny być wyświetlane w panelu zabezpieczenia

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Zrzut ekranu zasad WAF w widoku zabezpieczeń.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć punkt końcowy, gdy nie jest już wymagany, wybierz pozycję **Usuń punkt końcowy** na końcu wiersza punktu końcowego. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania punktu końcowego.":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o domenach niestandardowych, Kontynuuj [Dodawanie domeny niestandardowej](how-to-add-custom-domain.md).
