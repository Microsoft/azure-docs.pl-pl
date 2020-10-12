---
title: Ograniczanie zawartości Azure CDN według kraju/regionu | Microsoft Docs
description: Dowiedz się, jak ograniczyć dostęp według kraju/regionu do zawartości Azure CDN przy użyciu funkcji filtrowania geograficznego.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358138"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Ogranicz zawartość Azure CDN według kraju/regionu

## <a name="overview"></a>Omówienie
Gdy użytkownik zażąda zawartości, domyślnie zawartość jest obsługiwana niezależnie od lokalizacji użytkownika wysyłającego żądanie. Jednak w niektórych przypadkach możesz chcieć ograniczyć dostęp do zawartości według kraju/regionu. Za pomocą funkcji *filtrowania geograficznego* można tworzyć reguły dla określonych ścieżek w punkcie końcowym usługi CDN, aby zezwalać na zawartość lub blokować ją w wybranych krajach/regionach.

> [!IMPORTANT]
> Usługi **Azure CDN Standard from profile firmy Microsoft** nie obsługują filtrowania geograficznego opartego na ścieżkach.
> 

## <a name="standard-profiles"></a>Profile standardowe
Procedury przedstawione w tej sekcji dotyczą tylko **Azure CDN standardowych od Akamai** i **Azure CDN Standard z profilów Verizon** . 

W przypadku **Azure CDN Premium z profilów Verizon** należy użyć portalu **zarządzania** w celu aktywowania filtrowania geograficznego. Aby uzyskać więcej informacji, zobacz [Azure CDN Premium from profile Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiowanie ścieżki katalogu
Aby uzyskać dostęp do funkcji filtrowania geograficznego, wybierz punkt końcowy usługi CDN w portalu, a następnie wybierz pozycję **filtrowanie geograficzne** w obszarze Ustawienia w menu po lewej stronie. 

![Zrzut ekranu przedstawiający filtrowanie geograficzne wybrane z menu dla punktu końcowego.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

W polu **ścieżka** określ ścieżkę względną do lokalizacji, do której użytkownicy będą mogli uzyskać dostęp lub którym odmówiono dostępu. 

Można zastosować filtrowanie geograficzne dla wszystkich plików z ukośnikiem (/) lub wybrać określone foldery, określając ścieżki katalogów (na przykład */Pictures/*). Można również zastosować filtrowanie geograficzne do pojedynczego pliku (na przykład */pictures/city.png*). Dozwolone są wiele reguł; Po wprowadzeniu reguły zostanie wyświetlony pusty wiersz, aby wprowadzić następną regułę.

Na przykład wszystkie następujące filtry ścieżki katalogu są prawidłowe:   
*/*                                 
*Obrazy*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Zdefiniuj typ akcji

Z listy **Akcja** wybierz pozycję **Zezwalaj** lub **Blokuj**: 

- **Zezwalaj**: tylko użytkownicy z określonych krajów/regionów mają dostęp do zasobów żądanych ze ścieżki cyklicznej.

- **Blokuj**: Użytkownicy z określonych krajów/regionów mają odmowę dostępu do zasobów żądanych ze ścieżki cyklicznej. Jeśli nie skonfigurowano żadnych innych opcji filtrowania kraju/regionu dla tej lokalizacji, wszyscy inni użytkownicy będą mogli uzyskać dostęp.

Na przykład reguła filtrowania geograficznego do blokowania ścieżki */Photos/Strasbourg/* filtruje następujące pliki:     
*http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Zdefiniuj kraje/regiony
Z listy **Kody krajów** wybierz kraje/regiony, które mają być blokowane lub dozwolone dla ścieżki. 

Po wybraniu krajów/regionów wybierz pozycję **Zapisz** , aby aktywować nową regułę filtrowania geograficznego. 

![Zrzut ekranu przedstawia kody krajów, które mają być używane do blokowania lub zezwalania na kraje lub regiony.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć regułę, wybierz ją z listy na stronie **filtrowania geograficznego** , a następnie wybierz **Usuń**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium z profilów Verizon
Dla **programu Azure CDN Premium z profilów Verizon** interfejs użytkownika służący do tworzenia reguł filtrowania geograficznego jest różny:

1. W górnym menu w profilu Azure CDN wybierz pozycję **Zarządzaj**.

2. W portalu Verizon wybierz pozycję **http Large**, a następnie wybierz pozycję **Filtrowanie krajów**.

    ![Zrzut ekranu pokazuje, jak wybrać filtrowanie kraju na platformie Azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Wybierz pozycję **Dodaj filtr kraju**.

    Zostanie wyświetlona strona **krok jeden:** .

4. Wprowadź ścieżkę do katalogu, wybierz opcję **Blokuj** lub **Dodaj**, a następnie wybierz przycisk **dalej**.

    Zostanie wyświetlona strona **krok 2** . 

5. Wybierz z listy co najmniej jeden kraj/region, a następnie wybierz pozycję **Zakończ** , aby aktywować regułę. 
    
    Nowa reguła zostanie wyświetlona w tabeli na stronie **Filtrowanie krajów** .

    ![Zrzut ekranu pokazuje, gdzie reguła jest wyświetlana w obszarze Filtrowanie kraju.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Czyszczenie zasobów
W tabeli reguł filtrowania kraju/regionu wybierz ikonę Usuń obok reguły, aby ją usunąć, lub ikonę edycji, aby ją zmodyfikować.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zmiany w konfiguracji filtrowania geograficznego nie zaczynają obowiązywać natychmiast:
   * W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
   * W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
   * W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
 
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład *).

* Konfiguracja filtrowania geograficznego skojarzona ze ścieżką względną jest stosowana rekursywnie do tej ścieżki.

* Do tej samej ścieżki względnej można zastosować tylko jedną regułę. Oznacza to, że nie można utworzyć wielu filtrów kraju/regionu, które wskazują tę samą ścieżkę względną. Ponieważ jednak filtry kraju/regionu są cykliczne, folder może mieć wiele filtrów kraju/regionu. Inaczej mówiąc, do podfolderu w wcześniej skonfigurowanym folderze można przypisać inny filtr kraju/regionu.

* Funkcja filtrowania geograficznego używa kodów krajów do definiowania krajów/regionów, z których żądanie jest dozwolone lub zablokowane dla zabezpieczonego katalogu. Chociaż profile Akamai i Verizon obsługują większość z tych samych kodów kraju, istnieją pewne różnice. Aby uzyskać więcej informacji, zobacz [Azure CDN kodów krajów](/previous-versions/azure/mt761717(v=azure.100)). 

