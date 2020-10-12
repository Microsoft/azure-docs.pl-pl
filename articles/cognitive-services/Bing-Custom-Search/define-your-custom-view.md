---
title: Skonfiguruj środowisko wyszukiwanie niestandardowe Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Portal umożliwia utworzenie wystąpienia wyszukiwania, które określa wycinki sieci Web; domeny, podstrony i strony sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: fa518900b05ba552cb066fbf55e4dbeaa6c681e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86183209"
---
# <a name="configure-your-bing-custom-search-experience"></a>Skonfiguruj środowisko wyszukiwanie niestandardowe Bing

Niestandardowe wystąpienie wyszukiwania umożliwia dostosowanie funkcji wyszukiwania w celu uwzględnienia zawartości tylko z witryn sieci Web, na które ponosili użytkownicy. Zamiast przeprowadzać wyszukiwanie w całej sieci Web, Bing przeszukuje tylko wycinki sieci Web, która Cię interesuje. Aby utworzyć niestandardowy widok internetowy, użyj [portalu](https://customsearch.ai) usługi wyszukiwania niestandardowego Bing.

Portal umożliwia utworzenie wystąpienia wyszukiwania, które określa wycinków sieci Web: domen, podstron i stron internetowych, które mają być wyszukiwane przez usługę Bing, oraz tych, które nie powinny być przeszukiwane. Portal może również zasugerować zawartość, którą można uwzględnić.

Podczas definiowania wycinków sieci Web należy użyć następujących elementów:

| Nazwa wycinka | Opis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Obszar     | Wycinek domeny obejmuje całą zawartość znajdującą się w domenie internetowej. Na przykład `www.microsoft.com`. Pominięcie `www.` powoduje, że Bing również przeszukać domeny podrzędne domeny. Na przykład, jeśli określisz `microsoft.com` , Bing zwróci również wyniki z `support.microsoft.com` lub `technet.microsoft.com` . |
| Podstrony    | Wycinek podstrony obejmuje całą zawartość znajdującą się na podstronie i ścieżkach poniżej. W ścieżce można określić maksymalnie dwa podstrony. Na przykład `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Stron    | Wycinek strony sieci Web może zawierać tylko Tę stronę sieci Web w wyszukiwaniu niestandardowym. Opcjonalnie można określić, czy mają być dołączane podstrony.                                                                                                                                                                                  |

> [!IMPORTANT]
> Wszystkie domeny, podstrony i określone strony sieci Web muszą być publiczne i indeksowane przez usługę Bing. Jeśli jesteś członkiem publicznej witryny, która ma zostać uwzględniona w wyszukiwaniu, a Bing nie jest indeksowany, zapoznaj się z [dokumentacją](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) dotyczącą usługi Bing, aby uzyskać szczegółowe informacje na temat ich indeksowania. Ponadto zapoznaj się z dokumentacją webmaster, aby uzyskać szczegółowe informacje na temat pobierania usługi Bing w celu zaktualizowania przeszukiwanej witryny, jeśli indeks jest nieaktualny.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Dodawanie wycinków sieci Web do niestandardowego wystąpienia wyszukiwania

Podczas tworzenia niestandardowego wystąpienia wyszukiwania można określić wycinków sieci Web: domen, podstron i stron internetowych, które mają być dołączone lub zablokowane w wynikach wyszukiwania. 

Jeśli wiesz, które wycinki chcesz uwzględnić w wystąpieniu wyszukiwania niestandardowego, Dodaj je do **aktywnej** listy wystąpień. 

Jeśli nie masz pewności, które wycinki mają być dołączone, możesz wysyłać zapytania wyszukiwania do usługi Bing w okienku **podglądu** i wybierać wycinki. W tym celu: 

1. Wybierz pozycję "Bing" z listy rozwijanej w okienku podglądu, a następnie wprowadź zapytanie wyszukiwania.

2. Kliknij przycisk **Dodaj witrynę** obok wyniku, który ma zostać uwzględniony. Następnie kliknij przycisk OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Dostosuj środowisko wyszukiwania przy użyciu list aktywnych i zablokowanych 

Aby uzyskać dostęp do listy aktywnych i zablokowanych wycinków, kliknij kartę **aktywne** i **zablokowane** w wystąpieniu wyszukiwania niestandardowego. Wycinki dodane do aktywnej listy zostaną uwzględnione w wyszukiwaniu niestandardowym. Zablokowane wycinki nie będą przeszukiwane i nie będą wyświetlane w wynikach wyszukiwania.

Aby określić wycinków sieci Web, które mają być wyszukiwane przez usługę Bing, kliknij kartę **aktywne** i Dodaj co najmniej jeden adres URL. Aby edytować lub usunąć adresy URL, użyj opcji w kolumnie **formanty** . 

Podczas dodawania adresów URL do **aktywnej** listy można dodać pojedyncze adresy URL lub wiele adresów URL jednocześnie, przekazując plik tekstowy przy użyciu ikony Przekaż.

![Karta wyszukiwanie niestandardowe Bing aktywna](media/file-upload-icon.png)

Aby przekazać plik, Utwórz plik tekstowy i określ pojedynczą domenę, podstronę lub stronę sieci Web na wiersz. Plik zostanie odrzucony, jeśli nie został poprawnie sformatowany.

> [!NOTE]
> * Można przekazać tylko plik do **aktywnej** listy. Nie można używać go do dodawania wycinków do listy **zablokowanych** .  
> * Jeśli lista **zablokowanych** zawiera domenę, podstronę lub stronę sieci Web, która została określona w pliku przekazywania, zostanie usunięta z listy **zablokowanych** i dodana do **aktywnej** listy.
> * Zduplikowane wpisy w pliku przekazywania zostaną zignorowane przez wyszukiwanie niestandardowe Bing. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Uzyskaj sugestie dotyczące witryny sieci Web dla środowiska wyszukiwania

Po dodaniu wycinków internetowych do **aktywnej** listy Portal wyszukiwanie niestandardowe Bing będzie generował sugestie dotyczące witryny sieci Web i podstrony w dolnej części karty. Są to wycinki, które wyszukiwanie niestandardowe Bing mogą chcieć uwzględnić. Kliknij przycisk **Odśwież** , aby uzyskać zaktualizowane sugestie po zaktualizowaniu ustawień niestandardowego wystąpienia wyszukiwania. Ta sekcja jest widoczna tylko wtedy, gdy są dostępne sugestie.

## <a name="search-for-images-and-videos"></a>Wyszukaj obrazy i filmy wideo

Obrazy i filmy wideo można wyszukiwać podobnie jak w przypadku zawartości sieci Web przy użyciu [niestandardowego interfejsu api wyszukiwanie obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) lub [interfejsu API niestandardowego wyszukiwanie wideo Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Możesz wyświetlić te wyniki przy użyciu [hostowanego interfejsu użytkownika](hosted-ui.md)lub interfejsów API. 

Te interfejsy API są podobne do nieniestandardowych [Wyszukiwanie obrazów Bing](../Bing-Image-Search/overview.md) i [Wyszukiwanie wideo Bing](../Bing-Video-Search/search-the-web.md) interfejsów API, ale przeszukiwane są całe sieci Web i nie wymagają `customConfig` parametru zapytania. Więcej informacji na temat pracy z obrazami i filmami można znaleźć w tych zestawach dokumentacji. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testowanie wystąpienia wyszukiwania za pomocą okienka podglądu

Możesz przetestować wystąpienie wyszukiwania, korzystając z okienka podglądu po prawej stronie portalu, aby przesłać zapytania wyszukiwania i wyświetlić wyniki. 

1. Poniżej pola wyszukiwania wybierz pozycję **moje wystąpienie**. Wyniki wyszukiwania można porównać z usługą Bing, wybierając pozycję **Bing**. 
2. Wybierz filtr wyszukiwania bezpiecznego i szukany rynek (zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Wprowadź zapytanie i naciśnij klawisz ENTER lub kliknij ikonę wyszukiwania, aby wyświetlić wyniki z bieżącej konfiguracji. Typ wyszukiwania można zmienić, klikając pozycję **Sieć Web**, **obraz**lub **wideo** , aby uzyskać odpowiednie wyniki. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Dostosuj rangę określonych wyników wyszukiwania

Portal umożliwia dostosowanie kolejności wyszukiwania zawartości z określonych domen, podstron i stron sieci Web. Po wysłaniu zapytania wyszukiwania w okienku podglądu każdy wynik wyszukiwania zawiera listę dostosowań, które można dla niej wprowadzić:  

| Korekty | Opis |
|------------|-------------|
| Zablokowanie      | Przenosi domenę, podstronę lub stronę sieci Web na listę zablokowanych. W wynikach wyszukiwania nie będzie można wykluczać zawartości z wybranej witryny Bing.                    |
| Wzmocnienia      | Zwiększa zawartość z domeny lub podstrony tak, aby była wyższa w wynikach wyszukiwania.                                                                                        |
| Obniż poziom     | Obniża zawartość z domeny lub podstrony poniżej wyników wyszukiwania. Wybierz, czy chcesz obniżyć zawartość z domeny lub podstrony, do której należy strona sieci Web. |
| Przypnij do góry | Przenosi domenę, podstronę lub stronę sieci Web do listy **przypiętych** . Powoduje to wymuszenie wyświetlania strony sieci Web jako górnego wyniku wyszukiwania dla danego zapytania wyszukiwania.                   |

Dostosowywanie rangi nie jest dostępne dla wyszukiwania obrazów i wideo.

### <a name="boosting-and-demoting-search-results"></a>Zwiększanie i obniżanie poziomu wyników wyszukiwania

Możesz poprawić, zwiększyć lub obniżyć każdą domenę lub podstronę na **aktywnej** liście. Domyślnie wszystkie wycinki są dodawane bez korygowania klasyfikacji. Wycinki sieci Web, które są bardzo podwyższana lub podwyższana, są bardziej klasyfikowane w wynikach wyszukiwania (dzięki zwiększeniu klasyfikacji wyższej niż zwiększona). Elementy, które są degradowane, są klasyfikowane w wynikach wyszukiwania.

Można zwiększyć, zwiększyć lub obniżyć poziom elementów przy użyciu kontrolek **Dostosuj klasyfikację** na **aktywnej** liście lub korzystając z regulatorów Zwiększ i Obniż w okienku podglądu. Usługa dodaje wycinek do aktywnej listy i odpowiednio dostosowuje klasyfikację.

> [!NOTE] 
> Zwiększenie i obniżenie poziomu domen i podstron to jedna z wielu metod, wyszukiwanie niestandardowe Bing używa do określenia kolejności wyników wyszukiwania. Ze względu na inne czynniki wpływające na klasyfikację różnej zawartości sieci Web, efekty dostosowywania rangi mogą się różnić. Użyj okienka podglądu, aby przetestować efekty dostosowywania rangi wyników wyszukiwania. 

W przypadku wyszukiwania obrazów i wideo nie są dostępne żadne zwiększenie, zwiększenie i obniżenie poziomu.

## <a name="pin-slices-to-the-top-of-search-results"></a>Przypinanie wycinków na początku wyników wyszukiwania

Portal umożliwia także Przypinanie adresów URL na początku wyników wyszukiwania dla określonych terminów wyszukiwania przy użyciu **przypiętej** karty. Wprowadź adres URL i zapytanie, aby określić stronę sieci Web, która będzie wyświetlana jako górny wynik. Zwróć uwagę na to, że możesz przypiąć maksymalnie jedną stronę sieci Web na zapytanie wyszukiwania, a w wyszukiwaniach będą wyświetlane tylko indeksowane strony. Przypinanie wyników nie jest dostępne dla wyszukiwania obrazów i wideo.

Stronę sieci Web można przypiąć do góry na dwa sposoby:

* Na **przypiętej** karcie wprowadź adres URL strony sieci Web, aby przypiąć do góry i odpowiadającą jej kwerendą.

* W okienku **podglądu** wprowadź zapytanie wyszukiwania, a następnie kliknij przycisk Wyszukaj. Znajdź stronę sieci Web, którą chcesz przypiąć dla zapytania, a następnie kliknij pozycję **Przypnij do góry**. Strona sieci Web i zapytanie zostaną dodane do listy **przypiętych** .

### <a name="specify-the-pins-match-condition"></a>Określ warunek dopasowania numeru PIN

Domyślnie strony sieci Web są przypięte tylko do góry wyników wyszukiwania, gdy ciąg zapytania użytkownika dokładnie pasuje do jednego z wymienionych na liście **przypiętych** . Możesz zmienić to zachowanie, określając jeden z następujących warunków dopasowania:

> [!NOTE]
> Wszystkie porównania między zapytania wyszukiwania użytkownika a zapytaniem wyszukiwania kodu PIN nie uwzględnia wielkości liter.

| Wartość | Opis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Rozpoczyna się od | Kod PIN jest dopasowanie, jeśli ciąg zapytania użytkownika zaczyna się od ciągu zapytania kodu PIN |
| Kończący się na   | Kod PIN jest dopasowanie, jeśli ciąg zapytania użytkownika zostanie zakończony ciągiem zapytania kodu PIN.  |
| Contains    | Kod PIN jest dopasowanie, jeśli ciąg zapytania użytkownika zawiera ciąg zapytania kodu PIN.   |


Aby zmienić warunek dopasowania numeru PIN, kliknij ikonę edycji numeru PIN. W kolumnie **warunek dopasowania zapytania** kliknij listę rozwijaną, a następnie wybierz nowy warunek do użycia. Następnie kliknij ikonę Zapisz, aby zapisać zmiany.

### <a name="change-the-order-of-your-pinned-sites"></a>Zmiana kolejności przypiętych witryn

Aby zmienić kolejność numerów PIN, możesz je przeciągać i upuszczać lub edytować ich numer zamówienia, klikając ikonę "Edytuj" w kolumnie **kontrolki** listy **przypiętej** .

Jeśli wiele numerów PIN spełnia warunek dopasowania, wyszukiwanie niestandardowe Bing będzie używać jednego z najwyższego na liście.

## <a name="view-statistics"></a>Wyświetl statystyki

Jeśli zasubskrybujesz wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [strony cen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), karta **statystyki** jest dodawana do wystąpień produkcyjnych. Karta statystyka zawiera szczegółowe informacje o sposobie używania niestandardowych punktów końcowych wyszukiwania, w tym woluminu wywołań, najważniejszych zapytań, dystrybucji geograficznej, kodów odpowiedzi i bezpiecznego wyszukiwania. Można filtrować szczegóły przy użyciu podanych kontrolek.

## <a name="usage-guidelines"></a>Zalecenia dotyczące użycia

- Dla każdego wystąpienia wyszukiwania niestandardowego Maksymalna liczba korekt klasyfikacji, które mogą zostać wprowadzone do **aktywnych** i **zablokowanych** wycinków, jest ograniczona do 400.
- Dodawanie wycinka do aktywnych lub zablokowanych kart jest liczone jako jedna korekta klasyfikacji.
- Zwiększenie i obniżenie liczby w postaci dwóch korekt klasyfikacji.
- Dla każdego wystąpienia wyszukiwania niestandardowego Maksymalna liczba numerów PIN, które mogą zostać wprowadzone, jest ograniczona do 200.

## <a name="next-steps"></a>Następne kroki

- [Wywoływanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](../bing-web-search/hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
