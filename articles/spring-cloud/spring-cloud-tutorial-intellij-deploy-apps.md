---
title: 'Samouczek: używanie IntelliJ do wdrażania aplikacji w chmurze platformy Azure'
description: Użyj IntelliJ, aby wdrażać aplikacje w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: a808e37c0f0aae2816eb536dc43485c125354b81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916204"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Wdrażanie aplikacji Azure Spring Cloud za pomocą środowiska IntelliJ

**Ten artykuł ma zastosowanie do:** ✔️ Java

Wtyczka IntelliJ dla chmury wiosennej platformy Azure obsługuje wdrażanie aplikacji ze POMYSŁu IntelliJ.  

Przed uruchomieniem tego przykładu możesz skorzystać z [podstawowego przewodnika Szybki Start](spring-cloud-quickstart.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [IntelliJ pomysł, Community/Ultimate Edition, wersja 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instalowanie wtyczki
Można dodać Azure Toolkit for IntelliJ pomysł 3.43.0 z interfejsu użytkownika **wtyczek** IntelliJ.

1. Uruchom IntelliJ.  Jeśli wcześniej otwarto projekt, Zamknij projekt, aby wyświetlić okno dialogowe powitalne. Wybierz pozycję **Konfiguruj** z link dolny prawy, a następnie kliknij pozycję **wtyczki** , aby otworzyć okno dialogowe Konfiguracja wtyczki, a następnie wybierz pozycję **Zainstaluj wtyczki z dysku**.

    ![Wybierz pozycję Konfiguruj](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Wyszukaj Azure Toolkit for IntelliJ.  Kliknij przycisk **Zainstaluj**.

    ![Zainstaluj wtyczkę](media/spring-cloud-intellij-howto/install-plugin.png)

1. Kliknij pozycję **Uruchom ponownie środowisko IDE**.

## <a name="tutorial-procedures"></a>Procedury samouczka
Poniższe procedury służą do wdrażania aplikacji Hello world przy użyciu POMYSŁu IntelliJ.

* Otwórz projekt GS-sprężynowy
* Wdrażanie w chmurze Azure wiosennej
* Pokaż dzienniki przesyłania strumieniowego

## <a name="open-gs-spring-boot-project"></a>Otwórz projekt GS-sprężynowy

1. Pobierz i rozpakuj repozytorium źródłowe dla tego samouczka lub Sklonuj je za pomocą narzędzia Git: klonowanie git https://github.com/spring-guides/gs-spring-boot.git 
1. dysk CD do GS-Spring-boot\complete.
1. Otwórz okno dialogowe **Witamy** w IntelliJ, wybierz pozycję **Importuj projekt** , aby otworzyć Kreatora importu.
1. Wybierz `gs-spring-boot\complete` folder.

    ![Importuj projekt](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Wdrażanie w chmurze Azure wiosennej
Aby wdrożyć platformę Azure, musisz zalogować się przy użyciu konta platformy Azure i wybrać subskrypcję.  Aby uzyskać szczegółowe informacje dotyczące logowania, zobacz [Instalowanie i logowanie](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze projektów IntelliJ, a następnie wybierz pozycję **Azure**  ->  **Deploy w chmurze Azure wiosennej**.

    ![Wdrażanie na platformie Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Zaakceptuj nazwę aplikacji w polu **Nazwa** . **Nazwa** odwołuje się do konfiguracji, a nie nazwy aplikacji. Użytkownicy zazwyczaj nie muszą go zmieniać.
1. Zaakceptuj identyfikator z projektu dla **artefaktu**.
1. Wybierz **aplikację:** następnie kliknij pozycję **Utwórz aplikację..**..

    ![Wdrażanie na platformie Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Wprowadź **nazwę aplikacji**, a następnie kliknij przycisk **OK**.

    ![Wdróż na platformie Azure](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** . 

    ![Wdrażanie na platformie Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Wtyczka uruchomi polecenie `mvn package` w projekcie, a następnie utworzy nową aplikację i wdroży plik JAR wygenerowany przez `package` polecenie.

1. Jeśli adres URL aplikacji nie jest wyświetlany w oknie danych wyjściowych, Pobierz go z Azure Portal. Przejdź do grupy zasobów do wystąpienia chmury wiosennej platformy Azure.  Następnie kliknij pozycję **aplikacje**.  Uruchomiona aplikacja zostanie wyświetlona.

    ![Pobierz adres URL testu](media/spring-cloud-intellij-howto/get-test-url.png)

1. Przejdź do adresu URL w przeglądarce.

    ![Przejdź do przeglądarki 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Pokaż dzienniki przesyłania strumieniowego
Aby pobrać dzienniki:
1. Wybierz opcję **Eksplorator platformy Azure**, a następnie pozycję **chmura Wiosenna**.
1. Kliknij prawym przyciskiem myszy działającą aplikację.
1. Z listy rozwijanej wybierz pozycję **dzienniki przesyłania strumieniowego** .

    ![Wybieranie dzienników przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wybierz wystąpienie.

    ![Wybierz wystąpienie](media/spring-cloud-intellij-howto/select-instance.png)

1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

    ![Dane wyjściowe dziennika przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Następne kroki
* [Przygotuj aplikację wiosenną dla chmury wiosennej platformy Azure](./spring-cloud-tutorial-prepare-app-deployment.md)
* [Dowiedz się więcej o Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/)
