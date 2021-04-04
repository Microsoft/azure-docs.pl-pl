---
title: Omówienie modelu programowania usługi Service Fabric
description: 'Service Fabric oferuje dwie struktury do kompilowania usług: strukturę aktora i strukturę usług. Oferują one różne wady, prostota i kontrola.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575724"
---
# <a name="service-fabric-programming-model-overview"></a>Omówienie modelu programowania usługi Service Fabric

Service Fabric oferuje wiele sposobów zapisywania usług i zarządzania nimi. Usługi mogą korzystać z interfejsów API Service Fabric, aby w pełni korzystać z funkcji platformy i struktur aplikacji. Usługi mogą być również dowolnym skompilowanym programem wykonywalnym zapisanym w dowolnym języku lub kodzie działającym w kontenerze hostowanym w klastrze Service Fabric.

## <a name="guest-executables"></a>Pliki wykonywalne gościa

[Plik wykonywalny gościa](service-fabric-guest-executables-introduction.md) to istniejący, dowolny plik wykonywalny (zapisany w dowolnym języku), który może być uruchamiany jako usługa w aplikacji. Pliki wykonywalne gościa nie wywołują bezpośrednio interfejsów API Service Fabric SDK. Jednak nadal korzystają z funkcji oferowanych przez platformę, takich jak odnajdowanie usług, niestandardowa kondycja i raportowanie obciążeń przez wywoływanie interfejsów API REST udostępnianych przez Service Fabric. Mają także pełną pomoc techniczną dotyczącą cyklu życia aplikacji.

Wprowadzenie do plików wykonywalnych gościa przez wdrożenie pierwszej [aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Kontenery

Domyślnie program Service Fabric wdraża i aktywuje usługi jako procesy. Service Fabric można również wdrożyć usługi w [kontenerach](service-fabric-containers-overview.md). Service Fabric obsługuje wdrażanie kontenerów systemu Linux i kontenerów Windows w systemie Windows Server 2016 i nowszych. Obrazy kontenerów można ściągnąć z dowolnego repozytorium kontenera i wdrożyć je na komputerze. Istniejące aplikacje można wdrażać jako pliki wykonywalne gościa, Service Fabric bezstanowe lub stanowe usługi niezawodne lub Reliable Actors w kontenerach, a także łączyć usługi w ramach procesów i usług w kontenerach w tej samej aplikacji.

[Dowiedz się więcej na temat konteneryzowania usług w systemie Windows lub Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services to lekka platforma służąca do pisania usług, które integrują się z platformą Service Fabric i korzystają z pełnego zestawu funkcji platformy. Reliable Services udostępnić minimalny zestaw interfejsów API, które umożliwiają Service Fabric środowisko uruchomieniowe do zarządzania cyklem życia usług, dzięki czemu usługi mogą współdziałać ze środowiskiem uruchomieniowym. Struktura aplikacji jest minimalna, zapewniając pełną kontrolę nad opcjami projektowania i implementacji, i może służyć do hostowania dowolnej innej struktury aplikacji, takiej jak ASP.NET Core.

Reliable Services może być bezstanowa, podobnie jak w przypadku większości platform usługi, takich jak serwery sieci Web, w którym tworzone jest każde wystąpienie usługi, a stan jest utrwalany w rozwiązaniu zewnętrznym, takim jak Azure DB lub Azure Table Storage.

Na wyłączność Service Fabric, Reliable Services może być również stanowa, gdzie stan jest utrwalany bezpośrednio w samej usłudze przy użyciu niezawodnych kolekcji. Stan jest wysoce dostępny poprzez replikację i dystrybuowany za pomocą partycjonowania, które są zarządzane automatycznie przez Service Fabric.

[Dowiedz się więcej na temat Reliable Services](service-fabric-reliable-services-introduction.md) lub Zacznij od [utworzenia pierwszej niezawodnej usługi](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core to międzyplatformowa platforma służąca do tworzenia nowoczesnych, opartych na chmurze aplikacji internetowych, takich jak aplikacje internetowe, aplikacje IoT i frontony mobilne. Service Fabric integruje się z ASP.NET Core, dzięki czemu można napisać bezstanowe i bezASP.NET Core stanowe aplikacje, które wykorzystują niezawodne kolekcje i zaawansowane funkcje aranżacji Service Fabric.

[Dowiedz się więcej o ASP.NET Core w Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) lub Zacznij od [pisania pierwszej ASP.NET Core aplikacji Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

W oparciu o Reliable Services, niezawodna platforma aktorów jest strukturą aplikacji, która implementuje wzorzec [aktora wirtualnego](https://research.microsoft.com/en-us/projects/orleans/) , na podstawie [modelu aktorów](https://en.wikipedia.org/wiki/Actor_model)obliczeniowych. Niezawodna struktura aktora używa niezależnych jednostek obliczeniowych i stanu z jednowątkowym wykonywaniem nazywanym *aktorami*. Niezawodna struktura aktora zapewnia wbudowaną komunikację dla aktorów i wstępnie ustawionych ustawień trwałości stanu i skalowalnych w poziomie.

Ponieważ Reliable Actors to struktura aplikacji oparta na Reliable Services, jest w pełni zintegrowana z platformą Service Fabric i korzyściami z pełnego zestawu funkcji oferowanych przez platformę.

[Dowiedz się więcej na temat Reliable Actors](service-fabric-reliable-actors-introduction.md) lub Zacznij od [napisania pierwszej niezawodnej usługi aktora](service-fabric-reliable-actors-get-started.md)

[Tworzenie usługi frontonu przy użyciu ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Następne kroki

[Przegląd Service Fabric i kontenerów](service-fabric-containers-overview.md)

[Omówienie usług Reliable Services](service-fabric-reliable-services-introduction.md)

[Omówienie elementów Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric i ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
