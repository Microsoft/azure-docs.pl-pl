---
title: Ogranicz zawartość Azure CDN według kraju/regionu
description: Dowiedz się, jak ograniczyć dostęp według kraju/regionu do zawartości Azure CDN przy użyciu funkcji filtrowania geograficznego.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539479"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Ogranicz zawartość Azure CDN według kraju/regionu

## <a name="overview"></a>Omówienie
Gdy użytkownik zażąda zawartości, zawartość jest obsługiwana użytkownikom we wszystkich lokalizacjach. Możesz chcieć ograniczyć dostęp do zawartości według kraju/regionu. 

Za pomocą funkcji *filtrowania geograficznego* można tworzyć reguły dla określonych ścieżek w punkcie końcowym usługi CDN. Możesz ustawić reguły, aby zezwalać lub blokować zawartość w wybranych krajach/regionach.

> [!IMPORTANT]
> Usługi **Azure CDN Standard from profile firmy Microsoft** nie obsługują filtrowania geograficznego opartego na ścieżkach.
> 

## <a name="standard-profiles"></a>Profile standardowe

Te instrukcje są przeznaczone dla **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon** profile.

W przypadku **Azure CDN Premium z profilów Verizon** należy użyć portalu **zarządzania** w celu aktywowania filtrowania geograficznego. Aby uzyskać więcej informacji, zobacz [Azure CDN Premium from profile Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiowanie ścieżki katalogu
Aby uzyskać dostęp do funkcji filtrowania geograficznego, wybierz punkt końcowy usługi CDN w portalu, a następnie wybierz pozycję **filtrowanie geograficzne** w obszarze Ustawienia w menu po lewej stronie. 

![Zrzut ekranu przedstawiający filtrowanie geograficzne wybrane z menu dla punktu końcowego.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

W polu **ścieżka** określ ścieżkę względną do lokalizacji, do której użytkownicy będą mogli uzyskać dostęp lub którym odmówiono dostępu. 

Można zastosować filtrowanie geograficzne dla wszystkich plików z ukośnikiem (/) lub wybrać określone foldery, określając ścieżki katalogów (na przykład */Pictures/*). Można również zastosować filtrowanie geograficzne do pojedynczego pliku (na przykład */pictures/city.png*). Dozwolone są wiele reguł. Po wprowadzeniu reguły zostanie wyświetlony pusty wiersz, aby wprowadzić następną regułę.

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

W przypadku **Azure CDN Premium z profilów Verizon** interfejs użytkownika służący do tworzenia reguł filtrowania geograficznego jest różny:

1. W górnym menu w profilu Azure CDN wybierz pozycję **Zarządzaj**.

2. W portalu Verizon wybierz pozycję **http Large**, a następnie wybierz pozycję **Filtrowanie krajów**.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Zrzut ekranu przedstawia sposób wybierania filtrowania kraju w Azure CDN" border="true":::
  
3. Wybierz pozycję **Dodaj filtr kraju**.

4. W **kroku 1:** wprowadź ścieżkę katalogu. Wybierz pozycję **Blokuj** lub **Dodaj**, a następnie wybierz pozycję **dalej**.

    > [!IMPORTANT]
    > Nazwa punktu końcowego musi znajdować się w ścieżce.  Przykład: **/myendpoint8675/MyFolder**.  Zastąp **myendpoint8675** nazwą punktu końcowego.
    > 
    
5. W **kroku dwa** wybierz co najmniej jeden kraj/region z listy. Wybierz pozycję **Zakończ** , aby aktywować regułę. 
    
    Nowa reguła zostanie wyświetlona w tabeli na stronie **Filtrowanie krajów** .
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Zrzut ekranu pokazuje, gdzie reguła jest wyświetlana w obszarze Filtrowanie kraju." border="true":::
 
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

