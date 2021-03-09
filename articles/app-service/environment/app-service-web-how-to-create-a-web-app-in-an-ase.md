---
title: Tworzenie aplikacji sieci Web w środowisku ASE v1
description: Dowiedz się, jak tworzyć aplikacje sieci Web w App Service Environment v1. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505413"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Tworzenie aplikacji sieci Web w App Service Environment v1

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v1.  Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
> 

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób tworzenia aplikacji sieci Web i planów App Service w [App Service Environment V1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Jeśli chcesz dowiedzieć się, jak utworzyć aplikację sieci Web, ale nie musisz jej robić w App Service Environment, zobacz [Tworzenie aplikacji sieci Web platformy .NET](../quickstart-dotnetcore.md) lub jednego z pokrewnych samouczków dla innych języków i struktur.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że utworzono App Service Environment. Jeśli jeszcze tego nie zrobiono, zobacz [tworzenie App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
1. W [witrynie Azure Portal](https://portal.azure.com/)kliknij pozycję **utwórz zasób > sieć Web + aplikacje mobilne > aplikacji sieci Web**. 
   
    ![Zrzut ekranu pokazujący, gdzie utworzyć aplikację sieci Web w Azure Portal.][1]
2. Wybierz subskrypcję.  
   
    Jeśli masz wiele subskrypcji, które należy wziąć pod uwagę, aby utworzyć aplikację w App Service Environment, musisz użyć tej samej subskrypcji, która została użyta podczas tworzenia środowiska. 
3. Wybierz lub utwórz grupę zasobów.
   
    *Grupy zasobów* umożliwiają zarządzanie powiązanymi zasobami platformy Azure jako jednostką i są przydatne podczas ustanawiania reguł *kontroli dostępu opartej na ROLACH (Azure RBAC)* dla Twoich aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ResourceGroups]. 
4. Wybierz lub utwórz plan usługi App Service.
   
    *Plany App Service* są zarządzanymi zestawami aplikacji sieci Web.  Zwykle w przypadku wybrania cennika opłata naliczana jest stosowana do planu App Service, a nie do poszczególnych aplikacji. W środowisku ASE płacisz za wystąpienia obliczeniowe przydzieloną do środowiska ASE, a nie elementy wyświetlane na stronie ASP.  Skalowanie w górę liczby wystąpień aplikacji sieci Web pozwala skalować wystąpienia planu App Service i ma wpływ na wszystkie aplikacje sieci Web w tym planie.  Niektóre funkcje, takie jak gniazda witryny lub Integracja z siecią wirtualną, mają także ograniczenia dotyczące ilości w ramach planu.  Aby uzyskać więcej informacji, zobacz [Azure App Service planów — Omówienie](../overview-hosting-plans.md)
   
    Możesz zidentyfikować App Service plany w środowisku ASE, przeglądając lokalizację zanotowaną pod nazwą planu.  
   
    ![Zrzut ekranu pokazujący, że zostały wyświetlone plany App Service w środowisku ASE.][5]
   
    Jeśli chcesz użyć planu App Service, który już istnieje w App Service Environment, wybierz ten plan. Jeśli chcesz utworzyć nowy plan App Service, zapoznaj się z sekcją w tym samouczku, aby [utworzyć plan App Service w App Service Environment](#createplan).
5. Wprowadź nazwę aplikacji sieci Web, a następnie kliknij przycisk **Utwórz**. 
   
    Jeśli środowisko ASE używa zewnętrznego adresu VIP, adres URL aplikacji w środowisku ASE to: [*sitename*]. [*nazwa App Service Environment*]. p.azurewebsites.NET zamiast [*sitename*]. azurewebsites.NET
   
    Jeśli środowisko ASE używa wewnętrznego adresu VIP, adres URL aplikacji w tym środowisku ASE to: [*sitename*]. [*poddomena określona podczas tworzenia środowiska ASE*]   
    Po wybraniu programu ASP podczas tworzenia środowiska ASE zostanie wyświetlona **Nazwa** poddomeny aktualizacji poniżej

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Utwórz plan App Service
Po utworzeniu planu App Service w App Service Environment opcje procesu roboczego różnią się w zależności od tego, czy w środowisku ASE nie ma udostępnionych procesów roboczych.  Pracownicy, których zamierzasz używać, to te, które zostały przydzielone do środowiska ASE przez administratora.  Oznacza to, że aby utworzyć nowy plan, należy mieć więcej procesów roboczych przydzielonych do puli procesu roboczego środowiska ASE niż łączna liczba wystąpień we wszystkich planach, które znajdują się już w tej puli procesów roboczych.  Jeśli w puli procesów roboczych środowiska ASE nie masz wystarczającej liczby pracowników do utworzenia planu, musisz skontaktować się z administratorem środowiska ASE, aby je dodać.

Kolejną różnicą w przypadku planów App Service hostowanych przez App Service Environment jest brak wyboru cen.  Jeśli masz App Service Environment płacisz za zasoby obliczeniowe używane przez system i nie dodaliśmy opłat za plany w tym środowisku.  Zwykle podczas tworzenia planu App Service należy wybrać plan cenowy, który określa rozliczenia.  App Service Environment jest w zasadzie lokalizacją prywatną, w której można utworzyć zawartość.  Płacisz za środowisko i nie będzie hostować zawartości.

Poniższe instrukcje pokazują, jak utworzyć plan App Service podczas tworzenia aplikacji sieci Web zgodnie z opisem w poprzedniej sekcji tego samouczka.

1. Kliknij pozycję **Utwórz nowy** w interfejsie użytkownika wyboru planu i podaj nazwę planu tak samo, jak na ogół poza środowiskiem ASE.
2. Wybierz środowisko ASE, którego chcesz używać z poziomu selektora lokalizacji.
   
    Ponieważ App Service Environment jest zasadniczo lokalizacją prywatnego wdrożenia, zostanie ona wyświetlona w obszarze Lokalizacja. 
   
    ![Zrzut ekranu pokazujący selektor lokalizacji służący do wybierania żądanego środowiska ASE.][2]
   
    Po wybraniu środowiska ASE w selektorze lokalizacji aktualizacje interfejsu użytkownika tworzenia planu App Service.  Lokalizacja zawiera teraz nazwę systemu ASE i region, w którym znajduje się, a selektor planu cenowego jest zastępowany selektorem puli procesów roboczych.  
   
    ![Zrzut ekranu pokazujący szczegóły systemu ASE po wybraniu środowiska ASE w selektorze lokalizacji.][3]

### <a name="selecting-a-worker-pool"></a>Wybieranie puli procesów roboczych
Zwykle w Azure App Service i poza App Service Environment, dostępne są 3 rozmiary obliczeniowe z wyborem dedykowanego planu cenowego.  W podobny sposób, dla środowiska ASE można zdefiniować maksymalnie 3 pule procesów roboczych, a także określić rozmiar obliczeń używany dla tej puli.  Znaczenie dla dzierżawców środowiska ASE polega na tym, że zamiast wybierać plan cenowy z rozmiarem obliczeniowym dla planu App Service, należy wybrać element, który jest nazywany *pulą procesów roboczych*.  

Interfejs użytkownika wyboru puli procesów roboczych pokazuje rozmiar obliczeń używany dla tej puli procesów roboczych poniżej nazwy.  Dostępna ilość odnosi się do liczby wystąpień obliczeniowych dostępnych do użycia w tej puli.  Całkowita liczba wystąpień w puli może być większa niż ta liczba, ale ta wartość określa, jak wiele nie jest w użyciu.  Jeśli musisz dostosować App Service Environment, aby dodać więcej zasobów obliczeniowych, zobacz [konfigurowanie App Service Environment](app-service-web-configure-an-app-service-environment.md).

![Zrzut ekranu przedstawiający okienko Pula procesów roboczych, w którym można wybrać pule procesów roboczych dla środowiska ASE.][4]

W tym przykładzie widoczne są tylko dwie dostępne pule procesów roboczych. Wynika to z faktu, że administrator ASE przydzielił tylko hosty do tych dwóch pul procesów roboczych.  Trzecia wartość zostanie wyświetlona w przypadku przydzielenia maszyn wirtualnych.  

## <a name="after-web-app-creation"></a>Po utworzeniu aplikacji sieci Web
Istnieje kilka kwestii dotyczących uruchamiania aplikacji sieci Web i zarządzania planami App Service w środowisku ASE, które należy wziąć pod uwagę.  

Jak wspomniano wcześniej, właściciel środowiska ASE jest odpowiedzialny za rozmiar systemu i w związku z tym jest odpowiedzialny za zapewnienie wystarczającej pojemności do hostowania żądanych planów App Service. Jeśli nie ma żadnych dostępnych procesów roboczych, nie będziesz w stanie utworzyć planu App Service.  Jest to również prawdziwe skalowanie aplikacji sieci Web.  Jeśli potrzebujesz więcej wystąpień, musisz uzyskać App Service Environment administratora, aby dodać więcej procesów roboczych.

Po utworzeniu aplikacji internetowej i zaplanowaniu App Service warto ją skalować w górę.  W środowisku ASE zawsze musisz mieć co najmniej 2 wystąpienia planu App Service, aby zapewnić odporność na uszkodzenia dla aplikacji.  Skalowanie planu App Service w środowisku ASE jest takie samo jak normalne za pomocą interfejsu użytkownika planu App Service.  Aby uzyskać więcej informacji na temat skalowania, [Jak skalować aplikację sieci Web w App Service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/