---
title: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
description: W tym artykule wyjaśniono, jak połączyć się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772842"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku platformy Azure. Korzystając z prywatnego punktu końcowego, możesz połączyć się prywatnie z aplikacją sieci Web. W tym artykule dowiesz się, jak wdrożyć aplikację sieci Web przy użyciu prywatnego punktu końcowego, a następnie połączyć się z aplikacją internetową z maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Używanie prywatnych punktów końcowych dla aplikacji internetowej platformy Azure][privateendpointwebapp].

> [!Note]
> Prywatny punkt końcowy jest dostępny w regionach publicznych dla PremiumV2 warstwy PremiumV3 aplikacji sieci Web systemu Windows, aplikacji sieci Web w systemie Linux oraz planu Azure Functions Premium (czasami określanego jako elastyczny plan Premium). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Przed rozpoczęciem Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Tworzenie sieci wirtualnej i maszyny wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która będzie używana w celu uzyskania dostępu do aplikacji sieci Web za pomocą prywatnego punktu końcowego.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Aby utworzyć sieć wirtualną i podsieć, wykonaj następujące czynności:

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **sieci wirtualnej**.

1. W okienku **Tworzenie sieci wirtualnej** wybierz kartę **podstawowe** , a następnie wprowadź informacje, które są wyświetlane w tym miejscu:

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okienko "Tworzenie Virtual Network" w Azure Portal.][1]

1. Wybierz kartę **adresy IP** , a następnie wprowadź informacje, które są wyświetlane w tym miejscu:

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający kartę "adresy IP" w okienku tworzenie sieci wirtualnej.][2]

1. W sekcji **podsieć** wybierz pozycję **Dodaj podsieć**, wprowadź informacje, które są wyświetlane w tym miejscu, a następnie wybierz pozycję **Dodaj**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okienko "Dodawanie podsieci".][3]

1. Wybierz pozycję **Przeglądanie + tworzenie**.

1. Po pomyślnej weryfikacji wybierz pozycję **Utwórz**.

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną, wykonaj następujące czynności:

1. W Azure Portal w okienku po lewej stronie wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**.

1. W okienku **Utwórz maszynę wirtualną** wprowadź informacje, które są wyświetlane w tym miejscu:

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okienko "Tworzenie maszyny wirtualnej".][4]

1. Wybierz pozycję **Dalej: dyski**.

1. W okienku **dyski** Zachowaj ustawienia domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W okienku **Sieć** wprowadź informacje, które są wyświetlane w tym miejscu:

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający kartę "Sieć" w okienku "Tworzenie maszyny wirtualnej".][5]

1. Wybierz pozycję **Przeglądanie + tworzenie**.

1. Po pomyślnej weryfikacji wybierz pozycję **Utwórz**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Tworzenie aplikacji sieci Web i prywatnego punktu końcowego

W tej sekcji utworzysz prywatną aplikację sieci Web, która używa prywatnego punktu końcowego.

> [!Note]
> Funkcja prywatnego punktu końcowego jest dostępna tylko dla warstwy PremiumV2 i PremiumV3.

### <a name="create-the-web-app"></a>Tworzenie aplikacji internetowej

1. W Azure Portal w okienku po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Sieć**Web Web  >  **App**.

1. W okienku **aplikacja sieci Web** wybierz kartę **podstawowe** , a następnie wprowadź informacje, które są wyświetlane w tym miejscu:

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający kartę "podstawowe" w okienku "aplikacja sieci Web".][6]

1. Wybierz pozycję **Przeglądanie + tworzenie**.

1. Po pomyślnej weryfikacji wybierz pozycję **Utwórz**.

### <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

1. W oknie właściwości aplikacji sieci Web w obszarze **Ustawienia**wybierz pozycję **Sieć**, a następnie w obszarze * * połączenia prywatnych punktów końcowych * * wybierz pozycję **Konfiguruj połączenia prywatnych punktów końcowych**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający link "Konfigurowanie połączeń prywatnych punktów końcowych" w okienku sieć aplikacji sieci Web.][7]

1. W kreatorze **połączeń prywatnych punktów końcowych** wybierz pozycję **Dodaj**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający przycisk Dodaj w Kreatorze "połączenia prywatne punktów końcowych".][8]

1. Wybierz odpowiednie informacje z listy rozwijanej **subskrypcja**, **Sieć wirtualna**i **podsieć** , a następnie wybierz przycisk **OK**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okienko "Dodawanie prywatnego punktu końcowego".][9]

1. Monitoruj postęp tworzenia prywatnego punktu końcowego.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający postęp dodawania prywatnego punktu końcowego. ][10]
   >  ![ Zrzut ekranu przedstawiający nowo utworzony prywatny punkt końcowy.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

1. W polu **wyszukiwania** Azure Portal wprowadź **myVm**.
1. Wybierz pozycję **Połącz**, a następnie wybierz pozycję **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający przycisk "RDP" w okienku "myVM".][12]

1. W okienku **Połącz z RDP** wybierz pozycję **Pobierz plik RDP**.  

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający przycisk "Pobierz plik RDP" w okienku "łączenie z protokołem RDP".][13]

   Platforma Azure tworzy plik Remote Desktop Protocol (RDP) i pobiera go na komputer.   

1. Otwórz pobrany plik RDP.

   a. W wierszu polecenia wybierz pozycję **Połącz**.  
   b. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

     > [!Note]
     > Aby użyć tych poświadczeń, może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**.

1. Wybierz przycisk **OK**.

   > [!Note]
   > Jeśli podczas procesu logowania otrzymasz ostrzeżenie o certyfikacie, wybierz pozycję **tak** lub **Kontynuuj**.

1. Po wyświetleniu okna pulpitu maszyny wirtualnej Zminimalizuj go, aby wrócić do pulpitu lokalnego.

## <a name="access-the-web-app-privately-from-the-vm"></a>Uzyskaj dostęp do aplikacji internetowej prywatnie z maszyny wirtualnej

W tej sekcji połączysz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego.

1. Aby uzyskać prywatny adres IP prywatnego punktu końcowego, w polu **wyszukiwania** wpisz **link prywatny** , a następnie na liście wyników wybierz pozycję **link prywatny**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający link "Private link" na liście wyników wyszukiwania.][14]

1. W obszarze prywatne centrum połączeń w okienku po lewej stronie wybierz pozycję **prywatne punkty końcowe** , aby wyświetlić prywatne punkty końcowe.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający listę prywatnych punktów końcowych w prywatnym centrum linków.][15]

1. Wybierz prywatny punkt końcowy, który łączy się z Twoją aplikacją internetową i podsiecią.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okienko właściwości prywatnego punktu końcowego.][16]

1. Skopiuj prywatny adres IP prywatnego punktu końcowego oraz w pełni kwalifikowaną nazwę domeny (FQDN) aplikacji sieci Web. W poprzednim przykładzie prywatny identyfikator to *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. W okienku **myVM** upewnij się, że aplikacja sieci Web jest niedostępna za pomocą publicznego adresu IP. Aby to zrobić, Otwórz przeglądarkę i wklej nazwę aplikacji sieci Web. Na stronie powinna zostać wyświetlona wiadomość "Błąd 403-Zabronione".

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający stronę błędu "Błąd 403 — Dostęp zabroniony".][17]

   W przypadku systemu DNS wykonaj jedną z następujących czynności:
 
   - Użyj usługi strefy prywatnej Azure DNS.  

     a. Utwórz strefę prywatną DNS o nazwie *`privatelink.azurewebsites.net`* , a następnie połącz ją z siecią wirtualną.  
     b. Utwórz dwa rekordy A (czyli nazwę aplikacji i menedżera kontroli usług [SCM]) przy użyciu adresu IP prywatnego punktu końcowego.  
     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu rekordów strefy prywatnej DNS.][21]  

   - Użyj pliku *hosts* na maszynie wirtualnej.  

     a. Utwórz wpis hosty, otwórz Eksploratora plików i Wyszukaj plik *hosts* .  
     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający plik hosts w Eksploratorze plików.][18]  
     b. Dodaj wpis zawierający prywatny adres IP i publiczną nazwę aplikacji sieci Web, edytując plik *hosts* w edytorze tekstu.  
     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający tekst pliku Hosts.][19]  
     c. Zapisz plik.

1. W przeglądarce wpisz adres URL aplikacji sieci Web.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający przeglądarkę wyświetlającą aplikację sieci Web.][20]

Uzyskujesz dostęp do aplikacji sieci Web za pomocą prywatnego punktu końcowego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z prywatnego punktu końcowego, aplikacji sieci Web i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby.

1. W Azure Portal w polu **wyszukiwania** wprowadź wartość **readed-RG**, a następnie na liście wyników wybierz pozycję **readed-RG** .

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **wpisz nazwę grupy zasobów**, wprowadź wartość **Read-RG**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono MASZYNę wirtualną w sieci wirtualnej, aplikacji sieci Web i prywatnym punkcie końcowym. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z aplikacją internetową za pomocą linku prywatnego. 

Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
