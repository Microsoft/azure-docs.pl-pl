---
title: Wdrażanie programu Micro Focus Enterprise Server 5,0 do AKS | Microsoft Docs
description: Przehostaj obciążenia systemu mainframe firmy IBM z/OS przy użyciu środowiska deweloperskiego i testowego na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 9e5b3857c2252a939080206fb1f92cc422f326fc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564360"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Wdrażanie programu Micro Focus Enterprise Server 5,0 do AKS

W innym [artykule](./run-enterprise-server-container.md)opisano procedurę uruchamiania programu Micro Focus Enterprise Server 5,0 w kontenerze platformy Docker. Jak widać w tym celu, chcę pokazać, jak to zrobić jeszcze jeden krok, i wdrożyć ten obraz platformy Docker utworzony w usłudze Azure Kubernetes Service (AKS).

Usługa Azure Kubernetes to zarządzana usługa Orchestration oparta na Kubernetes. Umożliwia wdrażanie, skalowanie i Zarządzanie kontenerami platformy Docker (oraz innymi aplikacjami opartymi na kontenerach) w klastrze hostów kontenerów.

Jest to proces z trzema krokami. Należy:

1.  Utwórz Azure Container Registry do przechowywania obrazu platformy Docker.

2.  Utwórz klaster usługi Azure Kubernetes, aby uruchomić obraz platformy Docker.

3.  Uruchom aplikację.

Pozwala to na skalowanie w poziomie (i skalowanie) obciążeń związanych z modernizacją komputerów mainframe na platformie Azure dzięki prawdziwej obciążeniu platformy chmury.

Gotowe? Zaczynajmy.

## <a name="create-the-azure-container-registry"></a>Tworzenie Azure Container Registry

W Azure Portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu. Na pulpicie nawigacyjnym portalu Marketplace wybierz pozycję **kontenery,** a następnie **Container Registry**. Spowoduje to przejście do okienka **Tworzenie rejestru kontenera** , w którym należy podać **nazwę rejestru**, **subskrypcję platformy Azure**, **grupę zasobów** i **lokalizację**. **Nazwa rejestru** musi zostać rozpoznana, dlatego musi być unikatowa. Wybierz **grupę zasobów** używaną w poprzednim wpisie w blogu i tę samą **lokalizację**. Wybierz opcję **Włącz** dla **użytkownika Administrator** i **podstawowa** dla **jednostki SKU**. Gdy wszystko będzie wypełnione, wybierz pozycję **Utwórz**.

![Utwórz interfejs rejestru kontenerów](media/deploy-image-1.png)

Po wdrożeniu rejestru wybierz pozycję **Przejdź do zasobu**. Spowoduje to przejście do głównego bloku dla Azure Container Registry. W tym miejscu jest dostępna opcja menu **Szybki Start** . Wybierz go i Wyświetl instrukcje dotyczące tego, co należy zrobić, aby wypchnąć i ściągnąć obrazy do i z rejestru. Wykonajmy następujące:

1.  **Zainstaluj platformę Docker** — nie musisz martwić się o to, ponieważ to już zrobione.

2.  **Uruchom ponownie obraz podstawowy "Hello — World"** — nie jest to konieczne, ale możesz go wypróbować.

3.  **Zaloguj się do rejestru kontenerów** — musisz to zrobić z poziomu maszyny wirtualnej. Skopiuj polecenie do Schowka lub Notatnika.

    W przypadku utworzonego rejestru polecenie to: **Docker login acrmf50.azurecr.IO**

4.  **Wypchnij do rejestru** — należy to zrobić dla obrazu mikro-Focus, jak również po zalogowaniu się do maszyny wirtualnej.

5.  **Ściąganie z rejestru** — nie dotyczy tego przewodnika, ale warto wiedzieć, czy trzeba uruchomić inny obraz platformy Docker.

Przed opuszczeniem portalu należy uzyskać poświadczenia dla rejestru, aby móc się zalogować. Zamknij blok **Szybki Start** i wybierz pozycję **klucze dostępu** z menu Rejestr. Skopiuj **nazwę użytkownika** i jedno z **haseł** (dwa) do Schowka lub Notatnika. Będą one potrzebne później do zalogowania się.

Teraz, gdy wiesz już, co należy zrobić, zaloguj się do maszyny wirtualnej.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP z maszyną wirtualną, która została użyta do utworzenia obrazu platformy Docker

Ponieważ obraz platformy Docker został już utworzony na serwerze z systemem Windows 2016, należy zalogować się do tej maszyny wirtualnej. Z tej maszyny wirtualnej można wypchnąć obraz do właśnie utworzonego Azure Container Registry. Przejdź do maszyny wirtualnej w Azure Portal, a następnie wybierz pozycję **Przegląd** , a następnie polecenie **Połącz**. Spowoduje to połączenie z maszyną wirtualną za pośrednictwem protokołu RDP (Remote Desktop Protocol). Musisz użyć poświadczeń z programu podczas tworzenia maszyny wirtualnej.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Zaloguj się i wypchnij obraz do rejestru

Po zalogowaniu Otwórz wiersz polecenia i zainicjuj następujące polecenia platformy Docker:

-   **obrazy platformy Docker** — pokazuje listę wszystkich aktualnie zainstalowanych obrazów na maszynie wirtualnej. Zwróć uwagę na **mikrofokus/es — acctdemo** , ponieważ jest to ten, z którym będziesz pracować.

-   **acrmf50.azurecr.IO logowania platformy Docker** — poprawny format w tym miejscu to *Docker login \<registry name\>*. Zastąp dowolną nazwę, która została użyta podczas tworzenia rejestru.

    -   Wymagana jest **Nazwa użytkownika** i **hasło** skopiowane z Azure Portal. Na ekranie powinna być widoczna zawartość podobna do tej z poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wiersz polecenia administratora](media/deploy-image-2.png)

-   aparat **platformy Docker/es — acctdemo acrmf50.azurecr.IO/es-acctdemo** — ten tag ma odpowiedni obraz o nazwie repozytorium. **Uwaga**: Jeśli nazwa nie \<microfocus/es-acctdemo\> działa, spróbuj użyć pełnego identyfikatora obrazu. Po wykonaniu polecenia wpisz **obrazy platformy Docker — No-TRUNC —**. Powinien zostać wyświetlony komunikat podobny do poniższego. Zauważ, że obraz jest prawidłowo oznakowany.

    ![Ekran z wierszem polecenia administratora](media/deploy-image-3.png)

-   **Docker push acrmf50.azurecr.IO/es-acctdemo** — spowoduje to rozpoczęcie rzeczywistej wypychania do repozytorium. Ponieważ rozmiar wynosi 15 GB, uruchomienie może zająć kilka minut. Jeśli wszystko jest prawidłowe, powinien wyglądać podobnie do poniższej ilustracji.

    ![Ekran wiersza polecenia administratora](media/deploy-image-4.png)

Teraz wróć do Azure Portal, w odwrocie do **repozytorium**. W menu **repozytorium** wybierz pozycję **repozytoria**, a następnie na liście **acctdemo** . Teraz Utwórz klaster AKS.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Tworzenie klastra usługi Azure Kubernetes (AKS)

W Azure Portal wybierz opcję **Utwórz zasób** , a następnie pozycję **kontenery/usługa Kubernetes** z menu **portalu Marketplace** . Następnie musisz wypełnić blok **Tworzenie klastra Kubernetes** . Upewnij się, że klaster znajduje się w tym samym regionie i tej samej grupie zasobów. Pozostałe wartości domyślne można zaakceptować z wyjątkiem **liczby węzłów,** która musi mieć wartość 1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

![Utwórz ekran klastra Kubernetes](media/deploy-image-5.png)

Po zakończeniu wdrożenie umieści artefakty **usługi Kubernetes** w **grupie zasobów** wybranej w bloku. Jednak rzeczywisty klaster będzie miał własną grupę zasobów utworzoną podczas wdrażania. W przypadku wybrania opcji **grupy zasobów** z menu po lewej stronie można znaleźć je na podstawie konwencji nazewnictwa. Oto obraz kopalni — jest to ostatni z listy.

![Zrzut ekranu grup zasobów](media/deploy-image-6.png)

**Uruchamianie obrazu**

Teraz czas na pobranie obrazu i uruchomienie go w AKS. Najprostszym sposobem wykonania tej czynności w Azure Portal jest użycie Cloud Shell. Ikona znajduje się w prawym górnym rogu Azure Portal. Należy pamiętać, że w tym instruktażu używamy powłoki bash.

![Zrzut ekranu przedstawiający ikonę Cloud Shell](media/deploy-image-7.png)

Po załadowaniu powłoki wpisz następujące polecenie:

**polecenia kubectl Run es-acctdemo--Image acrmf50.azurecr.io/es-acctdemo--port = 9040**

Spowoduje to pobranie obrazu z repozytorium **acrmf50.azurecr.IO** i załadowanie go do AKS. Następnie zostanie uruchomiony obraz z otwartym portem 9040. Możesz odwołać, ponieważ port został otwarty dla obrazu platformy Docker. Musisz uzyskać dostęp do serwera przedsiębiorstwa.

Kubernetes powinna odpowiedzieć na komunikat, że wdrożenie zostało utworzone.

![Zrzut ekranu przedstawiający komunikat wdrożenia](media/deploy-image-8.jpg)

Aby sprawdzić, czy kontener jest w rzeczywistości uruchomiona, wpisz: **polecenia kubectl Get** Containers.

Powinieneś zobaczyć es-acctdemo jako uruchomiony pod, jak na poniższej ilustracji.

![Zrzut ekranu es — acctdemo jako uruchomiony](media/deploy-image-9.png)

Gratulacje! Teraz uruchamiasz serwer korporacyjny w usłudze Azure Kubernetes Service. W następnym artykule pokazano, jak uzyskać dostęp do konsoli administracyjnej serwera Enterprise Server, a także jak korzystać z Kubernetes w celu skalowania wdrożenia.
