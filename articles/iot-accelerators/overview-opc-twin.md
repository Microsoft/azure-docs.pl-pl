---
title: Co to jest OPC bliźniaczy — Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie wieloosiowego OPCu. Przędza OPCa zapewnia odnajdywanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za poorednictwem interfejsów API REST.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646952"
---
# <a name="what-is-opc-twin"></a>Co to jest OPC bliźniaczy?

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

OPC bliźniaczy składa się z mikrousług, które używają Azure IoT Edge i IoT Hub do łączenia chmur i sieci fabryki. Przędza OPCa zapewnia odnajdywanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za poorednictwem interfejsów API REST. OPC bliźniaczy nie wymaga zestawu SDK OPC Unified Architecture (OPC UA), jest językiem programowania niezależny od i może być dołączany do bezserwerowego przepływu pracy. W tym artykule opisano kilka OPC przypadków użycia.

## <a name="discovery-and-control"></a>Odnajdywanie i kontrola
Do odnajdowania i rejestracji można używać sznurka OPC.

### <a name="simple-discovery-and-registration"></a>Proste odnajdywanie i rejestracja
OPC bliźniaczy umożliwia operatorom fabryki skanowanie sieci fabrycznej, co umożliwia odnajdywanie i rejestrowanie serwerów OPC UA. Alternatywnie operatory fabryki mogą również ręcznie rejestrować urządzenia OPC UA przy użyciu znanego adresu URL odnajdowania. Na przykład aby nawiązać połączenie ze wszystkimi urządzeniami z systemem OPC UA po zainstalowaniu bramy IoT Edge z modułem OPC bliźniaczym w fabryce, operator fabryki może zdalnie wyzwolić skanowanie sieci i wizualnie zobaczyć wszystkie serwery OPC UA. 

### <a name="simple-control"></a>Kontrolka prosta
OPC bliźniaczy umożliwia operatorom fabryki reagowanie na zdarzenia i ponowne konfigurowanie maszyn piętra fabryki z chmury automatycznie lub ręcznie na bieżąco. OPC bliźniaczy udostępnia interfejsy API REST umożliwiające wywoływanie usług na serwerze OPC UA, przeglądanie przestrzeni adresowej, a także odczytywanie i zapisywanie zmiennych oraz wykonywanie metod. Na przykład, kocioł używa wskaźnika KPI temperatury do sterowania linią produkcyjną. Czujnik temperatury publikuje zmiany danych przy użyciu wydawcy OPC. Operator fabryki odbiera alert informujący o osiągnięciu progu przez temperaturę. Wiersz produkcji jest automatycznie chłodny za pomocą sznurka OPC. Operator fabryki jest powiadamiany o schłodzeniu.

## <a name="authentication"></a>Authentication
Możesz użyć sznurka OPC do prostego uwierzytelniania i dla prostego środowiska programistycznego.

### <a name="simple-authentication"></a>Proste uwierzytelnianie 
OPC bliźniaczy używa uwierzytelniania i inspekcji opartej na usłudze Azure Active Directory (AAD) od końca do końca. Na przykład, OPC bliźniaczy pozwala na skompilowanie aplikacji na górze OPC, aby określić, co operator wykonuje na komputerze. Po stronie maszyny odbywa się to za pomocą inspekcji OPC UA. Po stronie chmury polega na przechowywaniu niezmiennego dziennika inspekcji klienta i uwierzytelniania usługi AAD w interfejsie API REST.

### <a name="simple-developer-experience"></a>Proste środowisko programistyczne 
OPC sznury mogą być używane z aplikacjami zapisanymi w dowolnym języku programowania za pomocą interfejsów API REST. Deweloperzy, którzy integrują klienta OPC UA z rozwiązaniem, wiedzą, że zestaw SDK OPC UA nie jest potrzebny. Sznurki OPC mogą bezproblemowo zintegrować się z bezstanową architekturą bezserwerową. Na przykład, deweloper sieci Web w pełnym stosie, który opracowuje aplikację na potrzeby alarmu i pulpitu nawigacyjnego zdarzeń, może napisać logikę do reagowania na zdarzenia w języku JavaScript lub TypeScript przy użyciu sznurka OPC bez znajomości języka C, C# lub pełnej implementacji stosu OPC UA. 

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już OPCy i jej zastosowania, Oto sugerowany następny krok:

[Co to jest magazyn OPC](overview-opc-vault.md)