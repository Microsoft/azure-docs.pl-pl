---
title: Samouczek — Tworzenie aplikacji do analizy w sklepie na platformie Azure IoT Central
description: W tym samouczku pokazano, jak utworzyć aplikację detaliczną analizy w sklepie w IoT Central. Utworzysz go, dołączysz i dodasz urządzenia czujnika.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 50dd6038a8642f13cea7840fff723a5cf12ce2dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81000252"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do analizy w sklepie na platformie Azure IoT Central



W tym samouczku przedstawiono kompilacje rozwiązań, w jaki sposób można utworzyć aplikację analityczną usługi Azure IoT Central w sklepie. Przykładowa aplikacja dotyczy sklepu detalicznego. Jest to rozwiązanie dla typowych potrzeb firmy, które należy monitorować i dostosowywać do warunków użytkowania i środowiska.

Przykładowa aplikacja, którą tworzysz, zawiera trzy rzeczywiste urządzenia: Rigado Kaskada 500 Gateway oraz dwie czujniki RuuviTag. W tym samouczku pokazano również, jak używać czujnika zajętości symulowanej, zawartego w szablonie aplikacji do celów testowych. Brama C500 Rigado służy jako centrum komunikacyjne w aplikacji. Komunikuje się on z czujnikami w sklepie i zarządza swoimi połączeniami z chmurą. RuuviTag to czujnik środowiska, który zapewnia telemetrię, w tym temperaturę, wilgotność i ciśnienie. Czujnik zajętości symulowanych służy do śledzenia ruchu i obecności w obszarach wyewidencjonowania w sklepie. 

Ten samouczek zawiera wskazówki dotyczące łączenia urządzeń z Rigado i RuuviTag z aplikacją. Jeśli masz inną bramę i czujniki, nadal możesz wykonać kroki w celu skompilowania aplikacji. W tym samouczku przedstawiono również sposób tworzenia symulowanych czujników RuuviTag. Symulowane czujniki umożliwiają kompilowanie aplikacji, jeśli nie masz rzeczywistych urządzeń. 

Rozwiązanie do tworzenia i monitorowania stanu jest opracowywane w trzech częściach:

* Tworzenie aplikacji i łączenie urządzeń w celu monitorowania warunków
* Dostosuj pulpit nawigacyjny, aby umożliwić operatorom monitorowanie urządzeń i zarządzanie nimi
* Konfigurowanie eksportu danych w celu umożliwienia menedżerom sklepu uruchamiania analiz i wizualizacji szczegółowych informacji

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji dla sklepu detalicznego za pomocą szablonu usługi Azure IoT Central **in-Store Analytics — wyewidencjonowywanie**
> * Dostosowywanie ustawień aplikacji
> * Tworzenie i dostosowywanie szablonów urządzeń IoT
> * Łączenie urządzeń z aplikacją
> * Dodawanie reguł i akcji do monitorowania warunków

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę serię samouczków, potrzebne są:
* Zalecana jest subskrypcja platformy Azure. Opcjonalnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).
* Dostęp do urządzenia bramy i dwóch czujników środowiska (można opcjonalnie użyć symulowanych urządzeń zgodnie z opisem w samouczku)
* Szablony urządzeń dla używanych urządzeń (szablony są udostępniane wszystkim urządzeniom używanym w samouczku)

## <a name="create-an-application"></a>Tworzenie aplikacji
W tej sekcji utworzysz nową aplikację usługi Azure IoT Central na podstawie szablonu. Ta aplikacja zostanie użyta w całej serii samouczków, aby skompilować kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie zaloguj się przy pomocy konto Microsoft:

    ![Określanie konta organizacji](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

1. Wybierz pozycję **sprzedaż detaliczna**.  Na stronie detalicznej są wyświetlane kilka szablonów aplikacji sieci Web.

Aby utworzyć nową aplikację do wyewidencjonowania analizy w sklepie:  

1. Wybierz szablon aplikacja **do analizy w sklepie — wyewidencjonowywanie** . Ten szablon zawiera szablony urządzeń dla wszystkich urządzeń używanych w samouczku z wyjątkiem czujników RuuviTag. Szablon zawiera również pulpit nawigacyjny operatora służący do monitorowania wyewidencjonowywania i warunków środowiska oraz stanu urządzenia. 

1. Opcjonalnie możesz wybrać przyjazną **nazwę aplikacji**. Ta aplikacja jest oparta na fikcyjnym sklepie detalicznym o nazwie contoso. Samouczek używa **nazwy aplikacji** firmy *contoso do wyewidencjonowania*. Szablon aplikacji jest oparty na fikcyjnej firmie Northwind. W tym samouczku użyjesz firmy Contoso, aby dowiedzieć się, jak dostosować aplikację.

    > [!NOTE]
    > Jeśli używasz przyjaznej **nazwy aplikacji**, nadal musisz użyć unikatowej wartości dla **adresu URL**aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wprowadź swój *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../core/quick-deploy-iot-central.md).

1. Wybierz przycisk **Utwórz**.

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central tworzenie informacji dotyczących rozliczeń aplikacji](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Dostosuj ustawienia aplikacji

Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji. W tej sekcji wybierzesz motyw wstępnie zdefiniowanej aplikacji. Opcjonalnie dowiesz się, jak utworzyć motyw niestandardowy i zaktualizować obraz aplikacji. Motyw niestandardowy pozwala ustawić kolory przeglądarki aplikacji, ikonę przeglądarki i logo aplikacji, które pojawiają się w czasopismu.

Aby wybrać wstępnie zdefiniowaną kompozycję aplikacji:

1. Wybierz pozycję **Ustawienia** w obszarze tytuł.

    ![Ustawienia aplikacji IoT Central platformy Azure](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Wybierz nowy **motyw**.

3. Wybierz pozycję **Zapisz**.

Zamiast korzystać ze wstępnie zdefiniowanego motywu, można utworzyć niestandardowy motyw. Jeśli chcesz użyć zestawu przykładowych obrazów do dostosowania aplikacji i dokończyć ten samouczek, Pobierz [przykładowe obrazy](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)z firmy Contoso.

Aby utworzyć motyw niestandardowy:

1. Rozwiń okienko po lewej stronie, jeśli nie zostało jeszcze rozwinięte.

    ![Lewe okienko IoT Central platformy Azure](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Wybierz pozycję **administracja > Dostosuj aplikację**.

1. Użyj przycisku **Zmień** , aby wybrać obraz do przekazania jako **logo aplikacji**. Opcjonalnie określ wartość dla **tekstu zastępczego logo**. 

1. Użyj przycisku **Zmień** , aby wybrać obraz **ikony przeglądarki** , który będzie wyświetlany na kartach przeglądarki.

1. Opcjonalnie można zastąpić domyślne **kolory przeglądarki** , dodając szesnastkowe kody kolorów html. Dla **nagłówka**Dodaj *#008575*.  Na potrzeby **akcentu**Dodaj *#A1F3EA*. 

1. Wybierz pozycję **Zapisz**. 

    ![Niestandardowe logo IoT Central platformy Azure](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Po zapisaniu aplikacja aktualizuje kolory przeglądarki, logo w czasopismu i ikonę przeglądarki. 

    ![IoT Central zaktualizowane ustawienia aplikacji platformy Azure](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Aby zaktualizować obraz aplikacji:

1. Wybierz pozycję **administracja > ustawienia aplikacji**.

1. Użyj przycisku **Wybierz obraz** , aby wybrać obraz do przekazania jako obraz aplikacji. Ten obraz zostanie wyświetlony na kafelku aplikacji na stronie **Moje aplikacje** w programie IoT Central Application Manager.

1. Wybierz pozycję **Zapisz**.

1. Opcjonalnie przejdź do widoku **Moje aplikacje** w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Na kafelku aplikacji jest wyświetlany zaktualizowany obraz aplikacji.

    ![IoT Central dostosowywanie obrazu aplikacji platformy Azure](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Tworzenie szablonów urządzeń
Jako Konstruktor można utworzyć szablony urządzeń, które umożliwiają operatorom aplikacji Konfigurowanie urządzeń i zarządzanie nimi. Szablon można utworzyć, tworząc niestandardowy plik szablonu lub importując szablon z wykazu urządzeń usługi Azure IoT. Po utworzeniu i dostosowaniu szablonu urządzenia należy go używać do łączenia rzeczywistych urządzeń z aplikacją. Opcjonalnie można użyć szablonu urządzenia do generowania symulowanych urządzeń do testowania.

Szablon aplikacji do **analizy w sklepie** zawiera szablony urządzeń dla kilku urządzeń.  Istnieją szablony urządzeń dla dwóch z trzech urządzeń, które są używane w aplikacji. Szablon urządzenia RuuviTag nie jest uwzględniony w szablonie aplikacji **do analizy w sklepie** . W tej sekcji dodasz szablon urządzenia dla czujników RuuviTag do aplikacji.

Aby dodać szablon urządzenia RuuviTag do aplikacji:

1. W lewym okienku wybierz pozycję **Szablony urządzeń** .

1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia.

1. Znajdź i wybierz szablon urządzenia czujnika **RuuviTag** w katalogu urządzeń Azure IoT. 

1. Wybierz pozycję **Dalej: Dostosuj**.

    ![Szablon urządzenia czujnika usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Wybierz przycisk **Utwórz**. Aplikacja dodaje szablon urządzenia RuuviTag.

1. W okienku po lewej stronie wybierz pozycję **Szablony urządzeń** . Na stronie zostaną wyświetlone wszystkie szablony urządzeń znajdujące się w szablonie aplikacji oraz właśnie dodany szablon urządzenia RuuviTag.

    ![Szablon urządzenia czujnika usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Dostosowywanie szablonów urządzeń
Szablony urządzeń można dostosować w aplikacji na trzy sposoby. Najpierw można dostosować natywne interfejsy wbudowane na urządzeniach, zmieniając możliwości urządzeń. Na przykład przy użyciu czujnika temperatury można zmienić szczegóły, takie jak nazwa wyświetlana interfejsu temperatury, typ danych, jednostki miary i minimalne i maksymalne zakresy operacyjne. 

Następnie dostosuj szablony urządzeń, dodając właściwości chmury. Właściwości chmury nie są częścią wbudowanych możliwości urządzeń. Właściwości chmury to dane niestandardowe, które aplikacja IoT Central platformy Azure tworzy, przechowuje i kojarzy z urządzeniami. Przykładem właściwości chmury może być wartość obliczona lub metadane, takie jak lokalizacja, która ma zostać skojarzona z zestawem urządzeń. 

Po trzecie Dostosuj szablony urządzeń, tworząc widoki niestandardowe. Widoki umożliwiają operatorom wizualizację danych telemetrycznych i metadanych dla urządzeń, takich jak metryki i kondycja urządzenia.

W tym miejscu są używane dwie pierwsze metody dostosowywania szablonu urządzenia do czujników RuuviTag. Aby uzyskać informacje na temat tworzenia widoków dla czujników, zobacz temat [Dodawanie symulowanego urządzenia do aplikacji do IoT Central](../core/quick-create-simulated-device.md) — Szybki Start.

Aby dostosować wbudowane interfejsy szablonu urządzenia RuuviTag:

1. W lewym okienku wybierz pozycję **Szablony urządzeń** . 

1. Wybierz szablon czujników RuuviTag. 

1. Ukryj okienko po lewej stronie. Widok podsumowania szablonu zawiera możliwości urządzenia.

    ![Widok podsumowania szablonu urządzenia usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Wybierz pozycję **Dostosuj** w menu szablonu urządzenia RuuviTag. 

1. Przewiń listę możliwości i Znajdź `humidity` Typ telemetrii. Jest to element wiersza z edytowalną **nazwą wyświetlaną** *wilgotności*.

W poniższych krokach dostosowujesz `humidity` Typ telemetrii dla czujników RuuviTag. Opcjonalnie można dostosować niektóre inne typy telemetrii.

`humidity`Wprowadź następujące zmiany w polu Typ telemetrii:

1. Wybierz kontrolkę **Rozwiń** , aby rozwinąć szczegóły schematu dla wiersza.

1. Zaktualizuj wartość **nazwy wyświetlanej** z *wilgotności* na wartość niestandardową, taką jak *wilgotność względna*.

1. Zmień opcję **typu semantycznego** z *Brak* na *wilgotność*.  Opcjonalnie Ustaw wartości schematu dla typu telemetrii wilgotności w widoku rozwiniętych schematów. Ustawienia schematu umożliwiają tworzenie szczegółowych wymagań dotyczących sprawdzania poprawności danych śledzonych przez czujniki. Można na przykład ustawić minimalną i maksymalną wartość zakresu operacyjnego dla danego interfejsu.

1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

    ![Dostosowywanie szablonu urządzenia usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Aby dodać właściwość chmury do szablonu urządzenia w aplikacji:

1. Wybierz pozycję **właściwości chmury** w menu szablonu urządzenia RuuviTag.

1. Wybierz pozycję **Dodaj właściwość chmury**. 

Określ następujące wartości, aby utworzyć niestandardową właściwość do przechowywania lokalizacji poszczególnych urządzeń:

1. Wprowadź *lokalizację* wartości dla **nazwy wyświetlanej**. Ta wartość jest automatycznie kopiowana do pola **Nazwa** , która jest przyjazną nazwą właściwości. Możesz użyć skopiowanej wartości lub zmienić ją.

1. Wybierz pozycję *ciąg* na liście rozwijanej **schematu** . Typ ciągu umożliwia skojarzenie ciągu nazwy lokalizacji z dowolnym urządzeniem na podstawie szablonu. Na przykład możesz skojarzyć obszar w sklepie z każdym urządzeniem. Opcjonalnie można ustawić **Typ semantyczny** właściwości na *Lokalizacja*, a to ustawienie automatycznie ustawia **schemat** na wartość *geopunkt*. Pozwala on skojarzyć współrzędne GPS z urządzeniem. 

1. Ustaw **minimalną długość** na *2*. 

1. Ustaw **biały znak przycinania** **na wartość włączone**.

1. Wybierz pozycję **Zapisz** , aby zapisać niestandardową Właściwość chmury.

    ![Dostosowywanie szablonu urządzenia usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Kliknij pozycję **Opublikuj**. 

    Opublikowanie szablonu urządzenia sprawia, że jest on widoczny dla operatorów aplikacji. Po opublikowaniu szablonu należy go użyć do wygenerowania symulowanych urządzeń do testowania lub połączenia rzeczywistych urządzeń z aplikacją. Jeśli masz już urządzenia podłączone do aplikacji, opublikowanie niestandardowego szablonu powoduje wypchnięcie zmian na urządzeniach.

## <a name="add-devices"></a>Dodawanie urządzeń
Po utworzeniu i dostosowanym szablonie urządzeń czas dodawania urządzeń. 

W tym samouczku do kompilowania aplikacji należy użyć następującego zestawu rzeczywistych i symulowanych urządzeń:
- Rzeczywista Brama C500 Rigado
- Dwie prawdziwe czujniki RuuviTag
- Czujnik **zajętości** symulowanej. Czujnik symulowany jest zawarty w szablonie aplikacji, więc nie trzeba go tworzyć. 

> [!NOTE]
> Jeśli nie masz prawdziwych urządzeń, możesz nadal wykonać ten samouczek, tworząc symulowane czujniki RuuviTag. Poniższe wskazówki obejmują procedurę tworzenia symulowanych RuuviTag. Nie musisz tworzyć symulowanej bramy.

Wykonaj kroki opisane w poniższych dwóch artykułach, aby połączyć prawdziwą bramę Rigado i czujniki RuuviTag. Po zakończeniu Wróć do tego samouczka. Ponieważ szablony urządzeń zostały już utworzone w tym samouczku, nie trzeba ich utworzyć ponownie w następującym zestawie wskazówek.

- Aby nawiązać połączenie z bramą usługi Rigado, zobacz [łączenie Rigado kaskada 500 z aplikacją IoT Central platformy Azure](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Aby połączyć czujniki RuuviTag, zobacz [łączenie czujnika RuuviTag z aplikacją IoT Central platformy Azure](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Możesz również użyć tych wskazówek, aby utworzyć dwie symulowane czujniki, jeśli jest to konieczne.

## <a name="add-rules-and-actions"></a>Dodawanie reguł i akcji
W ramach korzystania z czujników w aplikacji IoT Central platformy Azure do monitorowania warunków można utworzyć reguły uruchamiania akcji w przypadku spełnienia określonych warunków. Reguła jest skojarzona z szablonem urządzenia i co najmniej jednym urządzeniem oraz zawiera warunki, które muszą zostać spełnione na podstawie danych telemetrycznych lub zdarzeń urządzeń. Reguła ma także co najmniej jedną skojarzoną akcję. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji elementu webhook w celu wysyłania danych do innych usług. Szablon aplikacji **do analizy w sklepie** zawiera kilka wstępnie zdefiniowanych reguł dla urządzeń w aplikacji.

W tej sekcji utworzysz nową regułę, która sprawdza maksymalny poziom wilgotności na podstawie danych telemetrycznych czujnika RuuviTag. Dodaj akcję do reguły, aby Jeśli wilgotność przekroczy wartość maksymalną, aplikacja wyśle wiadomość e-mail. 

Aby utworzyć regułę: 

1. Rozwiń okienko po lewej stronie.

1. Wybierz pozycję **reguły**.

1. Wybierz pozycję **+ Nowe**.

1. Wprowadź *poziom wilgotności* jako nazwę reguły. 

1. Wybierz szablon urządzenia RuuviTag w polu **zakresy**. Zdefiniowana reguła będzie miała zastosowanie do wszystkich czujników opartych na tym szablonie. Opcjonalnie można utworzyć filtr, który będzie stosował regułę tylko do zdefiniowanego podzestawu czujników. 

1. Wybierz `Relative humidity` jako dane **telemetryczne**. Jest to możliwość urządzenia dostosowana w poprzednim kroku.

1. Wybierz `Is greater than` jako **operatora**. 

1. Wprowadź typowy górny poziom wilgotności w danym środowisku jako **wartość**. Na przykład wprowadź *65*. Ustawisz warunek dla reguły, która występuje, gdy wilgotność względna w dowolnym RuuviTagm lub symulowanym czujniku przekracza tę wartość. Może być konieczne dostosowanie wartości w górę lub w dół w zależności od normalnego zakresu wilgotności w środowisku.  

   ![Dodawanie warunków reguły IoT Central platformy Azure](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Aby dodać akcję do reguły:

1. Wybierz pozycję **+ poczta e-mail**. 

1. Wprowadź wartość *powiadomienia o wysokiej wilgotności* jako przyjazną **nazwę wyświetlaną** dla akcji. 

1. Wprowadź adres e-mail skojarzony z Twoim kontem w programie **do**. Jeśli używasz innego adresu e-mail, używany adres musi należeć do użytkownika, który został dodany do aplikacji. Użytkownik musi także zalogować się i wypróbować co najmniej jeden raz.

1. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.

1. Wybierz pozycję **gotowe** , aby zakończyć akcję.

   ![IoT Central platformy Azure — Dodawanie akcji do reguł](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę. 

    W ciągu kilku minut określone konto e-mail powinno zacząć otrzymywać wiadomości e-mail. Aplikacja wysyła wiadomość e-mail za każdym razem, gdy czujnik wskazuje, że poziom wilgotności przekroczy wartość w warunku.

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie aplikacji dla sklepu detalicznego za pomocą szablonu usługi Azure IoT Central **in-Store Analytics — wyewidencjonowywanie**
* Dostosowywanie ustawień aplikacji
* Tworzenie i dostosowywanie szablonów urządzeń IoT
* Łączenie urządzeń z aplikacją
* Dodawanie reguł i akcji do monitorowania warunków

Teraz, po utworzeniu aplikacji do monitorowania warunków IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dostosowywanie pulpitu nawigacyjnego operatora](./tutorial-in-store-analytics-customize-dashboard.md)
