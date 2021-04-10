---
title: 'ML Studio (klasyczny): lokalna SQL Server — Azure'
description: Użyj danych z bazy danych SQL Server, aby przeprowadzić zaawansowaną analizę przy użyciu Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517403"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>Wykonywanie analizy za pomocą Azure Machine Learning Studio (klasyczny) przy użyciu bazy danych SQL Server

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Często przedsiębiorstwa, które współpracują z danymi lokalnymi, mogą wykorzystać skalę i elastyczność chmury na potrzeby obciążeń uczenia maszynowego. Ale nie chcą zakłócać swoich bieżących procesów i przepływów pracy, przenosząc dane lokalne do chmury. Azure Machine Learning Studio (klasyczny) obsługuje teraz odczytywanie danych z SQL Serverj bazy danych, a następnie uczenie i ocenianie modelu przy użyciu tych danych. Nie jest już konieczne ręczne kopiowanie i synchronizowanie danych między chmurą a serwerem lokalnym. Zamiast tego moduł **Importuj dane** w Azure Machine Learning Studio (klasyczny) można teraz odczytywać bezpośrednio z bazy danych SQL Server do zadań szkolenia i oceniania.

Ten artykuł zawiera omówienie sposobu, w jaki SQL Server dane w Azure Machine Learning Studio (klasyczne). Przyjęto założenie, że znane są koncepcje programu Studio (klasyczne), takie jak obszary robocze, moduły, zestawy danych, eksperymenty *itp.*

> [!NOTE]
> Ta funkcja jest niedostępna w przypadku bezpłatnych obszarów roboczych. Aby uzyskać więcej informacji o Machine Learning cenach i warstwach, zobacz [Azure Machine Learning cennika](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalowanie Data Factory samoobsługowego Integration Runtime
Aby uzyskać dostęp do bazy danych SQL Server w Azure Machine Learning Studio (klasyczny), należy pobrać i zainstalować Data Factory samodzielnej Integration Runtime, wcześniej znanej jako brama Zarządzanie danymi. Po skonfigurowaniu połączenia w Machine Learning Studio (klasyczny) można pobrać i zainstalować Integration Runtime (IR) przy użyciu okna dialogowego **Pobierz i zarejestruj bramę danych** opisaną poniżej.


Możesz również zainstalować środowisko IR wcześniej, pobierając i uruchamiając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Plik MSI może również służyć do uaktualniania istniejącego środowiska IR do najnowszej wersji z zachowaniem wszystkich ustawień.

Data Factory Self-Hosted Integration Runtime ma następujące wymagania wstępne:

* Integracja Self-Hosted Data Factory wymaga 64-bitowego systemu operacyjnego z .NET Framework 4.6.1 lub nowszym.
* Obsługiwane wersje systemu operacyjnego Windows to Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Zalecaną konfiguracją maszyny IR jest co najmniej 2 GHz, 4-rdzeniowy procesor CPU, 8 GB pamięci RAM i dysk 80 GB.
* Jeśli maszyna hosta przechodzi w stan hibernacji, środowisko IR nie będzie odpowiadać na żądania danych. W związku z tym należy skonfigurować odpowiedni plan zasilany na komputerze przed zainstalowaniem środowiska IR. Jeśli komputer jest skonfigurowany do hibernacji, w instalacji programu IR zostanie wyświetlony komunikat.
* Ponieważ działanie kopiowania występuje z określoną częstotliwością, użycie zasobów (procesor, pamięć) na maszynie również jest zgodne z tym samym wzorcem i okresem bezczynności. Użycie zasobów również jest zależne od ilości przenoszonych danych. Gdy trwa wykonywanie wielu zadań kopiowania, zaobserwuj użycie zasobów w godzinach szczytu. Mimo że minimalna konfiguracja określona powyżej jest technicznie wystarczająca, może być konieczne zdefiniowanie większej ilości zasobów niż konfiguracja minimalna w zależności od obciążenia danych.

Podczas konfigurowania i używania Data Factory samoobsługowego Integration Runtime należy wziąć pod uwagę następujące kwestie:

* Na jednym komputerze można zainstalować tylko jedno wystąpienie środowiska IR.
* Możesz użyć jednego środowiska IR dla wielu lokalnych źródeł danych.
* Można połączyć wiele urzędów skarbowych na różnych komputerach z tym samym lokalnym źródłem danych.
* Urząd Skarbowy można skonfigurować tylko dla jednego obszaru roboczego. Obecnie urząd skarbowy nie może być współużytkowany przez obszary robocze.
* Można skonfigurować wiele urzędów skarbowych dla jednego obszaru roboczego. Na przykład możesz chcieć użyć podczerwieni połączonej ze źródłami danych testowych podczas opracowywania i produkcyjnego środowiska IR, gdy wszystko jest gotowe do operacjonalizować.
* Środowisko IR nie musi znajdować się na tym samym komputerze co źródło danych. Jednak bliżej źródła danych skraca czas, w którym Brama może połączyć się ze źródłem danych. Zalecamy zainstalowanie środowiska IR na komputerze innym niż ten, który obsługuje lokalne źródło danych, aby brama i źródło danych nie konkurują o zasoby.
* Jeśli na komputerze zainstalowano już środowisko IR obsługujące Power BI lub Azure Data Factory scenariusze, zainstaluj oddzielne środowisko IR dla Azure Machine Learning Studio (klasycznego) na innym komputerze.

  > [!NOTE]
  > Nie można uruchomić Data Factory samoobsługowego Integration Runtime i Power BI Gateway na tym samym komputerze.
  >
  >
* Musisz użyć Data Factory samodzielnego Integration Runtime do Azure Machine Learning Studio (klasyczny), nawet jeśli używasz platformy Azure ExpressRoute do innych danych. Źródło danych powinno być traktowane jako lokalne źródło danych (znajdujące się za zaporą), nawet w przypadku korzystania z ExpressRoute. Użyj Data Factory samoobsługowego Integration Runtime do ustanowienia łączności między Machine Learning a źródłem danych.

Szczegółowe informacje na temat wymagań wstępnych instalacji, kroki instalacji i wskazówki dotyczące rozwiązywania problemów znajdują się w artykule [Integration Runtime w Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dane przychodzące z usługi SQL Server Database do Azure Machine Learning
W tym instruktażu skonfigurujesz Integration Runtime Azure Data Factory w obszarze roboczym Azure Machine Learning, skonfigurujesz ją, a następnie odczytasz dane z SQL Serverj bazy danych.

> [!TIP]
> Przed rozpoczęciem należy wyłączyć blokowanie wyskakujących okienek w przeglądarce dla programu `studio.azureml.net` . Jeśli używasz przeglądarki Google Chrome, Pobierz i Zainstaluj jedną z kilku wtyczek dostępnych w sklepie Google Chrome webstore [kliknij pozycję po rozszerzeniu aplikacji](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory samoobsługowy Integration Runtime był wcześniej znany jako brama Zarządzanie danymi. Samouczek krok po kroku będzie nadal odwoływać się do niego jako brama.  

### <a name="step-1-create-a-gateway"></a>Krok 1. Tworzenie bramy
Pierwszym krokiem jest utworzenie i skonfigurowanie bramy w celu uzyskania dostępu do bazy danych SQL.

1. Zaloguj się do [Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net/Home/) i wybierz obszar roboczy, w którym chcesz korzystać.
2. Kliknij blok **Ustawienia** po lewej stronie, a następnie kliknij kartę **bramy danych** w górnej części ekranu.
3. Kliknij pozycję **Nowa brama danych** w dolnej części ekranu.

    ![Nowa brama danych](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. W oknie dialogowym **Nowa brama danych** wprowadź **nazwę bramy** i opcjonalnie Dodaj **Opis**. Kliknij strzałkę w prawym dolnym rogu, aby przejść do następnego kroku konfiguracji.

    ![Wprowadź nazwę i opis bramy](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. W oknie dialogowym Pobieranie i rejestrowanie bramy danych skopiuj klucz rejestracji bramy do Schowka.

    ![Pobieranie i rejestrowanie bramy danych](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Jeśli nie pobrano jeszcze i nie zainstalowano bramy Zarządzanie danymi firmy Microsoft, kliknij pozycję **Pobierz bramę zarządzania danymi**. Spowoduje to przejście do centrum pobierania Microsoft, w którym można wybrać wymaganą wersję bramy, pobrać ją i zainstalować. Aby uzyskać szczegółowe informacje na temat wymagań wstępnych instalacji, czynności instalacyjnych i wskazówek dotyczących rozwiązywania problemów, należy [przenieść dane między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Po zainstalowaniu bramy zostanie otwarta Zarządzanie danymi Configuration Manager Gateway i zostanie wyświetlone okno dialogowe **Rejestrowanie bramy** . Wklej **klucz rejestracji bramy** skopiowany do schowka, a następnie kliknij pozycję **zarejestruj**.
8. Jeśli masz już zainstalowaną bramę, uruchom Configuration Manager Zarządzanie danymi bramy. Kliknij przycisk **Zmień klucz**, wklej **klucz rejestracji bramy** skopiowany do Schowka w poprzednim kroku, a następnie kliknij przycisk **OK**.
9. Po zakończeniu instalacji zostanie wyświetlone okno dialogowe **Rejestrowanie bramy** dla programu Microsoft Zarządzanie danymi Gateway Configuration Manager. Wklej klucz rejestracji bramy skopiowany do Schowka w poprzednim kroku, a następnie kliknij pozycję **zarejestruj**.

    ![Rejestrowanie bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Konfiguracja bramy została ukończona, gdy na karcie **Narzędzia główne** w usłudze Microsoft Zarządzanie danymi Gateway Configuration Manager zostanie ustawiona następująca wartość:

    * Nazwa **bramy** i **Nazwa wystąpienia** są ustawione na nazwę bramy.
    * **Rejestracja** jest ustawiona na **zarejestrowane**.
    * **Stan** jest ustawiony na wartość **uruchomiony**.
    * Na dolnej stronie pojawi się pasek stanu **połączony z usługą Zarządzanie danymi Gateway w chmurze** oraz zielony znacznik wyboru.

      ![Zarządzanie danymi Menedżera bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klasyczny) jest również aktualizowany po pomyślnym zakończeniu rejestracji.

    ![Rejestracja bramy powiodła się](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. W oknie dialogowym **Pobierz i zarejestruj bramę danych** kliknij znacznik wyboru, aby zakończyć instalację. Na stronie **Ustawienia** zostanie wyświetlony stan bramy "online". W okienku po prawej stronie znajdziesz informacje o stanie i innych użytecznych informacjach.

    ![Ustawienia bramy](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. W Configuration Manager Microsoft Zarządzanie danymi Gateway przejdź do karty **certyfikat** . Certyfikat określony na tej karcie jest używany do szyfrowania/odszyfrowywania poświadczeń dla lokalnego magazynu danych określonego w portalu. Ten certyfikat jest certyfikatem domyślnym. Firma Microsoft zaleca zmianę tego certyfikatu na własny certyfikat, którego kopię zapasową można utworzyć w systemie zarządzania certyfikatami. Kliknij przycisk **Zmień** , aby zamiast tego użyć własnego certyfikatu.

    ![Zmień certyfikat bramy](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. obowiązkowe Jeśli chcesz włączyć pełne rejestrowanie w celu rozwiązywania problemów z bramą, w Configuration Manager Microsoft Zarządzanie danymi Gateway przejdź do karty **Diagnostyka** i zaznacz opcję **Włącz pełne rejestrowanie w celu rozwiązywania problemów** . Informacje o rejestrowaniu można znaleźć w Podgląd zdarzeń systemu Windows w węźle **Dzienniki aplikacji i usług**  - &gt; **Zarządzanie danymi bramy** . Możesz również użyć karty **Diagnostyka** , aby przetestować połączenie z lokalnym źródłem danych przy użyciu bramy.

    ![Włącz pełne rejestrowanie](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Spowoduje to zakończenie procesu instalacji bramy w Azure Machine Learning Studio (klasyczny).
Teraz możesz przystąpić do korzystania z danych lokalnych.

Można utworzyć i skonfigurować wiele bram w programie Studio (klasyczny) dla każdego obszaru roboczego. Na przykład możesz mieć bramę, którą chcesz połączyć ze źródłami danych testowych podczas opracowywania, i inną bramą dla produkcyjnych źródeł danych. Azure Machine Learning Studio (klasyczny) zapewnia elastyczność konfigurowania wielu bram w zależności od środowiska firmowego. Obecnie nie można udostępnić bramy między obszarami roboczymi, a na jednym komputerze można zainstalować tylko jedną bramę. Aby uzyskać więcej informacji, zobacz [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Krok 2. Używanie bramy do odczytywania danych z lokalnego źródła danych
Po skonfigurowaniu bramy można dodać moduł **importowania danych** do eksperymentu, który wprowadza dane z bazy danych SQL Server.

1. W Machine Learning Studio (klasyczny) wybierz kartę **eksperymenty** , kliknij pozycję **+ Nowy** w lewym dolnym rogu, a następnie wybierz pozycję **pusty eksperyment** (lub wybierz jedną z kilku dostępnych przykładowych eksperymentów).
2. Znajdź i przeciągnij moduł **Import danych** do kanwy eksperymentu.
3. Kliknij przycisk **Zapisz jako** poniżej kanwy. W polu Nazwa eksperymentu wpisz "Azure Machine Learning Studio (klasyczny) SQL Server, wybierz obszar roboczy, a następnie kliknij znacznik wyboru **OK** .

   ![Zapisz eksperyment z nową nazwą](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kliknij moduł **Importuj dane** , aby go zaznaczyć, a następnie w okienku **Właściwości** z prawej strony kanwy wybierz pozycję "SQL Database lokalne" na liście rozwijanej **źródła danych** .
5. Wybierz zainstalowaną i zarejestrowaną **bramę danych** . Można skonfigurować inną bramę, wybierając pozycję "(Dodaj nową bramę danych)".

   ![Wybieranie bramy danych dla modułu importowania danych](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Wprowadź **nazwę serwera bazy danych** SQL i **nazwę bazy danych** wraz z **kwerendą bazy danych** SQL, która ma zostać wykonana.
7. Kliknij pozycję **wprowadź wartości** w obszarze **Nazwa użytkownika i hasło** , a następnie wprowadź swoje poświadczenia bazy danych. W zależności od konfiguracji SQL Server można użyć zintegrowanego uwierzytelniania systemu Windows lub uwierzytelniania SQL Server.

   ![Wprowadź poświadczenia bazy danych](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Komunikat "wymagane wartości" zmienia się na "ustawione wartości" z zielonym znacznikiem wyboru. Wystarczy wprowadzić poświadczenia tylko raz, chyba że zmienią się informacje lub hasło bazy danych. Azure Machine Learning Studio (klasyczny) używa certyfikatu podanego podczas instalacji bramy w celu szyfrowania poświadczeń w chmurze. Na platformie Azure nigdy nie są przechowywane poświadczenia lokalne bez szyfrowania.

   ![Właściwości modułu importowania danych](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kliknij przycisk **Uruchom** , aby uruchomić eksperyment.

Po zakończeniu eksperymentu można wizualizować dane zaimportowane z bazy danych, klikając port wyjściowy modułu **Importuj dane** i wybierając opcję **Wizualizuj**.

Po zakończeniu opracowywania eksperymentu możesz wdrożyć i operacjonalizować model. Przy użyciu usługi wykonywania wsadowego dane z SQL Serverj bazy danych skonfigurowanej w module **Importuj dane** zostaną odczytane i wykorzystane do oceny. Mimo że można użyć usługi odpowiedzi na żądanie do oceniania danych lokalnych, firma Microsoft zaleca użycie [dodatku programu Excel](excel-add-in-for-web-services.md) . Obecnie zapisywanie w bazie danych SQL Server za pomocą **eksportu danych** nie jest obsługiwane w eksperymentach ani opublikowanych usługach sieci Web.