---
title: 'Samouczek: tworzenie rekordu aliasu do obsługi nazw wierzchołków domeny — Traffic Manager'
titleSuffix: Azure DNS
description: W tym samouczku przedstawiono sposób konfigurowania rekordu aliasu usługi Azure DNS do obsługi nazwy wierzchołka domeny przy użyciu usługi Traffic Manager.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: e0101133c68142845a8ada50d9921d341cf10ad0
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738796"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager 

Można utworzyć rekord aliasu wierzchołka nazwy domeny, aby odwoływać się do profilu usługi Azure Traffic Manager. Przykładowa domena to contoso.com. Zamiast używać usługi przekierowania, skonfiguruj usługę Azure DNS, aby odwoływać się do profilu usługi Traffic Manager bezpośrednio z poziomu strefy. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej hosta i infrastruktury sieciowej.
> * Tworzenie profilu usługi Traffic Manager.
> * Tworzenie rekordu aliasu.
> * Testowanie rekordu aliasu.

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące sposobu hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale skorzystaj z własnej nazwy domeny.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieci

Najpierw utwórz sieć wirtualną i podsieć, aby umieścić w nich serwery internetowe.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź *grupę zasobów*, a następnie utwórz grupę zasobów o nazwie **RG-DNS-Alias-TM**.
3. Wybierz **pozycję Utwórz zasób**  >  **Sieć**  >  **wirtualna sieci**.
4. Utwórz sieć wirtualną o nazwie **VNet-Servers**. Umieść ją w grupie zasobów **RG-DNS-Alias-TM**, a następnie nadaj podsieci nazwę **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych serwera wirtualnego

1. Wybierz **pozycję Utwórz zasób maszyny**  >  **wirtualnej z systemem Windows Server 2016.**
2. Wprowadź nazwę **Web-01** i umieść maszynę wirtualną w grupie zasobów **RG-DNS-Alias-TM**. Wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **OK**.
3. W obszarze **Rozmiar** wybierz jednostkę magazynową zawierającą 8 GB pamięci RAM.
4. W obszarze **Ustawienia** wybierz sieć wirtualną **VNet-Servers** i podsieć **SN-Web**.
5. Wybierz pozycję **Publiczny adres IP**. W obszarze **Przypisanie** wybierz pozycję **Statyczne**, a następnie wybierz pozycję **OK**.
6. W przypadku publicznych portów przychodzących wybierz pozycję **HTTP (80)**  >  **HTTPS (443)**  >  **RDP (3389),** a następnie wybierz przycisk **OK.**
7. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**. Wykonanie tej procedury trwa kilka minut.

Powtórz tę procedurę, aby utworzyć inną maszynę wirtualną o nazwie **Web-02**.

### <a name="add-a-dns-label"></a>Dodawanie etykiety DNS

Publiczne adresy IP potrzebują etykiet DNS do pracy z usługą Traffic Manager.
1. W grupie zasobów **RG-DNS-Alias-TM** wybierz publiczny adres IP **Web-01-ip**.
2. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja**.
3. W polu tekstowym etykiety nazwy DNS wprowadź **web01pip**.
4. Wybierz pozycję **Zapisz**.

Powtórz tę procedurę dla publicznego adresu IP **Web-02-ip**, używając ciągu **web02pip** jako etykiety nazwy DNS.

### <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj usługi IIS na maszynach **Web-01** i **Web-02**.

1. Połącz się **z siecią Web-01** i zaloguj się.
2. Na pulpicie nawigacyjnym **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
3. Trzykrotnie wybierz pozycję **Dalej**. Na stronie **Role serwera** wybierz pozycję **Serwer sieci Web (IIS)**.
4. Wybierz pozycję **Dodaj funkcje**, a następnie wybierz pozycję **Dalej**.
5. Czterokrotnie wybierz pozycję **Dalej**. Następnie wybierz pozycję **Zainstaluj**. Wykonanie tej procedury trwa kilka minut.
6. Po zakończeniu instalacji wybierz pozycję **Zamknij**.
7. Otwórz przeglądarkę internetową. Przejdź do lokalizacji **localhost**, aby sprawdzić, czy zostanie wyświetlona domyślna strona internetowa usług IIS.

Powtórz tę procedurę, aby zainstalować usługi IIS na maszynie wirtualnej **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1. Otwórz grupę zasobów **RG-DNS-Alias-TM**, a następnie wybierz publiczny adres IP **Web-01-ip**. Zanotuj adres IP do późniejszego użycia. Powtórz ten krok dla publicznego adresu IP **Web-02-ip**.
1. Wybierz **pozycję Utwórz zasób**  >  **Sieć**  >  **Traffic Manager profilu**.
2. Jako nazwę wprowadź ciąg **TM-alias-test**. Umieść ją w grupie zasobów **RG-DNS-Alias-TM**.
3. Wybierz przycisk **Utwórz**.
4. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**.
5. Na stronie Profil usługi Traffic Manager w obszarze **Ustawienia** wybierz pozycję **Punkty końcowe**.
6. Wybierz pozycję **Dodaj**.
7. W obszarze **Typ** wybierz pozycję **Zewnętrzny punkt końcowy**, a w polu **Nazwa** wprowadź **EP-Web01**.
8. W polu tekstowym **W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP** wprowadź zanotowany wcześniej adres IP **Web-01-ip**.
9. Wybierz tę samą **lokalizację** co w przypadku innych zasobów, a następnie wybierz pozycję **OK**.

Powtórz tę procedurę, aby dodać punkt końcowy **Web-02**, używając zanotowanego wcześniej adresu IP **Web-02-ip**.

## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu wskazujący na profil usługi Traffic Manager.

1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. Pozostaw puste pole tekstowe **Nazwa**, aby reprezentować wierzchołek nazwy domeny. Przykładowa domena to contoso.com.
4. Pozostaw pole **Typ** ustawione na rekord **A**.
5. Zaznacz pole wyboru **Zestaw rekordów aliasów**.
6. Wybierz pozycję **Wybierz usługę platformy Azure** i wybierz profil usługi Traffic Manager **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. Z poziomu przeglądarki internetowej przejdź do wierzchołka nazwy domeny. Przykładowa domena to contoso.com. Zostanie wyświetlona domyślna strona internetowa usług IIS. Zamknij przeglądarkę internetową.
2. Zamknij maszynę wirtualną **Web-01**. Zaczekaj kilka minut na jej całkowite zamknięcie.
3. Otwórz nową przeglądarkę internetową i przejdź ponownie do wierzchołka nazwy domeny.
4. Ponownie zostanie wyświetlona domyślna strona internetowa usług IIS, ponieważ usługa Traffic Manager obsługiwała tę sytuację i kierowała ruch do maszyny wirtualnej **Web-02**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, usuń grupę zasobów **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony rekord aliasu umożliwiający użycie nazwy domeny wierzchołkowej do odwoływania się do profilu usługi Traffic Manager. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Host load-balanced web apps at the zone apex (Hostowanie aplikacji internetowych ze zrównoważonym obciążeniem w wierzchołku strefy)](./dns-alias-appservice.md)
