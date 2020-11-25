---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1af80f208c72af9434d596f2c1219c08c0e3f719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016013"
---
## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.  Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Poniższy przykład tworzy galerię o nazwie Moja *Gallery* w grupie zasobów *myGalleryRG* .

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Użyj **galerii obrazów udostępnionych** w polu wyszukiwania i wybierz pozycję **Galeria obrazów udostępnionych** w wynikach.
1. Na stronie **galerii obrazów udostępnionych** kliknij przycisk **Dodaj**.
1. Na stronie **Tworzenie galerii obrazów udostępnionych** wybierz odpowiednią subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wpisz *myGalleryRG* w polu Nazwa.
1. W polu **Nazwa** wpisz *webgallery* jako nazwę galerii.
1. Pozostaw wartość domyślną dla **regionu**.
1. Możesz wpisać Krótki opis galerii, *na przykład moją galerię obrazów do testowania.* a następnie kliknij pozycję **Przegląd + Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](../articles/virtual-machines/windows/shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu galerii w galerii. W tym przykładzie obraz galerii ma nazwę *myImageDefinition*.

1. Na stronie nowej galerii obrazów wybierz pozycję **Dodaj nową definicję obrazu** w górnej części strony. 
1. W obszarze **Dodaj nową definicję obrazu do galerii obrazów udostępnionych** w polu **region** wybierz pozycję *Wschodnie stany USA*.
1. W obszarze **Nazwa definicji obrazu** wpisz *myImageDefinition*.
1. W obszarze **system operacyjny** Wybierz poprawną opcję w oparciu o ŹRÓDŁową maszynę wirtualną.  
1. W przypadku **generacji maszyny wirtualnej** wybierz opcję opartą na ŹRÓDŁOWEJ maszynie wirtualnej. W większości przypadków jest to *generacji 1*. Aby uzyskać więcej informacji, zobacz [Obsługa maszyn wirtualnych 2. generacji](../articles/virtual-machines/generation-2.md).
1. W przypadku **stanu systemu operacyjnego** wybierz opcję w oparciu o ŹRÓDŁową maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [uogólnione i wyspecjalizowane](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. W przypadku **wydawcy** wpisz *polecenie*. 
1. W obszarze **Oferta** wpisz *ofertę*.
1. W przypadku **jednostki SKU** wpisz *mySKU*.
1. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**.
1. Po przejściu definicji obrazu do walidacji wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z zarządzanego obrazu. W tym przykładzie wersja obrazu to *1.0.0* i jest replikowana do centrów danych *zachodnio-środkowe stany USA* i południowo- *środkowe stany USA* . Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* jako element docelowy dla replikacji.

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

Kroki tworzenia wersji obrazu są nieco inne, w zależności od tego, czy źródłem jest obraz uogólniony, czy też migawką wyspecjalizowanej maszyny wirtualnej. 

### <a name="option-generalized"></a>Opcja: uogólnione

1. Na stronie definicji obrazu wybierz pozycję **Dodaj wersję** w górnej części strony.
1. W **obszarze region** wybierz region, w którym przechowywany jest obraz zarządzany. Wersje obrazów muszą zostać utworzone w tym samym regionie, w którym utworzono obraz zarządzany.
1. W obszarze **Nazwa** wpisz *1.0.0*. Nazwa wersji obrazu powinna być zgodna z wersją *główną*. *pomocnicze*. Format *poprawki* przy użyciu liczb całkowitych. 
1. W obszarze **obraz źródłowy** wybierz z listy rozwijanej źródłowy obraz zarządzany.
1. W polu **Wyklucz z najnowszych** pozostaw wartość domyślną *nie*.
1. W polu **Data zakończenia okresu istnienia** wybierz datę z kalendarza, który jest kilka miesięcy w przyszłości.
1. W obszarze **replikacja** pozostaw **domyślną liczbę replik** jako 1. Należy przeprowadzić replikację do regionu źródłowego, więc Pozostaw pierwszą replikę jako domyślną, a następnie wybierz drugi region repliki jako *Wschodnie stany USA*.
1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. Platforma Azure sprawdzi poprawność konfiguracji.
1. Gdy wersja obrazu kończy się walidacją, wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

Replikowanie obrazu do wszystkich regionów docelowych może chwilę potrwać.

### <a name="option-specialized"></a>Opcja: wyspecjalizowane

1. Na stronie definicji obrazu wybierz pozycję **Dodaj wersję** w górnej części strony.
1. W **obszarze region** wybierz region, w którym jest przechowywana dana migawka. Wersje obrazów muszą zostać utworzone w tym samym regionie, w którym utworzono źródło.
1. W obszarze **Nazwa** wpisz *1.0.0*. Nazwa wersji obrazu powinna być zgodna z wersją *główną*. *pomocnicze*. Format *poprawki* przy użyciu liczb całkowitych. 
1. W obszarze **migawka dysku systemu operacyjnego** wybierz migawkę ze ŹRÓDŁOWEJ maszyny wirtualnej z listy rozwijanej. Jeśli źródłowa maszyna wirtualna ma dysk danych, który chcesz dołączyć, wybierz z listy rozwijanej odpowiedni numer **LUN** , a następnie wybierz migawkę dysku danych dla **migawki dysku danych**. 
1. W polu **Wyklucz z najnowszych** pozostaw wartość domyślną *nie*.
1. W polu **Data zakończenia okresu istnienia** wybierz datę z kalendarza, który jest kilka miesięcy w przyszłości.
1. W obszarze **replikacja** pozostaw **domyślną liczbę replik** jako 1. Należy przeprowadzić replikację do regionu źródłowego, więc Pozostaw pierwszą replikę jako domyślną, a następnie wybierz drugi region repliki jako *Wschodnie stany USA*.
1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. Platforma Azure sprawdzi poprawność konfiguracji.
1. Gdy wersja obrazu kończy się walidacją, wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy Udostępnianie dostępu na poziomie galerii obrazów. Poniżej przedstawiono udostępnianie galerii, która została właśnie utworzona.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **grupy zasobów**. 
1. Na liście grup zasobów wybierz pozycję **myGalleryRG**. Zostanie otwarty blok dla grupy zasobów.
1. W menu po lewej stronie **myGalleryRG** wybierz pozycję **Kontrola dostępu (IAM)**. 
1. W obszarze **Dodaj przypisanie roli** wybierz pozycję **Dodaj**. Zostanie otwarte okienko **Dodawanie przypisania roli** . 
1. W obszarze **rola** wybierz pozycję **czytelnik**.
1. W obszarze **Przypisz dostęp do**, Pozostaw domyślną pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. W obszarze **Wybierz** wpisz adres e-mail osoby, którą chcesz zaprosić.
1. Jeśli użytkownik znajduje się poza organizacją, zobaczysz komunikat, **że użytkownik otrzyma wiadomość e-mail, która umożliwi im współpracę z firmą Microsoft.** Wybierz użytkownika z adresem e-mail, a następnie kliknij przycisk **Zapisz**.

Jeśli użytkownik jest spoza organizacji, otrzyma wiadomość e-mail z zaproszeniem do dołączenia do organizacji. Użytkownik musi zaakceptować zaproszenie, a następnie będzie mógł zobaczyć galerię oraz wszystkie definicje i wersje obrazu na liście zasobów.