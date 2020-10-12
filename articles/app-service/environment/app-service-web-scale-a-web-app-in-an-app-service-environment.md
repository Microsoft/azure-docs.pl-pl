---
title: Skalowanie aplikacji w środowisku ASE v1
description: Skalowanie aplikacji w App Service Environment. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220562"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Skalowanie aplikacji w App Service Environment v1
W Azure App Service zwykle trzy rzeczy, które można skalować:

* plan cenowy
* rozmiar procesu roboczego 
* Liczba wystąpień.

W środowisku ASE nie trzeba wybierać ani zmieniać planu cenowego.  W zakresie możliwości jest już na poziomie możliwości cenowych w warstwie Premium.  

W odniesieniu do rozmiarów procesów roboczych administrator środowiska ASE może przypisać rozmiar zasobu obliczeniowego, który ma być używany dla każdej puli procesów roboczych.  Oznacza to, że w razie potrzeby można mieć pulę procesów roboczych 1 z zasobami obliczeniowymi P4 i pulą procesów roboczych 2 z zasobami obliczeniowymi P1.  Nie muszą mieć kolejności rozmiaru.  Aby uzyskać szczegółowe informacje o rozmiarach i ich cenach, zobacz dokument tutaj [Azure App Service Cennik][AppServicePricing].  Spowoduje to pozostawienie opcji skalowania dla aplikacji sieci Web i planów App Service w App Service Environment, aby:

* wybór puli procesów roboczych
* Liczba wystąpień

Zmiana dowolnego elementu odbywa się za pomocą odpowiedniego interfejsu użytkownika wyświetlanego dla planów App Service hostowanych przez środowisko ASE.  

![Zrzut ekranu pokazujący, gdzie można wyświetlić szczegóły planu usługi skalowania oraz planu usługi puli procesów roboczych.][1]

Nie można skalować w górę stron ASP poza liczbą dostępnych zasobów obliczeniowych w puli procesów roboczych, w której znajduje się środowisko ASP.  Jeśli potrzebujesz zasobów obliczeniowych w tej puli procesów roboczych, musisz mieć uprawnienia administratora programu ASE, aby je dodać.  Aby uzyskać informacje na temat ponownego konfigurowania środowiska ASE, przeczytaj informacje tutaj: [jak skonfigurować środowisko App Service][HowtoConfigureASE].  Warto również skorzystać z funkcji automatycznego skalowania środowiska ASE w celu dodania pojemności na podstawie harmonogramu lub metryk.  Aby uzyskać więcej informacji na temat konfigurowania automatycznego skalowania dla środowiska ASE, zobacz [jak skonfigurować Skalowanie automatyczne dla App Service Environment][ASEAutoscale].

Można utworzyć wiele planów usługi App Service przy użyciu zasobów obliczeniowych z różnych pul procesów roboczych lub użyć tej samej puli procesów roboczych.  Jeśli na przykład masz (10) dostępne zasoby obliczeniowe w puli procesów roboczych 1, możesz utworzyć jeden plan usługi App Service za pomocą (6) zasobów obliczeniowych i drugi plan usługi App Service, który używa (4) zasobów obliczeniowych.

### <a name="scaling-the-number-of-instances"></a>Skalowanie liczby wystąpień
Podczas pierwszej tworzenia aplikacji sieci Web w App Service Environment rozpoczyna się od 1 wystąpienia.  Następnie można skalować w poziomie do dodatkowych wystąpień, aby zapewnić dodatkowe zasoby obliczeniowe dla aplikacji.   

Jeśli środowisko ASE ma wystarczającą ilość miejsca, jest to bardzo proste.  Przejdź do planu App Service, w którym znajdują się lokacje, które chcesz skalować w górę i wybierz pozycję Skala.  Spowoduje to otwarcie interfejsu użytkownika, w którym można ręcznie ustawić skalę dla ASP lub skonfigurować reguły automatycznego skalowania dla środowiska ASP.  Aby ręcznie skalować aplikację, wystarczy ustawić ***skalę według*** ***liczby wystąpień wprowadzanych ręcznie***.  W tym miejscu przeciągnij suwak do żądanej ilości lub wprowadź go w polu obok suwaka.  

![Zrzut ekranu pokazujący, gdzie można ustawić skalę dla ASP lub skonfigurować reguły automatycznego skalowania dla środowiska ASP.][2] 

Reguły automatycznego skalowania środowiska ASP w środowisku ASE działają tak samo, jak w normalnych warunkach.  Możesz wybrać ***wartość procentową procesora CPU*** w obszarze ***skalowanie według*** i utworzyć reguły automatycznego skalowania dla ASP w oparciu o wartość procentową procesora CPU lub utworzyć bardziej złożone reguły przy użyciu ***reguł harmonogramu i wydajności***.  Aby zapoznać się z bardziej szczegółowymi informacjami na temat konfigurowania automatycznego skalowania, Skorzystaj z przewodnika [w sekcji skalowanie aplikacji w Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Wybór puli procesów roboczych
Jak wspomniano wcześniej, do wyboru puli procesów roboczych jest uzyskiwany dostęp z interfejsu użytkownika ASP.  Otwórz blok dla ASP, który chcesz skalować, i wybierz pozycję Pula procesów roboczych.  Zostaną wyświetlone wszystkie pule procesów roboczych, które zostały skonfigurowane w App Service Environment.  Jeśli istnieje tylko jedna pula procesów roboczych, zostanie wyświetlona tylko jedna pula.  Aby zmienić pulę procesów roboczych, w której znajduje się środowisko ASP, wystarczy wybrać pulę procesów roboczych, do której ma zostać przeniesiony plan App Service.  

![Zrzut ekranu pokazujący, gdzie można zmienić pulę procesów roboczych, w której znajduje się środowisko ASP.][3]

Przed przeniesieniem ASP z jednej puli procesów roboczych do innej należy upewnić się, że będzie ona mieć odpowiednią pojemność dla środowiska ASP.  Na liście pul procesów roboczych nie tylko jest wyświetlana nazwa puli procesów roboczych, ale można także zobaczyć, ile procesów roboczych jest dostępnych w tej puli.  Upewnij się, że masz wystarczającą liczbę wystąpień, aby móc zawierać plan App Service.  Jeśli potrzebujesz więcej zasobów obliczeniowych w puli procesów roboczych, w której chcesz przenieść, a następnie poproś administratora programu ASE o ich dodanie.  

> [!NOTE]
> Przeniesienie ASP z jednej puli procesów roboczych spowoduje zimne uruchomienie aplikacji w tym środowisku ASP.  Może to spowodować spowolnienie działania żądań, gdy aplikacja zostanie zimnie uruchomiona w nowych zasobach obliczeniowych.  Zimny start można uniknąć przy użyciu [funkcji rozgrzewania aplikacji][AppWarmup] w Azure App Service.  Moduł inicjalizacji aplikacji opisany w artykule działa również w przypadku zimnego uruchamiania, ponieważ proces inicjowania jest również wywoływany, gdy aplikacje są zimnie uruchamiane w nowych zasobach obliczeniowych. 
> 
> 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [jak utworzyć App Service Environment][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
