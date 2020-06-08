---
title: Automatyzuj cykl życia aplikacji dzięki Visual Studio App Center i usługom platformy Azure
description: Dowiedz się więcej na temat usług, takich jak App Center, które pomagają skonfigurować ciągłą kompilację i integrację dla aplikacji mobilnych.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: 86f75e393f54827a58627e3c41b19563c3983ac9
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482858"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatyzuj cykl życia aplikacji dzięki ciągłej kompilacji i integracji

Deweloperzy mogą napisać kod i sprawdzać go w repozytorium kodu, ale zatwierdzenia, które zaewidencjonowano, nie zawsze są spójne. Gdy wielu deweloperów pracuje nad tym samym projektem, problemy mogą być zintegrowane z integracją. Zespoły mogą działać w sytuacjach, w których nie działają, które są błędne, a rozwój projektu jest opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie skompilowany i przetestowany w celu sprawdzenia pod kątem błędów, co sprawia, że proces jest powolny i mniej iteracyjny. 

Dzięki ciągłej kompilacji i integracji deweloperzy mogą uprościć kompilacje i przetestować swój kod przez zatwierdzenie zmian w repozytorium kodu źródłowego i umieszczenie testów i weryfikacji w środowisku kompilacji. W ten sposób są zawsze uruchamiane testy względem ich kodu. Wszystkie zmiany wprowadzone w kodzie źródłowym są tworzone w sposób ciągły zawsze, gdy istnieje zatwierdzenie dokonane w repozytorium. Po każdym zaewidencjonowaniu serwer ciągłej integracji sprawdza poprawność i wykonuje każdy test utworzony przez dewelopera. Jeśli testy nie zachodzą, kod jest wysyłany ponownie w celu wprowadzenia dalszych zmian. W ten sposób deweloperzy nie łamią utworzonych kompilacji. Nie muszą również uruchamiać wszystkich testów lokalnie na swoich komputerach, co zwiększa produktywność deweloperów. 

## <a name="key-benefits"></a>Najważniejsze korzyści
- Automatyzuj kompilacje, testy i wdrożenia dla potoków.
- Szybko wykrywaj usterki i naprawiaj problemy, aby zapewnić szybsze kursy wydania.
- Zatwierdź kod częściej i szybko Twórz aplikacje.
- Szybko zmieniaj kod bez jakichkolwiek problemów.
- Krótszy czas wprowadzenia na rynek, dzięki czemu tylko kod dobry zapewnia, że jest to cały sposób.
- Zwiększaj efektywność małych fragmentów kodu, ponieważ w tym samym czasie są zintegrowane małe fragmenty kodu.
- Zwiększ przejrzystość zespołu i odpowiedzialność, aby uzyskać ciągłą opinię od klientów i Twojego zespołu.

Użyj następujących usług, aby włączyć potok ciągłej integracji w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Kompilacja App Center](/appcenter/build/) pomaga tworzyć aplikacje natywne i Międzyplatformowe, nad którymi pracuje zespół przy użyciu bezpiecznej infrastruktury chmurowej. Repozytorium można łatwo połączyć w Visual Studio App Center i rozpocząć tworzenie aplikacji w chmurze na każdym zatwierdzeniu. Nie musisz martwić się o Konfigurowanie serwerów kompilacji lokalnie, skomplikowane konfiguracje i kod, który kompiluje na komputerze współpracownika, ale nie.

Dzięki dodaniu mocy usług Visual Studio App Center można dodatkowo zautomatyzować przepływ pracy. Możesz automatycznie wydać kompilacje dla testerów i publicznych sklepów z aplikacjami za pomocą dystrybucji App Center. Możesz również uruchamiać zautomatyzowane testy interfejsu użytkownika na tysiącach rzeczywistych konfiguracji urządzeń i systemów operacyjnych w chmurze za pomocą testu App Center.

**Najważniejsze funkcje**
- Skonfiguruj ciągłą integrację w kilka minut i szybciej Twórz aplikacje.
- Integruj się z usługami GitHub, BitBucket, Azure DevOps i GitLab.
- Twórz szybkie i bezpieczne kompilacje na zarządzanych maszynach hostowanych w chmurze.
- Zezwól na uruchamianie testów i sprawdź, czy aplikacja jest oparta na rzeczywistych urządzeniach z systemami iOS i Android.
- Korzystaj z natywnej i międzyplatformowej obsługi dla systemów iOS, Android, macOS, Windows, Xamarin i reaguj natywnie.
- Dostosuj kompilacje, dodając skrypty po kompilacji, które są wykonywane przed kompilacją i po kompilacji.

**Dokumentacja**
- [Zarejestruj się w usłudze Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do kompilacji App Center](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), usługa na platformie Azure DevOps, to w pełni oferowana usługa ciągłej integracji i ciągłego dostarczania, która współpracuje z preferowanym dostawcą usługi git. Można wdrożyć w większości najważniejszych usług w chmurze, w tym na platformie Azure. Możesz zacząć od kodu w usłudze GitHub, w witrynie GitHub Enterprise Server, GitLab, BitBucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services (AWS).

**Najważniejsze funkcje**
- **Uproszczone środowisko oparte na zadaniach służące do konfigurowania serwera Ci:** Skonfiguruj serwer CI dla natywnych (Android, iOS i Windows) i międzyplatformowych aplikacji mobilnych (Xamarin, Cordova i reaguj natywnie), oprócz technologii serwerowych firmy Microsoft i innych firm (Node. js, Java).
- **Dowolny język, platforma i chmura:** Kompiluj, Testuj i wdrażaj aplikacje Node. js, Python, Java, PHP, Ruby, go, C/C++, C#, Android i iOS. Uruchamiaj równolegle w systemach Linux, macOS i Windows. Wdrażaj do dostawców chmury, takich jak Azure, AWS i Google Cloud Platform. Dystrybuuj aplikacje mobilne za poorednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenera macierzystego:** Łatwo twórz nowe kontenery i wypchnij je do dowolnego rejestru. Wdróż kontenery na niezależnych hostach lub Kubernetes.
- **Zaawansowane przepływy pracy:** Łatwo twórz łańcuchy kompilacji i kompilacje wieloetapowe. Uzyskaj pomoc techniczną dotyczącą YAML, integracji testów, bram wydań, raportowania i nie tylko.
- **Rozszerzalne:** Korzystaj z wielu zadań kompilowania, testowania i wdrażania utworzonych przez społeczność, które obejmują setki rozszerzeń od zapasu do SonarCloud. Można nawet wdrożyć z innych systemów CI, takich jak Jenkins. Elementy webhook i interfejsy API REST mogą ułatwić integrację.
- **Bezpłatne kompilacje hostowane w chmurze:** Te kompilacje są dostępne dla repozytoriów publicznych i prywatnych.
- **Obsługa wdrożenia innym dostawcom chmury:** Dostawcy obejmują AWS i Google Cloud Platform.

**Dokumentacja**
- [Wprowadzenie do przewodnika Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Przewodniki Szybki start](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Aby ułatwić wybór odpowiedniej usługi dla kompilacji aplikacji, zapoznaj się z artykułem, który porównuje [App Center kompilacja a Azure Pipelines](/appcenter/build/choose-between-services).
