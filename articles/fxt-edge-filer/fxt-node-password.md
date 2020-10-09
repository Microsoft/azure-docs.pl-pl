---
title: 'Samouczek: Inicjowanie sprzętu — usługa Azure FXT Edge'
description: Dowiedz się, jak nawiązać połączenie z węzłem sprzętowym i ustawić początkowe hasło w węzłach usługi Azure FXT Edge.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4fae7795cdd61e3eb465285163bcd6c0dd9f7db2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184928"
---
# <a name="tutorial-set-hardware-passwords"></a>Samouczek: Ustawianie haseł sprzętowych

Przy pierwszym włączeniu węzła usługi Azure FXT Edge należy ustawić hasło główne. Węzły sprzętu nie są dostarczane z domyślnym hasłem. 

Porty sieciowe są wyłączone do momentu ustawienia hasła, a użytkownik główny zaloguje się.

Wykonaj ten krok po zainstalowaniu i okablowaniu węzła, ale przed podjęciem próby utworzenia klastra. 

W tym samouczku wyjaśniono, jak nawiązać połączenie z węzłem sprzętowym i ustawić hasło. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Podłącz klawiaturę i monitoruj do węzła i włącz go
> * Ustawianie haseł dla portu iDRAC i użytkownika root w tym węźle
> * Zaloguj się jako katalog główny 

Powtórz te kroki dla każdego węzła, który będzie używany w klastrze. 

Ukończenie tego samouczka trwa około 15 minut. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj następujące czynności: 

* [Zainstaluj](fxt-install.md) każdy węzeł usługi Azure FXT Edge w stojaku sprzętowym i Dołącz kable zasilające i dostęp do sieci zgodnie z opisem w [poprzednim samouczku](fxt-network-power.md). 
* Znajdź klawiaturę połączoną z USB i monitor połączony ze standardem VGA, który można dołączyć do węzłów sprzętu. (Port szeregowy węzła jest nieaktywny przed ustawieniem hasła).

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Łączenie klawiatury i monitora z węzłem

Fizycznie Podłącz monitor i klawiaturę do węzła usługi Azure FXT Edge. 

* Podłącz monitor do portu VGA.
* Podłącz klawiaturę do jednego z portów USB. 

Ten diagram referencyjny służy do lokalizowania portów z tyłu obudowy. 

> [!NOTE]
> Port szeregowy jest nieaktywny do momentu ustawienia hasła. 

![Diagram zaplecza usługi Azure FXT Edge z portami seryjnymi, VGA i USB oznaczonymi jako](media/fxt-back-serial-vga-usb.png)

Przełącznika KVM można użyć, jeśli chcesz połączyć więcej niż jeden węzeł z tymi samymi urządzeniami peryferyjnymi. 

Włącz w węźle, naciskając przycisk z góry. 

![Diagram frontonu usługi Azure FXT Edge — okrągły przycisk z prawej strony jest oznaczony w prawym górnym rogu](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Ustaw hasła początkowe 

Węzeł pliku usługi Azure FXT Edge będzie drukował różne komunikaty do monitora podczas rozruchu. Po kilku chwilach zostanie wyświetlony ekran wstępnej konfiguracji w następujący sposób:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Wprowadzone hasło jest używane dla dwóch rzeczy: 

* Jest to tymczasowe hasło główne dla tego węzła usługi Azure FXT Edge. 

  To hasło zostanie zmienione podczas tworzenia klastra przy użyciu tego węzła lub po dodaniu tego węzła do klastra. Hasło zarządzania klastrami (skojarzone z użytkownikiem ``admin`` ) to również hasło główne dla wszystkich węzłów w klastrze.

* Jest to hasło długoterminowe dla portu zarządzania sprzętem iDRAC/IPMI.

  Pamiętaj, aby zapamiętać hasło na wypadek, gdyby trzeba było zalogować się przy użyciu interfejsu IPMI później w celu rozwiązania problemu z sprzętem.

Wprowadź i Potwierdź hasło: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po wprowadzeniu hasła system kontynuuje rozruch. Po zakończeniu zostanie ``login:`` wyświetlony monit. 

## <a name="sign-in-as-root"></a>Zaloguj się jako katalog główny

Zaloguj się jako ``root`` przy użyciu hasła, które właśnie ustawisz. 

```
login: root
Password:**********
```

Gdy zalogujesz się jako użytkownik główny, porty sieciowe są aktywne i skontaktują się z serwerem DHCP w celu uzyskania adresów IP. 

## <a name="next-steps"></a>Następne kroki

Węzeł jest gotowy do klastra. Za jego pomocą można utworzyć klaster usługi Azure FXT Edge lub [dodać go do istniejącego klastra](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Tworzenie klastra](fxt-cluster-create.md)
