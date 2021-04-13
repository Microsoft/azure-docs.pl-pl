---
title: Omówienie interfejsu API Azure Relay | Microsoft Docs
description: Ten artykuł zawiera omówienie dostępnych Azure Relay interfejsów API (.NET Standard, .NET Framework, Node.js itp.).
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 391bd74f2bce8721b6d6359f3990af494277aa45
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312998"
---
# <a name="available-relay-apis"></a>Dostępne interfejsy API przekazywania

## <a name="runtime-apis"></a>Interfejsy API środowiska uruchomieniowego

W poniższej tabeli wymieniono wszystkie aktualnie dostępne klientów środowiska uruchomieniowego przekaźnika.

Sekcja [dodatkowe informacje](#additional-information) zawiera więcej informacji o stanie każdej biblioteki środowiska uruchomieniowego.

| Język/platforma | Dostępna funkcja | Pakiet klienta | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | Połączenia hybrydowe | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Przekaźnik WCF | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Nie dotyczy |
| Węzeł | Połączenia hybrydowe | [WebSockets `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Żądania HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem .NET ma wiele środowisk uruchomieniowych, dlatego istnieje wiele bibliotek .NET dla przekaźnika. Bibliotekę .NET Standard można uruchomić przy użyciu platformy .NET Core lub .NET Framework, podczas gdy biblioteka .NET Framework może być uruchamiana tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat platformy .NET Framework, zobacz [wersje architektury](/dotnet/articles/standard/frameworks).

Biblioteka .NET Framework obsługuje tylko model programowania WCF i opiera się na zastrzeżonym protokole binarnym opartym na `net.tcp` transportach WCF. Ten protokół i Biblioteka są utrzymywane w celu zapewnienia zgodności z poprzednimi wersjami z istniejącymi aplikacjami.

Biblioteka .NET Standard jest oparta na otwartej definicji protokołu dla przekaźnika Połączenia hybrydowe, który kompiluje w przypadku połączeń HTTP i WebSockets. Biblioteka obsługuje abstrakcję strumienia za pośrednictwem obiektów WebSockets i prostego gestu interfejsu API odpowiedzi na żądanie w celu odpowiedzi na żądania HTTP. Przykład [interfejsu Web API](https://github.com/Azure/azure-relay-dotnet) pokazuje, jak zintegrować Połączenia hybrydowe z ASP.NET Core dla usług sieci Web.

#### <a name="nodejs"></a>Node.js

Moduły Połączenia hybrydowe wymienione w powyższej tabeli zastępują lub zmieniają istniejące moduły Node.js z alternatywnymi implementacjami, które nasłuchują w usłudze Azure Relay zamiast na stosie sieci lokalnych.

`hyco-https`Moduł zmienia i częściowo zastępuje podstawowe moduły Node.js `http` i `https` dostarcza implementację odbiornika https zgodną z wieloma istniejącymi modułami Node.js i aplikacjami, które są zależne od tych modułów podstawowych.

`hyco-ws`Moduły i `hyco-websocket` zmieniają popularne `ws` i `websocket` moduły dla Node.js, zapewniając alternatywne implementacje odbiorników, które umożliwiają modułom i aplikacjom korzystanie z każdego z modułów, aby działały za usługą połączenia hybrydowe Relay.

Szczegółowe informacje o tych modułach znajdują się w repozytorium GitHub [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.yml)
