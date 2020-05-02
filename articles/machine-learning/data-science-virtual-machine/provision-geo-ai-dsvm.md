---
title: 'Szybki Start: Tworzenie geograficznego Data Science Virtual Machine AI'
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i Utwórz geograficzną Data Science Virtual Machine AI na platformie Azure na potrzeby analiz geoprzestrzennych i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77525893"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Szybki Start: Konfigurowanie geograficznej maszyny wirtualnej ze sztuczną inteligencją na platformie Azure 

Geograficzna Data Science Virtual Machine AI (geograficznie DSVM) to rozszerzenie popularnych [Data Science Virtual Machine platformy Azure](https://aka.ms/dsvm) , które jest specjalnie skonfigurowane do łączenia analiz AI i geoprzestrzennych. Analiza geoprzestrzenna na maszynie wirtualnej jest obsługiwana przez [ArcGIS Pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) umożliwia szybkie szkolenie uczenia maszynowego, a nawet modeli uczenia głębokiego. Aby opracować te modele, używa danych, które są wzbogacane o informacje geograficzne. DSVM geograficzna jest obsługiwana tylko w systemie Windows 2016 DSVM. 

Narzędzia AI zawarte w DSVM geograficznym obejmują następujące elementy:

- Wersje procesora GPU popularnych struktur głębokiego uczenia, takich jak Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 i łańcucha
- Narzędzia do uzyskiwania i wstępnego przetwarzania obrazu i danych tekstowych
- Narzędzia dla działań programistycznych, takie jak Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter Notess for Python i R, środowisk IDE dla języków Python i R oraz baz danych SQL
- Oprogramowanie ArcGIS Pro Desktop z ESRI, a także interfejsy Python i R, które mogą współdziałać z danymi geograficznymi z aplikacji AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Utwórz geograficzną Data Science VM AI

Aby utworzyć wystąpienie geograficznego Data Science VM AI, wykonaj następujące kroki:

1. Przejdź do listy maszyn wirtualnych na [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Wybierz pozycję **Utwórz** u dołu, aby wygenerować kreatora:

   ![Create-Geo-AI-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Kreator wymaga danych wejściowych dla każdego z czterech kroków. Aby uzyskać szczegółowe informacje na temat tych danych wejściowych, zobacz następującą sekcję.

### <a name="wizard-details"></a>Szczegóły Kreatora ###

**Podstawy**:

- **Name**: Nazwa tworzonego serwera analizy danych.
    
- **Nazwa użytkownika**: Identyfikator logowania konta administratora.
    
- **Hasło**: hasło konta administratora.
    
- **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której ma zostać utworzona i rozliczona.
    
- **Grupa zasobów**: możesz utworzyć nową lub użyć **pustej** istniejącej grupy zasobów platformy Azure w ramach subskrypcji.
    
- **Lokalizacja**: wybierz najbardziej odpowiednie centrum danych. Zwykle jest to ten, który ma większość danych lub znajduje się najbliżej fizycznej lokalizacji, aby uzyskać najszybszy dostęp do sieci. Jeśli planujesz uruchomić uczenie głębokie na procesorze GPU, musisz wybrać jedną z lokalizacji na platformie Azure, która ma wystąpienia maszyn wirtualnych procesora GPU z serii NC. Obecnie te lokalizacje to: **Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia**. Aby uzyskać najnowszą listę, sprawdź stronę [produkty platformy Azure według regionów](https://azure.microsoft.com/regions/services/) i Wyszukaj **serie NC** w obszarze **obliczenia**. 
    
    
**Ustawienia**: Wybierz jeden z rozmiarów maszyn wirtualnych procesora GPU z serii NC, jeśli planujesz uruchomić uczenie głębokie na procesorze GPU na DSVM geograficznym. W przeciwnym razie można wybrać jedno z wystąpień opartych na PROCESORAch. Utwórz konto magazynu dla maszyny wirtualnej. 
       
**Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje są poprawne.
    
**Kup**: Aby rozpocząć proces aprowizacji, kliknij przycisk **Kup**. Podano link do warunków usługi. Na maszynie wirtualnej nie są naliczane żadne dodatkowe opłaty poza opłaty za serwer wybrany w kroku **rozmiar** . 
 
 >[!NOTE]
 > Inicjowanie obsługi powinno potrwać od 20 do 30 minut. Stan aprowizacji jest wyświetlany na Azure Portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak uzyskać dostęp do geograficznego Data Science Virtual Machine AI

 Po utworzeniu maszyny wirtualnej możesz zacząć korzystać z narzędzi, które są zainstalowane i wstępnie skonfigurowane. Istnieją kafelki menu Start i ikony pulpitu dla wielu narzędzi. Możesz uzyskać dostęp do maszyny wirtualnej za pomocą pulpitu zdalnego przy użyciu poświadczeń konta administratora, które zostały skonfigurowane w sekcji **podstawy** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Korzystanie z ArcGIS Pro zainstalowanego na maszynie wirtualnej

W DSVM geograficznym programu ArcGIS Pro jest preinstalowany i środowisko jest wstępnie skonfigurowane do pracy ze wszystkimi narzędziami w DSVM. Po rozpoczęciu ArcGIS zostanie wyświetlony monit o podanie poświadczeń do konta ArcGIS. Jeśli masz już konto usługi ArcGIS i masz licencje na oprogramowanie, możesz użyć istniejących poświadczeń.  

![Łuk — GIS — logowanie](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

W przeciwnym razie możesz zarejestrować się w celu uzyskania nowego konta i licencji usługi ArcGIS lub skorzystać z [bezpłatnej wersji próbnej](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS — bezpłatna wersja próbna](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po zarejestrowaniu się w celu korzystania ze standardowego konta usługi ArcGIS lub bezpłatnej wersji próbnej możesz autoryzować ArcGIS Pro dla konta, postępując zgodnie z instrukcjami podanymi w [tematach Rozpoczynanie pracy z usługą ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po zalogowaniu się do ArcGIS Pro Desktop za pośrednictwem konta ArcGIS możesz zacząć korzystać z narzędzi do nauki o danych, które są zainstalowane i skonfigurowane na maszynie wirtualnej na potrzeby analiz geoprzestrzennych i projektów uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z Data Science VM geograficznej AI ze wskazówkami z następującego zasobu:

* [Użyj Data Science VM geograficznej AI](use-geo-ai-dsvm.md)
