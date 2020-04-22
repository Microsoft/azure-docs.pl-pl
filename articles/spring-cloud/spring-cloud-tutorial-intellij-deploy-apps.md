---
title: Samouczek — wdrażanie aplikacji Azure Spring Cloud za pomocą technologii IntelliJ
description: IntelliJ umożliwia wdrażanie aplikacji w usłudze Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731372"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Wdrażanie aplikacji Azure Spring Cloud za pomocą środowiska IntelliJ
Wtyczka IntelliJ dla usługi Azure Spring Cloud obsługuje wdrażanie aplikacji z intellij idea.  

## <a name="prerequisites"></a>Wymagania wstępne
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, wersja 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Zainstaluj wtyczkę
Zestaw narzędzi platformy Azure dla intellij idea 3.35.0 można dodać z interfejsu **intellij wtyczek.**

1. Uruchom IntelliJ.  Jeśli projekt został otwarty wcześniej, zamknij projekt, aby wyświetlić okno powitalne. Wybierz **pozycję Konfiguruj** z łącza w prawym dolnym dolnym czasie, a następnie kliknij pozycję **Wtyczki,** aby otworzyć okno konfiguracji wtyczki, a następnie wybierz pozycję **Zainstaluj wtyczki z dysku**.

    ![Wybierz pozycję Konfiguruj](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Wyszukaj zestaw narzędzi platformy Azure dla technologii IntelliJ.  Kliknij **pozycję Zainstaluj**.

    ![Zainstaluj wtyczkę](media/spring-cloud-intellij-howto/install-plugin.png)

1. Kliknij **pozycję Uruchom ponownie IDE**.

## <a name="tutorial-procedures"></a>Procedury samouczka
Poniższe procedury wdrażania aplikacji Hello World przy użyciu IntelliJ IDEA.

* Otwarty projekt gs-spring-boot
* Wdrażanie w wiosennej chmurze platformy Azure
* Pokaż dzienniki przesyłania strumieniowego

## <a name="open-gs-spring-boot-project"></a>Otwarty projekt gs-spring-boot

1. Pobierz i rozpakuj repozytorium źródłowe dla tego samouczka lub sklonuj go za pomocą Git: git clonehttps://github.com/spring-guides/gs-spring-boot.git 
1. cd do gs-spring-boot\complete.
1. Otwórz okno dialogowe **Powitanie** IntelliJ, wybierz pozycję **Importuj projekt,** aby otworzyć kreatora importu.
1. Wybierz `gs-spring-boot\complete` folder.

    ![Importowanie projektu](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Wdrażanie w wiosennej chmurze platformy Azure
Aby wdrożyć na platformie Azure, musisz zalogować się za pomocą konta platformy Azure i wybrać subskrypcję.  Aby uzyskać szczegółowe informacje na temat logowania, zobacz [Instalacja i logowanie](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze projektu IntelliJ i wybierz pozycję **Azure** -> **Deploy to Azure Spring Cloud**.

    ![Wdrażanie na platformie Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Zaakceptuj nazwę aplikacji w polu **Nazwa.** **Nazwa** odnosi się do konfiguracji, a nie nazwy aplikacji. Użytkownicy zazwyczaj nie muszą go zmieniać.
1. Zaakceptuj identyfikator z projektu dla **artefaktu**.
1. Wybierz **pozycję Aplikacja:** a następnie kliknij pozycję **Utwórz aplikację...**.

    ![Wdrażanie na platformie Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Wprowadź **nazwę aplikacji**, a następnie kliknij przycisk **OK**.

    ![Wdrażanie na platformie Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Rozpocznij wdrażanie, klikając przycisk **Uruchom.** 

    ![Wdrażanie na platformie Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Wtyczka uruchomi polecenie `mvn package` w projekcie, a następnie utworzy nową aplikację i `package` wdroży słoik wygenerowany przez polecenie.

1. Jeśli adres URL aplikacji nie jest wyświetlany w oknie danych wyjściowych, pobierz go z witryny Azure portal. Przejdź od grupy zasobów do wystąpienia usługi Azure Spring Cloud.  Następnie kliknij pozycję **Aplikacje**.  Uruchomiona aplikacja zostanie wyświetlona.

    ![Pobierz adres URL testu](media/spring-cloud-intellij-howto/get-test-url.png)

1. Przejdź do adresu URL w przeglądarce.

    ![Nawigacja w przeglądarce 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Pokaż dzienniki przesyłania strumieniowego
Aby uzyskać dzienniki:
1. Wybierz **Pozycję Azure Explorer**, a następnie Spring **Cloud**.
1. Kliknij prawym przyciskiem myszy uruchomiającą aplikację.
1. Z listy rozwijanej wybierz **pozycję Dzienniki przesyłania strumieniowego.**

    ![Wybieranie dzienników przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wybierz wystąpienie.

    ![Wybierz wystąpienie](media/spring-cloud-intellij-howto/select-instance.png)

1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

    ![Przesyłanie strumieniowe danych wyjściowych dziennika](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Następne kroki
* [Przygotuj aplikację Wiosna dla usługi Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Dowiedz się więcej o usłudze Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
