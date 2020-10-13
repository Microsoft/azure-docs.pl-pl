---
title: Tworzenie środowiska ASE v1
description: Opis przepływu tworzenia dla środowiska usługi App Service w wersji 1. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220579"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Jak utworzyć App Service Environment v1 

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v1. Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
> 

### <a name="overview"></a>Omówienie
App Service Environment (ASE) to opcja usługi Premium Azure App Service, która zapewnia rozszerzoną funkcję konfiguracji, która nie jest dostępna w sygnaturach z wieloma dzierżawcami. Funkcja ASE zasadniczo wdraża Azure App Service w sieci wirtualnej klienta. Aby uzyskać lepsze zrozumienie możliwości oferowanych przez App Service środowiska, przeczytaj artykuł [co to jest App Service Environment][WhatisASE] dokumentacja.

### <a name="before-you-create-your-ase"></a>Przed utworzeniem środowiska ASE
Ważne jest, aby mieć świadomość, że nie można zmienić. Te aspekty nie mogą ulec zmianie po utworzeniu środowiska ASE:

* Lokalizacja
* Subskrypcja
* Grupa zasobów
* Użyta Sieć wirtualna
* Używana podsieć 
* Rozmiar podsieci

Podczas wybierania sieci wirtualnej i określania podsieci upewnij się, że jest wystarczająco duża, aby pomieścić przyszłe przyrosty. 

### <a name="creating-an-app-service-environment-v1"></a>Tworzenie App Service Environment v1
Aby utworzyć App Service Environment V1, możesz przeszukać witrynę Azure Marketplace pod kątem ***App Service Environment V1***lub przejść przez polecenie **Utwórz zasób**  ->  **Sieć Web + aplikacje mobilne**  ->  **App Service Environment**. Aby utworzyć środowiska asev1:

1. Podaj nazwę środowiska ASE. Nazwa określona dla środowiska ASE zostanie użyta dla aplikacji utworzonych w środowisku ASE. Jeśli nazwa środowiska ASE to appsvcenvdemo, nazwą domeny podrzędnej byłaby: *appsvcenvdemo.p.azurewebsites.NET*. Jeśli w ten sposób utworzysz aplikację o nazwie *MojaAplikacja*, będzie ona adresować w *mytestapp.appsvcenvdemo.p.azurewebsites.NET*. Nie można użyć odstępu w nazwie środowiska ASE. Jeśli w nazwie używane są wielkie litery, nazwa domeny będzie sumą małych liter tej nazwy. Jeśli używasz ILB, Nazwa środowiska ASE nie zostanie użyta w poddomenie, ale zamiast tego zostanie jawnie określona podczas tworzenia środowiska ASE.
   
    ![Zrzut ekranu pokazujący sposób tworzenia App Service Environment (ASE).][1]
2. Wybierz subskrypcję. Subskrypcja używana dla środowiska ASE również będzie miała zastosowanie do wszystkich aplikacji tworzonych w tym środowisku ASE. Nie można umieścić środowiska ASE w sieci wirtualnej, która znajduje się w innej subskrypcji.
3. Wybierz lub określ nową grupę zasobów. Grupa zasobów używana dla środowiska ASE musi być taka sama, jak w przypadku sieci wirtualnej. W przypadku wybrania istniejącej sieci wirtualnej wybór grupy zasobów dla środowiska ASE zostanie zaktualizowany w celu odzwierciedlenia tej sieci wirtualnej.
   
    ![Zrzut ekranu pokazujący sposób wybierania lub modyfikowania nowej grupy zasobów.][2]
4. Zmień wybór Virtual Network i lokalizacji. Można utworzyć nową sieć wirtualną lub wybrać istniejącą wcześniej sieć wirtualną. W przypadku wybrania nowej sieci wirtualnej można określić nazwę i lokalizację. Nowa sieć wirtualna będzie miała zakres adresów 192.168.250.0/23 i podsieć o nazwie **default** , która jest zdefiniowana jako 192.168.250.0/24. Możesz również po prostu wybrać istniejącą wcześniej sieć wirtualną lub Menedżer zasobów sieci wirtualnej. Wybór typu adresu VIP określa, czy do środowiska ASE można uzyskać dostęp bezpośrednio z Internetu (zewnętrznego) czy też używa wewnętrznego Load Balancer (ILB). Aby dowiedzieć się więcej na temat odczytu [przy użyciu wewnętrznego Load Balancer z App Service Environment][ILBASE]. W przypadku wybrania typu adresu VIP zewnętrznego można wybrać liczbę zewnętrznych adresów IP, z którymi system zostanie utworzony, na potrzeby Połączenie SSL z adresu IP. W przypadku wybrania opcji wewnętrzne należy określić poddomenę, która będzie używana przez środowisko ASE. Środowisk ASE można wdrożyć w sieciach *wirtualnych korzystających* z zakresów adresów publicznych *lub* przestrzeni adresów RFC1918 (np. adresów prywatnych). Aby można było używać sieci wirtualnej z zakresem adresów publicznych, należy najpierw utworzyć sieć wirtualną przed czasem. Po wybraniu istniejącej sieci wirtualnej należy utworzyć nową podsieć podczas tworzenia środowiska ASE. **Nie można użyć wstępnie utworzonej podsieci w portalu. Jeśli tworzysz środowisko ASE przy użyciu szablonu usługi Resource Manager, możesz utworzyć środowisko ASE z istniejącą podsiecią.** Aby utworzyć środowisko ASE na podstawie szablonu, użyj tutaj informacji, [tworząc App Service Environment z szablonu][ILBAseTemplate] i w tym miejscu, [tworząc App Service Environment ILB z szablonu][ASEfromTemplate].

### <a name="details"></a>Szczegóły
Środowisko ASE jest tworzone za pomocą 2 frontonów i 2 procesów roboczych. Frontony działają jako punkty końcowe protokołu HTTP/HTTPS i wysyłają ruch do pracowników, którzy są rolami obsługującymi Twoje aplikacje. Możesz dostosować ilość po utworzeniu środowiska ASE i nawet skonfigurować reguły skalowania automatycznego dla tych pul zasobów. Aby uzyskać więcej informacji na temat ręcznego skalowania, zarządzania i monitorowania App Service Environment przejdź tutaj: [jak skonfigurować App Service Environment][ASEConfig] 

Tylko jeden ASE może istnieć w podsieci używanej przez środowisko ASE. Podsieci nie można używać dla elementów innych niż środowisko ASE

### <a name="after-app-service-environment-v1-creation"></a>Po utworzeniu App Service Environment v1
Po utworzeniu środowiska ASE można dostosować:

* Liczba frontonów (minimum: 2)
* Liczba procesów roboczych (minimum: 2)
* Liczba adresów IP dostępnych dla Połączenie SSL z adresu IP
* Rozmiary zasobów obliczeniowych używane przez frontony i procesy robocze (minimalny rozmiar frontonu to P2)

Istnieje więcej szczegółów dotyczących ręcznego skalowania, zarządzania i monitorowania środowisk App Service w tym miejscu: [jak skonfigurować App Service Environment][ASEConfig] 

Informacje na temat automatycznego skalowania zawiera Przewodnik: [jak skonfigurować Skalowanie automatyczne dla App Service Environment][ASEAutoscale]

Istnieją dodatkowe zależności, które nie są dostępne do dostosowania, takie jak baza danych i magazyn. Są one obsługiwane przez platformę Azure i są dołączone do systemu. Magazyn systemowy obsługuje do 500 GB dla całego App Service Environment i baza danych jest dostosowywana przez platformę Azure, zgodnie z wymaganiami skalowania systemu.

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service Environment V1, zobacz [wprowadzenie do App Service Environment V1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
