---
title: Szybki Start — Konfigurowanie zapory serwera Azure Analysis Services | Microsoft Docs
description: Ten przewodnik Szybki Start pomaga skonfigurować zaporę dla serwera Azure Analysis Services przy użyciu Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68994f9b79af55b32527eed52bbc4e5866c89538
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79205156"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Szybki start: konfigurowanie zapory serwera — Portal

Ten przewodnik Szybki start ułatwia konfigurowanie zapory na potrzeby serwera usług Azure Analysis Services. Włączanie zapory i konfiguracja zakresów adresów IP tylko na potrzeby komputerów uzyskujących dostęp do serwera użytkownika są ważnym elementem zabezpieczenia serwera i danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Serwer usług Analysis Services w ramach subskrypcji. Więcej informacji znajduje się w tematach [Quickstart: Create a server - Portal](analysis-services-create-server.md) (Szybki start: tworzenie serwera — Portal) oraz [Quickstart: Create a server - PowerShell](analysis-services-create-powershell.md) (Szybki start: tworzenie serwera — program PowerShell)
- Co najmniej jeden zakres adresów IP na potrzeby komputerów klienckich (w razie potrzeby).
- Niektóre scenariusze, w których Power BI Premium nawiązują połączenie z Azure Analysis Services, w tym do importowania danych (odświeżania) i raportów z podziałem na strony, nie są obecnie obsługiwane, nawet jeśli włączono opcję Zezwalaj na dostęp z Power BI. Bardziej typowy scenariusz korzystania z usługi Live Connect z Power BI Premium jest obsługiwany. Obsługiwane są wszystkie scenariusze Power BI Pro.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal 

[Zaloguj się do portalu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurowanie zapory

1. Kliknij serwer, aby otworzyć stronę Omówienie. 
2. W obszarze **Ustawienia** > **Zapora** > **Włącz zaporę**kliknij pozycję **włączone**.
3. Aby zezwolić na dostęp w trybie DirectQuery z poziomu usługi Power BI, w obszarze **Zezwalaj na dostęp z usługi Power BI** kliknij przycisk **Włączone**.  
4. (Opcjonalnie) Podaj co najmniej jeden zakres adresów IP. Wprowadź nazwę oraz początkowy i końcowy adres IP dla każdego zakresu. Nazwa reguły zapory powinna być ograniczona do 128 znaków i może zawierać tylko wielkie litery, małe litery, cyfry, znaki podkreślenia i łącznik. Odstępy i inne znaki specjalne są niedozwolone.
5. Kliknij przycisk **Zapisz**.

     ![Ustawienia zapory](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zakresy adresów IP nie będą już potrzebne, usuń je lub wyłącz zaporę.

## <a name="next-steps"></a>Następne kroki
W tym przewodniu Szybki start opisano, jak skonfigurować zaporę dla serwera. Teraz gdy serwer został już utworzony i zabezpieczony przy użyciu zapory, możesz do niego dodać podstawowy przykładowy model danych z portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Tutorial: Add a sample model to your server](analysis-services-create-sample-model.md) (Samouczek: dodawanie przykładowego modelu do serwera)
